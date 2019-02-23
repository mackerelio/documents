---
Title: FAQ・I’m not receiving any emails.
Date: 2017-12-15T15:44:20+09:00
URL: https://mackerel.io/docs/entry/faq/troubleshoot/not-delivered-mail
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/8599973812326835408
---

Account confirmation, alert notification, and other various emails are sent from Mackerel. If for some reason emails from Mackerel are not appearing, please refer to the following list of possible causes.

* emails from `noreply@mackerel.io` can not be received
  * If you are using email filtering, please make sure that your filter settings are configured so that `mackerel.io` emails can be received.
* Mail from `mackerel.io` gets recognized as spam.
  * If emails from Mackerel are being filtered as spam or junk mail, please check your spam filter settings. If you are using gmail, please refer to the gmail support page. (https://support.google.com/mail/answer/1366858 )
  * mackerel.io is an [SPF-verified domain](http://wikipedia.org/wiki/Sender_Policy_Framework). In your spam filter settings, there should be an option to allow mail from SPF-verified domains.
* No alert notification emails.
  * Alert notification emails will not be sent if you have not completed registration. To begin receiving alert notification email, please complete registration by verifying your account via the account verification email that was sent.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
