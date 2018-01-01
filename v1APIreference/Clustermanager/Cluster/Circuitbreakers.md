### Circuit breakers
Circuit breaking architecture overview.
Priority routing architecture overview.
Circuit breaking settings can be specified individually for each defined priority. How the different priorities are used are documented in the sections of the configuration guide that use them.

```
{
  "default": "{...}",
  "high": "{...}"
}
```
- **default**<br />
	(optional, object) Settings object for default priority.

- **high**<br />
	(optional, object) Settings object for high priority.

### Per priority settings
```
{
  "max_connections": "...",
  "max_pending_requests": "...",
  "max_requests": "...",
  "max_retries": "...",
}
```
- **max_connections**<br />
	(optional, integer) The maximum number of connections that Envoy will make to the upstream cluster. If not specified, the default is 1024. See the circuit breaking overview for more information.

- **max_pending_requests**<br />
	(optional, integer) The maximum number of pending requests that Envoy will allow to the upstream cluster. If not specified, the default is 1024. See the circuit breaking overview for more information.

- **max_requests**<br />
	(optional, integer) The maximum number of parallel requests that Envoy will make to the upstream cluster. If not specified, the default is 1024. See the circuit breaking overview for more information.

- **max_retries**<br />
	(optional, integer) The maximum number of parallel retries that Envoy will allow to the upstream cluster. If not specified, the default is 3. See the circuit breaking overview for more information.



## 返回
- [上一级](../Cluster.md)
- [首页目录](../../../README.md)