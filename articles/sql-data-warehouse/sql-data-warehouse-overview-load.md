---
title: Carregar dados no SQL Data Warehouse do Azure | Microsoft Docs
description: "Aprenda sobre os cenários comuns para carregamento de dados no SQL Data Warehouse. Essas opções incluem usar PolyBase, armazenamento de blobs do Azure, arquivos simples e envio de disco. Você também pode usar ferramentas de terceiros."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: eae347c75ddaadcf41644f80bcdc5880ee94541a


---
# <a name="load-data-into-azure-sql-data-warehouse"></a>Carregar dados no SQL Data Warehouse do Azure
Um resumo das opções de cenário e recomendações para carregar dados no SQL Data Warehouse.

A parte mais difícil do carregamento de dados geralmente é preparar os dados para a carga. O Azure simplifica o carregamento usando o armazenamento de blobs de Azure como um armazenamento de dados comum para muitos dos serviços, e usando o Azure Data Factory para orquestrar a movimentação de dados e comunicação entre os serviços do Azure. Esses processos são integrados com a tecnologia PolyBase que usa MPP (Processamento Paralelo Maciço) para carregar dados em paralelo do armazenamento de blobs do Azure no SQL Data Warehouse. 

Para ver os tutoriais que carregam bancos de dados de exemplo, consulte [Carregar bancos de dados de exemplo][Carregar bancos de dados de exemplo].

## <a name="load-from-azure-blob-storage"></a>Carregar por meio do armazenamento de blobs do Azure
A maneira mais rápida para importar dados para o SQL Data Warehouse é usar o PolyBase para carregar dados do armazenamento de blobs do Azure. O PolyBase usa o design de MPP (Processamento Paralelo Maciço) do SQL Data Warehouse para carregar dados em paralelo do armazenamento de blobs do Azure. Para usar o PolyBase, você pode usar comandos T-SQL ou um pipeline da Azure Data Factory.

### <a name="1-use-polybase-and-t-sql"></a>1. Usar PolyBase e T-SQL
Resumo do processo de carregamento:

1. Formate seus dados como UTF-8, pois o PolyBase atualmente não dá suporte a UTF-16.
2. Mova seus dados do armazenamento de blobs Azure e armazene-os em arquivos de texto.
3. Configurar objetos externos no SQL Data Warehouse para definir o local e o formato dos dados
4. Execute um comando T-SQL para carregar os dados em paralelo em uma nova tabela de banco de dados.

<!-- 5. Schedule and run a loading job. --> 

Para obter um tutorial, consulte [Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)][Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)].

### <a name="2-use-azure-data-factory"></a>2. Usar o Azure Data Factory
Para uma forma mais simples de usar o PolyBase, você pode criar um pipeline do Azure Data Factory que usa o PolyBase para carregar dados do armazenamento de blobs do Azure no SQL Data Warehouse. Ele é rápido de configurar porque você não precisa definir os objetos de T-SQL. Se você precisar consultar dados externos sem importá-los, use o T-SQL. 

Resumo do processo de carregamento:

1. Formate seus dados como UTF-8, pois o PolyBase atualmente não dá suporte a UTF-16.
2. Mova seus dados do armazenamento de blobs Azure e armazene-os em arquivos de texto.
3. Crie um pipeline do Azure Data Factory para ingestão dos dados. Use a opção PolyBase.
4. Agende e execute o pipeline.

Para obter um tutorial, consulte [Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (Azure Data Factory)][Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (Azure Data Factory)].

## <a name="load-from-sql-server"></a>Carregar do SQL Server
Para carregar os dados do SQL Server para o SQL Data Warehouse, você pode usar o SSIS (Integration Services), transferir arquivos simples ou enviar discos à Microsoft. Continue lendo para obter um resumo dos diferentes processos e links de carregamento a tutoriais.

Para planejar uma migração de dados completa do SQL Server para o SQL Data Warehouse, consulte a [Visão geral da migração][Visão geral da migração]. 

### <a name="use-integration-services-ssis"></a>Usar o SSIS (Integration Services)
Se você já estiver usando pacotes do SSIS (Integration Services) para carregar no SQL Server, você poderá atualizar seus pacotes para usar o SQL Server como a origem e o SQL Data Warehouse como destino. Isso é rápido e fácil e é uma boa opção se você não está tentando migrar seu processo de carregamento para usar dados já na nuvem. A desvantagem é que a carga será mais lenta do que usar o PolyBase porque esse SSIS não executa a carga em paralelo.

Resumo do processo de carregamento:

1. Revise o pacote do Integration Services para apontar para a instância do SQL Server para a origem e o banco de dados do SQL Data Warehouse para o destino.
2. Migre seu esquema para o SQL Data Warehouse, se ainda não estiver no local.
3. Altere o mapeamento em seus pacotes para usar apenas os tipos de dados com suporte do SQL Data Warehouse.
4. Agende e execute o pacote.

Para obter um tutorial, consulte [Carregar dados do SQL Server para o Azure SQL Data Warehouse (SSIS)][Carregar dados do SQL Server para o Azure SQL Data Warehouse (SSIS)].

