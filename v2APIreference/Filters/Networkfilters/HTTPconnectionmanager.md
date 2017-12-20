## HTTP连接管理

### HTTP connection manager
HTTP connection manager configuration overview.

- **filter.network.HttpConnectionManager**</br>
[filter.network.HttpConnectionManager proto]()

```
{
  "codec_type": "...",
  "stat_prefix": "...",
  "rds": "{...}",
  "route_config": "{...}",
  "http_filters": [],
  "add_user_agent": "{...}",
  "tracing": "{...}",
  "http_protocol_options": "{...}",
  "http2_protocol_options": "{...}",
  "server_name": "...",
  "idle_timeout": "{...}",
  "drain_timeout": "{...}",
  "access_log": [],
  "use_remote_address": "{...}",
  "generate_request_id": "{...}",
  "forward_client_cert_details": "...",
  "set_current_client_cert_details": "{...}"
}
```
- **codec_type**</br>
	([filter.network.HttpConnectionManager.CodecType](#)) Supplies the type of codec that the connection manager should use.

- **stat_prefix**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The human readable prefix to use when emitting statistics for the connection manager. See the statistics documentation for more information.

- **rds**</br>
	([filter.network.Rds](#)) The connection manager’s route table will be dynamically loaded via the RDS API.


Precisely one of rds, route_config must be set.

- **route_config**</br>
	([RouteConfiguration](#)) The route table for the connection manager is static and is specified in this property.


Precisely one of rds, route_config must be set.

- **http_filters**</br>
	([filter.network.HttpFilter](#)) A list of individual HTTP filters that make up the filter chain for requests made to the connection manager. Order matters as the filters are processed sequentially as request events happen.

- **add_user_agent**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Whether the connection manager manipulates the user-agent and x-envoy-downstream-service-cluster headers. See the linked documentation for more information. Defaults to false.

- **tracing**</br>
	([filter.network.HttpConnectionManager.Tracing](#)) Presence of the object defines whether the connection manager emits tracing data to the configured tracing provider.

- **http_protocol_options**</br>
	([Http1ProtocolOptions](#)) Additional HTTP/1 settings that are passed to the HTTP/1 codec.

- **http2_protocol_options**</br>
	([Http2ProtocolOptions](#)) Additional HTTP/2 settings that are passed directly to the HTTP/2 codec.

- **server_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) An optional override that the connection manager will write to the server header in responses. If not set, the default is envoy.

- **idle_timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The idle timeout for connections managed by the connection manager. The idle timeout is defined as the period in which there are no active requests. If not set, there is no idle timeout. When the idle timeout is reached the connection will be closed. If the connection is an HTTP/2 connection a drain sequence will occur prior to closing the connection. See drain_timeout.

- **drain_timeout**</br>
	([Duration](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#duration)) The time that Envoy will wait between sending an HTTP/2 “shutdown notification” (GOAWAY frame with max stream ID) and a final GOAWAY frame. This is used so that Envoy provides a grace period for new streams that race with the final GOAWAY frame. During this grace period, Envoy will continue to accept new streams. After the grace period, a final GOAWAY frame is sent and Envoy will start refusing new streams. Draining occurs both when a connection hits the idle timeout or during general server draining. The default grace period is 5000 milliseconds (5 seconds) if this option is not specified.

- **access_log**</br>
	([filter.accesslog.AccessLog](#)) Configuration for HTTP access logs emitted by the connection manager.

- **use_remote_address**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) If set to true, the connection manager will use the real remote address of the client connection when determining internal versus external origin and manipulating various headers. If set to false or absent, the connection manager will use the x-forwarded-for HTTP header. See the documentation for x-forwarded-for, x-envoy-internal, and x-envoy-external-address for more information.

- **generate_request_id**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Whether the connection manager will generate the x-request-id header if it does not exist. This defaults to true. Generating a random UUID4 is expensive so in high throughput scenarios where this feature is not desired it can be disabled.

- **forward_client_cert_details**</br>
	([filter.network.HttpConnectionManager.ForwardClientCertDetails](#)) How to handle the x-forwarded-client-cert (XFCC) HTTP header.

- **set_current_client_cert_details**</br>
	([filter.network.HttpConnectionManager.SetCurrentClientCertDetails](#)) This field is valid only when forward_client_cert_details is APPEND_FORWARD or SANITIZE_SET and the client connection is mTLS. It specifies the fields in the client certificate to be forwarded. Note that in the x-forwarded-client-cert header, Hash is always set, and By is always set when the client certificate presents the SAN value.

- **filter.network.HttpConnectionManager.Tracing**</br>
[filter.network.HttpConnectionManager.Tracing proto]()

```
{
  "operation_name": "...",
  "request_headers_for_tags": []
}
```
- **operation_name**</br>
	([filter.network.HttpConnectionManager.Tracing.OperationName](#)) The span name will be derived from this field.

- **request_headers_for_tags**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) A list of header names used to create tags for the active span. The header name is used to populate the tag name, and the header value is used to populate the tag value. The tag is created if the specified header name is present in the request’s headers.

Enum filter.network.HttpConnectionManager.Tracing.OperationName
[filter.network.HttpConnectionManager.Tracing.OperationName proto]()

### INGRESS
	(DEFAULT) ?The HTTP listener is used for ingress/incoming requests.

### EGRESS
?The HTTP listener is used for egress/outgoing requests.
- **filter.network.HttpConnectionManager.SetCurrentClientCertDetails**</br>
[filter.network.HttpConnectionManager.SetCurrentClientCertDetails proto]()

```
{
  "subject": "{...}",
  "san": "{...}"
}
```
- **subject**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Whether to forward the subject of the client cert. Defaults to false.

- **san**</br>
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) Whether to forward the SAN of the client cert. Defaults to false.

Enum filter.network.HttpConnectionManager.CodecType
[filter.network.HttpConnectionManager.CodecType proto]()

### AUTO
	(DEFAULT) ?For every new connection, the connection manager will determine which codec to use. This mode supports both ALPN for TLS listeners as well as protocol inference for plaintext listeners. If ALPN data is available, it is preferred, otherwise protocol inference is used. In almost all cases, this is the right option to choose for this setting.

### HTTP1
?The connection manager will assume that the client is speaking HTTP/1.1.
### HTTP2
?The connection manager will assume that the client is speaking HTTP/2 (Envoy does not require HTTP/2 to take place over TLS or to use ALPN. Prior knowledge is allowed).
Enum filter.network.HttpConnectionManager.ForwardClientCertDetails
[filter.network.HttpConnectionManager.ForwardClientCertDetails proto]()

How to handle the x-forwarded-client-cert (XFCC) HTTP header.

### SANITIZE
	(DEFAULT) ?Do not send the XFCC header to the next hop. This is the default value.

### FORWARD_ONLY
?When the client connection is mTLS (Mutual TLS), forward the XFCC header in the request.
### APPEND_FORWARD
?When the client connection is mTLS, append the client certificate information to the request’s XFCC header and forward it.
### SANITIZE_SET
?When the client connection is mTLS, reset the XFCC header with the client certificate information and send it to the next hop.
### ALWAYS_FORWARD_ONLY
?Always forward the XFCC header in the request, regardless of whether the client connection is mTLS.
- **filter.network.Rds**</br>
[filter.network.Rds proto]()

```
{
  "config_source": "{...}",
  "route_config_name": "..."
}
```
- **config_source**</br>
	([ConfigSource](#), REQUIRED) Configuration source specifier for RDS.

- **route_config_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The name of the route configuration. This name will be passed to the RDS API. This allows an Envoy configuration with multiple HTTP listeners (and associated HTTP connection manager filters) to use different route configurations.

- **filter.network.HttpFilter**</br>
[filter.network.HttpFilter proto]()

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) The name of the filter to instantiate. The name must match a supported filter. The built-in filters are:


- **envoy.buffer**</br>
- **envoy.cors**</br>
- **envoy.fault**</br>
- **envoy.http_dynamo_filter**</br>
- **envoy.grpc_http1_bridge**</br>
- **envoy.grpc_json_transcoder**</br>
- **envoy.grpc_web**</br>
- **envoy.health_check**</br>
- **envoy.lua**</br>
- **envoy.rate_limit**</br>
- **envoy.router**</br>
- **config**</br>
	([Struct](#)) Filter specific configuration which depends on the filter being instantiated. See the supported filters for further documentation.



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../../README.md)