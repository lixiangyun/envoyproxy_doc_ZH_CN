## 业界对比

总的来说，我们相信Envoy为现代服务导向架构提供了独特且引人注目的功能。下面我们比较一下Envoy和其他相关的系统。尽管在任何特定的领域（边缘代理，软件负载平衡器，服务消息传递层），Envoy可能不像下面的一些解决方案那样具有丰富的功能，但总体而言，没有其他解决方案将相同的整体特征提供到单个自包含的高性能套餐。

注：以下大部分项目也都正在积极开发中。因此，一些信息可能会过时。如果是这种情况，请让我们知道，我们会解决它。

### [nginx](https://nginx.org/en/)
nginx是规范的现代Web服务器。它支持静态内容服务，HTTP L7反向代理和负载均衡，HTTP/2和许多其他功能。尽管我们认为大多数现代的面向服务的体系结构通常不使用它们，但nginx比Envoy更具有整体特性作为前端反向代理。与nginx相比，Envoy提供了以下主要优势作为前端代理：

- 完整的HTTP/2透明代理。Envoy支持下行和上行通信的HTTP/2。nginx仅支持HTTP/2用于下游连接。
- 自由提供高级负载均衡。只有nginx plus（付费服务器）支持与Envoy类似的高级负载均衡功能。
- 能够在前端以及每个服务节点上运行相同的软件。许多基础设施运行nginx和haproxy的混合。从操作的角度来看，每一跳的单一代理解决方案都要简单得多。

### [HAProxy](http://www.haproxy.org/)

haproxy是规范的现代负载平衡器软件。它也支持基本的HTTP反向代理功能。作为负载均衡器，Envoy提供了比haproxy更多的主要优势：

- HTTP/2支持。
- 可插拔的架构。
- 多线程架构。当每台机器部署单个进程而不是多个进程时，操作和配置电路中断设置要容易得多。
- 与远程服务发现服务集成。
- 与远程全球限速服务集成。
- 能够热重启。
- 更详细的统计。

### [AWS ELB](https://aws.amazon.com/elasticloadbalancing/)
Amazon的ELB是用于EC2应用程序服务发现和负载平衡的标准解决方案。Envoy相比ELB作为负载平衡器和服务发现系统，主要提供了以下优势：

- 统计和日志记录（CloudWatch统计信息滞后，并且极其缺乏细节，日志必须从S3中检索，并具有固定格式）。
- 稳定性（使用ELB时偶尔会出现不稳定情况，最终无法调试）。
- 先进的负载平衡，并且与节点之间的直接连接。Envoy网格通过硬件的可伸缩性，避免了额外的网络跳跃。负载平衡器可以做出更好的决策，并根据区域，灰度状态等收集更有价值的统计信息。负载平衡器还支持诸如重试等高级功能。

AWS最近发布了应用程序负载均衡器产品。该产品将支持HTTP/2以及基本HTTP L7请求路由，并添加到多个后端群集。与Envoy相比，这个功能还是很小的，性能和稳定性是未知的，但显然AWS将来会继续在这个领域进行投资。

### [SmartStack](http://nerds.airbnb.com/smartstack-service-discovery-cloud/)
SmartStack是一个有趣的解决方案，它提供了haproxy之上，额外的支持了服务发现和健康检查。在高层次上，SmartStack与Envoy（外置进程架构，应用程序平台不感知等）具有大部分相同的目标。作为负载平衡器和服务发现软件包，Envoy相对于SmartStack提供了以下主要优势：

- 与haproxy相比，包括前面提到的所有优点。
- 集成服务发现和主动健康检查。Envoy在一个单一的高性能组件中提供了一切。

### [Finagle](https://twitter.github.io/finagle/)
Finagle是Twitter的Scala/JVM服务，用于服务通信库。Twitter和许多其他公司主要使用基于JVM的体系架构。它具有许多与Envoy相同的功能，例如服务发现，负载平衡，过滤器等。与Finagle相比，Envoy作为负载平衡器和服务发现软件包提供了以下主要优势：

- 通过分布式主动健康检查实现最终一致的服务发现。
- 所有度量标准（内存消耗，CPU使用率和P99延迟属性）的性能级别都较好。
- 外置进程和应用程序平台不感知的架构。Envoy可以与任何应用程序工作。

### [proxygen](https://github.com/facebook/proxygen)和[wangle](https://github.com/facebook/wangle)
proxygen是Facebook的高性能C++11 HTTP代理库，写在一个叫做wangle的Finagle之类的C++库之上。从代码角度来看，Envoy使用与proxygen大部分相同的技术来获得作为HTTP 库/代理的高性能。除此之外，这两个项目并没有真正的可比性，因为Envoy是一个完整的包含大型功能的独立服务，而不是每个项目都需要单独构建的库。

### [gRPC](http://www.grpc.io/)
gRPC是一种新的多平台消息传递系统。它使用IDL来描述RPC库，然后为各种不同的语言实现特定于应用程序的运行时。底层传输是HTTP/2。尽管gRPC可能有将来实现许多类似于Envoy的特性（负载平衡等）的目标，但是到目前为止，各种运行时并不成熟，主要关注于序列化/反序列化。我们认为gRPC和Envoy是合作伙伴，并非竞争对手。这里描述了Envoy如何与gRPC集成。

### [linkerd](https://github.com/BuoyantIO/linkerd)
linkerd是一个基于Netty和Finagle（Scala/JVM）的独立开源RPC路由代理。linkerd提供了许多Finagle的功能，包括延迟感知负载平衡，连接池，断路，重试阈值，截止期限，跟踪，细粒度检测以及用于请求级路由的流量路由层。linkerd提供了一个可插拔的服务发现接口（标准支持Consul和ZooKeeper以及Marathon和KubernetesAPI）。

linkerd的内存和CPU要求明显高于Envoy的。与Envoy相比，linkerd提供了极简配置语言，并且明确地不支持热重启，而是依赖于动态配置和服务抽象。linkerd支持HTTP/1.1，Thrift，ThriftMux，HTTP/2（试验阶段）和gRPC（试验阶段）。


### [nghttp2](https://nghttp2.org/)
nghttp2是一个包含几个不同东西的项目。它主要包含一个实现HTTP/2协议的库（nghttp2）。Envoy使用这个库（在顶层简单封装）来支持HTTP/2。该项目还包含一个非常有用的负载测试工具（h2load）以及一个反向代理（nghttpx）。从比较的角度来看，Envoy与nghttpx最为相似。nghttpx是一个透明的HTTP/1、HTTP/2反向代理，支持TLS终止代理，正确支持gRPC代理以及其他各种功能。有了这个说法，我们认为nghttpx是各种代理功能的一个很好的例子，而不是一个强大的生产就绪解决方案。Envoy的重点更多地集中在可观察性，一般操作敏捷性和高级负载平衡功能上。

## 返回
- [简介](../Introduction.md)
- [首页目录](../README.md)
