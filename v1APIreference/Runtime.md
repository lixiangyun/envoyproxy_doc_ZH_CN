## Runtime

Runtime configuration overview.

```
{
  "symlink_root": "...",
  "subdirectory": "...",
  "override_subdirectory": "..."
}
```
- **symlink_root**<br />
	(required, string) The implementation assumes that the file system tree is accessed via a symbolic link. An atomic link swap is used when a new tree should be switched to. This parameter specifies the path to the symbolic link. Envoy will watch the location for changes and reload the file system tree when they happen.

- **subdirectory**<br />
	(required, string) Specifies the subdirectory to load within the root directory. This is useful if multiple systems share the same delivery mechanism. Envoy configuration elements can be contained in a dedicated subdirectory.

- **override_subdirectory**<br />
	(optional, string) Specifies an optional subdirectory to load within the root directory. If specified and the directory exists, configuration values within this directory will override those found in the primary subdirectory. This is useful when Envoy is deployed across many different types of servers. Sometimes it is useful to have a per service cluster directory for runtime configuration. See below for exactly how the override directory is used.



## 返回
- [上一级](../v1APIreference.md)
- [首页目录](../README.md)

