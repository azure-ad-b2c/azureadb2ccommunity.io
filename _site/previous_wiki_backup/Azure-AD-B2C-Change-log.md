## March, 2020
- [New] Phone factor technical profile supports auto dial. For more information check the [metadata](https://docs.microsoft.com/en-us/azure/active-directory-b2c/phone-factor-technical-profile#metadata). 
- [Update] [Keep me signed in (KMSI)](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-policy-keep-me-signed-in) page layout version 1.2.0 and above requires `setting.enableRememberMe` metadata.
- [New] Secure a REST API technical profile with [bearer token](https://docs.microsoft.com/en-us/azure/active-directory-b2c/secure-rest-api#oauth2-bearer-authentication) 
- [New] Azure AD B2C UI supports (in GA) Safari for iOS and macOS, version 12 and above [doc link](https://docs.microsoft.com/en-us/azure/active-directory-b2c/customize-ui-overview#custom-html-and-css) 
- Documentation
  - [Recommendations and best practices for Azure Active Directory B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/best-practices)
  - [User profile attributes](https://docs.microsoft.com/en-us/azure/active-directory-b2c/user-profile-attributes)
  - [Phone factor technical profile](https://docs.microsoft.com/en-us/azure/active-directory-b2c/phone-factor-technical-profile)
  - [Application Insights technical profile ](https://docs.microsoft.com/en-us/azure/active-directory-b2c/application-insights-technical-profile)

- [New] [claim resolvers](https://docs.microsoft.com/en-us/azure/active-directory-b2c/claim-resolver-overview) `{OIDC:scope}`, `{OIDC:RedirectUri}`, `{Context:KMSI}` and [SAML claims resolvers](https://docs.microsoft.com/en-us/azure/active-directory-b2c/claim-resolver-overview#saml)
- [New] Azure AD attribute `signInNames` the unique sign in name of the local account user of **any type** in the directory . Use this to get a user with sign in value without specifying the local account type. For more information, see [Sign In and Sign Up with Username or Email sample](https://github.com/azure-ad-b2c/samples/tree/master/policies/username-or-email) 
## February, 2020
- [Document] Learn how to:
  - [Localize your custom verification email](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-email#optional-localize-your-email)
  - [Manage Azure AD B2C with Microsoft Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/microsoft-graph-get-started?tabs=applications)
  - [Manage Azure AD B2C user accounts with Microsoft Graph](https://docs.microsoft.com/en-us/azure/active-directory-b2c/manage-user-accounts-graph-api)
  - [Deploy custom policies with Azure Pipelines](https://docs.microsoft.com/en-us/azure/active-directory-b2c/deploy-custom-policies-devops)
  - [Manage Azure AD B2C custom policies with Azure PowerShell](https://docs.microsoft.com/en-us/azure/active-directory-b2c/manage-custom-policies-powershell)
  - [Guidelines for using custom page content](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-policy-ui-customization#guidelines-for-using-custom-page-content)
  - [Claim data types](https://docs.microsoft.com/en-us/azure/active-directory-b2c/claimsschema#datatype) and [UserInputType](https://docs.microsoft.com/en-us/azure/active-directory-b2c/claimsschema#userinputtype)
- [New] Use [Azure Monitor](https://docs.microsoft.com/en-us/azure/active-directory-b2c/azure-monitor) to route Azure Azure AD B2C usage activity events to different monitoring solutions. 
- [New] Claims transformations:
  - Boolean: [Compare Boolean claim to value](https://docs.microsoft.com/en-us/azure/active-directory-b2c/boolean-transformations#comparebooleanclaimtovalue).
  - Date: [Convert DateTime to Date claim](https://docs.microsoft.com/en-us/azure/active-directory-b2c/date-transformations#convertdatetimetodateclaim)
  - General: [Copy claim](https://docs.microsoft.com/en-us/azure/active-directory-b2c/general-transformations#copyclaim)
  - String: [String contains](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#stringcontains), [Substring](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#stringsubstring), [Find and replace](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#stringreplace), [String join](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#stringjoin),[ String split](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#stringsplit), [Set claims if Regex match](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#setclaimsifregexmatch), [Copies localized strings into claims](https://docs.microsoft.com/en-us/azure/active-directory-b2c/string-transformations#getlocalizedstringstransformation)
  - StringCollection: [String collection contains](https://docs.microsoft.com/en-us/azure/active-directory-b2c/stringcollection-transformations#stringcollectioncontains)
  - PhoneNumber: [ConvertPhoneNumberClaimToString](https://docs.microsoft.com/en-us/azure/active-directory-b2c/phone-number-claims-transformations#convertphonenumberclaimtostring), now you can convert a phoneNumber data type back into a string data type.

## January, 2020
- [New] [Python web app sample](https://docs.microsoft.com/en-us/azure/active-directory-b2c/code-samples#web-apps-and-apis) - Demonstrate how to Integrate B2C of Microsoft identity platform with a Python web application.
- [New] Logout force to pass a previously issued ID token to the logout endpoint as a hint about the end user's current authenticated session with the client, is now supported with custom policies using `EnforceIdTokenHintOnLogout` attribute of the [SingleSignOn](https://docs.microsoft.com/en-us/azure/active-directory-b2c/relyingparty#singlesignon) element.
- [New] [Company branding (preview)](https://docs.microsoft.com/en-us/azure/active-directory-b2c/customize-ui-overview#company-branding-preview) - You can customize your user flow pages with a banner logo, background image, and background color by using Azure Active Directory Company branding
- [Update] The Microsoft Azure AD B2C service is compatible with SameSite browser configurations, including support for SameSite=None with the Secure attribute. For more information, see [Azure AD B2C Cookie Definitions](https://docs.microsoft.com/en-us/azure/active-directory-b2c/cookie-definitions)

## December, 2019 

- [Update] Azure Active Directory B2C is [deprecating login.microsoftonline.com](https://azure.microsoft.com/en-us/updates/b2c-deprecate-msol/)
- [New] [Azure MFA technical profile](https://docs.microsoft.com/en-us/azure/active-directory-b2c/multi-factor-auth-technical-profile) provides support for verifying a phone number by using Azure Multi-Factor Authentication (MFA).
- [New] [phone number claims transformations](https://docs.microsoft.com/en-us/azure/active-directory-b2c/phone-number-claims-transformations)
- [New] [Phone sign-up and sign-in](https://docs.microsoft.com/en-us/azure/active-directory-b2c/phone-authentication) in Azure AD B2C enables your users to sign up and sign in to your applications by using a one-time password (OTP)
- [New] SAML app registration, [learn how to configure Azure AD B2C to act as a SAML identity provider to your web applications.](https://docs.microsoft.com/en-us/azure/active-directory-b2c/connect-with-saml-service-providers)
- [New] [Display Controls](https://docs.microsoft.com/en-us/azure/active-directory-b2c/display-controls) is a new user interface element that has special functionality and interacts with the Azure Active Directory B2C (Azure AD B2C) back-end service. It allows the user to perform actions on the page that invoke a validation technical profile at the back end.  Use a [Verification Display Control](https://docs.microsoft.com/en-us/azure/active-directory-b2c/display-control-verification) to verify a claim, for example an email address or phone number, with a verification code sent to the user. 
- [New] Self-Asserted technical profile, now supports [Display Claims](https://docs.microsoft.com/en-us/azure/active-directory-b2c/self-asserted-technical-profile#display-claims) with reference to claim types and display controls.
- [New] Azure AD B2C now provides support for managing the generation and verification of a one-time password. Use the [OTP technical profile](https://docs.microsoft.com/en-us/azure/active-directory-b2c/one-time-password-technical-profile) to generate a code, and then verify that code later.
- [New] REST API technical profile, support of [JSON payload input claim](https://docs.microsoft.com/en-us/azure/active-directory-b2c/restful-technical-profile#send-a-json-payload)  
- [New] REST API technical profile, support of sending bearer token that is stored in a policy key. Use `AuthenticationType` metadata to `Bearer`
- [New] [GenerateJson](https://docs.microsoft.com/en-us/azure/active-directory-b2c/json-transformations) claims trnsfomation generates a complex JSON using input claim and input parameters.
- [New] Use [custom email](https://docs.microsoft.com/en-us/azure/active-directory-b2c/custom-email) in Azure AD B2C to send customized email to users that sign up to use your applications.

## November, 2019
- [New] [Use the Azure portal to create and delete consumer users in Azure AD B2C](https://docs.microsoft.com/en-us/azure/active-directory-b2c/manage-users-portal)

- [New] [app registration experience](https://docs.microsoft.com/en-us/azure/active-directory-b2c/tutorial-register-applications?tabs=applications). With the new UX (in preview), you manage your Azure AD and Azure AD B2C applications in the same place.

- [Update] [Azure Active Directory B2C Monthly Active Users (MAU) pricing model](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-how-to-enable-billing)

- [Fix] When using `AlwaysUseDefaultValue` (in input claims or output claims) with a [claim resolver](https://docs.microsoft.com/en-us/azure/active-directory-b2c/claim-resolver-overview) . If the claim resolve is missing, B2C returns an empty string (without error message). For example, if the query string parameter `idp` is missing, the `selected_idp` contains an empty string.

    ```XML
    <InputClaim ClaimTypeReferenceId="selected_idp" DefaultValue="{OAUTH-KV:idp}" AlwaysUseDefaultValue="true" />
    ```


<InputClaim ClaimTypeReferenceId="selected_idp" DefaultValue="{OAUTH-KV:custom_param}" AlwaysUseDefaultValue="true" />

## October, 2019 
- [New] [Manage password protection settings](https://docs.microsoft.com/en-us/azure/active-directory-b2c/active-directory-b2c-reference-threat-management#manage-password-protection-settings)

- [New] [Use MS Graph API to perform CRUD operations against User Flows](https://docs.microsoft.com/en-us/graph/api/resources/identityuserflow?view=graph-rest-beta)

- [New] [Use MS Graph API to perform CRUD operations against Custom Policies](https://docs.microsoft.com/en-us/graph/api/resources/trustframeworkpolicy?view=graph-rest-beta)

- [New] [Use MS Graph API to perform CRUD operations against IEF Key Containers](https://docs.microsoft.com/en-us/graph/api/resources/trustframeworkkeyset?view=graph-rest-beta)

    - [New] [MS Graph API samples](https://github.com/Azure-Samples/ActiveDirectory-B2C-MSGraph-PolicyAndKeysets) for User Flow, Custom Policies, and Key CRUD operations.

## August, 2019
- [New] Pass through an identity provider's access token in Azure AD B2C with [user flow](https://docs.microsoft.com/en-us/azure/active-directory-b2c/idp-pass-through-user-flow) and [custom policy](https://docs.microsoft.com/en-us/azure/active-directory-b2c/idp-pass-through-custom)

## May, 2019
- [New] [Azure AD admin roles for B2C are in public preview](https://docs.microsoft.com/en-us/azure/active-directory/users-groups-roles/directory-assign-admin-roles#b2c-user-flow-administrator)