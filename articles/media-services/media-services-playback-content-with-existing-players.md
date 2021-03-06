---
title: "Reprodução de conteúdo | Microsoft Docs"
description: "Este tópico lista os players existentes que você pode usar para reproduzir conteúdo."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 7e9fcf89-0fb6-4fa4-96cb-666320684d69
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/12/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1f7e9b0c1a3bfc53153900ed353ba8805fff075e


---
# <a name="playing-your-content-with-existing-players"></a>Reprodução de seu conteúdo com players existentes
Os serviços de mídia do Microsoft Azure suporta muitos formatos populares de streaming, como Smooth Streaming, HTTP Live Streaming e MPEG-Dash. Este tópico aponta para players existentes que você pode usar para testar os fluxos.

> [!NOTE]
> Para reproduzir conteúdo dinamicamente empacotado ou criptografado dinamicamente, certifique-se de obter pelo menos uma unidade de transmissão para o ponto de extremidade de streaming por meio do qual você planeja distribuir seu conteúdo. Para obter informações sobre unidades de streaming de dimensionamento, consulte: [Como dimensionar unidades de streaming](media-services-portal-manage-streaming-endpoints.md).
> 
> 

### <a name="the-azure-portal-media-services-content-player"></a>Player de conteúdo dos Serviços de Mídia do Portal do Azure
O portal do **Azure** fornece um player de conteúdo que você pode usar para testar o vídeo.

Clique no vídeo desejado (verifique se ele foi [publicado](media-services-portal-publish.md)) e clique no botão **Reproduzir** na parte inferior do portal.

Algumas considerações se aplicam:

* O **PLAYER DE CONTEÚDO DOS SERVIÇOS DE MÍDIA** é reproduzido por meio do ponto de extremidade de streaming padrão. Se você deseja reproduzir por meio de um ponto de extremidade de streaming não padrão, use outro reprodutor. Por exemplo, [Azure Media Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

![AMSPlayer][AMSPlayer]

### <a name="azure-media-player"></a>Azure Media Player
Use o [Media Player do Azure](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para reproduzir conteúdo (protegido ou não) em qualquer um dos seguintes formatos:

* Smooth Streaming
* MPEG DASH
* HLS
* MP4 progressivo

### <a name="flash-player"></a>Flash Player
#### <a name="aes-encrypted-with-token"></a>Criptografado com AES com token
[http://aestoken.azurewebsites.net](http://aestoken.azurewebsites.net)

### <a name="silverlight-players"></a>Players Silverlight
#### <a name="monitoring"></a>Monitoramento
[http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor)

#### <a name="playready-with-token"></a>PlayReady com token
[http://sltoken.azurewebsites.net](http://sltoken.azurewebsites.net)

### <a name="dash-players"></a>Players DASH
[http://dashplayer.azurewebsites.net](http://dashplayer.azurewebsites.net)

[http://dashif.org](http://dashif.org)

### <a name="other"></a>Outros
Para testar as URLs de HLS, você também pode usar:

* **Safari** em um dispositivo iOS ou
* **Player 3ivx HLS** no Windows.

## <a name="developing-video-players"></a>Desenvolvendo players de vídeo
Para obter informações sobre como desenvolver seus próprios players, consulte [Desenvolvendo players de vídeo](media-services-develop-video-players.md)

## <a name="media-services-learning-paths"></a>Roteiros de aprendizagem dos Serviços de Mídia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fornecer comentários
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[AMSPlayer]: ./media/media-services-playback-content-with-existing-players/media-services-portal-player.png



<!--HONumber=Nov16_HO3-->


