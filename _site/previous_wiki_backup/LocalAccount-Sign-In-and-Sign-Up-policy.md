The following article will make use of the learnings from previous articles in order to understand how a Local Account sign in and sign up is achieved.

The Local Accounts starter pack can be found [here](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts). The files of interest are TrustFrameworkBase.xml and TrustFrameworkExtensions.xml.

You will find the user journey and its orchestration steps in the TrustFrameworkBase.xml file, with the Id "SignUpOrSignIn". Each Orchestration step and its referenced technical profile will be explained in detail in the following series.

For a user to be able to Sign in and Sign Up, the following User Experience must be translated into logical steps with a custom policy.

### Logical Steps
Handling Sign In:
1. Display a page where the user can enter their email and password.
1. On the sign in page, display a link to sign up.
1. If the user submits their credentials (signs in), we must validate the credentials.
1. Issue an id token.

Handling Sign Up:
1. Display a page that allows users to enter their email, password and name.
1. Verify their email with a Timed One Time Passcode sent to their email address.
1. When the user completes a sign up, we must create their account.
1. Prevent a user to sign up with an existing email address.
1. Issue an id token.

### Translating this into custom policies:  
Handling Sign In:
1. This requires a Self-Asserted technical profile. It must present output claims to obtain the email and password claims.
1. Use the combined sign in and sign up content definition which provides this for us.
1. Run a Validation technical profile to validate the credentials.
1. Read any additional information from the directory user object.
1. Call a technical profile to issue a token.  

Handling Sign Up:
1. This requires a Self-Asserted technical profile. It must present output claims to obtain the email, password and name claims.
1. Make use of a special claim which enforced email verification.
1. Use a Validation technical profile to write the account to the directory. This Validation technical profile will be of type Azure Active Directory.
1. As part of writing the account configure the technical profile to throw an error if the account exists.
1. Read any additional information from the directory user object.
1. Call a technical profile to issue a token.


## Building the custom policy
### Handling Sign In:
**Orchestration Step 1**: Provide functionality for a user to Sign in or Sign Up. This is achieved using a Self-Asserted technical profile and connected validation technical profile.

The XML required to generate this step is:
```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
  </ClaimsProviderSelections>
  <ClaimsExchanges>
    <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
  </ClaimsExchanges>
</OrchestrationStep>
```

The combined sign in and sign up page is treated specially by Azure AD B2C, since it presents a sign up link that can take the user to the sign up step.
This is achieved with the following two lines:
```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
```

Since Azure AD B2C understands that this is a Sign In page, you must specify the `ClaimsProviderSelections` element with at least one reference to a `ClaimsProviderSelection`. This `ClaimsProviderSelection` maps to the `ClaimsExchange` which ultimately calls a technical profile called `SelfAsserted-LocalAccountSignin-Email`.

The `SelfAsserted-LocalAccountSignin-Email` technical profile defines the actual page functionality:

