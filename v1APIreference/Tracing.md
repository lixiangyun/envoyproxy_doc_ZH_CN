## Tracing

### Tracing
The tracing configuration specifies global settings for the HTTP tracer used by Envoy. The configuration is defined on the server’s top level configuration. Envoy may support other tracers in the future, but right now the HTTP tracer is the only one supported.

```
{
  "http": {
    "driver": "{...}"
  }
}
```
- **http**<br />
	([optional](#), object) Provides configuration for the HTTP tracer.

- **driver**<br />
	([optional](#), object) Provides the driver that handles trace and span creation.

Currently LightStep and Zipkin drivers are supported.

### LightStep driver
```
{
  "type": "lightstep",
  "config": {
    "access_token_file": "...",
    "collector_cluster": "..."
  }
}
```
- **access_token_file**<br />
	([required](#), string) File containing the access token to the LightStep API.

- **collector_cluster**<br />
	([required](#), string) The cluster manager cluster that hosts the LightStep collectors.

### Zipkin driver
```
{
  "type": "zipkin",
  "config": {
    "collector_cluster": "...",
    "collector_endpoint": "..."
  }
}
```
- **collector_cluster**<br />
	([required](#), string) The cluster manager cluster that hosts the Zipkin collectors. Note that the Zipkin cluster must be defined under clusters in the cluster manager configuration section.

- **collector_endpoint**<br />
	([optional](#), string) The API endpoint of the Zipkin service where the spans will be sent. When using a standard Zipkin installation, the API endpoint is typically /api/v1/spans, which is the default value.



## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

