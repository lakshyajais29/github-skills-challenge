# GitHub Challenge

<img src="https://octodex.github.com/images/Professortocat_v2.png" align="right" height="200px" />

# AIOps Monitoring and Event Processing

## What This Project Does

This project is a simple AIOps example for monitoring a service called
`payment-service`. The service processes payments and records response time,
CPU usage, memory usage, and log messages.

The main goal is to find out when the service starts having problems. In this
dataset, the problems are slow responses, high resource usage, and timeout
errors. After finding an abnormal record, the program creates an event and
sends it through a small in-memory event pipeline.

## Data Used

The sample data is in `data/service_data.json`. Every record contains:

- `timestamp`
- `service`
- `response_time_ms`
- `cpu_percent`
- `memory_percent`
- `log_level`
- `message`

The records are about one minute apart, so it is possible to see how the
service changes over time.

## What I Found in the Data

### Normal records

At the beginning of the dataset, the service looks healthy:

- response time is around 120-150 ms
- CPU usage is around 42-50%
- memory usage is around 51-57%
- log level is `INFO`
- the messages show successful processing

These values do not show any major problem.

### Abnormal records

The main problems appear in these two records:

- `2026-09-20T10:05:00`
- `2026-09-20T10:06:00`

Their values are much higher than the normal records:

- response time: 610 ms and 640 ms
- CPU usage: 75% and 94%
- memory usage: 70% and 91%
- log level: `ERROR`
- messages: `Payment service timeout` and `Database connection timeout`

This suggests that the payment service was under heavy load and could not
process requests normally. The database timeout may also mean that the
backend was having connection or resource problems.

## How Anomaly Detection Works

The detector checks each record against limits for response time, CPU usage,
and memory usage. It also treats an `ERROR` log as an anomaly signal.

The two records above are detected because they have high metric values and
error messages. The detector stores the reasons for each anomaly so they can
be included in the event sent to the next part of the pipeline.

## Event Processing Flow

The project follows this flow:

```text
Operational Data -> Anomaly Detection -> Event Generation
-> Producer -> Topic -> Consumer -> AIOps Output
```

The topic used here is an in-memory Python class, so this is only a small
simulation of a streaming system. The producer publishes each anomaly event,
and the consumer reads the events from the same topic.

## Problems Found During Testing

While checking the project, I found and fixed these issues:

- the detector was checking for `WARNING`, but the data uses `ERROR`
- the producer and consumer were using different topic objects
- the `src` folder needed an `__init__.py` file so it could be imported as a
  Python package

After these fixes, the complete pipeline was able to detect and consume both
anomaly events.

## How to Run the Tests

From the project root, run:

```bash
PYTHONPATH=. python -m pytest -q
```

The current result is:

```text
8 passed in 0.06s
```

## Limitations

This project uses fixed thresholds, so it is mainly meant as a basic
demonstration. In a real monitoring system, the limits could change depending
on the normal traffic pattern. A future version could use time-window checks
or adaptive thresholds to make the detection more accurate.

