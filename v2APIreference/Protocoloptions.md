## HTTP协议选项

- [Http1ProtocolOptions](#http1protocoloptions)
- [Http2ProtocolOptions](#http2protocoloptions)

### Http1ProtocolOptions

[Http1ProtocolOptions proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/protocol.proto#L16)
```
{
  "allow_absolute_url": "{...}"
}
```
- **allow_absolute_url**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 在请求中使用绝对URL处理HTTP请求。这些请求通常由客户端发送到转发/显式代理。这允许客户端将envoy配置为他们的HTTP代理。例如，在Unix中，这通常是通过设置`http_proxy`环境变量来完成的。

### Http2ProtocolOptions

[Http2ProtocolOptions proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/protocol.proto#L24)
```
{
  "hpack_table_size": "{...}",
  "max_concurrent_streams": "{...}",
  "initial_stream_window_size": "{...}",
  "initial_connection_window_size": "{...}"
}
```

- **hpack_table_size**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 允许编码器使用动态HPACK表的最大表大小（以八位字节为单位）。有效值范围从0到4294967295（2^32-1），默认值为4096。0表示禁用头部压缩。

- **max_concurrent_streams**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 在一个HTTP/2连接上允许最大流大小。有效值范围从1到2147483647（2^31-1），默认值为2147483647。

- **initial_stream_window_size**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 这个字段也可以作为Envoy在HTTP/2编解码缓冲区中缓冲每个流的字节限制。一旦缓冲区到达这个值，将触发停止数据流发送到编解码缓冲区。

- **initial_connection_window_size**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 与`initial_stream_window_size`类似，但是用于连接级流量控制窗口。目前，这与`initial_stream_window_size`具有相同的最小/最大/默认值。

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)


