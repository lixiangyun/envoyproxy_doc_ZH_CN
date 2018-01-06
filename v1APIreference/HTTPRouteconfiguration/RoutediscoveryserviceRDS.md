### Route discovery service (RDS)
```
{
  "cluster": "...",
  "route_config_name": "...",
  "refresh_delay_ms": "..."
}
```
- **cluster**<br />
	(required, string) The name of an upstream cluster that hosts the route discovery service. The cluster must run a REST service that implements the RDS HTTP API. NOTE: This is the name of a cluster defined in the cluster manager configuration, not the full definition of a cluster as in the case of SDS and CDS.

- **route_config_name**<br />
	(required, string) The name of the route configuration. This name will be passed to the RDS HTTP API. This allows an Envoy configuration with multiple HTTP listeners (and associated HTTP connection manager filters) to use different route configurations. By default, the maximum length of the name is limited to 60 characters. This limit can be increased by setting the --max-obj-name-len command line argument to the desired value.

- **refresh_delay_ms**<br />
	(optional, integer) The delay, in milliseconds, between fetches to the RDS API. Envoy will add an additional random jitter to the delay that is between zero and refresh_delay_ms milliseconds. Thus the longest possible refresh delay is 2 * refresh_delay_ms. Default value is 30000ms (30 seconds).

### REST API
### GET /v1/routes/(string: route_config_name)/(string: service_cluster)/(string: service_node)
Asks the route discovery service to return the route configuration for a particular route_config_name, service_cluster, and service_node. route_config_name corresponds to the RDS configuration parameter above. service_cluster corresponds to the --service-cluster CLI option. service_node corresponds to the --service-node CLI option. Responses are a single JSON object that contains a route configuration as defined in the route configuration documentation.

A new route configuration will be gracefully swapped in such that existing requests are not affected. This means that when a request starts, it sees a consistent snapshot of the route configuration that does not change for the duration of the request. Thus, if an update changes a timeout for example, only new requests will use the updated timeout value.

As a performance optimization, Envoy hashes the route configuration it receives from the RDS API and will only perform a full reload if the hash value changes.

### Attention

Route configurations that are loaded via RDS are not checked to see if referenced clusters are known to the cluster manager. The RDS API has been designed to work alongside the CDS API such that Envoy assumes eventually consistent updates. If a route references an unknown cluster a 404 response will be returned by the router filter.


## 返回
- [上一级](../HTTPRouteconfiguration.md)
- [首页目录](../../README.md)