```xml
<TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
  <DisplayName>Local Account Signin</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="SignUpTarget">SignUpWithLogonEmailExchange</Item>
    <Item Key="setting.operatingMode">Email</Item>
    <Item Key="ContentDefinitionReferenceId">api.selfasserted</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="signInName" Required="true" />
    <OutputClaim ClaimTypeReferenceId="password" Required="true" />
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="login-NonInteractive" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

|Element name  |Description  |
|---------|---------|
|TechnicalProfile Id | Identifier for this technical profile. It is used to find the technical profile that this orchestration step calls.|
|DisplayName|Friendly name which can describe the function of this technical profile.|
|Protocol|The Azure AD B2C technical profile type. In this case, it is Self-Asserted, such that a page is rendered for the user to provide their inputs.|
|Metadata|For a Self Asserted Combined Sign in and Sign up profile, we provide a SignUpTarget which points to the Sign Up ClaimsExchange Id in a subsequent orchestrations step.|
|InputClaims|Enables the ability to pre-populate the signInName claim|
|OutputClaims| We require the user to provide their email and password, hence referenced as output claims. There are some claims here, such as objectId, that are not presented on the page since the validation technical profile satisfies this output claim.|
|ValidationTechnicalProfiles|The technical profile to launch to validate the date the user provided, in this case to validate their credentials.|
|UseTechnicalProfileForSessionManagement|TO DO|

To see all the configuration options for a Self-Asserted technical profile, find more [here](https://docs.microsoft.com/en-us/azure/active-directory-b2c/self-asserted-technical-profile).

By calling this technical profile we now satisfy the initial logical step for sign in. When the user submits the page, the Validation technical profile will run - login-NonInteractive.

This is a technical profile which makes an OpenID request using the [Resource Owner Password Credential](https://tools.ietf.org/html/rfc6749#section-4.3) grant flow to validate the users provided credentials at the Azure AD authorization server. Essentially this is an API based logon which the Azure AD B2C server will complete against the Azure AD authorization server.

```xml
<TechnicalProfile Id="login-NonInteractive">
  <DisplayName>Local Account SignIn</DisplayName>
  <Protocol Name="OpenIdConnect" />
  <Metadata>
    <Item Key="UserMessageIfClaimsPrincipalDoesNotExist">We can't seem to find your account</Item>
    <Item Key="UserMessageIfInvalidPassword">Your password is incorrect</Item>
    <Item Key="UserMessageIfOldPasswordUsed">Looks like you used an old password</Item>

    <Item Key="ProviderName">https://sts.windows.net/</Item>
    <Item Key="METADATA">https://login.microsoftonline.com/{tenant}/.well-known/openid-configuration</Item>
    <Item Key="authorization_endpoint">https://login.microsoftonline.com/{tenant}/oauth2/token</Item>
    <Item Key="response_types">id_token</Item>
    <Item Key="response_mode">query</Item>
    <Item Key="scope">email openid</Item>

    <!-- Policy Engine Clients -->
    <Item Key="UsePolicyInRedirectUri">false</Item>
    <Item Key="HttpBinding">POST</Item>
  </Metadata>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="signInName" PartnerClaimType="username" Required="true" />
    <InputClaim ClaimTypeReferenceId="password" Required="true" />
    <InputClaim ClaimTypeReferenceId="grant_type" DefaultValue="password" />
    <InputClaim ClaimTypeReferenceId="scope" DefaultValue="openid" />
    <InputClaim ClaimTypeReferenceId="nca" PartnerClaimType="nca" DefaultValue="1" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="oid" />
    <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid" />
    <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
    <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
    <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" PartnerClaimType="upn" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
  </OutputClaims>
</TechnicalProfile>
```

|Element name  |Description  |
|---------|---------|
|TechnicalProfile Id | Identifier for this technical profile. It is used to find the technical profile that this orchestration step calls.|
|DisplayName|Friendly name which can describe the function of this technical profile.|
|Protocol|The Azure AD B2C technical profile type. In this case, it is OpenId, such that Azure AD B2C understands to make an OpenId request.|
|Metadata|Various configuration options to make a valid OpenId request since the grant_type is configured password and the HTTP binding is set to POST.  This also includes various error handling responses, such as incorrect password.|
|InputClaims|Passes the username and password into the POST body of the OpenId request.|
|OutputClaims| Maps the JWT issued by the authorization server into Azure AD B2C's claim bag. Here we obtain the objectId and authenticationSource, hence it is not shown on the Self-Asserted page.|

To see all the configuration options for an OpenID technical profile, find more [here](https://docs.microsoft.com/en-us/azure/active-directory-b2c/openid-connect-technical-profile).

At this point we have now rendered a sign in page to the user, enter their email and password, and validate their credentials.

**Orchestration Step 3** - Read any additional data from the user object.

We maybe storing additional data the user provided or other data on the user object which allows your application/service to function correctly.

Therefore, we will read the user object for any desired attributes to add into the Azure AD B2C claims bag.

The following Orchestration step calls a technical profile called `AAD-UserReadUsingObjectId` which provides this functionality.
The ClaimsExchange Id is unique name for this claims exchange that you can set.

```xml
<OrchestrationStep Order="3" Type="ClaimsExchange">
  <ClaimsExchanges>
    <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
  </ClaimsExchanges>
</OrchestrationStep>
```

The referenced technical profile is as follows:

```xml
<TechnicalProfile Id="AAD-UserReadUsingObjectId">
  <Metadata>
    <Item Key="Operation">Read</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="otherMails" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surname" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
</TechnicalProfile>
```

This technical profile does not state a protocol, therefore is automatically of type Azure Active Directory, which provides the ability to read or write to the directory structure.


|Element name  |Description  |
|---------|---------|
|TechnicalProfile Id|Identifier for this technical profile. It is used to find the technical profile that this orchestration step calls.|
|Metadata|This is configured to read the directory. And to throw an error if the user is not found.|
|InputClaims|This is asking to find a user account with the objectId in the Azure AD B2C claims bag. This objectId will have been received via the login-NonInteractive technical profile and output into the claims bag by the SelfAsserted-LocalAccountSignin-Email technical profile. |
|OutputClaims|We are asking to read these claims from the directory. The Azure AD B2C claims referenced here have the same name as the attribute name in the directory. |
|IncludeTechnicalProfile|AAD-Common is included to provide the foundational functionality to read or write to the directory.|

A special case must be noted for the `signInNames.emailAddress`, this references the attribute `signInNames` which is a collection of key value pairs. In this case we are reading back the `emailAddress` key within the `signInNames` attribute.

**Orchestration Step 4** - Issue a an id token.

In the majority of user journeys, the journey will end by issuing an id token back to the application. This orchestration step looks as follows:

```xml
<OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
```

The referenced technical profile is as follows:
```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <InputClaims />
  <OutputClaims />
