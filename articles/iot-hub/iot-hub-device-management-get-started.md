---
title: Introdução ao gerenciamento de dispositivos de Hub IoT | Microsoft Docs
description: Tutorial de introdução ao Hub IoT do Azure para gerenciamento de dispositivo com C#. Use o Hub IoT do Azure e C# com os SDKs de IoT do Microsoft Azure para implementar o gerenciamento de dispositivo.
services: iot-hub
documentationcenter: .net
author: juanjperez
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: dotnet
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/11/2016
ms.author: juanpere

---
# Introdução ao gerenciamento de dispositivos do Hub IoT do Azure usando C# (visualização)
[!INCLUDE [iot-hub-device-management-get-started-selector](../../includes/iot-hub-device-management-get-started-selector.md)]

## Introdução
Para começar com o gerenciamento de dispositivos de Hub IoT do Azure, você terá de criar um Hub IoT do Azure, provisionar dispositivos no Hub IoT e iniciar vários dispositivos simulados, além de exibir esses dispositivos na interface do usuário de exemplo do gerenciamento de dispositivo. Este tutorial apresenta e explica as etapas a seguir.

> [!NOTE]
> Você precisará criar um novo Hub IoT para habilitar recursos de gerenciamento de dispositivo mesmo se tiver um Hub IoT existente, pois os Hubs IoT existentes ainda não têm esses recursos. Depois que o gerenciamento de dispositivos fica disponível, todos os Hubs IoT existentes serão atualizados para obter recursos de gerenciamento de dispositivo.
> 
> 

## Pré-requisitos
Este tutorial presume que você esteja usando uma máquina de desenvolvimento do Windows.

Você precisa ter os seguintes itens instalados para concluir as etapas:

* Microsoft Visual Studio 2015
* Git
* CMake (versão 2.8 ou posterior). Instale o CMake de <https://cmake.org/download/>. Para um PC com Windows, escolha a opção do Windows Installer (.msi). Não deixe de marcar a caixa para adicionar o CMake à variável PATH do usuário atual.
* Node. js 6.1.0 ou superior. Instalar o Node.js para sua plataforma de <https://nodejs.org/>.
* Uma assinatura ativa do Azure. Se você não tiver uma conta, poderá criar uma conta de avaliação gratuita em apenas alguns minutos. Para obter detalhes, consulte [Avaliação gratuita do Azure][lnk-free-trial].

## Criar um Hub IoT habilitado para gerenciamento de dispositivo
Você precisa criar um Hub IoT habilitado para gerenciamento de dispositivo ao qual seus dispositivos simulados possam se conectar. As etapas a seguir mostram como concluir esta tarefa usando o portal do Azure.

1. Entre no [Portal do Azure].
2. Na barra de navegação, clique em **Novo**, em seguida em **Internet das Coisas** e em **Hub IoT do Azure**.
   
   ![][img-new-hub]
3. Na folha **Hub IoT**, escolha a configuração para o hub IoT.
   
   ![][img-configure-hub]
   
   * Na caixa **Nome**, insira um nome para identificar seu hub IoT. Se o **Nome** for válido e se estiver disponível, aparecerá uma marca de seleção verde na caixa **Nome**.
   * Selecione um **Tipo de preço e de dimensionamento**. Este tutorial não requer uma camada específica.
   * No **Grupo de recursos**, crie um novo grupo de recursos ou selecione um existente. Para obter mais informações, consulte [Usando os grupos de recursos para gerenciar seus recursos do Azure].
   * Marque a caixa para **Habilitar o Gerenciamento de Dispositivos**.
   * Em **Local**, selecione o local para hospedar o hub IoT. O gerenciamento de dispositivos do Hub IoT só está disponível no Leste dos EUA, Europa Setentrional e Ásia Oriental durante a visualização pública. No futuro, ele estará disponível em todas as regiões.
   
   > [!NOTE]
   > Se você não marcar a caixa para **Ativar o Gerenciamento de Dispositivo**, os exemplos não funcionarão.<br/>Ao marcar **Ativar o Gerenciamento de Dispositivo**, você criará uma visualização do Hub IoT com suporte apenas no Leste dos EUA, na Europa Setentrional e na Ásia Oriental e ela não se destinará a cenários de produção. Você não pode migrar dispositivos dentro e fora de hubs com gerenciamento de dispositivo habilitado.
   > 
   > 
4. Depois de escolher as opções de configuração do Hub IoT, clique em **Criar**. O Azure poderá demorar alguns minutos para criar seu Hub IoT. Para verificar o status, você pode monitorar o progresso no **Quadro Inicial** ou no painel **Notificações**.
   
   ![][img-monitor]
