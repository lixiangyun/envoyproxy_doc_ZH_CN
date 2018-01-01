### Route
A route is both a specification of how to match a request as well as in indication of what to do next (e.g., redirect, forward, rewrite, etc.).

### Attention

Envoy supports routing on HTTP method via header matching.

```
{
  "prefix": "...",
  "path": "...",
  "regex": "...",
  "cluster": "...",
  "cluster_header": "...",
  "weighted_clusters" : "{...}",
  "host_redirect": "...",
  "path_redirect": "...",
  "prefix_rewrite": "...",
  "host_rewrite": "...",
  "auto_host_rewrite": "...",
  "case_sensitive": "...",
  "use_websocket": "...",
  "timeout_ms": "...",
  "runtime": "{...}",
  "retry_policy": "{...}",
  "shadow": "{...}",
  "priority": "...",
  "headers": [],
  "rate_limits": [],
  "include_vh_rate_limits" : "...",
  "hash_policy": "{...}",
  "request_headers_to_add" : [],
  "opaque_config": [],
  "cors": "{...}",
  "decorator" : "{...}"
}
```
- **prefix**<br />
	(sometimes required, string) If specified, the route is a prefix rule meaning that the prefix must match the beginning of the :path header. One of prefix, path, or regex must be specified.

- **path**<br />
	(sometimes required, string) If specified, the route is an exact path rule meaning that the path must exactly match the :path header once the query string is removed. One of prefix, path, or regex must be specified.

- **regex**<br />
	(sometimes required, string) If specified, the route is a regular expression rule meaning that the regex must match the :path header once the query string is removed. The entire path (without the query string) must match the regex. The rule will not match if only a subsequence of the :path header matches the regex. The regex grammar is defined here. One of prefix, path, or regex must be specified.


### Examples:

### The regex /b[io]t matches the path /bit
### The regex /b[io]t matches the path /bot
### The regex /b[io]t does not match the path /bite
### The regex /b[io]t does not match the path /bit/bot
- **cors**<br />
	(optional, object) Specifies the route’s CORS policy.

- **cluster**<br />
	(sometimes required, string) If the route is not a redirect (host_redirect and/or path_redirect is not specified), one of cluster, cluster_header, or weighted_clusters must be specified. When cluster is specified, its value indicates the upstream cluster to which the request should be forwarded to.

- **cluster_header**<br />
	(sometimes required, string) If the route is not a redirect (host_redirect and/or path_redirect is not specified), one of cluster, cluster_header, or weighted_clusters must be specified. When cluster_header is specified, Envoy will determine the cluster to route to by reading the value of the HTTP header named by cluster_header from the request headers. If the header is not found or the referenced cluster does not exist, Envoy will return a 404 response.


### Attention

Internally, Envoy always uses the HTTP/2 :authority header to represent the HTTP/1 Host header. Thus, if attempting to match on Host, match on :authority instead.

- **weighted_clusters**<br />
	(sometimes required, object) If the route is not a redirect (host_redirect and/or path_redirect is not specified), one of cluster, cluster_header, or weighted_clusters must be specified. With the weighted_clusters option, multiple upstream clusters can be specified for a given route. The request is forwarded to one of the upstream clusters based on weights assigned to each cluster. See traffic splitting for additional documentation.

- **host_redirect**<br />
	(sometimes required, string) Indicates that the route is a redirect rule. If there is a match, a 301 redirect response will be sent which swaps the host portion of the URL with this value. path_redirect can also be specified along with this option.

- **path_redirect**<br />
	(sometimes required, string) Indicates that the route is a redirect rule. If there is a match, a 301 redirect response will be sent which swaps the path portion of the URL with this value. host_redirect can also be specified along with this option. The router filter will place the original path before rewrite into the x-envoy-original-path header.

- **prefix_rewrite**<br />
	(optional, string) Indicates that during forwarding, the matched prefix (or path) should be swapped with this value. When using regex path matching, the entire path (not including the query string) will be swapped with this value. This option allows application URLs to be rooted at a different path from those exposed at the reverse proxy layer.

- **host_rewrite**<br />
	(optional, string) Indicates that during forwarding, the host header will be swapped with this value.

- **auto_host_rewrite**<br />
	(optional, boolean) Indicates that during forwarding, the host header will be swapped with the hostname of the upstream host chosen by the cluster manager. This option is applicable only when the destination cluster for a route is of type strict_dns or logical_dns. Setting this to true with other cluster types has no effect. auto_host_rewrite and host_rewrite are mutually exclusive options. Only one can be specified.

- **case_sensitive**<br />
	(optional, boolean) Indicates that prefix/path matching should be case sensitive. The default is true.

- **use_websocket**<br />
	(optional, boolean) Indicates that a HTTP/1.1 client connection to this particular route should be allowed to upgrade to a WebSocket connection. The default is false.


