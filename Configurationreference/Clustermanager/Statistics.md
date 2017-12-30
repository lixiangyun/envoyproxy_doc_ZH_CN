## 统计

- [概述](#概述)
- [健康检查统计](#健康检查统计)
- [离群检测统计](#离群检测统计)
- [动态HTTP统计](#动态http统计)
- [动态HTTP交叉树统计](#动态http交叉树统计)
- [按服务区动态HTTP统计](#按服务区动态http统计)
- [负载均衡统计](#负载均衡统计)
- [负载均衡子集统计](#负载均衡子集统计)

### 概述
集群管理器的统计树根为`cluster_manager.`用下面的统计描述。任何`:`字符在统计名称中的被替换为`_`。

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	cluster_added	|	Counter	|	总群集添加（通过静态配置或CDS）	|
|	cluster_modified	|	Counter	|	总群集修改（通过CDS）	|
|	cluster_removed	|	Counter	|	总群集删除（通过CDS）	|
|	total_clusters	|	Gauge	|	当前加载的群集数量	|

每个群集都有一个以`cluster.<name>.`为根的统计树。统计如下：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	upstream_cx_total	|	Counter	|	总连接数	|
|	upstream_cx_active	|	Gauge	|	总活动连接数	|
|	upstream_cx_http1_total	|	Counter	|	总HTTP/1.1连接数	|
|	upstream_cx_http2_total	|	Counter	|	总HTTP/2连接数	|
|	upstream_cx_connect_fail	|	Counter	|	总连接失败	|
|	upstream_cx_connect_timeout	|	Counter	|	总连接超时	|
|	upstream_cx_connect_attempts_exceeded	|	Counter	|	总连续的连接失败超过配置的连接尝试	|
|	upstream_cx_overflow	|	Counter	|	集群连接断路器溢出的总次数	|
|	upstream_cx_connect_ms	|	Histogram	|	连接建立毫秒	|
|	upstream_cx_length_ms	|	Histogram	|	连接长度毫秒	|
|	upstream_cx_destroy	|	Counter	|	总毁坏的连接	|
|	upstream_cx_destroy_local	|	Counter	|	总连接在本地被销毁	|
|	upstream_cx_destroy_remote	|	Counter	|	总连接被远程销毁	|
|	upstream_cx_destroy_with_active_rq	|	Counter	|	总共连接被1个活动请求销毁	|
|	upstream_cx_destroy_local_with_active_rq	|	Counter	|	总共有1个活动请求在本地销毁	|
|	upstream_cx_destroy_remote_with_active_rq	|	Counter	|	总共连接被1个活动请求远程销毁	|
|	upstream_cx_close_notify	|	Counter	|	总连接通过HTTP/1.1连接关闭标头或HTTP/2 GOAWAY关闭	|
|	upstream_cx_rx_bytes_total	|	Counter	|	收到的连接字节总数	|
|	upstream_cx_rx_bytes_buffered	|	Gauge	|	接收到当前缓冲的连接字节	|
|	upstream_cx_tx_bytes_total	|	Counter	|	发送的连接字节总数	|
|	upstream_cx_tx_bytes_buffered	|	Gauge	|	发送当前缓冲的连接字节	|
|	upstream_cx_protocol_error	|	Counter	|	协议错误的总连接数	|
|	upstream_cx_max_requests	|	Counter	|	由于最大请求而关闭总连接数	|
|	upstream_cx_none_healthy	|	Counter	|	由于没有健康的主机，没有建立连接总数	|
|	upstream_rq_total	|	Counter	|	总请求	|
|	upstream_rq_active	|	Gauge	|	总活动请求	|
|	upstream_rq_pending_total	|	Counter	|	挂起连接池连接的请求总数	|
|	upstream_rq_pending_overflow	|	Counter	|	连接池断路溢出并失败的请求总数	|
|	upstream_rq_pending_failure_eject	|	Counter	|	由于连接池连接失败而导致失败的总请求数	|
|	upstream_rq_pending_active	|	Gauge	|	等待连接池连接的活动请求总数	|
|	upstream_rq_cancelled	|	Counter	|	获取连接池连接之前被取消的总请求数	|
|	upstream_rq_maintenance_mode	|	Counter	|	由于维护模式而导致立即返回503错误的总请求	|
|	upstream_rq_timeout	|	Counter	|	超时等待响应的请求总数	|
|	upstream_rq_per_try_timeout	|	Counter	|	每次尝试超时的总请求数	|
|	upstream_rq_rx_reset	|	Counter	|	在远端重置的总请求数	|
|	upstream_rq_tx_reset	|	Counter	|	在本地重置的总请求数	|
|	upstream_rq_retry	|	Counter	|	请求重试次数	|
|	upstream_rq_retry_success	|	Counter	|	请求重试成功次数	|
|	upstream_rq_retry_overflow	|	Counter	|	由于熔断，未重试的总请求数	|
|	upstream_flow_control_paused_reading_total	|	Counter	|	流量控制，从上游暂停读取的总次数	|
|	upstream_flow_control_resumed_reading_total	|	Counter	|	流量控制，从上游恢复读取的总次数	|
|	upstream_flow_control_backed_up_total	|	Counter	|	上游连接备份、暂停下游读取的总次数	|
|	upstream_flow_control_drained_total	|	Counter	|	上游连接逐出、恢复下游读取的总次数	|
|	membership_change	|	Counter	|	总集群成员变化	|
|	membership_healthy	|	Gauge	|	当前群集健康成员总数（包括健康检查和异常值检测）	|
|	membership_total	|	Gauge	|	当前的集群成员总数	|
|	retry_or_shadow_abandoned	|	Counter	|	由于缓冲区限制，忽略或重试、被取消的总次数	|
|	config_reload	|	Counter	|	由于不同的配置，导致配置重新加载的API调用次数	|
|	update_attempt	|	Counter	|	总集群成员更新尝试次数	|
|	update_success	|	Counter	|	总集群成员更新成功次数	|
|	update_failure	|	Counter	|	总集群成员更新失败次数	|
|	version	|	Gauge	|	来自上次API调用加载成功的内容哈希	|
|	max_host_weight	|	Gauge	|	群集中所有主机的最大权重	|
|	bind_errors	|	Counter	|	将套接字绑定到配置的源地址错误总数	|


### 健康检查统计
如果配置了健康检查，那么集群会有一个以`cluster.<name>.health_check.`为根的统计树，统计如下：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	attempt	|	Counter	|	健康检查的次数	|
|	success	|	Counter	|	健康检查成功的次数	|
|	failure	|	Counter	|	执行健康检查快速失败的次数，（例如：HTTP 503，以及网络故障）	|
|	passive_failure	|	Counter	|	因被动事件导致的健康检查失败的次数（例如：`x-envoy-immediate-health-check-fail`）	|
|	network_failure	|	Counter	|	由于网络错误导致的健康检查失败次数	|
|	verify_cluster	|	Counter	|	尝试集群名称验证的健康检查的数量	|
|	healthy	|	Gauge	|	健康成员的数量	|


### 离群检测统计
如果为群集配置了离群异常检测，则统计信息将以`cluster.<name>.outlier_detection.`为根。并包含以下内容：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	ejections_enforced_total	|	Counter	|	由于任何异常类型导致的强制逐出的数量	|
|	ejections_active	|	Gauge	|	当前被逐出主机的数量	|
|	ejections_overflow	|	Counter	|	因达到最大逐出而中止次数百分占比	|
|	ejections_enforced_consecutive_5xx	|	Counter	|	执行的连续5xx逐出次数	|
|	ejections_detected_consecutive_5xx	|	Counter	|	检测到的连续5xx逐出次数（即使未被强制执行）	|
|	ejections_enforced_success_rate	|	Counter	|	执行成功率异常值逐出的次数	|
|	ejections_detected_success_rate	|	Counter	|	检测到的成功率异常值逐出次数（即使未执行）	|
|	ejections_enforced_consecutive_gateway_failure	|	Counter	|	执行的连续网关故障逐出次数	|
|	ejections_detected_consecutive_gateway_failure	|	Counter	|	检测到的连续网关故障逐出次数（即使未被强制执行）	|
|	ejections_total	|	Counter	|	**已过时**：由于任何异常值类型（即使未强制执行）	|
|	ejections_consecutive_5xx	|	Counter	|	**已过时**：连续的5xx被逐出次数（即使未被强制执行）	|


### 动态HTTP统计
若启用了HTTP，则动态HTTP响应统计信息也可用。这些由各种内部系统，以及各种路由、速率限制之类的过滤器构成的统计。以`cluster.<name>.`为根，并包含以下统计信息：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	upstream\_rq\_<*xx>	|	Counter	|	HTTP响应码汇总统计（例如：2xx，3xx等）	|
|	upstream\_rq_<*>	|	Counter	|	具体的HTTP响应码统计（例如：201、302等）	|
|	upstream\_rq_time	|	Histogram	|	请求时间，单位毫秒	|
|	canary.upstream\_rq_<*xx>	|	Counter	|	上游灰度发布期间的HTTP响应码统计	|
|	canary.upstream\_rq_<*>	|	Counter	|	上游灰度发布期间具体的HTTP响应码统计	|
|	canary.upstream_rq_time	|	Histogram	|	上游灰度发布期间请求时间毫秒	|
|	internal.upstream\_rq_<*xx>	|	Counter	|	来自内部的HTTP响应码统计	|
|	internal.upstream\_rq_<*>	|	Counter	|	来自内部具体的HTTP响应码统计	|
|	internal.upstream_rq_time	|	Histogram	|	来自内部请求时间，单位毫秒	|
|	external.upstream\_rq_<*xx>	|	Counter	|	来自外部HTTP响应码汇总统计	|
|	external.upstream\_rq_<*>	|	Counter	|	来自外部具体的HTTP响应码统计	|
|	external.upstream_rq_time	|	Histogram	|	来自外部请求时间，单位毫秒	|


### 动态HTTP交叉树统计
如果配置了交叉树统计信息，它们将以`cluster.<name>.<alt name>. `为命名空间。生成的统计信息与上面的动态HTTP统计信息相同。

### 按服务区动态HTTP统计
如果服务区可用于本地服务（通过`--service-zone`）和上游群集，则Envoy将以`cluster.<name>.zone.<from_zone>.<to_zone>`为命名空间。统计信息如下：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	upstream_rq_\<*xx>	|	Counter	|	HTTP响应码汇总统计（例如：2xx，3xx等）	|
|	upstream_rq_\<*>	|	Counter	|	具体的HTTP响应码统计（例如：201、302等）	|
|	upstream_rq_time	|	Histogram	|	请求时间，单位毫秒	|


### 负载均衡统计
监控负载均衡决策的统计信息。统计信息以`cluster.<name>.`为根，并包含以下统计信息：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	lb_healthy_panic	|	Counter	|	恐慌模式下承载负载均衡请求的总数	|
|	lb_zone_cluster_too_small	|	Counter	|	由于上游群集过小，无区域感知路由决策次数	|
|	lb_zone_routing_all_directly	|	Counter	|	所有请求直接发送到同一个区域决策次数	|
|	lb_zone_routing_sampled	|	Counter	|	发送一些请求到同一个区域决策次数	|
|	lb_zone_routing_cross_zone	|	Counter	|	区域感知路由模式，但必须发送交叉区域的次数	|
|	lb_local_cluster_not_ok	|	Counter	|	本地主机集未设置，或者是本地群集处于混乱模式	|
|	lb_zone_number_differs	|	Counter	|	本地和上游群集中的区域数目不同的次数	|


### 负载均衡子集统计
监控以`<arch_overview_load_balancer_subsets>`描述符负载均衡器子集的统计信息，统计信息以`cluster.<name>.`根并包含以下统计信息：


|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	lb_subsets_active	|	Gauge	|	当前可用子集的数量	|
|	lb_subsets_created	|	Counter	|	创建的子集数量	|
|	lb_subsets_removed	|	Counter	|	由于没有主机而被删除的子集数量	|
|	lb_subsets_selected	|	Counter	|	选择任何子集进行负载平衡的次数	|
|	lb_subsets_fallback	|	Counter	|	回退策略被调用的次数	|


## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)
