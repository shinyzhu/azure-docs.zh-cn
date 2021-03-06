---
title: 设置基于 Azure VPN 网关指标的警报
description: 基于 VPN 网关指标配置警报的步骤
services: vpn-gateway
author: anzaman
ms.service: vpn-gateway
ms.topic: conceptual
ms.date: 04/22/2019
ms.author: alzam
ms.openlocfilehash: 01704030ee3142cac9a328e1a3edc024651919a0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "82127903"
---
# <a name="set-up-alerts-on-vpn-gateway-metrics"></a>基于 VPN 网关指标设置警报

本文介绍如何基于 Azure VPN 网关指标设置警报。 Azure Monitor 提供为 Azure 资源设置警报的功能。 可以为“VPN”类型的虚拟网关设置警报。


|**指标**   | **单位** | **粒度** | **说明** | 
|---       | ---        | ---       | ---            | ---       |
|**AverageBandwidth**| 字节/秒  | 5 分钟| 网关上所有站点到站点连接的组合带宽平均利用率。     |
|**P2SBandwidth**| 字节/秒  | 1 分钟  | 网关上所有点到站点连接的组合带宽平均利用率。    |
|**P2SConnectionCount**| 计数  | 1 分钟  | 网关上点到站点连接的计数。   |
|**TunnelAverageBandwidth** | 字节/秒    | 5 分钟  | 在网关上创建的隧道的带宽平均利用率。 |
|**TunnelEgressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传出流量。   |
|**TunnelEgressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传出数据包的计数。   |
|**TunnelEgressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传出数据包的计数。 |
|**TunnelIngressBytes** | 字节 | 5 分钟 | 在网关上创建的隧道中的传入流量。   |
|**TunnelIngressPackets** | 计数 | 5 分钟 | 在网关上创建的隧道中的传入数据包的计数。   |
|**TunnelIngressPacketDropTSMismatch** | 计数 | 5 分钟 | 隧道中因流量选择器不匹配导致的被丢弃传入数据包的计数。 |


## <a name="set-up-azure-monitor-alerts-based-on-metrics-by-using-the-azure-portal"></a><a name="setup"></a>使用 Azure 门户基于指标设置 Azure Monitor 警报

以下示例步骤将在网关上针对以下项创建一个警报：

- **指标：** TunnelAverageBandwidth
- **条件：** 带宽 > 10 字节/秒
- **时限：** 5 分钟
- **警报操作：** Email



1. 转到虚拟网关资源，从“监视”选项卡中选择“警报”。   然后创建新的警报规则，或者编辑现有的警报规则。

   ![用于创建警报规则的选项](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert1.png "创建")

2. 选择 VPN 网关作为资源。

   ![资源列表中的“选择”按钮和 VPN 网关](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert2.png "选择")

3. 选择要为警报配置的指标。

   ![指标列表中的所选指标](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert3.png "选择")
4. 配置信号逻辑。 它有三个组件：

    a. **维度**：如果指标包含维度，则可选择特定的维度值，这样警报就只评估该维度的数据。 这些是可选的。

    b. **条件**：这是用于评估指标值的操作。

    c. **时间**：指定指标数据的粒度以及时间段，以便评估警报。

   ![信号逻辑配置详情](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert4.png "Select")

5. 若要查看配置的规则，请选择“管理警报规则”。 

   ![用于管理警报规则的按钮](./media/vpn-gateway-howto-setup-alerts-virtual-network-gateway-metric/metric-alert8.png "Select")

## <a name="next-steps"></a>后续步骤

若要配置对隧道资源日志的警报，请参阅[设置 VPN 网关资源日志的警报](vpn-gateway-howto-setup-alerts-virtual-network-gateway-log.md)。
