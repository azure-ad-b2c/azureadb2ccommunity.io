---
title: "Basic policies"
permalink: /docs/app-integration/
excerpt: "App Integration"
last_modified_at: 2020-04-06T21:36:11-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Vinu Gunasekaran
---
Your application, regardless if it is a web, mobile, desktop or single page application, initiates an authentication request referencing a policy id, such that a specific policy can be executed. The policy controls what the experience will be for this scenario.  The policy generates a token at the end of its execution, and it will be sent to your application to be consumed. This flow applies to any Azure AD B2C policy. 

The steps inside a policy can vary from policy to policy. It is truly based on set of steps you specify.
 
![A policy](/assets/images/docs/app-integration.png) 