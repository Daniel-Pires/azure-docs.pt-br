---
title: "Solução pré-configurada de manutenção preditiva | Microsoft Docs"
description: "Uma descrição da solução pré-configurada de manutenção preditiva do Azure IoT."
services: 
suite: iot-suite
documentationcenter: 
author: stevehob
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/31/2016
ms.author: araguila
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 2d44af03b8e16a2bd936fc805ed4f0c4e6c5fbfc


---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Visão geral da solução pré-configurada de manutenção preditiva
A solução pré-configurada de *manutenção preditiva* é uma das [soluções pré-configuradas][lnk_preconfigured_solutions] lançadas como parte do [Microsoft Azure IoT Suite][lnk_iot_suite]. Essa solução integra a coleta de telemetria do dispositivo em tempo real com um modelo preditivo criado com o [Azure Machine Learning][lnk_machine_learning].

Com o Azure IoT Suite, as empresas podem se conectar com rapidez e facilidade, monitorar ativos e analisar dados em tempo real. A solução pré-configurada de manutenção preditiva obtém esses dados e usa painéis e visualizações avançadas para fornecer às empresas uma nova inteligência que pode promover ganhos de eficiência e aumentar os fluxos de receita.

## <a name="the-scenario"></a>O cenário
A Fabrikam é uma companhia aérea regional que se dedica a fornecer uma excelente experiência ao cliente a preços competitivos. Uma das causas de atrasos de voos são problemas de manutenção e a manutenção dos motores de aeronave representa um desafio singular. A falha do motor durante o voo deve ser evitada a qualquer custo; portanto, a Fabrikam inspeciona seus motores regularmente e segue um programa de manutenção agendada. No entanto, os motores de aeronave nem sempre se desgastam da mesma forma. Algum tipo de manutenção desnecessária é realizada nos motores. O mais importante é que problemas ocorrem, o que pode fazer com que uma aeronave permaneça em solo até que a manutenção seja realizada. Isso causa atrasos caros, especialmente se uma aeronave estiver em um local onde os técnicos certos ou as peças de reposição certas não estiverem disponíveis.

Os motores de aeronave da Fabrikam são instrumentados com sensores que monitoram as condições do motor durante o voo. A Fabrikam usa o Azure IoT Suite para coletar os dados de sensor coletados durante o voo. Após vários anos acumulando dados operacionais e de falha do motor, os cientistas de dados da Fabrikam modelaram uma maneira para prever a RUL (Vida Útil Restante) de um motor de aeronave. O que eles identificaram foi uma correlação entre os dados de quatro dos sensores dos motores com um desgaste que leva a uma eventual falha. Embora a Fabrikam continue realizando inspeções regulares para garantir a segurança, ela agora pode usar os modelos para calcular a RUL para cada motor após cada voo, usando a telemetria coletada dos motores de durante o voo. A Fabrikam agora pode prever os futuros pontos de falha e planejar a manutenção e reparar antecipadamente.

> [!NOTE]
> O modelo de solução usa dados reais de desgaste do mecanismo.
> 
> 

Ao prever o momento em que a manutenção é necessária, a Fabrikam pode otimizar suas operações para reduzir os custos. Os coordenadores de manutenção trabalham com os agendadores para planejar a manutenção para que sua realização coincida com a parada de uma aeronave em um local específico e garantir tempo suficiente para que a aeronave esteja fora de serviço sem causar interrupções no cronograma. Nesse caso, a Fabrikam pode agendar técnicos, garantindo que a aeronave seja atendida com eficiência sem tempo de espera. Os gerentes de controle de inventário recebem planos de manutenção, para que possam otimizar seu processo de encomendas e inventário de peças de reposição. Tudo isso permite que a Fabrikam minimize o tempo de solo de aeronave e reduza os custos operacionais, ao mesmo tempo que garante a segurança dos passageiros e da tripulação.

Para entender como o [Azure IoT Suite][lnk_iot_suite] fornece recursos que os clientes precisam para aproveitar o potencial da manutenção preditiva, examine este [infográfico][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Como a solução de manutenção preditiva é criada
A solução aproveita um modelo de Aprendizado de Máquina do Azure existente como modelo para mostrar esses recursos trabalhando desde a telemetria de dispositivo coletada até os serviços IoT Suite. A Microsoft criou um [modelo de regressão][lnk_regression_model] de um mecanismo de aeronave e publicou o modelo completo, dados<sup>\[1\]</sup> e diretrizes passo a passo sobre como usar o modelo.

A solução pré-configurada de manutenção prevista do Azure IoT usa o modelo de regressão criado com base neste modelo. Ele é implantado em sua assinatura do Azure e exposto por meio de uma API gerada automaticamente. A solução inclui um subconjunto dos dados de teste que representa quatro (de um total de 100) de mecanismos e as quatro (de um total de 21) transmissões de dados de sensor que fornece um resultado preciso do modelo treinado.

*\[1\] A. Saxena e K. Goebel (2008). “Turbofan Engine Degradation Simulation Data Set” (Conjunto de dados da simulação de degradação do turbofan), Repositório de dados de prognóstico da NASA Ames (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="next-steps"></a>Próximas etapas
Para saber mais sobre como o Azure IoT habilita cenários de manutenção preditiva, leia [Capturar o valor da Internet das Coisas][lnk_capture_value].

Faça um [passo a passo][lnk-predictive-walkthrough] da solução pré-configurada de manutenção preditiva.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

Você também pode explorar alguns dos outros recursos das soluções pré-configuradas do IoT Suite:

* [Perguntas frequentes sobre o IoT Suite][lnk-faq]
* [Segurança de IoT desde o início][lnk-security-groundup]

[lnk-faq]: iot-suite-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md



<!--HONumber=Nov16_HO2-->


