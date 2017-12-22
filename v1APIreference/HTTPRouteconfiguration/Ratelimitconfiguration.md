### Rate limit configuration
Global rate limiting architecture overview.

```
{
  "stage": "...",
  "disable_key": "...",
  "actions": []
}
```
- **stage**</br>
	([optional](#), integer) Refers to the stage set in the filter. The rate limit configuration only applies to filters with the same stage number. The default stage number is 0.


NOTE: The filter supports a range of 0 - 10 inclusively for stage numbers.

- **disable_key**</br>
	([optional](#), string) The key to be set in runtime to disable this rate limit configuration.

- **actions**</br>
	([required](#), array) A list of actions that are to be applied for this rate limit configuration. Order matters as the actions are processed sequentially and the descriptor is composed by appending descriptor entries in that sequence. If an action cannot append a descriptor entry, no descriptor is generated for the configuration. See composing actions for additional documentation.

### Actions
```
{
  "type": "..."
}
```
- **type**</br>
	([required](#), string) The type of rate limit action to perform. The currently supported action types are source_cluster, destination_cluster , request_headers, remote_address, generic_key and header_value_match.

### Source Cluster
```
{
  "type": "source_cluster"
}
```
### The following descriptor entry is appended to the descriptor:

	(["source_cluster"](#), "<local service cluster>")

<local service cluster> is derived from the --service-cluster option.

### Destination Cluster
```
{
  "type": "destination_cluster"
}
```
### The following descriptor entry is appended to the descriptor:

	(["destination_cluster"](#), "<routed target cluster>")

### Once a request matches against a route table rule, a routed cluster is determined by one of the following route table configuration settings:

cluster indicates the upstream cluster to route to.
weighted_clusters chooses a cluster randomly from a set of clusters with attributed weight.
cluster_header indicates which header in the request contains the target cluster.
### Request Headers
```
{
  "type": "request_headers",
  "header_name": "...",
  "descriptor_key" : "..."
}
```
- **header_name**</br>
	([required](#), string) The header name to be queried from the request headers. The header’s value is used to populate the value of the descriptor entry for the descriptor_key.

- **descriptor_key**</br>
	([required](#), string) The key to use in the descriptor entry.

### The following descriptor entry is appended when a header contains a key that matches the header_name:

	(["<descriptor_key>"](#), "<header_value_queried_from_header>")

### Remote Address
```
{
  "type": "remote_address"
}
```
### The following descriptor entry is appended to the descriptor and is populated using the trusted address from x-forwarded-for:

	(["remote_address"](#), "<trusted address from x-forwarded-for>")

### Generic Key
```
{
  "type": "generic_key",
  "descriptor_value" : "..."
}
```
- **descriptor_value**</br>
	([required](#), string) The value to use in the descriptor entry.

### The following descriptor entry is appended to the descriptor:

	(["generic_key"](#), "<descriptor_value>")

### Header Value Match
```
{
  "type": "header_value_match",
  "descriptor_value" : "...",
  "expect_match" : "...",
  "headers" : []
}
```
- **descriptor_value**</br>
	([required](#), string) The value to use in the descriptor entry.

- **expect_match**</br>
	([optional](#), boolean) If set to true, the action will append a descriptor entry when the request matches the headers. If set to false, the action will append a descriptor entry when the request does not match the headers. The default value is true.

- **headers**</br>
	([required](#), array) Specifies a set of headers that the rate limit action should match on. The action will check the request’s headers against all the specified headers in the config. A match will happen if all the headers in the config are present in the request with the same values (or based on presence if the value field is not in the config).

The following descriptor entry is appended to the descriptor: .. code-block:: cpp

	([“header_match”](#), “<descriptor_value>”)

