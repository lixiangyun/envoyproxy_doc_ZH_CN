## Service discovery service
Service discovery service architecture overview.

```
{
  "cluster": "{...}",
  "refresh_delay_ms": "{...}"
}
```
- **cluster**<br />
	(required, object) A standard definition of an upstream cluster that hosts the service discovery service. The cluster must run a REST service that implements the SDS HTTP API.

- **refresh_delay_ms**<br />
	(required, integer) The delay, in milliseconds, between fetches to the SDS API for each configured SDS cluster. Envoy will add an additional random jitter to the delay that is between zero and refresh_delay_ms milliseconds. Thus the longest possible refresh delay is 2 * refresh_delay_ms.

### REST API
### Envoy expects the service discovery service to expose the following API (See Lyft’s reference implementation):

### GET /v1/registration/(string: service_name)
Asks the discovery service to return all hosts for a particular service_name. service_name corresponds to the service_name cluster parameter. Responses use the following JSON schema:

```
{
  "hosts": []
}
```
- **hosts**<br />
	(required, array) A list of hosts that make up the service.

### Host JSON
```
{
  "ip_address": "...",
  "port": "...",
  "tags": {
    "az": "...",
    "canary": "...",
    "load_balancing_weight": "..."
  }
}
```
- **ip_address**<br />
	(required, string) The IP address of the upstream host.

- **port**<br />
	(required, integer) The port of the upstream host.

- **az**<br />
	(optional, string) The optional zone of the upstream host. Envoy uses the zone for various statistics and load balancing tasks documented elsewhere.

- **canary**<br />
	(optional, boolean) The optional canary status of the upstream host. Envoy uses the canary status for various statistics and load balancing tasks documented elsewhere.

- **load_balancing_weight**<br />
	(optional, integer) The optional load balancing weight of the upstream host, in the range 1 - 100. Envoy uses the load balancing weight in some of the built in load balancers.


## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)