## gRPC桥接

### Envoy gRPC
gRPC桥接沙箱是展示Envoy gRPC桥接过滤器的一个例子。其中包含带有Python HTTP客户端的gRPC内存Key/Value存储。Python客户端通过Envoy代理进程发出HTTP/1请求，并将其升级为HTTP/2的gRPC请求。响应随后缓冲，并作为HTTP/1报文负载有效信息发送回客户端。

本例还演示Envoy另一个功能，就是通过Envoy路由配置，具有基础鉴权的路由能力。

### 构建Go服务
构建&运行Go gRPC服务：

```
$ pwd
envoy/examples/grpc-bridge
$ script/bootstrap
$ script/build
```

注意：构建需要的Envoy代码库（或其工作副本）位于`$GOPATH/src/github.com/envoyproxy/envoy`中。

### Docker Compose
运行`docker compose`文件，并设置Python和gRPC在容器中运行：

```
$ pwd
envoy/examples/grpc-bridge
$ docker-compose up --build
```

### 发送请求到Key/Value存储
要使用Python服务并发送gRPC请求：

```
$ pwd
envoy/examples/grpc-bridge
# set a key
$ docker-compose exec python /client/client.py set foo bar
setf foo to bar

# get a key
$ docker-compose exec python /client/client.py get foo
bar

# modify an existing key
$ docker-compose exec python /client/client.py set foo baz
setf foo to baz

# get the modified key
$ docker-compose exec python /client/client.py get foo
baz
```

在正在运行的`docker-compose`容器中，您应该可以看到gRPC服务打印其运行的记录：

```
grpc_1    | 2017/05/30 12:05:09 set: foo = bar
grpc_1    | 2017/05/30 12:05:12 get: foo
grpc_1    | 2017/05/30 12:05:18 set: foo = baz
```

## 返回
- [上一级](../Sandboxes.md)
- [首页目录](../../README.md)
