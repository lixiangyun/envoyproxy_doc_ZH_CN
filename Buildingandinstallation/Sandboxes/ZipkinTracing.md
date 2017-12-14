## Zipkin跟踪

Zipkin跟踪演示使用Zipkin作为跟踪服务端，提供跟踪Envoy请求记录展示的功能。这个沙箱与上面描述的前端代理架构非常类似，但有一点不同：在响应返回之前，`service1`对`service2`进行API调用。这三个容器将被部署在名为`envoymesh`的虚拟网络中。

所有的请求都经过前端Envoy进行路由，该Envoy充当位于`envoymesh`网络边缘的反向代理。端口`80`通过docker compose映射到端口`8000`（请参阅[/examples/zipkin-tracing/docker-compose.yml](https://github.com/envoyproxy/envoy/blob/master//examples/zipkin-tracing/docker-compose.yml)）。请注意，所有Envoy都配置请求跟踪收集（例如，[/examples/zipkin-tracing/front-envoy-zipkin.json](https://github.com/envoyproxy/envoy/blob/master//examples/zipkin-tracing/front-envoy-zipkin.json)中的`http_connection_manager/config/tracing`设置），并将Zipkin设置为跟踪所生成的spans收集器，将发送到Zipkin群集（在[/examples/zipkin-tracing/front-envoy-zipkin.json](https://github.com/envoyproxy/envoy/blob/master//examples/zipkin-tracing/front-envoy-zipkin.json)中跟踪驱动程序设置）。

在将请求路由到相应的Envoy服务或应用程序之前，Envoy将负责为跟踪生成适当的spans（父/子/span上下文共享）。在高级别，每个span记录上行API调用的延迟以及将span与其他相关span（例如跟踪ID）关联所需的信息。

从Envoy跟踪的最重要的特点是，它会将调用轨迹发送到Zipkin服务群集。但是，为了充分利用跟踪，应用程序必须传播Envoy生成的跟踪头，同时调用其他服务。在我们提供的沙箱中，`service1`充当了简单`flask`应用程序（请参阅[/examples/front-proxy/service.py](https://github.com/envoyproxy/envoy/blob/master//examples/front-proxy/service.py)中的跟踪函数）跟踪传播头，同时对`service2`进行远程调用。

### 运行沙箱
以下文档将按照上图中所述组织的envoy集群的进行设置和运行。

**第1步：构建沙箱**

若构建这个沙箱示例，并启动示例应用程序，请运行以下命令：

```
$ pwd
envoy/examples/zipkin-tracing
$ docker-compose up --build -d
$ docker-compose ps
        Name                       Command               State      Ports
-------------------------------------------------------------------------------------------------------------
zipkintracing_service1_1      /bin/sh -c /usr/local/bin/ ...    Up       80/tcp
zipkintracing_service2_1      /bin/sh -c /usr/local/bin/ ...    Up       80/tcp
zipkintracing_front-envoy_1   /bin/sh -c /usr/local/bin/ ...    Up       0.0.0.0:8000->80/tcp, 0.0.0.0:8001->8001/tcp
```

**第2步：产生一些请求负载**

您现在可以通过前端Envoy向`service1`发送一个请求，如下所示：


```
$ curl -v $(docker-machine ip default):8000/trace/1
*   Trying 192.168.99.100...
* Connected to 192.168.99.100 (192.168.99.100) port 8000 (#0)
> GET /trace/1 HTTP/1.1
> Host: 192.168.99.100:8000
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: text/html; charset=utf-8
< content-length: 89
< x-envoy-upstream-service-time: 1
< server: envoy
< date: Fri, 26 Aug 2016 19:39:19 GMT
< x-envoy-protocol-version: HTTP/1.1
<
Hello from behind Envoy (service 1)! hostname: f26027f1ce28 resolvedhostname: 172.19.0.6
* Connection #0 to host 192.168.99.100 left intact
```

**第3步：通过Zipkin UI查看跟踪记录**

使用您的浏览器打开`http://192.168.99.100:9411`。你应该看到Zipkin仪表板。 如果这个IP地址不正确，可以通过运行`$ docker-machine ip default`找到正确的地址。将服务设置为“前端代理”，并将开始时间设置为测试的前几分钟（如：第2步），然后按回车。你应该看到来自前端代理的跟踪记录。单击一个跟踪记录，就可以查看从前端代理到`service1`到`service2`的请求所经过的路径，以及每个调用所产生的延迟。


## 返回
- [上一级](../Sandboxes.md)
- [首页目录](../../README.md)
