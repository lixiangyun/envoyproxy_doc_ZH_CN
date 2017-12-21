## 缓存

缓冲区过滤器用于并等待并缓冲的完整请求。这在特殊场景下会很有用，如：保护一些应用程序，不必关心和处理部分请求，及高网络延迟。

- [v1 API 参考](../../v1APIreference/HTTPfilters/Buffer.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Buffer.md)

### 统计
缓冲过滤器输出统计信息以`http.<stat_prefix>.buffer.`命名空间。 `stat_prefix`来自拥有的HTTP连接管理器。

|	名称	|	类型	|	描述	|
|	 -----------	|	 -------------------	|	 ---------------	|
|	rq_timeout	|	Counter	|	等待完整请求超时的请求总数	|

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)

