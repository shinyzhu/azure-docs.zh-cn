---
title: 限制 - Azure Database for MariaDB
description: 本文介绍了 Azure Database for MariaDB 中的限制，例如连接数和存储引擎选项。
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/1/2020
ms.openlocfilehash: 18f227c1888e0565eebb640fa61ced56dc994865
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80632341"
---
# <a name="limitations-in-azure-database-for-mariadb"></a>Azure Database for MariaDB 中的限制
以下各部分介绍了数据库服务中的容量、存储引擎支持、特权支持、数据操作语句支持和功能限制。

## <a name="server-parameters"></a>服务器参数

多个常用服务器参数的最小值和最大值由定价层和 vCore 确定。 请参阅下表中的限制。

### <a name="max_connections"></a>max_connections

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|50|10 个|50|
|基本|2|100|10 个|100|
|常规用途|2|300|10 个|600|
|常规用途|4|625|10 个|1250|
|常规用途|8|1250|10 个|2500|
|常规用途|16|2500|10 个|5000|
|常规用途|32|5000|10 个|10000|
|常规用途|64|10000|10 个|20000|
|内存优化|2|600|10 个|800|
|内存优化|4|1250|10 个|2500|
|内存优化|8|2500|10 个|5000|
|内存优化|16|5000|10 个|10000|
|内存优化|32|10000|10 个|20000|

当连接数超出限制时，可能会收到以下错误：
> 错误 1040 (08004)：连接过多

> [!IMPORTANT]
> 为了获得最佳体验，我们建议你使用 ProxySQL 之类的连接池程序来有效地管理连接。

创建与 MariaDB 的新客户端连接需要时间，一旦建立，这些连接就会占用数据库资源，即使在空闲时也是如此。 大多数应用程序都请求许多短期连接，这加剧了这种情况。 其结果是可用于实际工作负荷的资源减少，从而导致性能下降。 减少空闲连接并重用现有连接的连接池会有助于避免这种情况。 若要了解如何设置 ProxySQL，请访问我们的[博客文章](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042)。

### <a name="query_cache_size"></a>query_cache_size

默认会禁用查询缓存。 若要启用查询缓存，请配置 `query_cache_type` 参数。 

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#query_cache_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|不适用|不适用|
|基本|2|在基本层中不可配置|空值|不适用|
|常规用途|2|0|0|16777216|
|常规用途|4|0|0|33554432|
|常规用途|8|0|0|67108864|
|常规用途|16|0|0|134217728|
|常规用途|32|0|0|134217728|
|常规用途|64|0|0|134217728|
|内存优化|2|0|0|33554432|
|内存优化|4|0|0|67108864|
|内存优化|8|0|0|134217728|
|内存优化|16|0|0|134217728|
|内存优化|32|0|0|134217728|

### <a name="sort_buffer_size"></a>sort_buffer_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#sort_buffer_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|不适用|不适用|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|524288|32768|4194304|
|常规用途|4|524288|32768|8388608|
|常规用途|8|524288|32768|16777216|
|常规用途|16|524288|32768|33554432|
|常规用途|32|524288|32768|33554432|
|常规用途|64|524288|32768|33554432|
|内存优化|2|524288|32768|8388608|
|内存优化|4|524288|32768|16777216|
|内存优化|8|524288|32768|33554432|
|内存优化|16|524288|32768|33554432|
|内存优化|32|524288|32768|33554432|

### <a name="join_buffer_size"></a>join_buffer_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#join_buffer_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|空值|不适用|
|基本|2|在基本层中不可配置|不适用|不适用|
|常规用途|2|262144|128|268435455|
|常规用途|4|262144|128|536870912|
|常规用途|8|262144|128|1073741824|
|常规用途|16|262144|128|2147483648|
|常规用途|32|262144|128|4294967295|
|常规用途|64|262144|128|4294967295|
|内存优化|2|262144|128|536870912|
|内存优化|4|262144|128|1073741824|
|内存优化|8|262144|128|2147483648|
|内存优化|16|262144|128|4294967295|
|内存优化|32|262144|128|4294967295|

### <a name="max_heap_table_size"></a>max_heap_table_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#max_heap_table_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|不适用|不适用|
|基本|2|在基本层中不可配置|空值|不适用|
|常规用途|2|16777216|16384|268435455|
|常规用途|4|16777216|16384|536870912|
|常规用途|8|16777216|16384|1073741824|
|常规用途|16|16777216|16384|2147483648|
|常规用途|32|16777216|16384|4294967295|
|常规用途|64|16777216|16384|4294967295|
|内存优化|2|16777216|16384|536870912|
|内存优化|4|16777216|16384|1073741824|
|内存优化|8|16777216|16384|2147483648|
|内存优化|16|16777216|16384|4294967295|
|内存优化|32|16777216|16384|4294967295|

