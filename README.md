# ENVOY智能代理中文参考文档 v1.5.0

**注意：水平有限，仅供参考，如果有任何疑问或者建议，请提交[issue](https://github.com/lixiangyun/envoyproxy_doc_ZH_CN/issues)讨论。**

**版权申明**
- 未经版权所有者明确授权，禁止发行本文档及其被实质上修改的版本。 
- 未经版权所有者事先授权，禁止将此作品及其衍生作品以标准（纸质）书籍形式发行。 

**联系方式：linimbus@126.com**

## 关于本文档
- **简介**：介绍Envoy相关概念，以及总体的架构设计和常用的部署方式等
- **安装**：如何通过Docker构建、安装Envoy
- **配置**：既有的V1/V2 API的详细配置说明，以及相关的统计、运行时的APIs
- **操作**：关于Envoy操作方式，如命令行、热更新、管理接口、统计概况等
- **扩展**：关于如何为Envoy编写自定义过滤器
- **V1 API 参考**：老的V1 API的详细配置说明
- **V2 API 参考**：新的V2 API的详细配置说明

## 相关连接
- [官方手册](https://www.envoyproxy.io/docs/envoy/v1.5.0/)
- [官方内源项目](https://github.com/envoyproxy/envoy)

## 正文目录

### [简介](Introduction.md)
- [Envoy是什么](Introduction/WhatisEnvoy.md)
- [架构介绍](Introduction/Architectureoverview.md)
- [部署](Introduction/Deploymenttypes.md)
- [业界对比](Introduction/Comparisontosimilarsystems.md)
- [获得帮助](Introduction/Gettinghelp.md)
- [历史版本](Introduction/Versionhistory.md)

### [编译&安装](Buildingandinstallation.md)
- [编译](Buildingandinstallation/Building.md)
- [参考配置](Buildingandinstallation/Referenceconfigurations.md)
- [演示沙箱](Buildingandinstallation/Sandboxes.md)
- [工具](Buildingandinstallation/Tools.md)

### [参考配置](Configurationreference.md)
- [V1 API 概述](Configurationreference/Overviewv1API.md)
- [V2 API 概述](Configurationreference/Overviewv2API.md)
- [监听器](Configurationreference/Listeners.md)
- [网络过滤器](Configurationreference/Networkfilters.md)
- [HTTP连接管理器](Configurationreference/HTTPconnectionmanager.md)
- [HTTP过滤器](Configurationreference/HTTPfilters.md)
- [集群管理](Configurationreference/Clustermanager.md)
- [访问日志](Configurationreference/Accesslogging.md)
- [限速服务](Configurationreference/Ratelimitservice.md)
- [运行时配置](Configurationreference/Runtime.md)
- [路由表检查工具](Configurationreference/Routetablechecktool.md)

### [运维&管理](Operationsandadministration.md)
- [命令行选项](Operationsandadministration/Commandlineoptions.md)
- [热重启](Operationsandadministration/HotrestartPythonwrapper.md)
- [管理接口](Operationsandadministration/Administrationinterface.md)
- [统计概述](Operationsandadministration/Statisticsoverview.md)
- [运行时配置](Operationsandadministration/Runtime.md)
- [文件系统](Operationsandadministration/Filesystemflags.md)

### [自定义扩展示例](ExtendingEnvoyforcustomusecases.md)

### [V1 API参考](v1APIreference.md)
- [监听器](v1APIreference/Listeners.md)
- [网络过滤器](v1APIreference/Networkfilters.md)
- [HTTP路由配置](v1APIreference/HTTPRouteconfiguration.md)
- [HTTP过滤器](v1APIreference/HTTPfilters.md)
- [集群管理](v1APIreference/Clustermanager.md)
- [访问日志](v1APIreference/Accesslogging.md)
- [管理接口](v1APIreference/Administrationinterface.md)
- [限速服务](v1APIreference/Ratelimitservice.md)
- [运行时配置](v1APIreference/Runtime.md)
- [跟踪](v1APIreference/Tracing.md)

### [V2 API参考](v2APIreference.md)
- [启动引导](v2APIreference/Bootstrap.md)
- [监听&监听发现](v2APIreference/ListenersandLDS.md)
- [集群&集群发现](v2APIreference/ClustersandCDS.md)
- [服务发现](v2APIreference/EndpointsandEDS.md)
- [健康检查](v2APIreference/Healthcheck.md)
- [HTTP路由管理&发现](v2APIreference/HTTProutemanagementandRDS.md)
- [TLS配置](v2APIreference/CommonTLSconfiguration.md)
- [通用的类型](v2APIreference/Commontypes.md)
- [网络地址](v2APIreference/Networkaddresses.md)
- [协议选项](v2APIreference/Protocoloptions.md)
- [发现API](v2APIreference/CommondiscoveryAPIcomponents.md)
- [限速组件](v2APIreference/Commonratelimitcomponents.md)
- [过滤器](v2APIreference/Filters.md)

### [FAQ](FAQ.md)

