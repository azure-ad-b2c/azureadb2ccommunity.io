---
title: "UI customization"
permalink: /docs/Customization/
excerpt: "UI customization"
last_modified_at: 2020-04-06T21:36:11-04:00
redirect_from:
  - /theme-setup/
toc: true
author: Vinu Gunasekaran
---
## HTML Templates
You can customize the look and feel of any Self-Asserted technical profile. To customize the user interface:

1. Create an HTML page with your own branding. This page can be a static one `*.html`, or a dynamic HTML page (e.g .NET, Node.js, PHP) which will serve the content. The HTML template can contain any HTML elements (except insecure elements, such as iframes) CSS styling, and JavaScript. The only required element is a **div** element with id set to 'api', such as this one `<div id="api"></div>` within your HTML page.
1. Publish the HTML page to an anonymous web server, and set the CORS to allow the Azure AD B2C domain to request your content.
1. In the policy you point to the HTML template you published.

During run time, Azure AD B2C will loads your HTML template and merge the Azure AD B2C user interface elements into the `<div id="api"></div>` element located in the custom HTML content.

![Content definitions](/assets/images/docs/ui-customization.png)

## Localization
Localization allows you to support multiple locales or languages in the policy for the user journeys. The localization support in policies allows you to:
- Set up the explicit list of the supported languages in a policy and pick a default language.
- Provide language-specific strings and collection

![Localization](/assets/images/docs/localization.png)

 