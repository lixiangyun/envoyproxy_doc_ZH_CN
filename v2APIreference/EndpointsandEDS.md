## 服务发现

### Endpoints and EDS
### LbEndpoint
[LbEndpoint proto]()

An Endpoint that Envoy can route traffic to.

```
{
  "endpoint": "{...}",
  "metadata": "{...}",
  "load_balancing_weight": "{...}"
}
```
- **endpoint**</br>
	([Endpoint](#)) Upstream host identifier

- **metadata**</br>
	([Metadata](#)) The endpoint metadata specifies values that may be used by the load balancer to select endpoints in a cluster for a given request. The filter name should be specified as envoy.lb. An example boolean key-value pair is canary, providing the optional canary status of the upstream host. This may be matched against in a route’s ForwardAction metadata_match field to subset the endpoints considered in cluster load balancing.

- **load_balancing_weight**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) The optional load balancing weight of the upstream host, in the range 1 - 128. Envoy uses the load balancing weight in some of the built in load balancers. The load balancing weight for an endpoint is divided by the sum of the weights of all endpoints in the endpoint’s locality to produce a percentage of traffic for the endpoint. This percentage is then further weighted by the endpoint’s locality’s load balancing weight from LocalityLbEndpoints. If unspecified, each host is presumed to have equal weight in a locality.


### Attention

The limit of 128 is somewhat arbitrary, but is applied due to performance concerns with the current implementation and can be removed when this issue is fixed.

### LocalityLbEndpoints
[LocalityLbEndpoints proto]()

A group of endpoints belonging to a Locality. One can have multiple LocalityLbEndpoints for a locality, but this is generally only done if the different groups need to have different load balancing weights or different priorities.

```
{
  "locality": "{...}",
  "lb_endpoints": [],
  "load_balancing_weight": "{...}",
  "priority": "..."
}
```
- **locality**</br>
	([Locality](#)) Identifies location of where the upstream hosts run.

- **lb_endpoints**</br>
	([LbEndpoint](#)) The group of endpoints belonging to the locality specified.

- **load_balancing_weight**</br>
	([UInt32Value](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#uint32value)) Optional: Per priority/region/zone/sub_zone weight - range 1-128. The load balancing weight for a locality is divided by the sum of the weights of all localities at the same priority level to produce the effective percentage of traffic for the locality.


Weights must be specified for either all localities in a given priority level or none.

If unspecified, each locality is presumed to have equal weight in a cluster.

### Attention

The limit of 128 is somewhat arbitrary, but is applied due to performance concerns with the current implementation and can be removed when this issue is fixed.

- **priority**</br>
	([uint32](https://developers.google.com/protocol-buffers/docs/proto#scalar)) Optional: the priority for this LocalityLbEndpoints. If unspecified this will default to the highest priority (0).


Under usual circumstances, Envoy will only select endpoints for the highest priority (0). In the event all endpoints for a particular priority are unavailable/unhealthy, Envoy will fail over to selecting endpoints for the next highest priority group.

Priorities should range from 0 (highest) to N (lowest) without skipping.

### ClusterLoadAssignment
[ClusterLoadAssignment proto]()

Each route from RDS will map to a single cluster or traffic split across clusters using weights expressed in the RDS WeightedCluster.

With EDS, each cluster is treated independently from a LB perspective, with LB taking place between the Localities within a cluster and at a finer granularity between the hosts within a locality. For a given cluster, the effective weight of a host is its load_balancing_weight multiplied by the load_balancing_weight of its Locality.

```
{
  "cluster_name": "...",
  "endpoints": [],
  "policy": "{...}"
}
```
- **cluster_name**</br>
	([string](https://developers.google.com/protocol-buffers/docs/proto#scalar), REQUIRED) Name of the cluster. This will be the service_name value if specified in the cluster EdsClusterConfig.

- **endpoints**</br>
	([LocalityLbEndpoints](#)) List of endpoints to load balance to.

- **policy**</br>
	([ClusterLoadAssignment.Policy](#)) Load balancing policy settings.

### ClusterLoadAssignment.Policy
[ClusterLoadAssignment.Policy proto]()

Load balancing policy settings.

```
{
  "drop_overload": "..."
}
```
- **drop_overload**</br>
	([double](#)) Percentage of traffic (0-100) that should be dropped. This action allows protection of upstream hosts should they unable to recover from an outage or should they be unable to autoscale and hence overall incoming traffic volume need to be trimmed to protect them.


### Note

- **v2 API difference: This is known as maintenance mode in v1.**</br>


## 返回
- [上一级](../v2APIreference.md)
- [首页目录](../README.md)