---
title: Analisando dados do sensor usando Hive e Hadoop | Microsoft Docs
description: Saiba como analisar dados do sensor usando o Console de consulta Hive com HDInsight (Hadoop) e depois visualizar os dados no Microsoft Excel usando o PowerView.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8ac160c-1cef-45d9-bf36-7beb5a439105
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/20/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ef2e4b9272389f4e027821e84df4499bf8987c39


---
# <a name="analyze-sensor-data-using-the-hive-query-console-on-hadoop-in-hdinsight"></a>Analisar dados de sensor usando o Console de consulta Hive no Hadoop HDInsight
Saiba como analisar dados do sensor usando o Console de consulta Hive com HDInsight (Hadoop) e depois visualizar os dados no Microsoft Excel usando o Power View.

> [!NOTE]
> As etapas neste documento só funcionam com clusters HDInsight baseados no Windows.
> 
> 

Nesta amostra, você usará o Hive para processar dados históricos produzidos por sistemas de aquecimento, ventilação e ar condicionado (HVAC) para identificar sistemas que não conseguem manter uma temperatura determinada de maneira confiável. Você saberá como:

* Criar tabelas do HIVE para consultar dados armazenados em arquivos com valores separados por vírgulas (CSV).
* Criar consultas do HIVE para analisar os dados.
* Use o Microsoft Excel para conectar-se ao HDInsight (usando ODBC, conectividade de banco de dados aberta) para recuperar os dados analisados.
* Use o Power View para visualizar os dados.

![Um diagrama da arquitetura da solução](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

## <a name="prerequisites"></a>Pré-requisitos
* Um cluster do HDInsight (Hadoop): veja [Provisionar clusters Hadoop no HDInsight](hdinsight-provision-clusters.md) para obter informações sobre como criar um cluster.
* Microsoft Excel 2013
  
  > [!NOTE]
  > O Microsoft Excel é usado para visualização de dados no [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).
  > 
  > 
* [Driver ODBC do Microsoft Hive](http://www.microsoft.com/download/details.aspx?id=40886)

## <a name="to-run-the-sample"></a>Para executar a amostra
1. No seu navegador da Web, vá até a URL a seguir. Substitua `<clustername>` pelo nome do cluster HDInsight.
   
         https://<clustername>.azurehdinsight.net
   
    Quando solicitado, faça a autenticação usando o nome de usuário e senha do administrador usados ao provisionar esse cluster.
2. Na página da Web que é aberta, clique na guia **Galeria de introdução** e na categoria **Soluções com dados de amostra**, clique na amostra **Análise de dados do sensor**.
   
    ![Imagem da galeria de Introdução](./media/hdinsight-hive-analyze-sensor-data/getting-started-gallery.png)
3. Siga as instruções fornecidas na página da Web para concluir a amostra.




<!--HONumber=Nov16_HO3-->


