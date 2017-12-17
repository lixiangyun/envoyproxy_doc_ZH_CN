## 路由

路由过滤器实现了HTTP转发。几乎所有Envoy部署的HTTP代理场景都有使用。过滤器的主要工作是遵循路由表中配置的指令。除了转发和重定向之外，过滤器还处理重试，统计等。

- [v1 API 参考](../../v1APIreference/HTTPfilters/Router.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Router.md)

### HTTP头部
路由器在出口/请求以及入口/响应路径上消费和设置各种HTTP头部。它们在本章节中有说明。

- [x-envoy-expected-rq-timeout-ms](#x-envoy-expected-rq-timeout-ms)
- [x-envoy-max-retries](#x-envoy-max-retries)
- [x-envoy-retry-on](#x-envoy-retry-on)
- [x-envoy-retry-grpc-on](#x-envoy-retry-grpc-on)
- [x-envoy-upstream-alt-stat-name](#x-envoy-upstream-alt-stat-name)
- [x-envoy-upstream-canary](#x-envoy-upstream-canary)
- [x-envoy-upstream-rq-timeout-alt-response](#x-envoy-upstream-rq-timeout-alt-response)
- [x-envoy-upstream-rq-timeout-ms](#)
- [x-envoy-upstream-rq-per-try-timeout-ms](#x-envoy-upstream-rq-timeout-ms)
- [x-envoy-upstream-service-time](#x-envoy-upstream-service-time)
- [x-envoy-original-path](#x-envoy-original-path)
- [x-envoy-immediate-health-check-fail](#x-envoy-immediate-health-check-fail)
- [x-envoy-overloaded](#x-envoy-overloaded)
- [x-envoy-decorator-operation](#x-envoy-decorator-operation)

#### x-envoy-expected-rq-timeout-ms

这是路由器期望请求完成的时间（以毫秒为单位）。Envoy设置这个HTTP头部，以便上游主机在接收请求后可以根据请求超时做出决定，例如提前退出。这是在内部请求上设置的，可以从[x-envoy-upstream-rq-timeout-ms](#x-envoy-upstream-rq-timeout-ms)头部或[路由超时](../../v1APIreference/HTTPRouteconfiguration/Route.md)中按顺序获取。

#### x-envoy-max-retries
如果有[重试策略](../../v1APIreference/HTTPRouteconfiguration/Route.md)，Envoy将默认重试一次，除非明确指定。可以在[路由重试配置](../../v1APIreference/HTTPRouteconfiguration/Route.md)或通过使用此头部显式设置重试次数。如果未配置重试策略，并且未指定[x-envoy-retry-on](#x-envoy-retry-on)或[x-envoy-retry-grpc-on](#x-envoy-retry-grpc-on)头，则Envoy将不会重试失败的请求。

关于Envoy如何重试的一些说明：

- 路由超时（通过[x-envoy-upstream-rq-timeout-ms](#x-envoy-upstream-rq-timeout-ms)或[路由配置设置](../../v1APIreference/HTTPRouteconfiguration/Route.md)）包括所有重试。 因此，如果请求超时设置为3秒，并且第一次请求尝试需要2.7秒，则重试（包括退避）只有0.3s时间完成。这是设计来避免因重试/超时导致指数增长。
- Envoy使用完全抖动的指数退避算法进行重试，其基准时间为25ms。第一次重试将在0-24ms之间随机延迟，第二次在0-74ms之间，第三次在0-174ms之间，依此类推。
- 如果最大重试次数在头部和路由配置都有设置，则在会采用最大值用于请求的最大重试次数。

#### x-envoy-retry-on

在出口请求上设置此报头将导致Envoy尝试重试失败的请求（重试次数默认为1，可以通过[x-envoy-max-retries](#x-envoy-max-retries)头部或路由配置重试策略进行控制）。设置[x-envoy-retry-on](#x-envoy-retry-on)头的值表示重试策略。可以使用','分隔列表来指定一个或多个策略。支持的策略有：

- **5xx**</br>
   如果上游服务器响应任何5xx的响应代码，或者根本没有响应（断开/重置/读取超时），Envoy将尝试重试请求。（包括连接失败和拒绝流）

   注：当请求超过[x-envoy-upstream-rq-timeout-ms](#x-envoy-upstream-rq-timeout-ms)（导致504误错代码）时，Envoy将不会重试。如果您想在个别请求上尝试超长时间的重试，请使用[x-envoy-upstream-rq-per-try-timeout-ms](#x-envoy-upstream-rq-per-try-timeout-ms)。`x-envoy-upstream-rq-timeout-ms`是请求的外部时间限制，包括发生的任何重试。

- **connect-failure**</br>
   如果由于上游服务器连接失败而导致请求失败（连接超时等），Envoy将尝试重试。（包含在5xx中）

   注：连接失败/超时是TCP级别，而不是请求级别。这不包括通过[x-envoy-upstream-rq-timeout-ms](#x-envoy-upstream-rq-timeout-ms)或通过[路由配置](../../v1APIreference/HTTPRouteconfiguration/Route.md)指定的上游请求超时。

- **retriable-4xx**</br>
   如果上游服务器响应可回复的4xx响应代码，Envoy将尝试重试。目前，这个类别中唯一的响应代码是409。

   注：小心打开此重试类型。在某些情况下，409可能表明需要更新乐观锁版本。因此，调用者不应该重试并且需要读取，然后再尝试写入。如果在这种类型的情况下发生重试，则将总是以另一个409失败。

- **refused-stream**</br>
   如果上游服务器使用`REFUSED_STREAM`错误代码重置流，Envoy将尝试重试。此重置类型表示请求可以安全地重试。（包含在5xx中）
重试次数可以通过[x-envoy-max-retries](#x-envoy-max-retries)头或通过[路由配置](../../v1APIreference/HTTPRouteconfiguration/Route.md)来控制。

请注意，重试策略也可以应用在[路由级别](../../v1APIreference/HTTPRouteconfiguration/Route.md)。

默认情况下，Envoy不会执行重试，除非您已经按照上面的方式配置它们。

#### x-envoy-retry-grpc-on
在出口请求上设置此报头会导致Envoy尝试重试失败的请求（重试次数默认为1，可以通过[x-envoy-max-retries](#x-envoy-max-retries)头或路由配置[重试策略](../../v1APIreference/HTTPRouteconfiguration/Route.md)进行控制）。gRPC重试当前仅支持响应头中的gRPC状态码。追踪者中的gRPC状态码不会触发重试逻辑。可以使用','分隔列表来指定一个或多个策略。支持的策略是：

- **cancelled**</br>
 如果响应头中的gRPC状态码被“cancelled”（1），Envoy将尝试重试;

- **deadline-exceeded**</br>
 如果响应头中的gRPC状态码是“deadline-exceeded”（4），Envoy将尝试重试;

- **resource-exhausted**</br>
 如果响应头中的gRPC状态码是“resource-exhausted”（8），Envoy将尝试重试;
 

同样`x-envoy-retry-grpc-on`头的重试次数，可以通过[x-envoy-max-retries](#x-envoy-max-retries)头来控制

请注意，重试策略也可以应用在[路由级别](../../v1APIreference/HTTPRouteconfiguration/Route.md)。

默认情况下，Envoy不会执行重试，除非您已经按照上面的方式配置它们。

#### x-envoy-upstream-alt-stat-name

在出口请求上设置这个头部会导致Envoy将上游响应代码/时间统计信息发送到双重统计树。这对于Envoy不知道的应用程序级别的情况下，可能很有用。详见[统计输出文档](../Clustermanager/Statistics.md)。

#### x-envoy-upstream-canary

如果上游主机设置了这个头部，路由器将使用它来生成金丝雀（灰度升级）相关的统计信息。详见[统计输出文档](../Clustermanager/Statistics.md)。

#### x-envoy-upstream-rq-timeout-alt-response

在出口请求上设置此头部会导致Envoy在请求超时的情况下设置一个204响应代码（而不是504）。头部实际的值被忽略; 只考虑它的存在。另请参见[x-envoy-upstream-rq-timeout-ms](#x-envoy-upstream-rq-timeout-ms)。

#### x-envoy-upstream-rq-timeout-ms

在出口请求上设置此标头将覆盖Envoy的路由配置。超时时间必须以毫秒为单位指定。另请参阅[x-envoy-upstream-rq-per-try-timeout-ms](#x-envoy-upstream-rq-per-try-timeout-ms)。

#### x-envoy-upstream-rq-per-try-timeout-ms

在出口请求上设置此头部将导致Envoy在路由请求上设置每次尝试超时。此超时必须<=全局路由超时（请参阅[x-envoy-upstream-rq-timeout-ms](#x-envoy-upstream-rq-timeout-ms)），否则将被忽略。这允许调用者每次都尝试设置超时重试，同时保持合理的总超时。

#### x-envoy-upstream-service-time

包含上游主机处理请求所花费的时间（以毫秒为单位）。如果客户想要确定网络延迟与服务请求的时间做对比，这是有用的。这个头是在响应中设置的。

#### x-envoy-original-path

如果路由使用`prefix_rewrite`，Envoy会将原始路径头部放在这个头部。这对日志记录和调试很有用。

#### x-envoy-immediate-health-check-fail

如果上游主机返回此头部（设置为任何值），则Envoy将立即判定上游主机的主动健康检查失败（如果已将群集配置为[主动健康检查](../../Configurationreference/Clustermanager/Healthchecking.md)）。这是通过标准数据平面快速上报上游主机所发生故障，而无需等待下一个运行健康检查超时。上游主机也可以通过标准的主动健康检查再次变得健康。请参阅[健康检查概述](../../Introduction/Architectureoverview/Healthchecking.md)了解更多信息。

#### x-envoy-overloaded

如果这个头由上游主机设置了，Envoy不会重试。目前不关心头部的值，只关心是否存在。此外，如果处于[维护模式](#运行时设置)或上游[熔断](../../Introduction/Architectureoverview/Circuitbreaking.md)而导致请求丢失，Envoy将在下游响应中设置此头部。

#### x-envoy-decorator-operation

如果此头部出现在入口请求上，则该头部的值将覆盖由跟踪机制生成的服务器span上的任何本地定义的操作名称（span name）。同样，如果在出口响应中出现此头部，其值将覆盖客户端范围上的任何本地定义的操作名称（span name）。

### 统计

路由器在集群命名空间中输出许多统计信息（取决于所选路由中指定的集群）。浏览[此处](../../Configurationreference/Clustermanager/Statistics.md)获取更多信息。

路由器过滤器输出的统计信息命名空间为`http.<stat_prefix>.`。其中`stat_prefix`来自拥有的[HTTP连接管理器](../../v1APIreference/Networkfilters/HTTPconnectionmanager.md)。

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	no_route	|	Counter	|	因没有路由而导致404错误的总请求数	|
|	no_cluster	|	Counter	|	因目标集群不存在而导致404错误的总请求数	|
|	rq_redirect	|	Counter	|	导致重定向响应的总请求数	|
|	rq_total	|	Counter	|	总路由请求数	|


输出的虚拟集群统计信息名称空间为`vhost.<virtual host name>.vcluster.<virtual cluster name>.`，并包含以下统计信息：

|	Name	|	Type	|	Description	|
|	 -------------	|	 -------------	|	 -------------	|
|	`upstream_rq_<*xx>`	|	Counter	|	HTTP响应代码汇总（例如，2xx，3xx等）	|
|	`upstream_rq_<*>`	|	Counter	|	特定的HTTP响应码（例如：201、302等）	|
|	`upstream_rq_time`	|	Histogram	|	请求时间，单位毫秒	|

### 运行时设置

路由器过滤器支持以下运行时设置：

- **upstream.base_retry_backoff_ms**</br>
指数重试退避时间粒度。默认为25ms。浏览[此处](../../Introduction/Architectureoverview/HTTProuting.md)获取更多信息。

- **upstream.maintenance_mode.\<cluster name>**</br>
将导致立即返回503响应请求的百分比。这会覆盖针对`<cluster name>`集群的任何路由请求行为。这可以用于卸载，故障注入等。默认为禁用。

- **upstream.use_retry**</br>
有资格使用重试的请求百分比。在任何其他重试配置之前检查此配置，如果需要，可用于禁用所有Envoy的重试。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)