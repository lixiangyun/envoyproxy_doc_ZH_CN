## HTTP头域操作

HTTP连接管理器在解码期间（当接收到请求时）以及在编码期间（当响应被发送时）处理多个HTTP头域。

### user-agent

如果启用了`add_user_agent`选项，则可以在解码过程中由连接管理器设置用户代理标头。标题只有在尚未设置的情况下才会被修改。如果连接管理器确实设置了头部，则该值由`--service-cluster`命令行选项确定。

### server


### x-client-trace-id


### x-envoy-downstream-service-cluster


### x-envoy-downstream-service-node


### x-envoy-external-address


### x-envoy-force-trace


### x-envoy-internal


### x-forwarded-client-cert


### x-forwarded-for


### x-forwarded-proto


### x-request-id


### x-ot-span-context


### x-b3-traceid
### x-b3-spanid
### x-b3-parentspanid
### x-b3-sampled
### x-b3-flags
### Custom request/response headers

## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)
