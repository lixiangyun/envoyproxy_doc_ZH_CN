### 异常检测

异常值检测和逐出是动态确定上游群集中，某些主机是否正在执行不同于其他主机的过程，并将其从正常负载平衡集中移除。 性能可能会受到不同程度的影响，例如连续的故障，时间成功率，时间延迟等。异常检测是被动健康检查的一种形式。 Envoy还支持主动健康检查。被动和主动健康检查可以一起使用或独立使用，形成整体上游健康检查解决方案的基础。

#### 逐出算法
取决于异常值检测的类型，弹出或者以行内（例如在连续5xx的情况下）或以指定的间隔（例如在定期成功率的情况下）运行。逐出算法的工作原理如下：

1. 主机被确定为异常。
2. Envoy检查以确保逐出的主机数量低于允许的阈值（通过`outlier_detection.max_ejection_percent`设置指定）。如果逐出的主机数量超过阈值，主机不会被逐出。
3. 主机被逐出几毫秒。意味着主机被标记为不健康，在负载平衡期间不会使用，除非负载平衡器处于紧急情况。毫秒数等于`outlier_detection.base_ejection_time_ms`值乘以主机被逐出的次数。这会导致主机如果继续失败，则会被逐出更长和更长的时间。
4. 逐出的主机将在逐出时间满之后自动重新投入使用。一般而言，异常值检测与主动健康检查一起使用，用于全面的健康检查解决方案。

### 检测类型
Envoy支持以下异常检测类型：

#### 连续5xx
如果上游主机返回一些连续的5xx，它将被逐出。请注意，在这种情况下，5xx意味着一个实际的5xx响应代码，或者一个会导致HTTP路由器代表上游返回的事件（复位，连接失败等）。逐出所需的连续5xx数量由`outlier_detection.consecutive_5xx`值控制。

#### 连续的网关故障
如果上游主机返回一些连续的“网关错误”（502,503或504状态码），它将被逐出。请注意，这包括HTTP路由代表上游返回其中一个状态码的事件（重置，连接失败等）。逐出所需的连续网关故障的数量由`outlier_detection.consecutive_gateway_failure`值控制。

#### 成功率
基于成功率的异常值逐出汇总来自群集中每个主机的成功率数据。然后以给定的时间间隔，基于统计异常值检测来逐出主机。如果主机在一个时间间隔内的，请求量小于`outlier_detection.success_rate_request_volume`值，则不会为认为该主机成功率异常值。此外，如果一个时间间隔内请求量最小的主机，请求数小于`outlier_detection.success_rate_minimum_hosts`值，则不会对群集执行检测。

#### 逐出事件记录
Envoy可以选择生成异常值逐出事件日志。这在日常操作中非常有用，因为全局统计数据，不能提供有关哪些主机被逐出的信息以及原因。下面是一条JSON格式的日志记录：

```
{
  "time": "...",
  "secs_since_last_action": "...",
  "cluster": "...",
  "upstream_url": "...",
  "action": "...",
  "type": "...",
  "num_ejections": "...",
  "enforced": "...",
  "host_success_rate": "...",
  "cluster_success_rate_average": "...",
  "cluster_success_rate_ejection_threshold": "..."
}
```

**time**：

    事件发生的时间。

**secs_since_last_action**：

    自从上一次操作（逐出或未逐出）发生以来的时间，以秒为单位。如果是第一次，之前没有动作，该值将为-1。

**cluster**：

    被逐出主机所在的群集。

**upstream_url**：

    被逐出的主机URL。例如，`tcp://1.2.3.4:80`。

**action**：

    触发的动作（`eject`/`uneject`）

**type**：

    如果`action`是`eject`，这里描述的是`eject类型`；如`5xx`、`GatewayFailure`、`SuccessRate`。

**num_ejections**：

    如果`action`是`eject`，指定主机被逐出的累计次数（对于Envoy而言是本地的，并且如果主机被重新添加到集群，那么这个数值会被重置）

**enforced**：

    如果`action`是`eject`，指定逐出是否被强制执行。`true`表示主机被强制逐出。`false`表示着事件被记录了，但是主机并没有被逐出。

**host_success_rate**：

    如果`action`是`eject`，并且`type`是`SuccessRate`，主机在被逐出时的成功率（0~100范围）。

**cluster_success_rate_average**：

    如果`action`是`eject`，并且`type`是`SuccessRate`，主机在被逐出时所在的集群平均成功率（0~100范围）。

**cluster_success_rate_ejection_threshold**：

    如果`action`是`eject`，并且`type`是`SuccessRate`，指定逐出事件的成功率阈值。

### 配置参考
- [集群管理全局配置]()
- [每个群集配置](../../Configurationreference/Clustermanager/Cluster.md)
- [运行时设置](../../Configurationreference/Clustermanager/Runtime.md)
- [统计参考](../../Configurationreference/Clustermanager/Statistics.md)

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)
