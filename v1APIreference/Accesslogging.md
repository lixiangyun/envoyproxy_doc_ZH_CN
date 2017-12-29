### Access logging
### Configuration
```
{
  "access_log": [
    {
      "path": "...",
      "format": "...",
      "filter": "{...}",
    },
  ]
}
```
- **path**<br />
	([required](#), string) Path the access log is written to.

- **format**<br />
	([optional](#), string) Access log format. Envoy supports custom access log formats as well as a default format.

- **filter**<br />
	([optional](#), object) Filter which is used to determine if the access log needs to be written.

### Filters
### Envoy supports the following access log filters:

### Status code
### Duration
### Not health check
### Traceable
### Runtime
### And
### Or
### Status code
```
{
  "filter": {
    "type": "status_code",
    "op": "...",
    "value": "...",
    "runtime_key": "..."
  }
}
```
Filters on HTTP response/status code.

- **op**<br />
	([required](#), string) Comparison operator. Currently >= and = are the only supported operators.

- **value**<br />
	([required](#), integer) Default value to compare against if runtime value is not available.

- **runtime_key**<br />
	([optional](#), string) Runtime key to get value for comparision. This value is used if defined.

### Duration
```
{
  "filter": {
    "type": "duration",
    "op": "..",
    "value": "...",
    "runtime_key": "..."
  }
}
```
Filters on total request duration in milliseconds.

- **op**<br />
	([required](#), string) Comparison operator. Currently >= and = are the only supported operators.

- **value**<br />
	([required](#), integer) Default value to compare against if runtime values is not available.

- **runtime_key**<br />
	([optional](#), string) Runtime key to get value for comparision. This value is used if defined.

### Not health check
```
{
  "filter": {
    "type": "not_healthcheck"
  }
}
```
Filters for requests that are not health check requests. A health check request is marked by the health check filter.

### Traceable
```
{
  "filter": {
    "type": "traceable_request"
  }
}
```
Filters for requests that are traceable. See the tracing overview for more information on how a request becomes traceable.

### Runtime
```
{
  "filter": {
    "type": "runtime",
    "key" : "..."
  }
}
```
Filters for random sampling of requests. Sampling pivots on the header x-request-id being present. If x-request-id is present, the filter will consistently sample across multiple hosts based on the runtime key value and the value extracted from x-request-id. If it is missing, the filter will randomly sample based on the runtime key value.

- **key**<br />
	([required](#), string) Runtime key to get the percentage of requests to be sampled. This runtime control is specified in the range 0-100 and defaults to 0.

### And
```
{
  "filter": {
    "type": "logical_and",
    "filters": []
  }
}
```
Performs a logical “and” operation on the result of each filter in filters. Filters are evaluated sequentially and if one of them returns false, the filter returns false immediately.

### Or
```
{
  "filter": {
    "type": "logical_or",
    "filters": []
  }
}
```
Performs a logical “or” operation on the result of each individual filter. Filters are evaluated sequentially and if one of them returns true, the filter returns true immediately.


## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

