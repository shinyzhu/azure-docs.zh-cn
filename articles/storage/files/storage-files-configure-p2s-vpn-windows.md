---
title: 在 Windows 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用 | Microsoft Docs
description: 如何在 Windows 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用
author: roygara
ms.service: storage
ms.topic: overview
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 95386af4522adca1d65e04b01c2a349a80e9ab8a
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/29/2020
ms.locfileid: "81273471"
---
# <a name="configure-a-point-to-site-p2s-vpn-on-windows-for-use-with-azure-files"></a>在 Windows 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用
你可以使用点到站点 (P2S) VPN 连接从 Azure 外部通过 SMB 装载 Azure 文件共享，而无需打开端口 445。 点到站点 VPN 连接是 Azure 与单个客户端之间的 VPN 连接。 若要将 P2S VPN 连接与 Azure 文件存储一起使用，需要为每个要连接的客户端配置 P2S VPN 连接。 如果有多个客户端需要从本地网络连接到 Azure 文件共享，则可以为每个客户端使用站点到站点 (S2S) VPN 连接，而不使用点到站点连接。 若要了解详细信息，请参阅[配置站点到站点 VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)。

我们强烈建议先阅读[关于直接访问 Azure 文件共享的网络注意事项](storage-files-networking-overview.md)，然后再继续阅读本方法指南文章，以全面讨论 Azure 文件存储可用的网络选项。

本文详细介绍了在 Windows（Windows 客户端和 Windows Server）上配置点到站点 VPN 以直接在本地装载 Azure 文件共享的相关步骤。 如果想要通过 VPN 路由 Azure 文件同步流量，请参阅[配置 Azure 文件同步代理和防火墙设置](storage-sync-files-firewall-and-proxy.md)。

