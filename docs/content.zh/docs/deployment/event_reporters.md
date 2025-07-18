---
title: "Event Reporters"
weight: 8
type: docs
aliases:
  - /deployment/event_reporters.html
---
<!--
Licensed to the Apache Software Foundation (ASF) under one
or more contributor license agreements.  See the NOTICE file
distributed with this work for additional information
regarding copyright ownership.  The ASF licenses this file
to you under the Apache License, Version 2.0 (the
"License"); you may not use this file except in compliance
with the License.  You may obtain a copy of the License at

  http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing,
software distributed under the License is distributed on an
"AS IS" BASIS, WITHOUT WARRANTIES OR CONDITIONS OF ANY
KIND, either express or implied.  See the License for the
specific language governing permissions and limitations
under the License.
-->

# Event Reporters

Flink allows reporting events (structured logging) to external systems.
For more information about Flink's event reporting system go to the [events system documentation]({{< ref "docs/ops/events" >}}).

Events can be exposed to an external system by configuring one or several reporters in [Flink configuration file]({{< ref "docs/deployment/config#flink-configuration-file" >}}).
These reporters will be instantiated on each job and task manager when they are started.

Below is a list of parameters that are generally applicable to all reporters.
All properties are configured by setting `events.reporter.<reporter_name>.<property>` in the configuration.
Reporters may additionally offer implementation-specific parameters, which are documented in the respective reporter's section. 

{{< include_reporter_config "layouts/shortcodes/generated/event_reporters_section.html" >}}

All reporter configurations must contain the `factory.class` property.

Example reporter configuration that specifies multiple reporters:

```yaml
events.reporters: otel,my_other_otel

events.reporter.otel.factory.class: org.apache.flink.events.otel.OpenTelemetryEventReporterFactory
events.reporter.otel.exporter.endpoint: http://127.0.0.1:1337
events.reporter.otel.scope.variables.additional: region:eu-west-1,environment:local,flink_runtime:1.17.1

events.reporter.my_other_otel.factory.class: org.apache.flink.common.events.OpenTelemetryEventReporterFactory
events.reporter.my_other_otel.exporter.endpoint: http://196.168.0.1:31337
```

**Important:** The jar containing the reporter must be accessible when Flink is started.
 Reporters are loaded as [plugins]({{< ref "docs/deployment/filesystems/plugins" >}}).
 All reporters documented on this page are available by default.

You can write your own `Reporter` by implementing the `org.apache.flink.events.reporter.EventReporter` and `org.apache.flink.events.reporter.EventReporterFactory` interfaces.
Be careful that all the methods must not block for a significant amount of time, and any reporter needing more time should run the operation asynchronously.

## Reporters

The following sections list the supported reporters.

### OpenTelemetry
#### (org.apache.flink.events.otel.OpenTelemetryEventReporterFactory)

`OpenTelemetryEventReporterFactory` currently supports only gRPC.

Parameters:

{{< include_reporter_config "layouts/shortcodes/generated/open_telemetry_reporter_configuration.html" >}}

Example configuration:

```yaml
events.reporter.otel.factory.class: org.apache.flink.events.otel.OpenTelemetryEventReporterFactory
events.reporter.otel.exporter.endpoint: http://127.0.0.1:1337
```

### Slf4j
#### (org.apache.flink.events.slf4j.Slf4jEventReporter)

Example configuration:

```yaml
events.reporter.slf4j.factory.class: org.apache.flink.events.slf4j.Slf4jEventReporterFactory
```
{{< top >}}
