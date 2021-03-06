---
title: Azure 公共 IP 地址前缀
description: 了解 Azure 公共 IP 地址前缀的涵义以及如何借助该前缀向资源分配可预测的公共 IP 地址。
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2020
ms.author: allensu
ms.openlocfilehash: 0f71f845ef3209146ead79cafae2f3aa5c8c6d7d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82144508"
---
# <a name="public-ip-address-prefix"></a>公共 IP 地址前缀

公共 IP 地址前缀是预留供 Azure 中的公共终结点使用的 IP 地址范围。 Azure 根据指定的地址数为订阅分配连续的地址范围。 如果不熟悉公共地址，请参阅[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。

公共 IP 地址是从各 Azure 区域的地址池分配的。 可[下载](https://www.microsoft.com/download/details.aspx?id=56519) Azure 在每个区域使用的范围列表。 例如，40.121.0.0/16 是 Azure 在美国东部区域中使用的 100 多个范围之一。 该范围包括从 40.121.0.1 到 40.121.255.254 的可用地址。

可通过指定一个名称和希望前缀包括的地址数，在 Azure 区域和订阅中创建公共 IP 地址前缀。 例如，如果创建一个公共 IP 地址前缀 /28，则 Azure 会从其范围之一为你分配 16 个地址。 除非创建范围，否则你不会知道 Azure 将分配哪个范围给你，但这些地址是连续的。 公共 IP 地址前缀会产生费用。 有关详细信息，请参阅[公共 IP 地址定价](https://azure.microsoft.com/pricing/details/ip-addresses)。

## <a name="why-create-a-public-ip-address-prefix"></a>为什么要创建公共 IP 地址前缀？

创建公共 IP 地址资源时，Azure 会从区域中使用的任何一个范围分配可用的公共 IP 地址。 Azure 分配地址后，你就会知道是哪一个地址，但在 Azure 分配地址之前，你并不知道可能会分配哪个地址。 有时这可能会是一个问题，例如，你或你的业务合作伙伴设置防火墙规则，允许使用特定 IP 地址。 每当向资源分配新的公共 IP 地址时，必需将该地址添加到防火墙规则。 当从公共 IP 地址前缀向资源分配地址时，无需在每次分配一个地址时都更新防火墙规则，因为可以将整个范围添加到规则。

## <a name="benefits"></a>优点

- 可以从已知范围创建公共 IP 地址资源。
- 你或你的业务合作伙伴可以使用包括当前分配的公共 IP 地址以及尚未分配的地址范围创建防火墙规则。 这样便可在向新资源分配 IP 地址时省去更改防火墙规则的需要。
- 可创建的默认范围大小为 /28 或 16 个 IP 地址。
- 可创建的范围数没有限制，但 Azure 订阅中可包括的静态公共 IP 地址的最大数有一定限制。 因此，可创建的范围数包含的静态公共 IP 地址数不能超过订阅中包含的地址数。 有关详细信息，请参阅 [Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)。
- 使用来自前缀的地址创建的地址可以分配到可向其分配公共 IP 地址的任何 Azure 资源。
- 可轻松查看分配的 IP 地址和范围中尚未分配的地址。

## <a name="scenarios"></a>方案
可将以下资源关联到来自前缀的静态公共 IP 地址：

|资源|方案|步骤|
|---|---|---|
|虚拟机| 将来自某个前缀的公共 IP 关联到 Azure 中的虚拟机可降低在防火墙中创建允许列表 IP 所产生的管理开销。 可使用单个防火墙规则简化创建整个前缀允许列表的过程。 缩放 Azure 中的虚拟机时，可关联来自同一前缀的 IP，从而节省成本、时间和管理开销。| 将来自某个前缀的 IP 关联到虚拟机：1. [创建前缀。](manage-public-ip-address-prefix.md) 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) 3. [将 IP 关联到虚拟机网络接口。](virtual-network-network-interface-addresses.md#add-ip-addresses) 也可以[将 IP 关联到虚拟机规模集](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)。
| 标准负载均衡器 | 将来自某个前缀的公共 IP 关联到负载均衡器的前端 IP 配置或出站规则可确保简化 Azure 公共 IP 地址空间。 可以通过整理源由公共 IP 前缀定义的连续 IP 地址范围的出站连接来简化你的方案。 | 将来自某个前缀的 IP 关联到负载均衡器：1. [创建前缀。](manage-public-ip-address-prefix.md) 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) 3. 创建负载均衡器时，选择或更新在前面第 2 步中创建的 IP 作为负载均衡器的前端 IP。 |
| Azure 防火墙 | 可使用来自前缀的公共 IP 地址作为出站 SNAT。 这意味着所有出站虚拟网络流量都会转换为 [Azure 防火墙](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)公共 IP。 该 IP 来自预先确定的前缀，因此很容易提前知道 Azure 中公共 IP 占用情况将来的情况。 | 1.[创建前缀。](manage-public-ip-address-prefix.md) 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) 3. [部署 Azure 防火墙](../firewall/tutorial-firewall-deploy-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json#deploy-the-firewall)时，请务必选择之前通过前缀分配的 IP。|
| 应用程序网关 v2 | 对于自动缩放和区域冗余应用程序网关 v2，可以使用来自前缀的公共 IP。 该 IP 来自预先确定的前缀，因此很容易提前知道 Azure 中公共 IP 占用情况将来的情况。 | 1.[创建前缀。](manage-public-ip-address-prefix.md) 2. [从前缀创建 IP。](manage-public-ip-address-prefix.md) 3. [部署应用程序网关](../application-gateway/quick-create-portal.md#create-an-application-gateway)时，请务必选择之前通过前缀分配的 IP。|

## <a name="constraints"></a>约束

- 不能指定前缀的 IP 地址。 Azure 将根据指定的大小分配前缀的 IP 地址。
- 默认情况下，可以创建最多为16个 IP 地址的前缀或/28。 查看[网络限制增加请求](https://docs.microsoft.com/azure/azure-portal/supportability/networking-quota-requests)和[Azure 限制](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#azure-resource-manager-virtual-networking-limits)以获取详细信息。
- 创建前缀后，无法更改该范围。
- 仅使用标准 SKU 创建的静态公共 IP 地址可从前缀范围进行分配。 若要详细了解公共 IP 地址 SKU，请参阅[公共 IP 地址](virtual-network-ip-addresses-overview-arm.md#public-ip-addresses)。
- 范围中的地址只能分配到 Azure 资源管理器资源。 这些地址不能分配到通过经典部署模型创建的资源。
- 使用前缀创建的所有公共 IP 地址都必须位于同一 Azure 区域和订阅作为前缀，并且必须分配到相同区域和订阅中的资源。
- 如果前缀中的任何地址被分配到与某个资源关联的公共 IP 地址资源，则无法删除该前缀。 首先应取消关联所有公共 IP 地址资源，这些资源通过前缀分配有 IP 地址。


## <a name="next-steps"></a>后续步骤

- [创建](manage-public-ip-address-prefix.md)公共 IP 地址前缀
