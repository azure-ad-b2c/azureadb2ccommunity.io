---
title: "You are invited - Azure AD B2C Webinar series"
permalink: /training/labs/
excerpt: "Learn the usecases of Azure AD B2C"
last_modified_at: 2020-04-06T21:36:11-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Vinu Gunasekaran
comment: true
---

## Introduction

Progressive profiling allows you to create frictionless user journeys within Azure AD B2C, making sure that users only need to provide the minimum information before interacting with various parts of your service. When users interact with part of your service which requires further information about the user, gather more data at the required time. This can work on both mobile and web applications.

## Objectives

Configure Azure Active Directory B2C to perform a progressive profile update.

1. Configure the required claims
2. Configure a page to collect user information
3. Configure the User Journey to determine whether the user must provide extra information
4. Upload and test AAD B2C custom policies

## Prerequisites

1. Azure account with subscription (See the Resources section for details)
2. Azure AD B2C tenant
3. [VS code](https://code.visualstudio.com/download)
4. [VS code plugin](Visual Studio Code Extension for Azure AD B2C)

## Tasks

### Task 1 – Download the Azure AD B2C Custom Policy files

To begin creating your Azure AD B2C custom user journey, you must start with the base XML files, known as the starter pack. Download these to your working directory via the Clone or Download link found here: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack

To add additional logic to the base journeys included in the starter pack, either download or clone the Custom Policy samples repository, saving its contents to your working directory: https://github.com/azure-ad-b2c/samples

### Task 2 – Configure your B2C tenant for Custom Policies

Follow the instructions here (from the “Get the starter pack” step, use the LocalAccounts folder instead of SocialAndLocalAccounts) : https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom up to and including “Test the custom policy”.

### Task 3 – Enable Extension Attribute support

1. Sign in to the [Azure portal](https://portal.azure.com/) as the global administrator of your Azure AD B2C tenant.
2. Make sure you are using the directory that contains your Azure AD B2C tenant. Click the Directory and subscription filter in the top menu and choosing the directory that contains your tenant.
3. In the top search bar, search for and select Azure Active Directory.
4. Select App registrations (preview).
5. Search “b2c-extensions-app”
6. Copy the following identifiers to your clipboard and save them:
    - Application ID. Example: 103ee0e6-f92d-4183-b576-8c3739027780
    - Object ID. Example: 80d8296a-da0a-49ee-b6ab-fd232aa45201

Open the samples/policies/progressive-profile/policy/ folder, and open the ProgressiveProfileTrustFrameworkExtensions.xml file.
Search for “<TechnicalProfile Id="AAD-Common">”

Modify the Metadata section in the AAD-Common technical profile as shown in the following example

- Replace ObjectID with the ObjectID of the b2c-extensions-app recorded earlier.
- Replace Application ID with the Application ID of the b2c-extensions-app recorded earlier.

```
<Metadata>

    <Item Key="ApplicationObjectId">Object ID</Item>

    <Item Key="ClientId">Application ID</Item>

  </Metadata>
```

This allows the policy to create/read/write extension attributes.

### Task 4 – Complete and upload your new policy

In the ProgressiveProfileTrustFrameworkExtensions.xml and ProgressiveSignUpOrSignin.xml, replace all instances of yourtenant.onmicrosoft.com with your tenant.

Upload the ProgressiveProfileTrustFrameworkExtensions.xml first and then ProgressiveSignUpOrSignin.xml file via the Azure AD B2C Blade – Identity Experience Framework – Upload.

### Task 5 – Configure an Azure AD B2C Application Registration

1. Sign in to the [Azure portal](https://portal.azure.com/) as the global administrator of your Azure AD B2C tenant.
2. Make sure you are using the directory that contains your Azure AD B2C tenant. Click the Directory and subscription filter in the top menu and choosing the directory that contains your tenant.
3. In the top search bar, search for and select Azure AD B2C.
4. On the Overview page, select Identity Experience Framework.
5. Select the Applications tab and click Add.
6. Enter “MyWebApp” as the Name of the application. Select Yes for Include Web App / Web API.  
7. Add “https://jwt.ms” as a Reply URL. This is where Azure AD B2C will send tokens back after a successful authentication. Because we do not have an application built yet, we will use https://jwt.ms temporarily. This web application will display the claims in the minted token.  
8. Click Create to create the application.  


### Task 6 – Run and test the Custom Policy

1. Within the Azure Portal – AAD B2C Blade - Identity Experience Framework Select Custom Policies
2. Select the B2C_1A_progressive_signup_signin
3. Select the Application Registration created in Task 5, and select https://jwt.ms as the reply Url.
4. Select b2clogin.com as the domain
5. Click Run Now
6. First Sign Up, then use the Run Now link again to Sign In.

If done correctly, the user will not be asked for the Loyalty Number when they Sign Up, but will be asked when they first Sign In. Subsequent Sign In’s will not ask for the Loyalty Number.

The JWT token issued at https://jwt.ms will show the Loyalty Number in the extension_LoyaltyNumber claim.

## Review the changes to enable Progressive Profiling

(There are no actions to take in the following section)

The ProgressiveProfileTrustFrameworkExtensions.xml file within the samples/policies/progressive-profile/policy/ folder contains the additions made to enable a progressive profile-based journey.  

When the user signs in, if 'extension_LoyaltyNumber' doesn't exist on the user object, and if it’s not a 'newUser' (sign up) they will be directed to 'SelfAsserted-ProfileUpdate' to update the Loyalty number.

To achieve this, the following steps were taken:

## Inherit the Starter Pack configuration

Setting the BasePolicy Id instructs AAD B2C to inherit all of the configuration from the Starter Pack, merging all modifications of elements declared with the same Id, or incorporating new elements on top of what the linked policies already configured.

```
<Base Policy>

  <TenantId>yourtenant.onmicrosoft.com</TenantId>

  <PolicyId>B2C_1A_TrustframeworkExtensions</PolicyId>

</BasePolicy>
```

## Added a claim to capture the Loyalty Number

In the ProgressiveSignUpOrSignin.xml file, after the </BasePolicy> section, the following section was added to define the claim:

```
<BuildingBlocks>

  <ClaimsSchema>

    <ClaimType Id="extension_LoyaltyNumber">

      <DisplayName>Loyalty-Number</DisplayName>

      <DataType>string</DataType>

      <UserHelpText>Your Loyalty number from your membership card</UserHelpText>

      <UserInputType>TextBox</UserInputType>

    </ClaimType>

  </ClaimsSchema>

</BuildingBlocks>
```

## Created a page to collect the Loyalty Number
Augmented the existing profile update technical profile to collect the loyalty number.

Under the </BuildingBlocks> section, added the following section:

```
<ClaimsProviders>



</ClaimsProviders>
```

Then within the ClaimsProviders section created above, added the following claims provider:

```
<ClaimsProvider>

  <DisplayName>Self Asserted</DisplayName>

  <TechnicalProfiles>

    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">

      <OutputClaims>

        <OutputClaim ClaimTypeReferenceId="extension_LoyaltyNumber" Required="true"/>

      </OutputClaims>

    </TechnicalProfile>

  </TechnicalProfiles>

</ClaimsProvider>
```

## Configured the Policy for Extension attributes

To enable custom attributes in your policy, provide Application ID and Application Object ID in the AAD-Common technical profile metadata. The AAD-Common technical profile provides support for Azure AD user management. Override the AAD-Common technical profile by adding the following to the ProgressiveProfileTrustFrameworkExtensions.xml within the ClaimsProviders section:

```
<ClaimsProvider>

  <DisplayName>Azure Active Directory</DisplayName>

  <TechnicalProfiles>

    <TechnicalProfile Id="AAD-Common">

      <Metadata>

        <Item Key="ClientId">b2c-extensions-app application ID here </Item>

        <Item Key="ApplicationObjectId">b2c-extensions-app application ObjectId here</Item>

      </Metadata>

    </TechnicalProfile>

  </TechnicalProfiles>  

</ClaimsProvider>
```

As per this [article](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-policy-custom-attributes#modify-your-custom-policy).

## Augmented the profile read/write technical profiles to read/write the Loyalty Number

Added another claims provider within the ClaimsProviders section as follows:  

```
<ClaimsProvider>

  <DisplayName>Azure Active Directory</DisplayName>

  <TechnicalProfiles>

    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">

      <PersistedClaims>

        <PersistedClaim ClaimTypeReferenceId="extension_LoyaltyNumber"/>

      </PersistedClaims>

    </TechnicalProfile>

    <TechnicalProfile Id="AAD-UserReadUsingObjectId">

      <OutputClaims>

        <OutputClaim ClaimTypeReferenceId="extension_LoyaltyNumber"/>

      </OutputClaims>

    </TechnicalProfile>

  </TechnicalProfiles>

</ClaimsProvider>
```

This augments the existing technical profiles found in the TrustframeworkBase.xml file to additionally read and write the Loyalty number extension attribute.

## Added a step to collect the Loyalty Number in the User Journey

Copied the default SignInOrSignUp User Journey from the TrusframeworkBase.xml file. Renamed the UserJourney Id to “SignUpOrSignInProgressive”.

Added a step in the User Journey to call the SelfAsserted-ProfileUpdate technical profile under the condition that we did not find the extension_LoyaltyNumber via Orchestration Step 3, and that the newUser claim did not exist (sign up scenario).  

The newUser claim is output by default when a new account is written, hence we can use this as a flag.

```
<OrchestrationStep Order="4" Type="ClaimsExchange">

  <Preconditions>

    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

      <Value>extension_LoyaltyNumber</Value>

      <Action>SkipThisOrchestrationStep</Action>

    </Precondition>

    <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

      <Value>newUser</Value>

      <Action>SkipThisOrchestrationStep</Action>

    </Precondition>

  </Preconditions>

  <ClaimsExchanges>

    <ClaimsExchange Id="ProfileUpdateExchange" TechnicalProfileReferenceId="SelfAsserted-ProfileUpdate" />

  </ClaimsExchanges>

</OrchestrationStep>  
```

This step is controlled by preconditions. It will only get triggered when the user is authenticating for a subsequent time after sign-up, and has not already provided their loyalty number. When triggered, the user must enter their loyalty number.

Then adjusted the “Order” number sequence of all steps to make sure it follows sequentially.

## Configured the Relying Party section

First copied the signInOrSignUp.xml from the LocalAccounts folder to the ProgressiveProfile folder.

Changed the BasePolicy to the PolicyId of the ProgressiveProfileTrustFrameworkExtensions.xml to B2C_1A_ProgressiveProfile_TrustFrameworkExtensions.

Change the defaultUserJourney Id under the RelyingParty section from SignUpOrSignIn to SignUpOrSignInProgressiveProfile.

Added an output claim as part of the Relying Party configuration such that the token contains the loyalty number.

In the RelyingParty section of ProgressiveProfileSignUpOrSignin.xml, within the OutputClaims section added:

```
<OutputClaim ClaimTypeReferenceId="extension_LoyaltyNumber"/>
```

## Lab Summary

Congratulations on completing this lab! You’ve learned how to use Azure AD B2C to add rich, secure & scalable customer sign-up & sign-in experiences to your web app.   

## Additional Tasks

- [Add Twitter as an Identity provider](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-custom-setup-twitter-idp)
- [Add an OIDC Azure AD as an Identity provider](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-setup-aad-custom)
- [Customize the complexity of the password. ](https://docs.microsoft.com/en-us/azure/active-directory-b2c/predicates)
