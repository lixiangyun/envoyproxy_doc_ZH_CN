## TLS客户端身份认证

- 客户端TLS认证过滤器[架构概述](../../Introduction/Architectureoverview/TLS.md)
- [v1 API 参考](../../v1APIreference/Networkfilters/ClientTLSauthentication.md)
- [v2 API 参考](../../v2APIreference/Filters/Networkfilters/ClientTLSauthentication.md)

### 统计

每个配置的TLS客户端身份验证过滤器统计信息均以`auth.clientssl.<stat_prefix>`为根。 统计如下：

|	名称	|	类型	|	描述	|
|-----------------------|-----------------------|-----------------------|
|	update_success	|	Counter	|	身份更新成功总数	|
|	update_failure	|	Counter	|	身份更新失败总数	|
|	auth_no_ssl	    |	Counter	|	由于没有TLS而被忽略的连接总数	|
|	auth_ip_white_list	|	Counter	|	由于IP白名单允许的连接总数	|
|	auth_digest_match	|	Counter	|	由于证书匹配而允许连接总数	|
|	auth_digest_no_match	|	Counter	|	由于没有证书匹配，被拒绝的连接总数	|
|	total_principals	|	Gauge	|	总加载的身份信息	|


### REST API

**`GET /v1/certs/list/approved`**

身份验证过滤器将每间隔调用一次此API，以刷新获取当前已批准的证书/主体列表。预期的JSON响应，如下所示：

```
{
  "certificates": []
}
```

**certificates** <br />
*(必选,数组)* 列出已批准的证书/身份。

每个证书对象被定义为：

```
{
  "fingerprint_sha256": "...",
}
```

**fingerprint_sha256** <br />
*(必选,字符串)* 经批准的客户端证书的SHA256哈希。Envoy将会用这个哈希值与客户端所提供的证书进行匹配，以确定是否存在摘要匹配。


## 返回
- [上一级](../Networkfilters.md)
- [首页目录](../../README.md)
