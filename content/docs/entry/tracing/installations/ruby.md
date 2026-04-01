---
Title: Sending traces from Ruby applications to Mackerel
Date: 2025-07-11T16:36:22+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/ruby
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507896762
---

This page explains how to send traces from Ruby applications to Mackerel.

[:contents]

## Overview

Mackerel collects traces using the OpenTelemetry mechanism (instrumentation). By using the [instrumentation libraries](https://opentelemetry.io/docs/languages/ruby/libraries/) provided for major frameworks and libraries such as Ruby on Rails and ActiveRecord, you can send traces without significantly modifying your existing implementation.

There are several web frameworks available for Ruby, but this page explains how to get started with Ruby on Rails.

<div class="note">
<p>📝 Note</p>
<p>Available instrumentation libraries can be searched in the <a href="https://opentelemetry.io/ecosystem/registry/?language=ruby&component=instrumentation">registry</a>. If there is no instrumentation library for your framework or application, you can implement manual instrumentation. For more details, see <a href="https://opentelemetry.io/docs/languages/ruby/instrumentation/">Instrumentation</a> in the official OpenTelemetry documentation.</p>
</div>

## Requirements

According to the OpenTelemetry [prerequisites](https://opentelemetry.io/docs/languages/ruby/getting-started/#prerequisites), the following must be installed.

- One of the following:
  - CRuby 3.1 or higher
  - JRuby 9.3.2.0 or higher
  - TruffleRuby 22.1 or higher
- Bundler

## Installation

To send traces from your application to Mackerel, do the following:

1. Add the gems
2. Initialize settings in initializers
3. Catch error details (optional)

### 1. Add the gems

The following gems automatically instrument requests to Ruby on Rails and queries to the database.

```ruby
gem 'opentelemetry-sdk'
gem 'opentelemetry-exporter-otlp'
gem 'opentelemetry-instrumentation-all'
```

Also, since `opentelemetry-instrumentation-all` includes many gems, you can use only the individual gems as shown below.

```ruby
gem 'opentelemetry-instrumentation-active_support'
gem 'opentelemetry-instrumentation-active_record'
gem 'opentelemetry-instrumentation-rack'
gem 'opentelemetry-instrumentation-rails'
```

### 2. Initialize settings in initializers

Configure settings in initializers to send traces. Place the following in `config/initializers/opentelemetry.rb` and start the application to send traces.

```ruby
require 'socket'
require 'opentelemetry/sdk'
require 'opentelemetry/exporter/otlp'
require 'opentelemetry/instrumentation/all'

OpenTelemetry::SDK.configure do |c|
  c.service_name = "my-sample-app"
  c.service_version = "vX.Y.Z"
  c.resource = OpenTelemetry::SDK::Resources::Resource.create(
    OpenTelemetry::SemanticConventions::Resource::DEPLOYMENT_ENVIRONMENT => Rails.env.to_s,
    OpenTelemetry::SemanticConventions::Resource::HOST_NAME => Socket.gethostname
  )

  # exporter settings
  exporter = OpenTelemetry::Exporter::OTLP::Exporter.new(
    endpoint: "https://otlp-vaxila.mackerelio.com/v1/traces",
    headers: {
      "Accept" => "*/*",
      "Mackerel-Api-Key" => ENV["MACKEREL_APIKEY"]
    }
  )
  # exporter = OpenTelemetry::SDK::Trace::Export::ConsoleSpanExporter.new
  
  c.add_span_processor(
    OpenTelemetry::SDK::Trace::Export::BatchSpanProcessor.new(exporter)
  )

  # When using c.use_all, the gems to use are automatically determined.
  c.use_all

  # You can also explicitly specify the instrumentation target using c.use(...).
  # c.use('OpenTelemetry::Instrumentation::ActiveRecord')
  # c.use('OpenTelemetry::Instrumentation::Mysql2')
  # c.use('OpenTelemetry::Instrumentation::Rails')
  # ...
end
```

- `service_name` is the service name of the trace (the value of the `service.name` attribute).
- `service_version` can include version information (optional).
- `resource` can specify attributes based on the OpenTelemetry [semantic conventions](https://opentelemetry.io/docs/specs/semconv/resource/). This allows for detailed identification of the trace source (optional).
  - `DEPLOYMENT_ENVIRONMENT`: Specifies the deployment environment (development, staging, production, etc.). This example uses the Rails environment name (`Rails.env`).
  - `HOST_NAME`: Specifies the hostname where the application is running. This example uses `Socket.gethostname` to automatically retrieve the hostname.
  - For other attributes, see [OpenTelemetry::SemanticConventions::Resource](https://open-telemetry.github.io/opentelemetry-ruby/opentelemetry-semantic_conventions/v1.8.0/OpenTelemetry/SemanticConventions/Resource.html).
- `exporter`
  - `endpoint` specifies the destination for sending traces.
    - To send directly to Mackerel, specify `https://otlp-vaxila.mackerelio.com/v1/traces`.
    - To send via a Collector, specify `http://<Collector address:port>/v1/traces`.
  - `${MACKEREL_APIKEY}` specifies the Mackerel API key. Define an API key with Write permission from the [API key list](https://mackerel.io/my?tab=apikeys) as an environment variable in the system where the application runs.
    - It will work if you write the API key directly instead of using an environment variable.
  - Specifying `ConsoleSpanExporter` will output traces to standard output.

### 3. Catch error details (optional)

Mackerel can track errors, but Ruby's OpenTelemetry does not send error details by default.

To send error details, you need to catch errors and record them in a span using one of the following methods.

1. Catch errors using `rescue_from`
2. Catch errors using Middleware

For example, if you use `rescue_from`, you would do the following.

```ruby
class ApplicationController < ActionController::API
  rescue_from Exception do |e|
    OpenTelemetry::Trace.current_span.record_exception(e)

    raise e
  end
end
```

## Viewing sent traces

Sent traces can be verified by following these steps:

1. Select "[APM](https://mackerel.io/my/apm)" from the menu<br>
2. Select the service name
  [f:id:mackerelio:20260109153548j:plain]
3. Select the "Traces" tab
  [f:id:mackerelio:20260109153629j:plain]
4. Select a trace from the trace list to view its details
  [f:id:mackerelio:20260109153349p:plain]

That's a wrap on setting up OpenTelemetry in your Ruby application and sending traces to Mackerel.
