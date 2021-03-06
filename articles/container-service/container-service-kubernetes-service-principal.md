---
title: "Entidade de serviço para cluster Kubernetes do Azure | Microsoft Docs"
description: "Criar e gerenciar uma entidade de serviço do Azure Active Directory em um cluster do Serviço de Contêiner do Azure com Kubernetes"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service, kubernetes
keywords: 
ms.assetid: 
ms.service: container-service
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/20/2016
ms.author: danlep
translationtype: Human Translation
ms.sourcegitcommit: 24e12e4606a5ec4fabf7046fe9847123033bb70a
ms.openlocfilehash: 073a9e66ac68643b27ecdd44a4ecac3ad79ec218


---

# <a name="about-the-azure-active-directory-service-principal-for-a-kubernetes-cluster-in-azure-container-service"></a>Sobre a entidade de serviço do Azure Active Directory para um cluster Kubernetes no Serviço de Contêiner do Azure



No Serviço de Contêiner do Azure, o Kubernetes requer uma [entidade de serviço do Azure Active Directory](../active-directory/active-directory-application-objects.md) como uma conta de serviço para interagir com as APIs do Azure. A entidade de serviço é necessária para gerenciar dinamicamente recursos como [rotas definidas pelo usuário](../virtual-network/virtual-networks-udr-overview.md) e o [Azure Load Balancer](../load-balancer/load-balancer-overview.md) da Camada 4.

