---
title: "Logon em uma VM do Azure clássica | Microsoft Docs"
description: "Use o portal clássico do Azure para fazer logon na máquina virtual do Windows criada com o modelo clássico de implantação."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 3c1239ed-07dc-48b8-8b3d-dc8c5f0ab20e
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 07/28/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: b2a66b134d42e0b2d965c1f3ae83f93bed9cdfba


---
# <a name="log-on-to-a-windows-virtual-machine-using-the-azure-classic-portal"></a>Faça logon em uma máquina virtual do Windows usando o portal clássico do Azure
No portal clássico do Azure, você usará o botão **Conectar** para iniciar uma sessão de Área de Trabalho Remota e fazer logon em uma VM do Windows.

Deseja conectar-se a uma VM do Linux? Consulte [Como fazer logon em uma máquina virtual que esteja executando o Linux](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Saiba como [executar essas etapas usando o novo Portal do Azure](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

Para obter informações sobre como fazer logon em uma VM usando o modelo do Resource Manager, consulte [aqui](virtual-machines-windows-connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="video-walkthrough"></a>Passo a passo em vídeo
Veja um passo a passo em vídeo deste tutorial. Ele também aborda os pontos de extremidade e as portas públicas e privadas usadas para se conectar a uma VM do Windows no Azure.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Documentation-Shorts/Logging-On-To-VM-Running-Windows-Server-on-Azure/player]



## <a name="connect-to-the-virtual-machine"></a>Conectar-se à máquina virtual
1. Entre no portal clássico do Azure.
2. Clique em **Máquinas Virtuais**e selecione a máquina virtual.
3. Na barra de comandos localizada na parte inferior da página, clique em **Conectar**.
   
    ![Faça logon na máquina virtual](./media/virtual-machines-windows-classic-connect-logon/connectwindows.png)

> [!TIP]
> Se o botão **Conectar** não estiver disponível, consulte as dicas de solução de problemas no final deste artigo.
> 
> 

## <a name="log-on-to-the-virtual-machine"></a>Faça logon na máquina virtual
[!INCLUDE [virtual-machines-log-on-win-server](../../includes/virtual-machines-log-on-win-server.md)]

## <a name="next-steps"></a>Próximas etapas
* Se o botão **Conectar** estiver inativo, ou se você estiver enfrentando outros problemas com a conexão de Área de Trabalho Remota, tente redefinir a configuração. Usando o painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir a configuração remota**.
* Se houver problemas com a senha, tente redefini-la. No painel da máquina virtual, em **Visão Rápida**, clique em **Redefinir senha**.

Se essas dicas não funcionarem ou se não forem necessárias, consulte [Solucionar problemas de conexões da Área de Trabalho Remota com uma Máquina Virtual do Azure baseada no Windows](virtual-machines-windows-troubleshoot-rdp-connection.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Este artigo orienta você no diagnóstico e na solução de problemas comuns.




<!--HONumber=Nov16_HO3-->


