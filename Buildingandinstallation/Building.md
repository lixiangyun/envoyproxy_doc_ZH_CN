## 构建
Envoy使用[Bazel](https://bazel.build/)工具构建系统。为了简化初次构建以及快速入门，我们提供了一个基于Ubuntu16的Docker容器镜像，其中包含了构建静态链接Envoy所需的所有内容，请参阅[ci/README.md](https://github.com/envoyproxy/envoy/blob/master/ci/README.md)。

如果需要手动构建，请按照[bazel/README.md](https://github.com/envoyproxy/envoy/blob/master/bazel/README.md)中的说明进行操作。

### 要求
Envoy最初是在Ubuntu 14 LTS上开发和部署的。它也可以在任何的最新Linux上运行，包括Ubuntu 16 LTS。

构建Envoy需要满足以下要求：

- GCC 5+（用于支持C++14）。
- [预构建](https://github.com/envoyproxy/envoy/tree/master/ci/build_container/build_recipes)的第三方依赖。
- 依赖本地Bazel工具。

有关手动构建的更多信息操作，请参阅[CI](https://github.com/envoyproxy/envoy/blob/master/ci/README.md)和[Bazel](https://github.com/envoyproxy/envoy/blob/master/bazel/README.md)文档链接。

### 预构建的二进制文件
在每个主提交上，我们创建一组包含Envoy二进制文件的轻量级Docker镜像。 当我们正式发布的时候，我们还会用发布版本标记Docker镜像。

- [envoyproxy/envoy](https://hub.docker.com/r/envoyproxy/envoy/tags/)：基于Ubuntu Xenial存放带有符号的二进制文件版本。
- [envoyproxy/envoy-alpine](https://hub.docker.com/r/envoyproxy/envoy-alpine/tags/)：基于glibc alpine无符号二进制文件版本。
- [envoyproxy/envoy-alpine-debug](https://hub.docker.com/r/envoyproxy/envoy-alpine-debug/tags/)：基于glibc alpine可调试的二进制文件版本。

我们也会考虑通过社区化运作，发挥大家兴趣来帮助CI，包装和提供额外的二进制类型。如果需要，请在GitHub中添加一个[issue](https://github.com/envoyproxy/envoy/issues)。

## 返回
- [上一级](../Buildingandinstallation.md)
- [首页目录](../README.md)
