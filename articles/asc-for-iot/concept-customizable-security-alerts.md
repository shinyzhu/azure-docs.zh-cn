---
title: 可自定义的安全警报
description: 了解使用 Azure 安全中心进行 IoT 功能和服务的可自定义安全警报和建议的补救措施。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/04/2020
ms.author: mlottner
ms.openlocfilehash: 3b39d70c60a4c9701d0a8bafde17b241fe01cc46
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311638"
---
# <a name="azure-security-center-for-iot-security-alerts"></a>用于 IoT 安全警报的 Azure 安全中心

用于 IoT 的 Azure 安全中心使用高级分析和威胁智能来持续分析 IoT 解决方案，以提醒你应对恶意活动。

我们鼓励你根据你对预期设备行为的了解来创建自定义警报，以确保在独特的组织部署和布局中，警报充当潜在危害的最有效指标。

下面列出了适用于 IoT 警报的 Azure 安全中心，它们根据预期的 IoT 中心和/或设备行为进行定义。 有关如何自定义每个警报的更多详细信息，请参阅[创建自定义警报](quickstart-create-custom-alerts.md)。

## <a name="iot-hub-alerts-available-for-customization"></a>可供自定义的 IoT 中心警报

| 严重性 | 警报名称 | 数据源 | 说明 | 建议的补救措施|
|---|---|---|---|---|
| 低      | 自定义警报 - AMQP 协议中云到设备的消息数目超出了允许的范围          | IoT 中心     | 特定时间范围内的云到设备消息（AMQP 协议）数目超出了配置的允许范围。||
| 低      | 自定义警报 - AMQP 协议中已拒绝的云到设备的消息数目超出了允许的范围 | IoT 中心     | 设备在特定时间范围内拒绝的云到设备消息（AMQP 协议）数目超出了配置的允许范围。||
| 低      | 自定义警报 - AMQP 协议中设备到云的消息数目超出了允许的范围      | IoT 中心     | 特定时间范围内的设备到云消息（AMQP 协议）数量超出了配置的允许范围。|   |
| 低      | 自定义警报 - 直接方法调用数目超出了允许的范围 | IoT 中心     | 特定时间范围内的直接方法调用数量超出了当前配置的允许范围||
| 低      | 自定义警报 - 文件上传数目超出了允许的范围 | IoT 中心     | 特定时间范围内的文件上传数量超出了当前配置的允许范围| |
| 低      | 自定义警报 - HTTP 协议中云到设备的消息数目超出了允许的范围 | IoT 中心     | 时间窗口中云到设备的消息（HTTP 协议）数量不在配置的允许范围内                                  |
| 低      | 自定义警报 - HTTP 协议中已拒绝的云到设备的消息数目不在允许的范围内 | IoT 中心     | 特定时间范围内的云到设备消息（HTTP 协议）数量超出了配置的允许范围。 |
| 低      | 自定义警报 - HTTP 协议中设备到云的消息数目超出了允许的范围 | IoT 中心| 特定时间范围内的设备到云消息（HTTP 协议）数量超出了配置的允许范围。|    |
| 低      | 自定义警报 - MQTT 协议中云到设备的消息数目超出了允许的范围 | IoT 中心     | 特定时间范围内的云到设备消息（MQTT 协议）数量超出了配置的允许范围。|   |
| 低      | 自定义警报 - MQTT 协议中已拒绝的云到设备消息数目超出了允许的范围 | IoT 中心     | 设备在特定时间范围内拒绝的云到设备消息（MQTT 协议）数量超出了配置的允许范围。 |
| 低      | 自定义警报 - MQTT 协议中设备到云的消息数目超出了允许的范围          | IoT 中心     | 特定时间范围内的设备到云消息（MQTT 协议）数量超出了配置的允许范围。|
| 低      | 自定义警报 - 命令队列清除数目超出了允许的范围                               | IoT 中心     | 特定时间范围内的命令队列清除数量超出了当前配置的允许范围。||
| 低      | 自定义警报 - 模块孪生更新数目超出了允许的范围                                       | IoT 中心     | 特定时间范围内的模块孪生更新数量超出了当前配置的允许范围。|
| 低      | 自定义警报 - 未授权的操作数目超出了允许的范围  | IoT 中心     | 特定时间范围内的未授权操作数量超出了当前配置的允许范围。|
|

## <a name="agent-alerts-available-for-customization"></a>可用于自定义的代理警报

| 严重性 | 警报名称 | 数据源 | 说明 | 建议的补救措施|
|---|---|---|---|---|
| 低      | 自定义警报 - 活动连接数目超出了允许的范围  | Agent       | 特定时间范围内的活动连接数目超出了当前配置的允许范围。|  调查设备日志。 了解连接的来源位置，并确定该连接是良性还是恶意的。 如果是恶意的，请删除可能的恶意软件并了解来源。 如果是良性的，请将来源添加到允许的连接列表。  |
| 低      | 自定义警报 - 与不允许的 IP 建立了出站连接                             | Agent       | 与允许的 IP 列表以外的 IP 建立了出站连接。 |调查设备日志。 了解连接的来源位置，并确定该连接是良性还是恶意的。 如果是恶意的，请删除可能的恶意软件并了解来源。 如果是良性的，请将来源添加到允许的 IP 列表。                        |
| 低      | 自定义警报 - 失败的本地登录次数超出了允许的范围                               | Agent       | 特定时间范围内的失败本地登录数量超出了当前配置的允许范围。 |   |
| 低      | 自定义警报 - 允许的用户列表以外的用户登录 | Agent       | 允许的用户列表以外的本地用户登录到了设备。|  如果你正在保存原始数据，请导航到 Log Analytics 帐户，并使用该数据调查设备，确定来源，然后修复这些设置的允许/阻止列表。 如果当前不是在保存原始数据，请转到设备，并修复这些设置的允许/阻止列表。|
| 低      | 自定义警报 - 执行了不允许的进程 | Agent       | 在设备上执行了某个不允许的进程。 |如果你正在保存原始数据，请导航到 Log Analytics 帐户，并使用该数据调查设备，确定来源，然后修复这些设置的允许/阻止列表。 如果当前不是在保存原始数据，请转到设备，并修复这些设置的允许/阻止列表。  |
|

## <a name="next-steps"></a>后续步骤

- 了解如何[自定义警报](quickstart-create-custom-alerts.md)
- 用于 IoT 服务的 Azure 安全中心[概述](overview.md)
- 了解如何[访问安全数据](how-to-security-data-access.md)
- 了解有关[调查设备的](how-to-investigate-device.md)详细信息
