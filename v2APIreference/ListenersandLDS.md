## 监听&监听发现

Listener[架构概述](../v2APIreference/ListenersandLDS.md)

### Listener
[Listener proto]()

```
{
  "name": "...",
  "address": "{...}",
  "filter_chains": [],
  "use_original_dst": "{...}",
  "per_connection_buffer_limit_bytes": "{...}",
  "drain_type": "..."
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The unique name by which this listener is known. If no name is provided, Envoy will allocate an internal UUID for the listener. If the listener is to be dynamically updated or removed via LDS a unique name must be provided. By default, the maximum length of a listener’s name is limited to 60 characters. This limit can be increased by setting the --max-obj-name-len command line argument to the desired value.

- **address**</br>
	([Address](#), REQUIRED) The address that the listener should listen on. In general, the address must be unique, though that is governed by the bind rules of the OS. E.g., multiple listeners can listen on port 0 on Linux as the actual port will be allocated by the OS.

- **filter_chains**</br>
	([FilterChain](#), REQUIRED) A list of filter chains to consider for this listener. The FilterChain with the most specific FilterChainMatch criteria is used on a connection.


### Attention

In the current version, multiple filter chains are supported only so that SNI can be configured. See the FAQ entry on how to configure SNI for more information. When multiple filter chains are configured, each filter chain must have an identical set of filters. If the filters differ, the configuration will fail to load. In the future, this limitation will be relaxed such that different filters can be used depending on which filter chain matches (based on SNI or some other parameter).

- **use_original_dst**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) If a connection is redirected using iptables, the port on which the proxy receives it might be different from the original destination address. When this flag is set to true, the listener hands off redirected connections to the listener associated with the original destination address. If there is no listener associated with the original destination address, the connection is handled by the listener that receives it. Defaults to false.

- **per_connection_buffer_limit_bytes**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Soft limit on size of the listener’s new connection read and write buffers. If unspecified, an implementation defined default is applied (1MiB).

- **drain_type**</br>
	([Listener.DrainType](#)) The type of draining to perform at a listener-wide level.

### Listener.DrainType(Enum)
[Listener.DrainType proto]()

DEFAULT
	(DEFAULT) ?Drain in response to calling /healthcheck/fail admin endpoint (along with the health check filter), listener removal/modification, and hot restart.

### MODIFY_ONLY
?Drain in response to listener removal/modification and hot restart. This setting does not include /healthcheck/fail. This setting may be desirable if Envoy is hosting both ingress and egress listeners.


### Filter
[Filter proto]()

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The name of the filter to instantiate. The name must match a supported filter. The built-in filters are:


- **envoy.echo**</br>
- **envoy.http_connection_manager**</br>
- **envoy.mongo_proxy**</br>
- **envoy.redis_proxy**</br>
- **envoy.tcp_proxy**</br>
- **config**</br>
	([Struct](#)) Filter specific configuration which depends on the filter being instantiated. See the supported filters for further documentation.

### FilterChainMatch
[FilterChainMatch proto]()

Specifies the match criteria for selecting a specific filter chain for a listener.

```
{
  "sni_domains": []
}
```
- **sni_domains**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) If non-empty, the SNI domains to consider. May contain a wildcard prefix, e.g. *.example.com.


### Attention

See the FAQ entry on how to configure SNI for more information.

### FilterChain
[FilterChain proto]()

A filter chain wraps a set of match criteria, an option TLS context, a set of filters, and various other parameters.

```
{
  "filter_chain_match": "{...}",
  "tls_context": "{...}",
  "filters": [],
  "use_proxy_proto": "{...}"
}
```
- **filter_chain_match**</br>
	([FilterChainMatch](#)) The criteria to use when matching a connection to this filter chain.

- **tls_context**</br>
	([DownstreamTlsContext](#)) The TLS context for this filter chain.

- **filters**</br>
	([Filter](#)) A list of individual network filters that make up the filter chain for connections established with the listener. Order matters as the filters are processed sequentially as connection events happen. Note: If the filter list is empty, the connection will close by default.

- **use_proxy_proto**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Whether the listener should expect a PROXY protocol V1 header on new connections. If this option is enabled, the listener will assume that that remote address of the connection is the one specified in the header. Some load balancers including the AWS ELB support this option. If the option is absent or set to false, Envoy will use the physical peer address of the connection as the remote address.




## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)