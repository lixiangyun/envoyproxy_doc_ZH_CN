## 通用的限速组件

- [RateLimitDescriptor](#ratelimitdescriptor)
- [RateLimitDescriptor.Entry](#ratelimitdescriptorentry)

### RateLimitDescriptor
[RateLimitDescriptor proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rls.proto#L78)

`RateLimitDescriptor`是服务所使用的分层条目列表，用于确定最终的速率限制key和整体限制。这里有一些使用“Envoy”为域名的例子。

```
["authenticated": "false"], ["remote_address": "10.0.0.1"]
```

功能：限制所有IP地址为`10.0.0.1`未经身份验证的流量。该配置key为`remote_address`使用默认限制。如果希望提高`10.0.0.1`的限制或完全阻止，可以直接在配置中指定。

```
["authenticated": "false"], ["path": "/foo/bar"]
```

它做什么：在全局范围内为一个特定的路径（或前缀，如果在服务中配置的方式）限制所有未经身份验证的请求。

```
["authenticated": "false"], ["path": "/foo/bar"], ["remote_address": "10.0.0.1"]
```

功能：将未经验证的流量限制为特定IP地址和路径。像（1）我们可以提高/阻止特定的IP地址，如果我们想要一个覆盖配置。

```
["authenticated": "true"], ["client_id": "foo"]
```

它做什么：限制一个经过身份验证的客户端“foo”的所有流量。

```
["authenticated": "true"], ["client_id": "foo"], ["path": "/foo/bar"]
```

它做什么：限制流量到一个经过验证的客户端的特定路径“foo”

API背后的想法是，如果需要，(1)/(2)/(3)和(4)/(5)可以在1个请求中发送。这使得构建具有通用复杂后端应用场景的成为可能。


```
{
  "entries": []
}
```

- **entries**<br />
	([RateLimitDescriptor.Entry](#ratelimitdescriptorentry), REQUIRED) 描述符条目列表。

### RateLimitDescriptor.Entry
[RateLimitDescriptor.Entry proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/rls.proto#L79)

```
{
  "key": "...",
  "value": "..."
}
```

- **key**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) key描述符。

- **value**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) value描述符。



## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)