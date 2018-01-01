### Fault Injection
Fault Injection configuration overview.

### Configuration
```
{
  "name" : "fault",
  "config" : {
    "abort" : "{...}",
    "delay" : "{...}",
    "upstream_cluster" : "...",
    "headers" : [],
    "downstream_nodes": []
  }
}
```
- **abort**<br />
	(sometimes required, object) If specified, the filter will abort requests based on the values in the object. At least abort or delay must be specified.

- **delay**<br />
	(sometimes required, object) If specified, the filter will inject delays based on the values in the object. At least abort or delay must be specified.

- **upstream_cluster:**<br />
	(optional, string) Specifies the name of the (destination) upstream cluster that the filter should match on. Fault injection will be restricted to requests bound to the specific upstream cluster.

- **headers**<br />
	(optional, array) Specifies a set of headers that the filter should match on. The fault injection filter can be applied selectively to requests that match a set of headers specified in the fault filter config. The chances of actual fault injection further depend on the values of abort_percent and fixed_delay_percent parameters. The filter will check the request’s headers against all the specified headers in the filter config. A match will happen if all the headers in the config are present in the request with the same values (or based on presence if the value field is not in the config).

- **downstream_nodes:**<br />
	(optional, array) Faults are injected for the specified list of downstream hosts. If this setting is not set, faults are injected for all downstream nodes. Downstream node name is taken from the HTTP x-envoy-downstream-service-node header and compared against downstream_nodes list.

### Abort
```
{
  "abort_percent" : "...",
  "http_status" : "..."
}
```
- **abort_percent**<br />
	(required, integer) The percentage of requests that should be aborted with the specified http_status code. Valid values range from 0 to 100.

- **http_status**<br />
	(required, integer) The HTTP status code that will be used as the response code for the request being aborted.

### Delay
```
{
  "type" : "...",
  "fixed_delay_percent" : "...",
  "fixed_duration_ms" : "..."
}
```
- **type:**<br />
	(required, string) Specifies the type of delay being injected. Currently only fixed delay type (step function) is supported.

- **fixed_delay_percent:**<br />
	(required, integer) The percentage of requests that will be delayed for the duration specified by fixed_duration_ms. Valid values range from 0 to 100.

- **fixed_duration_ms:**<br />
	(required, integer) The delay duration in milliseconds. Must be greater than 0.



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)