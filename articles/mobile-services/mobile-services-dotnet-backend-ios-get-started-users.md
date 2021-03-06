---
title: Adicionar autenticação ao aplicativo de Serviços Móveis do Azure (iOS) existente | Microsoft Docs
description: Aprenda a usar os serviços móveis para autenticar usuários de seu aplicativo iOS por meio de uma variedade de provedores de identidade, incluindo Google, Facebook, Twitter e Microsoft.
services: mobile-services
documentationcenter: ios
author: krisragh
manager: erikre
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-ios
ms.devlang: objective-c
ms.topic: article
ms.date: 07/21/2016
ms.author: krisragh

---
# Adicionar autenticação ao aplicativo Serviços Móveis do Azure existente
[!INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

> Para a versão de aplicativos móveis equivalente deste tópico, consulte [Adicionar autenticação ao seu aplicativo iOS](../app-service-mobile/app-service-mobile-ios-get-started-users.md).
> 
> 

Neste tutorial, você adiciona a autenticação ao projeto de Início Rápido usando um provedor de identidade com suporte. Este tutorial se baseia no [tutorial de Início Rápido dos Serviços Móveis], que você deve concluir primeiro.

## <a name="register"></a>Registrar seu aplicativo para a autenticação e configurar os Serviços Móveis
[!INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[!INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Restringir permissões a usuários autenticados
[!INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

No Xcode, abra o projeto. Pressione o botão **Executar** para iniciar o aplicativo. Verifique se uma exceção com um código de status 401 (Não autorizado) é gerada após o aplicativo ser iniciado. Isso acontece porque o aplicativo tenta acessar os Serviços Móveis como um usuário não autenticado, mas a tabela *TodoItem* agora exige autenticação.

## <a name="add-authentication"></a>Adicionar autenticação ao aplicativo
[!INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

## <a name="store-authentication"></a>Armazenar tokens de autenticação no aplicativo
[!INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Próximas etapas
No próximo tutorial, [Autorização de serviço de usuários dos Serviços Móveis], você fornecerá ao usuário o valor da ID de usuário para filtrar os dados retornados.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Autorização de serviço de usuários dos Serviços Móveis]: mobile-services-dotnet-backend-service-side-authorization.md
[tutorial de Início Rápido dos Serviços Móveis]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0727_2016-->