---
title: Proteger um banco de dados no SQL Data Warehouse | Microsoft Docs
description: "Dicas para proteger um banco de dados no SQL Data Warehouse do Azure para desenvolvimento de soluções."
services: sql-data-warehouse
documentationcenter: NA
author: ronortloff
manager: jhubbard
editor: 
ms.assetid: 8fa2f5ca-4cf5-4418-99a2-4dc745799850
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: rortloff;barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 03466b412405d45553d80ebf4e283038b7894727


---
# <a name="secure-a-database-in-sql-data-warehouse"></a>Proteger um banco de dados no SQL Data Warehouse
> [!div class="op_single_selector"]
> * [Visão Geral da Segurança](sql-data-warehouse-overview-manage-security.md)
> * [Autenticação](sql-data-warehouse-authentication.md)
> * [Criptografia (Portal)](sql-data-warehouse-encryption-tde.md)
> * [Criptografia (T-SQL)](sql-data-warehouse-encryption-tde-tsql.md)
> 
> 

Este artigo apresenta os conceitos básicos da proteção do banco de dados do SQL Data Warehouse do Azure. Especificamente, este artigo apresentará a você recursos para limitar acesso, proteger dados e monitorar atividades em um banco de dados.

## <a name="connection-security"></a>Segurança da conexão
A Segurança da Conexão refere-se a como você restringe e protege as conexões com o banco de dados usando regras de firewall e criptografia de conexão.

As regras de firewall são usadas pelo servidor e pelo banco de dados para rejeitar tentativas de conexão de endereços IP que não foram incluídos explicitamente na lista de permissões. Para permitir conexões do endereço IP público do seu aplicativo ou computador cliente, você deve primeiro criar uma regra de firewall no nível de servidor usando o Portal do Azure, a API REST ou o PowerShell. Como prática recomendada, você deve restringir ao máximo os intervalos de endereços IP permitidos por meio do firewall de servidor.  Para acessar o SQL Data Warehouse do Azure de seu computador local, verifique se o firewall em sua rede e computador local permite a comunicação de saída na porta TCP 1433.  Para obter mais informações, consulte [Firewall do Banco de Dados SQL do Azure][Firewall do Banco de Dados SQL do Azure], [sp_set_firewall_rule][sp_set_firewall_rule] e [sp_set_database_firewall_rule][sp_set_database_firewall_rule].

As conexões com o SQL Data Warehouse são criptografadas por padrão.  A modificação das configurações de conexão para desabilitar a criptografia é ignorada.

## <a name="authentication"></a>Autenticação
A Autenticação refere-se a como você comprova sua identidade durante a conexão com o banco de dados. Atualmente, o SQL Data Warehouse dá suporte à Autenticação do SQL Server com um nome de usuário e senha, bem como do Azure Active Directory. 

Quando você criou o servidor lógico do banco de dados, especificou um logon de "administrador de servidor" com um nome de usuário e uma senha. Usando essas credenciais, é possível se autenticar em qualquer banco de dados nesse servidor como o proprietário do banco de dados, ou "dbo", por meio da Autenticação do SQL Server.

No entanto, como uma melhor prática, os usuários de sua organização devem usar uma conta diferente para a autenticação. Dessa forma, você pode limitar as permissões concedidas ao aplicativo e reduzir os riscos de atividades mal-intencionadas, caso o código do aplicativo seja vulnerável a um ataque de injeção de SQL. 

Para criar um usuário Autenticado do SQL Server, conecte o banco de dados **mestre** no servidor com o logon de administrador do servidor e crie um novo logon do servidor.  Além disso, é uma boa ideia criar um usuário no banco de dados mestre para os usuários do Azure SQL Data Warehouse. A criação de um usuário mestre permite que um usuário faça logon usando ferramentas, como o SSMS, sem especificar um nome de banco de dados.  Ela também permite que o usuário utilize o pesquisador de objetos para exibir todos os bancos de dados em um SQL Server.

