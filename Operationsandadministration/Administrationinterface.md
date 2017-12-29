## 管理接口

Envoy公开了一个本地管理界面，可以用来查询和修改服务的不同方面：

- [v1 API 参考](../v1APIreference/Administrationinterface.md)
- [v2 API 参考](../v2APIreference/Bootstrap.md)

### GET /
打印所有可用的API清单

### GET /certs
列出所有加载的TLS证书，包括文件名，序列号和到期日期。

### GET /clusters
列出所有配置的[集群管理器](../Introduction/Architectureoverview/Clustermanager.md)集群。此信息包括每个群集中发现的所有上游主机以及每个主机统计信息。这对服务发现的问题调试很有用。
- **集群管理器信息**<br />
    `version_info` 字符串，上次加载的[CDS](../Configurationreference/Clustermanager/Clusterdiscoveryservice.md)服务版本信息的字符串。如果envoy没有安装CDS，将会读取`version_info::static`输出。

- **集群信息**<br />
    - 所有优先级都设置[熔断](../Configurationreference/Clustermanager/Circuitbreaking.md)。
    - 如果使能了[异常值检测](../Introduction/Architectureoverview/Outlierdetection.md)，将会呈现[成功率平均值](../Introduction/Architectureoverview/Outlierdetection.md)和[逐出阈值](../Introduction/Architectureoverview/Outlierdetection.md)。如果在最后一个时间间隔内没有足够的数据来计算它们，那么这两个值都将是-1。
    - `added_via_api`标志，如果通过静态配置添加的集群，则为`false`，如果通过CDS API添加，则为`true`。

- **按主机统计**<br />
    
|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	cx_total	|	Counter	|	连接总数	|
|	cx_active	|	Gauge	|	总活动连接数	|
|	cx_connect_fail	|	Counter	|	总连接失败数	|
|	rq_total	|	Counter	|	总请求数	|
|	rq_timeout	|	Counter	|	总请求超时数	|
|	rq_success	|	Counter	|	带有非5xx响应的总请求数	|
|	rq_error	|	Counter	|	带有5xx响应的总请求数	|
|	rq_active	|	Gauge	|	总活动请求数	|
|	healthy	|	String	|	主机的健康状况。 见下文	|
|	weight	|	Integer	|	负载平衡权重（1-100）	|
|	zone	|	String	|	所在服务区域	|
|	canary	|	Boolean	|	主机是否是金丝雀（灰度发布）状态	|
|	success_rate	|	Double	|	请求成功率（0-100）。 如果间隔中没有足够的请求量来计算它，则返回-1	|

<br />

- **主机健康状况**<br />
    由于一个或多个不健康的状态，主机可能是健康的或不健康的。<br />如果主机健康，则会输出`healthy`字符串。
    如果主机不健康，则会输出以下一个或多个字符串：<br />
    
    **/failed_active_hc**：主机主动健康检查失败。<br />
    **/failed_outlier_check**：主机未通过异常值检测检查。


### GET /cpuprofiler
启用或禁用CPU分析器。需要与`gperftools`一起进行编译。

### GET /healthcheck/fail
入站健康检查失败。这需要使用HTTP健康检查过滤器。这对于将要关闭服务或完全重新启动之前，逐出服务非常有用。无论过滤器如何配置，调用此命令都将执行健康检查失败的请求。

### GET /healthcheck/ok
取消`GET /healthcheck/fail`的作用。这需要使用HTTP健康检查过滤器。

### GET /hot_restart_version
参见[--hot-restart-version](Commandlineoptions.md)

### GET /logging
在不同的子系统上启用/禁用不同的日志记录级别。一般只在开发过程中使用。

### GET /quitquitquit
干净地退出服务

### GET /reset_counters
将所有计数器清零。在调试过程中，这对`GET /stats`很有用。请注意，这不会影响任何发送到`statsd`的数据。它只会影响`GET /stats`本地命令的输出。

### GET /routes?route_config_name=\<name>
此接口仅在envoy具有配置RDS的HTTP路由时才可用。如果指定了查询，则此接口会转储所有已配置的HTTP路由表，或者仅转储与`route_config_name`查询匹配的HTTP路由表。

### GET /server_info
输出有关运行的服务器的信息。输出示例如下所示：
```
envoy 267724/RELEASE live 1571 1571 0
```
这些字段是：

- 进程名称
- 编译SHA和生成类型
- 健康检查状态（活跃或逐出）
- 当前热重启时间，以秒为单位
- 正常运行总时间（跨所有热重启阶段），以秒为单位
- 当前热重启的迭代数


### GET /stats
输出所有需要的统计数据。这只包计数和测量值。直方图不会输出，因为Envoy目前没有内置直方图，依赖`statsd`进行汇总。这个命令对本地调试非常有用。浏览此处获取[更多](Statisticsoverview.md)信息。

- **GET /stats?format=json**<br />
    以JSON格式的输出统计信息。这个统计信息支持编程对接。

- **GET /stats?format=prometheus**<br />
    以[Prometheus](https://prometheus.io/docs/instrumenting/exposition_formats/) v0.0.4格式的输出。这可以用来与`Prometheus`服务器集成。目前，只有计数器和计量器输出。直方图将在未来版本中提供。


## 返回
- [上一级](../Operationsandadministration.md)
- [首页目录](../README.md)