## <a name="prerequisites"></a>先决条件
- 最新版本的 Azure PowerShell 模块。 若要详细了解如何安装 Azure PowerShell，请参阅[安装 Azure PowerShell 模块](https://docs.microsoft.com/powershell/azure/install-az-ps)并选择操作系统。 如果你更想在 Windows 上使用 Azure CLI，也可以使用，但下面是针对 Azure PowerShell 的说明。

- 要在本地装载的 Azure 文件共享。 Azure 文件共享部署在存储帐户中，是代表共享存储池的管理结构，可以在其中部署多个文件共享以及其他存储资源（例如 Blob 容器或队列）。 可以在[创建 Azure 文件共享](storage-how-to-create-file-share.md)中详细了解如何部署 Azure 文件共享和存储帐户。

- 包含要在本地装载的 Azure 文件共享的存储帐户的专用终结点。 若要详细了解如何创建专用终结点，请参阅[配置 Azure 文件存储网络终结点](storage-files-networking-endpoints.md?tabs=azure-powershell)。 

## <a name="deploy-a-virtual-network"></a>部署虚拟网络
若要通过点到站点 VPN 从本地访问 Azure 文件共享和其他 Azure 资源，必须创建虚拟网络或 VNet。 将自动创建的 P2S VPN 连接是本地 Windows 计算机与此 Azure 虚拟网络之间的桥。

以下 PowerShell 将创建包含三个子网的 Azure 虚拟网络：一个用于存储帐户的服务终结点，一个用于存储帐户的专用终结点（用于访问本地存储帐户，而无需为可能更改的存储帐户的公共 IP 创建自定义路由），以及一个用于提供 VPN 服务的虚拟网关。 

请记得将 `<region>`、`<resource-group>` 和 `<desired-vnet-name>` 替换为适合你的环境的值。

```PowerShell
$region = "<region>"
$resourceGroupName = "<resource-group>" 
$virtualNetworkName = "<desired-vnet-name>"

$virtualNetwork = New-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName `
    -Location $region `
    -AddressPrefix "192.168.0.0/16"

Add-AzVirtualNetworkSubnetConfig `
    -Name "ServiceEndpointSubnet" `
    -AddressPrefix "192.168.0.0/24" `
    -VirtualNetwork $virtualNetwork `
    -ServiceEndpoint "Microsoft.Storage" `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "PrivateEndpointSubnet" `
    -AddressPrefix "192.168.1.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

Add-AzVirtualNetworkSubnetConfig `
    -Name "GatewaySubnet" `
    -AddressPrefix "192.168.2.0/24" `
    -VirtualNetwork $virtualNetwork `
    -WarningAction SilentlyContinue | Out-Null

$virtualNetwork | Set-AzVirtualNetwork | Out-Null
$virtualNetwork = Get-AzVirtualNetwork `
    -ResourceGroupName $resourceGroupName `
    -Name $virtualNetworkName

$serviceEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "ServiceEndpointSubnet" }
$privateEndpointSubnet = $virtualNetwork.Subnets | `
    Where-Object { $_.Name -eq "PrivateEndpointSubnet" }
$gatewaySubnet = $virtualNetwork.Subnets | ` 
    Where-Object { $_.Name -eq "GatewaySubnet" }
```

## <a name="create-root-certificate-for-vpn-authentication"></a>为 VPN 身份验证创建根证书
为了对来自本地 Windows 计算机的 VPN 连接进行身份验证以访问虚拟网络，必须创建两个证书：根证书（将提供给虚拟机网关）和客户端证书（将使用根证书进行签名）。 以下 PowerShell 将创建根证书；创建 Azure 虚拟网络网关之后，将创建客户端证书，其中包含来自网关的信息。 

```PowerShell
$rootcertname = "CN=P2SRootCert"
$certLocation = "Cert:\CurrentUser\My"
$vpnTemp = "C:\vpn-temp\"
$exportedencodedrootcertpath = $vpnTemp + "P2SRootCertencoded.cer"
$exportedrootcertpath = $vpnTemp + "P2SRootCert.cer"

if (-Not (Test-Path $vpnTemp)) {
    New-Item -ItemType Directory -Force -Path $vpnTemp | Out-Null
}

if ($PSVersionTable.PSVersion -ge [System.Version]::new(6, 0)) {
    Install-Module WindowsCompatibility
    Import-WinModule PKI
}

$rootcert = New-SelfSignedCertificate `
    -Type Custom `
    -KeySpec Signature `
    -Subject $rootcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -KeyUsageProperty Sign `
    -KeyUsage CertSign

Export-Certificate `
    -Cert $rootcert `
    -FilePath $exportedencodedrootcertpath `
    -NoClobber | Out-Null

certutil -encode $exportedencodedrootcertpath $exportedrootcertpath | Out-Null

$rawRootCertificate = Get-Content -Path $exportedrootcertpath

[System.String]$rootCertificate = ""
foreach($line in $rawRootCertificate) { 
    if ($line -notlike "*Certificate*") { 
        $rootCertificate += $line 
    } 
}
```

## <a name="deploy-virtual-network-gateway"></a>部署虚拟网络网关
Azure 虚拟网络网关是本地 Windows 计算机将连接到的服务。 部署此服务需要两个基本组件：一个公共 IP（无论客户端位于何处，都可用此 IP 来标识其网关），一个之前创建的根证书（将用于对客户端进行身份验证）。

请记得将 `<desired-vpn-name-here>` 替换为要为这些资源使用的名称。

> [!Note]  
> 部署 Azure 虚拟网络网关最多需要 45 分钟。 部署此资源时，此 PowerShell 脚本将阻止部署完成。 这是正常情况。

```PowerShell
$vpnName = "<desired-vpn-name-here>" 
$publicIpAddressName = "$vpnName-PublicIP"

$publicIPAddress = New-AzPublicIpAddress `
    -ResourceGroupName $resourceGroupName `
    -Name $publicIpAddressName `
    -Location $region `
    -Sku Basic `
    -AllocationMethod Dynamic

$gatewayIpConfig = New-AzVirtualNetworkGatewayIpConfig `
    -Name "vnetGatewayConfig" `
    -SubnetId $gatewaySubnet.Id `
    -PublicIpAddressId $publicIPAddress.Id

$azRootCertificate = New-AzVpnClientRootCertificate `
    -Name "P2SRootCert" `
    -PublicCertData $rootCertificate

$vpn = New-AzVirtualNetworkGateway `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -Location $region `
    -GatewaySku VpnGw1 `
    -GatewayType Vpn `
    -VpnType RouteBased `
    -IpConfigurations $gatewayIpConfig `
    -VpnClientAddressPool "172.16.201.0/24" `
    -VpnClientProtocol IkeV2 `
    -VpnClientRootCertificates $azRootCertificate
```

## <a name="create-client-certificate"></a>创建客户端证书
客户端证书是使用虚拟网络网关的 URI 创建的。 此证书使用之前创建的根证书进行签名。

```PowerShell
$clientcertpassword = "1234"

$vpnClientConfiguration = New-AzVpnClientConfiguration `
    -ResourceGroupName $resourceGroupName `
    -Name $vpnName `
    -AuthenticationMethod EAPTLS

Invoke-WebRequest `
    -Uri $vpnClientConfiguration.VpnProfileSASUrl `
    -OutFile "$vpnTemp\vpnclientconfiguration.zip"

Expand-Archive `
    -Path "$vpnTemp\vpnclientconfiguration.zip" `
    -DestinationPath "$vpnTemp\vpnclientconfiguration"

$vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"
$vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

$exportedclientcertpath = $vpnTemp + "P2SClientCert.pfx"
$clientcertname = "CN=" + $vpnProfile.VpnServer

$clientcert = New-SelfSignedCertificate `
    -Type Custom `
    -DnsName $vpnProfile.VpnServer `
    -KeySpec Signature `
    -Subject $clientcertname `
    -KeyExportPolicy Exportable `
    -HashAlgorithm sha256 `
    -KeyLength 2048 `
    -CertStoreLocation $certLocation `
    -Signer $rootcert `
    -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.2")

$mypwd = ConvertTo-SecureString -String $clientcertpassword -Force -AsPlainText

Export-PfxCertificate `
    -FilePath $exportedclientcertpath `
    -Password $mypwd `
    -Cert $clientcert | Out-Null
```

## <a name="configure-the-vpn-client"></a>配置 VPN 客户端
Azure 虚拟网络网关将创建一个可下载的包，其中包含在本地 Windows 计算机上初始化 VPN 连接所需的配置文件。 我们将使用 Windows 10/Windows Server 2016 及更高版本的 [Always On VPN](https://docs.microsoft.com/windows-server/remote/remote-access/vpn/always-on-vpn/) 功能配置 VPN 连接。 此包还包含将配置旧版 Windows VPN 客户端的可执行包（如果确实需要）。 本指南使用 Always On VPN 而不是旧版 Windows VPN 客户端，因为使用 Always On VPN 客户端，最终用户无需拥有对其计算机的管理员权限，即可连接 Azure VPN 或断开与其建立的连接。 

以下脚本将安装针对虚拟网络网关进行身份验证所需的客户端证书，下载并安装 VPN 包。 请记得将 `<computer1>` 和 `<computer2>` 替换为所需的计算机。 通过将更多的 PowerShell 会话添加到 `$sessions` 数组，可以在所需的任意数量的计算机上运行此脚本。 使用帐户必须是每台计算机上的管理员。 如果其中一台计算机是正在运行脚本的本地计算机，则必须从提升权限的 PowerShell 会话运行该脚本。 

```PowerShell
$sessions = [System.Management.Automation.Runspaces.PSSession[]]@()
$sessions += New-PSSession -ComputerName "<computer1>"
$sessions += New-PSSession -ComputerName "<computer2>"

foreach ($session in $sessions) {
    Invoke-Command -Session $session -ArgumentList $vpnTemp -ScriptBlock { 
        $vpnTemp = $args[0]
        if (-Not (Test-Path $vpnTemp)) {
            New-Item `
                -ItemType Directory `
                -Force `
                -Path "C:\vpn-temp" | Out-Null
        }
    }

    Copy-Item `
        -Path $exportedclientcertpath, $exportedrootcertpath, "$vpnTemp\vpnclientconfiguration.zip" `
        -Destination $vpnTemp `
        -ToSession $session

    Invoke-Command `
        -Session $session `
        -ArgumentList `
            $mypwd, `
            $vpnTemp, `
            $virtualNetworkName `
        -ScriptBlock { 
            $mypwd = $args[0] 
            $vpnTemp = $args[1]
            $virtualNetworkName = $args[2]

            Import-PfxCertificate `
                -Exportable `
                -Password $mypwd `
                -CertStoreLocation "Cert:\LocalMachine\My" `
                -FilePath "$vpnTemp\P2SClientCert.pfx" | Out-Null

            Import-Certificate `
                -FilePath "$vpnTemp\P2SRootCert.cer" `
                -CertStoreLocation "Cert:\LocalMachine\Root" | Out-Null

            Expand-Archive `
                -Path "$vpnTemp\vpnclientconfiguration.zip" `
                -DestinationPath "$vpnTemp\vpnclientconfiguration"
            $vpnGeneric = "$vpnTemp\vpnclientconfiguration\Generic"

            $vpnProfile = ([xml](Get-Content -Path "$vpnGeneric\VpnSettings.xml")).VpnProfile

            Add-VpnConnection `
                -Name $virtualNetworkName `
                -ServerAddress $vpnProfile.VpnServer `
                -TunnelType Ikev2 `
                -EncryptionLevel Required `
                -AuthenticationMethod MachineCertificate `
                -SplitTunneling `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.Routes `
                -AllUserConnection

            Add-VpnConnectionRoute `
                -Name $virtualNetworkName `
                -DestinationPrefix $vpnProfile.VpnClientAddressPool `
                -AllUserConnection

            rasdial $virtualNetworkName
        }
}

Remove-Item -Path $vpnTemp -Recurse
```

## <a name="mount-azure-file-share"></a>装载 Azure 文件共享
现在，你已设置点到站点 VPN，可以使用它在通过 PowerShell 设置的计算机上装载 Azure 文件共享。 以下示例将装载共享、列出共享的根目录（证明实际上已装载该共享），然后卸载该共享。 遗憾的是，无法通过 PowerShell 远程处理永久装载共享。 若要永久装载，请参阅[将 Azure 文件共享与 Windows 配合使用](storage-how-to-use-files-windows.md)。 

```PowerShell
$myShareToMount = "<file-share>"

$storageAccountKeys = Get-AzStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName
$storageAccountKey = ConvertTo-SecureString `
    -String $storageAccountKeys[0].Value `
    -AsPlainText `
    -Force

$nic = Get-AzNetworkInterface -ResourceId $privateEndpoint.NetworkInterfaces[0].Id
$storageAccountPrivateIP = $nic.IpConfigurations[0].PrivateIpAddress

Invoke-Command `
    -Session $sessions `
    -ArgumentList  `
        $storageAccountName, `
        $storageAccountKey, `
        $storageAccountPrivateIP, `
        $myShareToMount `
    -ScriptBlock {
        $storageAccountName = $args[0]
        $storageAccountKey = $args[1]
        $storageAccountPrivateIP = $args[2]
        $myShareToMount = $args[3]

        $credential = [System.Management.Automation.PSCredential]::new(
            "AZURE\$storageAccountName", 
            $storageAccountKey)

        New-PSDrive `
            -Name Z `
            -PSProvider FileSystem `
            -Root "\\$storageAccountPrivateIP\$myShareToMount" `
            -Credential $credential `
            -Persist | Out-Null
        Get-ChildItem -Path Z:\
        Remove-PSDrive -Name Z
    }
```

## <a name="see-also"></a>另请参阅
- [关于直接访问 Azure 文件共享的网络注意事项](storage-files-networking-overview.md)
- [在 Linux 上配置点到站点 (P2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-p2s-vpn-linux.md)
- [配置站点到站点 (S2S) VPN 以与 Azure 文件存储一起使用](storage-files-configure-s2s-vpn.md)
