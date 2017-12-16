## 速率限制

- 速率限制[架构概述](../../Introduction/Architectureoverview/Globalratelimiting.md)
- [v1 API 参考](../../v1APIreference/HTTPfilters/Ratelimit.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Ratelimit.md)

当请求的路由或虚拟主机有一个或多个符合过滤器设置的速率限制配置时，HTTP速率限制过滤器将调用速率限制服务。路由可以选择包含虚拟主机速率限制配置。可配置多个应用于请求。每个配置描述符都会导致被发送到速率限制服务。

如果速率限制服务被调用，并且任何响应超出限制的描述符，都将返回429响应。

### 撰写行动
**注意：本文是为v1 API编写的，但这些概念也适用于v2 API。它将在未来版本中使用v2 API重写。**

路由或虚拟主机上的每个速率限制操作都会填充一个描述符条目。 描述符条目的矢量组成描述符。 要创建更复杂的费率限制描述符，可以按任意顺序组合操作。 描述符将按照在配置中指定操作的顺序进行填充。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)