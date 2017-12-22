## HTTP路由管理&发现


### HTTP route management and RDS
### RouteConfiguration
[RouteConfiguration proto]()

### Routing architecture overview
### HTTP router filter
```
{
  "name": "...",
  "virtual_hosts": [],
  "internal_only_headers": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "request_headers_to_add": [],
  "validate_clusters": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The name of the route configuration. For example, it might match route_config_name in filter.network.Rds.

- **virtual_hosts**</br>
	([VirtualHost](#)) An array of virtual hosts that make up the route table.

- **internal_only_headers**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Optionally specifies a list of HTTP headers that the connection manager will consider to be internal only. If they are found on external requests they will be cleaned prior to filter invocation. See x-envoy-internal for more information.

- **response_headers_to_add**</br>
	([HeaderValueOption](#)) Specifies a list of HTTP headers that should be added to each response that the connection manager encodes. Headers specified at this level are applied after headers from any enclosed VirtualHost or RouteAction.

- **response_headers_to_remove**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies a list of HTTP headers that should be removed from each response that the connection manager encodes.

- **request_headers_to_add**</br>
	([HeaderValueOption](#)) Specifies a list of HTTP headers that should be added to each request routed by the HTTP connection manager. Headers specified at this level are applied after headers from any enclosed VirtualHost or RouteAction. For more information see the documentation on custom request headers.

- **validate_clusters**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) An optional boolean that specifies whether the clusters that the route table refers to will be validated by the cluster manager. If set to true and a route refers to a non-existent cluster, the route table will not load. If set to false and a route refers to a non-existent cluster, the route table will load and the router filter will return a 404 if the route is selected at runtime. This setting defaults to true if the route table is statically defined via the route_config option. This setting default to false if the route table is loaded dynamically via the rds option. Users may which to override the default behavior in certain cases (for example when using CDS with a static route table).

### VirtualHost
[VirtualHost proto]()

The top level element in the routing configuration is a virtual host. Each virtual host has a logical name as well as a set of domains that get routed to it based on the incoming request’s host header. This allows a single listener to service multiple top level domain path trees. Once a virtual host is selected based on the domain, the routes are processed in order to see which upstream cluster to route to or whether to perform a redirect.

```
{
  "name": "...",
  "domains": [],
  "routes": [],
  "require_tls": "...",
  "virtual_clusters": [],
  "rate_limits": [],
  "request_headers_to_add": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "cors": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The logical name of the virtual host. This is used when emitting certain statistics but is not relevant for routing.

- **domains**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) A list of domains (host/authority header) that will be matched to this virtual host. Wildcard hosts are supported in the form of “.foo.com” or “-bar.foo.com”.


### Note

The wildcard will not match the empty string. e.g. “-bar.foo.com” will match “baz-bar.foo.com” but not “-bar.foo.com”. Additionally, a special entry “” is allowed which will match any host/authority header. Only a single virtual host in the entire route configuration can match on “*”. A domain must be unique across all virtual hosts or the config will fail to load.

