---
title: "Integração do Android SDK para Azure Mobile Engagement"
description: Descreve como integrar o SDK do Azure Mobile Engagement em aplicativos Android
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: a91ed04f-f3ce-4692-a6dd-b56a28d7dee8
ms.service: mobile-engagement
ms.workload: mobile
ms.tgt_pltfrm: mobile-android
ms.devlang: Java
ms.topic: article
ms.date: 08/12/2016
ms.author: piyushjo;ricksal
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 2b89a13230439698854656f0bb5367f1ed35ce1c


---
# <a name="android-sdk-integration-for-azure-mobile-engagement"></a>Integração do Android SDK para Azure Mobile Engagement
> [!div class="op_single_selector"]
> * [Universal do Windows](mobile-engagement-windows-store-sdk-overview.md)
> * [Windows Phone Silverlight](mobile-engagement-windows-phone-sdk-overview.md)
> * [iOS](mobile-engagement-ios-sdk-overview.md)
> * [Android](mobile-engagement-android-sdk-overview.md)
> 
> 

Este documento descreve todas as opções de integração e configuração disponíveis para o SDK do Azure Mobile Engagement para Android.

## <a name="prerequisites"></a>Pré-requisitos
[!INCLUDE [Prereqs](../../includes/mobile-engagement-android-prereqs.md)]

## <a name="advanced-features"></a>Recursos avançados
### <a name="reporting-features"></a>Recursos de relatório
Você pode adicionar esses recursos:

1. [Opções de relatório avançadas](mobile-engagement-android-advanced-reporting.md)
2. [Opções de relatório de localização](mobile-engagement-android-location-reporting.md)
3. [Opções de configuração avançada](mobile-engagement-android-advanced-configuration.md)

### <a name="notifications"></a>Notificações:
[Como integrar o Reach (notificações) em seu aplicativo Android](mobile-engagement-android-integrate-engagement-reach.md)

1. GCM (Google Cloud Messaging): [como integrar o GCM com o Mobile Engagement](mobile-engagement-android-gcm-integrate.md)
2. ADM (Amazon Device Messaging): [como integrar o ADM com o Mobile Engagement](mobile-engagement-android-adm-integrate.md)

### <a name="tag-plan-implementation"></a>Implementação do plano de marca:
[Como usar a API de marcação avançada do Mobile Engagement em seu aplicativo Android](mobile-engagement-android-use-engagement-api.md)

## <a name="release-notes"></a>Notas de versão
### <a name="423-08102016"></a>4.2.3 (08/10/2016)
* Nenhum outro bloqueio de WIFI.
* Corrige um deadlock ao chamar getDeviceId antes de init (bug introduzido na versão 4.2.0).

Para todas as versões, consulte as [notas de versão completas](mobile-engagement-android-release-notes.md).

## <a name="upgrade-procedures"></a>Procedimentos de atualização
Se você já tiver integrado uma versão mais antiga do nosso SDK em seu aplicativo, consulte os [Procedimentos de atualização](mobile-engagement-android-upgrade-procedure.md).




<!--HONumber=Nov16_HO3-->


