## gRPC-JSON 转码器

gRPC-JSON 转码器[配置参考](../../../Configurationreference/HTTPfilters/gRPCJSONtranscoderfilter.md)。

### filter.http.GrpcJsonTranscoder
[filter.http.GrpcJsonTranscoder proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/transcoder.proto#L9)

```
{
  "proto_descriptor": "...",
  "services": [],
  "print_options": "{...}"
}
```

- **proto_descriptor**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 为gRPC服务提供二进制`protobuf`描述符集合。描述符集合必须包含服务中使用的所有类型。确保为`protoc`，并使用`--include_import`选项。

    要为gRPC服务生成一个`protobuf`描述符集，在运行`protoc`之前，还需要从Github中克隆google apis库，因为在`include`路径中需要`annotations.proto`。

    ```
    git clone https://github.com/googleapis/googleapis
    GOOGLEAPIS_DIR=<your-local-googleapis-folder>
    ```

    然后运行protoc从`bookstore.proto`生成描述符：

    ```
    protoc -I$(GOOGLEAPIS_DIR) -I. --include_imports --include_source_info \
    --descriptor_set_out=proto.pb test/proto/bookstore.proto
    ```
    如果您有许多原始源文件，则可以使用通过这个命令来传递所有文件。
    
- **services**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) 提供将要转换的服务名的字符串列表。如果服务名在`proto_descriptor`中不存在，Envoy则启动失败。`proto_descriptor`可能包含比这里指定的服务名称更多的服务，但是它们不会被转换。

- **print_options**<br />
	([filter.http.GrpcJsonTranscoder.PrintOptions](#filterhttpgrpcjsontranscoderprintoptions)) 响应JSON的控制选项。这些选项直接传递给[JsonPrintOptions](https://developers.google.com/protocol-buffers/docs/reference/cpp/google.protobuf.util.json_util#JsonPrintOptions)。

### filter.http.GrpcJsonTranscoder.PrintOptions

[filter.http.GrpcJsonTranscoder.PrintOptions proto](https://github.com/envoyproxy/data-plane-api/blob/master/api/filter/http/transcoder.proto#L39)

```
{
  "add_whitespace": "...",
  "always_print_primitive_fields": "...",
  "always_print_enums_as_ints": "...",
  "preserve_proto_field_names": "..."
}
```

- **add_whitespace**<br />
	([bool](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 是否添加空格，换行符和缩进以使输出的JSON易于阅读。默认为false。

- **always_print_primitive_fields**<br />
	([bool](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 是否始终打印原始字段。默认情况下，具有默认值的原始字段将在JSON输出中被省略。例如，设置为0的int32字段将被省略。将此标志设置为true，将覆盖默认行为并打印原始字段，而不考虑其值。默认为false。

- **always_print_enums_as_ints**<br />
	([bool](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 是否始终打印枚举作为整数。默认情况下，它们呈现为字符串。默认为false。

- **preserve_proto_field_names**<br />
	([bool](https://developers.google.com/protocol-buffers/docs/proto#scalar)) 是否保留原始字段名称。默认情况下，`protobuf`将使用`json_name`选项生成JSON字段名称，或者按照下面的顺序生成较低的骆驼风格的大小写。设置此标志将保留原始字段名称。默认为false。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

