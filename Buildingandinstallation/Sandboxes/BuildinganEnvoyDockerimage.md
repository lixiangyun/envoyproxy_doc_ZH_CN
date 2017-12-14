## 构建Envoy Docker镜像

以下步骤指导您构建自己的Envoy二进制文件，并将其放入干净的Ubuntu容器中。

**第1步：构建Envoy**

使用`envoyproxy/envoy-build`编译Envoy。该镜像具有构建Envoy所需的全部软件。在您的Envoy目录中执行如下命令：

```
$ pwd
src/envoy
$ ./ci/run_envoy_docker.sh './ci/do_ci.sh bazel.release'
```

执行该命令需要一些时间，因为它会在编译一个Envoy二进制文件之后，运行相关测试。

有关构建和差异化构建的更多详细信息，请参阅[ci/README.md](https://github.com/envoyproxy/envoy/blob/master/ci/README.md)。

**步骤2：仅使用Envoy二进制生成映像**

在这一步中，我们将构建一个只有Envoy二进制文件的映像，而不是用来构建它的软件。

```
$ pwd
src/envoy/
$ docker build -f ci/Dockerfile-envoy-image -t envoy .
```

现在，可以在任何`Dockerfile`中更改`FROM`，则可以使用此Envoy镜像来构建任何沙箱。

如果您有兴趣，欢迎您修改Envoy并测试，这将特别有用。

## 返回
- [上一级](../Sandboxes.md)
- [首页目录](../../README.md)
