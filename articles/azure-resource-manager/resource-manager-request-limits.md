---
title: "Limites de solicitação do Azure Resource Manager | Microsoft Docs"
description: "Descreve como usar a limitação com solicitações Azure Resource Manager quando os limites de assinatura tiverem sido atingidos."
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: e1047233-b8e4-4232-8919-3268d93a3824
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/07/2016
ms.author: tomfitz
translationtype: Human Translation
ms.sourcegitcommit: f6e684b08ed481cdf84faf2b8426da72f98fc58c
ms.openlocfilehash: 39678df7c52781d2f7e8fec49d97c96bee58ce8c


---
# <a name="throttling-resource-manager-requests"></a>Restrição de solicitações do Resource Manager
Para cada assinatura e locatário, os limites do Resource Manager limita as solicitações de leitura para 15.000 por hora e solicitações de gravação para 1.200 por hora. Se seu aplicativo ou script atingir esses limites, será necessário restringir suas solicitações. Este tópico mostra como determinar as solicitações restantes que você tem antes de atingir o limite e como responder quando você tiver atingido o limite.

Quando você alcança o limite, recebe o código de status HTTP **429 Excesso de solicitações**.

O número de solicitações é no escopo da sua assinatura ou de seu locatário. Se você tiver vários aplicativos simultâneos fazendo solicitações em sua assinatura, as solicitações desses aplicativos serão adicionadas juntas para determinar o número de solicitações restantes.

As solicitações no escopo da assinatura são aquelas que envolvem a passagem da ID de assinatura, assim como a recuperação dos grupos de recursos em sua assinatura. Solicitações no escopo do locatário não incluem sua ID de assinatura, assim como a recuperação de locais do Azure válidos.

## <a name="remaining-requests"></a>Solicitações restantes
Você pode determinar o número de solicitações restantes ao examinar cabeçalhos de resposta. Cada solicitação inclui os valores para o número de solicitações de leitura e gravação restantes. A tabela a seguir descreve os cabeçalhos de resposta que você pode examinar em busca desses valores:

| Cabeçalho de resposta | Descrição |
| --- | --- |
| x-ms-ratelimit-remaining-subscription-reads |Leituras no escopo da assinatura restantes |
| x-ms-ratelimit-remaining-subscription-writes |Gravações no escopo da assinatura restantes |
| x-ms-ratelimit-remaining-tenant-reads |Leituras no escopo do locatário restantes |
| x-ms-ratelimit-remaining-tenant-writes |Gravações no escopo do locatário restantes |
| x-ms-ratelimit-remaining-subscription-resource-requests |Solicitações de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações da assinatura. |
| x-ms-ratelimit-remaining-subscription-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo da assinatura restantes.<br /><br />Esse valor de cabeçalho só será retornado se um serviço tiver substituído o limite padrão. Esse valor fornece o número de solicitações de coleta restantes (listar recursos). |
| x-ms-ratelimit-remaining-tenant-resource-requests |Solicitações de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. O Resource Manager adiciona esse valor em vez das leituras ou gravações do locatário. |
| x-ms-ratelimit-remaining-tenant-resource-entities-read |Solicitações de coletas de tipo de recurso no escopo do locatário restantes.<br /><br />Esse cabeçalho será adicionado somente para solicitações em nível de locatário e somente se um serviço tiver substituído o limite padrão. |

## <a name="retrieving-the-header-values"></a>Recuperar os valores de cabeçalho
Recuperar esses valores de cabeçalho no seu código ou script não é diferente de recuperar um outro valor de cabeçalho qualquer. 

Por exemplo, em **C#**, recupere o valor de cabeçalho de um objeto **HttpWebResponse** chamado **response** com o código a seguir:

    response.Headers.GetValues("x-ms-ratelimit-remaining-subscription-reads").GetValue(0)

No **PowerShell**, você recupera o valor de cabeçalho de uma operação Invoke-WebRequest.

    $r = Invoke-WebRequest -Uri https://management.azure.com/subscriptions/{guid}/resourcegroups?api-version=2016-09-01 -Method GET -Headers $authHeaders
    $r.Headers["x-ms-ratelimit-remaining-subscription-reads"]

Ou, se quiser ver as solicitações restantes para depuração, você pode fornecer o parâmetro **-Debug** em seu cmdlet **PowerShell**.

    Get-AzureRmResourceGroup -Debug

Este retorna várias informações, incluindo o seguinte valor de resposta:

    ...
    DEBUG: ============================ HTTP RESPONSE ============================

    Status Code:
    OK

    Headers:
    Pragma                        : no-cache
    x-ms-ratelimit-remaining-subscription-reads: 14999
    ...

Na **CLI do Azure**, você recupera o valor do cabeçalho usando a opção mais detalhada.

    azure group list -vv --json

Isso retorna várias informações, incluindo o seguinte objeto:

    ...
    silly: returnObject
    {
      "statusCode": 200,
      "header": {
        "cache-control": "no-cache",
        "pragma": "no-cache",
        "content-type": "application/json; charset=utf-8",
        "expires": "-1",
        "x-ms-ratelimit-remaining-subscription-reads": "14998",
        ...

## <a name="waiting-before-sending-next-request"></a>Aguardando antes de enviar a próxima solicitação
Quando você alcançar o limite de solicitação, o Resource Manager retorna o código de status HTTP **429** e um valor **Retry-After** no cabeçalho. O valor **Retry-After** especifica o número de segundos que o aplicativo deve aguardar (ou ficar suspenso) antes de enviar a próxima solicitação. Se você enviar uma solicitação antes que o valor de repetição tenha decorrido, sua solicitação não será processada e um novo valor de repetição será retornado.




<!--HONumber=Nov16_HO3-->


