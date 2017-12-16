## 统计

每个连接管理器都有一个以`http.<stat_prefix>.`为根的统计树。 统计如下：

|	名称	|	类型	|	描述	|
|	 ------------------------------------------	|	 ------------------------------------------	|	 ------------------------------------------	|
|	downstream_cx_total	|	Counter	|	连接总数	|
|	downstream_cx_ssl_total	|	Counter	|	TLS连接总数	|
|	downstream_cx_http1_total	|	Counter	|	HTTP / 1.1连接总数	|
|	downstream_cx_websocket_total	|	Counter	|	WebSocket连接总数	|
|	downstream_cx_http2_total	|	Counter	|	HTTP / 2连接总数	|
|	downstream_cx_destroy	|	Counter	|	连接关闭总数	|
|	downstream_cx_destroy_remote	|	Counter	|	因远端关闭而导致连接销毁总数	|
|	downstream_cx_destroy_local	|	Counter	|	由于本地关闭导致连接被摧毁的总数	|
|	downstream_cx_destroy_active_rq	|	Counter	|	连接被1个及以上活动请求销毁的总数	|
|	downstream_cx_destroy_local_active_rq	|	Counter	|	因1个及以上活动请求被本地销毁的连接总数	|
|	downstream_cx_destroy_remote_active_rq	|	Counter	|	因1个及以上活动请求被远端关闭而销毁的连接总数	|
|	downstream_cx_active	|	Gauge	|	活动的连接总数	|
|	downstream_cx_ssl_active	|	Gauge	|	活动TLS连接总数	|
|	downstream_cx_http1_active	|	Gauge	|	有效的HTTP / 1.1连接总数	|
|	downstream_cx_websocket_active	|	Gauge	|	有效的WebSocket连接总数	|
|	downstream_cx_http2_active	|	Gauge	|	有效的HTTP / 2连接总数	|
|	downstream_cx_protocol_error	|	Counter	|	协议错误的总数	|
|	downstream_cx_length_ms	|	Histogram	|	连接长度毫秒	|
|	downstream_cx_rx_bytes_total	|	Counter	|	接收的字节总数	|
|	downstream_cx_rx_bytes_buffered	|	Gauge	|	当前缓冲接收到的字节总数	|
|	downstream_cx_tx_bytes_total	|	Counter	|	发送的字节总数	|
|	downstream_cx_tx_bytes_buffered	|	Gauge	|	当前缓冲的发送字节总数	|
|	downstream_cx_drain_close	|	Counter	|	由逐出而造成总连接关闭总数	|
|	downstream_cx_idle_timeout	|	Counter	|	由于空闲超时而关闭的连接总数	|
|	downstream_flow_control_paused_reading_total	|	Counter	|	由于流量控制而被禁用的总读取次数	|
|	downstream_flow_control_resumed_reading_total	|	Counter	|	由于流量控制，在连接上启用的总读取次数	|
|	downstream_rq_total	|	Counter	|	总请求数	|
|	downstream_rq_http1_total	|	Counter	|	总HTTP / 1.1请求数	|
|	downstream_rq_http2_total	|	Counter	|	总HTTP / 2请求数	|
|	downstream_rq_active	|	Gauge	|	总活动请求	|
|	downstream_rq_response_before_rq_complete	|	Counter	|	在请求完成之前发送的响应总数	|
|	downstream_rq_rx_reset	|	Counter	|	收到的请求重置总数	|
|	downstream_rq_tx_reset	|	Counter	|	已发送请求重置总数	|
|	downstream_rq_non_relative_path	|	Counter	|	使用非相对HTTP路径的请求总数	|
|	downstream_rq_too_large	|	Counter	|	由于缓冲过大的Body而导致413请求总数	|
|	downstream_rq_2xx	|	Counter	|	回应2xx总数	|
|	downstream_rq_3xx	|	Counter	|	回应3xx总数	|
|	downstream_rq_4xx	|	Counter	|	回应4xx总数	|
|	downstream_rq_5xx	|	Counter	|	回应5xx总数	|
|	downstream_rq_ws_on_non_ws_route	|	Counter	|	因WebSocket升级而被拒绝的非WebSocket路由请求总数	|
|	downstream_rq_time	|	Histogram	|	请求时间，单位毫秒	|
|	rs_too_large	|	Counter	|	由于缓冲过大的Body而导致的响应错误总数	|


### 按代理客户统计
每个用户代理统计信息都以 ` http.<stat_prefix>.user_agent.<user_agent>.`为根。目前，Envoy与iOS（ios）和Android（android）的用户代理相匹配，并产生以下统计数据：

|	名称	|	类型	|	描述	|
|	 ------------------------------------------	|	 ------------------------------------------	|	 ------------------------------------------	|
|	downstream_cx_total	|	Counter	|	连接总数	|
|	downstream_cx_destroy_remote_active_rq	|	Counter	|	因1个及以上活动请求被远端关闭的连接总数	|
|	downstream_rq_total	|	Counter	|	请求总数	|


### 按监听端口统计

每个监听器统计信息的附加值均以`listener.<address>.http.<stat_prefix>.`为根。统计如下：

|	名称	|	类型	|	描述	|
|	 ------------------------------------------	|	 ------------------------------------------	|	 ------------------------------------------	|
|	downstream_rq_2xx	|	Counter	|	回应2xx总数	|
|	downstream_rq_3xx	|	Counter	|	回应3xx总数	|
|	downstream_rq_4xx	|	Counter	|	回应4xx总数	|
|	downstream_rq_5xx	|	Counter	|	回应5xx总数	|



## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)
