---
title: Azure Marketplace SEO 指南
description: 提供了有关最大程度地提升搜索引擎优化 (SEO) 的指南。
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2019
ms.author: dsindona
ms.openlocfilehash: 761cdc2233bce3619d4c2c9ce1d7d7177d3bc407
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80280144"
---
# <a name="azure-marketplace-seo-guidance"></a>Azure Marketplace SEO 指南

本文介绍如何通过[Azure Marketplace](https://azuremarketplace.microsoft.com)和[AppSource](https://appsource.microsoft.com)中的搜索功能，最大程度地提高产品/服务的可发现性。 


## <a name="general-explanation-of-algorithm"></a>算法的一般说明

Microsoft 市场使用 Azure 认知搜索为网站的搜索功能提供支持。 算法基于词频–逆向文档频率 ([TF-IDF](https://en.wikipedia.org/wiki/Tf–idf))。 将使用标准 [Lucene 分析器](https://lucene.apache.org/core/)。

通常情况下，所有文本字段、类别和行业都将包括在相关性权重中。 其他应用不常用但你的应用中常用的专业术语在搜索时将产生较高的匹配分数。 因此，使用“VM”这样的术语不会带来多大好处，而使用“Azure搜索”将更加专业。
下面是要考虑的最相关的字段。

 
|  字段                   | Importance | 指南                                                                                            |
|  --------------------    | ----------                   | ---------------                                                                   |
| 产品名称               |  高      | 与搜索查询完全匹配或接近完全匹配的项将产生较高的排名。                       |
| 发布者名称           |  高      | 与搜索查询完全匹配或接近完全匹配的项将产生较高的排名。                       |
| 简短说明        |  中    | 给定应用名称和发布者名称几乎可以保证高排名，但这可能不是最相关的。 在这种情况下，简短说明非常重要。 请使文本简明扼要。 为获得最佳结果，应当包括关键字和预期的搜索词。  例如，“This is the best Retail POS built fully on top of Dynamics 365”比“Retail POS (point of sale) for Dynamics 365”要低效。  | 
| 详细说明         |  低       | 说明提供了一种深入的方法。 最有效的说明应当长度合理并且使用关键字。  使用关键字的扼要说明将优于冗长的文本。 请确保说明中存在关键术语，例如“IoT”。  |
| 产品类别       | 中     |  产品类别是由所选择的发布者和 Microsoft 的组合确定的。 请恰当地选择这些类别，以便用户可以轻松在正确的类别中找到应用。 |
|  |  |  |


## <a name="other-tips"></a>其他技巧

-   搜索建议会导致过多的用户活动。 它根据应用名称/发布者确定匹配项的优先级。 当搜索词与发布者/应用名称不完全匹配时，简短说明将成为键字段。
-   用于下载的文档不包括在搜索权重中。
-   应用的实际获取和使用情况也将影响搜索排名。 例如，对于两个同等的应用程序，具有更大用户数的应用将获得较高的排名。
