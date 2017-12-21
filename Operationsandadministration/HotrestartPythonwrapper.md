## 热重启

通常情况下，Envoy将热启动以支持配置和二进制的更新升级。但是，在许多情况下，用户会希望使用标准的进程管理器，如monit，runit等。我们提供[/restarter/hot-restarter.py](https://github.com/envoyproxy/envoy/blob/master//restarter/hot-restarter.py)来使实现这个功能。

启动程序是这样调用的：

```
hot-restarter.py start_envoy.sh
```

start_envoy.sh可以参考使用salt/jinja类似的语法：

```
#!/bin/bash

ulimit -n {{ pillar.get('envoy_max_open_files', '102400') }}
exec /usr/sbin/envoy -c /etc/envoy/envoy.cfg --restart-epoch $RESTART_EPOCH --service-cluster {{ grains['cluster_name'] }} --service-node {{ grains['service_node'] }} --service-zone {{ grains.get('ec2_availability-zone', 'unknown') }}
```

环境变量`RESTART_EPOCH`需要在每次重新启动时由重启动程序设置，并可以传递给`--restart-epoch`选项。

重启程序需要处理以下信号：

- **SIGTERM**：将干净地终止所有子进程并退出。
- **SIGHUP**：将重新调用热重启脚本，并且使用一个传递参数内容来重启。
- **SIGCHLD**：如果有任何子进程意外关闭，那么重启脚本将关闭一切并退出以避免处于意外状态。然后，进程控制管理器应该重新启动脚本以再次启动Envoy。
- **SIGUSR1**：将被转发给Envoy进程，作为重新打开所有访问日志的信号。这用于移动并重新打开日志原子操作。

## 返回
- [上一级](../Operationsandadministration.md)
- [首页目录](../README.md)
