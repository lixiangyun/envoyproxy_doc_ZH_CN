## 监听器

Envoy配置顶层包含一个[监听器](../Introduction/Architectureoverview/Listeners.md)列表。每个单独的监听器配置具有以下格式：

* [v1 API参考](../v1APIreference.md)
* [v2 API参考](../v2APIreference.md)

### 统计

#### 监听器

每个监听器都有一个以 `listener.<address>` 为根的统计树。统计如下：

|	名称	|	类型	|	描述	|
|	 ------------------------	|	 ------------------------	|	 ------------------------	|
|	downstream_cx_total	|	Counter	|	连接总数	|
|	downstream_cx_destroy	|	Counter	|	销毁的连接总数	|
|	downstream_cx_active	|	Gauge	|	活动的连接总数	|
|	downstream_cx_length_ms	|	Histogram	|	连接时长，单位毫秒	|
|	ssl.connection_error	|	Counter	|	错误的TLS连接总数，不包括证书验证失败的	|
|	ssl.handshake	|	Counter	|	TLS连接握手成功的总数	|
|	ssl.session_reused	|	Counter	|	TLS会话恢复成功的总数	|
|	ssl.no_certificate	|	Counter	|	完全成功的TLS连接，没有客户端证书	|
|	ssl.fail_no_sni_match	|	Counter	|	由于缺少SNI匹配而被拒绝的TLS连接总数	|
|	ssl.fail_verify_no_cert	|	Counter	|	由于缺少客户端证书而失败的TLS连接总数	|
|	ssl.fail_verify_error	|	Counter	|	CA验证失败的TLS连接总数	|
|	ssl.fail_verify_san	|	Counter	|	SAN验证失败的TLS连接总数	|
|	ssl.fail_verify_cert_hash	|	Counter	|	证书锁定验证失败的TLS连接总数	|
|	ssl.cipher.<cipher>	|	Counter	|	使用<cipher>的TLS连接总数	|


#### 监听管理器

监听器管理器的统计树以`listener_manager`为根。用下面的统计。统计名称中的字符被替换为_。

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	listener_added	|	Counter	|	监听器被添加的总数（通过静态配置或LDS）	|
|	listener_modified	|	Counter	|	监听器被修改的总数（通过LDS）	|
|	listener_removed	|	Counter	|	监听器被删除的总数（通过LDS）	|
|	listener_create_success	|	Counter	|	监听器对象添加到工作组成功的总数。	|
|	listener_create_failure	|	Counter	|	监听器对象添加到工作组失败的总数。	|
|	total_listeners_warming	|	Gauge	|	当前正在热身的监听器的数量	|
|	total_listeners_active	|	Gauge	|	当前活动的监听器的数量	|
|	total_listeners_draining	|	Gauge	|	当前正在被引流的监听器数量	|


### 运行时
监听器支持以下运行时设置：

**ssl.alt_alpn**

有多少的百分比请求，使用配置的alt_alpn协议字符串。默认为0。



### 监听器发现服务（LDS）
监听器发现服务（LDS）是一个可选的API，Envoy将调用它来动态获取监听器。Envoy将协调API响应，并根据需要添加，修改或删除已知的监听器。

监听器更新的语义如下：

- 每个监听器必须有一个独特的名字。如果没有提供名称，Envoy将创建一个UUID。要动态更新的监听器，管理服务必须提供监听器的唯一名称。
- 当一个监听器被添加，在参与连接处理之前，会先进入“预热”阶段。例如，如果监听器引用RDS配置，那么在监听器移动到“活动”之前，将会解析并提取该配置。
- 监听器一旦创建，实际上就会保持不变。因此，更新监听器时，会创建一个全新的监听器（使用相同的侦听套接字）。这个监听者会通过上面所描述的，新增加的监听者都有“预热”过程。
- 当更新或删除监听器时，旧的监听器将被置于“逐出”状态，就像整个服务重新启动时一样。监听器移除之后，该监听器所拥有的连接，经过一段时间优雅地关闭（如果可能的话）剩余的连接。逐出时间通过`--drain-time-s`选项设置。


#### 配置
- [v1 LDS API](../v1APIreference/Listeners.md)
- [v2 LDS API](Overviewv2API.md)


#### 统计
LDS的统计树是以`listener_manager.lds`为根，统计如下：

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	config_reload	|	Counter	|	由于配置更新，导致配置API调用总数	|
|	update_attempt	|	Counter	|	LDS配置API调用重试总数	|
|	update_success	|	Counter	|	LDS配置API调用成功总数	|
|	update_failure	|	Counter	|	LDS配置API调用失败总数（网络或模式错误）	|
|	version	|	Gauge	|	上次成功调用的内容哈希值	|



## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
