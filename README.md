# envoy 智能代理中文参考文档 v1.5.0
FROM: https://www.envoyproxy.io/docs/envoy/v1.5.0/

## 关于本文档
- **简介**：介绍Envoy相关概念，以及总体的架构设计和常用的部署方式等
- **安装**：如何通过Docker构建、安装Envoy
- **配置**：既有的V1/V2 API的详细配置说明，以及相关的统计、运行时的APIs
- **操作**：关于Envoy操作方式，如命令行、热更新、管理接口、统计概况等
- **扩展**：关于如何为Envoy编写自定义过滤器
- **V1 API 参考**：遗留的V1 API的详细配置说明
- **V2 API 参考**：新的V2 API的详细配置说明

### [简介](Introduction.md)
- [Envoy是什么](Introduction/WhatisEnvoy.md)
- [架构介绍](Introduction/Architectureoverview.md)
- [部署](Introduction/Deploymenttypes.md)
- [对比相似系统](Introduction/Comparisontosimilarsystems.md)
- [获得帮助](Introduction/Gettinghelp.md)
- [历史版本](Introduction/Versionhistory.md)

### Building and installation
- Building
- Reference configurations
- Sandboxes
- Tools

### Configuration reference
- Overview (v1 API)
- Overview (v2 API)
- Listeners
- Network filters
- HTTP connection manager
- HTTP filters
- Cluster manager
- Access logging
- Rate limit service
- Runtime
- Route table check tool
### Operations and administration
- Command line options
- Hot restart Python wrapper
- Administration interface
- Statistics overview
- Runtime
- File system flags
- Next  Previous
### Extending Envoy for custom use cases

### v1 API reference
- Listeners
- Network filters
- HTTP Route configuration
- HTTP filters
- Cluster manager
- Access logging
- Administration interface
- Rate limit service
- Runtime
- Tracing

### v2 API reference
- Bootstrap
- Listeners and LDS
- Clusters and CDS
- Endpoints and EDS
- Health check
- HTTP route management and RDS
- Common TLS configuration
- Stats
- Common types
- Network addresses
- Protocol options
- Common discovery API components
- Common rate limit components
- Filters

### FAQ
- How fast is Envoy?
- Where do I get binaries?
- How do I setup SNI?
- How do I setup zone aware routing?
- How do I setup Zipkin tracing?
