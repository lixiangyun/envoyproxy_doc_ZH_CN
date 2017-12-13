## 工具

### 配置加载检查工具

配置加载检查工具校验JSON格式的配置文件，是否符合JSON编码规范，并符合Envoy JSON模式。 该工具利用`test/config_test/config_test.cc`中的配置进行校验。在服务配置初始化中加载JSON配置是，会使用到它进行校验。

**输入**

该工具需要一个PATH做为根目录配置，用于保存JSON Envoy配置文件。该工具将以递归方式遍历文件目录结构，并对每个文件运行配置测试。请记住，该工具将尝试所加载路径中的所有文件。

**输出**

该工具将输出Envoy日志，用它正在测试的配置初始化服务器配置。如果存在JSON文件格式不正确或不符合Envoy JSON模式的配置文件，则该工具将以状态`EXIT_FAILURE`退出。如果该工具成功加载所有找到的配置文件，它将以状态`EXIT_SUCCESS`退出。

**构建**

使用Bazel在本地构建该工具。

`bazel build //test/tools/config_load_check:config_load_check_tool`

**运行**

使用该工具时，如上所述加上PATH路径。

`bazel-bin/test/tools/config_load_check/config_load_check_tool PATH`


### 路由表检查工具

路由表检查工具检查路由器返回的路由参数是否与预期相符。该工具还可用于检查重定向路径，路径重写或主机重写是否与预期相符。

**输入**

该工具需要两个输入JSON文件：

1. 路由JSON配置文件。配置中找到路由配置JSON文件模式。
2. 工具JSON配置文件。配置中找到工具配置JSON文件模式。工具配置输入文件指定URL（由权限和路径组成）和期望的路由参数值。以及额外可选的参数，如附加标题。

**输出**

如果任何测试用例与期望的路由参数值不匹配，则程序以`EXIT_FAILURE`状态退出。

添加`--details`选项可以打印出每个测试的详细信息。第一行表示测试名称。

如果测试失败，则打印失败的测试用例的详细信息。第一个字段是预期的路由参数值。第二个字段是实际的路由参数值。第三个字段表示比较的参数。在下面的示例中，Test_2和Test_5在其他测试通过时失败。该测试案例中，将打印冲突详细信息。

```
Test_1
Test_2
default other virtual_host_name
Test_3
Test_4
Test_5
locations ats cluster_name
Test_6
```

目前不支持使用有效的运行时值进行测试，这可能会在未来添加该功能。

**构建**

使用Bazel在本地构建该工具。

`bazel build //test/tools/router_check:router_check_tool`

**运行**

该工具需要输入两个json文件和一个可选的命令行选项`--details`。命令行参数期望的顺序是：
1.路由器配置json文件；
2.工具配置json文件；
3.可选的详细信息标识；

`bazel-bin/test/tools/router_check/router_check_tool router_config.json tool_config.json`

`bazel-bin/test/tools/router_check/router_check_tool router_config.json tool_config.json --details`

**测试**

通过bash的shell脚本，使用bazel运行测试。该测试使用不同的路由和工具的配置json文件，进行比较。这些配置json文件可以在`bazel test //test/tools/router_check/...`中找到。

`bazel test //test/tools/router_check/...`


### 模式验证检查工具
模式验证程序工具验证传入的JSON是否符合配置规范。要验证整个配置，请参阅上述配置加载检查工具章节。目前只支持[路由配置](../v1APIreference/HTTPRouteconfiguration.md)模式验证。

**输入**

该工具需要两个输入：

1. 检查在JSON中传递的模式类型。支持的类型是：
    - route - 用于[路由配置](../v1APIreference/HTTPRouteconfiguration.md)验证；
2. JSON的路径；

**输出**

如果JSON符合模式，则该工具将以状态`EXIT_SUCCESS`退出。 如果JSON不符合模式，则会输出错误消息，详细说明不符合模式的内容。该工具将以状态`EXIT_FAILURE`退出。

**构建**

使用Bazel在本地构建该工具。

`bazel build //test/tools/schema_validator:schema_validator_tool`

**运行**

该工具如上所述采取路径。

`bazel-bin/test/tools/schema_validator/schema_validator_tool  --schema-type SCHEMA_TYPE  --json-path PATH`


## 返回
- [上一级](../Buildingandinstallation.md)
- [首页目录](../README.md)
