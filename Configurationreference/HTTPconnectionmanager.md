## HTTP连接管理器

- HTTP 连接管理器[架构概述](../Introduction/Architectureoverview/HTTPconnectionmanagement.md)
- HTTP 协议[架构概述](../Introduction/Architectureoverview/HTTPconnectionmanagement.md)
- [v1 API 参考](../v1APIreference/Networkfilters/HTTPconnectionmanager.md)
- [v2 API 参考](v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)

### [路由匹配](HTTPconnectionmanager/Routematching.md)

### [流量转移/分流](HTTPconnectionmanager/TrafficShiftingSplitting.md)
- 上游主机间的流量转移
- 多上游主机的流量分流

### [HTTP头部操作](HTTPconnectionmanager/HTTPheadermanipulation.md)
- user-agent                        
- server                            
- x-client-trace-id                 
- x-envoy-downstream-service-cluster
- x-envoy-downstream-service-node   
- x-envoy-external-address          
- x-envoy-force-trace               
- x-envoy-internal                  
- x-forwarded-client-cert           
- x-forwarded-for                   
- x-forwarded-proto                 
- x-request-id                      
- x-ot-span-context                 
- x-b3-traceid                      
- x-b3-spanid                       
- x-b3-parentspanid                 
- x-b3-sampled                      
- x-b3-flags                        
- Custom request/response headers 

### [HTTP头部清理](HTTPconnectionmanager/HTTPheadersanitizing.md)
- [x-envoy-decorator-operation](HTTPfilters/Router.md)
- [x-envoy-downstream-service-cluster](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-downstream-service-node](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-expected-rq-timeout-ms](HTTPfilters/Router.md)
- [x-envoy-external-address](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-force-trace](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-internal](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-envoy-max-retries](HTTPfilters/Router.md)
- [x-envoy-retry-grpc-on](HTTPfilters/Router.md)
- [x-envoy-retry-on](HTTPfilters/Router.md)
- [x-envoy-upstream-alt-stat-name](HTTPfilters/Router.md)
- [x-envoy-upstream-rq-per-try-timeout-ms](HTTPfilters/Router.md)
- [x-envoy-upstream-rq-timeout-alt-response](HTTPfilters/Router.md)
- [x-envoy-upstream-rq-timeout-ms](HTTPfilters/Router.md)
- [x-forwarded-client-cert](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-forwarded-for](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-forwarded-proto](HTTPconnectionmanager/HTTPheadermanipulation.md)
- [x-request-id](HTTPconnectionmanager/HTTPheadermanipulation.md)

### [统计](HTTPconnectionmanager/Statistics.md)
- 按代理客户统计
- 按监听端口统计

### [运行时设置](HTTPconnectionmanager/Runtime.md)

### [路由发现服务](HTTPconnectionmanager/RoutediscoveryserviceRDS.md)
- 统计

## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