</TechnicalProfile>
```

This step does not need configuring any further, but find out more [here](https://docs.microsoft.com/en-us/azure/active-directory-b2c/jwt-issuer-technical-profile).

### Handling Sign Up:
To handle sign up, we must have one additional orchestration step which allows the user to provide their email, new password and name. And upon validating this information, we must write an account to the directory. the other steps are shared with the orchestration steps explained in `Handling Sign in`.

The additional orchestration step is as follows:

```xml
<OrchestrationStep Order="2" Type="ClaimsExchange">
  <Preconditions>
    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
      <Value>objectId</Value>
      <Action>SkipThisOrchestrationStep</Action>
    </Precondition>
  </Preconditions>
  <ClaimsExchanges>
    <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
  </ClaimsExchanges>
</OrchestrationStep>
```

Since orchestration steps run sequentially, we must not run this step if the user is trying to sign in, and only run if the user clicked the sign up link. This is achieved using the **Precondition**. Note that during the sign in phase, the Azure AD B2C claims bag will have an objectId populated after login-NonInteractive has run. Therefore we can use the existence of this claim to skip this step as follows.

```xml
<Precondition Type="ClaimsExist" ExecuteActionsIf="true">
    <Value>objectId</Value>
    <Action>SkipThisOrchestrationStep</Action>
</Precondition>
```

When displaying the Combined Sign in and Sign up page, it was mentioned that the metadata of the `SelfAsserted-LocalAccountSignin-Email` technical profile configures an item called `SignUpTarget`. This enables the Sign Up link on the Combined Sign in and Sign up page to call the claims exchange in orchestration Step 2, which consequently executes the LocalAccountSignUpWithLogonEmail technical profile.

The technical profile is designed to capture the email, password and name of the user, and then write the account to the directory, as follows:
```xml
<TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
  <DisplayName>Email signup</DisplayName>
  <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
  <Metadata>
    <Item Key="IpAddressClaimReferenceId">IpAddress</Item>
    <Item Key="ContentDefinitionReferenceId">api.localaccountsignup</Item>
    <Item Key="language.button_continue">Create</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
  </CryptographicKeys>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" />
  </InputClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
    <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
    <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" />
    <OutputClaim ClaimTypeReferenceId="newUser" />

    <!-- Optional claims, to be collected from the user -->
    <OutputClaim ClaimTypeReferenceId="displayName" />
    <OutputClaim ClaimTypeReferenceId="givenName" />
    <OutputClaim ClaimTypeReferenceId="surName" />
  </OutputClaims>
  <ValidationTechnicalProfiles>
    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail" />
  </ValidationTechnicalProfiles>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>

```

|Element name  |Description  |
|---------|---------|
|TechnicalProfile Id|Identifier for this technical profile. It is used to find the technical profile that this orchestration step calls.|
|Metadata|Various configuration options available for a Self-Asserted page.|
|InputClaims|Why do we have this here?|
|OutputClaims|This asks the user to provide a verified email (via email verification), password and names. Other claims are satisfied by the validation technical profile, and therefore not displayed. They are there only such that those claims be available to subsequent steps after this step completes.|
|ValidationTechnicalProfiles|When the user submits the page, we must validate the users email doesn't already exist, and then write the account to the directory.|
|UseTechnicalProfileForSessionManagement||

Azure AD B2C uses a special partner claim type to enforce email verification on a claim, as seen here:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```

Here we are forcing the email claim presented on screen to be verified. Azure AD B2C will therefore render the Verify button on the page against this text field, and only allow the user to continue if this field was verified by a code sent to the users inbox. This technique can be used against any claim name presented to the user as an output claim (ClaimTypeReferenceId).

