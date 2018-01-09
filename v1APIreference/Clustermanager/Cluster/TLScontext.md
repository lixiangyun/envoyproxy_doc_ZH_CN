### TLS上下文

```
{
  "alpn_protocols": "...",
  "cert_chain_file": "...",
  "private_key_file": "...",
  "ca_cert_file": "...",
  "verify_certificate_hash": "...",
  "verify_subject_alt_name": [],
  "cipher_suites": "...",
  "ecdh_curves": "...",
  "sni": "..."
}
```

- **alpn_protocols**<br />
	(optional, string) 提供请求连接的ALPN协议列表。在实践中，这可能会被设置为一个单一的值或根本不设置：

    - "h2"：指定上游连接使用HTTP/2。在当前的实现中，这必须与集群的`http2`[功能选项](../Cluster.md#features)一起设置。这两个选项一起使用ALPN，来告诉Envoy服务器，期望支持HTTP/2的ALPN。然后http2功能将导致新的连接使用HTTP/2。

- **cert_chain_file**<br />
	(optional, string) 用于连接使用的证书文件链。这用于上游主机提供给客户端的TLS证书。

- **private_key_file**<br />
	(optional, string) 与证书文件链相对应的私钥。

- **ca_cert_file**<br />
	(optional, string) 包含证书颁发机构的证书文件，用于验证服务器提供的证书。

- **verify_certificate_hash**<br />
	(optional, string) 如果指定，Envoy将验证（pin）所服务器提供的证书哈希。

- **verify_subject_alt_name**<br />
	(optional, array) 可选的标题替代名称列表。如果指定，Envoy将验证服务器证书的`alt`标题名称是否与指定值之一匹配。

- **cipher_suites**<br />
	(optional, string) 如果指定，连接将支持指定的[加密套件列表](https://commondatastorage.googleapis.com/chromium-boringssl-docs/ssl.h.html#Cipher-suite-configuration)。如果未指定，则默认列表：

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

将会被使用；

- **ecdh_curves**<br />
	(optional, string) 如果指定，TLS连接将只支持指定的ECDH曲线算法。如果未指定，将使用默认（X25519，P-256）曲线算法。

- **sni**<br />
	(optional, string) 如果指定，则字符串将在TLS握手期间作为SNI呈现。


## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)