```sql
-- Connect to master database and create a login
CREATE LOGIN ApplicationLogin WITH PASSWORD = 'Str0ng_password';
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Em seguida, conecte o **banco de dados do SQL Data Warehouse** com seu logon de administrador do servidor e crie um usuário do banco de dados com base no logon do servidor que você acabou de criar.

```sql
-- Connect to SQL DW database and create a database user
CREATE USER ApplicationUser FOR LOGIN ApplicationLogin;
```

Se um usuário realizar operações adicionais, como criar logons ou novos bancos de dados, ele também precisará ser atribuído ao `Loginmanager` e às funções `dbmanager` no banco de dados mestre. Para obter mais informações sobre essas funções adicionais e autenticar em um Banco de Dados SQL, consulte [Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure][Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure].  Para obter mais detalhes sobre o Azure AD para SQL Data Warehouse, consulte [Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication][Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication].

## <a name="authorization"></a>Autorização
A Autorização refere-se ao que você pode fazer em um banco de dados do SQL Data Warehouse, e isso é controlado pelas associações e permissões da função da sua conta de usuário. Como uma prática recomendada, você deve conceder aos usuários os privilégios mínimos necessários. O SQL Data Warehouse do Azure facilita o gerenciamento deles com funções no T-SQL:

```sql
EXEC sp_addrolemember 'db_datareader', 'ApplicationUser'; -- allows ApplicationUser to read data
EXEC sp_addrolemember 'db_datawriter', 'ApplicationUser'; -- allows ApplicationUser to write data
```

A conta de administrador do servidor com a qual você está se conectando é um membro de db_owner, que tem autoridade para realizar qualquer tarefa no banco de dados. Salve essa conta para implantar atualizações de esquema e outras operações de gerenciamento. Use a conta "ApplicationUser" com permissões mais limitadas para se conectar do aplicativo ao banco de dados com os privilégios mínimos necessários para seu aplicativo.

Existem maneiras de limitar ainda mais o que um usuário pode fazer com o Banco de Dados SQL do Azure:

* [Permissões][Permissões] granulares possibilitam controlar quais operações você pode fazer em colunas, tabelas, exibições, procedimentos e outros objetos individuais no banco de dados. Use permissões granulares para ter maior controle e conceder as permissões mínimas necessárias. O sistema de permissão granular é um pouco complicado e exige estudo para ser usado com eficiência.
* [Funções de banco de dados][Funções de banco de dados] diferentes de db_datareader e db_datawriter podem ser usadas para criar contas de usuário de aplicativo mais potentes ou contas de gerenciamento menos potentes. As funções internas de banco de dados fixo fornecem uma maneira fácil para conceder permissões, mas podem resultar na concessão de mais permissões do que o necessário.
* [Procedimentos armazenados][Procedimentos armazenados] podem ser usados para limitar as ações que podem ser executadas no banco de dados.

O gerenciamento de bancos de dados e servidores lógicos pelo Portal Clássico do Azure ou usando a API do Gerenciador de Recursos do Azure é controlado pelas atribuições de função da sua conta de usuário. Para obter mais informações sobre esse tópico, consulte [Controle de acesso baseado em função no Portal do Azure][Controle de acesso baseado em função no Portal do Azure].

## <a name="encryption"></a>Criptografia
A TDE (Transparent Data Encryption) do SQL Data Warehouse do Azure ajuda a proteger contra a ameaça de atividades mal-intencionadas por meio da execução de criptografia e descriptografia de seus dados em repouso.  Quando você criptografa seus banco de dados, os arquivos de log de transações e backups associados são criptografados sem exigir nenhuma alteração em seus aplicativos. A TDE criptografa o armazenamento de um banco de dados inteiro usando uma chave simétrica chamada de chave de criptografia de banco de dados. No Banco de Dados SQL, a chave de criptografia do banco de dados está protegida por um certificado de servidor interno. O certificado de servidor interno é exclusivo para cada servidor de Banco de Dados SQL. A Microsoft alterna automaticamente esses certificados pelo menos a cada 90 dias. O algoritmo de criptografia usado pelo SQL Data Warehouse é o AES-256. Para obter uma descrição geral do TDE, consulte [Transparent Data Encryption][Transparent Data Encryption].

Você pode criptografar o banco de dados usando o [Portal do Azure][Criptografia com o Portal] ou o [T-SQL][Criptografia com o TSQL].

## <a name="next-steps"></a>Próximas etapas
Para obter detalhes e exemplos sobre como se conectar ao SQL Data Warehouse com protocolos diferentes, consulte [Conectar-se ao SQL Data Warehouse][Conectar-se ao SQL Data Warehouse].

<!--Image references-->

<!--Article references-->
[Conectar-se ao SQL Data Warehouse]: ./sql-data-warehouse-connect-overview.md
[Criptografia com o Portal]: ./sql-data-warehouse-encryption-tde.md
[Criptografia com o TSQL]: ./sql-data-warehouse-encryption-tde-tsql.md
[Connecting to SQL Data Warehouse By Using Azure Active Directory Authentication]: ./sql-data-warehouse-authentication.md

<!--MSDN references-->
[Firewall do Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/ee621782.aspx
[sp_set_firewall_rule]: https://msdn.microsoft.com/library/dn270017.aspx
[sp_set_database_firewall_rule]: https://msdn.microsoft.com/library/dn270010.aspx
[Funções de banco de dados]: https://msdn.microsoft.com/library/ms189121.aspx
[Gerenciando bancos de dados e logons no Banco de Dados SQL do Azure]: https://msdn.microsoft.com/library/ee336235.aspx
[Permissões]: https://msdn.microsoft.com/library/ms191291.aspx
[Procedimentos armazenados]: https://msdn.microsoft.com/library/ms190782.aspx
[Transparent Data Encryption]: https://msdn.microsoft.com/library/bb934049.aspx
[Portal do Azure]: https://portal.azure.com/

<!--Other Web references-->
[Controle de acesso baseado em função no Portal do Azure]: https://azure.microsoft.com/documentation/articles/role-based-access-control-configure



<!--HONumber=Nov16_HO3-->