### Attention

If set to true, Envoy will expect the first request matching this route to contain WebSocket upgrade headers. If the headers are not present, the connection will be processed as a normal HTTP/1.1 connection. If the upgrade headers are present, Envoy will setup plain TCP proxying between the client and the upstream server. Hence, an upstream server that rejects the WebSocket upgrade request is also responsible for closing the associated connection. Until then, Envoy will continue to proxy data from the client to the upstream server.

Redirects, timeouts and retries are not supported on requests with WebSocket upgrade headers.

- **timeout_ms**<br />
	(optional, integer) Specifies the timeout for the route. If not specified, the default is 15s. Note that this timeout includes all retries. See also x-envoy-upstream-rq-timeout-ms, x-envoy-upstream-rq-per-try-timeout-ms, and the retry overview.

- **runtime**<br />
	(optional, object) Indicates that the route should additionally match on a runtime key.

- **retry_policy**<br />
	(optional, object) Indicates that the route has a retry policy.

- **shadow**<br />
	(optional, object) Indicates that the route has a shadow policy.

- **priority**<br />
	(optional, string) Optionally specifies the routing priority.

- **headers**<br />
	(optional, array) Specifies a set of headers that the route should match on. The router will check the request’s headers against all the specified headers in the route config. A match will happen if all the headers in the route are present in the request with the same values (or based on presence if the value field is not in the config).

- **request_headers_to_add**<br />
	(optional, array) Specifies a list of HTTP headers that should be added to each request handled by this virtual host. Headers are specified in the following form:


```
[
  {"key": "header1", "value": "value1"},
  {"key": "header2", "value": "value2"}
]
```
For more information see the documentation on custom request headers.

- **opaque_config**<br />
	(optional, array) Specifies a set of optional route configuration values that can be accessed by filters.

- **rate_limits**<br />
	(optional, array) Specifies a set of rate limit configurations that could be applied to the route.

- **include_vh_rate_limits**<br />
	(optional, boolean) Specifies if the rate limit filter should include the virtual host rate limits. By default, if the route configured rate limits, the virtual host rate_limits are not applied to the request.

- **hash_policy**<br />
	(optional, object) Specifies the route’s hashing policy if the upstream cluster uses a hashing load balancer.

- **decorator**<br />
	(optional, object) Specifies the route’s decorator used to enhance information reported about the matched request.

### Runtime
A runtime route configuration can be used to roll out route changes in a gradual manner without full code/config deploys. Refer to the traffic shifting docs for additional documentation.

```
{
  "key": "...",
  "default": "..."
}
```
- **key**<br />
	(required, string) Specifies the runtime key name that should be consulted to determine whether the route matches or not. See the runtime documentation for how key names map to the underlying implementation.

