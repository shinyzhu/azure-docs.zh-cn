---
title: 如何共享 Azure Dev Spaces
services: azure-dev-spaces
ms.date: 05/11/2018
ms.topic: conceptual
description: 了解如何使用 Azure Dev Spaces 与团队中的其他人共享 Azure Kubernetes 服务中的开发人员空间
keywords: 'Docker, Kubernetes, Azure, AKS, Azure Kubernetes 服务, 容器, Helm, 服务网格, 服务网格路由, kubectl, k8s '
ms.openlocfilehash: 0fcb8be5107c7769af7e51dece9f190c8e7e22df
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79474401"
---
# <a name="share-azure-dev-spaces"></a>共享 Azure Dev Spaces

借助 Azure Dev Spaces，可以与团队中的其他人共享开发空间。 每个开发人员都可以在自己的空间中工作，而不必担心会破坏他人。 另外，在一个空间中一起工作可让你测试端到端代码，而不必创建模拟依赖关系。 请参阅[了解团队开发](../team-development-nodejs.md)指南以获取详细信息。

## <a name="set-up-a-dev-space-for-multiple-developers"></a>为多个开发人员设置开发空间

1. 在 Azure 中创建开发空间。 选择 [.NET Core 和 VS Code](../get-started-netcore.md)、[.NET Core 和 Visual Studio](../get-started-netcore-visualstudio.md) 或 [Node.js 和 VS Code](../get-started-nodejs.md)。 需要对所选 Azure 订阅具有“所有者”或“参与者”访问权限。
1. 确保每个团队成员都具有[访问 Azure Dev Spaces 控制器的适当权限](../troubleshooting.md#incorrect-rbac-permissions-for-calling-dev-spaces-controller-and-apis)。 例如，你可以配置 Azure 开发人员空间的**资源组**，以便为每个团队成员[授予 "参与者" 访问权限](/azure/active-directory/role-based-access-control-configure)。 可以运行下面的命令来检查开发空间的资源组：`azds show-context`
1. 让团队成员选择要用于开发的开发空间****。
   * **命令行或 VS Code**：若要查看现有 Azure Dev Spaces，可以访问 `azds space list`。 若要选择开发空间，可以访问 `azds space select`。
   * **Visual Studio IDE**：在 Visual Studio 中打开项目，从“启动设置”下拉列表中选择 **Azure Dev Spaces**。 在打开的对话框中，选择现有群集。

     ![Visual Studio“启动设置”下拉列表](../media/get-started-netcore-visualstudio/LaunchSettings.png)

## <a name="next-steps"></a>后续步骤

请参阅[了解团队开发](../team-development-nodejs.md)以获取详细信息。
