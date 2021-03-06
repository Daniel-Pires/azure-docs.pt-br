---
title: Como usar as filas do Barramento de Serviço com o Ruby | Microsoft Docs
description: Aprenda a usar as filas do barramento de serviço no Azure. Exemplos de códigos escritos em Ruby.
services: service-bus
documentationcenter: ruby
author: sethmanheim
manager: timlt
editor: ''

ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ruby
ms.topic: article
ms.date: 10/04/2016
ms.author: sethm

---
# <a name="how-to-use-service-bus-queues"></a>Como usar filas do Barramento de Serviço
[!INCLUDE [service-bus-selector-queues](../../includes/service-bus-selector-queues.md)]

Este guia descreve como usar as filas do barramento de serviço. Os exemplos são gravados no Ruby e usam a gema do Azure. Os cenários cobertos incluem **criar filas, enviar e receber mensagens** e **excluir filas**. Para saber mais sobre filas de Barramento de Serviços, confira a seção [Próximas etapas](#next-steps).

## <a name="what-are-service-bus-queues?"></a>O que são as filas do Barramento de Serviço?
Filas do Barramento de Serviço dão suporte a um modelo de comunicação de *sistema de mensagens agenciado*. Com filas, os componentes de um aplicativo distribuído não se comunicam diretamente uns com os outros, mas trocam mensagens por meio de uma fila, que atua como um intermediário. Um produtor de mensagem (remetente) transmite uma mensagem para a fila e, em seguida, continua o processamento.
De forma assíncrona, um consumidor de mensagem (receptor) recebe a mensagem da fila e a processa. O produtor não precisa esperar por uma resposta do consumidor a fim de continuar a processar e enviar mais mensagens. As filas oferecem entrega de mensagem do tipo **FIFO (primeiro a entrar, primeiro a sair)** para um ou mais consumidores concorrentes. Ou seja, as mensagens são normalmente recebidas e processadas pelos receptores na ordem em que foram adicionadas à fila, sendo que cada mensagem é recebida e processada por apenas um consumidor de mensagem.

![Conceitos de fila](./media/service-bus-ruby-how-to-use-queues/sb-queues-08.png)

Filas do Barramento de Serviço são uma tecnologia de uso geral que pode ser usada para uma grande variedade de cenários:

* Comunicação entre as funções Web e de trabalho em um [aplicativo multicamada do Azure](service-bus-dotnet-multi-tier-app-using-service-bus-queues.md).
* Comunicação entre aplicativos locais e aplicativos hospedados pelo Azure em uma [solução híbrida](../service-bus-relay/service-bus-dotnet-hybrid-app-using-service-bus-relay.md).
* Comunicação entre os componentes de um aplicativo distribuído executado localmente em diferentes organizações ou departamentos de uma organização.

O uso de filas permite uma maneira melhor de você escalar horizontalmente seus aplicativos e concede mais resiliência à sua arquitetura.

## <a name="create-a-namespace"></a>Criar um namespace
Para começar a usar as filas do Barramento de Serviço no Azure, primeiro é necessário criar um namespace. Um namespace fornece um contêiner de escopo para endereçar recursos do barramento de serviço dentro de seu aplicativo. Você deve criar o namespace através da interface de linha de comando porque o portal do Azure não cria o namespace com uma conexão do ACS.

Para criar um namespace:

1. Abra uma janela de console do PowerShell do Azure.
2. Digite o comando a seguir para criar um namespace do Barramento de Serviço. Forneça seu próprio valor de namespace e especifique a mesma região que a do seu aplicativo.
   
    ```
    New-AzureSBNamespace -Name 'yourexamplenamespace' -Location 'West US' -NamespaceType 'Messaging' -CreateACSNamespace $true
   
    ![Create Namespace](./media/service-bus-ruby-how-to-use-queues/showcmdcreate.png)
    ```

## <a name="obtain-management-credentials-for-the-namespace"></a>Obtenha credenciais de gerenciamento para o namespace
A fim de executar operações de gerenciamento, como criar uma fila no novo namespace, obtenha as credenciais de gerenciamento para o namespace.

O cmdlet do PowerShell que você executou para criar o namespace do barramento de serviço do Azure exibe a chave que você pode usar para gerenciar o namespace. Copie o valor de **DefaultKey**. Você usará esse valor em seu código posteriormente neste tutorial.

![Copiar chave](./media/service-bus-ruby-how-to-use-queues/defaultkey.png)

> [!NOTE]
> Você também poderá encontrar essa chave se efetuar logon no [portal do Azure](https://portal.azure.com/) e navegar até as informações de conexão para seu namespace do barramento de serviço.
> 
> 

## <a name="create-a-ruby-application"></a>Criar um aplicativo Ruby
Crie um aplicativo Ruby. Para obter instruções, confira [Criar um aplicativo Ruby no Azure (a página pode estar em inglês)](/develop/ruby/tutorials/web-app-with-linux-vm/).

## <a name="configure-your-application-to-use-service-bus"></a>Configurar seu aplicativo para usar o Barramento de serviço
Para usar o Barramento de Serviço do Azure, baixe e use o pacote do Azure do Ruby, que inclui um conjunto de bibliotecas convenientes que se comunicam com os serviços REST de armazenamento.

### <a name="use-rubygems-to-obtain-the-package"></a>Usar RubyGems para obter o pacote
1. Use uma interface de linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **Bash** (Unix).
2. Digite "gem install azure" na janela de comandos para instalar a gema e as dependências.

### <a name="import-the-package"></a>Importar o pacote
Usando seu editor de texto favorito, adicione o seguinte na parte superior do arquivo do Ruby no qual você pretende usar o armazenamento:

```
require "azure"
```

## <a name="set-up-an-azure-service-bus-connection"></a>Configurar uma conexão do barramento de serviço do Azure
O módulo do Azure lê as variáveis de ambiente **AZURE\_SERVICEBUS\_NAMESPACE** and **AZURE\_SERVICEBUS\_ACCESS_KEY** para obter as informações necessárias para se conectar ao Barramento de Serviço. Se essas variáveis de ambiente não forem definidas, você deverá especificar as informações do namespace antes de usar **Azure::ServiceBusService** com o seguinte código:

```
Azure.config.sb_namespace = "<your azure service bus namespace>"
Azure.config.sb_access_key = "<your azure service bus access key>"
```

Defina o valor de namespace para o valor que você criou, em vez de toda a URL. Por exemplo, use **"yourexamplenamespace"**, não "yourexamplenamespace.servicebus.windows.net".

## <a name="how-to-create-a-queue"></a>Como criar uma fila
O objeto**Azure::ServiceBusService** permite que você trabalhe com filas. Para criar uma fila, use o método **create_queue()**. O exemplo a seguir cria uma fila ou imprime os erros, se houver algum.

```
azure_service_bus_service = Azure::ServiceBusService.new
begin
  queue = azure_service_bus_service.create_queue("test-queue")
rescue
  puts $!
end
```

Você também pode passar em um objeto **Azure::ServiceBus::Queue** com opções adicionais, que permite a substituição de configurações padrão da fila, assim como a vida útil da mensagem ou o tamanho máximo da fila. O exemplo a seguir mostra como definir o tamanho máximo da fila para 5 GB e a vida útil para 1 minuto:

```
queue = Azure::ServiceBus::Queue.new("test-queue")
queue.max_size_in_megabytes = 5120
queue.default_message_time_to_live = "PT1M"

queue = azure_service_bus_service.create_queue(queue)
```

## <a name="how-to-send-messages-to-a-queue"></a>Como enviar mensagens para uma fila
Para enviar uma mensagem a uma fila do Barramento de Serviço, o aplicativo chama o método **send\_queue\_message()** no objeto **Azure::ServiceBusService**. Mensagens enviadas para (e recebidas das) as filas do Barramento de Serviço são objetos **Azure::ServiceBus::BrokeredMessage** e têm um conjunto de propriedades padrão (como **label** e **time\_to\_live**), um dicionário que é usado para manter propriedades personalizadas específicas ao aplicativo e um corpo de dados arbitrários do aplicativo. Um aplicativo pode definir o corpo da mensagem, passando um valor da cadeia de caracteres como a mensagem, e qualquer propriedade padrão necessária será preenchida com valores padrão.

O exemplo a seguir demonstra como enviar uma mensagem de teste à fila chamada "test-queue" que use **send\_queue\_message()**:

```
message = Azure::ServiceBus::BrokeredMessage.new("test queue message")
message.correlation_id = "test-correlation-id"
azure_service_bus_service.send_queue_message("test-queue", message)
```

As filas do Barramento de Serviço dão suporte ao tamanho máximo de mensagem de 256 KB na [camada Standard](service-bus-premium-messaging.md) e 1 MB na [camada Premium](service-bus-premium-messaging.md). O cabeçalho, que inclui as propriedades de aplicativo padrão e personalizadas, pode ter um tamanho máximo de 64 KB. Não há nenhum limite no número de mensagens mantidas em uma fila mas há uma capacidade do tamanho total das mensagens mantidas por uma fila. O tamanho da fila é definido no momento da criação, com um limite superior de 5 GB.

## <a name="how-to-receive-messages-from-a-queue"></a>Como receber mensagens de uma fila
As mensagens são recebidas de uma fila usando o método **receive\_queue\_message()** no objeto **Azure::ServiceBusService**. Por padrão, as mensagens são lidas e bloqueadas sem excluí-las da fila. Entretanto, você pode excluir mensagens da fila à medida que são lidas, definindo a opção **:peek_lock** para **false**.

O comportamento padrão faz com que a leitura e a exclusão se dividam em uma operação de dois estágios, o que também torna possível o suporte a aplicativos que não podem tolerar mensagens ausentes. Quando o Barramento de Serviço recebe uma solicitação, ele encontra a próxima mensagem a ser consumida, a bloqueia para evitar que outros clientes a recebam e a retorna para o aplicativo. Depois que o aplicativo termina de processar a mensagem (ou a armazena de forma segura para um processamento futuro), ele conclui o segundo estágio do processo de recebimento, chamando o método **delete\_queue\_message()** e fornecendo a mensagem a ser excluída como um parâmetro. O método **delete\_queue\_message()** marcará a mensagem como sendo consumida e a removerá da fila.

Se o parâmetro **:peek\_lock** estiver definido como **false**, a leitura e a exclusão da mensagem se tornam o modelo mais simples e funcionam melhor em cenários nos quais o aplicativo pode tolerar o não processamento de uma mensagem em caso de falha. Para compreender isso, considere um cenário no qual o consumidor emite a solicitação de recebimento e então falha antes de processá-la. Como o Barramento de Serviço terá marcado a mensagem como sendo consumida, quando o aplicativo for reiniciado e começar a consumir mensagens novamente, ele terá perdido a mensagem que foi consumida antes da falha.

O exemplo a seguir demonstra como receber e processar mensagens usando **receive\_queue\_message()**. O exemplo primeiro recebe e exclui uma mensagem usando **: peek\_lock** definido para **false**, em seguida, ele recebe outra mensagem e exclui a mensagem usando **delete\_queue\_message()**:

```
message = azure_service_bus_service.receive_queue_message("test-queue",
  { :peek_lock => false })
message = azure_service_bus_service.receive_queue_message("test-queue")
azure_service_bus_service.delete_queue_message(message)
```

## <a name="how-to-handle-application-crashes-and-unreadable-messages"></a>Como tratar falhas do aplicativo e mensagens ilegíveis
O Barramento de Serviço proporciona funcionalidade para ajudá-lo a se recuperar normalmente dos erros no seu aplicativo ou das dificuldades no processamento de uma mensagem. Se um aplicativo receptor não puder processar a mensagem por algum motivo, ele chamará o método **unlock\_queue\_message()** no objeto **Azure::ServiceBusService**. Isso fará com que o Barramento de Serviço desbloqueie a mensagem na fila e disponibilize-a para que ela possa ser recebida novamente pelo mesmo aplicativo de consumo ou por outro.

Também há um tempo limite associado a uma mensagem bloqueada na fila e, se houver falha no processamento da mensagem pelo aplicativo da expiração do tempo limite de bloqueio (por exemplo, se o aplicativo travar), o Barramento de Serviço desbloqueará a mensagem automaticamente e a disponibilizará para ser recebida novamente.

Caso o aplicativo falhe após o processamento da mensagem, mas antes que o método **delete\_queue\_message()** seja chamado, a mensagem será fornecida novamente ao aplicativo quando reiniciar. Isso é frequentemente chamado de **Processamento de pelo menos uma vez**, ou seja, cada mensagem será processada pelo menos uma vez, mas, em algumas situações, a mesma mensagem poderá ser entregue novamente. Se o cenário não tolerar o processamento duplicado, os desenvolvedores de aplicativos deverão adicionar lógica extra ao aplicativo para tratar a entrega de mensagem duplicada. Isso geralmente é obtido com a propriedade **Message\_id** da mensagem, que permanece constante nas tentativas da entrega.

## <a name="next-steps"></a>Próximas etapas
Agora que você já sabe as noções básicas das filas de Barramento de Serviço, siga estes links para saber mais.

* Visão geral de [filas, tópicos e assinaturas](service-bus-queues-topics-subscriptions.md).
* Visite o repositório [SDK do Azure para o Ruby](https://github.com/Azure/azure-sdk-for-ruby) no GitHub.

Para fazer uma comparação entre as filas de Barramento de Serviço do Azure discutidas nesse artigo e as filas do Azure discutidas no artigo [Como usar o armazenamento de Filas no Ruby](../storage/storage-ruby-how-to-use-queue-storage.md), confira [Filas do Azure e filas de Barramento de Serviço do Azure - Comparadas e Contrastadas](service-bus-azure-and-service-bus-queues-compared-contrasted.md)

<!--HONumber=Oct16_HO2-->