Este artigo mostra diferentes opções para especificar uma entidade de serviço para o cluster Kubernetes. Por exemplo, se tiver instalado e configurado a [CLI do Azure 2.0 (Visualização)](https://docs.microsoft.com/cli/azure/install-az-cli2), você poderá executar o comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) para criar o cluster Kubernetes e a entidade de serviço ao mesmo tempo.

> [!NOTE]
> O suporte a Kubernetes no Serviço de Contêiner do Azure está atualmente em visualização.


## <a name="requirements-for-the-service-principal"></a>Requisitos para a entidade de serviço

Estes são os requisitos para a entidade de serviço do Azure Active Directory em um cluster Kubernetes no Serviço de Contêiner do Azure. 

* **Escopo** - a assinatura do Azure na qual o cluster for implantado

* **Função** - **Colaborador**

* **Segredo do cliente** - deve ser uma senha. Atualmente, é possível usar uma entidade de serviço configurada para autenticação de certificado.

> [!NOTE]
> Cada entidade de serviço é associada a um aplicativo do Azure Active Directory. A entidade de serviço para um cluster Kubernetes pode ser associada a qualquer nome de aplicativo válido do Azure Active Directory.
> 


## <a name="service-principal-options-for-a-kubernetes-cluster"></a>Opções de entidades de serviço para um cluster Kubernetes

### <a name="option-1-pass-the-service-principal-client-id-and-client-secret"></a>Opção 1: passar o segredo do cliente e a ID da entidade de serviço

Forneça a **ID do cliente** (geralmente chamada de `appId`, para ID de aplicativo) e o **segredo do cliente** (`password`) de uma entidade de serviço existente como parâmetros ao criar o cluster Kubernetes. Se estiver usando uma entidade de serviço existente, verifique se ela atende aos requisitos da seção anterior. Se precisar criar uma entidade de serviço, confira [Criar uma entidade de serviço](#create-a-service-principal-in-azure-active-directory) posteriormente neste artigo.

Você pode especificar esses parâmetros ao [implantar o cluster Kubernetes](./container-service-deployment.md) usando o portal, a CLI (Interface de Linha de Comando) do Azure ou o Azure PowerShell.

>[!TIP] 
>Ao especificar a **ID do cliente**, use `appId` e não `ObjectId` da entidade de serviço.
>

O exemplo a seguir mostra uma maneira de passar os parâmetros com a [CLI do Azure](../xplat-cli-install.md) no [modo do Gerenciador de Recursos](../xplat-cli-connect.md). Este exemplo usa o [modelo de início rápido do Kubernetes](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Baixe](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) os parâmetros do modelo de arquivo azuredeploy.parameters.json do GitHub.

2. Para especificar a entidade de serviço, insira valores para `servicePrincipalClientId` e `servicePrincipalClientSecret` no arquivo. (Você também precisa fornecer seus próprios valores para `dnsNamePrefix` e `sshRSAPublicKey`. O segundo é a chave pública SSH para acessar o cluster.) Salve o arquivo.

    ![Passar parâmetros de entidade de serviço](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Execute o seguinte comando, usando o parâmetro `-e` para definir o caminho para o arquivo azuredeploy.parameters.json. Este comando implanta o cluster em um grupo de recursos existente chamado `myResourceGroup`.

    ```CLI
    azure group deployment create -n myClusterName -g myResourceGroup --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" -e azuredeploy.parameters.json
    ```


### <a name="option-2-generate-the-service-principal-when-creating-the-cluster-with-the-azure-cli-20-preview"></a>Opção 2: gerar a entidade de serviço ao criar o cluster com a CLI do Azure 2.0 Preview

Se tiver instalado e configurado a [CLI do Azure 2.0 (Visualização)](https://docs.microsoft.com/cli/azure/install-az-cli2), você poderá executar o comando [`az acs create`](https://docs.microsoft.com/en-us/cli/azure/acs#create) para [criar o cluster](./container-service-create-acs-cluster-cli.md).

Como ocorre com outras opções de criação do cluster Kubernetes, você pode especificar parâmetros para uma entidade de serviço existente quando executa `az acs create`. No entanto, quando você omite esses parâmetros, o Serviço de Contêiner do Azure cria uma entidade de serviço automaticamente. Isso ocorre de forma transparente durante a implantação. 

O comando a seguir cria um cluster Kubernetes e gera chaves SSH e credenciais de entidade de serviço:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

## <a name="create-a-service-principal-in-azure-active-directory"></a>Criar uma entidade de serviço no Azure Active Directory

Se você quer criar uma entidade de serviço no Azure Active Directory para uso no cluster Kubernetes, o Azure fornece vários métodos. 

Os comandos de exemplo a seguir mostram como fazer isso com a [CLI do Azure 2.0 (Preview)](https://docs.microsoft.com/cli/azure/install-az-cli2). Como alternativa, você pode criar uma entidade de serviço usando a [Interface de Linha de Comando do Azure](../azure-resource-manager/resource-group-authenticate-service-principal-cli.md), o [Azure PowerShell](../azure-resource-manager/resource-group-authenticate-service-principal.md) ou o [portal clássico](../azure-resource-manager/resource-group-create-service-principal-portal.md).

> [!IMPORTANT]
> Examine os requisitos da entidade de serviço neste artigo.
>

```console
az login

az account set --subscription="mySubscriptionID"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/mySubscriptionID"
```

Isso retorna uma saída semelhante à seguinte (mostrada aqui em uma versão editada):

![Criar uma entidade de serviço](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Estão realçados a **ID do cliente** (`appId`) e o **segredo do cliente** (`password`) que você usa como parâmetros da entidade de serviço para a implantação de cluster.


Confirme a entidade de serviço, abra um novo shell e execute os seguintes comandos, substituindo `appId`, `password` e `tenant`:

```console 
az login --service-principal -u yourClientID -p yourClientSecret --tenant yourTenant

az vm list-sizes --location westus
```

## <a name="additional-considerations"></a>Considerações adicionais


* Ao especificar a **ID do cliente** da entidade de serviço, você pode usar o valor de `appId` (conforme mostrado neste artigo) ou `name` da entidade de serviço correspondente (por exemplo, `https://www.contoso.org/example`).

* Se você usar o comando `az acs create` para gerar a entidade de serviço automaticamente, as credenciais da entidade de serviço serão gravadas no arquivo ~/.azure/acsServicePrincipal.json no computador usado para executar o comando.

* Nas VMs mestre e de nó no cluster Kubernetes, as credenciais de entidade de serviço são armazenadas no arquivo /etc/kubernetes/azure.json.

## <a name="next-steps"></a>Próximas etapas

* [Introdução ao Kubernetes](container-service-kubernetes-walkthrough.md) em seu cluster de serviço de contêiner.



<!--HONumber=Dec16_HO3-->