5. Quando o Hub IoT tiver sido criado com êxito, abra a folha do novo Hub IoT, anote o **Nome do host** e clique em **Políticas de acesso compartilhado**.
   
   ![][img-keys]
6. Clique na política **iothubowner**, copie e anote a cadeia de conexão na folha **iothubowner**. Copie-o em um local que você pode acessar posteriormente, pois precisará dele para concluir o restante deste tutorial.
   
   > [!NOTE]
   > Em cenários de produção, evite usar as credenciais **iothubowner**.
   > 
   > 
   
   ![][img-connection]

Você criou um Hub IoT habilitado para gerenciamento de dispositivo. Você precisa da cadeia de conexão para concluir o restante deste tutorial.

## Compilar os exemplos e provisionar dispositivos em seu Hub IoT
Nesta seção, você executa um script que cria o dispositivo simulado e os exemplos e provisiona um conjunto de novas identidades de dispositivo no registro do dispositivo de seu Hub IoT. Um dispositivo somente poderá se conectar ao Hub IoT se tiver uma entrada no registro do dispositivo.

Para criar os exemplos e provisionar dispositivos no Hub IoT, siga estas etapas:

1. Abra o **Prompt de comando do desenvolvedor para VS2015**.
2. Clone o repositório github. **Clone em um diretório que não tenha espaços.**
   
     ```
     git clone --recursive --branch dmpreview https://github.com/Azure/azure-iot-sdks.git
     ```
3. Na pasta raiz onde você clonou o repositório **azure-iot-sdks**, navegue até a pasta **\\azure-iot-sdks\\csharp\\service\\samples** e execute substituindo o valor de espaço reservado pela cadeia de conexão na seção anterior:
   
     ```
     setup.bat <IoT Hub Connection String>
     ```

Esse script faz o seguinte:

1. Executa **cmake** para criar uma solução do Visual Studio 2015 para o dispositivo simulado. Esse arquivo de projeto é **azure-iot-sdks\\csharp\\service\\samples\\cmake\\iotdm\_client\\samples\\iotdm\_simple\_sample\\iotdm\_simple\_sample.vcxproj**. Observe que os arquivos de origem estão na pasta **azure-iot-sdks\\c\\iotdm\_client\\samples\\iotdm\_simple\_sample**.
2. Compila o projeto de dispositivo simulado **iotdm\_simple\_sample.vcxproj**.
3. Cria os exemplos de gerenciamento de dispositivo **azure-iot-sdks\\csharp\\service\\samples\\GetStartedWithIoTDM\\GetStartedWithIoTDM.sln**.
4. Executa **GenerateDevices.exe** para provisionar as identidades de dispositivo em seu Hub IoT. Os dispositivos estão descritos em **sampledevices.json** (localizado na pasta **azure-iot-sdks\\node\\service\\samples**) e, depois que os dispositivos forem provisionados, as credenciais serão armazenadas no arquivo **devicecreds.txt** (localizado na pasta **azure-iot-sdks\\csharp\\service\\samples\\bin**).

## Iniciar seus dispositivos simulados
Agora que os dispositivos foram adicionados ao registro do dispositivo, você pode iniciar dispositivos gerenciados simulados. Um dispositivo simulado é iniciado para cada identidade de dispositivo provisionada no Hub IoT do Azure.

Usando o prompt de comando do desenvolvedor, na pasta **\\azure-iot-sdks\\csharp\\service\\samples\\bin**, execute:

  ```
  simulate.bat
  ```

Esse script é executado em uma instância de **iotdm\_simple\_sample.exe** para cada dispositivo listado no arquivo **devicecreds.txt**. O dispositivo simulado continua sendo executado até você fechar a janela de comando.

O aplicativo de exemplo **iotdm\_simple\_sample** é criado usando a biblioteca de cliente de gerenciamento de dispositivo de Hub IoT do Azure para C, que permite a criação de dispositivos IoT que podem ser gerenciados pelo Hub IoT do Azure. Os fabricantes de dispositivos podem usar essa biblioteca para relatar propriedades de dispositivo e implementar as ações de execução exigidas pelos trabalhos do dispositivo. Essa biblioteca é um componente fornecido como parte dos SDKs do Hub IoT de código aberto.

Quando você executa **simulate.bat**, vê um fluxo de dados na janela de saída. Essa saída mostra o tráfego de entrada e saída e as instruções **printf** nas funções de retorno de chamada específicas do aplicativo. Essa saída permite que você veja o tráfego de entrada e saída e como o aplicativo de exemplo trata os pacotes decodificados. Quando o dispositivo se conecta ao Hub IoT, o serviço começa a observar os recursos no dispositivo automaticamente. A biblioteca de cliente de mineração de dados de Hub IoT invoca os retornos de chamada do dispositivo para recuperar os valores mais recentes deste.

