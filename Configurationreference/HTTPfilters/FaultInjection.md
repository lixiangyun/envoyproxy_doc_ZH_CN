## 故障注入

故障注入过滤器可用于测试微服务对不同形式故障的恢复能力。该过滤器可用于注入延迟和中止请求，并带有用户指定的错误代码，从而能够处理不同的故障情况，如服务故障，服务过载，高网络延迟，网络分区等。故障注入可限制在基于请求的（目的地）上游集群，以及特定的一组预定义的请求报头组。

故障注入的范围仅限于通过网络进行通信的应用程序，以及可观察到的范围。无法模拟本地主机上的CPU和磁盘故障。

目前，故障注入过滤器有以下限制：
- 中止请求的错误代码仅限于HTTP状态码
- 延迟被限制在一定的时间内

未来的版本将包括支持限制故障到特定的路由，注入gRPC和HTTP/2特定的错误代码和基于分布的持续时延。

### 配置
**注意：故障注入过滤器必须在任何其他过滤器（包括路由器过滤器）之前插入。**

- [v1 API参考](../../v1APIreference/HTTPfilters/FaultInjection.md)
- [v2 API参考](../../v2APIreference/Filters/HTTPfilters/FaultInjection.md)

### 运行时设置
HTTP故障注入过滤器支持以下全局运行时设置：

- **fault.http.abort.abort_percent**</br>
如果头部匹配，将被中止请求的百分比。在配置中默认使用`abort_percent`值。如果配置不包含`abort`项，则`abort_percent`默认为0。

- **fault.http.abort.http_status**</br>
将被用作请求的HTTP状态码，如果头部匹配，则请求将被中止。默认为配置中指定的`http_status `。如果配置不包含`abort`项，则`http_status`默认为0。

- **fault.http.delay.fixed_delay_percent**</br>
如果头部匹配，请求将被延迟的百分比。默认为配置中指定的`delay_percent`，否则为0。

- **fault.http.delay.fixed_duration_ms**</br>
延迟时间以毫秒为单位。如果未指定，则将使用配置中指定的`fixed_duration_ms`。如果在运行时和配置中缺少这个字段，则不会注入延迟。

**请注意，在特定下游群集中，如果存在以下运行时配置值，则故障过滤器默认值会被覆盖。以下是下游指定的运行时配置值：**

- `fault.http.<downstream-cluster>.abort.abort_percent`
- `fault.http.<downstream-cluster>.abort.http_status`
- `fault.http.<downstream-cluster>.delay.fixed_delay_percent`
- `fault.http.<downstream-cluster>.delay.fixed_duration_ms`

下游集群名称取自HTTP `x-envoy-downstream-service-cluster`头部。如果在运行系统中找不到，则默认使用全局运行时设置为缺省配置。

### 统计
故障过滤器输出统计信息命名空间为`http.<stat_prefix>.fault.`。`stat_prefix`来自所拥有的HTTP连接管理器。

|	名称	|	类型	|	描述	|
|-----	|-------|-------|
|	`delays_injected`	|	Counter	|	延迟请求总数	|
|	`aborts_injected`	|	Counter	|	已中止的请求总数	|
|	`<downstream-cluster>.delays_injected`	|	Counter	|	指定下游群集的延迟请求总数	|
|	`<downstream-cluster>.aborts_injected`	|	Counter	|	指定下游群集的中止请求总数	|

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
