## Jaeger跟踪

Jaeger跟踪沙箱演示使用Jaeger作为跟踪服务程序，为Envoy请求跟踪提供能力。这个沙盒与上面描述的前端代理架构非常类似，但有一点不同：在返回请求响应之前，`service1`对`service2`进行API调用。这三个容器将被部署在名为`envoymesh`的虚拟网络中。

所有的请求都通过前端Envoy代理进行路由，该Envoy充当位于`envoymesh`网络边缘的反向代理角色。通过docker compose将端口`80`映射到端口`8000`（请参阅[/examples/jaeger-tracing/docker-compose.yml](https://github.com/envoyproxy/envoy/blob/master//examples/jaeger-tracing/docker-compose.yml)）。请注意，所有Envoy都配置为请求跟踪收集（例如，[/examples/jaeger-tracing/front-envoy-jaeger.json](https://github.com/envoyproxy/envoy/blob/master//examples/jaeger-tracing/front-envoy-jaeger.json)中的`http_connection_manager/config/tracing`设置），并将Jaeger设置为跟踪所生成的span收集服务集群（在[/examples/jaeger-tracing/front-envoy-jaeger.json](https://github.com/envoyproxy/envoy/blob/master//examples/jaeger-tracing/front-envoy-jaeger.json)中设置跟踪驱动程序）。

在将请求路由到相应的Envoy服务或应用程序之前，Envoy将负责为跟踪生成适当的span（父/子上下文span）。在高级别，每个span会记录上行API调用的延迟以及将span与其他相关span（例如跟踪ID）关联所需的信息。

Envoy跟踪最重要的好处之一是它能够将生成的跟踪记录发送到Jaeger服务集群。但是，为了充分利用跟踪功能，在调用其他服务时，应用程序也必须传递Envoy生成的跟踪头。在我们提供的沙箱中，充当`service1`简单的`flask`应用程序（请参阅/examples/front-proxy/service.py中的跟踪函数）传播跟踪头，同时对`service2`进行远程调用。

### 运行沙箱
以下文档将按照上图中所述组织的envoy集群的设置运行。

**第1步：建立沙箱**

要构建这个沙箱示例，并启动示例应用程序，请运行以下命令：


```
$ pwd
envoy/examples/jaeger-tracing
$ docker-compose up --build -d
$ docker-compose ps
        Name                       Command               State      Ports
-------------------------------------------------------------------------------------------------------------
jaegertracing_service1_1      /bin/sh -c /usr/local/bin/ ...    Up       80/tcp
jaegertracing_service2_1      /bin/sh -c /usr/local/bin/ ...    Up       80/tcp
jaegertracing_front-envoy_1   /bin/sh -c /usr/local/bin/ ...    Up       0.0.0.0:8000->80/tcp, 0.0.0.0:8001->8001/tcp
```

**第2步：产生一些负载请求**

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

**第3步：通过Jaeger UI查看跟踪记录**

使用浏览器打开`http://localhost:16686`。您应该可以看到Jaeger仪表板。将服务设置为“前端代理”，然后点击“查找跟踪”。你应该看到来自前端代理的跟踪信息。单击一个跟踪，就可以查看从前端代理到`service1`到`service2`的请求所经过的路径，以及每个调用产生的延迟。

## 返回
- [上一级](../Sandboxes.md)
- [首页目录](../../README.md)
