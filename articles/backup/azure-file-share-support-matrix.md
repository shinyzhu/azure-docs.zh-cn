---
title: Azure 文件共享备份的支持矩阵
description: 汇总了备份 Azure 文件共享时的支持设置和限制。
ms.topic: conceptual
ms.date: 5/07/2020
ms.openlocfilehash: 38e8cdba4913a56b4dc6b4398a99db9e9f93be51
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/12/2020
ms.locfileid: "83116724"
---
# <a name="support-matrix-for-azure-file-share-backup"></a>Azure 文件共享备份的支持矩阵

可以使用 [Azure 备份服务](https://docs.microsoft.com/azure/backup/backup-overview)来备份 Azure 文件共享。 本文汇总了通过 Azure 备份来备份 Azure 文件共享时的支持设置。

## <a name="supported-geos"></a>支持的地理区域

以下地理区域提供了 Azure 文件共享备份功能：

**正式发布区域**：<br>
澳大利亚东南部 (ASE)、加拿大中部 (CNC)、美国中西部 (WCUS)、美国西部 2 (WUS 2)、印度南部 (INS)、美国中北部 (NCUS)、日本东部 (JPE)、巴西南部 (BRS)、东南亚 (SEA)、瑞士西部 (SZW)、阿联酋中部 (UAC)、挪威东部 (NWE)、印度西部 (INW)、澳大利亚中部 (ACL)、韩国中部 (KRC)、日本西部 (JPW)、南非北部 (SAN)、英国西部 (UKW)、韩国南部 (KRS)、德国北部 (GN)、挪威西部 (NWW)、南非西部 (SAW)、瑞士北部 (SZN)、德国中西部 (GWC)、阿联酋北部 (UAN)、法国中部 (FRC)、印度中部 (INC)、加拿大东部 (CNE)、东亚 (EA)、澳大利亚东部 (AE)、美国中部 (CUS)、美国西部 (WUS)、美国亚利桑那州 (UGA)，美国德克萨斯州 (UGT)、美国弗吉尼亚州 (UGV)、美国国防部中央 (UDC)、美国国防部东部 (UDE)

**（作为预览版的一部分）支持的但未正式发布的区域**：<br>
美国东部 (EUS)、美国东部 2 (EUS2)、北欧 (NE)、美国中南部 (SCUS)、英国南部 (UKS)、西欧 (WE)

## <a name="supported-storage-accounts"></a>支持的存储帐户

| 存储帐户详细信息 | 支持                                                      |
| ------------------------ | ------------------------------------------------------------ |
| 帐户种类            | Azure 备份支持常规用途 v1、常规用途 v2 和文件存储类型存储帐户中存在的 Azure 文件共享 |
| 性能              | Azure 备份同时支持标准存储帐户和高级存储帐户中的文件共享 |
| 复制              | 支持具有任何复制类型的存储帐户中的 Azure 文件共享 |

## <a name="supported-file-shares"></a>支持的文件共享

| 文件共享类型                                   | 支持   |
| -------------------------------------------------- | --------- |
| Standard                                           | 支持 |
| 大型                                              | 支持 |
| Premium                                            | 支持 |
| 与 Azure 文件同步服务连接的文件共享 | 支持 |

## <a name="protection-limits"></a>保护限制

| 设置                                                      | 限制 |
| ------------------------------------------------------------ | ----- |
| 每个保管库每天可保护的文件共享数上限 | 200   |
| 每个保管库每天可以注册的存储帐户数上限 | 50    |

## <a name="backup-limits"></a>备份限制

| 设置                                      | 限制 |
| -------------------------------------------- | ----- |
| 每天的按需备份数上限 | 4     |
| 每天的计划内备份数上限 | 1     |

## <a name="restore-limits"></a>还原限制

| 设置                                                      | 限制   |
| ------------------------------------------------------------ | ------- |
| 每天的还原数上限                           | 10      |
| 每次还原的文件数上限                         | 10      |
| 大型文件共享的每次还原的建议还原大小上限 | 15 TiB |

## <a name="retention-limits"></a>保留期限制

| 设置                                                      | 限制    |
| ------------------------------------------------------------ | -------- |
| 在任意时间点每个文件共享的恢复点总数上限 | 200      |
| 按需备份创建的恢复点的最大保留期 | 10 年 |
| 每个文件共享的每日恢复点（快照）的最大保留期| 200 天 |
| 每个文件共享的每周恢复点（快照）的最大保留期 | 200 周 |
| 每个文件共享的每月恢复点（快照）的最大保留期 | 120 月 |
| 每个文件共享的每年恢复点（快照）的最大保留期 | 10 年 |

## <a name="supported-restore-methods"></a>支持的还原方法

| 还原方法     | 详细信息                                                      |
| ------------------ | ------------------------------------------------------------ |
| 完整共享还原 | 可将整个文件共享还原到原始位置或其他位置 |
| 项级还原 | 可将各个文件和文件夹还原到原始位置或其他位置 |

## <a name="next-steps"></a>后续步骤

* 了解如何[备份 Azure 文件共享](backup-afs.md)
* 了解如何[还原 Azure 文件共享](restore-afs.md)
* 了解如何[管理 Azure 文件共享备份](manage-afs-backup.md)
