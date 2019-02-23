---
Title: Using 2-step authentication
Date: 2016-08-12T11:07:11+09:00
URL: https://mackerel.io/docs/entry/howto/MFA
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687178453702
---

<h2 id="about-mfa">What is 2-step authentication?</h2>

By enabling Mackerel 2-step authentication for your Mackerel account, an authentication code obtained from your device, in addition to the username and password, will be required when signing-in. This not only strengthens the security of affiliated organizations, but also contributes to the protection of customer resources.

<h2 id="enable-mfa">Enable 2-step authentication</h2>

1. If you want to configure authentication for an external account only, use the password obtained from the [account configuration page](https://mackerel.io/settings/user?tab=account).
2. Go to the [2-step authentication activation page](https://mackerel.io/settings/user/mfa/enroll) and follow the guide.

Upon completing the configuration, the next time you sign-in to Mackerel, you will be prompted to enter the authentication code.

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160810/20160810185623.png)

For example, if using [Google Authenticator](https://support.google.com/accounts/answer/1066447) as an authentication application, the authentication code will be displayed like shown in the image below. Enter this code into the appropriate space. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160810/20160810190036.png)

Please note that when you enable 2-step authentication, log-in sessions from other terminals won’t retire. Additionally, even if 2-step authentication is enabled, API access will not be affected. 

<h2 id="about-recovery-code">Authentication with recovery code</h2>

In the event that you are no longer able to use the device from which the authentication code was issued, you can sign-in with a 10-digit single-use recovery code in place of the authentication code. We recommend that you download the recovery code from the [2-step authentication configuration page](https://mackerel.io/settings/user/mfa/configure), print it out, and keep it in a safe place. The recovery code can be re-issued from the [2-step authentication configuration page](https://mackerel.io/settings/user/mfa/configure).

### Sign-in with the recovery code

After authenticating with your email address and password, click on “enter recovery code”. This will take you to the recovery code authentication page. Select a code that you haven’t used before from the list prepared and enter it into the appropriate space.  

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160810/20160810185734.png)

We recommend printing out the list of recovery codes and checking-off the ones that you have already used as shown in the below image. It’s also possible to check the recovery code from [2-step authentication configuration page](https://mackerel.io/settings/user/mfa/configure). If the number of remaining codes is low, they can be re-issued. 

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20160810/20160810185858.png)

<h2 id="disable-mfa">Disable 2-step authentication</h2>

2-step authentication can be disabled from the [2-step authentication configuration page](https://mackerel.io/settings/user/mfa/configure). By disabling the 2-step authentication, both the existing authentication application and the recovery code will be disabled.

<h2 id="move-to-new-device">Moving to a new device</h2>

If you would like to enable 2-step authentication on a new/recently purchased device, follow the guide below.

1. Prepare a separate terminal from the one currently signed-in to Mackerel. You can sign-in using a recovery code if prepared in advance. 
2. Go to the [2-step authentication configuration page](https://mackerel.io/settings/user/mfa/configure) from the terminal currently signed-in and click on the “reconfigure authentication application” button. Or go directly to the [Enable 2-step authentication page](https://mackerel.io/settings/user/mfa/enroll) and follow the guide to enable 2-step authentication for the new terminal.
3. And lastly, delete the Mackerel account from the old device’s authentication application because the authentication code issued is now disabled.
