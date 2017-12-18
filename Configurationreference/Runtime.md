## 运行时配置

[运行时配置](../Introduction/Architectureoverview/Runtimeconfiguration.md)是指包含本地文件的可重载配置。如果运行时未配置，则默认使用“null”，该程序会使用代码中内置的缺省值。

- [v1 API 参考](../v1APIreference/Runtime.md)
- [v2 API 参考](../v2APIreference/Bootstrap.md)

### 文件系统层
在配置参考的各个部分描述了可用的运行时配置。例如，以下是上游群集的运行时配置。

假定文件夹`/srv/runtime/v1`指向的是存放全局运行时配置的文件路径。以下是运行时的典型配置设置：
- symlink_root(根目录的符号链接): `/srv/runtime/current` 
- subdirectory(子目录): `envoy` 
- override_subdirectory(覆盖子目录): `envoy_override` 

其中`/srv/runtime/current`是链接到`/srv/runtime/v1`目录的符号链接。

运行时配置中的每个“.”表示新的目录层级，以`symlink_root + subdirectory`为目录根。例如，`health_check.min_interval`键展开之后具有以下完整文件路径（使用符号链接）：

```
/srv/runtime/current/envoy/health_check/min_interval
```

路径的末端部分是文件。文件内容为运行时配置值。在从文件中读取数值时，空格和换行将被忽略。

目录`override_subdirectory`与`--service-cluster`命令行选项配合一起使用。假设`--service-cluster`已被设置为`my-cluster`。Envoy将优先在以下完整路径中查找`health_check.min_interval`配置项：

```
/srv/runtime/current/envoy_override/my-cluster/health_check/min_interval
```

如果找到，该值将覆盖在主路径中查找到的任何值。这允许用户在全局默认值之外，自定义单个群集的运行时配置值。

### 注释
以＃开头的行被视为注释。合理的注释可以帮助理解所提供值的背景/原因。另外在一个空文件增加注释也是有用的，预留文件的部署，以便在需要的时候可以使用。

### 通过符号链接交换更新运行值
有两个步骤来更新任何运行时值。首先，创建整个全新运行时配置或者拷贝/更新所需的运行时配置。其次，使用以下命令或者等效命令，将旧的符号链接根自动交换到新的配置目录下：

```
/srv/runtime:~$ ln -s /srv/runtime/v2 new && mv -Tf new current
```

关于如何部署文件系统数据，以及相关垃圾回收等问题，超出了本文档的范围。

### 统计
文件系统在运行时会收集一些统计信息，以`runtime.`命名空间。

|	名称	|	类型	|	描述	|
|	 -----------------------	|	 -----------------------	|	 -----------------------	|
|	load_error	|	Counter	|	因错误而导致加载尝试总数	|
|	override_dir_not_exists	|	Counter	|	未使用覆盖目录的加载总数	|
|	override_dir_exists	|	Counter	|	使用覆盖目录的加载总数	|
|	load_success	|	Counter	|	成功加载的尝试总数	|
|	num_keys	|	Gauge	|	当前加载的键值数量	|



## 返回
- [上一级](../Configurationreference.md)
- [首页目录](../README.md)
