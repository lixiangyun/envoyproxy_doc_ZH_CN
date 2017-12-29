## 访问日志

### 配置
访问日志是[HTTP连接管理器](../Configurationreference/HTTPconnectionmanager.md)或[TCP代理配置](../Configurationreference/Networkfilters/TCPproxy.md)的一部分。

- [v1 API 参考](../v1APIreference/Accesslogging.md)
- [v2 API 参考](../v2APIreference/Filters/Commonaccesslogtypes.md)

### 格式规则

访问日志格式字符串包含命令操作符或解释为普通字符串的其他字符。访问日志格式化程序不会做任何换行分隔符（如：“\n”），因此必须将其指定为格式字符串的一部分。请参阅示例的[默认格式](#默认格式)。请注意，访问日志行将为每个未设置/空值包含一个“-”字符。

访问日志的有些字段使用相同的格式字符串（如：HTTP和TCP）。有些字段的含义可能略有不同，具体取决于它是什么类型的日志。注意差异。

支持以下命令操作符：

- **%START_TIME%**<br />
    **HTTP**<br />
    请求开始时间，包括毫秒<br />
    
    **TCP**<br />
    下游连接开始时间，包括毫秒

- **%BYTES_RECEIVED%**<br />
    **HTTP**<br />
    收到主体字节<br />
    
    **TCP**<br />
    下行流连接时收到的字节

- **%PROTOCOL%**<br />
    **HTTP**<br />
    协议，目前是HTTP/1.1或HTTP/2<br />
    
    **TCP**<br />
    未实现 （”-”）

- **%RESPONSE_CODE%**<br />
    **HTTP**<br />
    HTTP响应代码。请注意，响应代码“0”表示服务器从未发送响应的开始。这通常意味着（下游）客户端连接断开了。<br />
    
    **TCP**<br />
    未实现 （”-”）
    
- **%BYTES_SENT%**<br />
    **HTTP**<br />
    正文发送的字节<br />
    
    **TCP**<br />
    在连接上发送到下行流字节

- **%DURATION%**<br />
    **HTTP**<br />
    请求从开始时间到最后一个字节输出的总持续时间（以毫秒为单位）<br />
    
    **TCP**<br />
    下游连接的总持续时间（以毫秒为单位）

- **%RESPONSE_FLAGS%**
    
    有关响应或连接的其他详细信息（如果有）。对于TCP连接，说明中提到的响应码不适用。可能的值是：<br />

    **HTTP and TCP**
    
        UH：除503响应码外，上游群集中没有健康的上游主机
        UF：除503响应代码外，上游连接失败
        UO：除503响应码外，上行溢出（断路）
        NR：除404响应码外，没有为的请求配置可用路由

    **HTTP only**
    
        LH：除503响应码之外，本地服务失败的健康检查请求
        UT：除了504响应代码之外，上游请求超时
        LR：除503响应码外，连接本地复位
        UR：除503响应码外，还有上行远程复位
        UC：除503响应码之外的上游连接终止
        DI：请求处理延迟了故障注入指定的时间
        FI：请求被中止，并通过故障注入指定响应代码
        RL：该请求除了429响应代码之外，还由HTTP速率限制过滤器进行本地速率限制
    

- **%UPSTREAM_HOST%**<br />
    上游主机URL（例如：TCP连接或者tcp://ip:port）

- **%UPSTREAM_CLUSTER%**<br />
    上游主机所属的上游集群

- **%UPSTREAM_LOCAL_ADDRESS%**<br />
    上游连接的本地地址

- **%DOWNSTREAM_ADDRESS%**<br />
    下游连接的远端地址

- **%REQ(X?Y):Z%**<br />
    **HTTP**<br />
    一个HTTP请求头部，其中X是主HTTP头部，Y是替代头部，而Z是一个可选参数，表示截取长度为Z个字符的字符串。该值取自名为X的HTTP请求头部，如果未设置，则使用请求头部Y. 如果不存在，则使用“-”符号替代<br />
    
    **TCP**<br />
    未实现 （”-”）

- **%RESP(X?Y):Z%**<br />
    **HTTP**<br />
    与`%REQ(X?Y):Z%`相同，但是来自HTTP响应头<br />
    
    **TCP**<br />
    未实现 （”-”）
    
### 默认格式

如果未指定自定义格式，Envoy将使用以下默认格式：

```
[%START_TIME%] "%REQ(:METHOD)% %REQ(X-ENVOY-ORIGINAL-PATH?:PATH)% %PROTOCOL%"
%RESPONSE_CODE% %RESPONSE_FLAGS% %BYTES_RECEIVED% %BYTES_SENT% %DURATION%
%RESP(X-ENVOY-UPSTREAM-SERVICE-TIME)% "%REQ(X-FORWARDED-FOR)%" "%REQ(USER-AGENT)%"
"%REQ(X-REQUEST-ID)%" "%REQ(:AUTHORITY)%" "%UPSTREAM_HOST%"\n
```

默认访问日志格式的示例：

```
[2016-04-15T20:17:00.310Z] "POST /api/v1/locations HTTP/2" 204 - 154 0 226 100 "10.0.35.28"
"nsq2http" "cc21d9b0-cf5c-432b-8c7e-98aeb7988cd2" "locations" "tcp://10.0.2.1:80"
```

## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
