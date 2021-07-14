---
title: "Best practices"
permalink: /docs/custom-policy-best-practices/
excerpt: "Best practices on writing custom policies"
last_modified_at: 2020-04-06T21:36:11-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Vinu Gunasekaran
---
## Best practices
Within an Azure AD B2C custom policy, you can integrate your own business logic to build the user experiences your require and extend functionality of the service. We have a set of best practices and recommendations to get started.

1. Within the base policy, we suggest avoiding making any changes.  And if required, make heavy notes. Instead, create your logic within the extension files. You can add new elements which will override the base policy by referencing the same Id. This will allow you to scale out your project while making it easier to upgrade base policy later on if Microsoft releases new starter packs.
1. Configure your user journey in the extension file only.
1. New elements should be added to the extension file. You can still override the technical profile configured in the base policy, in the extension file with new elements by referencing the original technical profile Id.  

## Customize elements defined in the base policy
- To customize any element from the base policy, you simply copy the element with its parent elements, and make the necessary changes.
- Want to add something new? That’s OK! Make sure you copy over all of those claims from the base policy. <!-- what does this mean?-->
- When overriding a Self-Asserted technical profile by adding new Output Claims, the new claim will be rendered first. To change the order, make sure you include all of the claims from the base policy and arrange the output claims in the order you require them to be rendered.

In the following example, the Facebook policy is configured in the base policy, while in the extension policy you must specify the Facebook application Id, the scopes to use and the information you want to retrieve from Facebook. The extension contains the values that are related to your environment like the scopes that are relevant to you in the interaction with Facebook.  

![Facebook customization](/azureadb2ccommunity.io/assets/images/docs/facebook-extension.png)

## Including technical profile
You can create a technical profile that is based on another one. Use the IncludeTechnicalProfile element to customize your technical profile.

For example `AAD-UserReadUsingAlternativeSecurityId-NoError` includes `AAD-UserReadUsingAlternativeSecurityId`, while configuring the `RaiseErrorIfClaimsPrincipalDoesNotExist` metadata item to `false`. 

`AAD-UserReadUsingAlternativeSecurityId` itself includes the `AAD-Common` technical profile.

`AAD-Common` provides the base functionality to communicate with Azure AD over the AzureActiveDirectoryProvider propriety protocol.

<!--So, instead of creating new technical profile with all the XML elements. You can create a new one based on another one.-->

![Include a technical profile](/azureadb2ccommunity.io/assets/images/docs/include-tp.png)

<!--So may ask: What is the benefits of creating a technical profile to include versus just overwriting it?-->

- **Include**: When you include a technical profile, you create a new technical profile, that is based on another one. The one you create has a new name (id). You can declare a technical profile that includes any technical profile, in the same policy or in any policy that inherits from the policy  where the technical profile is configured. 

    For example, a technical profile "A" can be included in technical profile "B1" and "B2". B1 itself can be included in technical profile "C" and so on.

- **Overwrite**. Overwrite is always done in a policy that inherits from the policy where the technical profile is declared. When you overwrite, you do not change the technical profile name. You can overwrite a technical profile that is configured in any policy which is inherited by your current working policy file.

![Include a technical profile versus overwriting](/azureadb2ccommunity.io/assets/images/docs/include-tp-2.png) 


## Escape XML invalid characters

If you need to include XML invalid characters in the XML policy file, use their escaped equivalents. The following table shows the invalid XML characters and their escaped equivalents.  

| Invalid XML character | Replaced with |
|-----------------------|---------------|
| `<`                   | `&lt;`        |
| `>`                   | `&gt;`        |
| `"`                   | `&quot;`      |
| `'`                   | `&apos;`      |
| `&`                   | `&amp;`       | 




