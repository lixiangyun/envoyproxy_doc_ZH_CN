## 故障注入

### Fault Injection
Fault Injection configuration overview.

- **filter.http.FaultAbort**<br />
[filter.http.FaultAbort proto]()

```
{
  "percent": "...",
  "http_status": "..."
}
```
- **percent**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An integer between 0-100 indicating the percentage of requests/operations/connections that will be aborted with the error code provided.

- **http_status**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) HTTP status code to use to abort the HTTP request.


Precisely one of http_status must be set.

- **filter.http.HTTPFault**<br />
[filter.http.HTTPFault proto]()

```
{
  "delay": "{...}",
  "abort": "{...}",
  "upstream_cluster": "...",
  "headers": [],
  "downstream_nodes": []
}
```
- **delay**<br />
	([filter.FaultDelay](#)) If specified, the filter will inject delays based on the values in the object. At least abort or delay must be specified.

- **abort**<br />
	([filter.http.FaultAbort](#)) If specified, the filter will abort requests based on the values in the object. At least abort or delay must be specified.

- **upstream_cluster**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the name of the (destination) upstream cluster that the filter should match on. Fault injection will be restricted to requests bound to the specific upstream cluster.

- **headers**<br />
	([HeaderMatcher](#)) Specifies a set of headers that the filter should match on. The fault injection filter can be applied selectively to requests that match a set of headers specified in the fault filter config. The chances of actual fault injection further depend on the value of the percent field. The filter will check the request’s headers against all the specified headers in the filter config. A match will happen if all the headers in the config are present in the request with the same values (or based on presence if the value field is not in the config).

- **downstream_nodes**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Faults are injected for the specified list of downstream hosts. If this setting is not set, faults are injected for all downstream nodes. Downstream node name is taken from the HTTP x-envoy-downstream-service-node header and compared against downstream_nodes list.



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

