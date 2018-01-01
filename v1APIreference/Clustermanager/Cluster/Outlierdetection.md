### Outlier detection
```
{
  "consecutive_5xx": "...",
  "consecutive_gateway_failure": "...",
  "interval_ms": "...",
  "base_ejection_time_ms": "...",
  "max_ejection_percent": "...",
  "enforcing_consecutive_5xx" : "...",
  "enforcing_consecutive_gateway_failure" : "...",
  "enforcing_success_rate" : "...",
  "success_rate_minimum_hosts" : "...",
  "success_rate_request_volume" : "...",
  "success_rate_stdev_factor" : "..."
}
```
- **consecutive_5xx**<br />
	(optional, integer) The number of consecutive 5xx responses before a consecutive 5xx ejection occurs. Defaults to 5.

- **consecutive_gateway_failure**<br />
	(optional, integer) The number of consecutive “gateway errors” (502, 503 and 504 responses), including those raised by Envoy for connection errors, before a consecutive gateway failure ejection occurs. Defaults to 5.

- **interval_ms**<br />
	(optional, integer) The time interval between ejection analysis sweeps. This can result in both new ejections as well as hosts being returned to service. Defaults to 10000ms or 10s.

- **base_ejection_time_ms**<br />
	(optional, integer) The base time that a host is ejected for. The real time is equal to the base time multiplied by the number of times the host has been ejected. Defaults to 30000ms or 30s.

- **max_ejection_percent**<br />
	(optional, integer) The maximum % of hosts in an upstream cluster that can be ejected due to outlier detection. Defaults to 10%.

- **enforcing_consecutive_5xx**<br />
	(optional, integer) The % chance that a host will be actually ejected when an outlier status is detected through consecutive 5xx. This setting can be used to disable ejection or to ramp it up slowly. Defaults to 100 with 1% granularity.

- **enforcing_consecutive_gateway_failure**<br />
	(optional, integer) The % chance that a host will be actually ejected when an outlier status is detected through consecutive gateway failure. This setting can be used to disable ejection or to ramp it up slowly. Defaults to 0 with 1% granularity.

- **enforcing_success_rate**<br />
	(optional, integer) The % chance that a host will be actually ejected when an outlier status is detected through success rate statistics. This setting can be used to disable ejection or to ramp it up slowly. Defaults to 100 with 1% granularity.

- **success_rate_minimum_hosts**<br />
	(optional, integer) The number of hosts in a cluster that must have enough request volume to detect success rate outliers. If the number of hosts is less than this setting, outlier detection via success rate statistics is not performed for any host in the cluster. Defaults to 5.

- **success_rate_request_volume**<br />
	(optional, integer) The minimum number of total requests that must be collected in one interval (as defined by interval_ms above) to include this host in success rate based outlier detection. If the volume is lower than this setting, outlier detection via success rate statistics is not performed for that host. Defaults to 100.

- **success_rate_stdev_factor**<br />
	(optional, integer) This factor is used to determine the ejection threshold for success rate outlier ejection. The ejection threshold is used as a measure to determine when a particular host has fallen below an acceptable success rate. The ejection threshold is the difference between the mean success rate, and the product of this factor and the standard deviation of the mean success rate: mean - (stdev * success_rate_stdev_factor). This factor is divided by a thousand to get a double. That is, if the desired factor is 1.9, the runtime value should be 1900. Defaults to 1900.

Each of the above configuration values can be overridden via runtime values.


## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)