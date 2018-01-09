### 环哈希负载均衡配置

当[集群管理器](../Cluster.md)中的`lb_type`设置为`ring_hash`时，将使用环哈希负载平衡策略。

```
{
  "minimum_ring_size": "...",
  "use_std_hash": "..."
}
```

- **minimum_ring_size**<br />
	(optional, integer) 最小哈希环大小，即虚拟节点总数。尺寸更大可以提供更好的请求分布，因为群集中的每个主机将具有更多的虚拟节点。默认为1024。若主机总数大于最小值的情况下，每个主机将被分配一个虚拟节点。

- **use_std_hash**<br />
	(optional, boolean) 默认为`true`，这意味着`std::hash`用于将主机散列到`ketama`环上。`std::hash`可能因平台而异。为此，Envoy默认最终会使用xxHash。该字段用于迁移目的，最终将被弃用。现在将其设置为`false`以使用`xxHash`。


## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)