To see all the configuration options for a Self-Asserted technical profile, find more [here](https://docs.microsoft.com/en-us/azure/active-directory-b2c/self-asserted-technical-profile).

|Element name  |Description  |
|---------|---------|
|TechnicalProfile Id|Identifier for this technical profile. It is used to find the technical profile that is referenced elsewhere.|
|Metadata|This is configured with a reference to a content definition to provide your custom look and feel to this page.|
|InputClaims|This will pre-popualte the email field if the email claim was acquired earlier in the journey. |
|OutputClaims|These are claims that will be presented to the user on the rendered page, potentially prefilled based on the inputClaims status. Those claims which cannot be fulfilled by the user, such as objectId and newUser, are not shown on the screen as they are fulfilled by the validation technical profile being referenced.|
|ValidationTechnicalProfile|A validation technical profile is used to write the user account when the user submits the page confirming their information.|

To see all the configuration options for a Self-Asserted technical profile, find more [here](https://docs.microsoft.com/en-us/azure/active-directory-b2c/self-asserted-technical-profile).

Azure AD B2C uses a special partner claim type to enforce email verification on a claim, as seen here:
```xml
<OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
```
Here we are forcing the email claim presented on screen to be verified. Azure AD B2C will therefore render the `Verify` button on the page against this text field, and only allow the user to continue if this field was verified by a code sent to the users inbox. This technique can be used against any claim name presented to the user as an output claim (ClaimTypeReferenceId).

When the user submits the page, the Validation technical profile will run, called `AAD-UserWriteUsingLogonEmail`. This is called since either the user account can be written successfully based on the information provided, or it cannot be. In this case, the user account may not be able to be written if the account exists.

The `AAD-UserWriteUsingLogonEmail` is as follows:
```xml
<TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
  <Metadata>
    <Item Key="Operation">Write</Item>
    <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">true</Item>
  </Metadata>
  <IncludeInSso>false</IncludeInSso>
  <InputClaims>
    <InputClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" Required="true" />
  </InputClaims>
  <PersistedClaims>
    <!-- Required claims -->
    <PersistedClaim ClaimTypeReferenceId="email" PartnerClaimType="signInNames.emailAddress" />
    <PersistedClaim ClaimTypeReferenceId="newPassword" PartnerClaimType="password"/>
    <PersistedClaim ClaimTypeReferenceId="displayName" DefaultValue="unknown" />
    <PersistedClaim ClaimTypeReferenceId="passwordPolicies" DefaultValue="DisablePasswordExpiration" />

    <!-- Optional claims. -->
    <PersistedClaim ClaimTypeReferenceId="givenName" />
    <PersistedClaim ClaimTypeReferenceId="surname" />
  </PersistedClaims>
  <OutputClaims>
    <OutputClaim ClaimTypeReferenceId="objectId" />
    <OutputClaim ClaimTypeReferenceId="newUser" PartnerClaimType="newClaimsPrincipalCreated" />
    <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="localAccountAuthentication" />
    <OutputClaim ClaimTypeReferenceId="userPrincipalName" />
    <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
  </OutputClaims>
  <IncludeTechnicalProfile ReferenceId="AAD-Common" />
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-AAD" />
</TechnicalProfile>
```

|Element name  |Description  |
|---------|---------|
|TechnicalProfile Id|Identifier for this technical profile. It is used to find the technical profile that is referenced elsewhere.|
|Metadata|This is configured to write to the directory. And to throw an error if the user already exists with an error message.|
|InputClaims|This is attempting to find a user account with the `email` provided as part of the sign up page - `LocalAccountSignUpWithLogonEmail` technical profile.|
|PersistedClaims|This section defines which claims are to be written to the account. In this case, it will automatically create the account with this information present.|
|OutputClaims|We are asking to read these claims from account which was just written. The Azure AD B2C claims referenced here have the same name as the attribute name in the directory. |
|IncludeTechnicalProfile|AAD-Common is included to provide the foundational functionality to read or write to the directory.|

**Orchestration Step 4** - Issue a an id token.

In the majority of user journeys, the journey will end by issuing an id token back to the application. This orchestration step looks as follows:

```xml
<OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
```

The referenced technical profile is as follows:
```xml
<TechnicalProfile Id="JwtIssuer">
  <DisplayName>JWT Issuer</DisplayName>
  <Protocol Name="None" />
  <OutputTokenFormat>JWT</OutputTokenFormat>
  <Metadata>
    <Item Key="client_id">{service:te}</Item>
    <Item Key="issuer_refresh_token_user_identity_claim_type">objectId</Item>
    <Item Key="SendTokenResponseBodyWithJsonNumbers">true</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="issuer_secret" StorageReferenceId="B2C_1A_TokenSigningKeyContainer" />
    <Key Id="issuer_refresh_token_key" StorageReferenceId="B2C_1A_TokenEncryptionKeyContainer" />
  </CryptographicKeys>
  <InputClaims />
  <OutputClaims />
</TechnicalProfile>
```

This step does not need configuring any further, but find out more [here](https://docs.microsoft.com/en-us/azure/active-directory-b2c/jwt-issuer-technical-profile).

## Summary
By reducing the user experience to a set of logical steps, we have translated these to a set of Orchestration Steps within an Azure AD B2C policy. These orchestration steps then implement the functionality of each logical step by allowing the user to interact with pages and validate various information. Finally we issue an id token back to the application.