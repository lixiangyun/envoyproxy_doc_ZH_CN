### HTTP connection manager
HTTP connection manager architecture overview.
HTTP protocols architecture overview.
```
{
  "name": "http_connection_manager",
  "config": {
    "codec_type": "...",
    "stat_prefix": "...",
    "rds": "{...}",
    "route_config": "{...}",
    "filters": [],
    "add_user_agent": "...",
    "tracing": "{...}",
    "http1_settings": "{...}",
    "http2_settings": "{...}",
    "server_name": "...",
    "idle_timeout_s": "...",
    "drain_timeout_ms": "...",
    "access_log": [],
    "use_remote_address": "...",
    "forward_client_cert": "...",
    "set_current_client_cert": "...",
    "generate_request_id": "..."
  }
}
```
- **codec_type**<br />
	(required, string) Supplies the type of codec that the connection manager should use. Possible values are:


- **http1**<br />
The connection manager will assume that the client is speaking HTTP/1.1.
- **http2**<br />
The connection manager will assume that the client is speaking HTTP/2 (Envoy does not require HTTP/2 to take place over TLS or to use ALPN. Prior knowledge is allowed).
- **auto**<br />
For every new connection, the connection manager will determine which codec to use. This mode supports both ALPN for TLS listeners as well as protocol inference for plaintext listeners. If ALPN data is available, it is preferred, otherwise protocol inference is used. In almost all cases, this is the right option to choose for this setting.
- **stat_prefix**<br />
	(required, string) The human readable prefix to use when emitting statistics for the connection manager. See the statistics documentation for more information.

- **rds**<br />
	(sometimes required, object) The connection manager configuration must specify one of rds or route_config. If rds is specified, the connection manager’s route table will be dynamically loaded via the RDS API. See the documentation for more information.

- **route_config**<br />
	(sometimes required, object) The connection manager configuration must specify one of rds or route_config. If route_config is specified, the route table for the connection manager is static and is specified in this property.

- **filters**<br />
	(required, array) A list of individual HTTP filters that make up the filter chain for requests made to the connection manager. Order matters as the filters are processed sequentially as request events happen.

- **add_user_agent**<br />
	(optional, boolean) Whether the connection manager manipulates the user-agent and x-envoy-downstream-service-cluster headers. See the linked documentation for more information. Defaults to false.

- **tracing**<br />
	(optional, object) Presence of the object defines whether the connection manager emits tracing data to the configured tracing provider.

- **http1_settings**<br />
	(optional, object) Additional HTTP/1 settings that are passed to the HTTP/1 codec.


- **allow_absolute_url**<br />
	(optional, boolean) Handle http requests with absolute urls in the requests. These requests are generally sent by clients to forward/explicit proxies. This allows clients to configure envoy as their http proxy. In Unix, for example, this is typically done by setting the http_proxy environment variable.

- **http2_settings**<br />
	(optional, object) Additional HTTP/2 settings that are passed directly to the HTTP/2 codec. Currently supported settings are:


- **hpack_table_size**<br />
	(optional, integer) Maximum table size (in octets) that the encoder is permitted to use for the dynamic HPACK table. Valid values range from 0 to 4294967295 (2^32 - 1) and defaults to 4096. 0 effectively disables header compression.

- **max_concurrent_streams**<br />
	(optional, integer) Maximum concurrent streams allowed for peer on one HTTP/2 connection. Valid values range from 1 to 2147483647 (2^31 - 1) and defaults to 2147483647.

- **initial_stream_window_size**<br />
	(optional, integer) Initial stream-level flow-control window size. Valid values range from 65535 (2^16 - 1, HTTP/2 default) to 2147483647 (2^31 - 1, HTTP/2 maximum) and defaults to 268435456 (256 * 1024 * 1024).


NOTE: 65535 is the initial window size from HTTP/2 spec. We only support increasing the default window size now, so it’s also the minimum.

