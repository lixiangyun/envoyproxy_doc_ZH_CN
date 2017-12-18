## 路由表检查工具

**注意：以下配置仅适用于路由表检查工具，不是Envoy二进制文件的一部分。路由表检查工具是一个独立的二进制文件，可用于验证给定配置文件的Envoy路由。**

路由表检查工具的需要相应的输入，并且检查之后返回的[路由配置](../v1APIreference/HTTPRouteconfiguration.md)是否符合预期。该工具可用于检查群集名称，虚拟群集名称，虚拟主机名称，手动路径重写，手动主机重写，路径重定向和标题字段匹配。可以扩展添加其他测试用例。有关安装工具和示例工具输入/输出的详细信息，请参见[安装](../Buildingandinstallation/Tools.md)。

路由表检查工具配置由一个json测试对象数组组成。每个测试对象由三部分组成。

- **test_name**</br>
该字段指定每个测试对象的名称。

- **input**</br>
输入值字段是指定要传递给路由器的参数。示例：输入字段包括`:authority`，`:path`和`:method` 头部字段。其中`:authority`和`:path`字段是指定发送到路由器的url，并且是必填字段。所有其他字段是可选的。

- **validate**</br>
检验字段是指定要检查的期望值和测试用例。至少需要一个测试用例。

一个简单的json格式的工具配置，以及一个测试用例，写法如下。测试与“instant-server”的群集匹配。

```
[
  {
    "test_name: "Cluster_name_test",
    "input":
      {
        ":authority":"api.lyft.com",
        ":path": "/api/locations"
      }
    "validate"
      {
        "cluster_name": "instant-server"
      }
   }
]
```

```
[
  {
    "test_name": "...",
    "input":
      {
        ":authority": "...",
        ":path": "...",
        ":method": "...",
        "internal" : "...",
        "random_value" : "...",
        "ssl" : "...",
        "additional_headers": [
          {
            "field": "...",
            "value": "..."
          },
          {
             "..."
          }
        ]
      }
    "validate": {
      "cluster_name": "...",
      "virtual_cluster_name": "...",
      "virtual_host_name": "...",
      "host_rewrite": "...",
      "path_rewrite": "...",
      "path_redirect": "...",
      "header_fields" : [
        {
          "field": "...",
          "value": "..."
        },
        {
          "..."
        }
      ]
    }
  },
  {
    "..."
  }
]
```

#### test_name </br>
(required, string) 测试对象名称

### input 
(required, object) 做为路由器输入，并测试返回的路由

- **:authority**</br>
(required, string) 权威的URL。此值与`:path`参数一起定义，匹配需要的`url`路径。示例值是“api.lyft.com”。

- **:path**</br>
(required, string) URL路径。示例值是“/foo”。

- **:method**</br>
(optional, string) 请求方法。如果未指定，则默认方法是`GET`。选项是`GET`，`PUT`或`POST`。

- **internal**</br>
(optional, boolean) 是否将`x-envoy-internal`设置为“true”的标志。如果未指定，或者如果    `internal`等于`false`，则不会设置`x-envoy-internal`。

- **random_value**</br>
(optional, integer) 用于标识加权群集选择的目标的整数。`random_value`的默认值是0。

- **ssl**</br>
(optional, boolean) 确定是否将`x-forwarded-proto`设置为`https`或`http`的标志。通过将`x-forwarded-proto`设置为给定的协议，该工具能够模拟通过`http`或`https`发出请求的客户端行为。默认情况是`false`，与之对应`x-forwarded-proto`设置为`http`。

- **additional_headers**</br>
(optional, array) 需要额外添加头部为路由器的输入。其他配置选项“:authority”，“:path”，“:method”，“x-forwarded-proto”和“x-envoy-internal”字段，不应在此设置。

    - **field**</br>
    (required, string) 被添加头部的名称
    
    - **value**</br>
    (required, string) 被添加头部的值

### validate
(required, object) 校验对象是指定要匹配的路由返回的参数。至少必须指定一个测试参数。使用“”（空字符串）表示没有返回值。例如，若测试不需要集群匹配，请使用{“cluster_name”:“”}。

- **cluster_name**</br>
(optional, string) 匹配的集群名称

- **virutal_cluster_name**</br>
(optional, string) 匹配的虚拟集群名称

- **virtual_host_name**</br>
(optional, string) 匹配的虚拟主机名称

- **host_rewrite**</br>
(optional, string) 重写后匹配主机头部字段

- **path_rewrite**</br>
(optional, string) 重写后匹配路径头部字段

- **path_redirect**</br>
(optional, string) 匹配返回的重定向路径

- **header_fields**</br>
(optional, array) 匹配列出的标题字段。示例标题字段包括“:path”，“cookie”和“date”字段。在所有其他测试用例之后检查标题字段。因此，检查的标题字段将是适用时重定向或重写路由的字段。

    - **field**</br>
      (required, string) 要匹配的标题字段的名称
      
    - **value**</br>
      (required, string) 要匹配的标题字段的值

## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
