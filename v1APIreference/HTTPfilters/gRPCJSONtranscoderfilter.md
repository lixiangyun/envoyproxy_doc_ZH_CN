## gRPC-JSON转码过滤器

gRPC-JSON转码[配置概述](../../Configurationreference/HTTPfilters/gRPCJSONtranscoderfilter.md)。

### gRPC-JSON转码配置
过滤器配置所需要描述符文件以及要转码的gRPC服务列表。
```
{
  "name": "grpc_json_transcoder",
  "config": {
    "proto_descriptor": "proto.pb",
    "services": ["grpc.service.Service"],
    "print_options": {
      "add_whitespace": false,
      "always_print_primitive_fields": false,
      "always_print_enums_as_ints": false,
      "preserve_proto_field_names": false
    }
  }
}
```
- **proto_descriptor**<br />
	(required, string) 为gRPC服务提供二进制`protobuf`描述符集。描述符集必须包含在服务中使用的所有类型。确保为`protoc`使用`--include_import`选项。


    要为gRPC服务生成一个`protobuf`描述符集，在运行`protoc`之前，还需要从Github中克隆`googleapis`仓库，因为在`include`路径中需要`annotations.proto`。

    ```
    git clone https://github.com/googleapis/googleapis
    GOOGLEAPIS_DIR=<your-local-googleapis-folder>
    ```

    然后运行`protoc`从`bookstore.proto`生成描述符集：

    ```
    protoc -I$(GOOGLEAPIS_DIR) -I. --include_imports --include_source_info \
      --descriptor_set_out=proto.pb test/proto/bookstore.proto
    ```
    如果您有多个原始源文件，您可以在一个命令中传递所有这些文件。    

- **services**<br />
	(required, array) 提供代码转换器进行转码服务的服务器名称列表。如果服务名称在`proto_descriptor`中不存在，Envoy将启动失败。`proto_descriptor`可能包含比这里指定的服务名称更多的服务，但是它们不会进行转码。

- **print_options**<br />
	(optional, object) 响应json的控制选项。这些选项直接传递给[JsonPrintOptions](https://developers.google.com/protocol-buffers/docs/reference/cpp/google.protobuf.util.json_util#JsonPrintOptions)。有效的选项是：

    - **add_whitespace**<br />
	(optional, boolean) 是否添加空格，换行符和缩进以使输出的JSON易于阅读。默认为false。

    - **always_print_primitive_fields**<br />
	(optional, boolean) 是否始终打印原始字段。默认情况下，具有默认值的原始字段将在JSON输出中被省略。例如，设置为0的`int32`字段将被省略。将此标志设置为true，将覆盖默认行为并打印原始字段，而不考虑其值。默认为false。

    - **always_print_enums_as_ints**<br />
	(optional, boolean) 是否始终打印枚举作为整数。默认情况下，它们呈现为字符串。默认为false。

    - **preserve_proto_field_names**<br />
	(optional, boolean) 是否保留原始字段名称。默认情况下，`protobuf`将使用`json_name`选项生成JSON字段名称，或者按照下面的顺序生成较低的骆驼大小写。设置此标志将保留原始字段名称。默认为false。


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)