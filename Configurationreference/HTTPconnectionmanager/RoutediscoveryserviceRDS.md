## 路由发现服务（RDS）

路由发现服务（RDS）的API在Envoy里面是一个可选API，用于动态获取路由配置。路由配置包括HTTP头部修改，虚拟主机以及每个虚拟主机中包含的单个路由规则。每个[HTTP连接管理器](../../Configurationreference/HTTPconnectionmanager.md)都可以通过API独立地获取自己的[路由配置](../../v1APIreference/HTTPRouteconfiguration.md)。

- [v1 API 参考](../../v1APIreference/HTTPRouteconfiguration/RoutediscoveryserviceRDS.md)
- [v2 API 参考](../../Configurationreference/Overviewv2API.md)

### 统计
RDS的统计树以 `http.<stat_prefix>.rds.<route_config_name>..`为根，`route_config_name`名称中的任何`:`字符在统计树中被替换为`_`。统计树包含以下统计信息：


|	名称	|	类型	|	描述	|
|	 ------------------------------------------	|	 ------------------------------------------	|	 ------------------------------------------	|
|	config_reload	|	Counter	|	加载配置不同导致重新调用API的总次数	|
|	update_attempt	|	Counter	|	调用API获取资源重试总数	|
|	update_success	|	Counter	|	调用API获取资源成功总数	|
|	update_failure	|	Counter	|	调用API获取资源失败总数（因网络、句法错误）	|
|	version	|	Gauge	|	最后一次API获取资源成功的内容HASH	|




## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)
