## 集群

- [健康检查](Cluster/Healthchecking.md)
- [熔断](Cluster/Circuitbreakers.md)
- [TLS上下文](Cluster/TLScontext.md)
- [异常值检测](Cluster/Outlierdetection.md)
- [HASH环负载均衡配置](Cluster/Ringhashloadbalancerconfiguration.md)

### Cluster
```
{
  "name": "...",
  "type": "...",
  "connect_timeout_ms": "...",
  "per_connection_buffer_limit_bytes": "...",
  "lb_type": "...",
  "ring_hash_lb_config": "{...}",
  "hosts": [],
  "service_name": "...",
  "health_check": "{...}",
  "max_requests_per_connection": "...",
  "circuit_breakers": "{...}",
  "ssl_context": "{...}",
  "features": "...",
  "http2_settings": "{...}",
  "cleanup_interval_ms": "...",
  "dns_refresh_rate_ms": "...",
  "dns_lookup_family": "...",
  "dns_resolvers": [],
  "outlier_detection": "{...}"
}
```
- **name**<br />
	([required](#), string) Supplies the name of the cluster which must be unique across all clusters. The cluster name is used when emitting statistics. By default, the maximum length of a cluster name is limited to 60 characters. This limit can be increased by setting the --max-obj-name-len command line argument to the desired value.

- **type**<br />
	([required](#), string) The service discovery type to use for resolving the cluster. Possible options are static, strict_dns, logical_dns, *original_dst*, and sds.

- **connect_timeout_ms**<br />
	([required](#), integer) The timeout for new network connections to hosts in the cluster specified in milliseconds.

- **per_connection_buffer_limit_bytes**<br />
	([optional](#), integer) Soft limit on size of the cluster’s connections read and write buffers. If unspecified, an implementation defined default is applied (1MiB).

- **lb_type**<br />
	([required](#), string) The load balancer type to use when picking a host in the cluster. Possible options are round_robin, least_request, ring_hash, random, and original_dst_lb. Note that *original_dst_lb* must be used with clusters of type *original_dst*, and may not be used with any other cluster type.

- **ring_hash_lb_config**<br />
	([optional](#), object) Optional configuration for the ring hash load balancer, used when lb_type is set to ring_hash.

- **hosts**<br />
	([sometimes required](#), array) If the service discovery type is static, strict_dns, or logical_dns the hosts array is required. Hosts array is not allowed with cluster type original_dst. How it is specified depends on the type of service discovery:


- **static**<br />
Static clusters must use fully resolved hosts that require no DNS lookups. Both TCP and unix domain sockets (UDS) addresses are supported. A TCP address looks like:

- **tcp://<ip>:<port>**<br />

### A UDS address looks like:

- **unix://<file name>**<br />

### A list of addresses can be specified as in the following example:

```
[{"url": "tcp://10.0.0.2:1234"}, {"url": "tcp://10.0.0.3:5678"}]
```
- **strict_dns**<br />
Strict DNS clusters can specify any number of hostname:port combinations. All names will be resolved using DNS and grouped together to form the final cluster. If multiple records are returned for a single name, all will be used. For example:

```
[{"url": "tcp://foo1.bar.com:1234"}, {"url": "tcp://foo2.bar.com:5678"}]
```
- **logical_dns**<br />
Logical DNS clusters specify hostnames much like strict DNS, however only the first host will be used. For example:

```
[{"url": "tcp://foo1.bar.com:1234"}]
```
- **service_name**<br />
	([sometimes required](#), string) This parameter is required if the service discovery type is sds. It will be passed to the SDS API when fetching cluster members.

- **health_check**<br />
	([optional](#), object) Optional active health checking configuration for the cluster. If no configuration is specified no health checking will be done and all cluster members will be considered healthy at all times.

- **max_requests_per_connection**<br />
	([optional](#), integer) Optional maximum requests for a single upstream connection. This parameter is respected by both the HTTP/1.1 and HTTP/2 connection pool implementations. If not specified, there is no limit. Setting this parameter to 1 will effectively disable keep alive.

- **circuit_breakers**<br />
	([optional](#), object) Optional circuit breaking settings for the cluster.

- **ssl_context**<br />
	([optional](#), object) The TLS configuration for connections to the upstream cluster. If no TLS configuration is specified, TLS will not be used for new connections.

- **features**<br />
	([optional](#), string) A comma delimited list of features that the upstream cluster supports. The currently supported features are:


- **http2**<br />
If http2 is specified, Envoy will assume that the upstream supports HTTP/2 when making new HTTP connection pool connections. Currently, Envoy only supports prior knowledge for upstream connections. Even if TLS is used with ALPN, http2 must be specified. As an aside this allows HTTP/2 connections to happen over plain text.
- **http2_settings**<br />
	([optional](#), object) Additional HTTP/2 settings that are passed directly to the HTTP/2 codec when initiating HTTP connection pool connections. These are the same options supported in the HTTP connection manager http2_settings option.

- **cleanup_interval_ms**<br />
	([optional](#), integer) The interval for removing stale hosts from an original_dst cluster. Hosts are considered stale if they have not been used as upstream destinations during this interval. New hosts are added to original destination clusters on demand as new connections are redirected to Envoy, causing the number of hosts in the cluster to grow over time. Hosts that are not stale (they are actively used as destinations) are kept in the cluster, which allows connections to them remain open, saving the latency that would otherwise be spent on opening new connections. If this setting is not specified, the value defaults to 5000. For cluster types other than original_dst this setting is ignored.

- **dns_refresh_rate_ms**<br />
	([optional](#), integer) If the dns refresh rate is specified and the cluster type is either strict_dns, or logical_dns, this value is used as the cluster’s dns refresh rate. If this setting is not specified, the value defaults to 5000. For cluster types other than strict_dns and logical_dns this setting is ignored.

- **dns_lookup_family**<br />
	([optional](#), string) The DNS IP address resolution policy. The options are v4_only, v6_only, and auto. If this setting is not specified, the value defaults to v4_only. When v4_only is selected, the DNS resolver will only perform a lookup for addresses in the IPv4 family. If v6_only is selected, the DNS resolver will only perform a lookup for addresses in the IPv6 family. If auto is specified, the DNS resolver will first perform a lookup for addresses in the IPv6 family and fallback to a lookup for addresses in the IPv4 family. For cluster types other than strict_dns and logical_dns, this setting is ignored.

- **dns_resolvers**<br />
	([optional](#), array) If DNS resolvers are specified and the cluster type is either strict_dns, or logical_dns, this value is used to specify the cluster’s dns resolvers. If this setting is not specified, the value defaults to the default resolver, which uses /etc/resolv.conf for configuration. For cluster types other than strict_dns and logical_dns this setting is ignored.

- **outlier_detection**<br />
	([optional](#), object) If specified, outlier detection will be enabled for this upstream cluster. See the architecture overview for more information on outlier detection.



## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)