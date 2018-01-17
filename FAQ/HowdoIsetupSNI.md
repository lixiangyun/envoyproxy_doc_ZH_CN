## 我如何设置SNI？

SNI仅在[V2配置](../Configurationreference/Overviewv2API.md)的API中受支持。

目前的实现要求每个[FilterChain](../v2APIreference/ListenersandLDS.md)中的过滤器必须相同。在未来的版本中，这个要求将被放宽，以便SNI可以用来在完全不同的过滤器链之间进行选择。HTTP连接管理器中仍然可以使用[域名匹配](../v2APIreference/HTTProutemanagementandRDS.md)来选择不同的路由。这是SNI最常见的用例。

以下是满足上述要求的YAML示例。

```
address:
  socket_address: { address: 127.0.0.1, port_value: 1234 }
filter_chains:
- filter_chain_match:
    sni_domains: "example.com"
  tls_context:
    common_tls_context:
      tls_certificates:
        - certificate_chain: { filename: "example_com_cert.pem" }
          private_key: { filename: "example_com_key.pem" }
  filters:
  - name: envoy.http_connection_manager
    config:
      route_config:
        virtual_hosts:
        - routes:
          - match: { prefix: "/" }
            route: { cluster: service_foo }
- filter_chain_match:
    sni_domains: "www.example.com"
  tls_context:
    common_tls_context:
      tls_certificates:
        - certificate_chain: { filename: "www_example_com_cert.pem" }
          private_key: { filename: "www_example_com_key.pem" }
  filters:
  - name: envoy.http_connection_manager
    config:
      route_config:
        virtual_hosts:
        - routes:
          - match: { prefix: "/" }
            route: { cluster: service_foo }
```

## 返回
- [上一级](../FAQ.md)
- [首页目录](../README.md)