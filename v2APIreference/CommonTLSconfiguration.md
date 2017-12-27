## TLS配置

### DataSource
[DataSource proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L26)

```
{
  "filename": "..."
}
```

- **filename**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 本地文件系统数据源。必须设置一个文件名。

### TlsParameters
[TlsParameters proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L38)

```
{
  "tls_minimum_protocol_version": "...",
  "tls_maximum_protocol_version": "...",
  "cipher_suites": [],
  "ecdh_curves": []
}
```
- **tls_minimum_protocol_version**<br />
	([TlsParameters.TlsProtocol](#tlsparameterstlsprotocol)) 容许的最小TLS协议版本。

- **tls_maximum_protocol_version**<br />
	([TlsParameters.TlsProtocol](#tlsparameterstlsprotocol)) 容许的最大TLS协议版本。

- **cipher_suites**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果指定，则TLS监听器将仅支持指定的密码套件。如果未指定，则默认列表：

    ```
[ECDHE-ECDSA-AES128-GCM-SHA256|ECDHE-ECDSA-CHACHA20-POLY1305]
[ECDHE-RSA-AES128-GCM-SHA256|ECDHE-RSA-CHACHA20-POLY1305]
ECDHE-ECDSA-AES128-SHA256
ECDHE-RSA-AES128-SHA256
ECDHE-ECDSA-AES128-SHA
ECDHE-RSA-AES128-SHA
AES128-GCM-SHA256
AES128-SHA256
AES128-SHA
ECDHE-ECDSA-AES256-GCM-SHA384
ECDHE-RSA-AES256-GCM-SHA384
ECDHE-ECDSA-AES256-SHA384
ECDHE-RSA-AES256-SHA384
ECDHE-ECDSA-AES256-SHA
ECDHE-RSA-AES256-SHA
AES256-GCM-SHA384
AES256-SHA256
AES256-SHA
```
    将会被使用。

- **ecdh_curves**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果指定，TLS连接将只支持指定的ECDH密钥交换。如果未指定，将使用默认（X25519，P-256）。

### TlsParameters.TlsProtocol (Enum)
[TlsParameters.TlsProtocol proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L39)

- **TLS_AUTO**<br />
	(DEFAULT) Envoy将选择最佳的TLS版本。

- **TLSv1_0**<br />
    TLS 1.0
- **TLSv1_1**<br />
    TLS 1.1
- **TLSv1_2**<br />
    TLS 1.2
- **TLSv1_3**<br />
    TLS 1.3

### TlsCertificate
[TlsCertificate proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L95)

```
{
  "certificate_chain": "{...}",
  "private_key": "{...}"
}
```

- **certificate_chain**<br />
	([DataSource](#datasource)) TLS证书链。

- **private_key**<br />
	([DataSource](#datasource)) TLS私钥。

### TlsSessionTicketKeys
[TlsSessionTicketKeys proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L112)

```
{
  "keys": []
}
```
- **keys**<br />
	([DataSource](#datasource), REQUIRED) 
TLS会话的加解密的密钥。数组中的第一个密钥将作所有新会话加密的上下文。所有密钥都将用来解密所收到的凭证。这允许通过，先放置新的密钥在前面，第二个是旧的密钥，用于实现密钥轮换。

    如果未指定[session_ticket_keys](#downstreamtlscontext)，那么TLS库仍将支持通过故障恢复单个会话，但会使用内部生成和管理的密钥，因此会话不能在热重启或不同的主机上恢复。

    每个密钥必须包含完全80字节的密码安全随机数据。例如，`openssl rand 80`的输出。

    注意：使用此功能需要考虑严重的安全风险。即使使用了支持完美前向保密的密码，对密钥的不正确处理也可能导致连接的保密性丧失。有关讨论，请[参阅](https://www.imperialviolet.org/2013/06/27/botchingpfs.html)讨论。为了最大限度地降低风险，您必须：
     - 保持会话凭证密钥至少与TLS证书私钥一样安全。
     - 至少每天轮换会话凭证密钥，最好每小时轮换一次。
     - 始终使用密码安全的随机数据源生成密钥。

### CertificateValidationContext
[CertificateValidationContext proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L139)

```
{
  "trusted_ca": "{...}",
  "verify_certificate_hash": [],
  "verify_subject_alt_name": []
}
```

- **trusted_ca**<br />
	([DataSource](#datasource)) TLS证书数据包含颁发机构证书，提供用于验证客户端的证书。如果未指定并且提供了客户端证书，则不会进行验证。默认情况下，校验客户端证书是可选的，除非还指定了其中一个附加选项（`require_client_certificate`，`verify_certificate_hash`或`verify_subject_alt_name`）。

- **verify_certificate_hash**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 如果指定，Envoy将验证（pin）所提供证书的十六进制编码的SHA-256散列。

- **verify_subject_alt_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选，标题替代名称列表。如果指定，Envoy将验证证书的标题替代名称是否与指定其中一个值匹配。

### CommonTlsContext
[CommonTlsContext proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L173)

客户端和服务器TLS上下文共享。

```
{
  "tls_params": "{...}",
  "tls_certificates": [],
  "validation_context": "{...}",
  "alpn_protocols": []
}
```

- **tls_params**<br />
	([TlsParameters](#tlsparameters)) TLS协议版本，算法套件等。

- **tls_certificates**<br />
	([TlsCertificate](#tlscertificate)) 多个TLS证书可以与相同的上下文关联。 例如：为了同时允许RSA和ECDSA证书，可以配置两个TLS证书。

    注意：虽然这是一个列表，但是目前只支持单个证书。这将在未来放宽。

- **validation_context**<br />
	([CertificateValidationContext](#certificatevalidationcontext)) 如何验证对等证书。

- **alpn_protocols**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 提供监听器应该公开的ALPN协议列表。实际上，这可能会被设置为两个值之一（有关更多信息，请参阅HTTP连接管理器中的codec_type参数）：

    - “h2,http/1.1” 如果监听器要同时支持HTTP/2和HTTP/1.1。
    
    - “http/1.1” 如果监听器只支持HTTP/1.1。
    
    这个参数没有默认值。如果为空，Envoy将不会暴露ALPN。

### UpstreamTlsContext
[UpstreamTlsContext proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L214)

```
{
  "common_tls_context": "{...}",
  "sni": "..."
}
```

- **common_tls_context**<br />
	([CommonTlsContext](#commontlscontext)) 常见的TLS上下文设置。

- **sni**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 创建TLS后端连接时使用的SNI字符串。

### DownstreamTlsContext
[DownstreamTlsContext proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/sds.proto#L222)

```
{
  "common_tls_context": "{...}",
  "require_client_certificate": "{...}",
  "session_ticket_keys": "{...}"
}
```

- **common_tls_context**<br />
	([CommonTlsContext](#commontlscontext)) 常见的TLS上下文设置。

- **require_client_certificate**<br />
	([BoolValue](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#boolvalue)) 如果指定，Envoy将拒绝没有有效客户端证书的连接。

- **session_ticket_keys**<br />
	([TlsSessionTicketKeys](#tlssessionticketkeys)) TLS会话凭证密钥设置。 
    
    注意：只有一个`session_ticket_keys`可被设置。

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)