- **routes**</br>
	([Route](#)) The list of routes that will be matched, in order, for incoming requests. The first route that matches will be used.

- **require_tls**</br>
	([VirtualHost.TlsRequirementType](#)) Specifies the type of TLS enforcement the virtual host expects. If this option is not specified, there is no TLS requirement for the virtual host.

- **virtual_clusters**</br>
	([VirtualCluster](#)) A list of virtual clusters defined for this virtual host. Virtual clusters are used for additional statistics gathering.

- **rate_limits**</br>
	([RateLimit](#)) Specifies a set of rate limit configurations that will be applied to the virtual host.

- **request_headers_to_add**</br>
	([HeaderValueOption](#)) Specifies a list of HTTP headers that should be added to each request handled by this virtual host. Headers specified at this level are applied after headers from enclosed RouteAction and before headers from the enclosing RouteConfiguration. For more information see the documentation on custom request headers.

- **response_headers_to_add**</br>
	([HeaderValueOption](#)) Specifies a list of HTTP headers that should be added to each response handled by this virtual host. Headers specified at this level are applied after headers from enclosed RouteAction and before headers from the enclosing RouteConfiguration.

- **response_headers_to_remove**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies a list of HTTP headers that should be removed from each response handle by this virtual host.

- **cors**</br>
	([CorsPolicy](#)) Indicates that the virtual host has a CORS policy.

Enum VirtualHost.TlsRequirementType
[VirtualHost.TlsRequirementType proto]()

### NONE
	(DEFAULT) ?No TLS requirement for the virtual host.

### EXTERNAL_ONLY
?External requests must use TLS. If a request is external and it is not using TLS, a 301 redirect will be sent telling the client to use HTTPS.
### ALL
?All requests must use TLS. If a request is not using TLS, a 301 redirect will be sent telling the client to use HTTPS.
### Route
[Route proto]()

A route is both a specification of how to match a request as well as an indication of what to do next (e.g., redirect, forward, rewrite, etc.).

### Attention

Envoy supports routing on HTTP method via header matching.

```
{
  "match": "{...}",
  "route": "{...}",
  "redirect": "{...}",
  "metadata": "{...}",
  "decorator": "{...}"
}
```
- **match**</br>
	([RouteMatch](#), REQUIRED) Route matching parameters.

- **route**</br>
	([RouteAction](#)) Route request to some upstream cluster.


Precisely one of route, redirect must be set.

- **redirect**</br>
	([RedirectAction](#)) Return a redirect.


Precisely one of route, redirect must be set.

- **metadata**</br>
	([Metadata](#)) The Metadata field can be used to provide additional information about the route. It can be used for configuration, stats, and logging. The metadata should go under the filter namespace that will need it. For instance, if the metadata is intended for the Router filter, the filter name should be specified as envoy.router.

- **decorator**</br>
	([Decorator](#)) Decorator for the matched route.

### WeightedCluster
[WeightedCluster proto]()

Compared to the cluster field that specifies a single upstream cluster as the target of a request, the weighted_clusters option allows for specification of multiple upstream clusters along with weights that indicate the percentage of traffic to be forwarded to each cluster. The router selects an upstream cluster based on the weights.

```
{
  "clusters": [],
  "runtime_key_prefix": "..."
}
```
- **clusters**</br>
	([WeightedCluster.ClusterWeight](#), REQUIRED) Specifies one or more upstream clusters associated with the route.

- **runtime_key_prefix**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the runtime key prefix that should be used to construct the runtime keys associated with each cluster. When the runtime_key_prefix is specified, the router will look for weights associated with each upstream cluster under the key runtime_key_prefix + “.” + cluster[i].name where cluster[i] denotes an entry in the clusters array field. If the runtime key for the cluster does not exist, the value specified in the configuration file will be used as the default weight. See the runtime documentation for how key names map to the underlying implementation.

### WeightedCluster.ClusterWeight
[WeightedCluster.ClusterWeight proto]()

```
{
  "name": "...",
  "weight": "{...}",
  "metadata_match": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Name of the upstream cluster. The cluster must exist in the cluster manager configuration.

- **weight**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) An integer between 0-100. When a request matches the route, the choice of an upstream cluster is determined by its weight. The sum of weights across all entries in the clusters array must add up to 100.

- **metadata_match**</br>
	([Metadata](#)) Optional endpoint metadata match criteria. Only endpoints in the upstream cluster with metadata matching that set in metadata_match will be considered. The filter name should be specified as envoy.lb.

### RouteMatch
[RouteMatch proto]()

```
{
  "prefix": "...",
  "path": "...",
  "regex": "...",
  "case_sensitive": "{...}",
  "runtime": "{...}",
  "headers": []
}
```
- **prefix**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If specified, the route is a prefix rule meaning that the prefix must match the beginning of the :path header.


Precisely one of prefix, path, regex must be set.

- **path**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If specified, the route is an exact path rule meaning that the path must exactly match the :path header once the query string is removed.


Precisely one of prefix, path, regex must be set.

- **regex**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If specified, the route is a regular expression rule meaning that the regex must match the :path header once the query string is removed. The entire path (without the query string) must match the regex. The rule will not match if only a subsequence of the :path header matches the regex. The regex grammar is defined here.


### Examples:

### The regex /b[io]t matches the path /bit
### The regex /b[io]t matches the path /bot
### The regex /b[io]t does not match the path /bite
### The regex /b[io]t does not match the path /bit/bot
Precisely one of prefix, path, regex must be set.

- **case_sensitive**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Indicates that prefix/path matching should be case insensitive. The default is true.

- **runtime**</br>
	([RuntimeUInt32](#)) Indicates that the route should additionally match on a runtime key. An integer between 0-100. Every time the route is considered for a match, a random number between 0-99 is selected. If the number is <= the value found in the key (checked first) or, if the key is not present, the default value, the route is a match (assuming everything also about the route matches). A runtime route configuration can be used to roll out route changes in a gradual manner without full code/config deploys. Refer to the traffic shifting docs for additional documentation.

- **headers**</br>
	([HeaderMatcher](#)) Specifies a set of headers that the route should match on. The router will check the request’s headers against all the specified headers in the route config. A match will happen if all the headers in the route are present in the request with the same values (or based on presence if the value field is not in the config).

### CorsPolicy
[CorsPolicy proto]()

```
{
  "allow_origin": [],
  "allow_methods": "...",
  "allow_headers": "...",
  "expose_headers": "...",
  "max_age": "...",
  "allow_credentials": "{...}",
  "enabled": "{...}"
}
```
- **allow_origin**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the origins that will be allowed to do CORS requests.

- **allow_methods**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the content for the access-control-allow-methods header.

- **allow_headers**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the content for the access-control-allow-headers header.

- **expose_headers**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the content for the access-control-expose-headers header.

- **max_age**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the content for the access-control-max-age header.

- **allow_credentials**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Specifies whether the resource allows credentials.

- **enabled**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Specifies if CORS is enabled. Defaults to true. Only effective on route.

### RouteAction
[RouteAction proto]()

```
{
  "cluster": "...",
  "cluster_header": "...",
  "weighted_clusters": "{...}",
  "cluster_not_found_response_code": "...",
  "metadata_match": "{...}",
  "prefix_rewrite": "...",
  "host_rewrite": "...",
  "auto_host_rewrite": "{...}",
  "timeout": "{...}",
  "retry_policy": "{...}",
  "request_mirror_policy": "{...}",
  "priority": "...",
  "request_headers_to_add": [],
  "response_headers_to_add": [],
  "response_headers_to_remove": [],
  "rate_limits": [],
  "include_vh_rate_limits": "{...}",
  "hash_policy": [],
  "use_websocket": "{...}",
  "cors": "{...}"
}
```
- **cluster**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Indicates the upstream cluster to which the request should be routed to.


Precisely one of cluster, cluster_header, weighted_clusters must be set.

- **cluster_header**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Envoy will determine the cluster to route to by reading the value of the HTTP header named by cluster_header from the request headers. If the header is not found or the referenced cluster does not exist, Envoy will return a 404 response.


### Attention

Internally, Envoy always uses the HTTP/2 :authority header to represent the HTTP/1 Host header. Thus, if attempting to match on Host, match on :authority instead.

Precisely one of cluster, cluster_header, weighted_clusters must be set.

- **weighted_clusters**</br>
	([WeightedCluster](#)) Multiple upstream clusters can be specified for a given route. The request is routed to one of the upstream clusters based on weights assigned to each cluster. See the traffic splitting for additional documentation.


Precisely one of cluster, cluster_header, weighted_clusters must be set.

- **cluster_not_found_response_code**</br>
	([RouteAction.ClusterNotFoundResponseCode](#)) The HTTP status code to use when configured cluster is not found. The default response code is 503 Service Unavailable.

- **metadata_match**</br>
	([Metadata](#)) Optional endpoint metadata match criteria. Only endpoints in the upstream cluster with metadata matching that set in metadata_match will be considered. The filter name should be specified as envoy.lb.

- **prefix_rewrite**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Indicates that during forwarding, the matched prefix (or path) should be swapped with this value. This option allows application URLs to be rooted at a different path from those exposed at the reverse proxy layer.

- **host_rewrite**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Indicates that during forwarding, the host header will be swapped with this value.


Only one of host_rewrite, auto_host_rewrite may be set.

- **auto_host_rewrite**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Indicates that during forwarding, the host header will be swapped with the hostname of the upstream host chosen by the cluster manager. This option is applicable only when the destination cluster for a route is of type strict_dns or logical_dns. Setting this to true with other cluster types has no effect.


Only one of host_rewrite, auto_host_rewrite may be set.

- **timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Specifies the timeout for the route. If not specified, the default is 15s.


### Note

This timeout includes all retries. See also x-envoy-upstream-rq-timeout-ms, x-envoy-upstream-rq-per-try-timeout-ms, and the retry overview.

- **retry_policy**</br>
	([RouteAction.RetryPolicy](#)) Indicates that the route has a retry policy.

- **request_mirror_policy**</br>
	([RouteAction.RequestMirrorPolicy](#)) Indicates that the route has a request mirroring policy.

- **priority**</br>
	([RoutingPriority](#)) Optionally specifies the routing priority.

- **request_headers_to_add**</br>
	([HeaderValueOption](#)) Specifies a set of headers that will be added to requests matching this route. Headers specified at this level are applied before headers from the enclosing VirtualHost and RouteConfiguration. For more information see the documentation on custom request headers.

- **response_headers_to_add**</br>
	([HeaderValueOption](#)) Specifies a set of headers that will be added to responses to requests matching this route. Headers specified at this level are applied before headers from the enclosing VirtualHost and RouteConfiguration.

- **response_headers_to_remove**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies a list of HTTP headers that should be removed from each response to requests matching this route.

- **rate_limits**</br>
	([RateLimit](#)) Specifies a set of rate limit configurations that could be applied to the route.

- **include_vh_rate_limits**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Specifies if the rate limit filter should include the virtual host rate limits. By default, if the route configured rate limits, the virtual host rate_limits are not applied to the request.

- **hash_policy**</br>
	([RouteAction.HashPolicy](#)) Specifies a list of hash policies to use for ring hash load balancing. Each hash policy is evaluated individually and the combined result is used to route the request. The method of combination is deterministic such that identical lists of hash policies will produce the same hash. Since a hash policy examines specific parts of a request, it can fail to produce a hash (i.e. if the hashed header is not present). If (and only if) all configured hash policies fail to generate a hash, no hash will be produced for the route. In this case, the behavior is the same as if no hash policies were specified (i.e. the ring hash load balancer will choose a random backend).

- **use_websocket**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Indicates that a HTTP/1.1 client connection to this particular route should be allowed (and expected) to upgrade to a WebSocket connection. The default is false.


### Attention

If set to true, Envoy will expect the first request matching this route to contain WebSocket upgrade headers. If the headers are not present, the connection will be rejected. If set to true, Envoy will setup plain TCP proxying between the client and the upstream server. Hence, an upstream server that rejects the WebSocket upgrade request is also responsible for closing the associated connection. Until then, Envoy will continue to proxy data from the client to the upstream server.

Redirects, timeouts and retries are not supported on routes where websocket upgrades are allowed.

- **cors**</br>
	([CorsPolicy](#)) Indicates that the route has a CORS policy.

### RouteAction.RetryPolicy
[RouteAction.RetryPolicy proto]()

HTTP retry architecture overview.

```
{
  "retry_on": "...",
  "num_retries": "{...}",
  "per_try_timeout": "{...}"
}
```
- **retry_on**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the conditions under which retry takes place. These are the same conditions documented for x-envoy-retry-on and x-envoy-retry-grpc-on.

- **num_retries**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Specifies the allowed number of retries. This parameter is optional and defaults to 1. These are the same conditions documented for x-envoy-max-retries.

- **per_try_timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) Specifies a non-zero timeout per retry attempt. This parameter is optional. The same conditions documented for x-envoy-upstream-rq-per-try-timeout-ms apply.


### Note

If left unspecified, Envoy will use the global route timeout for the request. Consequently, when using a 5xx based retry policy, a request that times out will not be retried as the total timeout budget would have been exhausted.

### RouteAction.RequestMirrorPolicy
[RouteAction.RequestMirrorPolicy proto]()

The router is capable of shadowing traffic from one cluster to another. The current implementation is “fire and forget,” meaning Envoy will not wait for the shadow cluster to respond before returning the response from the primary cluster. All normal statistics are collected for the shadow cluster making this feature useful for testing.

During shadowing, the host/authority header is altered such that -shadow is appended. This is useful for logging. For example, cluster1 becomes cluster1-shadow.

```
{
  "cluster": "...",
  "runtime_key": "..."
}
```
- **cluster**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Specifies the cluster that requests will be mirrored to. The cluster must exist in the cluster manager configuration.

- **runtime_key**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If not specified, all requests to the target cluster will be mirrored. If specified, Envoy will lookup the runtime key to get the % of requests to mirror. Valid values are from 0 to 10000, allowing for increments of 0.01% of requests to be mirrored. If the runtime key is specified in the configuration but not present in runtime, 0 is the default and thus 0% of requests will be mirrored.

### RouteAction.HashPolicy
[RouteAction.HashPolicy proto]()

Specifies the route’s hashing policy if the upstream cluster uses a hashing load balancer.

```
{
  "header": "{...}",
  "cookie": "{...}",
  "connection_properties": "{...}"
}
```
- **header**</br>
	([RouteAction.HashPolicy.Header](#)) Header hash policy.


Precisely one of header, cookie, connection_properties must be set.

- **cookie**</br>
	([RouteAction.HashPolicy.Cookie](#)) Cookie hash policy.


Precisely one of header, cookie, connection_properties must be set.

- **connection_properties**</br>
	([RouteAction.HashPolicy.ConnectionProperties](#)) Connection properties hash policy.


Precisely one of header, cookie, connection_properties must be set.

### RouteAction.HashPolicy.Header
[RouteAction.HashPolicy.Header proto]()

```
{
  "header_name": "..."
}
```
- **header_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The name of the request header that will be used to obtain the hash key. If the request header is not present, no hash will be produced.

### RouteAction.HashPolicy.Cookie
[RouteAction.HashPolicy.Cookie proto]()

### Envoy supports two types of cookie affinity:

Passive. Envoy takes a cookie that’s present in the cookies header and hashes on its value.
Generated. Envoy generates and sets a cookie with an expiration (TTL) on the first request from the client in its response to the client, based on the endpoint the request gets sent to. The client then presents this on the next and all subsequent requests. The hash of this is sufficient to ensure these requests get sent to the same endpoint. The cookie is generated by hashing the source and destination ports and addresses so that multiple independent HTTP2 streams on the same connection will independently receive the same cookie, even if they arrive at the Envoy simultaneously.
```
{
  "name": "...",
  "ttl": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The name of the cookie that will be used to obtain the hash key. If the cookie is not present and ttl below is not set, no hash will be produced.

- **ttl**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) If specified, a cookie with the TTL will be generated if the cookie is not present.

### RouteAction.HashPolicy.ConnectionProperties
[RouteAction.HashPolicy.ConnectionProperties proto]()

```
{
  "source_ip": "..."
}
```
- **source_ip**</br>
	([bool](#)) Hash on source IP address.

Enum RouteAction.ClusterNotFoundResponseCode
[RouteAction.ClusterNotFoundResponseCode proto]()

### SERVICE_UNAVAILABLE
	(DEFAULT) ?HTTP status code - 503 Service Unavailable.

### NOT_FOUND
?HTTP status code - 404 Not Found.
### RedirectAction
[RedirectAction proto]()

```
{
  "host_redirect": "...",
  "path_redirect": "...",
  "response_code": "..."
}
```
- **host_redirect**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The host portion of the URL will be swapped with this value.

- **path_redirect**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The path portion of the URL will be swapped with this value.

- **response_code**</br>
	([RedirectAction.RedirectResponseCode](#)) The HTTP status code to use in the redirect response. The default response code is MOVED_PERMANENTLY (301).

Enum RedirectAction.RedirectResponseCode
[RedirectAction.RedirectResponseCode proto]()

### MOVED_PERMANENTLY
	(DEFAULT) ?Moved Permanently HTTP Status Code - 301.

### FOUND
?Found HTTP Status Code - 302.
### SEE_OTHER
?See Other HTTP Status Code - 303.
### TEMPORARY_REDIRECT
?Temporary Redirect HTTP Status Code - 307.
### PERMANENT_REDIRECT
?Permanent Redirect HTTP Status Code - 308.
### Decorator
[Decorator proto]()

```
{
  "operation": "..."
}
```
- **operation**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The operation name associated with the request matched to this route. If tracing is enabled, this information will be used as the span name reported for this request.


### Note

For ingress (inbound) requests, or egress (outbound) responses, this value may be overridden by the x-envoy-decorator-operation header.

### VirtualCluster
[VirtualCluster proto]()

A virtual cluster is a way of specifying a regex matching rule against certain important endpoints such that statistics are generated explicitly for the matched requests. The reason this is useful is that when doing prefix/path matching Envoy does not always know what the application considers to be an endpoint. Thus, it’s impossible for Envoy to generically emit per endpoint statistics. However, often systems have highly critical endpoints that they wish to get “perfect” statistics on. Virtual cluster statistics are perfect in the sense that they are emitted on the downstream side such that they include network level failures.

Documentation for virtual cluster statistics.

### Note

Virtual clusters are a useful tool, but we do not recommend setting up a virtual cluster for every application endpoint. This is both not easily maintainable and as well the matching and statistics output are not free.

```
{
  "pattern": "...",
  "name": "...",
  "method": "..."
}
```
- **pattern**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Specifies a regex pattern to use for matching requests. The entire path of the request must match the regex. The regex grammar used is defined here.


### Examples:

### The regex /rides/d+ matches the path /rides/0
### The regex /rides/d+ matches the path /rides/123
### The regex /rides/d+ does not match the path /rides/123/456
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Specifies the name of the virtual cluster. The virtual cluster name as well as the virtual host name are used when emitting statistics. The statistics are emitted by the router filter and are documented here.

- **method**</br>
	([RequestMethod](#)) Optionally specifies the HTTP method to match on. For example GET, PUT, etc.

### RateLimit
[RateLimit proto]()

Global rate limiting architecture overview.

```
{
  "stage": "{...}",
  "disable_key": "...",
  "actions": []
}
```
- **stage**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Refers to the stage set in the filter. The rate limit configuration only applies to filters with the same stage number. The default stage number is 0.


### Note

The filter supports a range of 0 - 10 inclusively for stage numbers.

- **disable_key**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The key to be set in runtime to disable this rate limit configuration.

- **actions**</br>
	([RateLimit.Action](#), REQUIRED) A list of actions that are to be applied for this rate limit configuration. Order matters as the actions are processed sequentially and the descriptor is composed by appending descriptor entries in that sequence. If an action cannot append a descriptor entry, no descriptor is generated for the configuration. See composing actions for additional documentation.

### RateLimit.Action
[RateLimit.Action proto]()

```
{
  "source_cluster": "{...}",
  "destination_cluster": "{...}",
  "request_headers": "{...}",
  "remote_address": "{...}",
  "generic_key": "{...}",
  "header_value_match": "{...}"
}
```
- **source_cluster**</br>
	([RateLimit.Action.SourceCluster](#)) Rate limit on source cluster.


Precisely one of source_cluster, destination_cluster, request_headers, remote_address, generic_key, header_value_match must be set.

- **destination_cluster**</br>
	([RateLimit.Action.DestinationCluster](#)) Rate limit on destination cluster.


Precisely one of source_cluster, destination_cluster, request_headers, remote_address, generic_key, header_value_match must be set.

- **request_headers**</br>
	([RateLimit.Action.RequestHeaders](#)) Rate limit on request headers.


Precisely one of source_cluster, destination_cluster, request_headers, remote_address, generic_key, header_value_match must be set.

- **remote_address**</br>
	([RateLimit.Action.RemoteAddress](#)) Rate limit on remote address.


Precisely one of source_cluster, destination_cluster, request_headers, remote_address, generic_key, header_value_match must be set.

- **generic_key**</br>
	([RateLimit.Action.GenericKey](#)) Rate limit on a generic key.


Precisely one of source_cluster, destination_cluster, request_headers, remote_address, generic_key, header_value_match must be set.

- **header_value_match**</br>
	([RateLimit.Action.HeaderValueMatch](#)) Rate limit on the existence of request headers.


Precisely one of source_cluster, destination_cluster, request_headers, remote_address, generic_key, header_value_match must be set.

### RateLimit.Action.SourceCluster
[RateLimit.Action.SourceCluster proto]()

### The following descriptor entry is appended to the descriptor:

	(["source_cluster"](#), "<local service cluster>")

<local service cluster> is derived from the --service-cluster option.

```
{}
RateLimit.Action.DestinationCluster
[RateLimit.Action.DestinationCluster proto]

The following descriptor entry is appended to the descriptor:

("destination_cluster", "<routed target cluster>")
Once a request matches against a route table rule, a routed cluster is determined by one of the following route table configuration settings:

cluster indicates the upstream cluster to route to.
weighted_clusters chooses a cluster randomly from a set of clusters with attributed weight.
cluster_header indicates which header in the request contains the target cluster.
{}
RateLimit.Action.RequestHeaders
[RateLimit.Action.RequestHeaders proto]

The following descriptor entry is appended when a header contains a key that matches the header_name:

("<descriptor_key>", "<header_value_queried_from_header>")
{
  "header_name": "...",
  "descriptor_key": "..."
}
```
- **header_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The header name to be queried from the request headers. The header’s value is used to populate the value of the descriptor entry for the descriptor_key.

- **descriptor_key**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The key to use in the descriptor entry.

### RateLimit.Action.RemoteAddress
[RateLimit.Action.RemoteAddress proto]()

### The following descriptor entry is appended to the descriptor and is populated using the trusted address from x-forwarded-for:

	(["remote_address"](#), "<trusted address from x-forwarded-for>")

```
{}
RateLimit.Action.GenericKey
[RateLimit.Action.GenericKey proto]

The following descriptor entry is appended to the descriptor:

("generic_key", "<descriptor_value>")
{
  "descriptor_value": "..."
}
```
- **descriptor_value**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The value to use in the descriptor entry.

### RateLimit.Action.HeaderValueMatch
[RateLimit.Action.HeaderValueMatch proto]()

### The following descriptor entry is appended to the descriptor:

	(["header_match"](#), "<descriptor_value>")

```
{
  "descriptor_value": "...",
  "expect_match": "{...}",
  "headers": []
}
```
- **descriptor_value**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The value to use in the descriptor entry.

- **expect_match**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) If set to true, the action will append a descriptor entry when the request matches the headers. If set to false, the action will append a descriptor entry when the request does not match the headers. The default value is true.

- **headers**</br>
	([HeaderMatcher](#), REQUIRED) Specifies a set of headers that the rate limit action should match on. The action will check the request’s headers against all the specified headers in the config. A match will happen if all the headers in the config are present in the request with the same values (or based on presence if the value field is not in the config).

### HeaderMatcher
[HeaderMatcher proto]()

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
```
{
  "name": "...",
  "value": "...",
  "regex": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Specifies the name of the header in the request.

- **value**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Specifies the value of the header. If the value is absent a request that has the name header will match, regardless of the header’s value.

- **regex**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Specifies whether the header value is a regular expression or not. Defaults to false. The entire request header value must match the regex. The rule will not match if only a subsequence of the request header value matches the regex. The regex grammar used in the value field is defined here.


### Examples:

### The regex d{3} matches the value 123
### The regex d{3} does not match the value 1234
The regex d{3} does not match the value 123.456


## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)