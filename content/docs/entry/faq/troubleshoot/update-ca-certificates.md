---
Title: 'FAQ・The agent is outputting the error "x509: certificate signed by unknown
  authority"'
Date: 2020-05-08T11:38:31+09:00
URL: https://mackerel.io/docs/entry/faq/troubleshoot/update-ca-certificates
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/26006613563838455
---

If mackerel-agent outputs the following log;

```
Failed to post metrics value (will retry): Post https://mackerel.io/api/v0/tsdb: x509: certificate signed by unknown authority
```

It’s possible that the CA certificate of the host on which mackerel-agent is installed is too old. Please consider updating the `ca-certificates` package or performing a Windows Update. (As the output states, metric posting `will retry` when communication returns to normal.)

Also, make sure that your OS is one that is officially supported.

This can happen if the certificate needs to be renewed in order to establish SSL/TLS communication. This event is not limited to Mackerel and can occur in any case regarding SSL/TLS communication with the host, so we recommend that you update the host’s CA certificate regularly.

---

[< Return to FAQ](https://mackerel.io/docs/entry/faq)
