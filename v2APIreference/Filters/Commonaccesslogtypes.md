## 常见访问日志类型

### Common access log types
Envoy access logs describe incoming interaction with Envoy over a fixed period of time, and typically cover a single request/response exchange, (e.g. HTTP), stream (e.g. over HTTP/gRPC), or proxied connection (e.g. TCP). Access logs contain fields defined in protocol-specific protobuf messages.

Except where explicitly declared otherwise, all fields describe downstream interaction between Envoy and a connected client. Fields describing upstream interaction will explicitly include upstream in their name.

### filter.accesslog.AccessLog
[filter.accesslog.AccessLog proto]()

```
{
  "name": "...",
  "filter": "{...}",
  "config": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The name of the access log implementation to instantiate. The name must match a statically registered access log. Current built-in loggers include: 1) “envoy.file_access_log”

- **filter**</br>
	([filter.accesslog.AccessLogFilter](#)) Filter which is used to determine if the access log needs to be written.

- **config**</br>
	([Struct](#)) Custom configuration that depends on the access log being instantiated. built-in configurations include: 1) “envoy.file_access_log”: FileAccessLog

### filter.accesslog.AccessLogFilter
[filter.accesslog.AccessLogFilter proto]()

```
{
  "status_code_filter": "{...}",
  "duration_filter": "{...}",
  "not_health_check_filter": "{...}",
  "traceable_filter": "{...}",
  "runtime_filter": "{...}",
  "and_filter": "{...}",
  "or_filter": "{...}"
}
```
- **status_code_filter**</br>
	([filter.accesslog.StatusCodeFilter](#)) Status code filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

- **duration_filter**</br>
	([filter.accesslog.DurationFilter](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Duration filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

- **not_health_check_filter**</br>
	([filter.accesslog.NotHealthCheckFilter](#)) Not health check filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

- **traceable_filter**</br>
	([filter.accesslog.TraceableFilter](#)) Traceable filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

- **runtime_filter**</br>
	([filter.accesslog.RuntimeFilter](#)) Runtime filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

- **and_filter**</br>
	([filter.accesslog.AndFilter](#)) And filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

- **or_filter**</br>
	([filter.accesslog.OrFilter](#)) Or filter.


Precisely one of status_code_filter, duration_filter, not_health_check_filter, traceable_filter, runtime_filter, and_filter, or_filter must be set.

### filter.accesslog.ComparisonFilter
[filter.accesslog.ComparisonFilter proto]()

Filter on an integer comparison.

```
{
  "op": "...",
  "value": "{...}"
}
```
- **op**</br>
	([filter.accesslog.ComparisonFilter.Op](#)) Comparison operator.

- **value**</br>
	([RuntimeUInt32](#)) Value to compare against.

Enum filter.accesslog.ComparisonFilter.Op
[filter.accesslog.ComparisonFilter.Op proto]()

### EQ
	(DEFAULT) ?=

### GE
### ?>=
### filter.accesslog.StatusCodeFilter
[filter.accesslog.StatusCodeFilter proto]()

Filters on HTTP response/status code.

```
{
  "comparison": "{...}"
}
```
- **comparison**</br>
	([filter.accesslog.ComparisonFilter](#), REQUIRED) Comparison.

### filter.accesslog.DurationFilter
[filter.accesslog.DurationFilter proto]()

Filters on total request duration in milliseconds.

```
{
  "comparison": "{...}"
}
```
- **comparison**</br>
	([filter.accesslog.ComparisonFilter](#), REQUIRED) Comparison.

### filter.accesslog.NotHealthCheckFilter
[filter.accesslog.NotHealthCheckFilter proto]()

Filters for requests that are not health check requests. A health check request is marked by the health check filter.

```
{}
```
### filter.accesslog.TraceableFilter
[filter.accesslog.TraceableFilter proto]()

Filters for requests that are traceable. See the tracing overview for more information on how a request becomes traceable.

```
{}
```
### filter.accesslog.RuntimeFilter
[filter.accesslog.RuntimeFilter proto]()

Filters for random sampling of requests. Sampling pivots on the header x-request-id being present. If x-request-id is present, the filter will consistently sample across multiple hosts based on the runtime key value and the value extracted from x-request-id. If it is missing, the filter will randomly sample based on the runtime key value.

```
{
  "runtime_key": "..."
}
```
- **runtime_key**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Runtime key to get the percentage of requests to be sampled. This runtime control is specified in the range 0-100 and defaults to 0.

### filter.accesslog.AndFilter
[filter.accesslog.AndFilter proto]()

Performs a logical “and” operation on the result of each filter in filters. Filters are evaluated sequentially and if one of them returns false, the filter returns false immediately.

```
{
  "filters": []
}
```
- **filters**</br>
	([filter.accesslog.AccessLogFilter](#), REQUIRED)

### filter.accesslog.OrFilter
[filter.accesslog.OrFilter proto]()

Performs a logical “or” operation on the result of each individual filter. Filters are evaluated sequentially and if one of them returns true, the filter returns true immediately.

```
{
  "filters": []
}
```
- **filters**</br>
	([filter.accesslog.AccessLogFilter](#), REQUIRED)

### filter.accesslog.FileAccessLog
[filter.accesslog.FileAccessLog proto]()

Custom configuration for an AccessLog that writes log entries directly to a file. Configures the built-in envoy.file_access_log AccessLog.

```
{
  "path": "...",
  "format": "..."
}
```
- **path**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) A path to a local file to which to write the access log entries.

- **format**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Access log format. Envoy supports custom access log formats as well as a default format.



## 返回
- [上一级](../Filters.md)
- [首页目录](../../README.md)