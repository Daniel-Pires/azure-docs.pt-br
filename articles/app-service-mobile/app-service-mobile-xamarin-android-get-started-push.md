---
title: "Adicionar notificações por push ao aplicativo Xamarin Android | Microsoft Docs"
description: "Saiba como usar o Serviço de Aplicativo do Azure e os Hubs de notificação do Azure para enviar notificações por push para seu aplicativo Android.Xamarin"
services: app-service\mobile
documentationcenter: xamarin
author: ysxu
manager: erikre
editor: 
ms.assetid: 6f7e8517-e532-4559-9b07-874115f4c65b
ms.service: app-service-mobile
ms.workload: mobile
ms.tgt_pltfrm: mobile-xamarin-android
ms.devlang: dotnet
ms.topic: article
ms.date: 10/12/2016
ms.author: yuaxu
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c9f8c46fa5fba1e69382a383ff94700de07db4ab


---
# <a name="add-push-notifications-to-your-xamarinandroid-app"></a>Adicionar notificações por push ao aplicativo Xamarin.Android
[!INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## <a name="overview"></a>Visão geral
Neste tutorial, você adicionará notificações por push ao projeto de [Início rápido do Xamarin.Android](app-service-mobile-windows-store-dotnet-get-started.md) de forma que sempre que um registro for inserido, uma notificação por push seja enviada.

Se você não usar o projeto baixado do início rápido do servidor, deve adicionar o pacote de extensão de notificação por push ao seu projeto. Confira [Trabalhar com o servidor back-end SDK do .NET para os Aplicativos Móveis do Azure](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) para obter mais informações.

## <a name="prerequisites"></a>Pré-requisitos
Este tutorial exige o seguinte:

* Uma conta ativa do Google. Você pode se inscrever em uma conta do Google em [accounts.google.com](http://go.microsoft.com/fwlink/p/?LinkId=268302).
* [Componente do cliente Google Cloud Messaging](http://components.xamarin.com/view/GCMClient/).

## <a name="a-nameconfigure-hubaconfigure-a-notification-hub"></a><a name="configure-hub"></a>Configurar um novo Hub de Notificações
[!INCLUDE [app-service-mobile-configure-notification-hub](../../includes/app-service-mobile-configure-notification-hub.md)]

## <a name="a-idregisteraenable-firebase-cloud-messaging"></a><a id="register"></a>Habilitar mensagens de nuvem Firebase
[!INCLUDE [notification-hubs-enable-firebase-cloud-messaging](../../includes/notification-hubs-enable-firebase-cloud-messaging.md)]

## <a name="configure-azure-to-send-push-requests"></a>Configurar o Azure para enviar notificações por push
[!INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push-for-firebase.md)]

## <a name="a-idupdate-serveraupdate-the-server-project-to-send-push-notifications"></a><a id="update-server"></a>Atualizar o projeto de servidor para enviar notificações por push
[!INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]

## <a name="a-idconfigure-appaconfigure-the-client-project-for-push-notifications"></a><a id="configure-app"></a>Configurar o projeto para notificações por push
[!INCLUDE [mobile-services-xamarin-android-push-configure-project](../../includes/mobile-services-xamarin-android-push-configure-project.md)]

## <a name="a-idadd-pushaadd-push-notifications-code-to-your-app"></a><a id="add-push"></a>Adicionar código de notificações por push ao seu aplicativo
[!INCLUDE [app-service-mobile-xamarin-android-push-add-to-app](../../includes/app-service-mobile-xamarin-android-push-add-to-app.md)]

## <a name="a-nametestatest-push-notifications-in-your-app"></a><a name="test"></a>Testar notificações por push no seu aplicativo
Você pode testar o aplicativo usando um dispositivo virtual no emulador. Há etapas de configuração adicionais exigidas durante a execução em um emulador.

1. Verifique se você está implantando ou depurando em um dispositivo virtual com APIs do Google definidas como destino, conforme mostrado abaixo no Gerenciador de AVD (dispositivo virtual Android).
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/google-apis-avd-settings.png)
2. Adicione uma conta do Google para o dispositivo Android clicando em **Aplicativos** > **Configurações** > **Adicionar conta** e então siga os prompts.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/add-google-account.png)
3. Execute o aplicativo todolist como feito anteriormente s e insira um novo item de tarefa pendente. Dessa vez, um ícone de notificação é exibido na área de notificação. Você pode abrir a gaveta de notificações para exibir o texto completo da notificação.
   
    ![](./media/app-service-mobile-xamarin-android-get-started-push/android-notifications.png)

<!-- URLs. -->
[Início rápido do Xamarin.Android]: app-service-mobile-xamarin-android-get-started.md
[Componente de mensagens de nuvem do Google]: http://components.xamarin.com/view/GCMClient/
[Componente dos Serviços Móveis do Azure]: http://components.xamarin.com/view/azure-mobile-services/



<!--HONumber=Nov16_HO3-->


