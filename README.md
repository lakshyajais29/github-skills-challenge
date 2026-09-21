# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />


# AIOps Monitoring and Event Processing

## AIOps Scenario

This project simulates an AIOps workflow for monitoring a service called payment-service. The service handles payment requests and generates operational telemetry including response time, CPU usage, memory usage, and log events. The operational problem being addressed is degraded service health caused by latency spikes and resource saturation, which can lead to timeouts and failed transactions.

AIOps is used here to detect abnormal behavior from live operational data, turn those issues into events, pass those events through a lightweight streaming flow, and process the final outcome so the team can identify and respond to service problems more quickly.

## Operational Data Description

The dataset is stored in the service data file and contains a series of records captured over time. Each record includes:

- timestamp
- service
- response_time_ms
- cpu_percent
- memory_percent
- log_level
- message

The timestamps are used to track the sequence of events in order, with records collected approximately one minute apart. This allows the system to compare normal behavior against sudden spikes in latency or resource consumption.

## Observations from the Metrics and Logs

### Metrics fields
The metric fields are:

- response_time_ms
- cpu_percent
- memory_percent

These values represent the operational health of the service and show whether the service is responding normally or under stress.

### Log information
The log-related fields are:

- log_level
- message
- service

These fields provide textual operational context about what happened during each observation.

### Normal behavior
The normal observations are the records around the beginning of the data set, where values remain relatively stable:

- response_time_ms is approximately 120–150 ms
- cpu_percent is around 42–50%
- memory_percent is around 51–57%
- log_level is INFO
- message indicates successful processing

These records represent healthy service operation with no obvious signs of failure.

### Unusual behavior
The unusual observations appear at the later timestamps:

- 2026-09-20T10:05:00
- 2026-09-20T10:06:00

These records show:

- response_time_ms: 610 ms and 640 ms
- cpu_percent: 75% and 94%
- memory_percent: 70% and 91%
- log_level: ERROR
- messages: "Payment service timeout" and "Database connection timeout"

These values clearly indicate a degraded or failing service state and represent the main anomalous behavior in the dataset.

## Anomaly Detection Findings

The anomaly detection logic identifies records that exceed defined thresholds for response time, CPU, and memory usage. It also checks for error-level conditions that can point to operational issues.

The primary anomalies detected are:

1. 2026-09-20T10:05:00
   - High response time
   - Elevated CPU usage
   - Elevated memory usage
   - Error log message: "Payment service timeout"

2. 2026-09-20T10:06:00
   - High response time
   - Very high CPU usage
   - Very high memory usage
   - Error log message: "Database connection timeout"

These anomalies are significant because they combine slow service performance with severe resource pressure and error-level log output. Together, they indicate a likely backend or resource bottleneck affecting the payment service.

The project simulates a basic streaming workflow:

Operational Data -> Anomaly Detection -> Event Generation -> Producer -> Topic -> Consumer -> AIOps Output

This means the service telemetry is analyzed, abnormal events are identified, those events are emitted as messages, and downstream processing checks whether the issue is valid and actionable.

This README section documents the initial analysis of the simulated AIOps scenario. The normal records show stable service operation, while the later records show abnormal CPU, memory, and response metrics tied to timeout errors. These findings are the basis for the downstream event pipeline and final AIOps processing workflow.

