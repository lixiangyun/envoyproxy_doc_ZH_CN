## 网络地址

注意：v2 API差异：现有`.proto`地址结构。

### Pipe
[Pipe proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/address.proto#L11)

```
{
  "path": "..."
}
```

- **path**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Unix域套接字路径。

### SocketAddress
[SocketAddress proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/address.proto#L16)

```
{
  "protocol": "...",
  "address": "...",
  "port_value": "...",
  "named_port": "...",
  "resolver_name": "..."
}
```

- **protocol**<br />
	([SocketAddress.Protocol](#SocketAddress.Protocol))

- **address**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 套接字的地址。[监听器](../Configurationreference/Listeners.md)将绑定到该地址或出站连接地址。若配置为一个空的地址，意味着将绑定到0.0.0.0或::。在连接之后，仍然可以通过`FilterChainMatch`中的匹配前缀/后缀来区分地址。对于[群集](../v1APIreference/Clustermanager/Cluster.md)，可以是通过DNS解析的IP或主机名。如果是主机名，除非需要默认（即DNS）解析，否则应该设置[resolver_name](../v2APIreference/Networkaddresses.md)。

- **port_value**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 

- **named_port**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 这只有在下面指定了`resolver_name`并且指定的解析器能够进行命名的端口解析时才有效。

    注意：`port_value`、`named_port`必须选其中一个设置。

- **resolver_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 解析器的名称。这一定是在Envoy注册的。如果这是空的，则应用依赖于上下文的默认值。如果地址是主机名，则应该设置DNS以外的解决方案。如果地址是一个具体的IP地址，则不会发生解析。

### SocketAddress.Protocol (Enum)
[SocketAddress.Protocol proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/address.proto#L17)

- **TCP**<br />
	(DEFAULT) 

### BindConfig
[BindConfig proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/address.proto#L47)

```
{
  "source_address": "{...}"
}
```
- **source_address**<br />
	([SocketAddress](#SocketAddress), REQUIRED) 创建套接字时绑定的地址。

### Address
[Address proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/address.proto#L55)

指定逻辑或物理地址和端口，这些地址和端口用于告诉Envoy绑定/监听的地址，连接到上游并查找相应的管理服务器。

```
{
  "socket_address": "{...}",
  "pipe": "{...}"
}
```

- **socket_address**<br />
	([SocketAddress](#SocketAddress))

- **pipe**<br />
	([Pipe](#Pipe))
    
    注意：必须设置一个正确的socket_address或者Pipe。

### CidrRange
[CidrRange proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/address.proto#L65)

CidrRange指定一个IP地址和前缀长度来构造一个CIDR范围的子网掩码。

```
{
  "address_prefix": "...",
  "prefix_len": "{...}"
}
```

- **address_prefix**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) IPv4或IPv6地址，例如 192.0.0.0或2001:db8::。

- **prefix_len**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 前缀的长度，例如：0或者32。


## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)