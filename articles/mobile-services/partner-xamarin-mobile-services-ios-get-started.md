---
title: Introdução aos Serviços Móveis para aplicativos iOS do Xamarin | Microsoft Docs
description: Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento do Xamarin iOS.
services: mobile-services
documentationcenter: xamarin
author: conceptdev
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-ios
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 07/21/2016
ms.author: craig.dunn@xamarin.com

---
# <a name="getting-started"></a>Introdução aos Serviços Móveis
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para obter a versão de Aplicativos Móveis equivalente a este tópico, veja [Criar um Aplicativo Xamarin.iOS](../app-service-mobile/app-service-mobile-xamarin-ios-get-started.md).
> 
> 

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin.iOS usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel.

Se você preferir assistir a um vídeo, o clipe abaixo segue as mesmas etapas deste tutorial.

Vídeo: “Introdução aos Serviços Móveis do Xamarin e do Azure" com Craig Dunn, desenvolvedor evangelista para Xamarin (duração: 10:05 min)

> [!VIDEO https://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Getting-Started-with-Xamarin-and-Windows-Azure-Mobile-Services/player]
> 
> 

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial exige o XCode e o Xamarin Studio para OS X ou o Visual Studio no Windows com um Mac em rede. Encontre instruções de instalação completas em [Instalar e configurar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx),

> [!IMPORTANT]
> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
> 
> 

## <a name="create-new-service"> </a>Criar um novo serviço móvel
[!INCLUDE [mobile-services-create-new-service](../../includes/mobile-services-create-new-service.md)]

## Criar um novo aplicativo do Xamarin.iOS
Depois de criar seu serviço móvel, você poderá seguir um início rápido fácil no portal clássico do Azure para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você criará um novo aplicativo Xamarin.iOS que está conectado ao seu serviço móvel.

1. No [portal clássico do Azure], clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.
2. Na guia de início rápido, clique em **Xamarin.iOS** sob **Escolher Plataforma** e expanda **Criar um novo aplicativo Xamarin.iOS**.
   
    ![][6]
   
    Isso exibe as três etapas fáceis de criar um aplicativo Xamarin.iOS conectado a seu serviço móvel.
   
      ![][7]
3. Se ainda não tiver feito isso, baixe e instale o Xcode (recomendamos a versão mais recente, Xcode 6.0, ou mais recente) e o [Xamarin Studio].
4. Clique em **Criar tabela TodoItems** para criar uma tabela para armazenar dados de aplicativo.
5. Em **Baixe e execute o aplicativo**, clique em **Baixar**.
   
    Isso baixa o projeto para o aplicativo *To do list* que está conectado a seu serviço móvel e faz referência ao componente dos Serviços Móveis do Azure para Xamarin.iOS. Salve o arquivo de projeto compactado em seu computador local e anote onde ele foi salvo.

## Executar seu novo aplicativo Xamarin.iOS
O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue até o local onde você salvou os arquivos compactados do projeto, expanda os arquivos em seu computador e abra o arquivo da solução **XamarinTodoQuickStart.iOS.sln** usando o Xamarin Studio ou o Visual Studio.
   
    ![][8]
   
    ![][9]
2. Pressione o botão **Executar** para criar o projeto e iniciar o aplicativo no emulador do iPhone, que é o padrão para este projeto.
3. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique no ícone de adição (**+**).
   
    ![][10]
   
    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.
   
   > [!NOTE]
   > Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados que estão localizados no arquivo TodoService.cs C#.
   > 
   > 
4. Novamente no [portal clássico do Azure], clique na guia **Dados** e clique na tabela **TodoItems**.
   
    ![][11]
   
    Isso permite que você procure os dados inseridos pelo aplicativo na tabela.
   
    ![][12]

## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

* [Introdução à sincronização de dados offline] Saiba como o início rápido usa dados offline para tornar o seu aplicativo responsivo e robusto.
* [Introdução à autenticação] Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.
* [Introdução às notificações por push] Saiba como enviar uma notificação por push bastante básica a seu aplicativo.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Define the mobile service instance]: #define-mobile-service-instance
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-completed-ios.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-portal-quickstart-xamarin-ios.png
[7]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-steps-xamarin-ios.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-xs.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-xamarin-project-ios-vs.png
[10]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-quickstart-startup-ios.png
[11]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-tab.png
[12]: ./media/partner-xamarin-mobile-services-ios-get-started/mobile-data-browse.png


<!-- URLs. -->
[Introdução à sincronização de dados offline]: mobile-services-xamarin-ios-get-started-offline-data.md
[Introdução à autenticação]: partner-xamarin-mobile-services-ios-get-started-users.md
[Introdução às notificações por push]: partner-xamarin-mobile-services-ios-get-started-push.md

[Xamarin Studio]: http://xamarin.com/download
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533

[portal clássico do Azure]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0727_2016-->