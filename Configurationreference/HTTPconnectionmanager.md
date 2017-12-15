## HTTP连接管理器

- HTTP 连接管理器[架构概述](../Introduction/Architectureoverview/HTTPconnectionmanagement.md)
- HTTP 协议[架构概述](../Introduction/Architectureoverview/HTTPconnectionmanagement.md)
- [v1 API 参考](../v1APIreference/Networkfilters/HTTPconnectionmanager.md)
- [v2 API 参考](v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)

### [路由匹配](HTTPconnectionmanager/Routematching.md)

### [流量转移/分流](HTTPconnectionmanager/TrafficShiftingSplitting.md)
- 上游主机间的流量转移
- 多上游主机的流量分流

### [HTTP头域操作](HTTPconnectionmanager/HTTPheadermanipulation.md)
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

### [HTTP头域清理](HTTPconnectionmanager/HTTPheadersanitizing.md)
- x-envoy-decorator-operation             
- x-envoy-downstream-service-cluster      
- x-envoy-downstream-service-node         
- x-envoy-expected-rq-timeout-ms          
- x-envoy-external-address                
- x-envoy-force-trace                     
- x-envoy-internal                        
- x-envoy-max-retries                     
- x-envoy-retry-grpc-on                   
- x-envoy-retry-on                        
- x-envoy-upstream-alt-stat-name          
- x-envoy-upstream-rq-per-try-timeout-ms  
- x-envoy-upstream-rq-timeout-alt-response
- x-envoy-upstream-rq-timeout-ms          
- x-forwarded-client-cert                 
- x-forwarded-for                         
- x-forwarded-proto                       
- x-request-id                            

### [统计](HTTPconnectionmanager/Statistics.md)
- 按代理客户统计
- 按监听端口统计

### [运行设置](HTTPconnectionmanager/Runtime.md)

### [路由服务发现](HTTPconnectionmanager/RoutediscoveryserviceRDS.md)
- 统计

## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
