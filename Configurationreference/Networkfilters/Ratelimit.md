## 速率限制

- 全局限速[架构概述](../../Introduction/Architectureoverview/Globalratelimiting.md)
- [v1 API 参考](../../v1APIreference/Networkfilters/Ratelimit.md)
- [v2 API 参考](../../v2APIreference/Filters/Networkfilters/Ratelimit.md)

### 统计
每个配置的速率限制过滤器的统计信息均以`ratelimit.<stat_prefix>.`为前缀。统计如下：

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	total	|	Counter	|	请求限速服务的总数	|
|	error	|	Counter	|	限速服务请求失败的总数	|
|	over_limit	|	Counter	|	限速服务的响应上限	|
|	ok	|	Counter	|	限速服务的响应下限	|
|	cx_closed	|	Counter	|	因请求超限，而关闭的总连接数	|
|	active	|	Gauge	|	限速服务活跃请求的总数	|

### 运行设置
网络速率限制过滤器支持以下运行设置：

- **ratelimit.tcp_filter_enabled**</br>
容许调用速率限制服务连接的百分比。默认为100。

- **ratelimit.tcp_filter_enforcing**</br>
调用速率限制服务并执行限速决定连接的百分比。默认为100。这可以用来测试完全速运行之前会发生什么。

## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)
