---
Title: Authentication with SAML
Date: 2024-08-02T14:00:00+09:00
URL: https://mackerel.io/docs/entry/advanced/saml-authentication
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802340630902355593
CustomPath: advanced/saml-authentication
---

**This feature is only enabled for the users in organizations which have made a contract for a higher-tier Mackerel plan which includes SAML authentication, and have set up SAML authentication.**
For information on setting up SAML authentication, please see [Setting up SAML](https://mackerel.io/docs/entry/advanced/saml-settings).

When using SAML authentication, you can sign in to Mackerel using credentials from your Identity Provider.

## Authentication flow

### SP initiated flow

In the SP initiated flow, start SAML authentication from the Mackerel [sign-in page](https://mackerel.io/signin/saml).

![SAML sign-in screen in the Mackerel SP initiated flow. There is a form to enter the organization name and a Sign in button.](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20240830/20240830182141_original.png)

Enter the organization group name specified by the organization group Manager, click the Sign in button, and you will be redirected to your Identity Provider.

When authentication by the Identity Provider succeeds, you can sign in to Mackerel.

### IdP initiated flow

In the IdP initiated flow, you can sign in to Mackerel by launching it from your Identity Provider's app drawer or app portal, etc.

## JIT Provisioning

Mackerel supports JIT Provisioning sign-up.
You can start using Mackerel seamlessly through the SAML authentication flow.

1. Refer to [Authentication Flow](#authentication-flow) for SAML authentication.
2. The sign-up page will appear. If you agree to the Terms of Use and you are an adult or a minor with parental consent, please check two boxes.
3. Click on Sign Up.
4. An email will be sent to you to confirm your email address. Access the URL in the email to complete registration.
