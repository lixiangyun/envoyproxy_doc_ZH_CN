## 监听器

```
{
  "name": "...",
  "address": "...",
  "filters": [],
  "ssl_context": "{...}",
  "bind_to_port": "...",
  "use_proxy_proto": "...",
  "use_original_dst": "...",
  "per_connection_buffer_limit_bytes": "...",
  "drain_type": "..."
}
```
- **name**<br />
	(optional, string) 这个已知监听器的唯一名称。如果没有提供名称，Envoy将为监听器分配一个内部UUID。如果要通过LDS动态更新或删除监听器，则必须提供唯一的名称。默认情况下，监听器名称的最大长度限制为60个字符。通过`--max-obj-name-len`命令行参数可以设置为所需的值，可以提高此限制。

- **address**<br />
	(required, string) 监听器所监听的地址。目前仅支持监听TCP，例如“tcp://127.0.0.1:80”。请注意，“tcp://0.0.0.0:80”是匹配任何带有端口80的IPv4地址的通配符。

- **filters**<br />
	(required, array) 监听器连接时需要处理的过滤器链，包含各个网络过滤器的列表。当发生连接事件时，将按顺序处理过滤器链。

    注意：如果过滤器列表为空，则默认关闭连接。

- **ssl_context**<br />
	(optional, object) 监听器的TLS上下文配置。如果没有定义TLS上下文，则监听器是纯文本监听器。

- **bind_to_port**<br />
	(optional, boolean) 是否将监听器绑定到相应的端口。不绑定的监听器只能接收其他监听器重定向的连接，将`use_original_dst`参数设置为true。默认是true。

- **use_proxy_proto**<br />
	(optional, boolean) 监听器是否应该在新连接上使用PROXY协议V1头。如果启用此选项，则监听器将假定该连接的远程地址是在头部中指定的地址。包括`AWS ELB`的一些负载平衡器，也支持此选项。如果该选项不存在或设置为false，Envoy将使用连接的物理对等地址作为远程地址。

- **use_original_dst**<br />
	(optional, boolean) 如果使用iptables重定向连接，则代理接收连接的端口可能与原始目标地址不同。当此标志设置为true时，监听器将重定向的连接切换到与原始目标地址关联的监听器。如果没有与原始目标地址关联的监听器，则连接由接收该监听器的监听器处理。默认为false。

- **per_connection_buffer_limit_bytes**<br />
	(optional, integer) 监听器的新连接读取和写入缓冲区大小的软限制。如果未指定，则应用实现定义的默认值（1MB）。

- **drain_type**<br />
	(optional, string) 监听器所做的逐出期间的类型。允许的值包括`default`和`modify_only`。有关更多信息，请参阅逐出[架构概述](../Introduction/Architectureoverview/Draining.md)。

### Filters
网络过滤器[架构概述](../Introduction/Architectureoverview/NetworkL3L4filters.md)。

```
{
  "name": "...",
  "config": "{...}"
}
```
- **name**<br />
	(required, string) 要实例化的过滤器的名称。该名称必须与支持的过滤器名匹配。

- **config**<br />
	(required, object) 指定的过滤器配置，这取决于被实例化的过滤器。有关更多文档，请参阅支持的[过滤器](../Configurationreference/Networkfilters.md)。

### TLS context
TLS[架构概述](../Introduction/Architectureoverview/TLS.md)。

```
{
  "cert_chain_file": "...",
  "private_key_file": "...",
  "alpn_protocols": "...",
  "alt_alpn_protocols": "...",
  "ca_cert_file": "...",
  "verify_certificate_hash": "...",
  "verify_subject_alt_name": [],
  "cipher_suites": "...",
  "ecdh_curves": "...",
  "session_ticket_key_paths": []
}
```
- **cert_chain_file**<br />
	(required, string) 提供该监听器使用的证书链文件。

- **private_key_file**<br />
	(required, string) 与证书链文件相对应的私钥。

