---
Title: Tracing - Introducing OpenTelemetry to Ruby
Date: 2025-07-11T16:36:22+09:00
URL: https://mackerel.io/docs/entry/tracing/installations/ruby
EditURL: https://blog.hatena.ne.jp/mackerelio/mackerelio-docs.hatenablog.mackerel.io/atom/entry/6802418398507896762
---

Mackerel uses the OpenTelemetry mechanism (instrumentation) to collect data.

This page explains how to send data from Ruby to Mackerel.

[:contents]

## OpenTelemetry for Ruby

OpenTelemetry provides an SDK for Ruby.

In addition to this SDK, you can use SDKs for Ruby on Rails and ActiveRecord to instrument your application without modifying your existing implementation.

[https://opentelemetry.io/docs/languages/ruby/instrumentation/:embed:cite]

### Is it appropriate to use a Collector?

When sending data to Mackerel, you can use a Collector instead of sending directly from the SDK.

Refer to the following page when deciding whether to use a Collector.

[Deciding whether to use Collector](https://mackerel.io/docs/entry/tracing/guide/what-is-opentelemetry#using-collector-or-not)

## How to get started

There are several frameworks available for Ruby, but this page explains how to get started with Ruby on Rails.

You can implement Mackerel's tracing feature by following the steps below.

1. Add the gem
2. Initialize settings in initializers
3. Add error handling
4. Add custom instrumentation (optional)

### 1. Add the gems

The following gems automatically instrument around requests to Ruby on Rails and queries to the database.

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

If it’s missing, you can find the gem on the OpenTelemetry page.

[https://opentelemetry.io/ecosystem/registry/?language=ruby&component=instrumentation:embed:cite]

### 2. Initial settings in initializers

In order to send OpenTelemetry data to Mackerel, you need to configure settings in initializers.

In `OpenTelemetry::SDK.configure`, you can configure the following items.

* service_name
* service_version
* resource
* span_processor

For example, setting the following in `config/initializers/opentelemetry.rb` will allow the SDK to send data directly to Mackerel.

```ruby
require 'opentelemetry/sdk'
require 'opentelemetry/exporter/otlp'
require 'opentelemetry/instrumentation/all'

OpenTelemetry::SDK.configure do |c|
  c.service_name = "acme_service"
  c.service_version = "vX.Y.Z"
  c.resource = OpenTelemetry::SDK::Resources::Resource.create(
    OpenTelemetry::SemanticConventions::Resource::DEPLOYMENT_ENVIRONMENT => Rails.env.to_s,
    OpenTelemetry::SemanticConventions::Resource::HOST_NAME => Socket.gethostname
  )

  exporter = OpenTelemetry::Exporter::OTLP::Exporter.new(
    endpoint: "https://otlp-vaxila.mackerelio.com/v1/traces",
    headers: {
      "Accept" => "*/*",
      "Mackerel-Api-Key" => ENV["MACKEREL_API_KEY"]
    }
  )
  # ConsoleSpanExporter is useful during debugging.
  # exporter = OpenTelemetry::SDK::Trace::Export::ConsoleSpanExporter.new
  
  c.add_span_processor(
    OpenTelemetry::SDK::Trace::Export::BatchSpanProcessor.new(exporter)
  )

  # c.use_all is used, the Gem to be used is automatically determined.
  c.use_all

  # You can also explicitly specify the instrumentation target using c.use(...).
  # c.use('OpenTelemetry::Instrumentation::ActiveRecord')
  # c.use('OpenTelemetry::Instrumentation::Mysql2')
  # c.use('OpenTelemetry::Instrumentation::Rails')
  # ...
end
```

In this example, the following items are set.

* service_name, service_version, resource
  * Setting these items allows you to identify the source of the data.
* endpoint
  * Setting this to `https://otlp-vaxila.mackerelio.com/v1/traces` will send the data directly to Mackerel.
  * If using a Collector, set the Collector's endpoint (e.g., `http://localhost:4318/v1/traces`).
* headers
  * Setting the `Mackerel-Api-Key` and `Accept` headers enables communication with Mackerel.
  * Set the `Mackerel-Api-Key` header to the API key with write permissions issued by Mackerel. It may take about a minute for changes to take effect after modifying the API key permissions.
  * When using Collector, headers are not required.

### 3. Catch error details

Mackerel can track errors, but Ruby's OpenTelemetry does not send error details by default.

To send error details, you need to do one of the following.

1. Catch error details using `rescue_from`
2. Catch error details using Middleware

The location where error details are caught will change, but in either case, you only need to add the caught error to Span.

For example, if you use `rescue_from`, you would do the following.

```ruby
class ApplicationController < ActionController
  rescue_from Exception do |e|
    OpenTelemetry::Trace.current_span.record_exception(e)

    raise e
  end
end
```

### 4. Adding custom instrumentation (optional)

By adding custom spans, you can instrument any range.

Instrumentation makes it possible to record variable values and processing times.

You can add instrumentation by enclosing it in `in_span` as shown below.

```ruby
def awesome_action(arg)
  OpenTelemetry.tracer_provider.tracer.in_span("awesome_action", attributes: {"arg" => arg}) do |span|
    # ... existing processing
  end
end
```

Other instrumentation approaches are available. For details, refer to the OpenTelemetry documentation.

[https://opentelemetry.io/docs/languages/ruby/instrumentation/:embed:cite]
