---
title: Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn | Microsoft Docs
description: "Este tutorial explica as etapas de criação de um ouvinte de grupo de disponibilidade AlwaysOn no Azure que é acessível externamente usando o endereço IP virtual público do serviço de nuvem associado."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: a2453032-94ab-4775-b976-c74d24716728
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/12/2016
ms.author: MikeRayMSFT
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 51c7e858f063b14d362668f65fc2979eafad26ca


---
# <a name="configure-an-external-listener-for-always-on-availability-groups-in-azure"></a>Configurar um ouvinte externo para grupos de disponibilidade AlwaysOn no Azure
> [!div class="op_single_selector"]
> * [Ouvinte interno](virtual-machines-windows-classic-ps-sql-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Ouvinte externo](virtual-machines-windows-classic-ps-sql-ext-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

Este tópico mostra como configurar um ouvinte para um grupo de disponibilidade AlwaysOn que está acessível externamente na Internet. Isso se tornou possível associando o endereço **VIP (IP virtual público)** do serviço de nuvem ao ouvinte.

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

O seu grupo de disponibilidade pode conter somente réplicas locais, somente no Azure ou locais e no Azure para configurações híbridas. As réplicas do Azure podem residir na mesma região ou em várias regiões usando várias redes virtuais (VNets). As etapas a seguir pressupõem que você já tenha [configurado um grupo de disponibilidade](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) , mas não configurou um ouvinte.

## <a name="guidelines-and-limitations-for-external-listeners"></a>Diretrizes e limitações para ouvintes externos
Observe as seguintes limitações sobre o ouvinte do grupo de disponibilidade no Azure ao implantar usando o endereço VIP público do serviço de nuvem:

* Há suporte para o ouvinte do grupo de disponibilidade no Windows Server 2008 R2, Windows Server 2012 e Windows Server 2012 R2.
* O aplicativo cliente deve residir em um serviço de nuvem diferente daquele que contém as VMs do grupo de disponibilidade. O Azure não dá suporte a retorno de servidor direto com cliente e servidor no mesmo serviço de nuvem.
* Por padrão, as etapas neste artigo mostram como configurar um ouvinte para usar o endereço de IP Virtual (VIP) do serviço de nuvem. No entanto, é possível reservar e criar vários endereços VIP para seu serviço de nuvem. Isso permite que você use as etapas neste artigo para criar vários ouvintes associados a cada VIP diferente. Para obter informações sobre como criar vários endereços VIP, consulte [VIPs múltiplos por serviço de nuvem](../load-balancer/load-balancer-multivip.md).
* Se você estiver criando um ouvinte para um ambiente híbrido, a rede local deve ter conectividade com a Internet pública além da VPN site a site com a rede virtual do Azure. Quando estiver na sub-rede do Azure, o ouvinte do grupo de disponibilidade será alcançável somente pelo endereço IP público do respectivo serviço de nuvem.
* Não há suporte para criar um ouvinte externo no mesmo serviço de nuvem em que você também tem um ouvinte externo usando o Balanceador de Carga Interna (ILB).

## <a name="determine-the-accessibility-of-the-listener"></a>Determine a acessibilidade do ouvinte
[!INCLUDE [ag-listener-accessibility](../../includes/virtual-machines-ag-listener-determine-accessibility.md)]

Este artigo se concentra na criação de um ouvinte que use o **balanceamento de carga externo**. Se você quiser um ouvinte privado em sua rede virtual, consulte a versão deste artigo que fornece as etapas para configurar um [ouvinte com ILB](virtual-machines-windows-classic-ps-sql-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="create-load-balanced-vm-endpoints-with-direct-server-return"></a>Criar pontos de extremidade da VM com balanceamento de carga com retorno de servidor direto
O Balanceamento de carga externo usa o endereço IP Virtual público do serviço de nuvem que hospeda suas VMs. Portanto, você não precisa criar ou configurar o balanceador de carga nesse caso.

[!INCLUDE [load-balanced-endpoints](../../includes/virtual-machines-ag-listener-load-balanced-endpoints.md)]

1. Copie o script do PowerShell abaixo em um editor de texto e defina os valores de variáveis para se adequar ao seu ambiente (os padrões foram fornecidos para alguns parâmetros). Note que o seu grupo de disponibilidade abrange regiões do Azure, você deve executar o script uma vez em cada datacenter para o serviço de nuvem e os nós que residem naquele datacenter.
   
        # Define variables
        $ServiceName = "<MyCloudService>" # the name of the cloud service that contains the availability group nodes
        $AGNodes = "<VM1>","<VM2>","<VM3>" # all availability group nodes containing replicas in the same cloud service, separated by commas
   
        # Configure a load balanced endpoint for each node in $AGNodes, with direct server return enabled
        ForEach ($node in $AGNodes)
        {
            Get-AzureVM -ServiceName $ServiceName -Name $node | Add-AzureEndpoint -Name "ListenerEndpoint" -Protocol "TCP" -PublicPort 1433 -LocalPort 1433 -LBSetName "ListenerEndpointLB" -ProbePort 59999 -ProbeProtocol "TCP" -DirectServerReturn $true | Update-AzureVM
        }
2. Depois de ter definido as variáveis, copie o script do editor de texto para a sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.

## <a name="verify-that-kb2854082-is-installed-if-necessary"></a>Verifique se KB2854082 está instalado, se necessário
[!INCLUDE [kb2854082](../../includes/virtual-machines-ag-listener-kb2854082.md)]

## <a name="open-the-firewall-ports-in-availability-group-nodes"></a>Abra as portas de firewall em nós do grupo de disponibilidade
[!INCLUDE [firewall](../../includes/virtual-machines-ag-listener-open-firewall.md)]

## <a name="create-the-availability-group-listener"></a>Crie o ouvinte do grupo de disponibilidade
[!INCLUDE [firewall](../../includes/virtual-machines-ag-listener-create-listener.md)]

1. Para o balanceamento de carga externo, você deve obter o endereço IP virtual público do serviço de nuvem que contém suas réplicas. Faça logon no portal clássico do Azure. Navegue até o serviço de nuvem que contém o grupo de disponibilidade da VM. Abra a exibição **Painel** .
2. Anote o endereço mostrado em **Endereço IP Virtual Público (VIP)**. Se a sua solução abrange VNets, repita essa etapa para cada serviço de nuvem que contém uma VM que hospeda uma réplica.
3. Em uma das VMs, copie o script do PowerShell abaixo em um editor de texto e defina as variáveis para os valores que você anotou anteriormente.
   
        # Define variables
        $ClusterNetworkName = "<ClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $CloudServiceIP = "<X.X.X.X>" # Public Virtual IP (VIP) address of your cloud service
   
        Import-Module FailoverClusters
   
        # If you are using Windows Server 2012 or higher, use the Get-Cluster Resource command. If you are using Windows Server 2008 R2, use the cluster res command. Both commands are commented out. Choose the one applicable to your environment and remove the # at the beginning of the line to convert the comment to an executable line of code.
   
        # Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$CloudServiceIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"OverrideAddressMatch"=1;"EnableDhcp"=0}
        # cluster res $IPResourceName /priv enabledhcp=0 overrideaddressmatch=1 address=$CloudServiceIP probeport=59999  subnetmask=255.255.255.255
4. Assim que você tiver definido as variáveis, abra uma janela elevada do Windows PowerShell, depois copie o script do editor de texto e cole-o na sua sessão do Azure PowerShell para executá-lo. Se o prompt ainda mostrar >>, digite ENTER novamente para certificar-se de que o script comece a ser executado.
5. Repita esse procedimento em cada VM. Esse script configura o recurso de endereço IP com o endereço IP do serviço de nuvem e define outros parâmetros como a porta de investigação. Quando o recurso de endereço IP é colocado online, ele pode responder à sondagem na porta de investigação do ponto de extremidade com balanceamento de carga criado anteriormente neste tutorial.

## <a name="bring-the-listener-online"></a>Coloque o ouvinte online
[!INCLUDE [Bring-Listener-Online](../../includes/virtual-machines-ag-listener-bring-online.md)]

## <a name="follow-up-items"></a>Itens de acompanhamento
[!INCLUDE [Follow-up](../../includes/virtual-machines-ag-listener-follow-up.md)]

## <a name="test-the-availability-group-listener-within-the-same-vnet"></a>Testar o ouvinte do grupo de disponibilidade (na mesma VNet)
[!INCLUDE [Test-Listener-Within-VNET](../../includes/virtual-machines-ag-listener-test.md)]

## <a name="test-the-availability-group-listener-over-the-internet"></a>Testar o ouvinte de grupo de disponibilidade (pela Internet)
Para acessar o ouvinte de fora da rede virtual, você deve estar usando balanceamento de carga externa/pública (descrito neste tópico), em vez de ILB, que é acessível somente dentro do mesmo VNet. Na cadeia de conexão, você deve especificar o nome do serviço de nuvem. Por exemplo, se você tiver um serviço de nuvem com o nome *meuserviçodenuvem*, a instrução sqlcmd seria a seguinte:

    sqlcmd -S "mycloudservice.cloudapp.net,<EndpointPort>" -d "<DatabaseName>" -U "<LoginId>" -P "<Password>"  -Q "select @@servername, db_name()" -l 15

Ao contrário do exemplo anterior, a autenticação do SQL deve ser usada, porque o chamador não pode usar a autenticação do Windows pela Internet. Para obter mais informações, consulte [AlwaysOn Availability Group in Azure VM: Client Connectivity Scenarios](http://blogs.msdn.com/b/sqlcat/archive/2014/02/03/alwayson-availability-group-in-windows-azure-vm-client-connectivity-scenarios.aspx)(Grupo de disponibilidade AlwaysOn na VM do Azure: cenários de conectividade do cliente). Ao usar a autenticação do SQL, certifique-se de que você crie o mesmo logon em ambas as réplicas. Para saber mais sobre como solucionar problemas de logons com Grupos de Disponibilidade, consulte [Como mapear logons ou usar um usuário de banco de dados SQL contido para se conectar a outras réplicas e mapear para bancos de dados de disponibilidade](http://blogs.msdn.com/b/alwaysonpro/archive/2014/02/19/how-to-map-logins-or-use-contained-sql-database-user-to-connect-to-other-replicas-and-map-to-availability-databases.aspx).

Se as réplicas do AlwaysOn estiverem em sub-redes diferentes, os clientes deverão especificar **MultisubnetFailover=True** na cadeia de conexão. Isso resulta em tentativas de conexão paralela para réplicas em sub-redes diferentes. Observe que esse cenário inclui uma implantação de grupo de disponibilidade do AlwaysOn entre regiões.

## <a name="next-steps"></a>Próximas etapas
[!INCLUDE [Listener-Next-Steps](../../includes/virtual-machines-ag-listener-next-steps.md)]




<!--HONumber=Nov16_HO3-->


