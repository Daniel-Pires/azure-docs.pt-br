---
title: Monitoramento de operações do Hub IoT
description: Uma visão geral do monitoramento de operações do Hub IoT do Azure, que permite monitorar o status das operações no Hub IoT em tempo real
services: iot-hub
documentationcenter: ''
author: nberdy
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: nberdy

---
# Introdução ao monitoramento de operações
O monitoramento das operações do Hub IoT permite monitorar o status das operações no seu Hub IoT em tempo real. O Hub IoT controla eventos em várias categorias de operações e você pode aceitar enviar eventos de uma ou mais categorias para um ponto de extremidade de seu Hub IoT para processamento. É possível monitorar os dados em busca de erros ou configurar processamento mais complexo com base nos padrões de dados.

O Hub IoT monitora cinco categorias de eventos:

* Operações de identidade do dispositivo
* Telemetria de dispositivo
* Comandos da nuvem para o dispositivo
* Conexões
* Carregamentos de arquivos

## Como habilitar o monitoramento de operações
1. Crie um Hub IoT. Você pode encontrar instruções sobre como criar um Hub IoT no guia de [Introdução][lnk-get-started].
2. Abra a folha do seu Hub IoT. Nela, clique em **Monitoramento de operações**.
   
    ![][1]
3. Escolha as categorias de monitoramento que deseja monitorar e clique em **Salvar**. Os eventos estão disponíveis para leitura no ponto de extremidade compatível com o Hub de Eventos listado em **Configurações de monitoramento**. O ponto de extremidade do Hub IoT chama-se `messages/operationsmonitoringevents`.
   
    ![][2]

## Categorias de evento e como usá-las
Cada categoria de monitoramento de operações rastreia um tipo diferente de interação com o Hub IoT e cada categoria de monitoramento tem um esquema que define como os eventos nessa categoria são estruturados.

### Operações de identidade do dispositivo
A categoria de operações de identidade do dispositivo rastreia erros que ocorrem quando você tenta criar, atualizar ou excluir uma entrada no registro de identidade do seu Hub IoT. O rastreamento dessa categoria é útil em cenários de provisionamento.

    {
        "time": "UTC timestamp",
         "operationName": "create",
         "category": "DeviceIdentityOperations",
         "level": "Error",
         "statusCode": 4XX,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "durationMs": 1234,
         "userAgent": "userAgent",
         "sharedAccessPolicy": "accessPolicy"
    }

### Telemetria de dispositivo
A categoria de telemetria de dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline de telemetria. Essa categoria inclui erros que ocorrem no envio de eventos de telemetria (como limitação) e no recebimento de eventos de telemetria (como leitor não autorizado). Observe que essa categoria não pode capturar erros causados pelo código em execução no próprio dispositivo.

    {
         "messageSizeInBytes": 1234,
         "batching": 0,
         "protocol": "Amqp",
         "authType": "{"scope":"device","type":"sas","issuer":"iothub"}",
         "time": "UTC timestamp",
         "operationName": "ingress",
         "category": "DeviceTelemetry",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": "UTC timestamp"
    }

### Comandos da nuvem para o dispositivo
A categoria de comandos da nuvem para o dispositivo rastreia erros que ocorrem no Hub IoT e estão relacionados ao pipeline do comando do dispositivo. Essa categoria inclui erros que ocorrem no envio de comandos (como remetente não autorizado), no recebimento de comandos (como contagem de entrega excedida) e no recebimento de comentários de comando (como comentário expirado). Essa categoria não captura erros de um dispositivo que manipula incorretamente um comando se o comando foi entregue com êxito.

    {
         "messageSizeInBytes": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "deliveryAcknowledgement": 0,
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "C2DCommands",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "EventProcessedUtcTime": "UTC timestamp",
         "PartitionId": 1,
         "EventEnqueuedUtcTime": “UTC timestamp"
    }

### Conexões
A categoria de conexões rastreia erros que ocorrem quando os dispositivos se conectam a um Hub IoT ou se desconectam dele. Rastrear essa categoria é útil para identificar tentativas de conexão não autorizada e para saber quando dispositivos em áreas de conectividade ruim perdem conexão.

    {
         "durationMs": 1234,
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "protocol": "Amqp",
         "time": " UTC timestamp",
         "operationName": "deviceConnect",
         "category": "Connections",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID"
    }

### Carregamentos de arquivos
A categoria de carregamento de arquivo rastreia erros que ocorrem no Hub IoT e estão relacionados à funcionalidade de carregamento de arquivo. Essa categoria inclui erros que ocorrem com o URI de SAS (por exemplo, quando ele expirar antes de um dispositivo notificar o hub de um carregamento concluído), em carregamentos com falha relatados pelo dispositivo e quando um arquivo não é encontrado no armazenamento durante a criação de mensagem de notificação do Hub IoT. Observe que essa categoria não pode capturar erros que ocorrem diretamente enquanto o dispositivo estiver carregando um arquivo no armazenamento.

    {
         "authType": "{"scope":"hub","type":"sas","issuer":"iothub"}",
         "protocol": "HTTP",
         "time": " UTC timestamp",
         "operationName": "ingress",
         "category": "fileUpload",
         "level": "Error",
         "statusCode": 4XX,
         "statusType": 4XX001,
         "statusDescription": "MessageDescription",
         "deviceId": "device-ID",
         "blobUri": "http//bloburi.com",
         "durationMs": 1234
    }

## Próximas etapas
Agora que você já viu uma visão geral do monitoramento de operações, confira [Gerenciar o acesso ao Hub IoT][lnk-itpro] para obter informações adicionais sobre o gerenciamento de Hub IoT.

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Projetar sua solução][lnk-design]
* [Guia do desenvolvedor][lnk-devguide]
* [Explorar o gerenciamento de dispositivo usando a interface do usuário de exemplo][lnk-dmui]
* [Simular um dispositivo com o SDK do Gateway][lnk-gateway]

<!-- Links and images -->
[1]: media/iot-hub-operations-monitoring/enable-OM-1.png
[2]: media/iot-hub-operations-monitoring/enable-OM-2.png

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[lnk-diagnostic-metrics]: iot-hub-metrics.md
[lnk-scaling]: iot-hub-scaling.md
[lnk-dr]: iot-hub-ha-dr.md

[lnk-itpro]: iot-hub-itpro-info.md

[lnk-design]: iot-hub-guidance.md
[lnk-devguide]: iot-hub-devguide.md
[lnk-dmui]: iot-hub-device-management-ui-sample.md
[lnk-gateway]: iot-hub-linux-gateway-sdk-simulated-device.md

<!---HONumber=AcomDC_0817_2016-->