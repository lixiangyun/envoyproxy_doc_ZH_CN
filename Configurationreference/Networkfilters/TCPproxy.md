## TCP代理
- TCP代理[架构概述](../../Introduction/Architectureoverview/TCPproxy.md)
- [v1 API 参考](../../v1APIreference/Networkfilters/TCPproxy.md)
- [v2 API 参考](../../v2APIreference/Filters/Networkfilters/TCPProxy.md)

### 统计
TCP代理过滤器同时包括下游统计信息以及适用与多集群上游统计信息。下游统计信息的根为`tcp.<stat_prefix>.`统计如下：

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	downstream_cx_total	|	Counter	|	过滤器处理的连接总数	|
|	downstream_cx_no_route	|	Counter	|	没有找到匹配路由的连接数	|
|	downstream_cx_tx_bytes_total	|	Counter	|	写入下游连接的字节总数	|
|	downstream_cx_tx_bytes_buffered	|	Gauge	|	当前缓冲到下游连接的字节总数	|
|	downstream_flow_control_paused_reading_total	|	Counter	|	因流量控制暂停从下游读取的总次数	|
|	downstream_flow_control_resumed_reading_total	|	Counter	|	因流量控制从下游恢复读取的总次数	|

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)
