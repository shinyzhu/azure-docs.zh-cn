---
title: 在 Azure 中移动 Windows VM 资源
description: 在 Resource Manager 部署模型中将 Windows VM 移到其他 Azure 订阅或资源组。
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 07/03/2019
ms.author: cynthn
ms.openlocfilehash: 0c3f5541405d1fd983bbf988b99d2b4e10d8908c
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/22/2020
ms.locfileid: "81865667"
---
# <a name="move-a-windows-vm-to-another-azure-subscription-or-resource-group"></a>将 Windows VM 移到其他 Azure 订阅或资源组
本文逐步说明如何在资源组或订阅之间移动 Windows 虚拟机 (VM)。 如果最初在个人订阅中创建了 VM，现在想要将其移到公司的订阅以继续工作，则在订阅之间移动 VM 可能很方便。 无需启动 VM 即可移动该 VM，并且该 VM 应在移动过程中继续运行。

> [!IMPORTANT]
>在移动过程中将创建新的资源 ID。 移动 VM 后，需要更新工具和脚本以使用新的资源 ID。
>
>

[!INCLUDE [virtual-machines-common-move-vm](../../../includes/virtual-machines-common-move-vm.md)]

## <a name="use-powershell-to-move-a-vm"></a>使用 PowerShell 移动 VM

要将虚拟机移到其他资源组，需确保同时移动所有依赖资源。 要获取每种资源的资源 ID 列表，请使用 [Get-AzResource](https://docs.microsoft.com/powershell/module/az.resources/get-azresource) cmdlet。

```azurepowershell-interactive
 Get-AzResource -ResourceGroupName myResourceGroup | Format-table -wrap -Property ResourceId
```

可以使用上一个命令的输出创建一个逗号分隔的资源指示列表，以[移动 AzResource](https://docs.microsoft.com/powershell/module/az.resources/move-azresource)以将每个资源移动到目标。

```azurepowershell-interactive
Move-AzResource -DestinationResourceGroupName "myDestinationResourceGroup" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```

要将资源移到其他订阅，请包含 **-DestinationSubscriptionId** 参数的值。

```azurepowershell-interactive
Move-AzResource -DestinationSubscriptionId "<myDestinationSubscriptionID>" `
    -DestinationResourceGroupName "<myDestinationResourceGroup>" `
    -ResourceId <myResourceId,myResourceId,myResourceId>
```


系统要求确认是否要移动指定的资源时，请输入“Y”进行确认  。

## <a name="next-steps"></a>后续步骤
可以在资源组和订阅之间移动许多不同类型的资源。 有关详细信息，请参阅[将资源移到新资源组或订阅](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。    
