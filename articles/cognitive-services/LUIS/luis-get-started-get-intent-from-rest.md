---
title: 快速入门：使用 REST API 获取意向 - LUIS
description: 此 REST API 快速入门使用可用的公共 LUIS 应用从对话文本中确定用户的意向。
ms.topic: quickstart
ms.date: 04/20/2020
zone_pivot_groups: programming-languages-set-one
ms.openlocfilehash: 03f1c1539233872d9b98fab317c9caf997d5df69
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81733275"
---
# <a name="quickstart-get-intent-with-rest-apis"></a>快速入门：使用 REST API 获取意向

本快速入门使用可用的公共 LUIS 应用从会话文本中确定用户的意向。 将用户的意向作为文本发送到公共应用的 HTTP 预测终结点。 在终结点处，LUIS 应用公共应用的模型来分析自然语言文本的含义，确定总体意向并提取与应用的主题域相关的数据。

本快速入门使用终结点 REST API。 有关详细信息，请参阅[终结点 API 文档](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)。

对于本文，需要一个免费的 [LUIS](https://www.luis.ai) 帐户。

<a name="create-luis-subscription-key"></a>

::: zone pivot="programming-language-csharp"
[!INCLUDE [Get intent with C# and REST](./includes/get-started-get-intent-rest-csharp.md)]
::: zone-end

::: zone pivot="programming-language-go"
[!INCLUDE [Get intent with Go and REST](./includes/get-started-get-intent-rest-go.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Get intent with Java and REST](./includes/get-started-get-intent-rest-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [Get intent with Node.js and REST](./includes/get-started-get-intent-rest-nodejs.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Get intent with Python and REST](./includes/get-started-get-intent-rest-python.md)]
::: zone-end

