## 运行时配置

上游集群支持以下运行时配置：

### 主动健康检查

- **health_check.min_interval**<br />
健康检查间隔的最小值。默认值为0。运行状况[检查间隔](../../v1APIreference/Clustermanager/Cluster/Healthchecking.md)将介于`min_interval`和`max_interval`之间。

- **health_check.max_interval**<br />
健康检查间隔的最大值。默认值是MAX_INT。健康检查间隔将在`min_interval`和`max_interval`之间。

- **health_check.verify_cluster**<br />
[健康检查过滤器](../../v1APIreference/Clustermanager/Cluster/Healthchecking.md)将远程服务集群的写入响应中针对预期的上游服务验证健康检查请求的百分比。


### 离群异常检测
有关离群异常检测的更多信息，请参见异常值检测[架构概述](../../Introduction/Architectureoverview/Outlierdetection.md)。异常值检测支持的运行时参数与静态配置参数相同，即：


- **outlier_detection.consecutive_5xx**<br />
用于异常值检测的[consecutive_5XX](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.consecutive_gateway_failure**<br />
用于异常值检测的[connected_gateway_failure](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.interval_ms**<br />
在异常值检测中的[interval_ms](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.base_ejection_time_ms**<br />
基于异常值检测的[base_ejection_time_ms](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.max_ejection_percent**<br />
异常值检测中的[max_ejection_percent](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.enforcing_consecutive_5xx**<br />
在异常值检测中执行[enforcing_consecutive_5xx](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.enforcing_consecutive_gateway_failure**<br />
在异常值检测中执行[enforcing_consecutive_gateway_failure](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.enforcing_success_rate**<br />
在异常值检测中执行[enforcing_success_rate](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.success_rate_minimum_hosts**<br />
异常值检测中的[success_rate_minimum_hosts](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.success_rate_request_volume**<br />
异常值检测中的[success_rate_request_volume](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

- **outlier_detection.success_rate_stdev_factor**<br />
异常值检测中的[success_rate_stdev_factor](../../v1APIreference/Clustermanager/Cluster/Outlierdetection.md)设置

### 核心
- **upstream.healthy_panic_threshold**<br />
[恐慌阈值](../../Introduction/Architectureoverview/Loadbalancing.md)百分比设置。默认为50％。

- **upstream.use_http2**<br />
配置群集是否使用[http2功能](../../v1APIreference/Clustermanager/Cluster.md)。设置为0即禁用HTTP/2。默认为启用。

- **upstream.weight_enabled**<br />
二进制开关，用于打开或关闭加权负载均衡。如果设置为非0，则启用加权负载均衡。默认为启用。



### 区域感知负载均衡
- **upstream.zone_routing.enabled**<br />
将被路由到相同的上游区域的请求的百分比。默认为100％的请求。

- **upstream.zone_routing.min_cluster_size**<br />
可以尝试区域感知路由的上游群集的最小大小。默认值为6。如果上游群集大小小于`min_cluster_size`，则区域感知路由将不被执行。

## 熔断
- **circuit_breakers.\<cluster_name>.\<priority>.max_connections**<br />
[断路器最大连接数设置](../../v1APIreference/Clustermanager/Cluster/Circuitbreakers.md)

- **circuit_breakers.\<cluster_name>.\<priority>.max_pending_requests**<br />
[断路器最大待处理请求设置](../../v1APIreference/Clustermanager/Cluster/Circuitbreakers.md)

- **circuit_breakers.\<cluster_name>.\<priority>.max_requests**<br />
[断路器最大请求数量设置](../../v1APIreference/Clustermanager/Cluster/Circuitbreakers.md)

- **circuit_breakers.\<cluster_name>.\<priority>.max_retries**<br />
[断路器最大重试次数设置](../../v1APIreference/Clustermanager/Cluster/Circuitbreakers.md)




## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)
