---
title: Suporte ao MQTT do Hub IoT | Microsoft Docs
description: "Descrição do suporte ao MQTT no nível do Hub IoT"
services: iot-hub
documentationcenter: .net
author: kdotchkoff
manager: timlt
editor: 
ms.assetid: 1d71c27c-b466-4a40-b95b-d6550cf85144
ms.service: iot-hub
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/24/2016
ms.author: kdotchko
translationtype: Human Translation
ms.sourcegitcommit: c18a1b16cb561edabd69f17ecebedf686732ac34
ms.openlocfilehash: cb771818a437fdacd20fe192a087ebc0c8952f21


---
# <a name="iot-hub-mqtt-support"></a>Suporte ao MQTT do Hub IoT
O Hub IoT permite que dispositivos se comuniquem com os pontos de extremidade do dispositivo do Hub IoT usando o protocolo [MQTT v3.1.1][lnk-mqtt-org] na porta 8883 ou MQTT v3.1.1 sobre protocolo WebSocket na porta 443. Hub IoT exige que todas as comunicações de dispositivo a sejam protegidas usando o TLS/SSL (portanto, Hub IoT não oferece suporte a conexões não seguras pela porta 1883).

## <a name="connecting-to-iot-hub"></a>Conectando-se ao Hub IoT
Um dispositivo pode usar o protocolo MQTT para se conectar a um hub IoT usando as bibliotecas de [SDKs do IoT do Azure][lnk-device-sdks] ou usando o protocolo MQTT diretamente.

## <a name="using-the-device-sdks"></a>Usando os SDKs de dispositivo
Os [SDKs do cliente do dispositivo][lnk-device-sdks] que são compatíveis com o protocolo MQTT estão disponíveis para Java, Node.js, C, C# e Python. Os SDKs do dispositivo usam a cadeia de conexão do Hub IoT padrão para estabelecer uma conexão com um Hub IoT. Para usar o protocolo MQTT, o parâmetro do protocolo do cliente deve ser definido como **MQTT**. Por padrão, os SDKs do dispositivo se conectam a um Hub IoT com o sinalizador **CleanSession** definido como **0** e usam **QoS 1** para troca de mensagens com o Hub IoT.

Quando um dispositivo é conectado a um Hub IoT, os SDKs do dispositivo fornecem métodos que permitem que o dispositivo envie mensagens para um Hub IoT e receba mensagens dele.

A tabela a seguir contém links para exemplos de código de cada linguagem compatível e especifica o parâmetro a ser usado para estabelecer uma conexão com o Hub IoT usando o protocolo MQTT.