### <a name="tmp_table_size"></a>tmp_table_size

查看 [MariaDB 文档](https://mariadb.com/kb/en/server-system-variables/#tmp_table_size)详细了解此参数。

|**定价层**|**vCore(s)**|**默认值**|**最小值**|**最大值**|
|---|---|---|---|---|
|基本|1|在基本层中不可配置|不适用|不适用|
|基本|2|在基本层中不可配置|空值|不适用|
|常规用途|2|16777216|1024|67108864|
|常规用途|4|16777216|1024|134217728|
|常规用途|8|16777216|1024|268435456|
|常规用途|16|16777216|1024|536870912|
|常规用途|32|16777216|1024|1073741824|
|常规用途|64|16777216|1024|1073741824|
|内存优化|2|16777216|1024|134217728|
|内存优化|4|16777216|1024|268435456|
|内存优化|8|16777216|1024|536870912|
|内存优化|16|16777216|1024|1073741824|
|内存优化|32|16777216|1024|1073741824|

### <a name="time_zone"></a>time_zone

可以通过从 MySQL 命令行或 MySQL 工作台等`mysql.az_load_timezone`工具调用存储过程来填充时区表。 有关如何调用存储过程并设置全局或会话级别时区的详细说明，请参阅[Azure 门户](howto-server-parameters.md#working-with-the-time-zone-parameter)或[Azure CLI](howto-configure-server-parameters-cli.md#working-with-the-time-zone-parameter)文章。

## <a name="storage-engine-support"></a>存储引擎支持

### <a name="supported"></a>支持
- [InnoDB](https://mariadb.com/kb/en/library/xtradb-and-innodb/)
- [记忆](https://mariadb.com/kb/en/library/memory-storage-engine/)

### <a name="unsupported"></a>不支持
- [MyISAM](https://mariadb.com/kb/en/library/myisam-storage-engine/)
- [BLACKHOLE](https://mariadb.com/kb/en/library/blackhole/)
- [存档](https://mariadb.com/kb/en/library/archive/)

## <a name="privilege-support"></a>特权支持

### <a name="unsupported"></a>不支持
- DBA 角色：许多服务器参数和设置可能会无意中导致服务器性能下降或使 DBMS 的 ACID 属性无效。 因此，为了维护产品级别的服务完整性和 SLA，此服务不公开 DBA 角色。 默认用户帐户（在创建新的数据库实例时构造）允许该用户执行托管数据库实例中的大部分 DDL 和 DML 语句。
- 超级权限：同样，也会限制[超级权限](https://mariadb.com/kb/en/library/grant/#global-privileges)。
- DEFINER：需要超级权限才能创建和受限。 如果使用备份导入数据，请在执行 mysqldump 时手动删除或使用 `--skip-definer` 命令删除 `CREATE DEFINER` 命令。

## <a name="data-manipulation-statement-support"></a>数据操作语句支持

### <a name="supported"></a>支持
- 支持 `LOAD DATA INFILE`，但必须指定 `[LOCAL]` 参数，并将其定向到 UNC 路径（通过 SMB 装载的 Azure 存储空间）。

### <a name="unsupported"></a>不支持
- `SELECT ... INTO OUTFILE`

## <a name="functional-limitations"></a>功能限制

### <a name="scale-operations"></a>缩放操作
- 目前不支持向/从基本定价层动态缩放。
- 不支持减小服务器存储大小。

### <a name="server-version-upgrades"></a>服务器版本升级
- 目前不支持在主要数据库引擎版本之间进行自动迁移。

### <a name="point-in-time-restore"></a>时间点还原
- 使用 PITR 功能时，将使用与新服务器所基于的服务器相同的配置创建新服务器。
- 不支持还原已删除的服务器。

### <a name="subscription-management"></a>订阅管理
- 目前不支持跨订阅和资源组动态移动预先创建的服务器。

### <a name="vnet-service-endpoints"></a>VNet 服务终结点
- 只有常规用途和内存优化服务器才支持 VNet 服务终结点。

### <a name="storage-size"></a>存储大小
- 有关每个定价层的存储大小限制，请参阅[定价层](concepts-pricing-tiers.md)。

## <a name="current-known-issues"></a>当前已知的问题
- 建立连接后，MariaDB 服务器实例显示的服务器版本不正确。 若要获取正确的服务器实例引擎版本，请使用 `select version();` 命令。

## <a name="next-steps"></a>后续步骤
- [每个服务层提供的功能](concepts-pricing-tiers.md)
- [支持的 MariaDB 数据库版本](concepts-supported-versions.md)
