## 流量转移/分流

**注意：本文是为v1 API编写的，但这些概念也适用于v2 API。它将在未来版本的v2 API中重新描述。**

Envoy路由器支持将流量分发到两个或更多上游虚拟主机群集的路由。有两种常见的场景。

1. 版本升级：到一条路由的流量逐渐从一个集群转移到另一个集群。更详细地描述参见流量转移部分。

2. A/B测试或多重测试：同时测试两个或更多版本的相同服务。流向路由的流量必须在运行不同版本相同服务的集群之间进行拆分。更详细地描述参见流量分流部分。

### 上游主机间的流量转移

在路由运行时配置对象中，确定选择指定路由（以及它的集群）的概率。通过使用运行时配置，虚拟主机中指定路由的流量可逐渐从一个集群转移到另一个集群。参考以下配置示例，其中名为`helloworld`的服务，在Envoy配置文件中声明`helloworld_v1`和`helloworld_v2`两个版本。

```
{
  "route_config": {
    "virtual_hosts": [
      {
        "name": "helloworld",
        "domains": ["*"],
        "routes": [
          {
            "prefix": "/",
            "cluster": "helloworld_v1",
            "runtime": {
              "key": "routing.traffic_shift.helloworld",
              "default": 50
            }
          },
          {
            "prefix": "/",
            "cluster": "helloworld_v2",
          }
        ]
      }
    ]
  }
}
```

Envoy匹配策略是匹配第一个路由。如果路由具有运行时配置对象，则会根据使用运行时值进行匹配（如果没有指定值，则为默认值）。因此，通过在上面的例子中紧挨着的两条路由配置，通过在第一个路由中指定一个运行时对象，并且改变随着时间推移，修改该运行时对象的值来实现流量转移。以下是完成任务所需的大致操作顺序。

1. 在开始时，将`routing.traffic_shift.helloworld`设置为`100`，以便所有对`helloworld`服务的请求，都与v1的路由匹配，并由`helloworld_v1`集群提供服务。
2. 要开始将流量转移到`helloworld_v2`集群时，请将`routing.traffic_shift.helloworld`设置为`0<x<100`范围内。例如，设置`90`，对`helloworld`虚拟主机的每10个请求中，有1个将不匹配v1的路由，并将落入v2路由，由`helloworld_v2`集群提供服务。
3. 逐渐设置`routing.traffic_shift.helloworld`中的值变小，以便更大比例的请求匹配到v2路由。
4. 当`routing.traffic_shift.helloworld`设置为0时，对`helloworld`虚拟主机的请求将不会匹配v1路由。现在所有的流量都会流向v2路由，并由`helloworld_v2`集群提供服务。


### 多上游主机的流量分流

再次参考`helloworld`的例子，现在有三个版本（v1，v2和v3），而不是两个。 要在三个版本（即33％，33％，34％）之间平均分配流量，可以使用`weighted_clusters`选项指定每个上游群集的权重。

与前面的例子不同，一个路由条目就足够了。路由中的`weighted_clusters`配置可用于指定多个上游群集以及引导每个发送到上游群集的流量百分比权重。

```
{
  "route_config": {
    "virtual_hosts": [
      {
        "name": "helloworld",
        "domains": ["*"],
        "routes": [
          {
            "prefix": "/",
            "weighted_clusters": {
              "runtime_key_prefix" : "routing.traffic_split.helloworld",
              "clusters" : [
                { "name" : "helloworld_v1", "weight" : 33 },
                { "name" : "helloworld_v2", "weight" : 33 },
                { "name" : "helloworld_v3", "weight" : 34 }
              ]
            }
          }
        ]
      }
    ]
  }
}
```

可以使用以下运行时变量动态调整分配给每个群集的权重：</br>
`routing.traffic_split.helloworld.helloworld_v1`
`routing.traffic_split.helloworld.helloworld_v2`
`routing.traffic_split.helloworld.helloworld_v3`。



## 返回
- [上一级](../HTTPconnectionmanager.md)
- [首页目录](../../README.md)