| Linguagem | Parâmetro do protocolo |
| --- | --- |
| [Node.js][lnk-sample-node] |azure-iot-device-mqtt |
| [Java][lnk-sample-java] |IotHubClientProtocol.MQTT |
| [C][lnk-sample-c] |MQTT_Protocol |
| [C#][lnk-sample-csharp] |TransportType.Mqtt |
| [Python][lnk-sample-python] |IoTHubTransportProvider.MQTT |

### <a name="migrating-a-device-app-from-amqp-to-mqtt"></a>Migrando um aplicativo de dispositivo de AMQP para MQTT
Se você estiver usando o [SDKs de cliente de dispositivo][lnk-device-sdks], a mudança do uso de AMQP para MQTT exige alteração do parâmetro de protocolo na inicialização do cliente, conforme mencionado acima.

Ao fazer isso, verifique os seguintes itens:

* AMQP retorna erros para várias condições, enquanto MQTT encerra a conexão. Como resultado, sua lógica de manipulação de exceções pode exigir algumas alterações.
* MQTT não dá suporte a operações de *rejeição* quando recebe [mensagens da nuvem para do dispositivo][lnk-messaging]. Se o back-end precisar receber uma resposta do aplicativo do dispositivo, considere o uso de [métodos diretos][lnk-methods].

## <a name="using-the-mqtt-protocol-directly"></a>Usando o protocolo MQTT diretamente
Se um dispositivo não puder usar os SDKs do dispositivo, ele poderá se conectar com os pontos de extremidade públicos do dispositivo usando o protocolo MQTT. No pacote **CONNECT** , o dispositivo deve usar os seguintes valores:

* No campo **ClientId**, use o **deviceId**.
* No campo **Username**, use `{iothubhostname}/{device_id}`, em que {iothubhostname} é o CName completo do Hub IoT.

    Por exemplo, se o nome de seu Hub IoT for **contoso.azure-devices.net** e se o nome do dispositivo for **MyDevice01**, o campo **Username** completo deverá conter `contoso.azure-devices.net/MyDevice01`.
* No campo **Senha** use um token SAS. O formato do token SAS é o mesmo, conforme descrito para os protocolos HTTP e AMQP:<br/>`SharedAccessSignature sig={signature-string}&se={expiry}&sr={URL-encoded-resourceURI}`.

    Para saber mais sobre como gerar tokens SAS, confira a seção de dispositivo de [Usar tokens de segurança do Hub IoT][lnk-sas-tokens].

    Durante o teste, você também pode usar a ferramenta [Gerenciador de Dispositivo][lnk-device-explorer] para gerar rapidamente um token SAS que pode ser copiado e colado em seu próprio código:

  1. Acesse a guia **Gerenciamento** no Gerenciador de Dispositivo.
  2. Clique em **Token SAS** (parte superior direita).
  3. Em **SASTokenForm**, selecione seu dispositivo no menu suspenso **DeviceID**. Defina o **TTL**.
  4. Clique em **Gerar** para criar o token.

     O token SAS gerado tem esta estrutura: `HostName={your hub name}.azure-devices.net;DeviceId=javadevice;SharedAccessSignature=SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fMyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802`.

     A parte disso que deve ser usada, como no campo **Senha**, para se conectar usando MQTT é: `SharedAccessSignature sr={your hub name}.azure-devices.net%2fdevices%2fyDevice01&sig=vSgHBMUG.....Ntg%3d&se=1456481802g%3d&se=1456481802`.

Para pacotes de conexão e desconexão do MQTT, o Hub IoT emite um evento no canal **Monitoramento de operações** com informações adicionais que podem ajudar você a solucionar problemas de conectividade.

### <a name="sending-device-to-cloud-messages"></a>Enviando mensagens de dispositivo para nuvem
Depois de fazer uma conexão bem-sucedida, um dispositivo pode enviar mensagens ao IoT Hub usando `devices/{device_id}/messages/events/` ou `devices/{device_id}/messages/events/{property_bag}` como um **nome de tópico**. O elemento `{property_bag}` habilita o dispositivo a enviar mensagens com propriedades adicionais em um formato codificado de URL. Por exemplo:

```
RFC 2396-encoded(<PropertyName1>)=RFC 2396-encoded(<PropertyValue1>)&RFC 2396-encoded(<PropertyName2>)=RFC 2396-encoded(<PropertyValue2>)…
```

> [!NOTE]
> Esse elemento `{property_bag}` usa a mesma codificação para cadeias de caracteres de consulta do protocolo HTTP.
>
>

O aplicativo cliente do dispositivo também pode usar `devices/{device_id}/messages/events/{property_bag}` como o **nome do tópico Will** para definir *mensagens Will* a serem encaminhadas como uma mensagem de telemetria.

O Hub IoT não dá suporte a mensagens de QoS 2. Se um cliente de dispositivo publica uma mensagem com o **QoS 2**, o Hub IoT fecha a conexão de rede.
O Hub IoT não persiste mensagens de retenção. Se um dispositivo envia uma mensagem com o sinalizador **RETAIN** definido como 1, o Hub IoT adiciona a propriedade de aplicativo **x-opt-retain** à mensagem. Nesse caso, em vez de persistir a mensagem de retenção, o Hub IoT a transmite ao aplicativo back-end.

Consulte [Guia do desenvolvedor do sistema de mensagens][lnk-messaging] para saber mais.

### <a name="receiving-cloud-to-device-messages"></a>Recebendo mensagens da nuvem para o dispositivo
Para receber mensagens do Hub IoT, um dispositivo deve fazer uma assinatura usando `devices/{device_id}/messages/devicebound/#` como um **Filtro do Tópico**. O curinga de vários níveis **#** no filtro de tópico é usado apenas para permitir que o dispositivo receba propriedades adicionais no nome do tópico. O Hub IoT não permite o uso de caracteres curinga **#** ou **?** para filtragem de subtópicos. Como o Hub IoT Hub não é um agente de mensagens pub-sub de finalidade geral, ele suporta apenas os nomes de tópico e filtros de tópico documentados.

Observe que o dispositivo não receberá todas as mensagens do Hub IoT, antes de ter assinado com sucesso o ponto de extremidade específico do seu dispositivo, representado pelo filtro de tópico `devices/{device_id}/messages/devicebound/#`. Depois que a assinatura tiver sido estabelecida com êxito, o dispositivo começará a receber apenas as mensagens de nuvem para dispositivos que foram enviadas para ele após o momento da assinatura. Se o dispositivo se conectar com o sinalizador **CleanSession** definido como **0**, a assinatura será persistida entre as diferentes sessões. Nesse caso, na próxima vez que ele se conectar com **CleanSession 0**, o dispositivo receberá mensagens pendentes que foram enviadas a ele enquanto ele estava desconectado. Se o dispositivo usar o sinalizador **CleanSession** definido como **1**, não receberá todas as mensagens do Hub IoT até que ele se inscreva no ponto de extremidade do seu dispositivo.

IoT Hub entrega mensagens com o **Nome do Tópico** `devices/{device_id}/messages/devicebound/` ou `devices/{device_id}/messages/devicebound/{property_bag}` se houver propriedades de mensagem. `{property_bag}` contém pares de chave/valor codificados de URL das propriedades da mensagem. Somente propriedades de aplicativo e propriedades do sistema definível pelo usuário (como **messageId** ou **correlationId**) estão incluídas no recipiente de propriedades. Os nomes de propriedade do sistema têm o prefixo **$**; as propriedades de aplicativo usam o nome da propriedade original sem prefixo.

Quando um cliente de dispositivo assina um tópico com o **QoS 2**, o Hub IoT concede, no máximo, o nível 1 do QoS no pacote **SUBACK**. Depois disso, o Hub IoT entregará mensagens para o dispositivo usando QoS 1.

### <a name="retrieving-a-device-twins-properties"></a>Recuperando as propriedades de um dispositivo gêmeo

Primeiro, um dispositivo assina `$iothub/twin/res/#` para receber respostas da operação. Em seguida, ele envia uma mensagem vazia ao tópico `$iothub/twin/GET/?$rid={request id}`, com um valor preenchido como a **ID da solicitação**. O serviço enviará uma mensagem de resposta aos dados do dispositivo gêmeo no tópico `$iothub/twin/res/{status}/?$rid={request id}` usando a mesma **ID da solicitação** como solicitação.

A ID da solicitação pode ser qualquer valor válido para um valor de propriedade de mensagem de acordo com o [Guia do desenvolvedor do sistema de mensagens do Hub IoT][lnk-messaging], e o status é validado como um número inteiro.
O corpo da resposta conterá a seção Propriedades do dispositivo gêmeo:

O corpo da entrada do registro de identidade limitado ao membro "propriedades", por exemplo,

        {
            "properties": {
                "desired": {
                    "telemetrySendFrequency": "5m",
                    "$version": 12
                },
                "reported": {
                    "telemetrySendFrequency": "5m",
                    "batteryLevel": 55,
                    "$version": 123
                }
            }
        }

Os códigos de status possíveis são:

|Status | Descrição |
| ----- | ----------- |
| 200 | Sucesso |
| 429 | Número excessivo de solicitações (limitado), de acordo com a [Limitação do Hub IoT][lnk-quotas] |
| 5** | Erros do servidor |

Consulte o [Guia do desenvolvedor dos dispositivos gêmeos][lnk-devguide-twin] para saber mais.

### <a name="update-twins-reported-properties"></a>Atualizar propriedades relatadas do gêmeo

Primeiro, um dispositivo precisa assinar `$iothub/twin/res/#` para receber respostas da operação. Em seguida, ele envia uma mensagem que contém a atualização do dispositivo gêmeo para `$iothub/twin/PATCH/properties/reported/?$rid={request id}`, com um valor preenchido como a **ID da solicitação**. O serviço enviará uma mensagem de resposta aos dados do dispositivo gêmeo no tópico `$iothub/twin/res/{status}/?$rid={request id}` usando a mesma **ID da solicitação** como solicitação.

O corpo da mensagem de solicitação contém um documento JSON que fornece novos valores para as propriedades relatadas (nenhuma outra propriedade ou metadados podem ser modificados).
Cada membro no documento JSON atualiza ou adiciona o membro correspondente no documento do dispositivo gêmeo. Um membro definido como `null` exclui o membro do objeto recipiente. Por exemplo

        {
            "telemetrySendFrequency": "35m",
            "batteryLevel": 60
        }

Os códigos de status possíveis são:

|Status | Descrição |
| ----- | ----------- |
| 200 | Sucesso |
| 400 | Solicitação inválida. JSON malformado |
| 429 | Número excessivo de solicitações (limitado), de acordo com a [Limitação do Hub IoT][lnk-quotas] |
| 5** | Erros do servidor |

Consulte o [Guia do desenvolvedor dos dispositivos gêmeos][lnk-devguide-twin] para saber mais.

### <a name="receiving-desired-properties-update-notifications"></a>Recebendo notificações de atualização de propriedades desejadas

Quando um dispositivo é conectado, o Hub IoT envia notificações para o tópico `$iothub/twin/PATCH/properties/desired/?$version={new version}`, que contêm o conteúdo da atualização executada pelo back-end. Por exemplo,

        {
            "telemetrySendFrequency": "5m",
            "route": null
        }

Em relação às atualizações de propriedade, valores `null` significam que o membro do objeto JSON está sendo excluído.

> [AZURE.IMPORTANT] O Hub IoT só gera notificações de alteração quando os dispositivos estão conectados; não deixe de implementar o [fluxo de reconexão do dispositivo][lnk-devguide-twin-reconnection] para manter as propriedades desejadas sincronizadas entre o Hub IoT e o aplicativo do dispositivo.

Consulte o [Guia do desenvolvedor dos dispositivos gêmeos][lnk-devguide-twin] para saber mais.

### <a name="respond-to-a-direct-method"></a>Responder a um método direto

Primeiro, um dispositivo precisa assinar `$iothub/methods/POST/#`. O Hub IoT envia solicitações de método para o tópico `$iothub/methods/POST/{method name}/?$rid={request id}` com um corpo vazio ou JSON válido.

Para responder, o dispositivo enviará uma mensagem com um corpo vazio ou um JSON válido para o tópico `$iothub/methods/res/{status}/?$rid={request id}`, onde a **ID da solicitação** tem que corresponder à que está na mensagem de solicitação, e o **status** deverá ser um número inteiro.

Consulte o [Guia do desenvolvedor do método direto][lnk-methods] para saber mais.

### <a name="additional-considerations"></a>Considerações adicionais
Como uma consideração final, se você precisar personalizar o comportamento do protocolo MQTT no lado da nuvem, deverá examinar o [Gateway de protocolo IoT do Azure][lnk-azure-protocol-gateway], que permite que você implante um gateway de protocolo personalizado de alto desempenho que interage diretamente com o Hub IoT. O gateway do protocolo IoT do Azure permite que você personalize o protocolo de dispositivo para acomodar as implantações de MQTT de nível industrial ou outros protocolos personalizados. Essa abordagem exige, no entanto, que você execute e opere um gateway de protocolo personalizado.

## <a name="next-steps"></a>Próximas etapas
Para saber mais, confira [Observações sobre o suporte a MQTT][lnk-mqtt-devguide] no guia do desenvolvedor do Hub IoT do Azure.

Para saber mais sobre o protocolo MQTT, consulte a [documentação do MQTT][lnk-mqtt-docs].

Para saber mais sobre como planejar sua implantação do Hub IoT, consulte:

* [Catálogo de dispositivos Azure Certified para IoT][lnk-devices]
* [Suporte a protocolos adicionais][lnk-protocols]
* [Comparar com Hubs de Eventos][lnk-compare]
* [Escala, alta disponibilidade e recuperação de desastre][lnk-scaling]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia do desenvolvedor][lnk-devguide]
* [Simulando um dispositivo com o SDK do Gateway do IoT][lnk-gateway]

