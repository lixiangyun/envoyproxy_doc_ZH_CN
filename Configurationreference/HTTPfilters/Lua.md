## Lua

**注意：Lua脚本HTTP过滤器是实验性的。在生产中使用需要您自担风险。它正在被公布，以便对暴露的API进行初步反馈，并进行进一步的开发，测试和验证。当我们认为Lua过滤器已经受到足够的API稳定性测试，通常称其为生产准备就绪时，该警告将被移除。**


### 概述
HTTP Lua过滤器允许在请求和响应流程中运行Lua脚本。在运行时使用[LuaJIT](http://luajit.org/)。正因为如此，支持的Lua版本大部分是5.1，具有一些5.2的特性。有关更多详细信息，请参阅[LuaJIT文档](http://luajit.org/extensions.html)。

该过滤器仅支持在配置中直接加载Lua代码。如果需要本地文件系统代码，则可以使用简单的内联脚本从本地环境加载代码的其余部分。

过滤器和支持Lua是基于如下高级设计：

- 所有Lua环境都是每个工作者线程。这意味着没有真正的全局数据。任何在加载时创建和填充的全局变量在工作线程之间相互隔离。真正的全局支持可能会在未来通过API添加。
- 所有脚本都以协程运行。这意味着即使它们可能执行复杂的异步任务，它们也是以同步样式编写的。这使得脚本更容易编写。所有网络/异步处理由Envoy通过一组API执行。Envoy将酌情切换(yield)脚本，并在异步任务完成时恢复脚本。
- 不要在脚本中执行阻塞操作。因为Envoy API会用于所有IO，以及性能相关的重要操作。


### 目前支持高级功能
**注：预计随着Lua过滤器在生产中的应用，以下列表将随着时间的推移而扩大范围。这些API一直保持很小的用意。为了使脚本编写非常简单和安全。若存在非常复杂或更高性能的场景，请使用本地C++过滤器API。**

- 在传输的请求，响应流或两者同时，提供头部，正文和尾部的检查；
- 对头部和尾部进行修改；
- 阻止或者缓冲完整的请求/响应正文，进行检查；
- 对上游主机执行异步HTTP调用。这样可以在缓冲正文数据的同时执行处理，以便当调用完成时，可以修改上行头部；
- 直接执行响应并跳过接下来的迭代过滤器。例如，一个脚本可以创建一个上游HTTP认证调用，然后直接用403响应代码进行响应。

### 配置

- [v1 API 参考](../../v1APIreference/HTTPfilters/Lua.md)
- [v2 API 参考](../../v2APIreference/Filters/HTTPfilters/Lua.md)

### 脚本示例
本节提供了一些Lua脚本的一些具体的例子，作为一个更简单介绍和快速入门。有关支持的API的更多详细信息，请参阅[流处理API](#流处理API)。

```
-- Called on the request path.
function envoy_on_request(request_handle)
  -- Wait for the entire request body and add a request header with the body size.
  request_handle:headers():add("request_body_size", request_handle:body():length())
end

-- Called on the response path.
function envoy_on_response(response_handle)
  -- Wait for the entire response body and a response header with the the body size.
  response_handle:headers():add("response_body_size", response_handle:body():length())
  -- Remove a response header named 'foo'
  response_handle:headers():remove("foo")
end
```

```
function envoy_on_request(request_handle)
  -- Make an HTTP call to an upstream host with the following headers, body, and timeout.
  local headers, body = request_handle:httpCall(
  "lua_cluster",
  {
    [":method"] = "POST",
    [":path"] = "/",
    [":authority"] = "lua_cluster"
  },
  "hello world",
  5000)

  -- Add information from the HTTP call into the headers that are about to be sent to the next
  -- filter in the filter chain.
  request_handle:headers():add("upstream_foo", headers["foo"])
  request_handle:headers():add("upstream_body_size", #body)
end
```

```
function envoy_on_request(request_handle)
  -- Make an HTTP call.
  local headers, body = request_handle:httpCall(
  "lua_cluster",
  {
    [":method"] = "POST",
    [":path"] = "/",
    [":authority"] = "lua_cluster"
  },
  "hello world",
  5000)

  -- Response directly and set a header from the HTTP call. No further filter iteration
  -- occurs.
  request_handle:respond(
    {[":status"] = "403",
     ["upstream_foo"] = headers["foo"]},
    "nope")
end
```

### 流处理API

当Envoy在配置中加载脚本时，它会查找脚本中定义的两个全局函数：

```
function envoy_on_request(request_handle)
end

function envoy_on_response(response_handle)
end
```
一个脚本可以定义这两个函数中的一个或两个。在请求路径中，Envoy将运行`envoy_on_request`函数作为一个协程，传递一个API句柄。在响应路径中，Envoy将运行`envoy_on_response`作为协程，传递一个API句柄。

**注意：与Envoy的所有交互，都是通过传输流来实现的。流句柄不应该被保存到任何全局变量，不应该在协程之外使用。如果句柄使用不当，Envoy将会失败。**

支持以下流处理方法：

- **headers()**</br>
```
headers = handle:headers()
```
返回流的头部对象。只要头部还没有被发送到下一个过滤器的头部链中，该头部就可以被修改。例如，可以在`httpCall()`之后或在`body()`调用返回之后修改它们。如果在任何其他情况下修改了头部，脚本将会失败。

 返回一个[头部对象](#头部对象API)。

- **body()**</br>
```
body = handle:body()
```
返回流的主体（body）。这个调用将使的Envoy切换（yield）脚本，直到整个主体被缓冲。请注意，所有缓冲必须遵守流量控制政策。Envoy不会缓冲超过连接管理器所允许的数据范围。

 返回一个[缓冲对象](#缓存API)。

- **bodyChunks()**</br>
```
iterator = handle:bodyChunks()
```
返回一个迭代器，它可以用来迭代所有接收到的正文数据块。Envoy将在处理大块数据时，切换(yield)脚本，但不会缓冲它们。这可以用来检查数据流。
```
for chunk in request_handle:bodyChunks() do
  request_handle:log(0, chunk:length())
end
```
每次迭代器返回都是一个[缓冲对象](#缓存API)。

- **trailers()**</br>
```
trailers = handle:trailers()
```
返回流的尾部。如果没有尾部，可能会返回零。尾部在发送到下一个过滤器之前可能会被修改。

 返回一个[头标对象](#头部对象API)。

- **log*()**</br>
```
handle:logTrace(message)
handle:logDebug(message)
handle:logInfo(message)
handle:logWarn(message)
handle:logErr(message)
handle:logCritical(message)
```
使用Envoy的应用程序日志记录消息。参数`message`是需要记录的字符串。

- **httpCall()**</br>
```
headers, body = handle:httpCall(cluster, headers, body, timeout)
```
对上游主机进行HTTP调用。Envoy将切换脚本，直到调用完成或有错误。`cluster`是对应到群集管理器配置的群集字符串。`headers`是要发送的`key/value`键值对的列表。请注意，必须设置`:method`，`:path`和`:authority`头部。`body`是可选字符串，需要发送的数据主体。`timeout`是一个整数，指定以毫秒为单位的调用超时。

 返回是响应的`headers`，以及其`body`字符串。如果没有主体可能是`null`。

- **respond()**</br>
```
handle:respond(headers, body)
```
立即作出响应，不要进行进一步的过滤器迭代。此调用仅在请求流中有效。此外，只有在请求头还没有传递给后续过滤器的情况下，响应才是可能的。意思是，下面的Lua代码是错误的：
```
function envoy_on_request(request_handle)
  for chunk in request_handle:bodyChunks() do
    request_handle:respond(
      {[":status"] = "100"},
      "nope")
  end
end
```
`headers`是要发送的键值对的列表。请注意，必须设置`:status`头部。`body`是一个字符串，作为可选的响应主体，可能是`nil`。

### 头部对象API

- **add()**</br>
```
headers:add(key, value)
```
为头部对象添加一个头部。`key`是提供头部键的字符串。`value`是一个提供头部值的字符串。

- **get()**</br>
```
headers:get(key)
```
获取头部对象头部值，参数`key`为所对应的头部键。返回一个字符串作为头部值，如果没有这样的头部则返回`nil`。


- **__pairs()**</br>
```
for key, value in pairs(headers) do
end
```
遍历每个头部键。返回的`key`是头部键的字符串。返回的`value`是对应的头部值字符串。

    **注意：在当前的实现中，在迭代期间不能修改头部。 另外，如果需要在迭代之后修改头部，则必须完成迭代。意味着，不要使用break或其他机制提前退出循环。但这可能会在未来版本中解除这个限制。**

- **remove()**</br>
```
headers:remove(key)
```
删除头部键值对。入参`key`对应的头部键值将会被删除。


### 缓存API

- **length()**
```
size = buffer:length()
```
获取缓冲区的大小（以字节为单位）。返回一个整数。

- **getBytes()**
```
buffer:getBytes(index, length)
```
从缓冲区获取字节。默认情况下，Envoy不会将所有缓冲区字节复制到Lua中。这将导致一个缓冲区段被复制。`index`是一个整数，并提供要复制的缓冲区起始索引。`length`是一个整数并提供要复制的缓冲区长度。`index`+`length`必须小于缓冲区长度。




## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)