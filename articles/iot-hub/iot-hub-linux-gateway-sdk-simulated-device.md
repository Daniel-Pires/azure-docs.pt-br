---
title: Simular um dispositivo com o SDK do Gateway | Microsoft Docs
description: Passo a passo do SDK do Gateway do Hub IoT do Azure usando o Linux para ilustrar o envio de telemetria de um dispositivo simulado usando o SDK do Gateway do Hub IoT do Azure.
services: iot-hub
documentationcenter: ''
author: chipalost
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: cpp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/29/2016
ms.author: andbuc

---
# <a name="iot-gateway-sdk-(beta)-–-send-device-to-cloud-messages-with-a-simulated-device-using-linux"></a>SDK do Gateway IoT (beta) - enviar mensagens do dispositivo para a nuvem com um dispositivo simulado usando o Linux
[!INCLUDE [iot-hub-gateway-sdk-simulated-selector](../../includes/iot-hub-gateway-sdk-simulated-selector.md)]

## <a name="build-and-run-the-sample"></a>Criar e executar a amostra
Antes de começar, é necessário:

* [Configure seu ambiente de desenvolvimento][lnk-setupdevbox] para trabalhar com o SDK no Linux.
* [Crie um Hub IoT][lnk-create-hub] em sua assinatura do Azure. Você precisará do nome do hub para concluir este passo a passo. Se você ainda não tiver uma assinatura do Azure, será possível obter uma [conta gratuita][lnk-free-trial].
* Adicione dois dispositivos ao hub IoT e anote as IDs e chaves de dispositivo. É possível usar o [Gerenciador de Dispositivos ou a ferramenta iothub-explorer][lnk-explorer-tools] para adicionar seus dispositivos ao hub IoT criado na etapa anterior e recuperar suas chaves.

Para compilar a amostra:

1. Abra um shell.
2. Navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
3. Execute o script **tools/build.sh** . Esse script usa o utilitário **cmake** para criar uma pasta chamada **build** na pasta raiz da cópia local do repositório **azure-iot-gateway-sdk** e gerar um makefile. O script então cria a solução e executa os testes.

> [!NOTE]
> Sempre que você executa o script **build.sh**, ele exclui e recria a pasta **build** na pasta raiz da cópia local do repositório **azure-iot-gateway-sdk**.
> 
> 

Para executar a amostra:

Em um editor de texto, abra o arquivo **samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json** na cópia local do repositório **azure-iot-gateway-sdk**. Este arquivo configura os módulos no gateway de exemplo:

* O módulo **IoTHub** se conecta ao seu hub IoT. É necessário configurá-lo para enviar dados ao hub IoT. Especificamente, defina o valor de **IoTHubName** como o nome de seu hub IoT e defina o valor de **IoTHubSuffix** como **azure-devices.net**. Defina o valor de **Transporte** como um destes: "HTTP", "AMQP" ou "MQTT". Observe que, no momento, apenas "HTTP" compartilhará uma conexão TCP para todas as mensagens de dispositivo. Se você definir o valor como "AMQP" ou "MQTT", o gateway manterá uma conexão TCP separada para o Hub IoT para cada dispositivo.
* O módulo **mapping** mapeia os endereços MAC dos dispositivos simulados para as IDs de dispositivo do Hub IoT. Verifique se os valores de **deviceId** correspondem às IDs dos dois dispositivos que você adicionou ao hub IoT e se os valores de **deviceKey** contêm as chaves dos dois dispositivos.
* Os módulos **BLE1** e **BLE2** são os dispositivos simulados. Observe como os endereços MAC correspondem ao módulo **mapping** .
* O módulo **Logger** registra a atividade de gateway em um arquivo.
* Os valores de **caminho do módulo** abaixo pressupõem que você executará a amostra da raiz de sua cópia local do repositório **azure-iot-gateway-sdk**.
* A matriz **links** na parte inferior do arquivo JSON conecta os módulos **BLE1** e **BLE2** ao módulo **mapping**, e o **mapping** ao módulo **IoTHub**. Ela também garante que todas as mensagens são registradas pelo módulo **Logger** .

```
{
    "modules" :
    [ 
        {
            "module name" : "IoTHub",
            "module path" : "./build/modules/iothub/libiothub_hl.so",
            "args" : 
            {
                "IoTHubName" : "{Your IoT hub name}",
                "IoTHubSuffix" : "azure-devices.net",
                "Transport": "HTTP"
            }
        },
        {
            "module name" : "mapping",
            "module path" : "./build/modules/identitymap/libidentity_map_hl.so",
            "args" : 
            [
                {
                    "macAddress" : "01-01-01-01-01-01",
                    "deviceId"   : "{Device ID 1}",
                    "deviceKey"  : "{Device key 1}"
                },
                {
                    "macAddress" : "02-02-02-02-02-02",
                    "deviceId"   : "{Device ID 2}",
                    "deviceKey"  : "{Device key 2}"
                }
            ]
        },
        {
            "module name":"BLE1",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "01-01-01-01-01-01"
            }
        },
        {
            "module name":"BLE2",
            "module path" : "./build/modules/simulated_device/libsimulated_device_hl.so",
            "args":
            {
                "macAddress" : "02-02-02-02-02-02"
            }
        },
        {
            "module name":"Logger",
            "module path" : "./build/modules/logger/liblogger_hl.so",
            "args":
            {
                "filename":"./deviceCloudUploadGatewaylog.log"
            }
        }
    ],
    "links" : [
        { "source" : "*", "sink" : "Logger" },
        { "source" : "BLE1", "sink" : "mapping" },
        { "source" : "BLE2", "sink" : "mapping" },
        { "source" : "mapping", "sink" : "IoTHub" }
    ]
}

```

Salve todas as alterações feitas no arquivo de configuração.

Para executar a amostra:

1. No shell, navegue até a pasta raiz de sua cópia local do repositório **azure-iot-gateway-sdk** .
2. Execute o comando a seguir:
   
    ```
    ./build/samples/simulated_device_cloud_upload/simulated_device_cloud_upload_sample ./samples/simulated_device_cloud_upload/src/simulated_device_cloud_upload_lin.json
    ```
3. É possível usar o [Gerenciador de Dispositivos ou a ferramenta iothub-explorer][lnk-explorer-tools] para monitorar as mensagens que o hub IoT recebe do gateway.

## <a name="next-steps"></a>Próximas etapas
Se você quiser obter uma compreensão mais avançada do SDK do Gateway e experimentar alguns exemplos de código, visite os seguintes recursos e tutoriais para desenvolvedores:

* [Enviar mensagens do dispositivo para a nuvem de um dispositivo real com o SDK do Gateway][lnk-physical-device]
* [SDK de Gateway IoT do Azure][lnk-gateway-sdk]

Para explorar melhor as funcionalidades do Hub IoT, consulte:

* [Guia de desenvolvedor][lnk-devguide]
* [Proteger sua solução de IoT desde o início][lnk-securing]

<!-- Links -->
[lnk-setupdevbox]: https://github.com/Azure/azure-iot-gateway-sdk/blob/master/doc/devbox_setup.md
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-explorer-tools]: https://github.com/Azure/azure-iot-sdks/blob/master/doc/manage_iot_hub.md
[lnk-gateway-sdk]: https://github.com/Azure/azure-iot-gateway-sdk/

[lnk-physical-device]: iot-hub-gateway-sdk-physical-device.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-securing]: iot-hub-security-ground-up.md
[lnk-create-hub]: iot-hub-create-through-portal.md


<!--HONumber=Oct16_HO2-->


