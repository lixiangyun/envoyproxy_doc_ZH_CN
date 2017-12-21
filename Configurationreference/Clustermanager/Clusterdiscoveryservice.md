## 集群发现服务

群集发现服务（CDS）是一个可选的API，Envoy将调用该API来动态获取群集管理器成员。 Envoy还将根据API响应协调集群管理，根据需要完成添加，修改或删除已知的群集。

- [v1 CDS API](../../v1APIreference/Clustermanager/Clusterdiscoveryservice.md)
- [v2 CDS API](../../Configurationreference/Overviewv2API.md)

### 统计
CDS的统计树以`cluster_manager.cds.`为根，统计如下：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	config_reload	|	Counter	|	因配置不同而导致配置重新加载的总次数	|
|	update_attempt	|	Counter	|	尝试调用配置加载API的总次数	|
|	update_success	|	Counter	|	调用配置加载API成功的总次数	|
|	update_failure	|	Counter	|	调用配置加载API失败的总次数（网络或参数错误）	|
|	version	|	Gauge	|	来自上次成功调用配置加载API的内容哈希	|

## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)
