## gRPC-JSON 转码器

- **gRPC-JSON transcoder**<br />
- **gRPC-JSON transcoder configuration overview.**<br />

- **filter.http.GrpcJsonTranscoder**<br />
[filter.http.GrpcJsonTranscoder proto]()

```
{
  "proto_descriptor": "...",
  "services": [],
  "print_options": "{...}"
}
```
- **proto_descriptor**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Supplies the binary protobuf descriptor set for the gRPC services. The descriptor set has to include all of the types that are used in the services. Make sure to use the --include_import option for protoc.


To generate a protobuf descriptor set for the gRPC service, you’ll also need to clone the googleapis repository from Github before running protoc, as you’ll need annotations.proto in your include path.

- **git clone https://github.com/googleapis/googleapis**<br />
### GOOGLEAPIS_DIR=<your-local-googleapis-folder>
Then run protoc to generate the descriptor set from bookstore.proto:

- **protoc -I$(GOOGLEAPIS_DIR) -I. --include_imports --include_source_info \**<br />
--descriptor_set_out=proto.pb test/proto/bookstore.proto
If you have more than one proto source files, you can pass all of them in one command.

- **services**<br />
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) A list of strings that supplies the service names that the transcoder will translate. If the service name doesn’t exist in proto_descriptor, Envoy will fail at startup. The proto_descriptor may contain more services than the service names specified here, but they won’t be translated.

- **print_options**<br />
	([filter.http.GrpcJsonTranscoder.PrintOptions](#)) Control options for response JSON. These options are passed directly to JsonPrintOptions.

### filter.http.GrpcJsonTranscoder.PrintOptions

[filter.http.GrpcJsonTranscoder.PrintOptions proto]()

```
{
  "add_whitespace": "...",
  "always_print_primitive_fields": "...",
  "always_print_enums_as_ints": "...",
  "preserve_proto_field_names": "..."
}
```
- **add_whitespace**<br />
	([bool](#)) Whether to add spaces, line breaks and indentation to make the JSON output easy to read. Defaults to false.

- **always_print_primitive_fields**<br />
	([bool](#)) Whether to always print primitive fields. By default primitive fields with default values will be omitted in JSON output. For example, an int32 field set to 0 will be omitted. Setting this flag to true will override the default behavior and print primitive fields regardless of their values. Defaults to false.

- **always_print_enums_as_ints**<br />
	([bool](#)) Whether to always print enums as ints. By default they are rendered as strings. Defaults to false.

- **preserve_proto_field_names**<br />
	([bool](#)) Whether to preserve proto field names. By default protobuf will generate JSON field names using the json_name option, or lower camel case, in that order. Setting this flag will preserve the original field names. Defaults to false.



## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../../README.md)

