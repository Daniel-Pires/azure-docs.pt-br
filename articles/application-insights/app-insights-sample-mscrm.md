---
title: 'Passo a passo: monitorar o Microsoft Dynamics CRM com o Application Insights'
description: "Obtenha a telemetria do Microsoft Dynamics CRM Online usando o Application Insights. Passo a passo da instalação, obtenção de dados, visualização e exportação."
services: application-insights
documentationcenter: 
author: mazharmicrosoft
manager: douge
ms.assetid: 04c66338-687e-49e5-9975-be935f98f156
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/17/2015
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 41ce9b0e323c0938b6db98b99d8d687d1ed0f0ef
ms.openlocfilehash: 9304b26711226fc9a7e672f59441ae65c0d5a023


---
# <a name="walkthrough-enabling-telemetry-for-microsoft-dynamics-crm-online-using-application-insights"></a>Passo a passo: Ativar a telemetria para o Microsoft Dynamics CRM Online usando o Application Insights
Este artigo mostra como obter dados de telemetria no [Microsoft Dynamics CRM Online](https://www.dynamics.com/) usando o [Azure Application Insights](https://azure.microsoft.com/services/application-insights/). Percorreremos o processo completo de adição de um script do Application Insights ao seu aplicativo, captura de dados e visualização de dados.

> [!NOTE]
> [Procurar a solução de exemplo](https://dynamicsandappinsights.codeplex.com/).
> 
> 

## <a name="add-application-insights-to-new-or-existing-crm-online-instance"></a>Adicionar o Application Insights a uma instância nova ou existente do CRM Online
Para monitorar seu aplicativo, você adiciona um SDK do Application Insights a ele. O SDK envia a telemetria para o [Portal do Application Insights](https://portal.azure.com), no qual você pode usar nossas potentes ferramentas de análise e diagnóstico ou exportar os dados para o armazenamento.

### <a name="create-an-application-insights-resource-in-azure"></a>Criar um recurso do Application Insights no Azure
1. Obtenha [uma conta no Microsoft Azure](http://azure.com/pricing). 
2. Entre no [Portal do Azure](https://portal.azure.com) e adicione um novo recurso do Application Insights. Este é o local em que os dados serão processados e exibidos.
   
    ![Clique em +, Serviços de Desenvolvedor, Application Insights.](./media/app-insights-sample-mscrm/01.png)
   
    Escolha ASP.NET como o tipo de aplicativo.
3. Abra a guia Início Rápido e abra o script de código.
   
    ![](./media/app-insights-sample-mscrm/03.png)

**Mantenha a página de código aberta** enquanto realiza a próxima etapa na outra janela do navegador. Você precisará do código em breve. 

### <a name="create-a-javascript-web-resource-in-microsoft-dynamics-crm"></a>Criar um recurso da Web em JavaScript no Microsoft Dynamics CRM
1. Abra sua instância do CRM Online e faça logon com privilégios de administrador.
2. Abra o Microsoft Dynamics CRM Configurações, Personalizações, Personalizar o Sistema
   
    ![](./media/app-insights-sample-mscrm/04.png)
   
    ![](./media/app-insights-sample-mscrm/05.png)

    ![](./media/app-insights-sample-mscrm/06.png)

1. Crie um recurso de JavaScript.
   
    ![](./media/app-insights-sample-mscrm/07.png)
   
    Atribua um nome, selecione **Script (JScript)** e abra o editor de texto.
   
    ![](./media/app-insights-sample-mscrm/08.png)
2. Copie o código do Application Insights. Ao copiar, certifique-se de ignorar marcas de script. Consulte a captura de tela abaixo:
   
    ![](./media/app-insights-sample-mscrm/09.png)
   
    O código inclui a chave de instrumentação que identifica seu recurso do Application insights.
3. Salve e publique.
   
    ![](./media/app-insights-sample-mscrm/10.png)

### <a name="instrument-forms"></a>Formulários de instrumento
1. No Microsoft CRM Online, abra o formulário Conta
   
    ![](./media/app-insights-sample-mscrm/11.png)
2. Abra as propriedades do formulário
   
    ![](./media/app-insights-sample-mscrm/12.png)
3. Adicione o recurso da Web de JavaScript que você criou
   
    ![](./media/app-insights-sample-mscrm/13.png)
   
    ![](./media/app-insights-sample-mscrm/14.png)
4. Salve e publique as personalizações do formulário.

## <a name="metrics-captured"></a>Métricas capturadas
Agora você configurou a captura de telemetria do formulário. Sempre que ele for usado, os dados serão enviados ao recurso do Application Insights.

Aqui estão exemplos dos dados que você verá.

#### <a name="application-health"></a>Integridade do aplicativo
![](./media/app-insights-sample-mscrm/15.png)

![](./media/app-insights-sample-mscrm/16.png)

Exceções de navegador:

![](./media/app-insights-sample-mscrm/17.png)

Clique no gráfico para obter mais detalhes:

![](./media/app-insights-sample-mscrm/18.png)

#### <a name="usage"></a>Uso
![](./media/app-insights-sample-mscrm/19.png)

![](./media/app-insights-sample-mscrm/20.png)

![](./media/app-insights-sample-mscrm/21.png)

#### <a name="browsers"></a>Navegadores
![](./media/app-insights-sample-mscrm/22.png)

![](./media/app-insights-sample-mscrm/23.png)

#### <a name="geolocation"></a>Geolocalização
![](./media/app-insights-sample-mscrm/24.png)

![](./media/app-insights-sample-mscrm/25.png)

#### <a name="inside-page-view-request"></a>Solicitação de exibição de página interna
![](./media/app-insights-sample-mscrm/26.png)

![](./media/app-insights-sample-mscrm/27.png)

![](./media/app-insights-sample-mscrm/28.png)

![](./media/app-insights-sample-mscrm/29.png)

![](./media/app-insights-sample-mscrm/30.png)

## <a name="sample-code"></a>Exemplo de código
[Procurar no código de amostra](https://dynamicsandappinsights.codeplex.com/).

## <a name="power-bi"></a>Power BI
Você pode fazer análises ainda mais profundas se [exportar os dados para o Microsoft Power BI](app-insights-export-power-bi.md).

## <a name="sample-microsoft-dynamics-crm-solution"></a>Exemplo de Solução para o Microsoft Dynamics CRM
[Veja a solução do exemplo implementada no Microsoft Dynamics CRM](https://dynamicsandappinsights.codeplex.com/).

## <a name="learn-more"></a>Saiba mais
* [O que é o Application Insights?](app-insights-overview.md)
* [Application Insights para páginas da Web](app-insights-javascript.md)
* [Mais exemplos e explicações passo a passo](app-insights-code-samples.md)




<!--HONumber=Nov16_HO3-->


