---
title: "Guia do desenvolvedor - cotas e limitações | Microsoft Docs"
description: "Guia do desenvolvedor do Hub IoT do Azure - descrição das cotas que se aplicam ao Hub IoT e o comportamento de limitação esperado"
services: iot-hub
documentationcenter: .net
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 425e1b08-8789-4377-85f7-c13131fae4ce
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/30/2016
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: c0f8c779d7f9552dc05ac3791b74c3d57cb1fe64


---
# <a name="reference---quotas-and-throttling"></a>Referência - Cotas e limitação
## <a name="quotas-and-throttling"></a>Cotas e limitação
Cada assinatura do Azure pode ter no máximo 10 hubs IoT e pelo menos 1 hub Gratuito.

Cada hub IoT é provisionado com um determinado número de unidades em um SKU específico (para obter mais informações, veja [Preços do Hub IoT do Azure][lnk-pricing]). O SKU e o número de unidades determinam a cota máxima diária de mensagens que você pode enviar.

O SKU também determina os limites impostos pelo Hub IoT em todas as operações.

## <a name="operation-throttles"></a>Restrições de operação
As restrições de operação são limites de taxa aplicados em intervalos de minutos, e têm como objetivo evitar o abuso. O Hub IoT tenta evitar o retorno de erros sempre que possível, mas ele começará a retornar exceções se a restrição for violada por muito tempo.

A seguir, a lista de limitações impostas. Os valores referem-se a um hub individual.

| Restrição | Hubs Gratuitos e S1 | Hubs S2 | Hubs S3 | 
| -------- | ------- | ------- | ------- |
| Operações de registro de identidade (criar, recuperar, listar, atualizar, excluir) | 100/min/unidade | 100/min/unidade | 5000/min/unidade |
| Conexões do dispositivo | Máximo de 100/s ou 12/s/unidade <br/> Por exemplo, duas unidades de S1 são 2\*12 = 24/s, mas você tem pelo menos 100/s em suas unidades. Com nove unidades S1 você tem 108/s (9\*12) em suas unidades. | 120/s/unidade | 6000/s/unidade |
| Envios do dispositivo para a nuvem | Máximo de 100/s ou 12/s/unidade <br/> Por exemplo, duas unidades de S1 são 2\*12 = 24/s, mas você tem pelo menos 100/s em suas unidades. Com nove unidades S1 você tem 108/s (9\*12) em suas unidades. | 120/s/unidade | 6000/s/unidade |
| Envios da nuvem para o dispositivo | 100/min/unidade | 100/min/unidade | 5000/min/unidade |
| Recebimentos da nuvem para o dispositivo <br/> (somente quando os dispositivos usam HTTP)| 1000/min/unidade | 1000/min/unidade| 50000/min/unidade |
| Upload de arquivos | 100 notificações de upload de arquivo/min/unidade | 100 notificações de upload de arquivo/min/unidade | 5000 notificações de upload de arquivo/min/unidade |
| Métodos diretos | 10/s/unidade | 30/s/unidade | 1500/s/unidade | 
| Leituras de gêmeos | 10/s | Máximo de 10/s ou 1/s/unidade | 50/s/unidade |
| Atualizações de gêmeos | 10/s | Máximo de 10/s ou 1/s/unidade | 50/s/unidade |
| Operações de trabalhos <br/> (criar, atualizar, listar, excluir) | 100/min/unidade | 100/min/unidade | 5000/min/unidade |
| Taxa de transferência de operação de trabalhos por dispositivo | 10/s | Máximo de 10/s ou 1/s/unidade | 50/s/unidade |

É importante esclarecer que a restrição de *conexões de dispositivo* controla a taxa em que novas conexões de dispositivo podem ser estabelecidas com um Hub IoT e não o número máximo de dispositivos conectados simultaneamente. A restrição depende do número de unidades provisionadas para o hub IoT.

Por exemplo, se você comprar uma única unidade S1, obterá uma restrição de 100 conexões por segundo. Isso significa que serão necessários pelo menos 1000 segundos (aproximadamente 16 minutos) para conectar 100.000 dispositivos. No entanto, você pode conectar ao mesmo tempo todos os seus dispositivos registrados no registro de identidade.

Para uma discussão aprofundada do comportamento de limitação do Hub IoT, veja a postagem do blog [A limitação do Hub IoT e você][lnk-throttle-blog].

> [!NOTE]
> A qualquer momento, é possível aumentar as cotas ou restrições aumentando o número de unidades provisionadas em um Hub IoT.
> 
> [!IMPORTANT]
> As operações de registro de identidade são destinadas ao uso no tempo de execução em cenários de provisionamento e gerenciamento de dispositivos. Há suporte para leitura ou atualização de grandes números de identidades de dispositivo por meio de [trabalhos de importação e exportação][lnk-importexport].
> 
> 

## <a name="other-limits"></a>Outros limites

O Hub IoT impõe outros limites nas suas diferentes funcionalidades.

| Operação | Limite |
| --------- | ----- |
| URIs de upload de arquivos | 10000 URIs de SAS podem estar fora de uma conta de armazenamento ao mesmo tempo. <br/>  10 URIs de SAS/dispositivo podem estar fora ao mesmo tempo. |
| Trabalhos | O histórico do trabalho é retido por até 30 dias <br/> O máximo de trabalhos simultâneos é 1 (para Gratuito e S1, 5 (para S2), 10 (para S3). |

## <a name="next-steps"></a>Próximas etapas
Outros tópicos de referência neste Guia do desenvolvedor do Hub IoT incluem:

* [Pontos de extremidade do Hub IoT][lnk-devguide-endpoints]
* [Linguagem de consulta do Hub IoT para gêmeos e trabalhos][lnk-devguide-query]
* [Suporte ao MQTT do Hub IoT][lnk-devguide-mqtt]

[lnk-pricing]: https://azure.microsoft.com/pricing/details/iot-hub
[lnk-throttle-blog]: https://azure.microsoft.com/blog/iot-hub-throttling-and-you/
[lnk-importexport]: iot-hub-devguide-identity-registry.md#import-and-export-device-identities

[lnk-devguide-endpoints]: iot-hub-devguide-endpoints.md
[lnk-devguide-query]: iot-hub-devguide-query-language.md
[lnk-devguide-mqtt]: iot-hub-mqtt-support.md



<!--HONumber=Nov16_HO5-->