[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks/blob/master/readme.md
[lnk-mqtt-org]: http://mqtt.org/
[lnk-mqtt-docs]: http://mqtt.org/documentation
[lnk-sample-node]: https://github.com/Azure/azure-iot-sdks/blob/develop/node/device/samples/simple_sample_device.js
[lnk-sample-java]: https://github.com/Azure/azure-iot-sdks/blob/develop/java/device/samples/send-receive-sample/src/main/java/samples/com/microsoft/azure/iothub/SendReceive.java
[lnk-sample-c]: https://github.com/Azure/azure-iot-sdks/tree/master/c/iothub_client/samples/iothub_client_sample_mqtt
[lnk-sample-csharp]: https://github.com/Azure/azure-iot-sdks/tree/master/csharp/device/samples
[lnk-sample-python]: https://github.com/Azure/azure-iot-sdks/tree/master/python/device/samples
[lnk-device-explorer]: https://github.com/Azure/azure-iot-sdks/blob/master/tools/DeviceExplorer/readme.md
[lnk-sas-tokens]: iot-hub-devguide-security.md#use-sas-tokens-in-a-device-client
[lnk-mqtt-devguide]: iot-hub-devguide-messaging.md#notes-on-mqtt-support
[lnk-azure-protocol-gateway]: iot-hub-protocol-gateway.md

[lnk-devices]: https://catalog.azureiotsuite.com/
[lnk-protocols]: iot-hub-protocol-gateway.md
[lnk-compare]: iot-hub-compare-event-hubs.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

[lnk-methods]: iot-hub-devguide-direct-methods.md
[lnk-messaging]: iot-hub-devguide-messaging.md

[lnk-quotas]: iot-hub-devguide-quotas-throttling.md
[lnk-devguide-twin-reconnection]: iot-hub-devguide-device-twins.md#device-reconnection-flow
[lnk-devguide-twin]: iot-hub-devguide-device-twins.md


<!--HONumber=Nov16_HO5-->


