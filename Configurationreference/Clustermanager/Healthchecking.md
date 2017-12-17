## 健康检查

- 健康检查[架构概述](../../Introduction/Architectureoverview/Healthchecking.md)
- 如果为集群配置了健康检查，则会触发其他统计信息。详见[这里](../../Configurationreference/Clustermanager/Statistics.md)文档。
- [v1 API文档](../../v1APIreference/Clustermanager/Cluster/Healthchecking.md)
- [v2 API文档](../../v2APIreference/Healthcheck.md)

### TCP健康检查

**注意：本文是为v1 API编写的，但这些概念也适用于v2 API。它将在未来版本中使用v2 API重写。**

执行的匹配类型如下（这是MongoDB运行健康检查请求和响应）：

```
 {
   "send": [
     {"binary": "39000000"},
     {"binary": "EEEEEEEE"},
     {"binary": "00000000"},
     {"binary": "d4070000"},
     {"binary": "00000000"},
     {"binary": "746573742e"},
     {"binary": "24636d6400"},
     {"binary": "00000000"},
     {"binary": "FFFFFFFF"},
     {"binary": "13000000"},
     {"binary": "01"},
     {"binary": "70696e6700"},
     {"binary": "000000000000f03f"},
     {"binary": "00"}
    ],
    "receive": [
     {"binary": "EEEEEEEE"},
     {"binary": "01000000"},
     {"binary": "00000000"},
     {"binary": "0000000000000000"},
     {"binary": "00000000"},
     {"binary": "11000000"},
     {"binary": "01"},
     {"binary": "6f6b"},
     {"binary": "00000000000000f03f"},
     {"binary": "00"}
    ]
}
```

在每个健康检查周期中，所有“发送”字节都会发送到目标服务器。每个二进制块可以是任意长度的，并且在发送时只是连接在一起。（分成多个块用于可读性）。

在检查响应时，执行“模糊”匹配，使得必须找到每个二进制块，并且按照指定的顺序，但不必是连续的。因此，在上面的例子中，在“EEEEEEEE”和“01000000”之间的响应中可以插入“FFFFFFFF”，并且检查仍然通过。这样做是为了支持在响应中插入非确定性数据（如时间）的协议。

若需要一个更复杂的健康检查模式，如发送/接收/发送/接收目前还不支持。

如果“接收”是一个空数组，则Envoy将执行“仅连接”TCP健康检查。在每个周期中，Envoy将尝试连接到上游主机，并且如果连接成功，则认为它是成功的。为每个健康检查周期创建一个新的连接。


## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)