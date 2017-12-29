
## Listeners

### [Listener discovery service (LDS)]



```
{
  "name": "...",
  "address": "...",
  "filters": [],
  "ssl_context": "{...}",
  "bind_to_port": "...",
  "use_proxy_proto": "...",
  "use_original_dst": "...",
  "per_connection_buffer_limit_bytes": "...",
  "drain_type": "..."
}
```
- **name**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The unique name by which this listener is known. If no name is provided, Envoy will allocate an internal UUID for the listener. If the listener is to be dynamically updated or removed via LDS a unique name must be provided. By default, the maximum length of a listener’s name is limited to 60 characters. This limit can be increased by setting the --max-obj-name-len command line argument to the desired value.

- **address**<br />
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The address that the listener should listen on. Currently only TCP listeners are supported, e.g., “tcp://127.0.0.1:80”. Note, “tcp://0.0.0.0:80” is the wild card match for any IPv4 address with port 80.

- **filters**<br />
	([required](#), array) A list of individual network filters that make up the filter chain for connections established with the listener. Order matters as the filters are processed sequentially as connection events happen.


Note: If the filter list is empty, the connection will close by default.

- **ssl_context**<br />
	([optional](#), object) The TLS context configuration for a TLS listener. If no TLS context block is defined, the listener is a plain text listener.

- **bind_to_port**<br />
	([optional](#), boolean) Whether the listener should bind to the port. A listener that doesn’t bind can only receive connections redirected from other listeners that set use_original_dst parameter to true. Default is true.

- **use_proxy_proto**<br />
	([optional](#), boolean) Whether the listener should expect a PROXY protocol V1 header on new connections. If this option is enabled, the listener will assume that that remote address of the connection is the one specified in the header. Some load balancers including the AWS ELB support this option. If the option is absent or set to false, Envoy will use the physical peer address of the connection as the remote address.

- **use_original_dst**<br />
	([optional](#), boolean) If a connection is redirected using iptables, the port on which the proxy receives it might be different from the original destination address. When this flag is set to true, the listener hands off redirected connections to the listener associated with the original destination address. If there is no listener associated with the original destination address, the connection is handled by the listener that receives it. Defaults to false.

- **per_connection_buffer_limit_bytes**<br />
	([optional](#), integer) Soft limit on size of the listener’s new connection read and write buffers. If unspecified, an implementation defined default is applied (1MiB).

- **drain_type**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The type of draining that the listener does. Allowed values include default and modify_only. See the draining architecture overview for more information.

### Filters
Network filter architecture overview.

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**<br />
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The name of the filter to instantiate. The name must match a supported filter.

- **config**<br />
	([required](#), object) Filter specific configuration which depends on the filter being instantiated. See the supported filters for further documentation.

### TLS context
TLS architecture overview.

```
{
  "cert_chain_file": "...",
  "private_key_file": "...",
  "alpn_protocols": "...",
  "alt_alpn_protocols": "...",
  "ca_cert_file": "...",
  "verify_certificate_hash": "...",
  "verify_subject_alt_name": [],
  "cipher_suites": "...",
  "ecdh_curves": "...",
  "session_ticket_key_paths": []
}
```
- **cert_chain_file**<br />
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The certificate chain file that should be served by the listener.

- **private_key_file**<br />
	([required](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) The private key that corresponds to the certificate chain file.

- **alpn_protocols**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) Supplies the list of ALPN protocols that the listener should expose. In practice this is likely to be set to one of two values (see the codec_type parameter in the HTTP connection manager for more information):


“h2,http/1.1” If the listener is going to support both HTTP/2 and HTTP/1.1.
“http/1.1” If the listener is only going to support HTTP/1.1
- **alt_alpn_protocols**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) An alternate ALPN protocol string that can be switched to via runtime. This is useful for example to disable HTTP/2 without having to deploy a new configuration.

- **ca_cert_file**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) A file containing certificate authority certificates to use in verifying a presented client side certificate. If not specified and a client certificate is presented it will not be verified. By default, a client certificate is optional, unless one of the additional options ( require_client_certificate, verify_certificate_hash or verify_subject_alt_name) is also specified.

- **require_client_certificate**<br />
	([optional](#), boolean) If specified, Envoy will reject connections without a valid client certificate.

- **verify_certificate_hash**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) If specified, Envoy will verify (pin) the hash of the presented client side certificate.

- **verify_subject_alt_name**<br />
	([optional](#), array) An optional list of subject alt names. If specified, Envoy will verify that the client certificate’s subject alt name matches one of the specified values.

- **cipher_suites**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) If specified, the TLS listener will only support the specified cipher list. If not specified, the default list:

[ECDHE-ECDSA-AES128-GCM-SHA256|ECDHE-ECDSA-CHACHA20-POLY1305]()
[ECDHE-RSA-AES128-GCM-SHA256|ECDHE-RSA-CHACHA20-POLY1305]()
### ECDHE-ECDSA-AES128-SHA256
### ECDHE-RSA-AES128-SHA256
### ECDHE-ECDSA-AES128-SHA
### ECDHE-RSA-AES128-SHA
### AES128-GCM-SHA256
### AES128-SHA256
### AES128-SHA
### ECDHE-ECDSA-AES256-GCM-SHA384
### ECDHE-RSA-AES256-GCM-SHA384
### ECDHE-ECDSA-AES256-SHA384
### ECDHE-RSA-AES256-SHA384
### ECDHE-ECDSA-AES256-SHA
### ECDHE-RSA-AES256-SHA
### AES256-GCM-SHA384
### AES256-SHA256
### AES256-SHA
will be used.

- **ecdh_curves**<br />
	([optional](https://developers.google.com/protocol-buffers/docs/proto#scalar), string) If specified, the TLS connection will only support the specified ECDH curves. If not specified, the default curves (X25519, P-256) will be used.

- **session_ticket_key_paths**<br />
	([optional](#), array) Paths to keyfiles for encrypting and decrypting TLS session tickets. The first keyfile in the array contains the key to encrypt all new sessions created by this context. All keys are candidates for decrypting received tickets. This allows for easy rotation of keys by, for example, putting the new keyfile first, and the previous keyfile second.


If session_ticket_key_paths is not specified, the TLS library will still support resuming sessions via tickets, but it will use an internally-generated and managed key, so sessions cannot be resumed across hot restarts or on different hosts.

Each keyfile must contain exactly 80 bytes of cryptographically-secure random data. For example, the output of openssl rand 80.




### Listener discovery service (LDS)
```
{
  "cluster": "...",
  "refresh_delay_ms": "..."
}
```
- **cluster**<br />
	([required](#), string) The name of an upstream cluster that hosts the listener discovery service. The cluster must run a REST service that implements the LDS HTTP API. NOTE: This is the name of a cluster defined in the cluster manager configuration, not the full definition of a cluster as in the case of SDS and CDS.

- **refresh_delay_ms**<br />
	([optional](#), integer) The delay, in milliseconds, between fetches to the LDS API. Envoy will add an additional random jitter to the delay that is between zero and refresh_delay_ms milliseconds. Thus the longest possible refresh delay is 2 * refresh_delay_ms. Default value is 30000ms (30 seconds).

### REST API
### GET /v1/listeners/(string: service_cluster)/(string: service_node)
Asks the discovery service to return all listeners for a particular service_cluster and service_node. service_cluster corresponds to the --service-cluster CLI option. service_node corresponds to the --service-node CLI option. Responses use the following JSON schema:

```
{
  "listeners": []
}
```
- **listeners**<br />
	([Required](#), array) A list of listeners that will be dynamically added/modified within the listener manager. The management server is expected to respond with the complete set of listeners that Envoy should configure during each polling cycle. Envoy will reconcile this list with the listeners that are currently loaded and either add/modify/remove listeners as necessary.




## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

