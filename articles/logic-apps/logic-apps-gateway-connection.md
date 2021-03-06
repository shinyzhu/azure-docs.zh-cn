---
title: 在本地访问数据源
description: 通过创建 Azure 本地数据网关资源从 Azure 逻辑应用连接到本地数据源
services: logic-apps
ms.suite: integration
ms.reviewer: arthii, logicappspm
ms.topic: article
ms.date: 02/14/2020
ms.openlocfilehash: 096943ff796f9c12c7f8715cadce5c3085965d4d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "80657138"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>从 Azure 逻辑应用连接到本地数据源

[在本地计算机上安装本地数据网关  ](../logic-apps/logic-apps-gateway-install.md)之后，需创建 Azure 资源，然后才能从逻辑应用访问本地数据源。 逻辑应用然后在用于 Azure 逻辑应用的[本地连接器](../connectors/apis-list.md#on-premises-connectors)提供的触发器和操作中使用该 Azure 网关资源。

本文介绍如何为以前[安装在本地计算机上的网关](../logic-apps/logic-apps-gateway-install.md)创建 Azure 网关资源。 有关网关的详细信息，请参阅[网关的工作原理](../logic-apps/logic-apps-gateway-install.md#gateway-cloud-service)。

> [!TIP]
> 若要连接到 Azure 虚拟网络，请考虑改为创建[*集成服务环境*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md)。 

有关如何将网关用于其他服务的信息，请参阅以下文章：

* [Microsoft Power 自动执行本地数据网关](/power-automate/gateway-reference)
* [Microsoft Power BI 本地数据网关](/power-bi/service-gateway-onprem)
* [Microsoft Power Apps 本地数据网关](/powerapps/maker/canvas-apps/gateway-reference)
* [Azure Analysis Services 本地数据网关](../analysis-services/analysis-services-gateway.md)

<a name="supported-connections"></a>

## <a name="supported-data-sources"></a>支持的数据源

在 Azure 逻辑应用中，本地数据网关支持以下数据源的[本地连接器](../connectors/apis-list.md#on-premises-connectors)：

* BizTalk Server 2016
* 文件系统
* IBM DB2  
* IBM Informix
* IBM MQ
* MySQL
* Oracle Database
* PostgreSQL
* SAP
* SharePoint Server
* SQL Server
* Teradata

Azure 逻辑应用支持通过数据网关进行的读取和写入操作。 但是，这些操作存在[有效负载大小限制](https://docs.microsoft.com/data-integration/gateway/service-gateway-onprem#considerations)。 虽然网关本身不额外收费，但[逻辑应用定价模型](../logic-apps/logic-apps-pricing.md)适用于这些连接器以及 Azure 逻辑应用中的其他操作。

## <a name="prerequisites"></a>必备条件

* 已经[在本地计算机上安装本地数据网关](../logic-apps/logic-apps-gateway-install.md)。

* 你使用的 [Azure 帐户和订阅](../logic-apps/logic-apps-gateway-install.md#requirements)与安装该数据网关时使用的帐户和订阅相同。 此 Azure 帐户必须属于单个 [Azure Active Directory (Azure AD) 租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)。

* 网关安装尚未由另一 Azure 网关资源注册并声明。

  在 Azure 门户中创建网关资源时，请选择一个与你的网关资源相关联且仅与该网关资源相关联的网关安装。 然后，在 Azure 逻辑应用中，本地触发器和操作将使用网关资源连接到本地数据源。 在这些触发器和操作中，请选择需要使用的 Azure 订阅和关联的网关资源。 每个网关资源只关联到一个网关安装，后者仅关联到一个 Azure 帐户。

  > [!NOTE]
  > 只有网关管理员才能在 Azure 门户中创建网关资源。 当前不支持服务主体。 

<a name="create-gateway-resource"></a>

## <a name="create-azure-gateway-resource"></a>创建 Azure 网关资源

在本地计算机上安装网关后，请为网关创建 Azure 资源。

1. 使用安装网关时所使用的 Azure 帐户登录到 [Azure 门户](https://portal.azure.com)。

1. 在 Azure 门户搜索框中输入“本地数据网关”，然后选择“本地数据网关”****。

   ![查找“本地数据网关”](./media/logic-apps-gateway-connection/search-for-on-premises-data-gateway.png)

1. 在“本地数据网关”下，选择“添加”。********

   ![为数据网关添加新的 Azure 资源](./media/logic-apps-gateway-connection/add-azure-data-gateway-resource.png)

1. 在“创建连接网关”下，提供网关资源的以下信息。**** 完成操作后，选择“创建”  。

   | 属性 | 说明 |
   |----------|-------------|
   | **资源名称** | 为网关资源提供一个名称，其中只包含字母、数字、连字符 (`-`)、下划线 (`_`)、括号（`(`、`)`）或句点 (`.`)。 |
   | **订阅** | 选择曾用于网关安装的 Azure 帐户的 Azure 订阅。 默认订阅取决于用来登录的 Azure 帐户。 |
   | **资源组** | 要使用的 [Azure 资源组](../azure-resource-manager/management/overview.md) |
   | **位置** | 在[网关安装](../logic-apps/logic-apps-gateway-install.md)期间为网关云服务选择的区域或位置。 否则，网关安装不会显示在“安装名称”列表中。**** 逻辑应用位置可能不同于网关资源位置。 |
   | **安装名称** | 选择一个网关安装，该安装仅在以下条件满足的情况下显示在列表中： <p><p>- 网关安装使用的区域与要创建的网关资源所在的区域相同。 <br>- 网关安装未关联到另一 Azure 网关资源。 <br>- 网关安装关联到要用来创建网关资源的 Azure 帐户。 <br>- 你的 Azure 帐户属于单个 [Azure Active Directory (Azure AD) 租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)，是用于网关安装的帐户。 <p><p>有关详细信息，请参阅[常见问题解答](#faq)部分。 |
   |||

   以下示例演示的网关安装位于你的网关资源所在的区域，并关联到同一 Azure 帐户：

   ![提供创建数据网关资源所需的详细信息](./media/logic-apps-gateway-connection/on-premises-data-gateway-create-connection.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>连接到本地数据

创建网关资源并将 Azure 订阅与此资源相关联后，可以使用该网关在逻辑应用与本地数据源之间创建连接。

1. 在 Azure 门户的逻辑应用设计器中创建或打开逻辑应用。

1. 添加支持本地连接的连接器，例如 **SQL Server**。

1. 选择“通过本地数据网关连接”。****

1. 在“网关”下，从“订阅”列表中选择******** 你的 Azure 订阅，该订阅有你需要的网关资源。

1. 从“连接网关”列表中选择所需网关资源，该列表会显示所选订阅中的可用网关资源。**** 每个网关资源关联到单个网关安装。

   > [!NOTE]
   > 网关列表包含其他区域的网关资源，因为逻辑应用的位置可能不同于网关资源的位置。 

1. 提供唯一的连接名称和其他所需信息，具体取决于要创建的连接。

   唯一的连接名称有助于在以后轻松查找该连接，尤其是在创建多个连接的情况下。 另请包括用户名的限定域（如果适用）。

   以下是示例：

   ![在逻辑应用和数据网关之间创建连接](./media/logic-apps-gateway-connection/logic-app-gateway-connection.png)

1. 完成操作后，选择“创建”  。

网关连接现在已准备就绪，可供逻辑应用使用。

## <a name="edit-connection"></a>编辑连接

若要更新网关连接的设置，可以编辑连接。

1. 若要查找你的逻辑应用的所有 API 连接，请在逻辑应用菜单中的“开发工具”下，选择“API 连接”。********

   ![在逻辑应用菜单上，选择“API 连接”](./media/logic-apps-gateway-connection/logic-app-api-connections.png)

1. 选择所需的网关连接，然后选择“编辑 API 连接”。****

   > [!TIP]
   > 如果更新未生效，请尝试针对网关安装[停止网关 Windows 服务帐户，然后重启该帐户](../logic-apps/logic-apps-gateway-install.md#restart-gateway)。

查找与 Azure 订阅关联的所有 API 连接：

* 从 "Azure 门户" 菜单中，选择 "**所有服务** > " "**Web** > **API 连接**"。
* 或者，从 "Azure 门户" 菜单中选择 "**所有资源**"。 将“类型”**** 筛选器设置为“API 连接”。****

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>删除网关资源

若要创建其他网关资源、将网关安装与其他网关资源相关联，或者移除网关资源，则可删除网关资源，不影响网关安装。

1. 从 "Azure 门户" 菜单中，选择 "**所有资源**"，或从任意页面搜索并选择 "**所有资源**"。 找到并选择所需的网关资源。

1. 如果尚未选择，请在网关资源菜单中选择“本地数据网关”。**** 在网关资源工具栏上，选择“删除”。****

   例如：

   ![删除 Azure 中的网关资源](./media/logic-apps-gateway-connection/delete-on-premises-data-gateway.png)

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>常见问题解答

**问**：为什么我的网关安装在 Azure 中创建网关资源时未显示？ <br/>
**答**：此问题的可能原因如下：

* Azure 帐户必须是关联到本地计算机上的网关安装的帐户。 请检查你是否使用了关联到网关安装的标识登录到 Azure 门户。 另请确保你的 Azure 帐户属于单个 [Azure AD 租户或目录](../active-directory/fundamentals/active-directory-whatis.md#terminology)，并已设置为在网关安装过程中使用过的 Azure AD 租户或目录。

* 网关资源和网关安装必须使用同一区域。 但是，逻辑应用位置可以不同于网关资源位置。

* 网关安装已由另一网关资源注册并声明。 这些安装不会显示在“安装名称”列表中。**** 若要在 Azure 门户中查看网关注册，请找到所有 Azure 订阅的“本地数据网关”类型的所有 Azure 资源。****** 若要取消关联其他网关资源中的网关安装，请参阅[删除网关资源](#change-delete-gateway-resource)。

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="next-steps"></a>后续步骤

* [保护逻辑应用](./logic-apps-securing-a-logic-app.md)
* [逻辑应用的常见示例和方案](./logic-apps-examples-and-scenarios.md)
