---
Title: Tracing - Introducing OpenTelemetry to Python
Date: 2025-07-11T16:34:11+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/python
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507896178
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to acquire data.

This page explains how to send data from Python to Mackerel.

[:contents]

## OpenTelemetry for Python

OpenTelemetry provides an SDK for Python.

In addition to this SDK, you can use SDKs for Django and AWS to instrument various areas.

[https://opentelemetry.io/docs/languages/python/instrumentation/:embed:cite]

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can either send it directly from the SDK or use a Collector.

Refer to the following page to decide whether to use a Collector.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## How to get started

There are multiple web frameworks and servers available for Python, but this page explains how to get started with `Django` and `Gunicorn`. You can instrument other frameworks such as Flask and FastAPI using almost the same approaches.

You can implement Mackerel's tracing feature by following the steps below.

1. Add packages
2. Initial setup
3. Gunicorn settings
4. Add custom instrumentation (optional)

### 1. Add packages

Use the following packages.

```bash
pip install opentelemetry-api \
  opentelemetry-exporter-otlp-proto-http \
  opentelemetry-instrumentation-django \
  opentelemetry-instrumentation-dbapi \
  opentelemetry-sdk
```

This command installs packages for instrumenting Django and the database.

There are also packages for various other libraries such as AWS, Jinja, and Redis, which can be found on the OpenTelemetry website.

[https://opentelemetry.io/ecosystem/registry/?language=python&component=instrumentation:embed:cite]

### 2. Initial setup

To send OpenTelemetry data to Mackerel, you need to configure the following items.

* Resource
* SpanProcessor

For example, the following configuration allows you to send data directly from the SDK to Mackerel.

```python
import os
import socket
import MySQLdb
from opentelemetry import trace
from opentelemetry.exporter.otlp.proto.http import Compression
from opentelemetry.exporter.otlp.proto.http.trace_exporter import OTLPSpanExporter
from opentelemetry.instrumentation.django import DjangoInstrumentor
from opentelemetry.instrumentation.dbapi import trace_integration
from opentelemetry.sdk.resources import Resource
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor, ConsoleSpanExporter
from opentelemetry.semconv.resource import ResourceAttributes

def instrument():
    tracer_provider = TracerProvider(resource=Resource.create({
        ResourceAttributes.SERVICE_NAME: "acme_service",
        ResourceAttributes.SERVICE_VERSION: "vX.Y.Z",
        ResourceAttributes.DEPLOYMENT_ENVIRONMENT: "production",
        ResourceAttributes.HOST_NAME: socket.gethostname(),
        ResourceAttributes.PROCESS_PID: os.getpid(),
    }))
    tracer_provider.add_span_processor(
        # ConsoleSpanExporter is useful during debugging.
        # BatchSpanProcessor(ConsoleSpanExporter())

        BatchSpanProcessor(OTLPSpanExporter(
            endpoint="https://otlp-vaxila.mackerelio.com/v1/traces",
            headers={
              "Mackerel-Api-Key": os.environ["MACKEREL_API_KEY"],
              "Accept": "*/*",
            },
            compression=Compression.Gzip
        ))
    )
    trace.set_tracer_provider(tracer_provider)

    DjangoInstrumentor().instrument()
    trace_integration(MySQLdb, "connect", "mysql")
```

In this example, the following items are set.

* Resource
  * By setting the Resource of TracerProvider, you can identify where the data came from.
* Endpoint
  * By setting `https://otlp-vaxila.mackerelio.com/v1/traces`, the data will be sent directly to Mackerel.
  * If using a Collector, set the Collector's endpoint (e.g., `http://localhost:4318/v1/traces`).
* Headers
  * Setting the `Mackerel-Api-Key` and `Accept` headers enables communication with Mackerel.
  * Set the `Mackerel-Api-Key` header to the API key with write permissions issued by Mackerel. It may take about a minute for changes to take effect after modifying the API key permissions.
  * When using Collector, headers are not required.
* Compression
  * You can specify the data compression method when sending data. If not specified, data will not be compressed.

### 3. Configuring the HTTP server

For servers that fork processes like `Gunicorn`, you need to configure OpenTelemetry after forking.

To do this, call the function you created earlier in the Gunicorn configuration file, such as `gunicorn.conf.py`.

```python
def post_fork(server, worker):
    instrument()
```

If you are using `uWSGI`, call it within `@postfork`.

```python
@postfork
def init_tracing():
    instrument()
```

It can also be called from `wsgi.py`.

```python
os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'mysite.settings')

instrument()
```

If you are using Django's built-in `manage.py`, call it in `main`.

```python
def main():
    """Run administrative tasks."""
    os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'message.settings')

    # Add the following line
    instrument()

    try:
        from django.core.management import execute_from_command_line
    ...
```

### 4. Adding custom instrumentation (optional)

By adding custom instrumentation, you can instrument any range.

Instrumentation makes it possible to record variable values and processing times.

you can add instrumentation using `start_as_current_span`, as shown below.

```python
tracer = trace.get_tracer("my.tracer.name")

def awesome_action(id: int):
    with tracer.start_as_current_span("awesome_action") as span:
        span.set_attribute("id", id)

        # ... existing processing
```

Other instrumentation approaches are available. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/python/instrumentation/:embed:cite]
