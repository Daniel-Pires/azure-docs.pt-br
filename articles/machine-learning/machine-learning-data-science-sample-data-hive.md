---
title: Dados de exemplo nas tabelas Hive do Azure HDInsight | Microsoft Docs
description: Reduzir os dados de amostragem em Tabelas Hive do Azure HDInsight (Hadoop)
services: machine-learning,hdinsight
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: f31e8d01-0fd4-4a10-b1a7-35de3c327521
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: hangzh;bradsev
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: b42658bde128dec9bdc54ffc59c624e957527d5b


---
# <a name="sample-data-in-azure-hdinsight-hive-tables"></a>Dados de exemplo nas tabelas Hive do Azure HDInsight
Neste artigo, descreveremos como reduzir os dados de amostra armazenados nas tabelas Hive do Azure HDInsight usando consultas de Hive. Abordaremos três métodos de amostragem popularmente usados:

* Amostragem aleatória uniforme
* Amostragem aleatória por grupos
* Amostragem estratificada

**Por que fazer amostragem dos dados?**
 Se o conjunto de dados que você deseja analisar é grande, geralmente é uma boa ideia reduzir a amostra de dados para um tamanho menor, mas representativo e mais gerenciável. Isso facilita a compreensão de dados, exploração e engenharia de recursos. Sua função no Processo de Ciência de Dados de Equipe é permitir a rápida criação de protótipos de funções de processamento de dados e modelos de aprendizado de máquina.

O **menu** abaixo leva a tópicos que descrevem como obter amostras de dados de vários ambientes de armazenamento.

[!INCLUDE [cap-sample-data-selector](../../includes/cap-sample-data-selector.md)]

Essa tarefa de amostragem é uma etapa do [TDSP (Processo de Ciência de Dados de Equipe)](https://azure.microsoft.com/documentation/learning-paths/cortana-analytics-process/).

## <a name="how-to-submit-hive-queries"></a>Como enviar consultas de Hive
Consultas de Hive podem ser enviadas do console de Linha de Comando do Hadoop no nó principal do cluster do Hadoop. Para isso, faça logon no nó principal do cluster do Hadoop, abra o Console de Linha de Comando do Hadoop e envie as consultas de Hive dele. Para obter instruções sobre como enviar consultas de Hive no console Linha de Comando do Hadoop, confira [Como enviar consultas de Hive](machine-learning-data-science-move-hive-tables.md#submit).

## <a name="a-nameuniforma-uniform-random-sampling"></a><a name="uniform"></a> Amostragem aleatória uniforme
A amostragem aleatória uniforme significa que cada linha no conjunto de dados tem a mesma chance de amostra. Isso pode ser implementado adicionando um campo rand() extra ao conjunto de dados na consulta interna "select" e na consulta externa “select”, esta condição estará nesse campo aleatório.

Veja um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, …, fieldN
    from
        (
        select
            field1, field2, …, fieldN, rand() as samplekey
        from <hive table name>
        )a
    where samplekey<='${hiveconf:sampleRate}'

Aqui, `<sample rate, 0-1>` especifica a proporção de registros que os usuários desejam como amostra.

## <a name="a-namegroupa-random-sampling-by-groups"></a><a name="group"></a> Amostragem aleatória por grupos
Ao realizar amostragem de dados categóricos, convém incluir ou excluir todas as instâncias de um determinado valor de uma variável categórica. Isso é o que "amostragem por grupo” significa.
Por exemplo, se você tiver uma variável categórica "Estado", que tem como valores NY, MA, CA, NJ, PA, etc. (siglas de estados dos EUA), o ideal é que registros do mesmo estado estejam sempre juntos, presentes ou não como amostra.

Veja um exemplo de consulta com amostragem por grupo:

    SET sampleRate=<sample rate, 0-1>;
    select
        b.field1, b.field2, …, b.catfield, …, b.fieldN
    from
        (
        select
            field1, field2, …, catfield, …, fieldN
        from <table name>
        )b
    join
        (
        select
            catfield
        from
            (
            select
                catfield, rand() as samplekey
            from <table name>
            group by catfield
            )a
        where samplekey<='${hiveconf:sampleRate}'
        )c
    on b.catfield=c.catfield

## <a name="a-namestratifiedastratified-sampling"></a><a name="stratified"></a>Amostragem estratificada
A amostragem aleatória é estratificada em relação a uma variável categórica quando as amostras obtidas têm valores da categoria em questão que estão na mesma proporção como a população-mãe da qual os exemplos foram obtidos. Usando o mesmo exemplo anterior, suponha que os dados têm subpopulações por estados, digamos NJ tem 100 observações, NY tem 60 observações e WA tem 300 observações. Se você especificar a taxa de amostragem estratificada para ser 0,5, a amostra obtida deve ter aproximadamente 50, 30 e 150 observações de NJ, NY e WA, respectivamente.

Veja um exemplo de consulta:

    SET sampleRate=<sample rate, 0-1>;
    select
        field1, field2, field3, ..., fieldN, state
    from
        (
        select
            field1, field2, field3, ..., fieldN, state,
            count(*) over (partition by state) as state_cnt,
              rank() over (partition by state order by rand()) as state_rank
          from <table name>
        ) a
    where state_rank <= state_cnt*'${hiveconf:sampleRate}'


Para obter informações sobre os métodos de amostragem mais avançados disponíveis no Hive, consulte [Amostragem LanguageManual](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Sampling).




<!--HONumber=Nov16_HO3-->