- **default**<br />
	([required](#), integer) An integer between 0-100. Every time the route is considered for a match, a random number between 0-99 is selected. If the number is <= the value found in the key (checked first) or, if the key is not present, the default value, the route is a match (assuming everything also about the route matches).

### Retry policy
HTTP retry architecture overview.

```
{
  "retry_on": "...",
  "num_retries": "...",
  "per_try_timeout_ms" : "..."
}
```
- **retry_on**<br />
	(required, string) Specifies the conditions under which retry takes place. These are the same conditions documented for x-envoy-retry-on and x-envoy-retry-grpc-on.

- **num_retries**<br />
	(optional, integer) Specifies the allowed number of retries. This parameter is optional and defaults to 1. These are the same conditions documented for x-envoy-max-retries.

- **per_try_timeout_ms**<br />
	(optional, integer) Specifies a non-zero timeout per retry attempt. This parameter is optional. The same conditions documented for x-envoy-upstream-rq-per-try-timeout-ms apply.


Note: If left unspecified, Envoy will use the global route timeout for the request. Consequently, when using a 5xx based retry policy, a request that times out will not be retried as the total timeout budget would have been exhausted.

### Shadow
The router is capable of shadowing traffic from one cluster to another. The current implementation is “fire and forget,” meaning Envoy will not wait for the shadow cluster to respond before returning the response from the primary cluster. All normal statistics are collected for the shadow cluster making this feature useful for testing.

During shadowing, the host/authority header is altered such that -shadow is appended. This is useful for logging. For example, cluster1 becomes cluster1-shadow.

```
{
  "cluster": "...",
  "runtime_key": "..."
}
```
- **cluster**<br />
	(required, string) Specifies the cluster that requests will be shadowed to. The cluster must exist in the cluster manager configuration.

- **runtime_key**<br />
	(optional, string) If not specified, all requests to the target cluster will be shadowed. If specified, Envoy will lookup the runtime key to get the % of requests to shadow. Valid values are from 0 to 10000, allowing for increments of 0.01% of requests to be shadowed. If the runtime key is specified in the configuration but not present in runtime, 0 is the default and thus 0% of requests will be shadowed.

### Headers
```
{
  "name": "...",
  "value": "...",
  "regex": "..."
}
```
- **name**<br />
	(required, string) Specifies the name of the header in the request.

- **value**<br />
	(optional, string) Specifies the value of the header. If the value is absent a request that has the name header will match, regardless of the header’s value.

- **regex**<br />
	(optional, boolean) Specifies whether the header value is a regular expression or not. Defaults to false. The entire request header value must match the regex. The rule will not match if only a subsequence of the request header value matches the regex. The regex grammar used in the value field is defined here.


### Examples:

### The regex d{3} matches the value 123
### The regex d{3} does not match the value 1234
The regex d{3} does not match the value 123.456
### Attention

Internally, Envoy always uses the HTTP/2 :authority header to represent the HTTP/1 Host header. Thus, if attempting to match on Host, match on :authority instead.

### Attention

To route on HTTP method, use the special HTTP/2 :method header. This works for both HTTP/1 and HTTP/2 as Envoy normalizes headers. E.g.,

```
{
  "name": ":method",
  "value": "POST"
}
```
### Weighted Clusters
Compared to the cluster field that specifies a single upstream cluster as the target of a request, the weighted_clusters option allows for specification of multiple upstream clusters along with weights that indicate the percentage of traffic to be forwarded to each cluster. The router selects an upstream cluster based on the weights.

```
{
  "clusters": [],
  "runtime_key_prefix" : "..."
}
```
- **clusters**<br />
	(required, array) Specifies one or more upstream clusters associated with the route.


```
{
  "name" : "...",
  "weight": "..."
}
```
- **name**<br />
	(required, string) Name of the upstream cluster. The cluster must exist in the cluster manager configuration.

- **weight**<br />
	(required, integer) An integer between 0-100. When a request matches the route, the choice of an upstream cluster is determined by its weight. The sum of weights across all entries in the clusters array must add up to 100.

- **runtime_key_prefix**<br />
	(optional, string) Specifies the runtime key prefix that should be used to construct the runtime keys associated with each cluster. When the runtime_key_prefix is specified, the router will look for weights associated with each upstream cluster under the key runtime_key_prefix + "." + cluster[i].name where cluster[i] denotes an entry in the clusters array field. If the runtime key for the cluster does not exist, the value specified in the configuration file will be used as the default weight. See the runtime documentation for how key names map to the underlying implementation.


Note: If the sum of runtime weights exceed 100, the traffic splitting behavior is undefined (although the request will be routed to one of the clusters).

### Hash policy
Specifies the route’s hashing policy if the upstream cluster uses a hashing load balancer.

```
{
  "header_name": "..."
}
```
- **header_name**<br />
	(required, string) The name of the request header that will be used to obtain the hash key. If the request header is not present, the load balancer will use a random number as the hash, effectively making the load balancing policy random.

### Decorator
Specifies the route’s decorator.

```
{
  "operation": "..."
}
```
- **operation**<br />
	(required, string) The operation name associated with the request matched to this route. If tracing is enabled, this information will be used as the span name reported for this request. NOTE: For ingress (inbound) requests, or egress (outbound) responses, this value may be overridden by the x-envoy-decorator-operation header.

### Opaque Config
Additional configuration can be provided to filters through the “Opaque Config” mechanism. A list of properties are specified in the route config. The configuration is uninterpreted by envoy and can be accessed within a user-defined filter. The configuration is a generic string map. Nested objects are not supported.

```
[
  {"...": "..."}
]
```
### Cors
Settings on a route take precedence over settings on the virtual host.

```
{
  "enabled": false,
  "allow_origin": ["http://foo.example"],
  "allow_methods": "POST, GET, OPTIONS",
  "allow_headers": "Content-Type",
  "allow_credentials": false,
  "expose_headers": "X-Custom-Header",
  "max_age": "86400"
}
```
- **enabled**<br />
	(optional, boolean) Defaults to true. Setting enabled to false on a route disables CORS for this route only. The setting has no effect on a virtual host.

- **allow_origin**<br />
	(optional, array) The origins that will be allowed to do CORS request. Wildcard “*” will allow any origin.

- **allow_methods**<br />
	(optional, string) The content for the access-control-allow-methods header. Comma separated list of HTTP methods.

- **allow_headers**<br />
	(optional, string) The content for the access-control-allow-headers header. Comma separated list of HTTP headers.

- **allow_credentials**<br />
	(optional, boolean) Whether the resource allows credentials.

- **expose_headers**<br />
	(optional, string) The content for the access-control-expose-headers header. Comma separated list of HTTP headers.

- **max_age**<br />
	(optional, string) The content for the access-control-max-age header. Value in seconds for how long the response to the preflight request can be cached.

