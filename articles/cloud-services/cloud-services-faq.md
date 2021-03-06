---
title: "Perguntas frequentes sobre Serviços de Nuvem | Microsoft Docs"
description: "Perguntas frequentes sobre os Serviços de Nuvem."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 84985660-2cfd-483a-8378-50eef6a0151d
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 2501b6480e81b236995c37db7171a4ed1429dcbf
ms.openlocfilehash: f7bad9a46132dec43f73e561362c9e6441a5c1c0


---
# <a name="cloud-services-faq"></a>Perguntas frequentes sobre Serviços de Nuvem
Este artigo responde a algumas perguntas frequentes sobre os Serviços de Nuvem do Microsoft Azure. Você também pode visitar as [Perguntas frequentes de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para informações gerais sobre preços e suporte do Azure. Você também pode consultar a [página de tamanho de VM de Serviços de Nuvem](cloud-services-sizes-specs.md) para obter informações de tamanho.

## <a name="certificates"></a>Certificados
### <a name="where-should-i-install-my-certificate"></a>Onde devo instalar o certificado?
* **Meu**  
  Certificado do aplicativo com a chave privada (\*. pfx, \*. p12).
* **AC**  
   Todos os certificados intermediários ficam neste repositório (política e Sub ACs).
* **RAIZ**  
   O repositório de AC raiz, para que seu certificado de AC raiz seja inserido aqui.

### <a name="i-cant-remove-expired-certificate"></a>Não é possível remover o certificado expirado
Azure impede a remoção de um certificado enquanto ele está em uso. É necessário excluir a implantação que usa o certificado ou atualizá-la com um certificado diferente ou renovado.

### <a name="delete-an-expired-certificate"></a>Excluir um certificado expirado
Enquanto o certificado não está em uso, você pode usar o cmdlet do PowerShell [AzureCertificate remover](https://msdn.microsoft.com/library/azure/mt589145.aspx) para remover um certificado.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Tenho certificados expirados denominados Gerenciamento de Serviço do Microsoft Azure para Extensões
Esses certificados são criados sempre que uma extensão é adicionada ao serviço de nuvem, como a extensão de área de trabalho remota. Esses certificados são usados apenas para criptografar e descriptografar a configuração privada da extensão. Não importa se esses certificados expiram. A data de validade não é verificada.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Certificados que excluí continuam reaparecendo
Eles continuam reaparecendo muito provavelmente devido a uma ferramenta que você está usando, como o Visual Studio. Sempre que você se reconectar com uma ferramenta que está usando um certificado, ele será carregado novamente no Azure.

### <a name="my-certificates-keep-disappearing"></a>Meus certificados continuam desaparecendo
Quando a instância de máquina virtual for reciclada, todas as alterações locais serão perdidas. Use um [tarefa de inicialização](cloud-services-startup-tasks.md) para instalar certificados na máquina virtual sempre que a função for iniciada.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Não consigo encontrar meus certificados de gerenciamento no portal
Os [Certificados de gerenciamento](../azure-api-management-certs.md) estão disponíveis apenas no Portal Clássico do Azure. O Portal do Azure atual não usa certificados de gerenciamento. 

### <a name="how-can-i-disable-a-management-certificate"></a>Como desabilitar um certificado de gerenciamento?
[certificados de gerenciamento](../azure-api-management-certs.md) não podem ser desabilitados. Exclua-os no Portal Clássico do Azure quando você não quiser mais usá-los.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Como criar um certificado SSL para um endereço IP específico?
Siga as instruções no [tutorial Criar um certificado](cloud-services-certs-create.md). Use o endereço IP como o nome DNS.

## <a name="security"></a>Segurança
### <a name="disable-ssl-30"></a>Desabilitar SSL 3.0
Para desabilitar o SSL 3.0 e usar a segurança TLS, crie uma tarefa de inicialização que está documentada nesta postagem de blog: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Dimensionamento de um serviço de nuvem
### <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar além de X instâncias
Sua Assinatura do Azure tem um limite no número de núcleos que você pode usar. O dimensionamento não funcionará se você tiver usado todos os núcleos disponíveis. Por exemplo, se você tiver um limite de 100 núcleos, isso significa você poderia ter 100 instâncias de máquina virtual A1 dimensionadas para seu serviço de nuvem ou 50 instâncias de máquina virtual A2.

## <a name="troubleshooting"></a>Solucionar problemas
### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Eu não consigo reservar um IP em um serviço de nuvem de vários VIPs
Primeiro, certifique-se de que a instância de máquina virtual que você está tentando reservar o IP esteja ativada. Segundo, certifique-se de que você esteja usando IPs reservados para as implantações de preparo e produção. **Não** altere as configurações enquanto a implantação é atualizada.




<!--HONumber=Nov16_HO3-->


