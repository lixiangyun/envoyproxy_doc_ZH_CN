## 通用发现服务接口

### DiscoveryRequest

[DiscoveryRequest proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/discovery.proto#L24)

发现请求：是指在通过某些API，为Envoy节点请求一组相同类型的带版本标签的资源。

```
{
  "version_info": "...",
  "node": "{...}",
  "resource_names": [],
  "type_url": "...",
  "response_nonce": "..."
}
```

- **version_info**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 请求消息所携带的版本信息，将是最近成功处理的响应中收到的版本信息，第一个请求中`version_info`为空。在收到响应之前不会发送新的请求，直到Envoy实例准备好ACK/NACK新配置为止。ACK/NACK分别通过返回应用的新API配置版本或先前的API配置版本来进行。每个`type_url`（见下文）都有一个独立的版本信息。

- **node**</br>
	([Node](Commontypes.md)) 发出请求的节点信息

- **resource_names**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 要订阅的资源列表，群集名称列表或路由配置名称。如果为空，则返回该API的所有资源。LDS/CDS期望`resource_names`为空，因为这是Envoy实例的全局资源。LDS和CDS响应将意味着需要通过EDS/RDS获取的一些资源，这些资源将会在`resource_names`中列出。

- **type_url**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 正在请求的资源的类型，例如“type.googleapis.com/envoy.api.v2.ClusterLoadAssignment”。 在单独的xDS API（例如CDS，LDS等）的请求中，资源类型不可见，但对于ADS是必需的。

- **response_nonce**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 对应于DiscoveryResponse的nonce的ACK/NACK。请参阅关于`version_info`和[DiscoveryResponse](#DiscoveryResponse) nonce上述的讨论。如果`nonce`不可用，则这可能是空的。以支持启动或非流xDS的实现。

### DiscoveryResponse

[DiscoveryResponse proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/discovery.proto#L58)

```
{
  "version_info": "...",
  "resources": [],
  "type_url": "...",
  "nonce": "..."
}
```

- **version_info**</br>
	([string](#)) 响应数据的版本信息

- **resources**</br>
	([Any](#)) 响应资源。关于这些资源的类型，取决于被调用的API。

- **type_url**</br>
	([string](#)) 资源的URL。如果资源非空，任何消息中的`type_url`必须与资源保持一致。这可以有效地识别在ADS上混合提供的xDS API。

- **nonce**</br>
	([string](#)) 对于基于gRPC的订阅，nonce提供了一种方法来显式确认以下`DiscoveryRequest`中的特定`DiscoveryResponse`。在此发现响应之前，Envoy可能会向流管理服务器发送其他消息，以便在响应发送时未经处理。nonce允许管理服务器忽略前一版本的任何进一步发现请求，直到当前的发现请求。nonce是可选的，对于基于非流的xDS实现，不是必需的。

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)