A seguir, vemos a saída do aplicativo de exemplo **iotdm\_simple\_sample**. Na parte superior, você vê uma mensagem **REGISTRADO** bem-sucedida mostrando o dispositivo com a Id **Device11-7ce4a850** conectando o Hub IoT.

> [!NOTE]
> Para ter uma saída menos detalhada, compile e execute a configuração comercial.
> 
> 

![][img-output]

Deixe todos os dispositivos simulados em execução enquanto você conclui as seções a seguir.

## Executar a interface de usuário do gerenciamento de dispositivos
Agora que você tem um Hub IoT e tem vários dispositivos simulados em execução e registrados para gerenciamento, poderá implantar a interface do usuário de exemplo do gerenciamento de dispositivos. A interface do usuário de exemplo do gerenciamento de dispositivos oferece um exemplo prático de como utilizar as APIs do gerenciamento de dispositivos para criar uma experiência de interface do usuário interativa. Para saber mais sobre a interface do usuário de exemplo do gerenciamento de dispositivos, incluindo [Problemas conhecidos](https://github.com/Azure/azure-iot-device-management#knownissues), confira o repositório do GitHub [Interface do usuário do gerenciamento de dispositivos IoT do Azure][lnk-dm-github].

Para recuperar, compilar e executar a interface do usuário de exemplo do gerenciamento de dispositivos, siga estas etapas:

1. Abra um **Prompt de comando**.
2. Confirme que você instalou o Node.js 6.1.0 ou superior de acordo com a seção de pré-requisitos ao digitar `node --version`.
3. Clone o repositório do GitHub da interface do usuário do gerenciamento de dispositivos IoT do Azure ao executar o seguinte comando:
   
    ```
    git clone https://github.com/Azure/azure-iot-device-management.git
    ```
4. Na pasta raiz de sua cópia clonada do repositório da interface do usuário do gerenciamento de dispositivos IoT do Azure, execute o comando a seguir para recuperar os pacotes dependentes:
   
    ```
    npm install
    ```
5. Quando o comando npm install for concluído, execute o seguinte comando para compilar o código:
   
    ```
    npm run build
    ```
6. Use um editor de texto para abrir o arquivo user-config.json na raiz da pasta clonada. Substitua o texto "&lt;SUA CADEIA DE CONEXÃO AQUI&gt;" por sua cadeia de conexão do Hub IoT da seção anterior e salve o arquivo.
7. No prompt de comando, execute o comando a seguir para iniciar o aplicativo de experiência do gerenciamento de dispositivos:
   
    ```
    npm run start
    ```
8. Quando o prompt de comando relatar "Os serviços foram iniciados", abra um navegador da Web (Edge/IE 11+/Safari/Chrome têm suporte no momento) e navegue até o aplicativo de gerenciamento de dispositivos na URL a seguir para exibir os dispositivos simulados: <http://127.0.0.1:3003>.
   
    ![][img-dm-ui]

Deixe os dispositivos simulados e o aplicativo de gerenciamento de dispositivos em execução enquanto prossegue para o próximo tutorial de gerenciamento.

## Próximas etapas
Para continuar a introdução ao Hub IoT, confira [Introdução ao SDK do Gateway][lnk-gateway-SDK].

Para saber mais sobre os recursos de gerenciamento de dispositivos do Hub IoT do Azure, confira o tutorial [Explorar o gerenciamento de dispositivos do Hub IoT do Azure usando a interface do usuário de exemplo][lnk-sample-ui].

<!-- images and links -->
[img-new-hub]: media/iot-hub-device-management-get-started/image1.png
[img-configure-hub]: media/iot-hub-device-management-get-started/image2.png
[img-monitor]: media/iot-hub-device-management-get-started/image3.png
[img-keys]: media/iot-hub-device-management-get-started/image4.png
[img-connection]: media/iot-hub-device-management-get-started/image5.png
[img-output]: media/iot-hub-device-management-get-started/image6.png
[img-dm-ui]: media/iot-hub-device-management-get-started/dmui.png

[lnk-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[Portal do Azure]: https://portal.azure.com/
[Usando os grupos de recursos para gerenciar seus recursos do Azure]: ../azure-portal/resource-group-portal.md
[lnk-dm-github]: https://github.com/Azure/azure-iot-device-management
[lnk-sample-ui]: iot-hub-device-management-ui-sample.md
[lnk-gateway-SDK]: iot-hub-linux-gateway-sdk-get-started.md

<!---HONumber=AcomDC_0817_2016-->