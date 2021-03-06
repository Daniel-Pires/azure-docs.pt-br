---
title: "Criar ouvinte para o grupo de disponibilidade AlwaysOn para SQL Server em máquinas virtuais do Azure"
description: "Instruções passo a passo para criar um ouvinte para um grupo de disponibilidade AlwaysOn para SQL Server em máquinas virtuais do Azure"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 07/12/2016
ms.author: MikeRayMSFT
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 0d82a2a30ec2e1fefe1efa0cb2ae0d2597998e36


---
# <a name="configure-an-internal-load-balancer-for-an-alwayson-availability-group-in-azure"></a>Configurar um balanceador de carga interno para um grupo de disponibilidade AlwaysOn no Azure
Este tópico explica como criar um balanceador de carga interno para um grupo de disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure em execução no modelo do Gerenciador de Recursos. Um grupo de disponibilidade do AlwaysOn exige um balanceador de carga quando as instâncias do SQL Server estão em máquinas virtuais do Azure. O balanceador de carga armazena o endereço IP do ouvinte do grupo de disponibilidade. Se um grupo de disponibilidade abranger várias regiões, cada região precisará de um balanceador de carga.

Para concluir essa tarefa, você precisa ter um grupo de disponibilidade AlwaysOn do SQL Server implantado em máquinas virtuais do Azure no modelo do Gerenciador de Recursos. As máquinas virtuais do SQL Server devem pertencer ao mesmo conjunto de disponibilidade. Você pode usar o [modelo da Microsoft](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para criar automaticamente o grupo de disponibilidade AlwaysOn no Azure Resource Manager. Este modelo cria automaticamente o balanceador de carga interno para você. 

Se preferir, você poderá [configurar manualmente um grupo de disponibilidade AlwaysOn](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

Este tópico exige que os grupos de disponibilidade já estejam configurados.  

Os tópicos relacionados incluem:

* [Configurar os Grupos de Disponibilidade AlwaysOn na VM do Azure (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)   
* [Configurar uma conexão de rede virtual com rede virtual usando o PowerShell e o Azure Resource Manager](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

## <a name="steps"></a>Etapas
Ao examinar este documento, você criará e configurará um balanceador de carga no Portal do Azure. Depois de concluir esta tarefa, você configurará o cluster para usar o endereço IP do balanceador de carga no ouvinte do grupo de disponibilidade AlwaysOn.

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Criar e configurar o balanceador de carga no Portal do Azure
Nesta parte da tarefa, você seguirá as seguintes etapas no Portal do Azure:

1. Criar o balanceador de carga e configurar o endereço IP
2. Configurar o pool de back-end
3. Criar a investigação 
4. Definir as regras de balanceamento de carga

> [!NOTE]
> Se os servidores SQL estiverem em regiões e grupos de recursos diferentes, você fará todas essas etapas duas vezes, uma vez em cada grupo de recursos.
> 
> 

## <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1. Criar o balanceador de carga e configurar o endereço IP
A primeira etapa é criar o balanceador de carga. No Portal do Azure, abra o grupo de recursos que contém as máquinas virtuais do SQL Server. No grupo de recursos, clique em **Adicionar**.

* Pesquise pelo **balanceador de carga**. Nos resultados da pesquisa, selecione **Balanceador de Carga**, que é publicado pela **Microsoft**.
* Na folha **Balanceador de Carga**, clique em **Criar**.
* Em **Criar balanceador de carga**, configure o balanceador de carga da seguinte maneira:

| Configuração | Valor |
| --- | --- |
| **Nome** |Um nome de texto que representa o balanceador de carga. Por exemplo, **sqlLB**. |
| **Esquema** |**Interna** |
| **Rede virtual** |Selecione a rede virtual na qual os servidores SQL estão. |
| **Sub-rede** |Selecione a sub-rede na qual os servidores SQL estão. |
| **Assinatura** |Se você tiver várias assinaturas, este campo poderá aparecer. Selecione a assinatura que você deseja que seja associada a esse recurso. Normalmente é a mesma assinatura de todos os recursos do grupo de disponibilidade. |
| **Grupo de recursos** |Selecione o grupo de recursos no qual os servidores SQL estão. |
| **Localidade** |Selecione a localização do Azure na qual os servidores SQL estão. |

* Clique em **Criar**. 

O Azure cria o balanceador de carga configurado acima. O balanceador de carga pertence a uma rede, sub-rede, grupo de recursos e local específicos. Após a conclusão do Azure, verifique as configurações do balanceador de carga no Azure. 

Agora, configure o endereço IP do balanceador de carga.  

* Na folha **Configurações** do balanceador de carga, clique em **Endereço IP**. A folha **Endereço IP** mostra que se trata de um balanceador de carga particular na mesma rede virtual que seus Servidores SQL. 
* Defina as configurações a seguir: 

| Configuração | Valor |
| --- | --- |
| **Sub-rede** |Selecione a sub-rede na qual os servidores SQL estão. |
| **Atribuição** |**Estático** |
| **Endereço IP** |Digite um endereço IP virtual não utilizado da sub-rede. |

* Salve as configurações.

Agora o balanceador de carga tem um endereço IP. Registre esse endereço IP. Você usará esse endereço IP quando criar um ouvinte no cluster. Em um script do PowerShell posterior neste artigo, use esse endereço para a variável `$ILBIP` .

## <a name="2-configure-the-backend-pool"></a>2. Configurar o pool de back-end
A próxima etapa é criar um pool de endereços de back-end. O Azure chama o pool de endereços de back-end de *pool de back-end*. Nesse caso, o pool de back-end consiste dos endereços dos dois servidores SQL em seu grupo de disponibilidade. 

* No grupo de recursos, clique no balanceador de carga que você criou. 
* Em **Configurações**, clique em **Pools de back-end**.
* Em **Pools de endereços de back-end**, clique em **Adicionar** para criar um pool de endereços de back-end. 
* Em **Adicionar pool de back-end** under **Nome**, digite um nome para o pool de back-end.
* Em **Máquinas virtuais**, clique em **+ Adicionar uma máquina virtual**. 
* Em **Escolher máquinas virtuais**, clique em **Escolher um conjunto de disponibilidade** e especifique o conjunto de disponibilidade ao qual as máquinas virtuais do SQL Server pertencem.
* Depois de escolher o conjunto de disponibilidade, clique em **Escolher as máquinas virtuais**. Clique em duas máquinas virtuais que hospedam as instâncias do SQL Server no grupo de disponibilidade. Clique em **Selecionar**. 
* Clique em **OK** para fechar as folhas para **Escolher as máquinas virtuais** e, então, em **Adicionar pool de back-end**. 

O Azure atualiza as configurações para o pool de endereços de back-end. Agora seu conjunto de disponibilidade tem um pool de dois servidores SQL.

## <a name="3-create-a-probe"></a>3. Criar uma investigação
A próxima etapa é criar uma investigação. A investigação define como o Azure verificará quais dos servidores SQL atualmente têm o ouvinte do grupo de disponibilidade. O Azure investigará o serviço com base no endereço IP em uma porta que você definirá quando criar o teste.

* Na folha **Configurações** do balanceador de carga, clique em **Investigações**. 
* Na folha **Investigações**, clique em **Adicionar**.
* Configure a investigação na folha **Adicionar investigação** . Use os valores a seguir para configurar a investigação:

| Configuração | Valor |
| --- | --- |
| **Nome** |Um nome de texto que representa a investigação. Por exemplo, **SQLAlwaysOnEndPointProbe**. |
| **Protocolo** |**TCP** |
| **Porta** |Você pode usar qualquer porta disponível. Por exemplo, *59999*. |
| **Intervalo** |*5* |
| **Limite não íntegro** |*2* |

* Clique em **OK**. 

> [!NOTE]
> Verifique se a porta especificada está aberta no firewall dos servidores SQL. Ambos os servidores exigem uma regra de entrada para a porta TCP que você usa. Confira [Adicionar ou editar regra de firewall](http://technet.microsoft.com/library/cc753558.aspx) para obter mais informações. 
> 
> 

O Azure cria a investigação. O Azure usará a investigação para testar qual SQL Server tem o ouvinte do grupo de disponibilidade.

## <a name="4-set-the-load-balancing-rules"></a>4. Definir as regras de balanceamento de carga
Definir as regras de balanceamento de carga. As regras de balanceamento de carga configuram como o balanceador de carga encaminha o tráfego para os servidores SQL. Para este balanceador de carga, você habilitará o retorno de servidor direto porque somente um dos dois servidores SQL sempre terá o recurso de ouvinte do grupo de disponibilidade de uma vez.

* Na folha **Configurações** do balanceador de carga, clique em **Regras de balanceamento de carga**. 
* Na folha **Regras de balanceamento de carga**, clique em **Adicionar**.
* Use a folha **Adicionar regras de balanceamento de carga** para configurar a regra de balanceamento de carga. Use as configurações a seguir: 

| Configuração | Valor |
| --- | --- |
| **Nome** |Um nome de texto que representa as regras de balanceamento de carga. Por exemplo, **SQLAlwaysOnEndPointListener**. |
| **Protocolo** |**TCP** |
| **Porta** |*1433* |
| **Porta de back-end** |*1433*. Observe que ela será desabilitada porque esta regra usa um **IP flutuante (retorno de servidor direto)**. |
| **Investigação** |Use o nome da investigação que você criou para este balanceador de carga. |
| **Persistência de sessão** |**Nenhum** |
| **Tempo limite de ociosidade (minutos)** |*4* |
| **IP flutuante (retorno de servidor direto)** |**Habilitado** |

> [!NOTE]
> Talvez você precise rolar para baixo na folha para ver todas as configurações.
> 
> 

* Clique em **OK**. 
* O Azure configura a regra de balanceamento de carga. Agora o balanceador de carga está configurado para rotear o tráfego para o SQL Server que hospeda o ouvinte para o grupo de disponibilidade. 

Neste ponto, o grupo de recursos tem um balanceador de carga que se conecta em ambas as máquinas do SQL Server. O balanceador de carga também contém um endereço IP para o ouvinte do grupo de disponibilidade AlwaysOn do SQL Server para que o computador possa responder às solicitações para os grupos de disponibilidade.

> [!NOTE]
> Se seus servidores SQL estiverem em duas regiões separadas, repita as etapas na outra região. Cada região exige um balanceador de carga. 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>Configure o cluster para usar o endereço IP do balanceador de carga
A próxima etapa é configurar o ouvinte no cluster e colocar o ouvinte online. Para realizar esta tarefa, faça o seguinte: 

1. Crie o ouvinte do grupo de disponibilidade no cluster de failover 
2. Coloque o ouvinte online

## <a name="1-create-the-availablity-group-listener-on-the-failover-cluster"></a>1. Crie o ouvinte do grupo de disponibilidade no cluster de failover
Nesta etapa, você cria manualmente o ouvinte do grupo de disponibilidade no Gerenciador de Cluster de Failover e no Server Management Studio (SSMS).

* Use o RDP para se conectar à máquina virtual do Azure que hospeda a réplica primária. 
* Abra o Gerenciador de Cluster de Failover.
* Selecione o nó **Redes** e observe o nome da rede do cluster. Esse nome será usado na variável `$ClusterNetworkName` no script do PowerShell.
* Expanda o nome do cluster e, em seguida, clique em **Funções**.
* No painel **Funções**, clique com o botão direito do mouse no nome do grupo de disponibilidade e, em seguida, selecione**Adicionar recurso** > **Ponto de acesso para o cliente**.
* Na caixa **Nome**, crie um nome para este novo ouvinte, clique em **Avançar** duas vezes e, em seguida, clique em **Concluir**. Não coloque o ouvinte ou o recurso online neste momento.
  
  > [!NOTE]
  > O nome para o novo ouvinte é o nome da rede que o aplicativo usará para se conectar aos bancos de dados no grupo de disponibilidade do SQL Server.
  > 
  > 
* Clique a guia **recursos** , em seguida, expanda o ponto de acesso do cliente que você acabou de criar. Clique com o botão direito do mouse no recurso de IP e clique em Propriedades. Observe o nome do endereço IP. Você usará esse nome na variável `$IPResourceName` no script do PowerShell.
* Em **IP Address** click **Endereço IP estático** and set the static Endereço IP to the same address that you used when you set the load balancer Endereço IP on the Azure portal. Habilite o NetBIOS para este endereço e clique em **OK**. Repita essa etapa para cada recurso IP se sua solução abrange diversas VNets do Azure. 
* No nó de cluster que hospeda a réplica primária, abra um ISE do PowerShell ISE elevado e cole os seguintes comandos em um novo script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Atualize as variáveis e execute o script do PowerShell para configurar o endereço IP e a porta para o novo ouvinte.
  
  > [!NOTE]
  > Se seus servidores SQL estiverem em regiões separadas, você precisará executar o script do PowerShell duas vezes. Na primeira vez, use o nome de rede de cluster, o nome do recurso IP do cluster e o endereço IP do balanceador de carga do primeiro grupo de recursos. Na segunda vez, use o nome de rede de cluster, o nome do recurso IP do cluster e o endereço IP do balanceador de carga do segundo grupo de recursos.
  > 
  > 

Agora o cluster tem um recurso de ouvinte do grupo de disponibilidade.

## <a name="2-bring-the-listener-online"></a>2. Coloque o ouvinte online
Com o recurso de ouvinte do grupo de disponibilidade configurado, você pode colocar o ouvinte online para que os aplicativos possam se conectar aos bancos de dados no grupo de disponibilidade com o ouvinte.

* Navegue de volta ao Gerenciador de Cluster de Failover. Expanda **funções** e realce seu grupo de disponibilidade. Na guia **Recursos** , clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.
* Selecione a guia **Dependências** . Se houver vários recursos listados, verifique se os endereços IP têm dependências OR, e não AND. Clique em **OK**.
* Clique com o botão direito do mouse no nome do ouvinte e clique em **Trazer online**.
* Quando o ouvinte estiver online, na guia **Recursos**, clique com o botão direito do mouse no grupo de disponibilidade e clique em **propriedades**.
* Crie uma dependência no recurso de nome de ouvinte (não o nome de recursos de endereço IP). Clique em **OK**.
* Inicie o SQL Server Management Studio e conecte-se à réplica principal.
* Navegue até **Alta Disponibilidade do AlwaysOn** | **Grupos de Disponibilidade** | **Ouvintes do Grupo de Disponibilidade**. 
* Agora você deve ver o nome do ouvinte que você criou no Gerenciador de Cluster de Failover. Clique com o botão direito do mouse no nome do ouvinte e clique em **Propriedades**.
* Na caixa **Porta**, especifique o número da porta para o ouvinte do grupo de disponibilidade usando o $EndpointPort usado anteriormente (1433 era o padrão) e depois clique em **OK**.

Agora você tem um grupo de disponibilidade AlwaysOn do SQL Server em máquinas virtuais do Azure, em execução no modo de Gerenciador de Recursos. 

## <a name="test-the-connection-to-the-listener"></a>Testar a conexão com o ouvinte
Para testar a conexão:

1. RDP para um SQL Server que está na mesma rede virtual, mas não tem a réplica. Isso pode ser outro SQL Server no cluster.
2. Use o utilitário **sqlcmd** para testar a conexão. Por exemplo, o script a seguir estabelece uma conexão de **sqlcmd** com a réplica primária por meio do ouvinte com autenticação do Windows:
   
        sqlcmd -S <listenerName> -E

A conexão SQLCMD automaticamente se conecta a qualquer instância do SQL Server que hospeda a réplica primária. 

## <a name="guidelines-and-limitations"></a>Diretrizes e limitações
Observe as diretrizes a seguir no ouvinte do grupo de disponibilidade no Azure usando o balanceador de carga interno:

* Há suporte somente para um ouvinte de grupo de disponibilidade por serviço de nuvem porque o ouvinte é configurado ao balanceador de carga e há apenas um balanceador de carga interno. No entanto, é possível criar vários ouvintes externos. 
* Com um balanceador de carga interno, você só pode acessar ao ouvinte de dentro da mesma rede virtual.




<!--HONumber=Nov16_HO3-->


