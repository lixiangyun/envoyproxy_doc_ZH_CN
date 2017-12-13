## 参考配置
Envoy的源代码发行版中，包含三个主要部署类型的配置模板范例：

- [服务间](../Introduction/Deploymenttypes.md)
- [前端代理](../Introduction/Deploymenttypes.md)
- [双重代理](../Introduction/Deploymenttypes.md)

本文的目标展示Envoy在复杂部署场景下的全部功能。不适用于所有功能。有关完整的文档，请参阅[配置参考](../Configurationreference.md)。

### 配置生成器
可能配置Envoy已经变的相对复杂。在Lyft中，我们使用[jinja](http://jinja.pocoo.org/)模板来让创建和管理配置更容易。源代码发行版包含一个的配置生成器，它大致上与我们在Lyft中使用的版本相似。我们还为以上三种情况提供了三个示例配置模板。

- 配置生成器脚本：[configs/configgen.py](https://github.com/envoyproxy/envoy/blob/master/configs/configgen.py)
- 服务模板：[configs/envoy_service_to_service.template.json](https://github.com/envoyproxy/envoy/blob/master/configs/envoy_service_to_service.template.json)
- 前端代理模板：[configs/envoy_front_proxy.template.json](https://github.com/envoyproxy/envoy/blob/master/configs/envoy_front_proxy.template.json)
- 双重代理模板：[configs/envoy_double_proxy.template.json](https://github.com/envoyproxy/envoy/blob/master/configs/envoy_double_proxy.template.json)

若要生成示例配置，请在repo根目录运行以下命令：

```
mkdir -p generated/configs
bazel build //configs:example_configs
tar xvf $PWD/bazel-genfiles/configs/example_configs.tar -C generated/configs
```

上一个命令将使用`configgen.py`中定义的一些变量，展开生成三个完全的配置。 请参阅`configgen.py`中的注释，以获取有关详细扩展配置。

关于示例配置的一些注意事项：

- 假定服务发现服务的实例正在`discovery.yourcompany.net`上运行。
- 假定您的`company.net`的DNS设置了各种各样的东西。在配置模板中修改以支持不同的示例。
- 跟踪默认配置LightStep。要禁用此功能或启用[Zipkin](http://zipkin.io)跟踪，请删除或更改相应跟踪配置。
- 该配置示例使用全局限速服务。若要禁用此功能，请删除速限相关的配置。
- 配置路由发现的服务，以便服务间引用该配置，并假定它正在`rds.yourcompany.net`上运行。
- 配置集群发现的服务，作为配置参考，假定在`cds.yourcompany.net`上运行。


### 配置冒烟测试
[configs/google_com_proxy.json](https://github.com/envoyproxy/envoy/blob/master/configs/google_com_proxy.json)中提供了一个非常简单的Envoy配置，可用于验证基本纯HTTP代理场景。但并不代表一个实际的Envoy部署。只是用这个来冒烟测试Envoy，如下运行：

```
build/source/exe/envoy -c configs/google_com_proxy.json -l debug
curl -v localhost:10000
```

## 返回
- [上一级](../Buildingandinstallation.md)
- [首页目录](../README.md)