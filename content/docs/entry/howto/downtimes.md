---
Title: Configuring Downtime
Date: 2019-07-16T13:00:00+09:00
URL: https://mackerel.io/docs/entry/howto/downtimes
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613439795642
---

In Mackerel, it's possible to suspend monitoring for a specified period of time.
You can set alerts not to occur during periods when the load is known to increase, such as regular batch processing and maintenance periods.

This configuration which includes the period of monitoring suspension and the target scope is referred to as downtime.

[:contents]

## Configuring Downtime
### From the console screen
Downtime can be configured from https://mackerel.io/my/downtimes.

The most basic downtime specifies only the time period for which monitoring is to be suspended.
Set the start date and time and the duration of suspension.
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190906/20190906173106.png)

To have the downtime repeat at regular intervals, configure the recurrence setting.
Settings such as every other weekend can also be configured.
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190906/20190906173111.png)

Lastly, set the target scope for the downtime.
Filtering based on services, roles, and monitoring settings as well as exlusion settings are supported.
Specifying services and roles and specifying monitoring settings are AND conditions.
![](https://cdn-ak.f.st-hatena.com/images/fotolife/m/mackerelio/20190906/20190906173100.png)

### From the API
Downtime can also be configured using the API.
The following command configures a downtime for the target `TestService` that starts from the current time with a duration of one hour.

```shell
curl -X POST https://api.mackerelio.com/api/v0/downtimes \
    -H "X-Api-Key: $MACKEREL_APIKEY" \
    -H "Content-Type: application/json" \
    -d '{"name": "Test downtime", "start": '$(date +%s)', "duration": 60, "serviceScopes": ["TestService"] }'
```

For more detailed information, check out the [API document](https://mackerel.io/api-docs/entry/downtimes)

### Notes

Please note that due to Mackerel specifications, if downtime starts on Sunday and exceeds 24 hours, downtime will be ignored after 24 hours.  
For example, if the downtime start time is set to Sunday 00:00 and the downtime period is set to 24 hours and 30 minutes, monitoring will not stop on Monday from 00:00 to 00:30. The same is true for a repeating setting.
For downtime beginning on Sunday, the duration should not exceed 24 hours.
