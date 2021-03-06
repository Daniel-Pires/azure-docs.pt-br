---
title: Publicar um aplicativo no RemoteApp do Azure | Microsoft Docs
description: Saiba como publicar aplicativos e recursos no RemoteApp do Azure.
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: c7e1a2cd-8e1f-4a33-bd43-8032ec9ac952
ms.service: remoteapp
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0b81ef8425e44a9813fc0220f0ab314a3110f265


---
# <a name="how-to-publish-an-app-in-remoteapp"></a>Como publicar um aplicativo no RemoteApp
> [!IMPORTANT]
> O Azure RemoteApp está sendo descontinuado. Leia o [comunicado](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.
> 
> 

Depois de criar sua coleção RemoteApp, você precisa publicar os aplicativos ou recursos que você deseja disponibilizar para os usuários. As imagens de modelo fornecidas com sua assinatura tem apenas alguns aplicativos publicados por padrão – para compartilhar os outros aplicativos, é necessário publicá-los.

> [!NOTE]
> Você precisa atualizar um aplicativo? Você precisará [atualizar a imagem](remoteapp-update.md) primeiro.
> 
> 

Na guia **Publicação** no portal, clique em **Publicar**. Você pode adicionar um aplicativo da imagem do modelo do menu **Iniciar** ou fornecer o caminho onde o aplicativo está instalado na imagem do modelo. Se você optar por adicionar do menu **Iniciar** , escolha na lista o aplicativo para publicação. Se você optar por fornecer o caminho para o aplicativo, digite um nome para o aplicativo e o caminho para o aplicativo. Use variáveis no caminho - por exemplo, "%systemdrive%" em vez de "c:\"".

> [!NOTE]
> Se quiser adicionar seu aplicativo do menu **Iniciar**, precisará ter *adicionado esse aplicativo ao menu **Iniciar** em sua imagem de modelo* Caso contrário, o RemoteApp verá somente o que *está* no menu **Iniciar** e você ficará confuso. 
> 
> Para ter certeza de que seu aplicativo está no menu **Iniciar**, coloque um arquivo de atalho - **.lnk** - dentro da pasta %systemdrive%\ProgramData\Microsoft\Windows\Menu Iniciar\Programas.
> 
> Se você se esqueceu de adicionar o aplicativo ao menu **Iniciar** quando criou o modelo, opte por adicionar o caminho ao aplicativo. (Ou recrie a imagem do seu modelo, mas é muito mais trabalhoso.)
> 
> 




<!--HONumber=Nov16_HO3-->


