---
title: "Tamanhos dos serviços de nuvem | Microsoft Docs"
description: "Lista os tamanhos diferentes da máquina virtual (e IDs) para funções Web e de trabalho do serviço de nuvem do Azure."
services: cloud-services
documentationcenter: 
author: Thraka
manager: timlt
editor: 
ms.assetid: 1127c23e-106a-47c1-a2e9-40e6dda640f6
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 10/27/2016
ms.author: adegeo
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: d6faf73b3fb5f56e42a07dc9c0bbf1a48767c7c8


---
# <a name="sizes-for-cloud-services"></a>Tamanhos dos serviços de nuvem
Este tópico descreve os tamanhos disponíveis e as opções para instâncias de função de Serviço de Nuvem (funções web e funções de trabalho). Ele também fornece considerações de implantação a serem observadas ao planejar o uso desses recursos. Cada tamanho tem uma ID que você colocará em seu [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef).

O Serviços de Nuvem é um dos vários tipos de recursos de computação oferecidos pelo Azure. Clique [aqui](cloud-services-choose-me.md) para obter mais informações sobre Serviços de Nuvem.

> [!NOTE]
> Para ver os limites relacionados do Azure, confira [Assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md)
> 
> 

## <a name="sizes-for-web-and-worker-role-instances"></a>Tamanhos de instâncias de função web e de trabalho
Há vários tamanhos padrão para você escolher no Azure. Entre as considerações sobre algumas dessas dimensões estão:

* As VMs da série D são projetadas para executar aplicativos que exigem maior capacidade de computação e de desempenho de disco temporário. As VMs da série D fornecem processadores mais rápidos, uma maior taxa de memória por núcleo e uma unidade de estado sólido (SSD) para o disco temporário. Para obter detalhes, confira o anúncio no blog do Azure, [Novos tamanhos de máquina virtual da série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/).
* A série Dv2, uma continuação da série D original, apresenta uma CPU mais potente. A CPU da série Dv2 é aproximadamente 35% mais rápida do que a CPU da série D. Ela se baseia na última geração do processador Intel Xeon® E5-2673 v3 (Haswell) de 2.4 GHz e, com a Intel Turbo Boost Technology 2.0, pode chegar a até 3.1 GHz. A série Dv2 tem as mesmas configurações de memória e disco que a série D.
* As VMs da série G oferecem a maior memória e são executadas em hosts com processadores da família Intel Xeon E5 V3.
* As VMs da série A podem ser implantadas em uma variedade de tipos de hardware e processadores. O tamanho é limitado, com base no hardware, para oferecer desempenho de processador consistente para a instância em execução, independentemente do hardware em que é implantado. Para determinar o hardware físico no qual esse tamanho é implantado, consulte o hardware virtual de dentro da Máquina Virtual.
* O tamanho A0 está assinado em excesso no hardware físico. Para este tamanho específico somente, outras implantações de clientes podem afetar o desempenho da carga de trabalho em execução. O desempenho relativo é descrito a seguir como a linha de base esperada, sujeito a uma variação aproximada de 15%.

