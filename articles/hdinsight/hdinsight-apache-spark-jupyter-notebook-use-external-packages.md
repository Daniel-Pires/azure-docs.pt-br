---
title: Usar pacotes externos com notebooks Jupyter em clusters do Apache Spark no HDInsight | Microsoft Docs
description: "Instruções passo a passo sobre como configurar os blocos de notas Jupyter disponíveis com clusters Spark no HDInsight para usar pacotes Spark externos."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 2a8bc545-064e-436f-8b5f-e67c26cfbf98
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 9e2726a53f5594f0f41edc5ac5e29ad999ea4533


---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight-linux"></a>Usar pacotes externos com notebooks Jupyter em clusters do Apache Spark no HDInsight Linux
Saiba como configurar um bloco de notas Jupyter no cluster Apache Spark no HDInsight (Linux) para usar pacotes externos enviados pela comunidade que não estão incluídos prontos no cluster. 

Você pode pesquisar o [Repositório do Maven](http://search.maven.org/) para obter uma lista de pacotes que estão disponíveis. Você também pode obter uma lista de pacotes disponíveis de outras fontes. Por exemplo, uma lista completa dos pacotes enviados pela comunidade está disponível em [Pacotes do Spark](http://spark-packages.org/).

Neste artigo, você aprenderá a usar o pacote [spark csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) com o bloco de notas Jupyter.

## <a name="prerequisites"></a>Pré-requisitos
Você deve ter o seguinte:

* Uma assinatura do Azure. Consulte [Obter a avaliação gratuita do Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Um cluster do Apache Spark no HDInsight no Linux. Para obter instruções, confira [Create Apache Spark clusters in Azure HDInsight](hdinsight-apache-spark-jupyter-spark-sql.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Usar pacotes externos com blocos de notas Jupyter
1. No [Portal do Azure](https://portal.azure.com/), no quadro inicial, clique no bloco do cluster Spark (se você o tiver fixado no quadro inicial). Você também pode navegar até o cluster em **Procurar Tudo** > **Clusters HDInsight**.   
2. Na folha do cluster Spark, clique em **Links Rápidos** e, na folha **Painel do Cluster**, clique em **Notebook do Jupyter**. Se você receber uma solicitação, insira as credenciais de administrador para o cluster.
   
   > [!NOTE]
   > Você também pode acessar o Bloco de Notas Jupyter de seu cluster abrindo a seguinte URL no navegador. Substitua **CLUSTERNAME** pelo nome do cluster:
   > 
   > `https://CLUSTERNAME.azurehdinsight.net/jupyter`
   > 
   > 
3. Crie um novo bloco de anotações. Clique em **Novo** e em **Spark**.
   
    ![Criar um novo bloco de anotações do Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.createnotebook.png "Create a new Jupyter notebook")
4. Um novo bloco de anotações é criado e aberto com o nome Untitled.pynb. Clique no nome do bloco de anotações na parte superior e digite um nome amigável.
   
    ![Fornecer um nome para o bloco de anotações](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/hdispark.note.jupyter.notebook.name.png "Provide a name for the notebook")
5. Você usará a mágica `%%configure` a fim de configurar o bloco de notas para usar um pacote externo. Em blocos de notas que usam pacotes externos, não deixe de chamar a mágica `%%configure` na primeira célula de código. Isso garante que o kernel está configurado para usar o pacote antes de iniciar a sessão.

    **Para HDInsight 3.3 e HDInsight 3.4** 

        %%configure
        { "packages":["com.databricks:spark-csv_2.10:1.4.0"] }


    **Para HDInsight 3.5** 

        %%configure
        { "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.10:1.4.0" }}


    >[AZURE.IMPORTANT] Se esquecer de configurar o kernel na primeira célula, você poderá usar `%%configure` com o parâmetro `-f`, mas isso reiniciará a sessão e todo o progresso será perdido.

1. O trecho de código acima espera as coordenadas do maven para o pacote externo no Repositório Central do Maven. Nesse trecho de código, `com.databricks:spark-csv_2.10:1.4.0` é a coordenada do maven para o pacote **spark-csv** . Veja como você constrói as coordenadas de um pacote.
   
    a. Localize o pacote no Repositório Maven. Para este tutorial, usamos [spark-csv](http://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar).
   
    b. No repositório, colete os valores para **GroupId**, **ArtifactId** e **Version**.
   
    ![Usar pacotes externos com o bloco de notas Jupyter](./media/hdinsight-apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Use external packages with Jupyter notebook")
   
    c. Concatene os três valores, separados por dois pontos (**:**).
   
        com.databricks:spark-csv_2.10:1.4.0
2. Execute a célula de código com a mágica `%%configure` . Isso irá configurar a sessão Livy subjacente para usar o pacote fornecido. Nas células subsequentes no bloco de notas, você agora pode usar o pacote conforme mostrado abaixo.
   
        val df = sqlContext.read.format("com.databricks.spark.csv").
        option("header", "true").
        option("inferSchema", "true").
        load("wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
3. Você pode executar os trechos de código, como mostrado abaixo, para exibir os dados do dataframe que você criou na etapa anterior.
   
        df.show()
   
        df.select("Time").count()

## <a name="a-nameseealsoasee-also"></a><a name="seealso"></a>Consulte também
* [Visão geral: Apache Spark no Azure HDInsight](hdinsight-apache-spark-overview.md)

### <a name="scenarios"></a>Cenários
* [Spark com BI: executar análise de dados interativa usando o Spark no HDInsight com ferramentas de BI](hdinsight-apache-spark-use-bi-tools.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para analisar a temperatura de prédios usando dados do sistema HVAC](hdinsight-apache-spark-ipython-notebook-machine-learning.md)
* [Spark com Aprendizado de Máquina: usar o Spark no HDInsight para prever resultados da inspeção de alimentos](hdinsight-apache-spark-machine-learning-mllib-ipython.md)
* [Streaming Spark: usar o Spark no HDInsight para a criação de aplicativos de streaming em tempo real](hdinsight-apache-spark-eventhub-streaming.md)
* [Análise de log do site usando o Spark no HDInsight](hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Criar e executar aplicativos
* [Criar um aplicativo autônomo usando Scala](hdinsight-apache-spark-create-standalone-application.md)
* [Executar trabalhos remotamente em um cluster do Spark usando Livy](hdinsight-apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Ferramentas e extensões
* [Usar o plug-in de Ferramentas do HDInsight para IntelliJ IDEA para criar e enviar aplicativos Spark Scala](hdinsight-apache-spark-intellij-tool-plugin.md)
* [Usar o plug-in de Ferramentas do HDInsight para depurar aplicativos Spark remotamente](hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Usar blocos de anotações do Zeppelin com um cluster Spark no HDInsight](hdinsight-apache-spark-use-zeppelin-notebook.md)
* [Kernels disponíveis para o bloco de anotações Jupyter no cluster do Spark para HDInsight](hdinsight-apache-spark-jupyter-notebook-kernels.md)
* [Instalar o Jupyter em seu computador e conectar-se a um cluster Spark do HDInsight](hdinsight-apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Gerenciar recursos
* [Gerenciar os recursos de cluster do Apache Spark no Azure HDInsight](hdinsight-apache-spark-resource-manager.md)
* [Rastrear e depurar trabalhos em execução em um cluster do Apache Spark no HDInsight](hdinsight-apache-spark-job-debugging.md)




<!--HONumber=Nov16_HO3-->


