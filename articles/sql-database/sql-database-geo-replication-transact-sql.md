---
title: "Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o Transact-SQL | Microsoft Docs"
description: "Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure usando o Transact-SQL"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: d94d89a6-3234-46c5-8279-5eb8daad10ac
ms.service: sql-database
ms.custom: business continuity; how to
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/13/2016
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 702a01f7ea56e8af6286149bcb42590294cb618b


---
# <a name="configure-geo-replication-for-azure-sql-database-with-transact-sql"></a>Configurar a Replicação Geográfica para o Banco de Dados SQL do Azure com o Transact-SQL
> [!div class="op_single_selector"]
> * [Visão geral](sql-database-geo-replication-overview.md)
> * [Portal do Azure](sql-database-geo-replication-portal.md)
> * [PowerShell](sql-database-geo-replication-powershell.md)
> * [T-SQL](sql-database-geo-replication-transact-sql.md)
> 
> 

Este artigo mostra como configurar a Replicação Geográfica Ativa para um Banco de Dados SQL do Azure com o Transact-SQL.

Para iniciar o failover usando o Transact-SQL, veja [Iniciar um failover planejado ou não planejado para o Banco de Dados SQL do Azure com o Transact-SQL](sql-database-geo-replication-failover-transact-sql.md).

> [!NOTE]
> Replicação Geográfica Ativa (secundários legíveis) agora está disponível para todos os bancos de dados em todas as camadas de serviço. Em abril de 2017 o tipo de secundário não legível será descontinuado e bancos de dados não legíveis existentes serão automaticamente atualizados para secundários legíveis.
> 
> 

Para configurar a replicação geográfica ativa usando Transact-SQL, você precisará do seguinte:

* Uma assinatura do Azure.
* Um servidor do Banco de Dados SQL do Azure lógico <MyLocalServer> e um banco de dados SQL <MyDB> -O banco de dados primário que você deseja replicar.
* Um ou mais servidores lógicos do Banco de Dados SQL do Azure <MySecondaryServer(n)> - os servidores lógicos que serão os servidores parceiros nos quais você criará bancos de dados secundários.
* Um logon que é o DBManager no primário, ter o db_ownership do banco de dados local que você replicará geograficamente e ser o DBManager no servidor parceiro para o qual você vai configurar a Replicação Geográfica.
* SQL Server Management Studio (SSMS)

> [!IMPORTANT]
> Recomendamos que você sempre use a versão mais recente do Management Studio a fim de permanecer sincronizado com as atualizações no Microsoft Azure e no Banco de Dados SQL. [Atualizar o SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
> 
> 

## <a name="add-secondary-database"></a>Adicionar banco de dados secundário
Você pode usar a instrução **ALTER DATABASE** para criar um banco de dados secundário replicado geograficamente em um servidor parceiro. Você executa essa instrução no banco de dados mestre do servidor que contém o banco de dados a ser replicado. O banco de dados replicado geograficamente (o "banco de dados primário") terá o mesmo nome do banco de dados sendo replicado e, por padrão, terá o mesmo nível de serviço do banco de dados primário. O banco de dados secundário pode ser legível ou não legível, e pode ser um único banco de dados ou um banco de dados elástico. Para saber mais, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de Serviço](sql-database-service-tiers.md).
Depois do banco de dados secundário ser criado e propagado, os dados começarão a replicação assíncrona a partir do banco de dados primário. As etapas a seguir descrevem como configurar a Replicação Geográfica usando o Management Studio. As etapas para criar secundários não legíveis e legíveis, com um banco de dados individual ou um banco de dados elástico, são fornecidas.

> [!NOTE]
> Se existir um banco de dados no servidor do parceiro especificado com o mesmo nome do banco de dados primário, o comando falhará.
> 
> 

### <a name="add-non-readable-secondary-single-database"></a>Adicionar um secundário não legível (banco de dados individual)
Use as seguintes etapas para criar um secundário não legível como um banco de dados individual.

