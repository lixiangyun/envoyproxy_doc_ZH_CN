## DynamoDB
- [DynamoDB架构概述](../../Introduction/Architectureoverview/DynamoDB.md)
- [v1 API 参考](../../v1APIreference/HTTPfilters/DynamoDB.md)
- [v2 API 参考](../../v2APIreference/Filters/Networkfilters/HTTPconnectionmanager.md)

### 统计

DynamoDB过滤器输出统计信息命名空间为`http.<stat_prefix>.dynamodb.`。其中`stat_prefix`来自所拥有的HTTP连接管理器。

每个操作的统计信息可以在命名空间`http.<stat_prefix>.dynamodb.operation.<operation_name>.`找到。

|	名称	|	类型	|	描述	|
|	 -------------	|	 -------------	|	 -------------	|
|	upstream_rq_total	|	Counter	|	Total number of requests with <operation_name>	|
|	upstream_rq_time	|	Histogram	|	Time spent on <operation_name>	|
|	upstream_rq_total_xxx	|	Counter	|	Total number of requests with <operation_name> per response code (503/2xx/etc)	|
|	upstream_rq_time_xxx	|	Histogram	|	Time spent on <operation_name> per response code (400/3xx/etc)	|


每个表的统计信息可以在命名空间`http.<stat_prefix>.dynamodb.table.<table_name>.`中找到。 DynamoDB的大部分操作都涉及单个表，但`BatchGetItem`和`BatchWriteItem`可以包含多个表，Envoy仅在所有批处理操作使用相同的表时才跟踪每个表的统计信息。

|	名称	|	类型	|	描述	|
|	 -------------	|	 -------------	|	 -------------	|
|	upstream_rq_total	|	Counter	|	Total number of requests on <table_name> table	|
|	upstream_rq_time	|	Histogram	|	Time spent on <table_name> table	|
|	upstream_rq_total_xxx	|	Counter	|	Total number of requests on <table_name> table per response code (503/2xx/etc)	|
|	upstream_rq_time_xxx	|	Histogram	|	Time spent on <table_name> table per response code (400/3xx/etc)	|

*免责声明：请注意，这是尚未广泛使用的预发行版本的Amazon DynamoDB功能。*
每个分区和操作统计信息可以在命名空间`http.<stat_prefix>.dynamodb.table.<table_name>.`找到。对于批量操作，Envoy仅在所有操作中使用相同的表时跟踪每个分区和操作统计信息。

|	名称	|	类型	|	描述	|
|	 -------------	|	 -------------	|	 -------------	|
|	capacity.\<operation_name>.__partition_id=\<last_seven_characters_from_partition_id>	|	Counter	|	Total number of capacity for \<operation_name> on \<table_name> table for a given \<partition_id>	|

其他详细统计信息：

- 对于4xx响应和部分批处理操作失败，在命名空间` http.<stat_prefix>.dynamodb.error.<table_name>.`中跟踪指定的表，失败的总次数。

|	名称	|	类型	|	描述	|
|	 -------------	|	 -------------	|	 -------------	|
|	\<error_type>	|	Counter	|	Total number of specific \<error_type> for a given \<table_name>	|
|	BatchFailureUnprocessedKeys	|	Counter	|	Total number of partial batch failures for a given \<table_name>	|

### 运行时设置
DynamoDB过滤器支持以下运行时设置：

- **dynamodb.filter_enabled**<br />
启用过滤器的请求百分比。默认是100％。

## 返回
- [上一级](../HTTPfilters.md)
- [首页目录](../../README.md)
