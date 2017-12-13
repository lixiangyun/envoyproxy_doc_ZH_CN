## 前端代理

为让大家尽快了解Envoy如何作为前端代理，我们发布了一个[docker compose](https://docs.docker.com/compose/)沙箱，这个沙箱部署了一个前端Envoy代理和几个后端服务（简单的flask应用），并与一个正在运行的合作的Envoy服务。这三个容器将部署在名为`envoymesh`的虚拟网格中。

该Docker compose的部署图如下所示：

![部署图](docker_compose_v0.1.svg)

所有传入的请求都通过前端Envoy进行路由，该Envoy充当位于`envoymesh`网络边缘的反向代理。端口`80`通过docker compose映射到端口`8000`（请参阅[/examples/front-proxy/docker-compose.yml](https://github.com/envoyproxy/envoy/blob/master//examples/front-proxy/docker-compose.yml)）。此外，请注意，由前端Envoy路由到容器内的服务，实际上所有流量都是路由到服务的Envoy代理（在[/examples/front-proxy/front-envoy.json](https://github.com/envoyproxy/envoy/blob/master//examples/front-proxy/front-envoy.json)中设置的路由）。反过来，服务的Envoy通过环回地址（[/examples/front-proxy/service-envoy.json](https://github.com/envoyproxy/envoy/blob/master//examples/front-proxy/service-envoy.json)中的路由设置）将请求路由到flask应用程序。此阐述了Envoy与您服务搭配的优势：所有请求都由Envoy代理，并有效地路由到您的服务。


### 运行沙箱
以下文档将按照上图中所述的envoy集群进行运行设置。

**第1步：安装Docker工具集**

请您确保已经安装了最新版本的`docker`，`docker-compose`和`docker-machine`。

[Docker工具箱](https://www.docker.com/products/docker-toolbox)提供了简单的方法来获取这些工具。

**第2步：设置Docker Machine**

首先让我们创建一个新的机器来容纳容器：

```
$ docker-machine create --driver virtualbox default
$ eval $(docker-machine env default)
```

**第3步：建立本地Envoy克隆仓库，并启动所有的容器**

如果你还没有克隆Envoy仓库，请用git克隆`git clone git@github.com:envoyproxy/envoy`或者`git clone https://github.com/envoyproxy/envoy.git`：

```
$ pwd
envoy/examples/front-proxy
$ docker-compose up --build -d
$ docker-compose ps
        Name                       Command               State      Ports
-------------------------------------------------------------------------------------------------------------
example_service1_1      /bin/sh -c /usr/local/bin/ ...    Up       80/tcp
example_service2_1      /bin/sh -c /usr/local/bin/ ...    Up       80/tcp
example_front-envoy_1   /bin/sh -c /usr/local/bin/ ...    Up       0.0.0.0:8000->80/tcp, 0.0.0.0:8001->8001/tcp
```

**第4步：测试Envoy的路由功能**

您现在可以通过前端Envoy向两个服务发送请求。

对于service1：

```
$ curl -v $(docker-machine ip default):8000/service/1
*   Trying 192.168.99.100...
* Connected to 192.168.99.100 (192.168.99.100) port 8000 (#0)
> GET /service/1 HTTP/1.1
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

对于service2：

```
$ curl -v $(docker-machine ip default):8000/service/2
*   Trying 192.168.99.100...
* Connected to 192.168.99.100 (192.168.99.100) port 8000 (#0)
> GET /service/2 HTTP/1.1
> Host: 192.168.99.100:8000
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: text/html; charset=utf-8
< content-length: 89
< x-envoy-upstream-service-time: 2
< server: envoy
< date: Fri, 26 Aug 2016 19:39:23 GMT
< x-envoy-protocol-version: HTTP/1.1
<
Hello from behind Envoy (service 2)! hostname: 92f4a3737bbc resolvedhostname: 172.19.0.2
* Connection #0 to host 192.168.99.100 left intact
```

请注意，每个请求在发送给前端Envoy时，已正确路由到相应的应用程序。

**第5步：测试Envoy的负载均衡能力**

现在扩展我们的service1节点来演示Envoy的集群能力。

```
$ docker-compose scale service1=3
Creating and starting example_service1_2 ... done
Creating and starting example_service1_3 ... done
```

现在，如果我们多次向service1发送请求，前端Envoy会将请求通过负载均衡发给三个service1服务：

```
$ curl -v $(docker-machine ip default):8000/service/1
*   Trying 192.168.99.100...
* Connected to 192.168.99.100 (192.168.99.100) port 8000 (#0)
> GET /service/1 HTTP/1.1
> Host: 192.168.99.100:8000
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: text/html; charset=utf-8
< content-length: 89
< x-envoy-upstream-service-time: 1
< server: envoy
< date: Fri, 26 Aug 2016 19:40:21 GMT
< x-envoy-protocol-version: HTTP/1.1
<
Hello from behind Envoy (service 1)! hostname: 85ac151715c6 resolvedhostname: 172.19.0.3
* Connection #0 to host 192.168.99.100 left intact
$ curl -v $(docker-machine ip default):8000/service/1
*   Trying 192.168.99.100...
* Connected to 192.168.99.100 (192.168.99.100) port 8000 (#0)
> GET /service/1 HTTP/1.1
> Host: 192.168.99.100:8000
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: text/html; charset=utf-8
< content-length: 89
< x-envoy-upstream-service-time: 1
< server: envoy
< date: Fri, 26 Aug 2016 19:40:22 GMT
< x-envoy-protocol-version: HTTP/1.1
<
Hello from behind Envoy (service 1)! hostname: 20da22cfc955 resolvedhostname: 172.19.0.5
* Connection #0 to host 192.168.99.100 left intact
$ curl -v $(docker-machine ip default):8000/service/1
*   Trying 192.168.99.100...
* Connected to 192.168.99.100 (192.168.99.100) port 8000 (#0)
> GET /service/1 HTTP/1.1
> Host: 192.168.99.100:8000
> User-Agent: curl/7.43.0
> Accept: */*
>
< HTTP/1.1 200 OK
< content-type: text/html; charset=utf-8
< content-length: 89
< x-envoy-upstream-service-time: 1
< server: envoy
< date: Fri, 26 Aug 2016 19:40:24 GMT
< x-envoy-protocol-version: HTTP/1.1
<
Hello from behind Envoy (service 1)! hostname: f26027f1ce28 resolvedhostname: 172.19.0.6
* Connection #0 to host 192.168.99.100 left intact
```

**第6步：进入容器开启curl服务**

除了使用主机上的curl外，您还可以自己输入容器并从里面curl。要输入一个容器镜像，你可以使用`docker-compose exec <container_name> /bin/bash`。例如，我们可以进入`front-envoy`容器，并在执行本地的curl服务：

```
$ docker-compose exec front-envoy /bin/bash
root@81288499f9d7:/# curl localhost:80/service/1
Hello from behind Envoy (service 1)! hostname: 85ac151715c6 resolvedhostname: 172.19.0.3
root@81288499f9d7:/# curl localhost:80/service/1
Hello from behind Envoy (service 1)! hostname: 20da22cfc955 resolvedhostname: 172.19.0.5
root@81288499f9d7:/# curl localhost:80/service/1
Hello from behind Envoy (service 1)! hostname: f26027f1ce28 resolvedhostname: 172.19.0.6
root@81288499f9d7:/# curl localhost:80/service/2
Hello from behind Envoy (service 2)! hostname: 92f4a3737bbc resolvedhostname: 172.19.0.2
```

**第7步：进入容器和使用curl管理**

当Envoy运行时，它也将`admin`连接到所需的端口。在示例配置`admin`被绑定到`8001`端口.我们可以`curl`它获得有用的信息。例如，您可以`curl` `/server_info`来获取有关您正在运行的Envoy版本信息。另外，你可以·curl· ·/stats·得到统计数据。例如在`frontenvoy`里面我们可以得到：

```
$ docker-compose exec front-envoy /bin/bash
root@e654c2c83277:/# curl localhost:8001/server_info
envoy 10e00b/RELEASE live 142 142 0
root@e654c2c83277:/# curl localhost:8001/stats
cluster.service1.external.upstream_rq_200: 7
...
cluster.service1.membership_change: 2
cluster.service1.membership_total: 3
...
cluster.service1.upstream_cx_http2_total: 3
...
cluster.service1.upstream_rq_total: 7
...
cluster.service2.external.upstream_rq_200: 2
...
cluster.service2.membership_change: 1
cluster.service2.membership_total: 1
...
cluster.service2.upstream_cx_http2_total: 1
...
cluster.service2.upstream_rq_total: 2
...
```

请注意，我们还可以获得上游群集的成员数量，完成的请求数量，有关http入站的信息以及其他大量有用的统计信息。

## 返回
- [上一级](../Sandboxes.md)
- [首页目录](../../README.md)