- **alpn_protocols**<br />
	(optional, string) 由该监听器公开的ALPN协议列表。实际上，这可能会被设置为两个值之一（有关更多信息，请参阅[HTTP连接管理器](../v1APIreference/Networkfilters/HTTPconnectionmanager.md#codec_type)中的codec_type参数）：

	- “h2,http/1.1”：如果监听器要同时支持HTTP/2和HTTP/1.1。
    - “http/1.1”：如果监听器只支持HTTP/1.1

- **alt_alpn_protocols**<br />
	(optional, string) 一个可以通过运行时切换到的ALPN协议字符串。例如在不通过部署新的配置时，禁用HTTP/2是很有用。

- **ca_cert_file**<br />
	(optional, string) 包含证书颁发机构证书的文件，用于验证客户端提供的证书。如果未指定，而客户端提供了证书，则不会进行验证。默认情况下，客户端的证书是可选的，除非还指定了其中一个附加选项（`require_client_certificate`，`verify_certificate_hash`或`verify_subject_alt_name`）。

- **require_client_certificate**<br />
	(optional, boolean) 如果指定，Envoy将拒绝没有携带有效证书的客户端连接。

- **verify_certificate_hash**<br />
	(optional, string) 如果指定，Envoy将验证（pin）所提供的客户端证书的HASH。

- **verify_subject_alt_name**<br />
	(optional, array) 一组可选的标题名称，如果指定，Envoy将验证客户端证书的标题ALT名称是否与指定其中之一匹配。

- **cipher_suites**<br />
	(optional, string) 如果指定，则TLS监听器将仅支持指定的加密套件。如果未指定，则默认列表为：

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

- **ecdh_curves**<br />
	(optional, string) 如果指定，TLS连接将只支持指定的ECDH曲线算法。如果未指定，将使用默认曲线算法（X25519，P-256）。

- **session_ticket_key_paths**<br />
	(optional, array) 用于加密和解密TLS会话凭证的密钥文件路径。数组中的第一个密钥文件包含加密由此上下文创建的所有新会话的密钥。所有的密钥都是解密收到的凭证的备选。这允许通过例如将新的密钥文件放在第一位，将先前的密钥文件放在第二位，来很容易实现密钥轮换。

    如果未指定`session_ticket_key_paths`，则TLS库仍将支持通过故障恢复会话，但会使用内部生成和管理的密钥，因此会话不能在热重启或不同的主机上恢复。

    每个密钥文件必须包含正好80个字节的密码安全随机数据。 例如，openssl rand 80的输出。

    注意：使用此功能需要考虑存在的安全风险。即使支持完美前向保密的密码，对密钥的不正确处理也可能导致连接的保密性丧失。有关讨论，请参阅[连接]https://www.imperialviolet.org/2013/06/27/botchingpfs.html。为了最大限度地降低风险，您必须：

    - 保持会话凭证密钥至少与TLS证书私钥一样安全
    - 至少每天轮换会话凭证密钥，最好每小时轮换一次
    - 始终使用密码安全的随机数据源生成密钥

### Listener discovery service (LDS)
```
{
  "cluster": "...",
  "refresh_delay_ms": "..."
}
```

- **cluster**<br />
	(required, string) 承载监听发现服务的上游群集名称。群集必须实现LDS HTTP API的REST服务。注：这是在群集管理器配置中定义的群集名称，而不是群集的完整定义，例如SDS和CDS的配置方式。

- **refresh_delay_ms**<br />
	(optional, integer) 从LDS API获取信息的延迟（以毫秒为单位）。Envoy将在0到`refresh_delay_ms`之间的延迟上添加一个额外的随机抖动值。因此，最长的刷新延迟可能是`2*refresh_delay_ms`。默认值是30000ms（30秒）。

### REST API

```
GET /v1/listeners/(string: service_cluster)/(string: service_node)
```

请求发现服务返回特定`service_cluster`和`service_node`的所有监听器。`service_cluster`对应于`--service-cluster` [CLI选项](../Operationsandadministration/Commandlineoptions.md)。`service_node`对应于`--service-node` [CLI选项](../Operationsandadministration/Commandlineoptions.md)。使用以下JSON格式响应：

```
{
  "listeners": []
}
```

- **listeners**<br />
	(required, array) 将在监听器管理器中动态添加/修改的监听器列表。管理服务器将在每个轮询周期内，使用Envoy配置的完整监听程序进行响应。Envoy将协调此列表与当前加载的监听器，并根据需要添加/修改/删除监听器。

## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

