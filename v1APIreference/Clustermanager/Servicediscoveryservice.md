## 服务发现服务(SDS)
服务发现服务[架构概述]。

```
{
  "cluster": "{...}",
  "refresh_delay_ms": "{...}"
}
```

- **cluster**<br />
	(required, object) 承载服务发现服务的上游群集的标准定义。该群集必须实现和运行SDS HTTP API的REST服务。

- **refresh_delay_ms**<br />
	(required, integer) 每次访问SDS群集的API延迟（以毫秒为单位）。Envoy将在`0~refresh_delay_ms`之间，添加一个额外的随机抖动。因此，最长可能的延迟是`2*refresh_delay_ms`。

### REST API

Envoy希望服务发现服务提供一下API（请参阅Lyft的[参考实现](https://github.com/lyft/discovery)）：

```
GET /v1/registration/(string: service_name)
```

请求发现服务返回指定`service_name`的所有主机。`service_name`对应于群集参数`service_name`。使用以下JSON格式响应：

```
{
  "hosts": []
}
```

- **hosts**<br />
	(required, array) 组成该服务的主机列表。

### Host(JSON)

```
{
  "ip_address": "...",
  "port": "...",
  "tags": {
    "az": "...",
    "canary": "...",
    "load_balancing_weight": "..."
  }
}
```
- **ip_address**<br />
	(required, string) 上游主机的IP地址。

- **port**<br />
	(required, integer) 上游主机的端口。

- **az**<br />
	(optional, string) 可选的上游主机的区域。Envoy使用该区域记录相应的各种统计和负载平衡任务。

- **canary**<br />
	(optional, boolean) 可选的上游主机金丝雀（灰度发布）状态。 Envoy将Canary状态用于相应的各种统计和负载平衡任务。

- **load_balancing_weight**<br />
	(optional, integer) 可选的上游主机负载平衡权重，范围为1-100. Envoy在某些内置负载平衡器中会使用到负载平衡权重。


## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)
