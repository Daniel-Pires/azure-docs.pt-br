---
title: "Conectar VMs do Windows em um serviço de nuvem | Microsoft Docs"
description: "Conectar máquinas virtuais do Windows criadas com o modelo clássico de implantação a um serviço de nuvem ou de rede virtual do Azure."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: c1cbc802-4352-4d2e-9e49-4ccbd955324b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: ee34a7ebd48879448e126c1c9c46c751e477c406
ms.openlocfilehash: 75f130c20819c8bf89f9f850ec41f2f137180bcc


---
# <a name="connect-windows-virtual-machines-created-with-the-classic-deployment-model-with-a-virtual-network-or-cloud-service"></a>Conectar máquinas virtuais do Windows criadas com o modelo clássico de implantação com um serviço de nuvem ou de rede virtual
[!INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

As máquinas virtuais do Windows criadas com o modelo de implantação clássico são sempre colocadas em um serviço de nuvem. O serviço de nuvem funciona como um contêiner e fornece um nome DNS público exclusivo, um endereço IP público e um conjunto de pontos de extremidade para acessar a máquina virtual pela Internet. O serviço de nuvem pode estar em uma rede virtual, mas isso não é um requisito. Você também pode [conectar máquinas virtuais Linux a uma rede virtual ou serviço de nuvem](virtual-machines-linux-classic-connect-vms.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json).

Se um serviço de nuvem não estiver em uma rede virtual, ele será chamado de serviço de nuvem *autônomo* . As máquinas virtuais em um serviço de nuvem autônomo só poderão se comunicar com outras máquinas virtuais usando os nomes DNS públicos de outras máquinas virtuais, e o tráfego viajará pela Internet. Se um serviço de nuvem estiver em uma rede virtual, as máquinas virtuais no serviço de nuvem podem se comunicar com todas as outras máquinas virtuais na rede virtual sem enviar tráfego pela Internet.

Ao colocar as máquinas virtuais no mesmo serviço de nuvem autônomo, você ainda pode usar o balanceamento de carga e os conjuntos de disponibilidade. Para obter detalhes, confira [Máquinas virtuais de balanceamento de carga](virtual-machines-windows-load-balance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) e [Gerenciar a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). No entanto, você não pode organizar as máquinas virtuais em sub-redes ou conectar um serviço de nuvem autônomo à sua rede local. Aqui está um exemplo:

[!INCLUDE [virtual-machines-common-classic-connect-vms](../../includes/virtual-machines-common-classic-connect-vms.md)]

## <a name="next-steps"></a>Próximas etapas
Após criar uma máquina virtual, é uma boa ideia [adicionar um disco de dados](virtual-machines-windows-classic-attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) para que seus serviços e cargas de trabalho tenham um local para armazenar dados. 




<!--HONumber=Nov16_HO3-->


