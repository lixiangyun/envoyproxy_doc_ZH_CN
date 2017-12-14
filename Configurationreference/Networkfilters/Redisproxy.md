## Redis代理

- Redis[架构概述](../../Introduction/Architectureoverview/Redis.md)
- [v1 API 参考](../../v1APIreference/Networkfilters/Redisproxy.md)
- [v2 API 参考](../../v2APIreference/Filters/Networkfilters/RedisProxy.md)

### 统计
配置Redis代理筛选器的统计信息，均以`redis.<stat_prefix>.`为前缀。统计如下：

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	downstream_cx_active	|	Gauge	|	总活动连接	|
|	downstream_cx_protocol_error	|	Counter	|	总协议错误	|
|	downstream_cx_rx_bytes_buffered	|	Gauge	|	当前缓冲中收到的字节总数	|
|	downstream_cx_rx_bytes_total	|	Counter	|	收到的字节总数	|
|	downstream_cx_total	|	Counter	|	连接总数	|
|	downstream_cx_tx_bytes_buffered	|	Gauge	|	当前缓冲中发送字节总数	|
|	downstream_cx_tx_bytes_total	|	Counter	|	发送的字节总数	|
|	downstream_cx_drain_close	|	Counter	|	由于逐出而关闭的连接数量	|
|	downstream_rq_active	|	Gauge	|	总活跃请求数量	|
|	downstream_rq_total	|	Counter	|	总请求数	|


### 分配器统计
Redis过滤器会收集redis中命令分配器的统计信息。`redis.<stat_prefix>.splitter.` 统计如下：

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	invalid_request	|	Counter	|	参数个数错误的请求数	|
|	unsupported_command	|	Counter	|	分配器无法识别的命令总数	|


### 按命令统计
Redis过滤器将收集redis中命令的统计信息。命名空间为`redis.<stat_prefix>.command.<command>. `。

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	total	|	Counter	|	命令总数	|


### 运行配置
Redis代理筛选器支持以下运行时设置：

**redis.drain_close_enabled**</br>
如果服务器正在逐出关闭，将会关闭连接的百分比，否则将尝试前置关闭。默认为100。



## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)
