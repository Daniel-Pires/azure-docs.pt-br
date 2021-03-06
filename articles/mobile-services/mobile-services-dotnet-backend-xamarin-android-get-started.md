---
title: Introdução aos Serviços Móveis para aplicativos Xamarin Android | Microsoft Docs
description: Siga este tutorial para começar a usar os serviços móveis do Azure para desenvolvimento Android Xamarin
services: mobile-services
documentationcenter: xamarin
author: lindydonna
manager: dwrede
editor: mollybos

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: get-started-article
ms.date: 07/21/2016
ms.author: donnam

---
# <a name="getting-started"></a>Introdução aos Serviços Móveis
[!INCLUDE [mobile-services-selector-get-started](../../includes/mobile-services-selector-get-started.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para obter a versão de Aplicativos Móveis equivalente a este tópico, veja [Criar um aplicativo Xamarin.Android](../app-service-mobile/app-service-mobile-xamarin-android-get-started.md).
> 
> 

Este tutorial mostra como adicionar um serviço de back-end baseado em nuvem a um aplicativo Xamarin Android usando os Serviços Móveis do Azure. Neste tutorial, você criará um novo serviço móvel e um aplicativo simples *To do list* que armazena dados do aplicativo no novo serviço móvel. O serviço móvel que você criará usa as linguagens do .NET com suporte usando o Visual Studio para a lógica de negócios do lado do servidor e para gerenciar o serviço móvel. Para criar um serviço móvel que permita que você escreva a lógica de negócios do lado do servidor em JavaScript, consulte [Versão de back-end do JavaScript] neste tópico.

> [!NOTE]
> Este tópico mostra como criar um novo projeto de serviço móvel usando o portal clássico do Azure. Usando o Visual Studio 2013 Update 2, você também pode adicionar um novo projeto de serviço móvel em uma solução existente do Visual Studio. Para saber mais, confira [Início rápido: adicionar um serviço móvel (back-end do .NET)](http://msdn.microsoft.com/library/windows/apps/dn629482.aspx)
> 
> 

Uma captura de tela do aplicativo completo está disponível abaixo:

![][0]

A conclusão deste tutorial é um pré-requisito para todos os outros tutoriais de Serviços Móveis para aplicativos Xamarin Android.

> [!NOTE]
> Para concluir este tutorial, você precisa de uma conta do Azure. Se você não tem uma conta, você pode se inscrever para uma avaliação do Azure e obter até 10 serviços móveis gratuitos que você pode continuar usando mesmo depois do fim de sua avaliação. Para obter detalhes, consulte [Avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fpt-BR%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-xamarin-android-get-started). Este tutorial requer o [Visual Studio Professional 2013](https://go.microsoft.com/fwLink/p/?LinkID=257546). Uma versão de avaliação gratuita está disponível.
> 
> 

## Criar um novo serviço móvel
[!INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

## Criar um novo aplicativo Xamarin Android
Depois de criar seu serviço móvel, você poderá seguir um início rápido fácil no portal clássico para criar um novo aplicativo ou modificar um aplicativo existente para conexão a seu serviço móvel.

Nesta seção você baixará um novo aplicativo Xamarin Android e um projeto de serviço para seu serviço móvel.

1. Se você ainda não fez isso, instale o Visual Studio com Xamarin. Encontre instruções em [Instalar e configurar para Visual Studio e Xamarin](https://msdn.microsoft.com/library/mt613162.aspx). Você também pode usar o Xamarin Studio em um computador com Mac OS X. Para isso, confira [Configuração, instalação e verificações para usuários do Mac](https://msdn.microsoft.com/library/mt488770.aspx).
2. No [Portal Clássico], clique em **Serviços Móveis** e clique no serviço móvel que você acabou de criar.
3. Na guia de início rápido, clique em **Xamarin** em **Escolher plataforma** e expanda **Criar um novo aplicativo Xamarin**.
   
       ![][6]
   
       Isso exibe as três etapas fáceis para criar um aplicativo Xamarin Android conectado ao seu serviço móvel.
   
      ![][7]
4. Em **Baixar e publicar seu serviço para nuvem**, selecione **Android** e clique em **Baixar**.
   
      Isso baixa uma solução que contém projetos para o serviço móvel e para o aplicativo de exemplo *Lista de tarefas pendentes* que está conectado ao seu serviço móvel. Salve o arquivo do projeto compactado em seu computador local e anote onde ele foi salvo.
5. Baixe seu perfil de publicação, salve o arquivo baixado no computador local e anote o local onde o salvou.

## Testar o serviço móvel
[!INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

## Publicar o serviço móvel
[!INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

## Executar o aplicativo Xamarin Android
O estágio final deste tutorial é compilar e executar seu novo aplicativo.

1. Navegue para o projeto cliente dentro da solução de serviço móvel, no Visual Studio ou no Xamarin Studio.
2. Pressione o botão **Executar** para compilar o projeto e iniciar o aplicativo. Você será solicitado a selecionar um emulador ou um dispositivo USB conectado.
   
   > [!NOTE]
   > Para que seja possível executar o projeto no emulador do Android, você deve definir no mínimo um AVD (Dispositivo Virtual Android). Use o Gerenciador do AVD para criar e gerenciar esses dispositivos.
   > 
   > 
3. No aplicativo, digite um texto significativo, como *Concluir o tutorial* e clique no ícone de adição (**+**).
   
    ![][10]
   
    Isso envia uma solicitação de POST para o novo serviço móvel hospedado no Azure. Os dados da solicitação são inseridos na tabela TodoItem. Os itens armazenados na tabela são retornados pelo serviço móvel, e os dados são exibidos na lista.
   
   > [!NOTE]
   > Você pode examinar o código que acessa o serviço móvel para consultar e inserir dados que estão localizados no arquivo ToDoActivity.cs C#.
   > 
   > 

## Próximas etapas
Agora que você concluiu o início rápido, aprenda a executar tarefas adicionais importantes nos Serviços Móveis:

* [Introdução à sincronização de dados offline] <br/>Saiba como o início rápido usa dados offline para tornar o seu aplicativo responsivo e robusto.
* [Introdução à autenticação] <br/>Saiba como autenticar usuários de seu aplicativo com um provedor de identidade.
* [Introdução às notificações por push] <br/>Saiba como enviar uma notificação por push bastante básica a seu aplicativo.
* [Solucionar problemas de back-end do .NET dos Serviços Móveis] <br/> Saiba como diagnosticar e corrigir problemas que podem surgir com o back-end do .NET dos Serviços Móveis.

[!INCLUDE [app-service-disqus-feedback-slug](../../includes/app-service-disqus-feedback-slug.md)]

<!-- Anchors. -->
[Getting started with Mobile Services]: #getting-started
[Create a new mobile service]: #create-new-service
[Next Steps]: #next-steps



<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-completed-android.png
[6]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-portal-quickstart-xamarin.png
[7]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-steps-xamarin-android.png
[8]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-vs.png
[9]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-xamarin-project-android-xs.png
[10]: ./media/mobile-services-dotnet-backend-xamarin-android-get-started/mobile-quickstart-startup-android.png

<!-- URLs. -->
[Introdução à sincronização de dados offline]: mobile-services-xamarin-android-get-started-offline-data.md
[Introdução à autenticação]: mobile-services-dotnet-backend-xamarin-android-get-started-users.md
[Introdução às notificações por push]: mobile-services-dotnet-backend-xamarin-android-get-started-push.md
[Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[JavaScript and HTML]: mobile-services-win8-javascript/
[Azure classic portal]: https://manage.windowsazure.com/
[Portal Clássico]: https://manage.windowsazure.com/
[Versão de back-end do JavaScript]: mobile-services-android-get-started.md
[Solucionar problemas de back-end do .NET dos Serviços Móveis]: mobile-services-dotnet-backend-how-to-troubleshoot.md

<!---HONumber=AcomDC_0727_2016-->