1. Com a versão 13.0.600.65 ou posterior do SQL Server Management Studio.
   
   > [!IMPORTANT]
   > Baixe a versão [mais recente](https://msdn.microsoft.com/library/mt238290.aspx) do SQL Server Management Studio. É recomendável usar sempre a versão mais recente do Management Studio para continuar em sincronia com as atualizações do portal do Azure.
   > 
   > 
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a instrução **ALTER DATABASE** a seguir para transformar um banco de dados local em uma Replicação Geográfica primária com um banco de dados secundário não legível em MySecondaryServer1, no qual MySecondaryServer1 é o nome amistoso do servidor.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer1> WITH (ALLOW_CONNECTIONS = NO);
4. Clique em **Execute** para executar a consulta.

### <a name="add-readable-secondary-single-database"></a>Adicionar um secundário legível (banco de dados individual)
Use as seguintes etapas para criar um secundário legível como um banco de dados individual.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma Replicação Geográfica primária com um banco de dados secundário legível em um servidor secundário.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer2> WITH (ALLOW_CONNECTIONS = ALL);
4. Clique em **Execute** para executar a consulta.

### <a name="add-non-readable-secondary-elastic-database"></a>Adicionar um secundário não legível (banco de dados elástico)
Use as seguintes etapas para criar um secundário não legível como um banco de dados elástico.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma Replicação Geográfica primária com um banco de dados secundário não legível em um servidor secundário em um pool elástico.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer3> WITH (ALLOW_CONNECTIONS = NO
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool1));
4. Clique em **Execute** para executar a consulta.

### <a name="add-readable-secondary-elastic-database"></a>Adicionar um secundário legível (banco de dados elástico)
Use as seguintes etapas para criar um secundário legível como um banco de dados elástico.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução **ALTER DATABASE** para transformar um banco de dados local em uma Replicação Geográfica primária com um banco de dados secundário legível em um servidor secundário em um pool elástico.
   
        ALTER DATABASE <MyDB>
           ADD SECONDARY ON SERVER <MySecondaryServer4> WITH (ALLOW_CONNECTIONS = ALL
           , SERVICE_OBJECTIVE = ELASTIC_POOL (name = MyElasticPool2));
4. Clique em **Execute** para executar a consulta.

