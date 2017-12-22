##gRPC-JSON transcoder filter


gRPC-JSON transcoder configuration overview.

### Configure gRPC-JSON transcoder
The filter config for the filter requires the descriptor file as well as a list of the gRPC services to be transcoded.

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
- **proto_descriptor**</br>
	([required](#), string) Supplies the binary protobuf descriptor set for the gRPC services. The descriptor set has to include all of the types that are used in the services. Make sure to use the --include_import option for protoc.


To generate a protobuf descriptor set for the gRPC service, you’ll also need to clone the googleapis repository from Github before running protoc, as you’ll need annotations.proto in your include path.

git clone https://github.com/googleapis/googleapis
### GOOGLEAPIS_DIR=<your-local-googleapis-folder>
Then run protoc to generate the descriptor set from bookstore.proto:

protoc -I$(GOOGLEAPIS_DIR) -I. --include_imports --include_source_info \
  --descriptor_set_out=proto.pb test/proto/bookstore.proto
If you have more than one proto source files, you can pass all of them in one command.

- **services**</br>
	([required](#), array) A list of strings that supplies the service names that the transcoder will translate. If the service name doesn’t exist in proto_descriptor, Envoy will fail at startup. The proto_descriptor may contain more services than the service names specified here, but they won’t be translated.

- **print_options**</br>
	([optional](#), object) Control options for response json. These options are passed directly to JsonPrintOptions. Valid options are:


- **add_whitespace**</br>
	([optional](#), boolean) Whether to add spaces, line breaks and indentation to make the JSON output easy to read. Defaults to false.

- **always_print_primitive_fields**</br>
	([optional](#), boolean) Whether to always print primitive fields. By default primitive fields with default values will be omitted in JSON output. For example, an int32 field set to 0 will be omitted. Setting this flag to true will override the default behavior and print primitive fields regardless of their values. Defaults to false.

- **always_print_enums_as_ints**</br>
	([optional](#), boolean) Whether to always print enums as ints. By default they are rendered as strings. Defaults to false.

- **preserve_proto_field_names**</br>
	([optional](#), boolean) Whether to preserve proto field names. By default protobuf will generate JSON field names using the json_name option, or lower camel case, in that order. Setting this flag will preserve the original field names. Defaults to false.


## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)