## 服务发现

### LbEndpoint
[LbEndpoint proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/eds.proto#L62)

EndPoint是指Envoy可以将流量路由到的端口。

```
{
  "endpoint": "{...}",
  "metadata": "{...}",
  "load_balancing_weight": "{...}"
}
```

- **endpoint**<br />
	([Endpoint](../v2APIreference/Commontypes.md)) 上游主机标识符

- **metadata**<br />
	([Metadata](../v2APIreference/Commontypes.md)) 负载均衡端口元数据是指可用于为请求选择群集中的端口。过滤器名称应该指定为`envoy.lb`。一个bool类型的键值对例子为`canary`，提供上游主机的可选`canary`状态。这可以在路由的`ForwardAction` `metadata_match`字段中匹配，以在集群负载平衡中考虑的端口子集。

- **load_balancing_weight**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 上游主机的可选负载平衡权重，范围为1~128. 在某些内置Envoy负载平衡器中，会使用负载平衡权重。端口的负载平衡权重除以本地所有端口权重的总和，以获得端口的所占通信量百分比。然后这个百分比再由来自`LocalityLbEndpoints`的端点的本地负载平衡权重加权。如果没有指定，每个主机被假定在一个地方具有相同的权重。

    注意：当前128的限制是有些随意，但是考虑当前实现的性能问题而使用，若解决这个问题是可以取消这个限制。

### LocalityLbEndpoints
[LocalityLbEndpoints proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/eds.proto#L100)

一组端口归属一个局部负载均衡器。一个本地可以有多个局部负载均衡器，通常只有在不同的组之间需要负载平衡权重或不同的优先级时，才会使用多个组。

```
{
  "locality": "{...}",
  "lb_endpoints": [],
  "load_balancing_weight": "{...}",
  "priority": "..."
}
```

- **locality**<br />
	([Locality](../v2APIreference/Commontypes.md)) 标识上游主机运行的位置。

- **lb_endpoints**<br />
	([LbEndpoint](#lbendpoint)) 所属的端口组配置。

- **load_balancing_weight**<br />
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) 可选：配置每个端口组的优先级、`region`、`zone`、`sub_zone`权重，范围1~128。一个端口组的负载均衡权重除以相同优先级的所有权重之和，获得该端口组的承载业务有效百分比。

     必须为相同优先级的所有局部端口组指定权重，若不指定，则认为每个局部在群集中具有相同的权重。

     注意：当前128的限制是有些随意，但是考虑当前实现的性能问题而使用，若解决这个问题是可以取消这个限制。

- **priority**<br />
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 可选：此局部负载均衡端口组的优先级。如果未指定，则默认为最高优先级（0）。
    
    一般情况下，Envoy只会选择最高优先级（0）的端口。如果该优先级的所有端口不可用/不健康，Envoy将快速故障恢复至下一个最高优先级的端口组。

     优先级应该配置为从0（最高）到N（最低），中间没有间隔。

### ClusterLoadAssignment
[ClusterLoadAssignment proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/eds.proto#L244)

从RDS中获取的路由，映射到单个集群或者使用RDS集群权重实现跨集群的流量拆分。

使用EDS，每个集群都有独立的LB进行处理，将在集群内的产生局部LB，即在一个局部主机之间具有更细粒度的LB。对于给定的集群，主机的有效权重是其`load_balancing_weight`乘以其局部的LB权重`load_balancing_weight`。

```
{
  "cluster_name": "...",
  "endpoints": [],
  "policy": "{...}"
}
```

- **cluster_name**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 集群的名称。如果在集群[EdsClusterConfig](../v2APIreference/ClustersandCDS.md)中指定，则是`service_name`值。

- **endpoints**<br />
	([LocalityLbEndpoints](#localitylbendpoints)) 对应的局部端口组列表。

- **policy**<br />
	([ClusterLoadAssignment.Policy](#clusterloadassignmentpolicy)) 负载均衡策略设置。

### ClusterLoadAssignment.Policy
[ClusterLoadAssignment.Policy proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/eds.proto#L255)

负载平衡策略设置。

```
{
  "drop_overload": "..."
}
```

- **drop_overload**<br />
	([double](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 应该丢弃的流量（0-100）的百分比。如果上游主机无法从down机中恢复，或者无法进行自动调整，则需要对上游主机进行保护，因此需要修整整个传入流量以保护它们。

    注意：v2 API与v1 API存在差异，在v1中被称为维护模式。

## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)

