## V1 API概述

**注意：当前V1配置被认为是历史遗留的。它将在未来Envoy的版本中被弃用，并最终完全删除。如果您是Envoy的新手，强烈建议从[V2配置API](Overviewv2API.md)开始。**

Envoy配置格式以JSON编写，并针对JSON配置进行校验。配置模式可以在[source/common/json/config_schemas.cc](https://github.com/envoyproxy/envoy/blob/master/source/common/json/config_schemas.cc)中找到。服务器的主要配置包含在监听器和集群管理器部分。其他配置项指定其他配置。

支持YAML提供便利的手写配置。如果配置文件路径以.yaml结尾，Envoy在内部将YAML转换为JSON。在剩下的配置文档中，我们只提供JSON的配置描述。Envoy期望明确一下YAML标量，如一个集群名称（应该是一个字符串）被置为真，它应该在YAML配置中被写为“true”。同样也适用于整数值和浮点值（例如1、1.0和“1.0”）。

```
{
  "listeners": [],
  "lds": "{...}",
  "admin": "{...}",
  "cluster_manager": "{...}",
  "flags_path": "...",
  "statsd_udp_ip_address": "...",
  "statsd_tcp_cluster_name": "...",
  "stats_flush_interval_ms": "...",
  "watchdog_miss_timeout_ms": "...",
  "watchdog_megamiss_timeout_ms": "...",
  "watchdog_kill_timeout_ms": "...",
  "watchdog_multikill_timeout_ms": "...",
  "tracing": "{...}",
  "rate_limit_service": "{...}",
  "runtime": "{...}",
}
```


**listeners（必选，数组）**</br>
需要由服务实例化的监听器数组。一个Envoy进程可以包含任意数量的监听器。

**lds（可选，对象）**</br>
监听发现服务（LDS）的配置。如果未指定，则只加载静态监听器。

**admin（必选，对象）**</br>
本地管理HTTP服务器的配置。

**cluster_manager（必选，对象）**</br>
服务内所拥有的所有上游群集的群集管理器配置。

**flags_path（可选，字符串）**</br>
启动在文件系统路径下搜索文件的标志。

**statsd_udp_ip_address（可选，字符串）**</br>
符合`statsd`正在运行的UDP监听地址。如果指定了，则统计数据将会刷新到这个地址上。IPv4地址格式`host:port`（例如：`127.0.0.1:855`）。IPv6地址的格式`[host]:port`（例如`[::1]:855`）。

**statsd_tcp_cluster_name（可选，字符串）**</br>
符合TCP的`statsd`集群管理集群的名称。如果指定，Envoy将连接到此群集以刷新统计信息。

**stats_flush_interval_ms（可选，整数）**</br>
配置刷新统计信息时间（以毫秒为单位）。出于性能方面的原因，Envoy锁定计数器，并且只是周期性地刷新计数器和计量器。如果未指定，则默认值为5000毫秒（5秒）。

**watchdog_miss_timeout_ms（可选，整数）**</br>
Envoy统计“server.watchdog_miss”统计信息中的线程无响应的时间（以毫秒为单位）。如果没有指定，默认是200ms。

**watchdog_megamiss_timeout_ms（可选，整数）**</br>
Envoy统计“server.watchdog_mega_miss”统计信息中的线程无响应的时间（以毫秒为单位）。如果未指定，则默认值为1000毫秒。

**watchdog_kill_timeout_ms（可选，整数）**</br>
监视一个线程在这个毫秒内没有响应，假定因BUG导致导致kill整个Envoy进程。设置为0表示禁用kill行为。如果未指定，则默认值为0（禁用）。

**watchdog_multikill_timeout_ms（可选，整数）**</br>
如果至少有两个监视的线程至少在这个毫秒内没有响应，假定因一个真正的死锁导致kill整个Envoy进程。设置为0表示禁用kill行为。如果未指定，则默认值为0（禁用）。

**tracing（可选，对象）**</br>
外部跟踪提供程序的配置。如果没有指定，则不会执行跟踪。

**rate_limit_service（可选，对象）**</br>
配置外部限速服务提供商。如果没有指定，任何调用速率限制服务将立即返回成功。

**runtime（可选，对象）**</br>
运行时配置提供程序的配置。如果未指定，将使用“null”提供程序，这将导致使用所有默认值。


## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