O tamanho da máquina virtual afeta os preços. O tamanho também afeta a capacidade de processamento, memória e armazenamento da máquina virtual. Os custos de armazenamento são calculados separadamente com base nas páginas usadas na conta de armazenamento. Para obter detalhes, confira [Detalhes de preços de máquinas virtuais](https://azure.microsoft.com/pricing/details/virtual-machines/) e [Preços do Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/). 

As considerações a seguir podem ajudá-lo a escolher um tamanho:

* Os tamanhos A8-A11 e série H também são conhecidos como *instâncias de computação intensiva*. O hardware de datacenter do Azure que executa esses tamanhos é projetado e otimizado para aplicativos de uso intensivo de computação e rede, incluindo aplicativos, modelagem e simulações de cluster HPC (computação de alto desempenho). A série de A8-A11 usa Intel Xeon E5-2670 @ 2,6 GHz e a série H usa Intel Xeon E5-2667 v3 @ 3,2 GHz. Para obter informações detalhadas e considerações sobre o uso desses tamanhos, confira [Sobre VMs série H ou série A com computação intensiva](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* As séries Dv2 e D e G são ideais para aplicativos que exigem CPUs mais rápidas, melhor desempenho de disco local ou que têm uma maior demanda de memória.  Elas oferecem uma combinação poderosa para vários aplicativos de nível empresarial.
* Alguns dos hosts físicos em data centers do Azure podem não aceitar tamanhos de máquina virtual maiores, como A5 – A11. Como resultado, você poderá ver a mensagem de erro **Falha ao configurar a máquina virtual {nome da máquina}** ou **Falha ao criar a máquina virtual {nome da máquina}** ao redimensionar uma máquina virtual existente para um novo tamanho, criar uma nova máquina virtual em uma rede virtual criada antes de 16 de abril de 2013 ou adicionar uma nova máquina virtual a um serviço de nuvem existente. Confira [Erro: "Falha ao configurar a máquina virtual"](https://social.msdn.microsoft.com/Forums/9693f56c-fcd3-4d42-850e-5e3b56c7d6be/error-failed-to-configure-virtual-machine-with-a5-a6-or-a7-vm-size?forum=WAVirtualMachinesforWindows) no fórum de suporte de soluções alternativas para cada cenário de implantação.  
* Sua assinatura também pode limitar o número de núcleos que você pode implantar em determinadas famílias de tamanho. Para aumentar a cota, entre em contato com o Suporte do Azure.

## <a name="performance-considerations"></a>Considerações sobre o desempenho
Criamos o conceito da ACU (unidade de computação do Azure) para fornecer uma maneira de comparar o desempenho de computação (CPU) em SKUs do Azure. Isso ajudará você a identificar facilmente qual SKU é tem maior probabilidade de satisfazer suas necessidades de desempenho.  A ACU atualmente é padronizada como uma VM pequena (Standard_A1) sendo 100 e todas as SKUs representam, aproximadamente, o quanto a SKU pode executar um parâmetro de comparação padrão mais rapidamente. 

> [!IMPORTANT]
> A ACU é apenas uma diretriz.  Os resultados para sua carga de trabalho podem variar. 
> 
> 

<br>

| Família de SKU | ACU/núcleo |
| --- | --- |
| [Standard_A0](#a-series) |50 |
| [Standard_A1-4](#a-series) |100 |
| [Standard_A5-7](#a-series) |100 |
| [A8-A11](#a-series) |225* |
| [D1-14](#d-series) |160 |
| [D1-15v2](#dv2-series) |210 - 250* |
| [G1-5](#g-series) |180 - 240* |
| [H](#h-series) |290 – 300* |

ACUs marcados com um * usam tecnologia Intel® Turbo para aumentar a frequência da CPU e fornecer um aumento de desempenho.  A quantidade do aumento pode variar com base no tamanho da VM, na carga de trabalho e em outras cargas de trabalho em execução no mesmo host.

## <a name="size-tables"></a>Tabelas de tamanho
As tabelas a seguir mostram os tamanhos e as capacidades oferecidas.

* A capacidade de armazenamento é mostrada em unidades de GiB ou de 1024^3 bytes. Ao comparar os discos medidos em GB (1000^3 bytes) com os discos medidos em GiB (1024^3), lembre-se de que os números de capacidade fornecidos em GiB poderão parecer menores. Por exemplo, 1023 GiB = 1098,4 GB
* A taxa de transferência do disco é medida em IOPS (operações de entrada/saída por segundo) e em MBps, em que MBps = 10^6 bytes/s.
* Os discos de dados podem operar nos modos em cache ou não armazenado em cache. Para a operação do disco de dados armazenados em cache, o modo de cache do host é definido como **ReadOnly** ou **ReadWrite**.  Para as operação do disco de dados não armazenados em cache, o modo de cache do host é definido como **Nenhum**.
* A largura de banda de rede máxima é a largura de banda agregada máxima alocada e atribuída por tipo de VM. A largura de banda máxima fornece diretrizes para selecionar o tipo correto de VM, a fim de garantir que uma capacidade adequada de rede está disponível. Durante a movimentação entre Baixa, Moderada, Alta e Muito Alta, a taxa de transferência será aumentada de acordo. O desempenho real da rede dependerá de vários fatores, incluindo cargas de rede e aplicativos, bem como configurações de rede do aplicativo.

## <a name="a-series"></a>Séria A
| Tamanho | Núcleos de CPU | Memória: GiB | HDD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 |1 |0,768 |20 |1 |1 x 500 |1 / baixa |
| Standard_A1 |1 |1,75 |70 |2 |2x500 |1 / moderada |
| Standard_A2 |2 |3,5 GB |135 |4 |4x500 |1 / moderada |
| Standard_A3 |4 |7 |285 |8 |8 x 500 |2 / alta |
| Standard_A4 |8 |14 |605 |16 |16 x 500 |4 / alta |
| Standard_A5 |2 |14 |135 |4 |4x500 |1 / moderada |
| Standard_A6 |4 |28 |285 |8 |8 x 500 |2 / alta |
| Standard_A7 |8 |56 |605 |16 |16 x 500 |4 / alta |

## <a name="a-series---compute-intensive-instances"></a>Série A – Instâncias de computação intensiva
Para obter informações e considerações sobre o uso desses tamanhos, confira [Sobre VMs série H ou série A com computação intensiva](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

| Tamanho | Núcleos de CPU | Memória: GiB | HDD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A8* |8 |56 |382 |16 |16 x 500 |2 / alta |
| Standard_A9* |16 |112 |382 |16 |16 x 500 |4 / muito alta |
| Standard_A10 |8 |56 |382 |16 |16 x 500 |2 / alta |
| Standard_A11 |16 |112 |382 |16 |16 x 500 |4 / muito alta |

*Compatível com RDMA

## <a name="d-series"></a>Série D
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1 |1 |3,5 |50 |2 |2x500 |1 / moderada |
| Standard_D2 |2 |7 |100 |4 |4x500 |2 / alta |
| Standard_D3 |4 |14 |200 |8 |8 x 500 |4 / alta |
| Standard_D4 |8 |28 |400 |16 |16 x 500 |8 / alta |
| Standard_D11 |2 |14 |100 |4 |4x500 |2 / alta |
| Standard_D12 |4 |28 |200 |8 |8 x 500 |4 / alta |
| Standard_D13 |8 |56 |400 |16 |16 x 500 |8 / alta |
| Standard_D14 |16 |112 |800 |32 |32 x 500 |8 / muito alta |

## <a name="dv2-series"></a>Série Dv2
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco de dados: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_D1_v2 |1 |3,5 |50 |2 |2x500 |1 / moderada |
| Standard_D2_v2 |2 |7 |100 |4 |4x500 |2 / alta |
| Standard_D3_v2 |4 |14 |200 |8 |8 x 500 |4 / alta |
| Standard_D4_v2 |8 |28 |400 |16 |16 x 500 |8 / alta |
| Standard_D5_v2 |16 |56 |800 |32 |32 x 500 |8 / extremamente alta |
| Standard_D11_v2 |2 |14 |100 |4 |4x500 |2 / alta |
| Standard_D12_v2 |4 |28 |200 |8 |8 x 500 |4 / alta |
| Standard_D13_v2 |8 |56 |400 |16 |16 x 500 |8 / alta |
| Standard_D14_v2 |16 |112 |800 |32 |32 x 500 |8 / extremamente alta |
| Standard_D15_v2 |20 |140 |1.000 |40 |40 x 500 |8 / extremamente alta |

## <a name="g-series"></a>Série G
| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_G1 |2 |28 |384 |4 |4 x 500 |1 / alta |
| Standard_G2 |4 |56 |768 |8 |8 x 500 |2 / alta |
| Standard_G3 |8 |112 |1.536 |16 |16 x 500 |4 / muito alta |
| Standard_G4 |16 |224 |3.072 |32 |32 x 500 |8 / extremamente alta |
| Standard_G5 |32 |448 |6.144 |64 |64 x 500 |8 / extremamente alta |

## <a name="h-series"></a>Série H
As máquinas virtuais da série H do Azure são a próxima geração de VMs de computação de alto desempenho voltadas para as necessidades computacionais de alto nível, como modelagem molecular e dinâmica de fluido computacional. Essas VMs de 8 e 16 núcleos baseiam-se na tecnologia do processador Intel Haswell E5-2667 V3 apresentando memória DDR4 e armazenamento SSD local. 

Além de potência de CPU considerável, a série H oferece diversas opções para RDMA e rede de baixa latência usando FDR InfiniBand e várias configurações de memória para dar suporte a requisitos computacionais com uso intensivo de memória.

| Tamanho | Núcleos de CPU | Memória: GiB | SSD local: GiB | Discos de dados máximos | Taxa de transferência máxima do disco: IOPS | NICs máximas / largura de banda da rede |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_H8 |8 |56 |1000 |16 |16 x 500 |8 / alta |
| Standard_H16 |16 |112 |2000 |32 |32 x 500 |8 / muito alta |
| Standard_H8m |8 |112 |1000 |16 |16 x 500 |8 / alta |
| Standard_H16m |16 |224 |2000 |32 |32 x 500 |8 / muito alta |
| Standard_H16r* |16 |112 |2000 |32 |32 x 500 |8 / muito alta |
| Standard_H16mr* |16 |224 |2000 |32 |32 x 500 |8 / muito alta |

*Compatível com RDMA

## <a name="notes-standard-a0---a4-using-cli-and-powershell"></a>Observações: Standard A0 - A4 usando a CLI e o PowerShell
No modelo de implantação clássica, alguns nomes de tamanhos de VM são ligeiramente diferentes na CLI e no PowerShell:

* Standard_A0 é ExtraSmall 
* Standard_A1 é pequeno
* Standard_A2 é médio
* Standard_A3 é grande
* Standard_A4 é ExtraLarge

## <a name="configure-sizes-for-cloud-services"></a>Configurar tamanhos para os Serviços de Nuvem
Você pode especificar o tamanho da Máquina Virtual de uma instância de função como parte do modelo de serviço descrito pelo [arquivo de definição de serviço](cloud-services-model-and-package.md#csdef). O tamanho da função determina o número de núcleos de CPU, a capacidade de memória e o tamanho do sistema de arquivos local alocados para uma instância em execução. Escolha o tamanho da função com base em requisitos de recurso do aplicativo.

Veja um exemplo de definição do tamanho da função como [Standard_D2](#general-purpose-d) para uma instância de Função Web:

```xml
<WorkerRole name="Worker1" vmsize="Standard_D2">
...
</WorkerRole>
```

## <a name="next-steps"></a>Próximas etapas
* Saiba mais sobre a [assinatura do Azure e limites de serviços, cotas e restrições](../azure-subscription-service-limits.md).
* Saiba mais [sobre as VMs da série H e da série A com computação intensiva](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) para cargas de trabalho como HPC (Computação de Alto Desempenho).




<!--HONumber=Nov16_HO3-->


