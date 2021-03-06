---
title: Abrir portas para uma VM usando o PowerShell | Microsoft Docs
description: "Saiba como abrir uma porta/criar um ponto de extremidade para sua VM do Windows usando o modelo de implantação do Azure Resource Manager e o Azure PowerShell"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: cf45f7d8-451a-48ab-8419-730366d54f1e
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/27/2016
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b1cded7214406821600f9e3d5a6b84a277fcc07a


---
# <a name="opening-ports-and-endpoints-to-a-vm-in-azure-using-powershell"></a>Abrir portas e pontos de extremidade para uma VM no Azure usando o PowerShell
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Comandos rápidos
Para criar um Grupo de Segurança de Rede e as regras de ACL, você precisa [ter a versão mais recente do Azure PowerShell instalada](../powershell-install-configure.md). Você também pode [executar essas etapas usando o Portal do Azure](virtual-machines-windows-nsg-quickstart-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Faça logon na sua Conta do Azure:

```powershell
Login-AzureRmAccount
```

Nos exemplos a seguir, substitua os nomes de parâmetro de exemplo com seus próprios valores. Os nomes de parâmetro de exemplo incluíram `myResourceGroup`, `myNetworkSecurityGroup` e `myVnet`.

Criar uma regra. O exemplo a seguir cria uma regra denominada `myNetworkSecurityGroupRule` para permitir o tráfego TCP na porta 80:

```powershell
$httprule = New-AzureRmNetworkSecurityRuleConfig -Name "myNetworkSecurityGroupRule" `
    -Description "Allow HTTP" -Access "Allow" -Protocol "Tcp" -Direction "Inbound" `
    -Priority "100" -SourceAddressPrefix "Internet" -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 80
```

Em seguida, crie seu Grupo de Segurança de Rede e atribua a regra de HTTP que você acabou de criar conforme descrito a seguir. O exemplo a seguir cria um Grupo de Segurança de Rede denominado `myNetworkSecurityGroup`:

```powershell
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName "myResourceGroup" `
    -Location "WestUS" -Name "myNetworkSecurityGroup" -SecurityRules $httprule
```

Agora, vamos atribuir seu Grupo de Segurança de Rede a uma sub-rede. O exemplo a seguir atribui uma rede virtual existente denominada `myVnet` à variável `$vnet`:

```powershell
$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName "myResourceGroup" `
    -Name "myVnet"
```

Associe seu Grupo de Segurança de Rede à sua sub-rede. O exemplo a seguir associa a sub-rede denominada `mySubnet` ao seu Grupo de Segurança de Rede:

```powershell
$subnetPrefix = $vnet.Subnets|?{$_.Name -eq 'mySubnet'}

Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "mySubnet" `
    -AddressPrefix $subnetPrefix.AddressPrefix `
    -NetworkSecurityGroup $nsg
```

Por fim, atualize sua rede virtual para que as alterações entrem em vigor:

```powershell
Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
```


## <a name="more-information-on-network-security-groups"></a>Mais informações sobre os Grupos de Segurança de Rede
Os comandos rápidos aqui permitem que você coloque tudo em funcionamento com o tráfego que flui para sua VM. Os Grupos de Segurança de Rede fornecem muitos recursos excelentes e granularidade para controlar o acesso aos recursos. Você pode ler mais sobre a [criação de um Grupo de Segurança de Rede e as regras ACL aqui](../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Você pode definir Grupos de Segurança de Rede e regras de ACL como parte dos modelos do Azure Resource Manager. Leia mais sobre a [criação de Grupos de Segurança de Rede com modelos](../virtual-network/virtual-networks-create-nsg-arm-template.md).

Se precisar usar o encaminhamento de porta para mapear uma porta externa exclusiva para uma porta interna em sua VM, use um balanceador de carga e regras de NAT (Conversão de Endereços de Rede). Por exemplo, talvez você queira expor a porta TCP 8080 externamente e direcionar o tráfego para a porta TCP 80 em uma VM. Você pode aprender sobre a [criação de um balanceador de carga para a Internet](../load-balancer/load-balancer-get-started-internet-arm-ps.md).

## <a name="next-steps"></a>Próximas etapas
Neste exemplo, você criou uma regra simples para permitir o tráfego HTTP. Você pode encontrar informações sobre a criação de ambientes mais detalhados nos seguintes artigos:

* [Visão geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md)
* [O que é um NSG (grupo de segurança de rede)?](../virtual-network/virtual-networks-nsg.md)
* [Visão Geral do Azure Resource Manager para Balanceadores de Carga](../load-balancer/load-balancer-arm.md)




<!--HONumber=Nov16_HO3-->


