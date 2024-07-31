# Observavility utilities for .NET

## About

These instructions, code snippets and utility tools help you set up the observability of the [.NET](https://dotnet.microsoft.com/)-based  software projects with [OpenTelemetry](https://opentelemetry.io/) using automatic and manual instrumentation, tracing, metrics, collector, logging, context propagation, etc.

## Concepts

### Monitoring 

**Monitoring** involves collecting and analyzing predefined sets of metrics, logs, and traces from your software systems. It focuses on known issues and expected behaviors, typically using dashboards and alerts to track system health and performance against predetermined thresholds.

### Observability

**Observability** is the ability to understand a system's internal state by examining its outputs. It goes beyond monitoring by allowing you to ask new questions and investigate unforeseen issues. With observability, you can quickly derive insights into your system's behavior. Moreover, it can help you predict and prevent problems before they even occur.

### Monitoring vs Observability

While monitoring focuses on known unknowns (predefined metrics and issues you expect), observability deals with unknown unknowns (unanticipated problems or behaviors). Monitoring is a subset of observability. Observability provides a more comprehensive view of your system, enabling deeper troubleshooting and analysis of complex, distributed systems.

### OpenTelemetry

**[OpenTelemetry](https://opentelemetry.io/)** is an open-source framework for observability. It offers a set of APIs, libraries, agents, and instrumentation tools for collecting telemetry data - signals, in particular, metrics, logs, and traces, from software applications and their environments in a standardized format. OpenTelemetry standards, tools, and signals, which are vendor-neutral, make implementing comprehensive observability practices across heterogeneous software environments easier.


### Signals

**Signals** are used in observability and OpenTelemetry to describe the different types of telemetry data that provide information about a system's health, performance, and behavior. The three main types of signals in observability are metrics, logs, and traces. A signal is essentially a stream of data.

#### Metrics 

**Metrics** are numerical measurements of system behavior collected over time. They provide quantitative data about the performance and state of your system. Metrics are associated with the following characteristics:
* Typically aggregated data (e.g., averages, counts, rates)
* Collected at regular intervals
* Compact and efficient to store
* Good for spotting trends and anomalies

_Examples:_ CPU usage, request rate, error rate, latency percentiles

#### Logs

**Logs** are timestamped records of discrete events that happened in the system. They provide a detailed narrative of what occurred at specific points in time. Logs are associated with the following characteristics:
* Textual records with timestamps
* Often unstructured or semi-structured
* Can be high-volume
* Suitable especially for debugging, less so for auditing

_Examples:_ Error messages, application state changes, user actions

#### Traces

**Traces** track the progression of a single request as it moves through various components of a distributed system. They provide insight into the system's behavior and performance for individual transactions. Traces are associated with the following characteristics:
* Represent the end-to-end journey of a request
* Consist of spans (individual operations within the trace)
* Include timing and contextual information
* Suitable for understanding system topology and identifying bottlenecks

_Examples:_ A user's request flowing through microservices, database queries, external API calls

#### Metrics vs Logs vs Traces

Metrics, logs and traces have some conceptual differences:
1. **Granularity:**
   * Metrics: Aggregated, high-level view
   * Logs: Detailed, event-level information
   * Traces: End-to-end view of individual requests
2. **Time scope:**
   * Metrics: Typically cover longer time ranges
   * Logs: Pinpoint specific moments in time
   * Traces: Show the timeline of a single request
3. **Use cases:**
   * Metrics: Trend analysis, alerting, capacity planning
   * Logs: Debugging, security analysis, compliance
   * Traces: Performance optimization, understanding system dependencies
4. **Data volume:**
   * Metrics: Low volume, highly compressible
   * Logs: Can be a high volume
   * Traces: Moderate volume, depending on sampling strategy
5. **Correlation:**
   * Metrics: Often viewed in isolation or correlated with other metrics
   * Logs: Can be correlated using timestamps or IDs
   * Traces: Inherently show the correlation between different system components

Each of these telemetry types provides a different perspective on your system's behavior and performance. When used together, they offer a comprehensive overview of a system and, thus, effective observability. OpenTelemetry provides a standardized way to collect and transmit all three types of telemetry data - signals, making implementing a holistic observability strategy easier.

Understanding the relationship between these signals is critical to effective observability:
1. **Complementary Information:** Each signal provides a different view of your system. Metrics give you a high-level overview, logs provide detailed events, and traces show request flows.
2. **Cross-Signal Analysis:** Correlating data across signals can yield deeper insights. For example, you might use a trace to identify a slow operation, then examine metrics to determine whether it's a systemic issue, and finally examine logs for error messages.
3. **Different Granularity:** Metrics are often aggregated and provide a broad view, while logs and traces offer more granular details about specific events or requests.
4. **Use in Conjunction:** These signals are used together in a well-designed observability system. For instance, an anomaly in metrics might prompt you to examine relevant logs and traces for root cause analysis.

OpenTelemetry is designed to handle these three primary signals (metrics, logs, and traces) in a unified and standardized way. Here's how OpenTelemetry approaches signals:
1. **Unified Data Collection:** OpenTelemetry provides APIs and SDKs for collecting all three signals using a consistent approach across different languages and platforms.
2. **Signal-Specific Components:** While OpenTelemetry unifies the approach to telemetry, it recognizes the unique characteristics of each signal:
    * Metrics API and SDK for collecting and exporting metric data
    * Logging API for integrating with existing logging libraries
    * Tracing API and SDK for creating and managing traces and spans
3. **Correlation Between Signals:** OpenTelemetry enables correlation between different signals by using consistent identifiers and metadata across metrics, logs, and traces.
4. **Extensibility:** While metrics, logs, and traces are the primary signals, OpenTelemetry's architecture allows for the addition of new signal types in the future.
5. **Signal-Agnostic Exporters:** OpenTelemetry's exporter components are designed to handle multiple types of signals, allowing you to send different types of telemetry data to the same backend system.


### Context and Correlation

#### Context

**Context** refers to the additional information that provides a fuller picture of the circumstances surrounding a particular signal, i.e., event, metric, or trace.

Crucial aspects of context:
* It enriches telemetry data with metadata.
* It helps in understanding the environment and conditions in which an operation occurs.
* It can include information such as user ID, session ID, request ID, environment details, etc.

In OpenTelemetry, context is often implemented through **context propagation**, which involves passing this contextual information across different components or services of a distributed system.

#### Correlation

**Correlation** refers to the ability to link related telemetry data across different parts of a system, allowing you to build a comprehensive view of system behavior and performance.

Key aspects of correlation:
* It allows you to connect logs, metrics, and traces that are related to the same operation or transaction.
* It helps in tracing the path of a request through a distributed system.
* It's crucial for root cause analysis and understanding the impact of issues across the system.

#### Context and Correlation in OpenTelemetry 

OpenTelemetry provides mechanisms for both context and correlation:
1. **Context Propagation:** OpenTelemetry offers a standardized way to propagate context across service boundaries. This typically includes:
   * **Trace context:** Information about the current trace and span.
   * **Baggage:** Additional application-defined properties that are propagated throughout the trace.
2. **Correlation through Trace and Span IDs:**
    * Each trace has a unique trace ID.
   * Within a trace, each operation (span) has a unique span ID.
   * Parent-child relationships between spans are maintained.
3. **Attributes and Tags:** OpenTelemetry allows you to add custom attributes to metrics, logs, and traces. These can be used for correlation and providing additional context.
4. **Unified Data Model:** OpenTelemetry's data model ensures that metrics, logs, and traces share common concepts and identifiers, making correlation easier.

**_Example_:** Imagine a user makes a request to your application:
1. The initial service generates a trace ID and span ID.
2. These IDs, along with other contextual information (like user ID), are propagated to all subsequent services handling the request.
3. Each service adds its own spans to the trace, maintaining the parent-child relationship.
4. Logs generated during this process include the trace and span IDs.
5. Metrics collected may be tagged with relevant IDs or attributes.

This allows you to:
* Follow the request's journey through the entire system (tracing).
* Examine logs from all involved services for this specific request.
* Correlate performance metrics with the specific user's experience.

By leveraging context and correlation, OpenTelemetry enables you to gain a holistic view of your system's behavior, making it easier and quicker to debug issues, optimize performance, and understand complex interactions within your distributed system.


