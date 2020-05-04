---
title: "Azure AD B2C policy"
permalink: /training/azuread-b2c-policies/
excerpt: "Learn Azure AD B2C policies"
last_modified_at: 2020-04-06T21:36:11-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Vinu Gunasekaran
---

## Concept

For consumer-based applications, there is a notion of sign-up, sign-in, profile management and even password reset. Azure AD B2C makes this easy across all platforms, web, mobile and desktop apps. Your customers have the flexibility to choose their identity:

- **Social accounts** such as Facebook, Microsoft, Google, Amazon or any other social identify provider. 
- **Enterprise accounts** such as ADFS or Salesforce.
- **Local account** where the account is stored and manage by Azure AD B2C.

A policy, whether it’s a built-in policy or a custom policy, holds the same underlying concepts. Configuring a set of steps and user experiences to take the user through a journey to retrieve claims that are ultimately presented to the relying party application. 

Sign-up or sign-in is a common consumer enrollment experience.  These types of experiences are controlled by different policies.

### Examples

Our first policy example is "Sign-up or sign-in". Using a sign-up or sign-in policy, users can: sign-in with a local account. Or sign-in with any external account, such as Facebook or ADFS. Create new local account. Or reset their password. All those actions are configured in the policy. 

![Sign-up or sign-in policy](/assets/images/docs/policy-susi.png)


Maintaining a user's information is important.  Utilizing the "Edit profile" policy will allow your users to maintain their information after successfully signing in with a social, local or external account.

![Profile edit policy](/assets/images/docs//profile-edit.png)

## Configuration

{% include video id="nWJ3m82536A" provider="youtube" %}

