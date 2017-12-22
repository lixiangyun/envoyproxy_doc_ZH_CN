### Cluster discovery service
```
{
  "cluster": "{...}",
  "refresh_delay_ms": "..."
}
```
- **cluster**</br>
	([required](#), object) A standard definition of an upstream cluster that hosts the cluster discovery service. The cluster must run a REST service that implements the CDS HTTP API.

- **refresh_delay_ms**</br>
	([optional](#), integer) The delay, in milliseconds, between fetches to the CDS API. Envoy will add an additional random jitter to the delay that is between zero and refresh_delay_ms milliseconds. Thus the longest possible refresh delay is 2 * refresh_delay_ms. Default value is 30000ms (30 seconds).

### REST API
### GET /v1/clusters/(string: service_cluster)/(string: service_node)
Asks the discovery service to return all clusters for a particular service_cluster and service_node. service_cluster corresponds to the --service-cluster CLI option. service_node corresponds to the --service-node CLI option. Responses use the following JSON schema:

```
{
  "clusters": []
}
```
- **clusters**</br>
	([Required](#), array) A list of clusters that will be dynamically added/modified within the cluster manager. Envoy will reconcile this list with the clusters that are currently loaded and either add/modify/remove clusters as necessary. Note that any clusters that are statically defined within the Envoy configuration cannot be modified via the CDS API.



## 返回
- [上一级](../Clustermanager.md)
- [首页目录](../../README.md)