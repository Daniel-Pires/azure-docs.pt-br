---
title: "Configurar os grupos de disponibilidade Always On na VM do Azure - Clássico"
description: "Crie um grupo de disponibilidade AlwaysOn em máquinas virtuais do Azure. Este tutorial usa principalmente a interface do usuário e ferramentas em vez de scripts."
services: virtual-machines-windows
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: 
tags: azure-service-management
ms.assetid: 8d48a3d2-79f8-4ab0-9327-2f30ee0b2ff1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: infrastructure-services
ms.date: 09/22/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: f6be1d51f570467e0a7cdc26892a648d7f6ea225


---
# <a name="configure-always-on-availability-group-in-azure-vm---classic"></a>Configurar os grupos de disponibilidade Always On na VM do Azure - Clássico
> [!div class="op_single_selector"]
> * [Resource Manager: Modelo](virtual-machines-windows-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Resource Manager: Manual](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
> * [Clássico: Interface de usuário](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> * [Clássico: PowerShell](virtual-machines-windows-classic-ps-sql-alwayson-availability-groups.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
> 
> 

<br/>

> [!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]
> 
> 

Este tutorial completo mostra como implementar os grupos de disponibilidade usando o SQL Server AlwaysOn em execução em máquinas virtuais do Azure.

Ao final do tutorial, sua solução SQL Server AlwaysOn no Azure consistirá nos seguintes elementos:

* Uma rede virtual que contém várias sub-redes, incluindo uma de front-end e uma de back-end
* Um controlador de domínio com um domínio do AD (Active Directory)
* Duas VMs do SQL Server implantadas na sub-rede de back-end e ingressadas no domínio do AD
* Um cluster WSFC de três nós com o modelo de quórum de Nó Principal
* Um grupo de disponibilidade com duas réplicas de confirmação síncrona de um banco de dados de disponibilidade

A figura abaixo é uma representação gráfica da solução.

![Arquitetura de laboratório de teste para o AG no Azure](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC791912.png)

Observe que se trata de uma das configurações possíveis. Por exemplo, você pode minimizar o número de VMs para um grupo de disponibilidade de duas réplicas para economizar horas de computação no Azure usando o controlador de domínio como a testemunha de compartilhamento de arquivo de quórum em um cluster do WSFC de dois nós. Esse método reduz a contagem de VMs em uma em comparação com a configuração acima.

Este tutorial pressupõe o seguinte:

* Você já tem uma conta do Azure.
* Você já sabe como provisionar uma VM clássica do SQL Server por meio da galeria da máquina virtual usando a GUI.
* Você já tem uma compreensão sólida dos grupos de disponibilidade AlwaysOn. Para obter mais informações, confira [Grupos de disponibilidade Always On (SQL Server)](https://msdn.microsoft.com/library/hh510230.aspx).

> [!NOTE]
> Se você estiver interessado em usar os grupos de disponibilidade Always On com o SharePoint, consulte também [Configure SQL Server 2012 Always On Availability Groups for SharePoint 2013](https://technet.microsoft.com/library/jj715261.aspx)(Configurar grupos de disponibilidade AlwaysOn do SQL Server 2012 para o SharePoint 2013).
> 
> 

## <a name="create-the-virtual-network-and-domain-controller-server"></a>Criar a Rede Virtual e o Servidor de Controlador de Domínio
Comece com uma nova conta de avaliação do Azure. Depois de concluir a configuração da conta, você deverá estar na tela inicial do portal clássico do Azure.

1. Clique no botão **Novo** no canto inferior esquerdo da página, conforme mostrado abaixo.
   
    ![Clique em Novo no portal](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665511.gif)
2. Clique em **Serviços de Rede**, em **Rede Virtual** e em **Criação Personalizada**, conforme mostrado abaixo.
   
    ![Criar rede virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665512.gif)
3. Na caixa de diálogo **CRIAR UMA REDE VIRTUAL** , crie uma nova rede virtual percorrendo as páginas com as configurações a seguir. 
   
   | Página | Configurações |
   | --- | --- |
   | Detalhes de rede virtual |**NAME = ContosoNET**<br/>**REGION = Oeste dos EUA** |
   | Conectividade de VPN e servidores DNS |Nenhum |
   | Espaços de Endereço da Rede Virtual |As configurações são mostradas na captura de tela abaixo:  ![Criar Rede Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784620.png) |
4. Em seguida, crie a VM que será usada como o DC (Controlador de Domínio). Clique novamente em **Novo**, depois em **Computação**, em **Máquina Virtual** e em **Da Galeria**, conforme mostrado abaixo.
   
    ![Criar uma máquina virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784621.png)
5. Na caixa de diálogo **CRIAR UMA MÁQUINA VIRTUAL** , configure uma nova VM seguindo as páginas com as configurações abaixo. 
   
   | Página | Configurações |
   | --- | --- |
   | Selecionar o sistema operacional da máquina virtual |Windows Server 2012 R2 Datacenter |
   | Configuração de máquina virtual |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoDC<br/>**TIER** = STANDARD<br/>**TAMANHO** = A2 (2 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |
   | Configuração de máquina virtual |**CLOUD SERVICE** = Criar um novo serviço de nuvem<br/>**CLOUD SERVICE DNS NAME** = um nome de serviço de nuvem exclusivo<br/>**DNS NAME** = um nome exclusivo (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = (Nenhum) |
   | Opções de máquina virtual |Usar padrões |

Ao terminar de configurar a nova VM, aguarde até ela ser provisionada. Esse processo leva algum tempo para ser concluído e se você clicar na guia **Máquina Virtual** no portal clássico do Azure, poderá ver os estados de ciclo de ContosoDC desde **Iniciando (Provisionamento)** até **Parado**, **Iniciando**, **Em execução (Provisionamento)** e, finalmente, **Em execução**.

O servidor de DC agora foi provisionado com êxito. Em seguida, você configurará o domínio do Active Directory nesse servidor de DC.

## <a name="configure-the-domain-controller"></a>Configurar o Controlador de Domínio
Nas etapas a seguir, você poderá configurar o computador ContosoDC como um controlador de domínio para corp.contoso.com.

1. No portal, selecione o computador **ContosoDC** . Na guia **Painel**, clique em **Conectar** para abrir um arquivo RDP para o acesso à área de trabalho remota.
   
    ![Conectar à Máquina Virtual](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784622.png)
2. Faça logon com sua conta de administrador configurada (**\AzureAdmin**) e a senha (**Contoso!000**).
3. Por padrão, o painel **Gerenciador do Servidor** deve ser exibido.
4. Clique no link **Adicionar funções e recursos** no painel.
   
    ![Adicionar Funções ao Gerenciador de Servidores](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784623.png)
5. Selecione **Avançar** até alcançar a seção **Funções de Servidor**.
6. Selecione **Active Directory Domain Services** e as funções do **Servidor DNS**. Quando solicitado, acrescente os recursos adicionais necessários para essas funções.
   
   > [!NOTE]
   > Você receberá um aviso de validação de que não há nenhum endereço IP estático. Se você estiver testando a configuração, clique em continuar. Para cenários de produção, [use o PowerShell para definir o endereço IP estático do computador do controlador de domínio](../virtual-network/virtual-networks-reserved-private-ip.md).
   > 
   > 
   
    ![Adicionar Caixa de Diálogo de Funções](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784624.png)
7. Clique em **Avançar** até alcançar a seção **Confirmação**. Marque a caixa de seleção **Reiniciar cada servidor de destino automaticamente, se necessário** .
8. Clique em **Instalar**.
9. Depois que os recursos forem instalados, retorne para o painel **Gerenciador do Servidor** .
10. Selecione a nova opção **AD DS** no painel esquerdo.
11. Clique no link **Mais** na barra de aviso amarela.
    
     ![Caixa de diálogo do AD DS na VM do servidor DNS](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784625.png)
12. Na coluna **Ação** da caixa de diálogo **Todos os Detalhes de Tarefa do Servidor**, clique em **Promover este servidor a um controlador de domínio**.
13. No **Assistente de Configuração dos Serviços de Domínio do Active Directory**, use os seguintes valores:
    
    | Página | Configuração |
    | --- | --- |
    | Configuração de Implantação |**Adicionar uma nova floresta** = selecionado<br/>**Nome do domínio raiz** = corp.contoso.com |
    | Opções de Controlador de Domínio |**Senha** = Contoso!000<br/>**Confirmar Senha** = Contoso!000 |
14. Clique em **Avançar** para percorrer as outras páginas do assistente. Na página **Verificação de pré-requisitos**, verifique se a seguinte mensagem é exibida: **Todas as verificações de pré-requisitos passaram com êxito**. Observe que você deve examinar todas as mensagens de aviso aplicáveis, porém é possível continuar com a instalação.
15. Clique em **Instalar**. A máquina virtual **ContosoDC** será reinicializada automaticamente.

## <a name="configure-domain-accounts"></a>Configurar Contas de Domínio
As próximas etapas configuram as contas do AD (Active Directory) para uso posterior.

1. Faça logon novamente no computador **ContosoDC** .
2. Em **Gerenciador do Servidor**, selecione **Ferramentas** e, em seguida, clique em **Centro Administrativo do Active Directory**.
   
    ![Centro Administrativo do Active Directory](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784626.png)
3. Na folha **Centro Administrativo do Active Directory** select **corp (local)** no painel esquerdo.
4. No painel **Tarefas** à direita, selecione **Novo** e clique em **Usuário**. Use as configurações a seguir:
   
   | Configuração | Valor |
   | --- | --- |
   | **Nome** |Instalar |
   | **SamAccountName do usuário** |Instalar |
   | **Senha** |Contoso!000 |
   | **Confirmar senha** |Contoso!000 |
   | **Outras opções de senha** |Selecionado |
   | **A senha nunca expira** |Verificado |
5. Clique em **OK** para criar o usuário de **Instalação**. Essa conta será usada para configurar o cluster de failover e o grupo de disponibilidade.
6. Crie dois usuários adicionais com as mesmas etapas: **CORP\SQLSvc1** e **CORP\SQLSvc2**. Essas contas serão usadas para instâncias do SQL Server. Em seguida, você precisa conceder a **CORP\Install** as permissões necessárias para configurar o WSFC (Windows Service Failover Clustering).
7. No **Centro Administrativo do Active Directory**, selecione **corp (local)** no painel esquerdo. Em seguida, no painel **Tarefas** à direita, clique em **Propriedades**.
   
    ![Propriedades do usuário CORP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784627.png)
8. Selecione **Extensões** e clique no botão **Avançado** na guia **Segurança**.
9. Na caixa de diálogo **Configurações de Segurança Avançadas para corp** . Clique em **Adicionar**.
10. Clique em **Selecione uma entidade**. Em seguida, procure **CORP\Install**. Clique em **OK**.
11. Selecione as permissões **Ler todas as propriedades** e **Criar objetos de computador**.
    
     ![Permissões de usuário corp](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784628.png)
12. Clique em **OK** e em **OK** novamente. Feche a janela de propriedades corporativas.

Agora que você concluiu a configuração do Active Directory e dos objetos de usuário, você criará três VMs do SQL Server e vai associá-las a esse domínio.

## <a name="create-the-sql-server-vms"></a>Criar VMs do SQL Server
Em seguida, crie três VMs, incluindo um nó de cluster WSFC e duas VMs do SQL Server. Para criar cada uma das VMs, volte ao portal clássico do Azure, clique em **Novo**, **Computação**, **Máquina Virtual** e **Da Galeria**. Em seguida, use os modelos na tabela a seguir para ajudá-lo a criar as VMs.

| Página | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| Selecionar o sistema operacional da máquina virtual |**Windows Server 2012 R2 Datacenter** |**SQL Server 2014 RTM Enterprise** |**SQL Server 2014 RTM Enterprise** |
| Configuração de máquina virtual |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoWSFCNode<br/>**TIER** = STANDARD<br/>**TAMANHO** = A2 (2 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL1<br/>**TIER** = STANDARD<br/>**TAMANHO** = A3 (4 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |**VERSION RELEASE DATE** = (latest)<br/>**VIRTUAL MACHINE NAME** = ContosoSQL2<br/>**TIER** = STANDARD<br/>**TAMANHO** = A3 (4 núcleos)<br/>**NEW USER NAME** = AzureAdmin<br/>**NEW PASSWORD** = Contoso!000<br/>**CONFIRM** = Contoso!000 |
| Configuração de máquina virtual |**CLOUD SERVICE** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = Criar um conjunto de disponibilidade<br/>**AVAILABILITY SET NAME** = SQLHADR |**CLOUD SERVICE** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = SQLHADR (Você também pode configurar o conjunto de disponibilidade após a criação da máquina. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.) |**CLOUD SERVICE** = nome DNS do serviço de nuvem exclusivo criado anteriormente (ex: ContosoDC123)<br/>**REGION/AFFINITY GROUP/VIRTUAL NETWORK** = ContosoNET<br/>**VIRTUAL NETWORK SUBNETS** = Back(10.10.2.0/24)<br/>**CONTA DE ARMAZENAMENTO** = Use uma conta de armazenamento gerada automaticamente<br/>**AVAILABILITY SET** = SQLHADR (Você também pode configurar o conjunto de disponibilidade após a criação da máquina. Todas as três máquinas devem ser atribuídas ao conjunto de disponibilidade SQLHADR.) |
| Opções de máquina virtual |Usar padrões |Usar padrões |Usar padrões |

<br/>

> [!NOTE]
> A configuração anterior sugere máquinas virtuais de camada STANDARD, porque máquinas de camada BASIC não dão suporte a pontos de extremidade com balanceamento de carga necessários para criar posteriormente os ouvintes do grupo de disponibilidade. Além disso, os tamanhos de máquina sugeridos aqui servem para testar grupos de disponibilidade em VMs do Azure. Para obter o melhor desempenho em cargas de trabalho de produção, consulte as recomendações de configuração e tamanhos de máquina do SQL Server em [Práticas recomendadas relacionadas ao desempenho para o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
> 
> 

Depois que as três VMs forem totalmente provisionadas, você precisará ingressá-las no domínio **corp.contoso.com** e conceder direitos administrativos CORP\Install às máquinas. Para fazer isso, use as seguintes etapas para cada uma das três VMs.

1. Primeiro, altere o endereço do servidor DNS preferencial. Comece baixando o arquivo RDP (Área de Trabalho Remota) de cada VM em seu diretório local selecionando-a na lista e clicando no botão **Conectar** . Para selecionar uma VM, clique em qualquer lugar exceto na primeira célula na linha, conforme mostrado abaixo.
   
    ![Baixe o arquivo RDP](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664953.jpg)
2. Inicie o arquivo RDP baixado e faça logon na VM usando sua conta de administrador configurada (**BUILTIN\AzureAdmin**) e a senha (**Contoso!000**).
3. Quando estiver conectado, você deverá ver o painel **Gerenciador do Servidor** . No painel esquerdo, clique em **Servidor Local** .
4. Selecione o link **Endereço IPv4 atribuído pelo DHCP, habilitado para IPv6** .
5. Na janela **Conexões de Rede** , selecione o ícone de rede.
   
    ![Alterar o servidor DNS preferencial da VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784629.png)
6. Na barra de comandos, clique em **Alterar as configurações desta conexão** (dependendo do tamanho da janela, talvez você precise clicar na seta dupla à direita para ver esse comando).
7. Clique em **Protocolo IP versão 4 (TCP/IPv4)** e clique em Propriedades.
8. Selecione Usar os seguintes endereços de servidor DNS e especifique **10.10.2.4** em **Servidor DNS preferencial**.
9. O endereço **10.10.2.4** é o endereço atribuído a uma VM na sub-rede 10.10.2.0/24 em uma rede virtual do Azure e essa VM é **ContosoDC**. Para verificar o endereço IP de **ContosoDC**, use o **nslookup contosodc** no prompt de comando, conforme mostrado abaixo.
   
    ![Use NSLOOKUP para localizar o endereço IP para DC](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC664954.jpg)
10. Clique em O**K** e em **Fechar** para confirmar as alterações. Agora é possível ingressar a VM em **corp.contoso.com**.
11. Volte para a janela **Servidor Local** e clique no link **GRUPO DE TRABALHO**.
12. Na seção **Nome do Computador**, clique em **Alterar**.
13. Marque a caixa de seleção **Domínio** e digite **corp.contoso.com** na caixa de texto. Clique em **OK**.
14. Na caixa de diálogo pop-up **Segurança do Windows**, especifique as credenciais para a conta de administrador de domínio padrão (**CORP\AzureAdmin**) e a senha (**Contoso!000**).
15. Quando a mensagem "Bem-vindo ao domínio corp.contoso.com" for exibida, clique em **OK**.
16. Clique em **Fechar** e em **Reiniciar Agora** na caixa de diálogo pop-up.

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-vm"></a>Adicione o usuário Corp\Install como um administrador em cada VM:
1. Aguarde até que a VM seja reiniciada e inicie o arquivo RDP para fazer logon na VM usando a conta **BUILTIN\AzureAdmin**.
2. Em **Gerenciador do Servidor**, selecione **Ferramentas** e clique em **Gerenciamento do Computador**.
   
    ![Gerenciamento do Computador](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784630.png)
3. Na janela **Gerenciamento do Computador**, expanda **Usuários e Grupos Locais** e selecione **Grupos**.
4. Clique duas vezes no grupo **Administradores** .
5. Na caixa de diálogo **Propriedades de Administradores**, clique no botão **Adicionar**.
6. Insira o usuário **CORP\Install** e clique em **OK**. Quando solicitado a fornecer credenciais, use a conta **AzureAdmin** com a senha **Contoso!000**.
7. Clique em **OK** para fechar a caixa do diálogo **Propriedades do Administrador**.

### <a name="add-the-failover-clustering-feature-to-each-vm"></a>Adicione o recurso de **Clustering de Failover** a cada VM.
1. No painel **Gerenciador do Servidor**, clique em **Adicionar funções e recursos**.
2. No **Assistente para Adicionar Funções e Recursos**, clique em **Avançar** até chegar à página **Recursos**.
3. Selecione **Clustering de Failover**. Quando solicitado, adicione todos os outros recursos dependentes.
   
    ![Adicionar o Recurso de Cluster de Failover à VM](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784631.png)
4. Clique em **Avançar** e em **Instalar** na página **Confirmação**.
5. Quando a instalação do recurso **Clustering de Failover** for concluída, clique em **Fechar**.
6. Faça logoff da VM.
7. Repita as etapas nesta seção para todos os três servidores – **ContosoWSFCNode**, **ContosoSQL1** e **ContosoSQL2**.

As VMs do SQL Server agora estão provisionadas e em execução, mas estão instaladas com o SQL Server com as opções padrão.

## <a name="create-the-wsfc-cluster"></a>Criar o Cluster WSFC
Nesta seção, você deve criar o cluster WSFC que hospedará o grupo de disponibilidade que você criará posteriormente. Agora, você deve ter feito o seguinte para cada uma das três VMs que serão usadas no cluster WSFC:

* Totalmente provisionado no Azure
* Ingressou a VM no domínio
* **CORP\Install** adicionado ao grupo de administradores local
* Recurso de Clustering de Failover adicionado

Todos são pré-requisitos em cada VM para poder ingressá-la no cluster WSFC.

Além disso, observe que a rede virtual do Azure não se comporta da mesma forma que uma rede local. Você precisa criar o cluster na seguinte ordem:

1. Crie um cluster de nó único em um dos nós (**ContosoSQL1**).
2. Modifique o endereço IP do cluster para um endereço IP não usado (**10.10.2.101**).
3. Deixe o nome do cluster online.
4. Adicione outros nós (**ContosoSQL2** e **ContosoWSFCNode**).

Siga as etapas abaixo para executar essas tarefas que definem totalmente o cluster.

1. Inicie o arquivo RDP para **ContosoSQL1** e faça logon usando a conta de domínio **CORP\Install**.
2. No painel **Gerenciador do Servidor** selecione **Ferramentas**, e clique em **Gerenciador de Cluster de Failover**.
3. No painel esquerdo, clique com o botão direito do mouse em **Gerenciador de Cluster de Failover** e clique em **Criar um Cluster**, conforme mostrado abaixo.
   
    ![Criar Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784632.png)
4. No Assistente para Criação de Cluster, crie um cluster de um nó percorrendo as páginas com as configurações a seguir:
   
   | Página | Configurações |
   | --- | --- |
   | Antes de começar |Usar padrões |
   | Selecionar Servidores |Digite **ContosoSQL1** em **Inserir nome do servidor** e clique em **Adicionar** |
   | Aviso de Validação |Selecione **Não. Eu não preciso de suporte da Microsoft para este cluster e, portanto, não desejo executar testes de validação. Continuar a criar o cluster quando eu clicar em Avançar**. |
   | Ponto de Acesso para Administrar o Cluster |Digite **Cluster1** em **Nome do Cluster** |
   | Confirmação |Use os padrões, a menos que você esteja usando Espaços de Armazenamento. Consulte a observação após esta tabela. |
   
   > [!WARNING]
   > Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster** na página **Confirmação**. Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.
   > 
   > 
5. No painel esquerdo, expanda o **Gerenciador de Cluster de Failover** e clique em **Cluster1.corp.contoso.com**.
6. No painel central, role para baixo até a seção **Recursos Principais do Cluster** e expanda os detalhes **Nome: Clutser1**. Você verá os recursos de **Nome** e de **Endereço IP** no estado **Com Falha**. O recurso de endereço IP não pode ficar online porque o cluster recebeu o mesmo endereço IP que a própria máquina, que é um endereço duplicado.
7. Clique com o botão direito do mouse no recurso **Endereço IP** com falha e clique em **Propriedades**.
   
    ![Propriedades do Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784633.png)
8. Selecione **Endereço IP Estático** e especifique **10.10.2.101** na caixa de texto Endereço. Em seguida, clique em **OK**.
9. Na seção **Recursos Principais do Cluster**, clique com o botão direito do mouse em **Nome: Cluster1** e clique em **Colocar Online**. Em seguida, aguarde até que ambos os recursos estejam online. Quando o recurso de nome de cluster fica online, ele atualiza o servidor DC com uma nova conta de computador do AD. Essa conta AD será usada para executar o serviço clusterizado do grupo de disponibilidade posteriormente.
10. Por fim, você deve adicionar os nós restantes ao cluster. Na árvore do navegador, clique com o botão direito do mouse em **Cluster1.corp.contoso.com** e clique em **Adicionar Nó**, conforme mostrado abaixo.
    
     ![Adicionar Nó ao Cluster](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC784634.png)
11. No **Assistente para Adicionar Nó**, clique em **Avançar**. Na página **Selecionar Servidores**, adicione **ContosoSQL2** e **ContosoWSFCNode** à lista digitando o nome do servidor em **Insira o nome do servidor** e clicando em **Adicionar**. Quando terminar, clique em **Avançar**.
12. Na página **Aviso de Validação**, clique em **Não** (em um cenário de produção, você deve executar os testes de validação). Em seguida, clique em **Avançar**.
13. Na página **Confirmação**, clique em **Avançar** para adicionar os nós.
    
    > [!WARNING]
    > Se você estiver usando [Espaços de Armazenamento](https://technet.microsoft.com/library/hh831739), que agrupam vários discos em pools de armazenamento, desmarque a caixa de seleção **Adicione todo o armazenamento qualificado ao cluster** . Se você não desmarcar essa opção, os discos virtuais serão desanexados durante o processo de clustering. Como resultado, eles também não aparecerão no Gerenciador ou Explorador de Discos até que os espaços de armazenamento sejam removidos do cluster e reanexados usando o PowerShell.
    > 
    > 
14. Depois que os nós forem adicionados ao cluster, clique em **Concluir**. O Gerenciador de Cluster de Failover agora deve mostrar que o cluster tem três nós e listá-los no contêiner **Nós** .
15. Faça logoff da sessão de área de trabalho remota.

## <a name="prepare-the-sql-server-instances-for-availability-group"></a>Preparar instâncias do SQL Server para o Grupo de Disponibilidade
Nesta seção, você fará o seguinte em ambos os **ContosoSQL1** e **contosoSQL2**:

* Adicionar um logon para **NT AUTHORITY\System** com um conjunto de permissões necessárias para a instância padrão do SQL Server
* Adicionar **CORP\Install** como uma função de sysadmin à instância padrão do SQL Server
* Abrir o firewall para acesso remoto do SQL Server
* Habilitar a funcionalidade de grupos de disponibilidade AlwaysOn
* Alterar a conta de serviço do SQL Server para **CORP\SQLSvc1** e **CORP\SQLSvc2**, respectivamente

Essas ações podem ser executadas em qualquer ordem. No entanto, as etapas a seguir as percorrerá em ordem. Siga as etapas para ambos **ContosoSQL1** e **ContosoSQL2**:

1. Se você não tiver feito logoff da sessão de área de trabalho remota para a VM, faça isso agora.
2. Inicie os arquivos RDP para **ContosoSQL1** e **ContosoSQL2** e faça logon como **BUILTIN\AzureAdmin**.
3. Primeiro, adicione **NT AUTHORITY\System** aos logons do SQL Server e com as permissões necessárias. Inicie o **SQL Server Management Studio**.
4. Clique em **Conectar** para se conectar à instância padrão do SQL Server.
5. No **Pesquisador de Objetos**, expanda **Segurança** e depois **Logons**.
6. Clique com o botão direito do mouse no logon **NT AUTHORITY\System** e clique em **Propriedades**.
7. Na página **Protegíveis**, para o servidor local, selecione **Conceder** para as seguintes permissões e clique em **OK**.
   
   * Alterar qualquer grupo de disponibilidade
   * Conectar o SQL
   * Exibir o estado do servidor
8. Em seguida, adicione **CORP\Install** como uma função de **sysadmin** à instância padrão do SQL Server. Em **Pesquisador de Objetos**, clique com o botão direito do mouse em **Logons** e clique em **Novo Logon**.
9. Digite **CORP\Install** em **Nome de logon**.
10. Na página **Funções de Servidor**, selecione **sysadmin**. Em seguida, clique em **OK**. Depois que o logon for criado, você pode vê-lo expandindo **Logons** in **Pesquisador de Objetos**.
11. Em seguida, crie uma regra de firewall para o SQL Server. Na tela **Iniciar**, inicie o **Firewall do Windows com Segurança Avançada**.
12. No painel esquerdo, selecione **Regras de Entrada**. No painel direito, clique em **Nova Regra**.
13. Na página **Tipo de Regra**, selecione **Programa**, e clique em **Avançar**.
14. Na página **Programa** selecione **Este caminho de programa** e digite **%ProgramFiles%\Microsoft SQL Server\MSSQL12.MSSQLSERVER\MSSQL\Binn\sqlservr.exe** na caixa de texto (se você estiver seguindo essas instruções, mas usando o SQL Server 2012, o diretório do SQL Server será **MSSQL11.MSSQLSERVER**). Em seguida, clique em **Próximo**.
15. Na página **Ação**, mantenha selecionado **Permitir a conexão** e clique em **Avançar**.
16. Na página **Perfil**, aceite as configurações padrão e clique em **Avançar**.
17. Na página **Nome**, especifique um nome de regra, como **SQL Server (Regra de Programa)**, na caixa de texto **Nome** e clique em **Concluir**.
18. Em seguida, habilite o recurso **Grupos de Disponibilidade AlwaysOn** . Na tela **Iniciar**, inicie o **SQL Server Configuration Manager**.
19. Na árvore do navegador, clique em **Serviços do SQL Server**, clique com o botão direito do mouse no serviço **SQL Server (MSSQLSERVER)** e clique em **Propriedades**.
20. Clique na guia **Alta Disponibilidade AlwaysOn**, selecione **Habilitar Grupos de Disponibilidade AlwaysOn** conforme mostrado abaixo e clique em **Aplicar**. Clique em **OK** na caixa de diálogo pop-up e não feche a janela Propriedades por enquanto. Você reiniciará o serviço SQL Server depois de alterar a conta de serviço.
    
     ![Habilitar grupos de disponibilidade AlwaysOn](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665520.gif)
21. Em seguida, você pode alterar a conta de serviço do SQL Server. Clique na guia **Logon** e digite **CORP\SQLSvc1** (para **ContosoSQL1**) ou **CORP\SQLSvc2** (para **ContosoSQL2**) em **Nome da Conta**, em seguida, insira e confirme a senha e clique em **OK**.
22. Na janela pop-up, clique em **Sim** para reiniciar o serviço do SQL Server. Depois que o serviço do SQL Server for reiniciado, as alterações feitas na janela de propriedades entrarão em vigor.
23. Faça logoff das VMs.

## <a name="create-the-availability-group"></a>Criar o Grupo de Disponibilidade
Agora você está pronto para configurar um grupo de disponibilidade. Abaixo está uma descrição do que você deve fazer:

* Crie um novo banco de dados (**MyDB1**) em **ContosoSQL1**
* Faça um backup completo e um backup de log de transações do banco de dados
* Restaure os backups completo e de log para **ContosoSQL2** com a opção **NORECOVERY**
* Crie o grupo de disponibilidade (**AG1**) com confirmação síncrona, failover automático e réplicas secundárias legíveis

### <a name="create-the-mydb1-database-on-contososql1"></a>Crie o banco de dados MyDB1 no ContosoSQL1:
1. Se você ainda não tiver saído das sessões de área de trabalho remota para **ContosoSQL1** e **ContosoSQL2**, faça isso agora.
2. Inicie o arquivo RDP para **ContosoSQL1** e faça logon como **CORP\Install**.
3. No **Explorador de Arquivos**, em **C:\**, crie um diretório chamado **backup**. Você usará esse uso de diretório para fazer backup e restaurar o banco de dados.
4. Clique com o botão direito do mouse no novo diretório, aponte o mouse para **Compartilhar com**, e clique em **Pessoas específicas**, como mostrado abaixo.
   
    ![Criar uma pasta de Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665521.gif)
5. Adicione **CORP\SQLSvc1** e conceda a ele a permissão de **Leitura/Gravação** e, em seguida, adicione **CORP\SQLSvc2** e conceda a ele a permissão de **Leitura**, conforme mostrado abaixo e clique **Compartilhar**. Quando o processo de compartilhamento de arquivos for concluído, clique em **Concluído**.
   
    ![Conceder permissões para a pasta de Backup](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665522.gif)
6. Em seguida, crie o banco de dados. No menu **Iniciar**, abra o **SQL Server Management Studio** e clique em **Conectar** para se conectar à instância padrão do SQL Server.
7. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Novo Banco de Dados**.
8. Em **Nome do banco de dados**, digite **MyDB1** e clique em **OK**.

### <a name="take-a-full-backup-of-mydb1-and-restore-it-on-contososql2"></a>Faça um backup completo de MyDB1 e restaure-o no ContosoSQL2:
1. Em seguida, faça um backup completo do banco de dados. No **Pesquisador de Objetos**c, expanda **Bancos de Dados** e clique com o botão direito do mouse em **MyDB1**; em seguida aponte o mouse para **Tarefas** e clique em **Fazer Backup**.
2. Na seção **Origem**, mantenha o **Tipo de backup** definido como **Completo**. Na seção **Destino**, clique em **Remover** para remover o caminho de arquivo padrão para o arquivo de backup.
3. Na seção **Destino**, clique em **Adicionar**.
4. Na caixa de texto **Nome do arquivo**, digite **\\\ContosoSQL1\backup\MyDB1.bak**. Em seguida, clique em **OK** e em **OK** novamente para fazer backup do banco de dados. Quando a operação de backup for concluída, clique em **OK** novamente para fechar a caixa de diálogo.
5. Em seguida, faça um backup do log de transações do banco de dados. No **Pesquisador de Objetos**c, expanda **Bancos de Dados** e clique com o botão direito do mouse em **MyDB1**; em seguida aponte o mouse para **Tarefas** e clique em **Fazer Backup**.
6. No tipo **Backup**, selecione **Log de Transações**. Mantenha o caminho do arquivo de **Destino** definido para aquele especificado anteriormente e clique em **OK**. Quando a operação de backup for concluída, clique em **OK** novamente.
7. Em seguida, restaure os backups completo e de log de transações em **ContosoSQL2**. Inicie o arquivo RDP para **ContosoSQL2** e faça logon como **CORP\Install**. Deixe a sessão de área de trabalho remota para **ContosoSQL1** aberta.
8. No menu **Iniciar**, abra o **SQL Server Management Studio** e clique em **Conectar** para se conectar à instância padrão do SQL Server.
9. No **Pesquisador de Objetos**, clique com o botão direito do mouse em **Bancos de Dados** e em **Restaurar Banco de Dados**.
10. Na seção **Origem**, selecione **Dispositivo** e clique no botão **…** .
11. Em **Selecionar dispositivos de backup**, clique em **Adicionar**.
12. No local do arquivo de Backup, digite \\ContosoSQL1\backup, clique em Atualizar, selecione MyDB1.bak, clique em OK e, em seguida, clique em OK novamente. Agora você deverá ver o backup completo e o backup de log no painel Conjuntos de backup a serem restaurados.
13. Vá para a página de Opções, selecione RESTORE WITH NORECOVERY em Estado de recuperação e, em seguida, clique em OK para restaurar o banco de dados. Quando a operação de restauração for concluída, clique em OK.

### <a name="create-the-availability-group"></a>Crie o Grupo de Disponibilidade:
1. Volte para a sessão de área de trabalho remota para **ContosoSQL1**. No **Pesquisador de Objetos** do SSMS, clique com o botão direito do mouse em **Alta Disponibilidade AlwaysOn** e clique em **Assistente de Novo Grupo de Disponibilidade**, conforme mostrado abaixo.
   
    ![Inicie o Assistente de Novo Grupo de Disponibilidade](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665523.gif)
2. Na página **Introdução**, clique em **Avançar**. Na página **Especificar Nome do Grupo de Disponibilidade**, digite **AG1** em **Nome do grupo de disponibilidade** e clique em **Avançar** novamente.
   
    ![Novo assistente de AG, especifique o nome do AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665524.gif)
3. Na página **Selecionar Bancos de Dados**, selecione **MyDB1** e clique em **Avançar**. Esse banco de dados atende aos pré-requisitos para um grupo de disponibilidade, pois você fez pelo menos um backup completo na réplica primária pretendida.
   
    ![Novo assistente AG, selecione os bancos de dados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665525.gif)
4. Na página **Especificar Réplicas**, clique em **Adicionar Réplica**.
   
    ![Novo assistente AG, especifique as réplicas](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665526.gif)
5. A caixa de diálogo **Conectar ao Servidor** é aberta. Digite **ContosoSQL2** em **Nome do servidor** e clique em **Conectar**.
   
    ![Novo assistente AG, conecte ao servidor](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665527.gif)
6. De volta à página **Especificar Réplicas**, você verá agora **ContosoSQL2** listado em **Réplicas Disponíveis**. Configure as réplicas conforme mostrado abaixo. Quando tiver terminado, clique em **Avançar**.
   
    ![Novo assistente de AG, Especificar Réplicas (Completo)](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665528.gif)
7. Na página **Selecionar Sincronização de Dados Inicial**, selecione **Somente ingressar** e clique em **Avançar**. Você já executou a sincronização de dados manualmente quando obteve os backups completos e de transações em **ContosoSQL1** e os restaurou em **ContosoSQL2**. Você pode preferir não realizar as operações de backup e restauração no seu banco de dados e selecionar **Completo** para permitir que o Assistente de Novo Grupo de Disponibilidade execute a sincronização de dados para você. No entanto, isso não é recomendado para grandes bancos de dados que se encontram em algumas empresas.
   
    ![Novo assistente de AG, selecionar sincronização de dados inicial](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665529.gif)
8. Na página **Validação**, clique em **Avançar**. Esta página deve ser semelhante ao que é mostrado abaixo. Há um aviso para a configuração de ouvinte porque você não configurou um ouvinte de grupo de disponibilidade. Você pode ignorar esse aviso, pois este tutorial não configura um ouvinte. Para configurar o ouvinte após a conclusão deste tutorial, veja [Configurar um ouvinte de ILB para Grupos de Disponibilidade AlwaysOn no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
   
    ![Novo assistente AG, Validação](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665530.gif)
9. Na página **Resumo**, clique em **Concluir** e aguarde enquanto o assistente configura o novo grupo de disponibilidade. Na página **Progresso**, você pode clicar em **Mais detalhes** para exibir o progresso detalhado. Quando o assistente for concluído, inspecione a página **Resultados** para verificar se o grupo de disponibilidade foi criado com êxito, conforme mostrado abaixo, e clique em **Fechar** para sair do assistente.
   
    ![Novo assistente de AG, Resultados](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665531.gif)
10. No **Pesquisador de Objetos**, expanda **Alta Disponibilidade AlwaysOn** e **Grupos de Disponibilidade**. Agora você poderá ver o novo grupo de disponibilidade neste contêiner. Clique com o botão direito do mouse em **AG1 (Primário)** e clique em **Mostrar Painel**.
    
     ![Mostrar Painel de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665532.gif)
11. O **Painel do AlwaysOn** deve ser semelhante ao mostrado abaixo. Você pode ver as réplicas, o modo de failover de cada réplica e o estado de sincronização.
    
     ![Painel de AG](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665533.gif)
12. Retorne ao **Gerenciador do Servidor**, selecione **Ferramentas**, e inicie o **Gerenciador de Cluster de Failover**.
13. Expanda **Cluster1.corp.contoso.com** e depois expanda **Serviços e Aplicativos**. Selecione **Funções** e observe se a função do grupo de disponibilidade **AG1** foi criada. Observe que o AG1 não tem nenhum endereço IP pelo qual os clientes do banco de dados podem se conectar ao grupo de disponibilidade porque você não configurou um ouvinte. Você pode se conectar diretamente ao nó principal para operações de leitura/gravação e o nó secundário para consultas somente leitura.
    
     ![AG no Gerenciador de Cluster de Failover](./media/virtual-machines-windows-classic-portal-sql-alwayson-availability-groups/IC665534.gif)

> [!WARNING]
> Não tente fazer failover do grupo de disponibilidade no Gerenciador de Cluster de Failover. Todas as operações de failover devem ser executadas no **Painel do AlwaysOn** no SSMS. Para obter mais informações, consulte [Restrições do uso do Gerenciador de Cluster de Failover WSFC com Grupos de Disponibilidade](https://msdn.microsoft.com/library/ff929171.aspx).
> 
> 

## <a name="next-steps"></a>Próximas etapas
Agora você implementou com êxito o SQL Server AlwaysOn criando um grupo de disponibilidade no Azure. Para configurar um ouvinte para este grupo de disponibilidade, veja [Configurar um ouvinte de ILB para Grupos de Disponibilidade Always On no Azure](virtual-machines-windows-classic-ps-sql-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

Para obter outras informações sobre como usar o SQL Server no Azure, veja [SQL Server em Máquinas Virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).




<!--HONumber=Nov16_HO3-->


