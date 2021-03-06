---
title: 发布产品/服务 |Azure Marketplace
description: 用于发布指定产品/服务的 API。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 960d5facb53f20719045c5fdbe2179f549aca3f2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81255934"
---
# <a name="publish-an-offer"></a>发布产品/服务

> [!NOTE]
> 云合作伙伴门户 Api 与合作伙伴中心集成，并在将产品/服务迁移到合作伙伴中心后继续工作。 集成引入了少量更改。 查看[云合作伙伴门户 API 参考](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview)中列出的更改，以确保你的代码在迁移到合作伙伴中心后仍能正常工作。

启动指定产品/服务的发布过程。 此调用是一个长时间运行的操作。

  `POST  https://cloudpartner.azure.com/api/publishers/<publisherId>/offers/<offerId>/publish?api-version=2017-10-31`

## <a name="uri-parameters"></a>URI 参数
--------------

|  **名称**      |    **描述**                               |  **数据类型** |
|  ------------- |  ------------------------------------            |   -----------  |
|  publisherId   | 发布者标识符，例如 `contoso`      |   字符串       |
|  offerId       | 产品/服务标识符                                 |   字符串       |
|  api-version   | API 最新版本                        |   日期         |
|  |  |

## <a name="header"></a>Header
------

|  **Name**        |    **值**          |
|  --------        |    ---------          |
|  Content-Type    | `application/json`    |
|  授权   |  `Bearer YOUR_TOKEN`  |
|  |  |


## <a name="body-example"></a>正文示例
------------

### <a name="request"></a>请求

``` json
  { 
      'metadata': 
          { 
              'notification-emails': 'jdoe@contoso.com'
          } 
  }
```

### <a name="request-body-properties"></a>请求正文属性

|  **名称**               |   **描述**                                                                                 |
|  ---------------------  | ------------------------------------------------------------------------------------------------- |
|  notification-emails    | 逗号分隔的电子邮件地址列表，用于通知发布操作的进度。 |
|  |  |


### <a name="response"></a>响应

#### <a name="migrated-offers"></a>已迁移产品

`Location: /api/publishers/contoso/offers/contoso-offer/operations/56615b67-2185-49fe-80d2-c4ddf77bb2e8?api-version=2017-10-31`

#### <a name="non-migrated-offers"></a>未迁移的产品

`Location: /api/operations/contoso$contoso-offer$2$preview?api-version=2017-10-31`


### <a name="response-header"></a>响应标头

|  **Name**             |    **值**                                                                 |
|  -------------------- | ---------------------------------------------------------------------------- |
| 位置    | 用于检索此操作的状态的相对路径     |
|  |  |


### <a name="response-status-codes"></a>响应状态代码

| **编写** |  **说明**                                                                                                                           |
| ------   |  ----------------------------------------------------------------------------------------------------------------------------------------- |
| 202   | `Accepted` - 已成功接受请求。 响应包含一个位置，该位置可用于跟踪已启动的操作。 |
| 400   | `Bad/Malformed request` - 错误响应正文可以提供更多信息。                                                               |
| 422   | `Un-processable entity` -指示要发布的实体未通过验证。                                                        |
| 404   | `Not found` - 客户端指定的实体不存在。                                                                              |
|  |  |
