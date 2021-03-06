---
title: "Gerenciar potência de computação no SQL Data Warehouse do Azure (PowerShell) | Microsoft Docs"
description: "Tarefas do PowerShell para gerenciar o poder de computação. Dimensionar recursos de computação ajustando as DWUs. Ou, para economizar custos, pause e retome os recursos de computação."
services: sql-data-warehouse
documentationcenter: NA
author: barbkess
manager: jhubbard
editor: 
ms.assetid: 8354a3c1-4e04-4809-933f-db414a8c74dc
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 10/31/2016
ms.author: barbkess
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 32e48964cb8b6dabac74d0f07e04a151ab444728


---
# <a name="manage-compute-power-in-azure-sql-data-warehouse-powershell"></a>Gerenciar poder de computação no SQL Data Warehouse do Azure (PowerShell)
> [!div class="op_single_selector"]
> * [Visão geral](sql-data-warehouse-manage-compute-overview.md)
> * [Portal](sql-data-warehouse-manage-compute-portal.md)
> * [PowerShell](sql-data-warehouse-manage-compute-powershell.md)
> * [REST](sql-data-warehouse-manage-compute-rest-api.md)
> * [TSQL](sql-data-warehouse-manage-compute-tsql.md)
> 
> 

Dimensionar o desempenho escalando horizontalmente recursos de computação e memória para atender às demandas de mudança de sua carga de trabalho. Economizar custos reduzindo recursos durante horários que não sejam de pico ou pausando a computação.

Esta coleção de tarefas usa o portal do Azure para:

* Computação de escala
* Pausar computação
* Retomar a computação

Para saber mais sobre isso, consulte [Visão geral sobre gerenciar computação][Visão geral sobre gerenciar computação].

## <a name="before-you-begin"></a>Antes de começar
### <a name="install-the-latest-version-of-azure-powershell"></a>Instale a versão mais recente do Azure PowerShell
> [!NOTE]
> Para usar o Azure PowerShell com o SQL Data Warehouse, você precisa instalar a versão 1.0.3 ou superior do Azure PowerShell.  Para verificar a versão atual, execute o comando **Get-Module -ListAvailable -Name Azure**. Você pode instalar a versão mais recente do [Microsoft Web Platform Installer][Microsoft Web Platform Installer].  Para saber mais sobre como instalar a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell][Como instalar e configurar o Azure PowerShell].
> 
> 

### <a name="get-started-with-azure-powershell-cmdlets"></a>Introdução aos cmdlets do Azure PowerShell
Introdução:

1. Abra o PowerShell do Azure.
2. No prompt do PowerShell, execute estes comandos para entrar no Azure Resource Manager e selecione sua assinatura.
   
    ```PowerShell
    Login-AzureRmAccount
    Get-AzureRmSubscription
    Select-AzureRmSubscription -SubscriptionName "MySubscription"
    ```

<a name="scale-performance-bk"></a>
<a name="scale-compute-bk"></a>

## <a name="scale-compute-power"></a>Dimensionar poder de computação
[!INCLUDE [SQL Data Warehouse scale DWUs description](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para alterar DWUs, use o cmdlet do PowerShell [Set-AzureRmSqlDatabase][Set-AzureRmSqlDatabase]. O exemplo a seguir define o objetivo de nível de serviço como DW1000 para o banco de dados MySQLDW, que está hospedado no servidor MyServer.

```Powershell
Set-AzureRmSqlDatabase -DatabaseName "MySQLDW" -ServerName "MyServer" -RequestedServiceObjectiveName "DW1000"
```

<a name="pause-compute-bk"></a>

## <a name="pause-compute"></a>Pausar computação
[!INCLUDE [SQL Data Warehouse pause description](../../includes/sql-data-warehouse-pause-description.md)]

Para pausar um banco de dados, use o cmdlet [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. O exemplo a seguir pausa um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

> [!NOTE]
> Observe que, se o servidor for foo.database.windows.net, use "foo" como o -ServerName nos cmdlets do PowerShell.
> 
> 

```Powershell
Suspend-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
```
Uma variação, o próximo exemplo recupera o banco de dados para o objeto $database. Ele redireciona o objeto para [Suspend-AzureRmSqlDatabase][Suspend-AzureRmSqlDatabase]. Os resultados são armazenados no objeto resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Suspend-AzureRmSqlDatabase
$resultDatabase
```

<a name="resume-compute-bk"></a>

## <a name="resume-compute"></a>Retomar a computação
[!INCLUDE [SQL Data Warehouse resume description](../../includes/sql-data-warehouse-resume-description.md)]

Para iniciar um banco de dados, use o cmdlet [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase]. O exemplo a seguir inicia um banco de dados chamado Database02 hospedado em um servidor chamado Server01. O servidor está em um grupo de recursos do Azure chamado ResourceGroup1.

```Powershell
Resume-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" -DatabaseName "Database02"
```

Uma variação, o próximo exemplo recupera o banco de dados para o objeto $database. Ele então redireciona o objeto [Resume-AzureRmSqlDatabase][Resume-AzureRmSqlDatabase] e armazena os resultados em $resultDatabase. O comando final mostra os resultados.

```Powershell
$database = Get-AzureRmSqlDatabase –ResourceGroupName "ResourceGroup1" `
–ServerName "Server01" –DatabaseName "Database02"
$resultDatabase = $database | Resume-AzureRmSqlDatabase
$resultDatabase
```

<a name="next-steps-bk"></a>

## <a name="next-steps"></a>Próximas etapas
Para outras tarefas de gerenciamento, consulte [Visão geral de gerenciamento][Visão geral de gerenciamento].

<!--Image references-->

<!--Article references-->
[Limites de capacidade do serviço]: ./sql-data-warehouse-service-capacity-limits.md
[Visão geral de gerenciamento]: ./sql-data-warehouse-overview-manage.md
[Como instalar e configurar o Azure PowerShell]: ../powershell-install-configure.md
[Visão geral sobre gerenciar computação]: ./sql-data-warehouse-manage-compute-overview.md

<!--MSDN references-->
[Resume-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619347.aspx
[Suspend-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619337.aspx
[Set-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619433.aspx

<!--Other Web references-->
[Microsoft Web Platform Installer]: https://aka.ms/webpi-azps
[Portal do Azure]: http://portal.azure.com/



<!--HONumber=Nov16_HO3-->


