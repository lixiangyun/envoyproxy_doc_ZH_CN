## Redis

Envoy可以做为Redis代理，在集群中的实例之间对命令进行分区。在这种模式下，Envoy的目标是保持可用性和分区容错的一致性。将Envoy与Redis集群进行比较时，这是重点。Envoy的缓存设计的不足够强大，这意味着它不会尝试协调不一致的数据，无法保持全局一致的群集成员关系视图。

Redis项目提供了与Redis相关的分区的全面参考。请参阅“[分区](https://redis.io/topics/partitioning)：如何在多个Redis实例之间分割数据”。

**Envoy Redis的特点**：

- [Redis协议](https://redis.io/topics/protocol)编解码
- 基于Hash散列的分区
- Ketama发行
- 详细的命令统计
- 主动和被动健康检查

**未来计划增强特性**：

- 补充时间统计
- 断路
- 请求分散命令
- 复制
- 内置重试
- 跟踪
- 哈希标记

### 配置

有关过滤器配置的详细信息，请参阅[Redis代理过滤器](../../Configurationreference/Networkfilters/Redisproxy.md)配置参考。

配置相应集群所定义的[Hash环负载平衡](../../v1APIreference/Clustermanager/Cluster.md)。

如果需要进行主动健康检查，则应该对群集使用[Redis健康检查](../../Configurationreference/Clustermanager/Healthchecking.md)配置。

如果需要被动健康检查，还要配置[异常检测](../../v1APIreference/Clustermanager/Cluster.md)。

为了进行被动健康检查，将超时，命令超时和连接关闭映射连接到5xx。来自Redis的所有其他响应被视为成功。


### 支持的命令
在协议级别，支持管道。不是MULTI（事务块）。尽可能使用流水线来获得最佳性能。

在命令级别，Envoy仅支持可靠地散列到服务器的命令。因此，所有支持的命令都包含一个key。受支持的命令在功能上与原始Redis命令相同，除非可能出现故障。

有关每个命令用法的详细信息，请参阅官方的[Redis命令参考](https://redis.io/commands)。

|	命令	|	组	|
|	:-------	|	:-------	|
|	DEL	|	Generic	|
|	DUMP	|	Generic	|
|	EXISTS	|	Generic	|
|	EXPIRE	|	Generic	|
|	EXPIREAT	|	Generic	|
|	PERSIST	|	Generic	|
|	PEXPIRE	|	Generic	|
|	PEXPIREAT	|	Generic	|
|	PTTL	|	Generic	|
|	RESTORE	|	Generic	|
|	TOUCH	|	Generic	|
|	TTL	|	Generic	|
|	TYPE	|	Generic	|
|	UNLINK	|	Generic	|
|	GEOADD	|	Geo	|
|	GEODIST	|	Geo	|
|	GEOHASH	|	Geo	|
|	GEOPOS	|	Geo	|
|	HDEL	|	Hash	|
|	HEXISTS	|	Hash	|
|	HGET	|	Hash	|
|	HGETALL	|	Hash	|
|	HINCRBY	|	Hash	|
|	HINCRBYFLOAT	|	Hash	|
|	HKEYS	|	Hash	|
|	HLEN	|	Hash	|
|	HMGET	|	Hash	|
|	HMSET	|	Hash	|
|	HSCAN	|	Hash	|
|	HSET	|	Hash	|
|	HSETNX	|	Hash	|
|	HSTRLEN	|	Hash	|
|	HVALS	|	Hash	|
|	LINDEX	|	List	|
|	LINSERT	|	List	|
|	LLEN	|	List	|
|	LPOP	|	List	|
|	LPUSH	|	List	|
|	LPUSHX	|	List	|
|	LRANGE	|	List	|
|	LREM	|	List	|
|	LSET	|	List	|
|	LTRIM	|	List	|
|	RPOP	|	List	|
|	RPUSH	|	List	|
|	RPUSHX	|	List	|
|	EVAL	|	Scripting	|
|	EVALSHA	|	Scripting	|
|	SADD	|	Set	|
|	SCARD	|	Set	|
|	SISMEMBER	|	Set	|
|	SMEMBERS	|	Set	|
|	SPOP	|	Set	|
|	SRANDMEMBER	|	Set	|
|	SREM	|	Set	|
|	SSCAN	|	Set	|
|	ZADD	|	Sorted Set	|
|	ZCARD	|	Sorted Set	|
|	ZCOUNT	|	Sorted Set	|
|	ZINCRBY	|	Sorted Set	|
|	ZLEXCOUNT	|	Sorted Set	|
|	ZRANGE	|	Sorted Set	|
|	ZRANGEBYLEX	|	Sorted Set	|
|	ZRANGEBYSCORE	|	Sorted Set	|
|	ZRANK	|	Sorted Set	|
|	ZREM	|	Sorted Set	|
|	ZREMRANGEBYLEX	|	Sorted Set	|
|	ZREMRANGEBYRANK	|	Sorted Set	|
|	ZREMRANGEBYSCORE	|	Sorted Set	|
|	ZREVRANGE	|	Sorted Set	|
|	ZREVRANGEBYLEX	|	Sorted Set	|
|	ZREVRANGEBYSCORE	|	Sorted Set	|
|	ZREVRANK	|	Sorted Set	|
|	ZSCAN	|	Sorted Set	|
|	ZSCORE	|	Sorted Set	|
|	APPEND	|	String	|
|	BITCOUNT	|	String	|
|	BITFIELD	|	String	|
|	BITPOS	|	String	|
|	DECR	|	String	|
|	DECRBY	|	String	|
|	GET	|	String	|
|	GETBIT	|	String	|
|	GETRANGE	|	String	|
|	GETSET	|	String	|
|	INCR	|	String	|
|	INCRBY	|	String	|
|	INCRBYFLOAT	|	String	|
|	MGET	|	String	|
|	MSET	|	String	|
|	PSETEX	|	String	|
|	SET	|	String	|
|	SETBIT	|	String	|
|	SETEX	|	String	|
|	SETNX	|	String	|
|	SETRANGE	|	String	|
|	STRLEN	|	String	|

### 失败模型

如果Redis抛出一个错误，我们把这个错误作为响应传递给这个命令。 Envoy将错误数据类型的Redis响应视为正常响应，并将其传递给调用者。

Envoy也可以产生自己的错误来回应客户。

|	错误	|	含义	|
|	:-------	|	:-------	|
|	no upstream host	|	哈希环负载均衡器在为Key选择的环位置上没有可用的主机。	|
|	upstream failure	|	后端在超时期限内没有响应或关闭连接。	|
|	invalid request	|	命令由于数据类型或长度而被命令拆分器的第一阶段拒绝。	|
|	unsupported command	|	该命令不被Envoy识别，因此不能被服务，因为它不能被散列到后端服务器。	|
|	finished with n errors	|	对响应进行求和的分段命令（例如，DEL）将返回接收到的错误的总数。	|
|	upstream protocol error	|	碎片命令接收到意外的数据类型或后端以不符合Redis协议的响应进行响应。	|
|	wrong number of arguments for command	|	特定的命令检查Envoy参数的数量是正确的。	|


在MGET的情况下，无法提取单独Key所产生错误响应。例如，如果我们获取五个Keys和两个Keys的后端超时，我们会得到一个错误的响应代替每个值。

```
$ redis-cli MGET a b c d e
1) "alpha"
2) "bravo"
3) (error) upstream failure
4) (error) upstream failure
5) "echo"
```

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)
