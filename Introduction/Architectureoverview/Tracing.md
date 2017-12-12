### 跟踪

#### 概述

分布式跟踪使开发人员可以在大型面向服务的体系结构中获得调用流的可视化。在理解序列化，并行性和延迟来源方面，这是非常宝贵的。Envoy支持系统范围与跟踪相关的三个功能：

- **请求ID生成**：Envoy将在需要时生成UUID并填充`x-request-id` HTTP头。 应用程序可以转发`x-request-id`头以进行统一日志记录以及跟踪。
- **外部跟踪服务集成**：Envoy支持可插入的外部跟踪可视化提供程序。 目前，Envoy支持[LightStep](http://lightstep.com/)，[Zipkin](http://zipkin.io/)或Zipkin兼容后端（例如[Jaeger](https://github.com/jaegertracing/)）。但是，并不支持其他调用跟踪机制。
- **客户端跟踪ID加入**：用于将不可信`x-client-trace-id`请求ID头连接到可信的内部`x-request-id`。

#### 如何启动跟踪
处理请求的HTTP连接管理器必须设置跟踪对象。有几种方法可以启动跟踪：

- 外部客户端通过`x-client-trace-id`头域字段请求。
- 通过`x-envoy-force-trace`头域字段请求内部服务。
- 通过设置随机采样进行采样。

路由过滤器还可以通过`start_child_span`选项为出站调用发起跟踪。

Envoy提供报告有关网格中服务之间通信的跟踪信息的功能。但是，为了能够关联调用流中各个代理生成的跟踪信息，服务必须在入站和出站请求之间传播特定的跟踪上下文。

无论使用哪个跟踪机制，该服务都应该传播`x-request-id`，以便使得在被调用服务日志中记录相关信息。

跟踪机制还需要支持额外的上下文记录，以便开发者更加容易理解（如：逻辑工作单元）之间的父/子关系。这可以通过在服务本身内直接使用LightStep（通过OpenTracing API）或Zipkin tracer来实现，以从入站请求中提取跟踪上下文，并将其注入到任何后续的出站请求中。这种方法还可以使服务创建额外的spans，描述在服务内部完成的工作，这在检查端到端跟踪时可能是有用的。

跟踪上下文可以由服务手动传播：

- 当使用LightStep跟踪器时，Envoy依靠该服务传播`x-ot-span-context` HTTP头，同时向其他服务发送HTTP请求。
- 当使用Zipkin示踪器时，Envoy依靠该服务来传播官方的B3 HTTP报头（`x-b3-traceid`，`x-b3-spanid`，`x-b3-parentspanid`，`x-b3-sampled`和`x-b3-flags` ）或为了方便起见，也可以传播`x-ot-span-context` HTTP头。

注意：分布式跟踪社区中正在进行工作以定义跟踪上下文传播的标准。 一旦采用了合适的方法，用于传播Zipkin跟踪上下文的非标准单头`x-ot-span-context`的使用将被替换。



#### 每个跟踪包含哪些数据
端到端跟踪由一个或多个spans组成。spans表示具有开始时间和持续时间的逻辑工作单元，并且可以包含与其关联的元数据。 Envoy生成的每个spans包含以下数据：

- 通过`--service-cluster`设置始发服务集群。
- 开始时间和请求的持续时间。
- 始发主机通过`--service-node`设置。
- 通过`x-envoy-downstream-service-cluster`头设置下游集群。
- HTTP网址。
- HTTP方法。
- HTTP响应代码。
- 跟踪系统中的特定元数据。

范围还包括一个名称（或操作），默认情况下被定义为被调用的服务的主机。但是，这可以使用路线上的装饰器来定制。该名称也可以使用`x-envoy-decorator-operation`头域字段替代。

Envoy自动发送spans跟踪收集。根据跟踪收集器的不同，使用通用信息（如全局唯一请求标识`x-request-id`（LightStep）或跟踪标识配置（Zipkin））将多个spans拼接在一起。

关于如何在Envoy中设置跟踪详细信息，请参考手册。
- [v1 API参考](../../v1APIreference/Tracing.md)
- [v2 API参考](../../v2APIreference/Bootstrap.md)

### 返回
- [架构介绍](../Architectureoverview.md)
- [简介](../../Introduction.md)
- [首页目录](../../README.md)
