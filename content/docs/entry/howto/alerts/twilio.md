---
Title: Send Notifications in Twilio
Date: 2016-11-11T17:02:37+09:00
URL: https://mackerel.io/docs/entry/howto/alerts/twilio
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/10328749687193932961
---

[Twilio](http://twilio.kddi-web.com) is a service that let’s you make calls and send messages via API. 

In Mackerel, by registering the access token and phone number created with Twilio, you can now receive notifications via SMS or a phone call when a Mackerel alert occurs. [Twilio for KDDI Web Communications](http://twilio.kddi-web.com) or [Twilio](https://www.twilio.com/) can be used.

Using Twilio, any of the following 3 types of notifications can be configured for one notification channel. If more than one type of notification is necessary, add multiple notification channels using the same AccountSid and AuthToken.

####  SMS

Notify alert content by SMS.

#### Phone call notification (Notify with fixed messages)

When an alert occurs, a call is made and a fixed message is transmitted.  

#### Phone call notification (Notify by specifying the TwiML endpoint)

When an alert occurs, a call is made using [TwiML](https://jp.twilio.com/docs/api/twiml) delivered from the specified endpoint. Use this when an automated correspondence other than a simple message is desired. 

To configure notifications using Twilio, add a new channel from the [Twilio form on the channel’s page](https://mackerel.io/my/channels/-/create#twilio).

![](https://cdn-ak.f.st-hatena.com/images/fotolife/a/andyyk/20161111/20161111165830.png)

## Common configuration items

Among the configuration items, the following are common to both SMS and call notifications.

### From

The phone number from which notifications will be sent. Enter the phone number purchased by Twilio in E.164 format starting with `+` and the country code. Any arbitrary phone number (a cell phone number etc.) can not be specified.

Depending on the Twilio number, phone call notifications as well as SMS may not be supported. When adding a new channel, register a number that supports your desired method of notifications with Twilio. In particular, Twilio does not support SMS transmissions from Japanese domestic phone numbers as of October 2016. To use SMS notifications, select a phone number supported with SMS notifications such as one from the United States (even if the target is within Japan).

### To

The phone number to which notifications will be sent. As with “From”, enter the number in E.164 format starting with `+` followed by the country code.

### Status Callback URL (Option)

This is the endpoint to be called back from Twilio by HTTP when an SMS or call notification is made.  You can receive the outcome of SMS transmissions and whether or not calls were received. For more details, reference the [Twilio document](https://twilio.com/docs/).

## Notification Type

In Notification Type, you can select which of the above three notifications to use. There are additional configuration items for both types of phone call notifications.

### Notification level

Notification levels can be configured for both cases: when using a fixed message or by specifying the TwiML endpoint. By default, a call will be dispatched for both “Warning & Critical” alerts, but can be changed to “Critical only”. Additionally, if using phone call notifications and the alert status returns to `ok`, the call will not be made. 

### Message / Language

This item is configured for notifications with fixed messages only. Specify the language parameters and the contents of the message to be transmitted when a phone call notification occurs. For more details about language parameters, refer to the [Twilio document](https://twilio.com/docs/api/twiml/say#attributes-language).

### TwiML URL

This item is configured for TwiML endpoint specified notifications only.

Specify the external URL to return the [TwiML](https://twilio.com/docs/api/twiml) format response. This URL is accessed from the Twilio server. In the Twilio notification channel in Mackerel, access to this endpoint from Twilio will be performed with the GET method. In order to perform notifications, the [TwiML](https://twilio.com/docs/api/twiml)  will need to respond to accesses that use the GET method.

Additionally, if query parameters are not included in the configured URL, they will be automatically assigned to the requested URL displaying the id of the corresponding alert in the form of `?alertId={alertId}`. By combining with the [alert API](https://mackerel.io/api-docs/entry/alerts), it can be used to construct automatic correspondence to trigger call notifications etc.  If query parameters are already included in the configured URL, additional query parameters will not be assigned by Mackerel. 

## Notes

When notifying with a fixed message, we use the following TwiML text.

```xml
<Response>
<Say language="@(language)">
@(message)
</Say>
</Response>
```