## <a name="remove-secondary-database"></a>Remover banco de dados secundário
Você pode usar a instrução **ALTER DATABASE** para encerrar permanentemente a parceria de replicação entre um banco de dados secundário e seu primário. Essa instrução é executada no banco de dados mestre no qual reside o banco de dados primário. Após o encerramento da relação, o banco de dados secundário se torna um banco de dados de leitura/gravação normal. Se a conectividade com o banco de dados secundário for interrompida, o comando terá êxito, mas o secundário se tornará de leitura/gravação após a conectividade ser restaurada. Para saber mais, confira [ALTER DATABASE (Transact-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) e [Camadas de Serviço](sql-database-service-tiers.md).

Use as seguintes etapas para remover um secundário replicado geograficamente de uma parceria de Replicação Geográfica.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução **ALTER DATABASE** para remover um secundário replicado geograficamente.
   
        ALTER DATABASE <MyDB>
           REMOVE SECONDARY ON SERVER <MySecondaryServer1>;
4. Clique em **Execute** para executar a consulta.

## <a name="monitor-geo-replication-configuration-and-health"></a>Monitorar a configuração e a integridade da Replicação Geográfica
Monitorar as tarefas inclui o monitoramento da configuração de Replicação Geográfica e da integridade da replicação dos dados.  Você pode usar a exibição de gerenciamento dinâmica **sys.dm_geo_replication_links** no banco de dados mestre para retornar informações sobre todos os links de replicação existentes para cada banco de dados no servidor lógico do Banco de Dados SQL do Azure. Essa exibição contém uma linha para cada link de replicação entre os bancos de dados primários e secundários. Você pode usar a exibição de gerenciamento dinâmica **sys.dm_replication_link_status** para retornar uma linha para cada Banco de Dados SQL do Azure atualmente envolvido em um link de replicação. Isso inclui os bancos de dados primários e secundários. Se houver mais de um link de replicação contínua para um determinado banco de dados primário, essa tabela conterá uma linha para cada uma das relações. A exibição é criada em todos os bancos de dados, incluindo o mestre lógico. No entanto, consultar essa exibição no mestre lógico retorna um conjunto vazio. Você pode usar a exibição de gerenciamento dinâmica **sys.dm_operation_status** para mostrar o status de todas as operações do banco de dados, incluindo o status dos links de replicação. Para saber mais, confira [sys.geo_replication_links (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt575501.aspx), [sys.dm_geo_replication_link_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/mt575504.aspx) e [sys.dm_operation_status (Banco de Dados SQL do Azure)](https://msdn.microsoft.com/library/dn270022.aspx).

Use as etapas a seguir para monitorar uma parceria de Replicação Geográfica.

1. No Management Studio, conecte seu servidor lógico do Banco de Dados SQL do Azure.
2. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **mestre** e, em seguida, clique em **Nova Consulta**.
3. Use a seguinte instrução para mostrar todos os bancos de dados com links de Replicação Geográfica.
   
        SELECT database_id, start_date, modify_date, partner_server, partner_database, replication_state_desc, role, secondary_allow_connections_desc FROM [sys].geo_replication_links;
4. Clique em **Execute** para executar a consulta.
5. Abra a pasta Bancos de Dados, expanda a pasta **Bancos de Dados do Sistema**, clique com o botão direito do mouse em **MyDB** e, em seguida, clique em **Nova Consulta**.
6. Use a seguinte instrução para mostrar os intervalos de replicação e a hora da última replicação de meus bancos de dados secundários do MyDB.
   
        SELECT link_guid, partner_server, last_replication, replication_lag_sec FROM sys.dm_geo_replication_link_status
7. Clique em **Execute** para executar a consulta.
8. Use a seguinte instrução para mostrar as operações de replicação geográfica mais recentes associadas ao banco de dados MyDB.
   
        SELECT * FROM sys.dm_operation_status where major_resource_id = 'MyDB'
        ORDER BY start_time DESC
9. Clique em **Execute** para executar a consulta.

## <a name="upgrade-a-non-readable-secondary-to-readable"></a>Atualizar um secundário não legível para legível
Em abril de 2017 o tipo de secundário não legível será descontinuado e bancos de dados não legíveis existentes serão automaticamente atualizados para secundários legíveis. Se você estiver usando secundários não legíveis atualmente e desejar atualizá-los para serem legíveis, será possível usar as etapas simples a seguir para cada secundário.

> [!IMPORTANT]
> Não há nenhum método de autoatendimento de atualização in-loco de um secundário não legível para legível. Se você remover seu único secundário, o banco de dados primário permanecerá desprotegido até que o novo secundário esteja totalmente sincronizado. Se o SLA do seu aplicativo exigir que o primário esteja protegido sempre, você deverá considerar criar um secundário paralelo em um servidor diferente antes de aplicar as etapas de atualização acima. Observe que cada primário pode ter até quatro bancos de dados secundários.
> 
> 

1. Primeiro, conecte o servidor *secundário* e remova o banco de dados secundário não legível:  
   
        DROP DATABASE <MyNonReadableSecondaryDB>;
2. Agora se conecte ao servidor *primário* e adicione um novo secundário legível
   
        ALTER DATABASE <MyDB>
            ADD SECONDARY ON SERVER <MySecondaryServer> WITH (ALLOW_CONNECTIONS = ALL);

## <a name="next-steps"></a>Próximas etapas
* Para saber mais sobre a Replicação Geográfica Ativa, consulte [Replicação Geográfica Ativa](sql-database-geo-replication-overview.md)
* Para obter uma visão geral e os cenários de continuidade dos negócios, confira [Visão geral da continuidade dos negócios](sql-database-business-continuity.md)




<!--HONumber=Nov16_HO3-->


