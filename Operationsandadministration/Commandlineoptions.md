## 命令行选项

Envoy由JSON配置文件以及一组命令行选项驱动。以下是Envoy支持的命令行选项。

- **-c \<path string>, --config-path \<path string>**</br>
    （必选）指向v1或v2 [JSON/YAML/proto3配置文件]((../../Configurationreference/Overviewv2API.md))的路径。若设置`-v2-config-only`选项，则将被解析为一个v2引导配置文件，如果是v1 JSON配置文件，则返回失败。对于v2配置文件，有效的扩展名是`.json`，`.yaml`，`.pb`和`.pb_text`，分别表示JSON，YAML，二进制proto3和文本proto3格式。

- **--v2-config-only**</br>
    （可选）该标志表示配置文件是否仅为v2引导配置文件。如果为`false`（默认值），那么当v2引导配置解析失败时，将尝试解析为v1 JSON配置文件。

- **--mode \<string>**</br>
    （可选）Envoy的其中一种操作模式：</br>
    
   serve：(默认）验证JSON配置，然后正常提供流量。</br>
   validate：验证JSON配置，然后退出，打印“OK”消息（在这种情况下退出代码为`0`）或者因配置文件的任何错误（退出代码为`1`）。不会产生网络流量，并且不会执行热重启，所以不会影响其他任何进程。

- **--admin-address-path \<path string>**</br>
    （可选）将输出管理员地址和端口的文件路径。

- **--local-address-ip-version \<string>**</br>
    （可选）用于填充服务器本地IP地址的IP地址版本。此参数影响各种标题，包括附加到`X-Forwarded-For`（XFF）头部的内容。选项是`v4`或`v6`。默认是`v4`。

- **--base-id \<integer>**</br>
    （可选）分配共享内存区域时使用的基本ID。Envoy在[热启动](../Introduction/Architectureoverview/Hotrestart.md)期间使用共享内存区域。大多数用户不需要设置这个选项。但是，如果Envoy需要在同一台计算机上多次运行，则每个运行的Envoy都需要一个唯一的基本ID，以便共享内存区域不会发生冲突。

- **--concurrency \<integer>**</br>
    （可选）要运行的[工作线程数](../Introduction/Architectureoverview/Threadingmodel.md)。如果未指定，则默认为机器上的硬线程数。

- **-l \<string>, --log-level \<string>**</br>
    （可选）日志级别。非开发者通常不应该设置这个选项。有关可用的日志级别和默认值，请参阅[帮助文档]()。

- **--log-path \<path string>**</br>
    （可选）日志输出的文件路径。当`SIGUSR1`被处理时，文件会被重新打开。如果没有设置，记录到`stderr`。

- **--restart-epoch \<integer>**</br>
    （可选）热重启期间。（Envoy重新启动的次数，而不是重新开始）。第一次启动时默认为0。此选项告诉Envoy是否尝试创建热重启所需的共享内存区域，或者是否打开现有的共享内存区域。每次热重启时都应该增加这个值。通常在大多数情况下，应该设置此`RESTART_EPOCH`环境变量。

- **--hot-restart-version**</br>
    （可选）为当前的二进制输出一个热重启兼容的版本。这可以与[GET /hot_restart_version](../Operationsandadministration/Administrationinterface.md)管理端口的输出相匹配，以确定新的二进制文件和正在运行的二进制文件是否热重启兼容。

- **--service-cluster \<string>**</br>
    （可选）定义Envoy运行的本地服务群集名称。尽管是可选的，但是如果使用以下任何特性，应该设置：[statsd](../Introduction/Architectureoverview/Statistics.md)，[健康检查集群验证](../v1APIreference/Clustermanager/Cluster/Healthchecking.md)，[运行时配置目录覆盖](../v1APIreference/Runtime.md)，[添加用户代理](../v1APIreference/Networkfilters/HTTPconnectionmanager.md)，[HTTP全局速率限制](../Configurationreference/HTTPfilters/Ratelimit.md)，[CDS](../Configurationreference/Clustermanager/Clusterdiscoveryservice.md)和[HTTP跟踪](../Introduction/Architectureoverview/Tracing.md)。

- **--service-node \<string>**</br>
    （可选）定义Envoy运行的本地服务节点名称。虽然是可选的，但是如果使用以下任何功能，应该设置它们：[statsd](../Introduction/Architectureoverview/Statistics.md)，[CDS](../Configurationreference/Clustermanager/Clusterdiscoveryservice.md)和[HTTP跟踪](../Introduction/Architectureoverview/Tracing.md)。

- **--service-zone \<string>**</br>
    （可选）定义Envoy运行的本地服务区域。尽管是可选的，但是如果使用路由发现服务并且发现服务暴露[区域信息](../v1APIreference/Clustermanager/Servicediscoveryservice.md)，则应该设置它。

- **--file-flush-interval-msec \<integer>**</br>
    （可选）文件刷新间隔，以毫秒为单位。默认为10秒。在创建文件时使用此设置来确定缓冲区刷新到文件之间的持续时间。缓冲区满或每隔一段时间刷新一次（以先到者为准）。为了获得更多（或更少）的即时刷新，调整此设置对跟踪访问日志非常有用。

- **--drain-time-s \<integer>**</br>
    （可选）Envoy在热重启期间将耗尽连接的时间（秒）。请参阅热重启概述了解[更多](../Introduction/Architectureoverview/Hotrestart.md)信息。默认为600秒（10分钟）。通常，逐出时间应小于通过`--parent-shutdown-time-s`选项设置的父进程关闭时间。如何配置这两个设置取决于具体的部署。在边缘情况下，可能需要耗费很长时间。在服务场景中，可能使逐出和关闭时间缩短得多（例如，60s/90s）。

- **--parent-shutdown-time-s \<integer>**</br>
    （可选）Envoy在热重启期间关闭父进程之前等待的时间（秒）。请参阅热启动概述了解[更多](../Introduction/Architectureoverview/Hotrestart.md)信息。默认为900秒（15分钟）。

- **--max-obj-name-len \<uint64_t>**</br>
    （可选）`cluster/route_config/listener`中名称字段的最大长度（以字节为单位）。此选项通常用于群集名称自动生成的场景，通常超过会60个字符的内部限制。默认为60。</br>
     **注意：此设置会影响`--hot-restart-version`的输出。如果您开始使用此选项，并设置为非默认值，则应该使用相同的值配置到热重启的新进程。**

- **--max-stats \<uint64_t>**</br>
    （可选）热重启之间可以共享的最大统计数量。此设置会影响`--hot-restart-version`的输出; 必须使用相同的值来配置热重启进程。默认为16384。

## 返回
- [上一级](../Operationsandadministration.md)
- [首页目录](../README.md)
