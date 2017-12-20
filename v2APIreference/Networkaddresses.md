## 网络地址

### Network addresses
### Note

- **v2 API difference: Addresses now have .proto structure.**</br>

### Pipe
[Pipe proto]()

```
{
  "path": "..."
}
```
- **path**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Unix Domain Socket path.

### SocketAddress
[SocketAddress proto]()

```
{
  "protocol": "...",
  "address": "...",
  "port_value": "...",
  "named_port": "...",
  "resolver_name": "..."
}
```
- **protocol**</br>
	([SocketAddress.Protocol](#))

- **address**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The address for this socket. Listeners will bind to the address or outbound connections will be made. An empty address implies a bind to 0.0.0.0 or ::. It’s still possible to distinguish on an address via the prefix/suffix matching in FilterChainMatch after connection. For clusters, an address may be either an IP or hostname to be resolved via DNS. If it is a hostname, resolver_name should be set unless default (i.e. DNS) resolution is expected.

- **port_value**</br>
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar))


Precisely one of port_value, named_port must be set.

- **named_port**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) This is only valid if resolver_name is specified below and the named resolver is capable of named port resolution.


Precisely one of port_value, named_port must be set.

- **resolver_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) The name of the resolver. This must have been registered with Envoy. If this is empty, a context dependent default applies. If address is a hostname this should be set for resolution other than DNS. If the address is a concrete IP address, no resolution will occur.

Enum SocketAddress.Protocol
[SocketAddress.Protocol proto]()

### TCP
	(DEFAULT) ?

### BindConfig
[BindConfig proto]()

```
{
  "source_address": "{...}"
}
```
- **source_address**</br>
	([SocketAddress](#), REQUIRED) The address to bind to when creating a socket.

### Address
[Address proto]()

Addresses specify either a logical or physical address and port, which are used to tell Envoy where to bind/listen, connect to upstream and find management servers.

```
{
  "socket_address": "{...}",
  "pipe": "{...}"
}
```
- **socket_address**</br>
	([SocketAddress](#))


Precisely one of socket_address, pipe must be set.

- **pipe**</br>
	([Pipe](#))


Precisely one of socket_address, pipe must be set.

### CidrRange
[CidrRange proto]()

CidrRange specifies an IP Address and a prefix length to construct the subnet mask for a CIDR range.

```
{
  "address_prefix": "...",
  "prefix_len": "{...}"
}
```
- **address_prefix**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) IPv4 or IPv6 address, e.g. 192.0.0.0 or 2001:db8::.

- **prefix_len**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Length of prefix, e.g. 0, 32.



## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)