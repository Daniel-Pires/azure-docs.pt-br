---
title: "Adicionar ou remover nós de um cluster autônomo do Service Fabric | Microsoft Docs"
description: "Saiba como adicionar ou remover nós de um cluster do Azure Service Fabric em um computador físico ou virtual executando o Windows Server, que pode ser local ou em qualquer nuvem."
services: service-fabric
documentationcenter: .net
author: dsk-2015
manager: timlt
editor: 
ms.assetid: bc6b8fc0-d2af-42f8-a164-58538be38d02
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/20/2016
ms.author: dkshir;chackdan
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 335ab9d3746b089e9e7a8d640a89a2d381295b46


---
# <a name="add-or-remove-nodes-to-a-standalone-service-fabric-cluster-running-on-windows-server"></a>Adicionar ou remover nós de um cluster do Service Fabric autônomo em execução no Windows Server
Depois de ter [criado seu cluster autônomo do Service Fabric em computadores com Windows Server](service-fabric-cluster-creation-for-windows-server.md), suas necessidades de negócios podem ser alteradas para que você talvez precise adicionar ou remover vários nós ao cluster. Este artigo fornece etapas detalhadas para atingir esse objetivo.

## <a name="add-nodes-to-your-cluster"></a>Adicionar nós ao cluster
1. Preparar a VM/computador que você deseja adicionar ao cluster, seguindo as etapas mencionadas na seção [Preparar as máquinas para atender aos pré-requisitos para implantação de cluster](service-fabric-cluster-creation-for-windows-server.md#preparemachines) .
2. Planeje a qual domínio de falha e domínio de atualização você vai adicionar essa VM/computador.
3. RDP (área de trabalho remota) na VM/computador que você deseja adicionar ao cluster.
4. Copie ou [baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) para esta VM/computador e descompacte o pacote.
5. Execute o Powershell como administrador e navegue até o local do pacote descompactado.
6. Execute o PowerShell *AddNode.ps1* com os parâmetros que descrevem o novo nó a adicionar. O exemplo a seguir adiciona um novo nó chamado VM5, com tipo NodeType0, endereço IP 182.17.34.52 em UD1 e FD1. O *ExistingClusterConnectionEndPoint* é um ponto de extremidade de conexão para um nó em um cluster existente. Para esse ponto de extremidade, você pode escolher o endereço IP de *qualquer* nó no cluster.

```
.\AddNode.ps1 -NodeName VM5 -NodeType NodeType0 -NodeIPAddressorFQDN 182.17.34.52 -ExistingClientConnectionEndpoint 182.17.34.50:19000 -UpgradeDomain UD1 -FaultDomain FD1 -AcceptEULA

```

## <a name="remove-nodes-from-your-cluster"></a>Remover nós do cluster
1. Dependendo do nível de Confiabilidade escolhido para o cluster, você não pode remover os primeiros nós n (3/5/7/9) do tipo de nó principal
2. Não há suporte para executar o comando RemoveNode em um cluster de desenvolvimento.
3. RDP (área de trabalho remota) na VM/computador que você deseja remover do cluster.
4. Copie ou [baixe o pacote autônomo do Service Fabric para Windows Server](http://go.microsoft.com/fwlink/?LinkId=730690) e descompacte o pacote para esta VM/computador.
5. Execute o Powershell como administrador e navegue até o local do pacote descompactado.
6. Execute o *RemoveNode.ps1* no PowerShell. O exemplo a seguir remove o nó atual do cluster. O *ExistingClientConnectionEndpoint* é um ponto de extremidade de conexão de cliente para qualquer nó que permanecerá no cluster. Escolha o endereço IP e a porta do ponto de extremidade de *qualquer* **outro nó** no cluster. Esse **outro nó** por sua vez atualizará a configuração de cluster para o nó removido. 

```
.\RemoveNode.ps1 -ExistingClientConnectionEndpoint 182.17.34.50:19000
```

> [!NOTE]
> Alguns nós podem não ser removidos devido às dependências de serviços do sistema. Esses nós são nós primários e podem ser identificados consultando o manifesto do cluster usando `Get-ServiceFabricClusterManifest` e localizando entradas de nó marcadas com `IsSeedNode=”true”`. 
> 
> 

Mesmo após a remoção de um nó, se ele aparecer como desativado em consultas e SFX, observe que se trata de um defeito conhecido. Isso será corrigido em uma versão futura. 

## <a name="next-steps"></a>Próximas etapas
* [Definições de configuração para o cluster autônomo no Windows](service-fabric-cluster-manifest.md)
* [Proteger um cluster autônomo no Windows usando a segurança](service-fabric-windows-cluster-windows-security.md)
* [Proteger um cluster autônomo no Windows usando os certificados X509](service-fabric-windows-cluster-x509-security.md)
* [Criar um cluster do Service Fabric autônomo com VMs do Azure executando o Windows](service-fabric-cluster-creation-with-windows-azure-vms.md)




<!--HONumber=Nov16_HO3-->


