### TLS

在与上游集群连接时，Envoy支持在监听器中的终止以及发起TLS。对于Envoy来说，足以支持为现代Web服务执行标准的前端代理职责，并启动与具有高级TLS要求（TLS1.2，SNI等）的外部服务的连接。Envoy支持以下TLS特性：

- **密码可配置**：每个TLS监听者和客户端可以指定它支持的密码。
- **客户端证书**：除服务器证书验证之外，上游/客户端连接还可以提供客户端证书。
- **证书验证和固定**：证书验证选项包括基本链验证，验证标题名称和哈希固定。
- **ALPN**：TLS监听器支持ALPN。HTTP连接管理器使用这个信息来确定客户端是HTTP/1.1还是HTTP/2。
- **SNI**：SNI当前支持客户端连接。监听器可能会在未来添加支持。
- **会话恢复**：服务器连接支持通过TLS会话票据恢复以前的会话（请参阅[RFC 5077](https://www.ietf.org/rfc/rfc5077.txt)）。可以在热启动之间和并行Envoy实例之间执行恢复（通常在前端代理配置中使用）。

#### 基础实施
目前Envoy被写入使用[BoringSSL](https://boringssl.googlesource.com/boringssl)作为TLS提供者。

#### 认证过滤器
Envoy提供了一个网络过滤器，通过从REST VPN服务获取的主体执行TLS客户端身份验证。此过滤器将提供的客户端证书哈希与主机列表进行匹配，以确定是否允许连接。可选IP白名单也可以配置。该功能可用于为Web基础架构VPN前端代理。

客户端TLS认证过滤器[配置参考](../../Configurationreference/Networkfilters/ClientTLSauthentication.md)。

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)
