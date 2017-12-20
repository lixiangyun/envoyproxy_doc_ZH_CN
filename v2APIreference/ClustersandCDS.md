## 集群&集群发现


### Clusters and CDS
### Cluster
[Cluster proto]()

```
{
  "name": "...",
  "type": "...",
  "eds_cluster_config": "{...}",
  "connect_timeout": "{...}",
  "per_connection_buffer_limit_bytes": "{...}",
  "lb_policy": "...",
  "hosts": [],
  "health_checks": [],
  "max_requests_per_connection": "{...}",
  "circuit_breakers": "{...}",
  "tls_context": "{...}",
  "http_protocol_options": "{...}",
  "http2_protocol_options": "{...}",
  "dns_refresh_rate": "{...}",
  "dns_lookup_family": "...",
  "dns_resolvers": [],
  "outlier_detection": "{...}",
  "cleanup_interval": "{...}",
  "upstream_bind_config": "{...}",
  "lb_subset_config": "{...}",
  "ring_hash_lb_config": "{...}",
  "transport_socket": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Supplies the name of the cluster which must be unique across all clusters. The cluster name is used when emitting statistics. Any : in the cluster name will be converted to _ when emitting statistics. By default, the maximum length of a cluster name is limited to 60 characters. This limit can be increased by setting the --max-obj-name-len command line argument to the desired value.

- **type**</br>
	([Cluster.DiscoveryType](#)) The service discovery type to use for resolving the cluster.

- **eds_cluster_config**</br>
	([Cluster.EdsClusterConfig](#)) Configuration to use for EDS updates for the Cluster.

- **connect_timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The timeout for new network connections to hosts in the cluster.

- **per_connection_buffer_limit_bytes**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Soft limit on size of the cluster’s connections read and write buffers. If unspecified, an implementation defined default is applied (1MiB).

- **lb_policy**</br>
	([Cluster.LbPolicy](#)) The load balancer type to use when picking a host in the cluster.

- **hosts**</br>
	([Address](#)) If the service discovery type is STATIC, STRICT_DNS or LOGICAL_DNS, then hosts is required.

- **health_checks**</br>
	([HealthCheck](#)) Optional active health checking configuration for the cluster. If no configuration is specified no health checking will be done and all cluster members will be considered healthy at all times.

- **max_requests_per_connection**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Optional maximum requests for a single upstream connection. This parameter is respected by both the HTTP/1.1 and HTTP/2 connection pool implementations. If not specified, there is no limit. Setting this parameter to 1 will effectively disable keep alive.

- **circuit_breakers**</br>
	([CircuitBreakers](#)) Optional circuit breaking for the cluster.

- **tls_context**</br>
	([UpstreamTlsContext](#)) The TLS configuration for connections to the upstream cluster. If no TLS configuration is specified, TLS will not be used for new connections.

- **http_protocol_options**</br>
	([Http1ProtocolOptions](#)) Additional options when handling HTTP1 requests.


Only one of http_protocol_options, http2_protocol_options may be set.

- **http2_protocol_options**</br>
	([Http2ProtocolOptions](#)) Even if default HTTP2 protocol options are desired, this field must be set so that Envoy will assume that the upstream supports HTTP/2 when making new HTTP connection pool connections. Currently, Envoy only supports prior knowledge for upstream connections. Even if TLS is used with ALPN, http2_protocol_options must be specified. As an aside this allows HTTP/2 connections to happen over plain text.


Only one of http_protocol_options, http2_protocol_options may be set.

- **dns_refresh_rate**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) If the DNS refresh rate is specified and the cluster type is either STRICT_DNS, or LOGICAL_DNS, this value is used as the cluster’s DNS refresh rate. If this setting is not specified, the value defaults to 5000. For cluster types other than STRICT_DNS and LOGICAL_DNS this setting is ignored.

- **dns_lookup_family**</br>
	([Cluster.DnsLookupFamily](#)) The DNS IP address resolution policy. If this setting is not specified, the value defaults to V4_ONLY.

- **dns_resolvers**</br>
	([Address](#)) If DNS resolvers are specified and the cluster type is either STRICT_DNS, or LOGICAL_DNS, this value is used to specify the cluster’s dns resolvers. If this setting is not specified, the value defaults to the default resolver, which uses /etc/resolv.conf for configuration. For cluster types other than STRICT_DNS and LOGICAL_DNS this setting is ignored.

- **outlier_detection**</br>
	([Cluster.OutlierDetection](#)) If specified, outlier detection will be enabled for this upstream cluster. Each of the configuration values can be overridden via runtime values.

- **cleanup_interval**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The interval for removing stale hosts from a cluster type ORIGINAL_DST. Hosts are considered stale if they have not been used as upstream destinations during this interval. New hosts are added to original destination clusters on demand as new connections are redirected to Envoy, causing the number of hosts in the cluster to grow over time. Hosts that are not stale (they are actively used as destinations) are kept in the cluster, which allows connections to them remain open, saving the latency that would otherwise be spent on opening new connections. If this setting is not specified, the value defaults to 5000ms. For cluster types other than ORIGINAL_DST this setting is ignored.

- **upstream_bind_config**</br>
	([BindConfig](#)) Optional configuration used to bind newly established upstream connections. This overrides any bind_config specified in the bootstrap proto. If the addres and port are empty, no bind will be performed.

- **lb_subset_config**</br>
	([Cluster.LbSubsetConfig](#)) Configuration for load balancing subsetting.

- **ring_hash_lb_config**</br>
	([Cluster.RingHashLbConfig](#)) Optional configuration for the Ring Hash load balancing policy.


Only one of ring_hash_lb_config may be set.

- **transport_socket**</br>
	([TransportSocket](#)) See base.TransportSocket description.

### Cluster.EdsClusterConfig
[Cluster.EdsClusterConfig proto]()

Only valid when discovery type is EDS.

```
{
  "eds_config": "{...}",
  "service_name": "..."
}
```
- **eds_config**</br>
	([ConfigSource](#)) Configuration for the source of EDS updates for this Cluster.

- **service_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Optional alternative to cluster name to present to EDS. This does not have the same restrictions as cluster name, i.e. it may be arbitrary length.

### Cluster.OutlierDetection
[Cluster.OutlierDetection proto]()

See the architecture overview for more information on outlier detection.

```
{
  "consecutive_5xx": "{...}",
  "interval": "{...}",
  "base_ejection_time": "{...}",
  "max_ejection_percent": "{...}",
  "enforcing_consecutive_5xx": "{...}",
  "enforcing_success_rate": "{...}",
  "success_rate_minimum_hosts": "{...}",
  "success_rate_request_volume": "{...}",
  "success_rate_stdev_factor": "{...}",
  "consecutive_gateway_failure": "{...}",
  "enforcing_consecutive_gateway_failure": "{...}"
}
```
- **consecutive_5xx**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The number of consecutive 5xx responses before a consecutive 5xx ejection occurs. Defaults to 5.

- **interval**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The time interval between ejection analysis sweeps. This can result in both new ejections as well as hosts being returned to service. Defaults to 10000ms or 10s.

- **base_ejection_time**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The base time that a host is ejected for. The real time is equal to the base time multiplied by the number of times the host has been ejected. Defaults to 30000ms or 30s.

- **max_ejection_percent**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum % of an upstream cluster that can be ejected due to outlier detection. Defaults to 10%.

- **enforcing_consecutive_5xx**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The % chance that a host will be actually ejected when an outlier status is detected through consecutive 5xx. This setting can be used to disable ejection or to ramp it up slowly. Defaults to 100.

- **enforcing_success_rate**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The % chance that a host will be actually ejected when an outlier status is detected through success rate statistics. This setting can be used to disable ejection or to ramp it up slowly. Defaults to 100.

- **success_rate_minimum_hosts**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The number of hosts in a cluster that must have enough request volume to detect success rate outliers. If the number of hosts is less than this setting, outlier detection via success rate statistics is not performed for any host in the cluster. Defaults to 5.

- **success_rate_request_volume**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The minimum number of total requests that must be collected in one interval (as defined by the interval duration above) to include this host in success rate based outlier detection. If the volume is lower than this setting, outlier detection via success rate statistics is not performed for that host. Defaults to 100.

- **success_rate_stdev_factor**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) This factor is used to determine the ejection threshold for success rate outlier ejection. The ejection threshold is the difference between the mean success rate, and the product of this factor and the standard deviation of the mean success rate: mean - (stdev * success_rate_stdev_factor). This factor is divided by a thousand to get a double. That is, if the desired factor is 1.9, the runtime value should be 1900. Defaults to 1900.

- **consecutive_gateway_failure**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The number of consecutive gateway failures (502, 503, 504 status or connection errors that are mapped to one of those status codes) before a consecutive gateway failure ejection occurs. Defaults to 5.

- **enforcing_consecutive_gateway_failure**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The % chance that a host will be actually ejected when an outlier status is detected through consecutive gateway failures. This setting can be used to disable ejection or to ramp it up slowly. Defaults to 0.

### Cluster.LbSubsetConfig
[Cluster.LbSubsetConfig proto]()

Optionally divide the endpoints in this cluster into subsets defined by endpoint metadata and selected by route and weighted cluster metadata.

```
{
  "fallback_policy": "...",
  "default_subset": "{...}",
  "subset_selectors": []
}
```
- **fallback_policy**</br>
	([Cluster.LbSubsetConfig.LbSubsetFallbackPolicy](#)) The behavior used when no endpoint subset matches the selected route’s metadata. The value defaults to NO_FALLBACK.

- **default_subset**</br>
	([Struct](#)) Specifies the default subset of endpoints used during fallback if fallback_policy is DEFAULT_SUBSET. Each field in default_subset is compared to the matching LbEndpoint.Metadata under the envoy.lb namespace. It is valid for no hosts to match, in which case the behavior is the same as a fallback_policy of NO_FALLBACK.

- **subset_selectors**</br>
	([Cluster.LbSubsetConfig.LbSubsetSelector](#)) For each entry, LbEndpoint.Metadata’s envoy.lb namespace is traversed and a subset is created for each unique combination of key and value. For example:


```
{ "subset_selectors": [
    { "keys": [ "version" ] },
    { "keys": [ "stage", "hardware_type" ] }
]}
A subset is matched when the metadata from the selected route and weighted cluster contains the same keys and values as the subset’s metadata. The same host may appear in multiple subsets.

