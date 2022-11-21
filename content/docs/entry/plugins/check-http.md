---
Title: Check plugins - check-http
Date: 2022-11-21T18:09:14+09:00
URL: https://mackerel.io/docs/entry/plugins/check-http
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/4207112889938595168
CustomPath: plugins/check-http
---

The check-http plugin performs monitoring of a desired server via HTTP connection. 

[:contents]

<h2 id="options">Configurable options</h2>

| Option                 | Abbreviation | Required | Explanation                                                                                    | Multiple Allowed | Default Value  |
| ---------------------- | ------------ | -------- | ---------------------------------------------------------------------------------------------- | ---------------- | -------------- |
| --url                  | -u           | ◯        | URL to connect to                                                                              |                  |                |
| --status               | -s           |          | Designate the monitoring results for each HTTP response code in the format `key=value`         | ○                |                |
| --no-check-certificate |              |          | Do not authenticate certificate                                                                |                  |                |
| --source-ip            | -i           |          | Source IP address                                                                              |                  |                |
|                        | -H           |          | HTTP request header                                                                            | ○                |                |
| --pattern              | -p           |          | Regular expression to check the HTTP response body                                             |                  |                |
| --max-redirects        |              |          | Maximum number of redirects to follow                                                          |                  | 10             |
| --method               | -m           |          | HTTP request method (Specify `GET`, `HEAD`, `POST`, or `PUT`)                                  |                  | GET            |
| --connect-to           |              |          | Connect to HOST2:PORT2 instead of HOST1:PORT1. Specify in the format `HOST1:PORT1:HOST2:PORT2` | ○                |                |
| --proxy                | -x           |          | Send requests via HTTP proxy. Specify in the format `[PROTOCOL://][USER:PASS@]HOST[:PORT]`     |                  | `PORT` is 1080 |
| --user                 |              |          | Assign the user ID and password for Basic authentication in the format `USER[:PASSWORD]`       |                  |                |
| --require-bytes        | -B           |          | Checks that response size matches designated number of bytes                                   |                  | -1             |
| --body                 | -d           |          | Request body                                                                                   |                  |                |
| --min-bytes            | -g           |          | Checks that responses are at least the designated number of bytes                              |                  | -1             |
| --timeout              | -t           |          | Timeout length (seconds)                                                                       |                  | 0              |
| --cert-file            |              |          | Assign certificate used for client authentication                                              |                  |                |
| --key-file             |              |          | Assign key file used for client authentication                                                 |                  |                |
| --ca-file              |              |          | Assign CA certificate used for client authentication                                           |                  |                |

<h3 id="options">Monitoring results by HTTP Response code</h3>

The monitoring results for each HTTP response code can be configured via the `--status` option, but if it they are not configured, the monitoring results are as below.

| HTTP Response Code | Monitoring Results |
| ------------------ | ------------------ |
| 100 level          | OK                 |
| 200 level          | OK                 |
| 300 level          | OK                 |
| 400 level          | WARNING            |
| Other              | CRITICAL           |

- The monitoring results for each HTTP response code can be configured via the `--status` option in the format `key-value`.
- HTTP response codes may also be assigned to a range, such as `200-404`.

<h2 id="config">Example configurations</h2>

To monitor `https://mackerel.io/`, use the following configuration.

‘’’
[plugin.checks.https-mackerelio]
command = ["check-http", "-u", "https://mackerel.io"]
‘’’

To set the monitoring result for the HTTP response of 404 to `OK`, use the following configuration.

‘’’
[plugin.checks.https-mackerelio]
command = ["check-http", "-u", "https://mackerel.io", "--status", "404=OK"]
‘’’

It is also possible to designate the HTTP status code in the format `200-404`.

‘’’
[plugin.checks.https-mackerelio]
command = ["check-http", "-u", "https://mackerel.io", "--status", "200-404=OK"]
‘’’

<h2 id="tips">Tips</h2>

Usually, for monitoring Internet-exposed endpoints as in the [Example configurations](##Example configurations), [External URL Monitoring] (https://mackerel.io/docs/entry/external-monitoring) via the main Mackerel service is recommended.

Please use the check-http plugin as an internal monitoring method for monitoring internal APIs, etc., on the same network which cannot be monitored directly from the Internet.

It can also be used as a substitute to monitor URLs which require client authentication, etc., that are not compatible with external URL monitoring.

<h2 id="repository">Repository</h2>
https://github.com/mackerelio/go-check-plugins/tree/master/check-http