### <a name="use-azcopy-recommended-for-10-tb-data"></a>Usar o AZCopy (recomendado para < 10 TB de dados)
Se o tamanho dos dados é < 10 TB, você pode exportar os dados do SQL Server para arquivos simples, copiar os arquivos para o armazenamento de blobs do Azure e, em seguida, use o PolyBase para carregar os dados no SQL Data Warehouse

Resumo do processo de carregamento:

1. Use o utilitário de linha de comando bcp para exportar dados do SQL Server para arquivos simples.
2. Use o utilitário de linha de comando AZCopy para copiar dados de arquivos simples para o armazenamento de blobs do Azure.
3. Usar o PolyBase para carregar SQL Data Warehouse.

Para obter um tutorial, consulte [Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)][Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)].

### <a name="use-bcp"></a>Usar o bcp
Se você tiver uma pequena quantidade de dados, você poderá usar o bcp para carregar diretamente no SQL Data Warehouse do Azure.

Resumo do processo de carregamento:

1. Use o utilitário de linha de comando bcp para exportar dados do SQL Server para arquivos simples.
2. Use o bcp para carregar dados de arquivos simples diretamente para o SQL Data Warehouse.

Para obter um tutorial, consulte [Carregar dados do SQL Server para o Azure SQL Data Warehouse (bcp)][Carregar dados do SQL Server para o Azure SQL Data Warehouse (bcp)].

### <a name="use-importexport-recommended-for-10-tb-data"></a>Usar Importar/Exportar (recomendado para > 10 TB de dados)
Se o tamanho dos dados for > 10 TB e você desejar movê-los para o Azure, recomendamos usar o serviço de envio de disco [Importar/Exportar][Importar/Exportar]. 

Resumo do processo de carregamento

1. Use o utilitário de linha de comando bcp para exportar dados do SQL Server para arquivos simples em discos transferíveis.
2. Envie os discos para a Microsoft.
3. A Microsoft carrega os dados no SQL Data Warehouse

## <a name="load-from-hdinsight"></a>Carregar do HDInsight
O SQL Data Warehouse dá suporte ao carregamento de dados do HDInsight via PolyBase. O processo é igual ao carregamento de dados do Armazenamento de Blobs do Azure, usando o PolyBase para se conectar ao HDInsight para carregar dados. 

### <a name="1-use-polybase-and-t-sql"></a>1. Usar PolyBase e T-SQL
Resumo do processo de carregamento:

1. Formate seus dados como UTF-8, pois o PolyBase atualmente não dá suporte a UTF-16.
2. Transfira seus dados para o HDInsight e armazene-os em arquivos de texto, no formato ORC ou Parquet.
3. Configurar objetos externos no SQL Data Warehouse para definir o local e o formato dos dados.
4. Execute um comando T-SQL para carregar os dados em paralelo em uma nova tabela de banco de dados.

Para obter um tutorial, consulte [Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)][Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)].

## <a name="recommendations"></a>Recomendações
Muitos de nossos parceiros têm soluções de carregamento. Para saber mais, confira uma lista dos nossos [parceiros de solução][parceiros de solução]. 

Se seus dados forem provenientes de uma fonte não relacional e você desejar carregá-los no SQL Data Warehouse, você precisará transformá-los em linhas e colunas antes de carregá-los. Os dados transformados não precisam ser armazenado em um banco de dados, eles podem ser armazenados em arquivos de texto.

Crie estatísticas sobre os dados recém-carregados. O SQL Data Warehouse do Azure ainda não dá suporte a estatísticas de criação ou atualização automática.  Para obter o melhor desempenho de suas consultas, é importante que as estatísticas sejam criadas em todas as colunas de todas as tabelas após o primeiro carregamento ou após uma alteração significativa nos dados.  Para detalhes, consulte [Estatísticas][Estatísticas].

## <a name="next-steps"></a>Próximas etapas
Para mais dicas de desenvolvimento, consulte [visão geral sobre desenvolvimento][visão geral sobre desenvolvimento].

<!--Image references-->

<!--Article references-->
[Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (PolyBase)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Carregar dados do armazenamento de blobs do Azure para o SQL Data Warehouse (Azure Data Factory)]: ./sql-data-warehouse-load-from-azure-blob-storage-with-data-factory.md
[Carregar dados do SQL Server para o Azure SQL Data Warehouse (SSIS)]: ./sql-data-warehouse-load-from-sql-server-with-integration-services.md
[Carregar dados do SQL Server para o Azure SQL Data Warehouse (bcp)]: ./sql-data-warehouse-load-from-sql-server-with-bcp.md
[Carregar dados do SQL Server para o Azure SQL Data Warehouse (AZCopy)]: ./sql-data-warehouse-load-from-sql-server-with-azcopy.md

[Carregar bancos de dados de exemplo]: ./sql-data-warehouse-load-sample-databases.md
[Visão geral da migração]: ./sql-data-warehouse-overview-migrate.md
[parceiros de solução]: ./sql-data-warehouse-partner-business-intelligence.md
[visão geral sobre desenvolvimento]: ./sql-data-warehouse-overview-develop.md
[Estatísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->

<!--Other Web references-->
[Importar/Exportar]: https://azure.microsoft.com/documentation/articles/storage-import-export-service/



<!--HONumber=Nov16_HO3-->


