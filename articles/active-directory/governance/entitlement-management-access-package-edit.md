---
title: 在授权管理中隐藏或删除访问包-Azure AD
description: 了解如何在 Azure Active Directory 权限管理中隐藏或删除访问包。
services: active-directory
documentationCenter: ''
author: msaburnley
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 10/15/2019
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: cf410db5a483fc4f5d9efc1a6bd5c6202694ef4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79261991"
---
# <a name="hide-or-delete-an-access-package-in-azure-ad-entitlement-management"></a>在 Azure AD 权限管理中隐藏或删除访问包

默认情况下，访问包是可发现的。 这意味着，如果策略允许用户请求访问包，则他们自动会看到该访问包列在“我的访问权限”门户中。 但是，你可以更改**隐藏**的设置，以便在用户的我的 access 门户中未列出该访问包。

本文介绍如何隐藏或删除访问包。

## <a name="change-the-hidden-setting"></a>更改隐藏设置

按照以下步骤更改访问包的**隐藏**设置。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 在“概述”页上单击“编辑”****。

1. 设置“隐藏”设置。****

    如果设置为“否”，则该访问包将列在用户的“我的访问权限”门户中。****

    如果设置为“是”，则该访问包不会列在用户的“我的访问权限”门户中。**** 仅当用户已获得访问包的“我的访问权限”门户直接链接时，他们才可以查看访问包。**** 有关详细信息，请参阅 "[共享链接" 来请求访问包](entitlement-management-access-package-settings.md)。

## <a name="delete-an-access-package"></a>删除访问包

仅当访问包没有活动的用户分配时，才能将其删除。 请按照下列步骤操作以删除访问包。

**必备角色：** 全局管理员、用户管理员、目录所有者或访问包管理员

1. 在 Azure 门户中，依次单击“Azure Active Directory”、“标识监管”。  

1. 在左侧菜单中单击“访问包”，然后打开访问包。****

1. 在左侧菜单中，单击“分配”并删除所有用户的访问权限。****

1. 在左侧菜单中单击“概述”，然后单击“删除”。********

1. 在出现的删除消息中单击“是”****。

## <a name="next-steps"></a>后续步骤

- [查看、添加和删除访问包的分配](entitlement-management-access-package-assignments.md)
- [查看报表和日志](entitlement-management-reports.md)
