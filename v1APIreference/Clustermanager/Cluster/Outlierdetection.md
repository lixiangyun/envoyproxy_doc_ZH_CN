### 异常值检测

```
{
  "consecutive_5xx": "...",
  "consecutive_gateway_failure": "...",
  "interval_ms": "...",
  "base_ejection_time_ms": "...",
  "max_ejection_percent": "...",
  "enforcing_consecutive_5xx" : "...",
  "enforcing_consecutive_gateway_failure" : "...",
  "enforcing_success_rate" : "...",
  "success_rate_minimum_hosts" : "...",
  "success_rate_request_volume" : "...",
  "success_rate_stdev_factor" : "..."
}
```

- **consecutive_5xx**<br />
	(optional, integer) 发生连续5xx逐出主机之前，连续5xx响应的数量。默认为5。

- **consecutive_gateway_failure**<br />
	(optional, integer) 逐出之前连续发生的连续“gateway errors”数量，包括（502,503,504状态或连接错误，映射到其中一个状态代码）默认为5。

- **interval_ms**<br />
	(optional, integer) 每次异常值分析扫描的时间间隔，这可能导致新抛出异常以及主机被重新添加到服务集群。默认为10000ms或10s。

- **base_ejection_time_ms**<br />
	(optional, integer) 主机被逐出的基准时间。实际时间等于基本时间乘以主机被逐出的次数。默认为30000ms或30s。

- **max_ejection_percent**<br />
	(optional, integer) 由于异常检测而逐出的主机占上游群集的最大百分比。默认为10％。

- **enforcing_consecutive_5xx**<br />
	(optional, integer) 当通过连续5xx检测到异常状态时，主机实际被逐出的几率百分比。这个设置可以用来禁止逐出或者缓慢地加速。默认为100。

- **enforcing_consecutive_gateway_failure**<br />
	(optional, integer) 当通过连续的网关故障检测到异常状态时，主机实际被逐出的几率百分比。这个设置可以用来禁止逐出或者缓慢地加速。默认为0。

- **enforcing_success_rate**<br />
	(optional, integer) 通过成功率统计检测到异常状态时，主机实际被逐出的几率百分比。这个设置可以用来禁止逐出或者缓慢地加速。默认为100。

- **success_rate_minimum_hosts**<br />
	(optional, integer) 必须具有足够的请求量来检测成功率异常值的群集中的主机数量。如果主机数量小于此设置，则不会为群集中的任何主机执行通过成功率统计信息的异常值检测。默认为5。

- **success_rate_request_volume**<br />
	(optional, integer) 在一个时间间隔内（如上述定义的时间间隔）必须收集的最小请求总数，以便将此主机包含在基于成功率的异常值检测中。如果低于此设置，则不会为该主机执行通过成功率统计的异常值检测。默认为100。

- **success_rate_stdev_factor**<br />
	(optional, integer) 这个因子被用来确定异常逐出成功率的阈值。逐出阈值是平均成功率与该因子与平均成功率的标准偏差的乘积之差：`mean-(stdev* success_rate_stdev_factor)`。这个因子除以一千得到一个两位小数值。也就是说，如果期望的因子是1.9，运行时间值应该是1900，默认为1900。


上述每个配置值都可以通过运行时值覆盖。

## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)