Cluster.LbSubsetConfig.LbSubsetSelector
[Cluster.LbSubsetConfig.LbSubsetSelector proto]

Specifications for subsets.

{
  "keys": []
}
```
- **keys**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) List of keys to match with the weighted cluster metadata.

Enum Cluster.LbSubsetConfig.LbSubsetFallbackPolicy
[Cluster.LbSubsetConfig.LbSubsetFallbackPolicy proto]()

If NO_FALLBACK is selected, a result equivalent to no healthy hosts is reported. If ANY_ENDPOINT is selected, any cluster endpoint may be returned (subject to policy, health checks, etc). If DEFAULT_SUBSET is selected, load balancing is performed over the endpoints matching the values from the default_subset field.

### NO_FALLBACK
	(DEFAULT) ?

### ANY_ENDPOINT
### ?
### DEFAULT_SUBSET
### ?
### Cluster.RingHashLbConfig
[Cluster.RingHashLbConfig proto]()

Specific configuration for the RingHash load balancing policy.

```
{
  "minimum_ring_size": "{...}",
  "deprecated_v1": "{...}"
}
```
- **minimum_ring_size**</br>
	([UInt64Value](#)) Minimum hash ring size, i.e. total virtual nodes. A larger size will provide better request distribution since each host in the cluster will have more virtual nodes. Defaults to 1024. In the case that total number of hosts is greater than the minimum, each host will be allocated a single virtual node.

- **deprecated_v1**</br>
	([Cluster.RingHashLbConfig.DeprecatedV1](#)) Deprecated settings from v1 config.

### Cluster.RingHashLbConfig.DeprecatedV1
[Cluster.RingHashLbConfig.DeprecatedV1 proto]()

```
{
  "use_std_hash": "{...}"
}
```
- **use_std_hash**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Defaults to true, meaning that std::hash is used to hash hosts onto the ketama ring. std::hash can vary by platform. For this reason, Envoy will eventually use xxHash by default. This field exists for migration purposes and will eventually be deprecated. Set it to false to use xxHash now.

Enum Cluster.DiscoveryType
[Cluster.DiscoveryType proto]()

Refer to service discovery type for an explanation on each type.

### STATIC
	(DEFAULT) ?Refer to the static discovery type for an explanation.

### STRICT_DNS
?Refer to the strict DNS discovery type for an explanation.
### LOGICAL_DNS
?Refer to the logical DNS discovery type for an explanation.
### EDS
?Refer to the service discovery type for an explanation.
### ORIGINAL_DST
?Refer to the original destination discovery type for an explanation.
Enum Cluster.LbPolicy
[Cluster.LbPolicy proto]()

Refer to load balancer type architecture overview section for information on each type.

### ROUND_ROBIN
	(DEFAULT) ?Refer to the round robin load balancing policy for an explanation.

### LEAST_REQUEST
?Refer to the least request load balancing policy for an explanation.
### RING_HASH
?Refer to the ring hash load balancing policy for an explanation.
### RANDOM
?Refer to the random load balancing policy for an explanation.
### ORIGINAL_DST_LB
?Refer to the original destination load balancing policy for an explanation.
Enum Cluster.DnsLookupFamily
[Cluster.DnsLookupFamily proto]()

When V4_ONLY is selected, the DNS resolver will only perform a lookup for addresses in the IPv4 family. If V6_ONLY is selected, the DNS resolver will only perform a lookup for addresses in the IPv6 family. If AUTO is specified, the DNS resolver will first perform a lookup for addresses in the IPv6 family and fallback to a lookup for addresses in the IPv4 family. For cluster types other than STRICT_DNS and LOGICAL_DNS, this setting is ignored.

### AUTO
	(DEFAULT) ?

### V4_ONLY
### ?
### V6_ONLY
### ?
### UpstreamBindConfig
[UpstreamBindConfig proto]()

An extensible structure containing the address Envoy should bind to when establishing upstream connections.

```
{
  "source_address": "{...}"
}
```
- **source_address**</br>
	([Address](#)) The address Envoy should bind to when establishing upstream connections.

### CircuitBreakers
[CircuitBreakers proto]()

Circuit breaking settings can be specified individually for each defined priority.

```
{
  "thresholds": []
}
```
- **thresholds**</br>
	([CircuitBreakers.Thresholds](#)) If multiple Thresholds are defined with the same RoutingPriority, the first one in the list is used. If no Thresholds is defined for a given RoutingPriority, the default values are used.

### CircuitBreakers.Thresholds
[CircuitBreakers.Thresholds proto]()

A Thresholds defines CircuitBreaker settings for a RoutingPriority.

```
{
  "priority": "...",
  "max_connections": "{...}",
  "max_pending_requests": "{...}",
  "max_requests": "{...}",
  "max_retries": "{...}"
}
```
- **priority**</br>
	([RoutingPriority](#)) The RoutingPriority the specified CircuitBreaker settings apply to.

- **max_connections**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum number of connections that Envoy will make to the upstream cluster. If not specified, the default is 1024.

- **max_pending_requests**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum number of pending requests that Envoy will allow to the upstream cluster. If not specified, the default is 1024.

- **max_requests**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum number of parallel requests that Envoy will make to the upstream cluster. If not specified, the default is 1024.

- **max_retries**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The maximum number of parallel retries that Envoy will allow to the upstream cluster. If not specified, the default is 3.



## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)