This field also acts as a soft limit on the number of bytes Envoy will buffer per-stream in the HTTP/2 codec buffers. Once the buffer reaches this pointer, watermark callbacks will fire to stop the flow of data to the codec buffers.

- **initial_connection_window_size**<br />
	(optional, integer) Similar to initial_stream_window_size, but for connection-level flow-control window. Currently , this has the same minimum/maximum/default as initial_stream_window_size.

These are the same options available in the upstream cluster http2_settings option.

- **server_name**<br />
	(optional, string) An optional override that the connection manager will write to the server header in responses. If not set, the default is envoy.

- **idle_timeout_s**<br />
	(optional, integer) The idle timeout in seconds for connections managed by the connection manager. The idle timeout is defined as the period in which there are no active requests. If not set, there is no idle timeout. When the idle timeout is reached the connection will be closed. If the connection is an HTTP/2 connection a drain sequence will occur prior to closing the connection. See drain_timeout_ms.

- **drain_timeout_ms**<br />
	(optional, integer) The time in milliseconds that Envoy will wait between sending an HTTP/2 “shutdown notification” (GOAWAY frame with max stream ID) and a final GOAWAY frame. This is used so that Envoy provides a grace period for new streams that race with the final GOAWAY frame. During this grace period, Envoy will continue to accept new streams. After the grace period, a final GOAWAY frame is sent and Envoy will start refusing new streams. Draining occurs both when a connection hits the idle timeout or during general server draining. The default grace period is 5000 milliseconds (5 seconds) if this option is not specified.

- **access_log**<br />
	(optional, array) Configuration for HTTP access logs emitted by the connection manager.

- **use_remote_address**<br />
	(optional, boolean) If set to true, the connection manager will use the real remote address of the client connection when determining internal versus external origin and manipulating various headers. If set to false or absent, the connection manager will use the x-forwarded-for HTTP header. See the documentation for x-forwarded-for, x-envoy-internal, and x-envoy-external-address for more information.

- **forward_client_cert**<br />
	(optional, string) How to handle the x-forwarded-client-cert (XFCC) HTTP header. Possible values are:


sanitize: Do not send the XFCC header to the next hop. This is the default value.
forward_only: When the client connection is mTLS (Mutual TLS), forward the XFCC header in the request.
always_forward_only: Always forward the XFCC header in the request, regardless of whether the client connection is mTLS.
append_forward: When the client connection is mTLS, append the client certificate information to the request’s XFCC header and forward it.
sanitize_set: When the client connection is mTLS, reset the XFCC header with the client certificate information and send it to the next hop.
For the format of the XFCC header, please refer to x-forwarded-client-cert.

- **set_current_client_cert_details**<br />
	(optional, array) A list of strings, possible values are Subject and SAN. This field is valid only when forward_client_cert is append_forward or sanitize_set and the client connection is mTLS. It specifies the fields in the client certificate to be forwarded. Note that in the x-forwarded-client-cert header, Hash is always set, and By is always set when the client certificate presents the SAN value.

- **generate_request_id**<br />
	(optional, boolean) Whether the connection manager will generate the x-request-id header if it does not exist. This defaults to true. Generating a random UUID4 is expensive so in high throughput scenarios where this feature is not desired it can be disabled.

### Tracing
```
{
  "tracing": {
    "operation_name": "...",
    "request_headers_for_tags": []
  }
}
```
- **operation_name**<br />
	(required, string) Span name will be derived from operation_name. “ingress” and “egress” are the only supported values.

- **request_headers_for_tags**<br />
	(optional, array) A list of header names used to create tags for the active span. The header name is used to populate the tag name, and the header value is used to populate the tag value. The tag is created if the specified header name is present in the request’s headers.

### Filters
HTTP filter architecture overview.

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**<br />
	(required, string) The name of the filter to instantiate. The name must match a supported filter.

- **config**<br />
	(required, object) Filter specific configuration which depends on the filter being instantiated. See the supported filters for further documentation.



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)

