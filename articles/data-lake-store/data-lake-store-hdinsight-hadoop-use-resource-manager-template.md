---
title: Criar clusters HDInsight com o Azure Data Lake Store usando Modelos do Resource Manager | Microsoft Docs
description: Usar modelos do Resource Manager do Azure para criar e usar clusters HDInsight com o Azure Data Lake Store
services: data-lake-store,hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 8ef8152f-2121-461e-956c-51c55144919d
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/18/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 3f8c9b22fb9a7aae97c43e39fe82a610f6b8b374
ms.openlocfilehash: 2d3415c654307b50420abe1e7e61938c91088552


---
# <a name="create-an-hdinsight-cluster-with-data-lake-store-using-azure-resource-manager-template"></a>Criar o cluster HDInsight com o Data Lake Store usando o modelo do Resource Manager do Azure
> [!div class="op_single_selector"]
> * [Usando o Portal](data-lake-store-hdinsight-hadoop-use-portal.md)
> * [Usando o PowerShell](data-lake-store-hdinsight-hadoop-use-powershell.md)
> * [Usando o Gerenciador de Recursos](data-lake-store-hdinsight-hadoop-use-resource-manager-template.md)
>

Aprenda a usar um modelo do Azure Resource Manager para configurar um cluster HDInsight com acesso ao Azure Data Lake Store. Para tipos de cluster compatíveis, o Data Lake Store pode ser usado como um armazenamento padrão ou uma conta de armazenamento adicional. Quando o Data Lake Store é usado como armazenamento adicional, a conta de armazenamento padrão para os clusters ainda será Blobs de Armazenamento do Azure (WASB) e os arquivos relacionados ao cluster (como logs, etc.) ainda serão gravados no armazenamento padrão, embora os dados que você queira processar possam ser armazenados em uma conta do Data Lake Store. O uso do Repositório Data Lake como uma conta de armazenamento adicional não afeta o desempenho ou a capacidade de leitura/gravação no armazenamento do cluster.

Algumas considerações importantes:

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento padrão está disponível para o HDInsight versão 3.5.

* A opção para criar clusters HDInsight com acesso ao Data Lake Store como armazenamento adicional está disponível para o HDInsight versões 3.2, 3.4 e 3.5.

* Para clusters HBase (Windows e Linux), o Data Lake Store **não é compatível** como uma opção de armazenamento, tanto para armazenamento padrão quanto para armazenamento adicional.


Neste artigo, provisionaremos um cluster Hadoop com o Repositório Data Lake como armazenamento adicional. Para obter instruções sobre como criar um cluster Hadoop com o Data Lake Store como armazenamento padrão, veja [Criar um cluster HDInsight com o Data Lake Store usando o Portal do Azure](data-lake-store-hdinsight-hadoop-use-portal.md).

## <a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* **Uma assinatura do Azure**. Consulte [Obter avaliação gratuita do Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou superior**. Consulte [Como instalar e configurar o PowerShell do Azure](../powershell-install-configure.md).
* **Entidade de serviço do Azure Active Directory**. As etapas neste tutorial fornecem instruções sobre como criar uma entidade de serviço no Azure AD. No entanto, você deve ser administrador do Azure AD para poder criar uma entidade de serviço. Se você for administrador do Azure AD, poderá ignorar esse pré-requisito e continuar com o tutorial.

    **Se você não for um administrador do Azure AD**, não poderá executar as etapas necessárias para criar uma entidade de serviço. Nesse caso, o administrador do Azure AD deverá primeiro criar uma entidade de serviço antes de criar um cluster HDInsight com Data Lake Store. Além disso, a entidade de serviço deve ser criada usando um certificado, conforme descrito em [Criar uma entidade de serviço com certificado](../resource-group-authenticate-service-principal.md#create-service-principal-with-certificate).

## <a name="create-an-hdinsight-cluster-with-azure-data-lake-store"></a>Criar um cluster HDInsight com o Azure Data Lake Store
O modelo do Resource Manager e os pré-requisitos para usar o modelo estão disponíveis no GitHub em [Implantar um cluster HDInsight Linux com o novo Data Lake Store](https://github.com/Azure/azure-quickstart-templates/tree/master/201-hdinsight-datalake-store-azure-storage). Siga as instruções fornecidas neste link para criar um cluster HDInsight com o Azure Data Lake Store como o armazenamento adicional.

As instruções no link mencionado acima exigem o PowerShell. Antes de iniciar as instruções, verifique se você está conectado com sua conta do Azure. Na área de trabalho, abra uma nova janela do Azure PowerShell e insira o trecho a seguir. Quando solicitado a fazer logon, lembre-se de fazer logon como o proprietário ou um dos administradores da assinatura:

```
# Log in to your Azure account
Login-AzureRmAccount

# List all the subscriptions associated to your account
Get-AzureRmSubscription

# Select a subscription
Set-AzureRmContext -SubscriptionId <subscription ID>
```

## <a name="upload-sample-data-to-the-azure-data-lake-store"></a>Carregue os exemplos de dados no Azure Data Lake Store
O modelo do Resource Manager cria uma nova conta do Data Lake Store e associa-a com o cluster HDInsight. Você deve carregar alguns dados de exemplo no Data Lake Store. Você precisará desses dados posteriormente no tutorial para executar trabalhos de um cluster HDInsight que acessa dados no repositório Data Lake. Para obter instruções sobre como carregar dados, consulte [Carregar um arquivo no Data Lake Store](data-lake-store-get-started-portal.md#uploaddata). Se estiver procurando alguns dados de exemplo para carregar, é possível obter a pasta **Dados da Ambulância** no [Repositório Git do Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData).

## <a name="set-relevant-acls-on-the-sample-data"></a>Definir ACLs relevantes nos dados de exemplo
Para verificar se os dados de exemplo que você carregou estão acessíveis do cluster HDInsight, você deverá garantir que o aplicativo do Azure AD que é usado para estabelecer a identidade entre o cluster HDInsight e o Data Lake Store tem acesso para o arquivo/pasta que você está tentando acessar. Para fazer isso, execute as seguintes etapas.

1. Localize o nome do aplicativo do Azure AD que está associado ao cluster HDInsight e o Data Lake Store. Uma maneira para procurar o nome é abrir a folha do cluster HDInsight que você criou usando o modelo do Resource Manager, clique na guia **Identidade do Cluster AAD** e procure o valor do **Nome de exibição da entidade de serviço**.
2. Agora, forneça acesso a esse aplicativo do Azure AD no arquivo/pasta que você deseja acessar do cluster HDInsight. Para definir as ACLs certas no arquivo/pasta no Data Lake Store, consulte [Protegendo dados no Data Lake Store](data-lake-store-secure-data.md#filepermissions).

## <a name="run-test-jobs-on-the-hdinsight-cluster-to-use-the-data-lake-store"></a>Executar trabalhos de teste no cluster HDInsight para usar o Repositório Data Lake
Após a configuração de um cluster HDInsight, você poderá executar trabalhos de teste no cluster a fim de testar se o cluster HDInsight pode acessar o Repositório Data Lake. Para fazer isso, executaremos um exemplo de trabalho do Hive que cria uma tabela usando os exemplos de dados que você carregou anteriormente em seu Repositório Data Lake.

### <a name="for-a-linux-cluster"></a>Para um cluster do Linux
Nesta seção, você acessará o cluster por SSH e executará uma consulta Hive de exemplo. O Windows não fornece um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para saber mais sobre a utilização do PuTTY, confira [Usar SSH com o Hadoop baseado em Linux no HDInsight no Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

1. Uma vez conectado, inicie a CLI do Hive usando o seguinte comando:

   ```
   hive
   ```
2. Usando a CLI, insira as instruções a seguir para criar uma nova tabela chamada **vehicles** usando os dados de exemplo no Repositório Data Lake:

   ```
   DROP TABLE vehicles;
   CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://<mydatalakestore>.azuredatalakestore.net:443/';
   SELECT * FROM vehicles LIMIT 10;
   ```

   Você deverá ver um resultado semelhante ao seguinte:

   ```
   1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
   1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
   1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
   1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
   1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
   1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
   1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
   1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
   1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
   1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
   ```

### <a name="for-a-windows-cluster"></a>Para um cluster do Windows
Use os seguintes cmdlets para executar a consulta do Hive. Nessa consulta criamos uma tabela a partir dos dados no Repositório Data Lake e executamos uma consulta seleção na tabela criada.

```
$queryString = "DROP TABLE vehicles;" + "CREATE EXTERNAL TABLE vehicles (str string) LOCATION 'adl://$dataLakeStoreName.azuredatalakestore.net:443/';" + "SELECT * FROM vehicles LIMIT 10;"

$hiveJobDefinition = New-AzureRmHDInsightHiveJobDefinition -Query $queryString

$hiveJob = Start-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobDefinition $hiveJobDefinition -ClusterCredential $httpCredentials

Wait-AzureRmHDInsightJob -ResourceGroupName $resourceGroupName -ClusterName $clusterName -JobId $hiveJob.JobId -ClusterCredential $httpCredentials
```

Esta será a saída. **ExitValue** com valor 0 na saída sugere que o trabalho foi concluído com êxito.

```
Cluster         : hdiadlcluster.
HttpEndpoint    : hdiadlcluster.azurehdinsight.net
State           : SUCCEEDED
JobId           : job_1445386885331_0012
ParentId        :
PercentComplete :
ExitValue       : 0
User            : admin
Callback        :
Completed       : done
```

Recupere a saída do trabalho usando o seguinte cmdlet:

```
Get-AzureRmHDInsightJobOutput -ClusterName $clusterName -JobId $hiveJob.JobId -DefaultContainer $containerName -DefaultStorageAccountName $storageAccountName -DefaultStorageAccountKey $storageAccountKey -ClusterCredential $httpCredentials
```

A saída é semelhante ao seguinte:

```
1,1,2014-09-14 00:00:03,46.81006,-92.08174,51,S,1
1,2,2014-09-14 00:00:06,46.81006,-92.08174,13,NE,1
1,3,2014-09-14 00:00:09,46.81006,-92.08174,48,NE,1
1,4,2014-09-14 00:00:12,46.81006,-92.08174,30,W,1
1,5,2014-09-14 00:00:15,46.81006,-92.08174,47,S,1
1,6,2014-09-14 00:00:18,46.81006,-92.08174,9,S,1
1,7,2014-09-14 00:00:21,46.81006,-92.08174,53,N,1
1,8,2014-09-14 00:00:24,46.81006,-92.08174,63,SW,1
1,9,2014-09-14 00:00:27,46.81006,-92.08174,4,NE,1
1,10,2014-09-14 00:00:30,46.81006,-92.08174,31,N,1
```

## <a name="access-data-lake-store-using-hdfs-commands"></a>Repositório Data Lake usando comandos do HDFS
Após a configuração do cluster HDInsight para usar o Repositório Data Lake, você poderá usar os comandos do shell HDFS para acessar o armazenamento.

### <a name="for-a-linux-cluster"></a>Para um cluster do Linux
Nesta seção, você acessará o cluster por SSH e executará os comandos HDFS. O Windows não fornece um cliente SSH integrado. É recomendável usar o **PuTTY**, que pode ser baixado de [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

Para saber mais sobre a utilização do PuTTY, confira [Usar SSH com o Hadoop baseado em Linux no HDInsight no Windows ](../hdinsight/hdinsight-hadoop-linux-use-ssh-windows.md).

Uma vez conectado, use o comando do sistema de arquivos HDFS a seguir para listar os arquivos no Repositório Data Lake.

```
hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
```

Isso deve listar o arquivo carregado anteriormente no Repositório Data Lake.

```
15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
Found 1 items
-rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/mynewfolder
```

Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Repositório Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

### <a name="for-a-windows-cluster"></a>Para um cluster do Windows
1. Entre no novo [Portal do Azure](https://portal.azure.com).
2. Clique em **Procurar**, em **Clusters HDInsight** e clique no cluster HDInsight que você criou.
3. Na folha do cluster, clique em **Área de Trabalho Remota** e na folha **Área de Trabalho Remota**, clique em **Conectar**.

   ![Remoto em cluster HDI](./media/data-lake-store-hdinsight-hadoop-use-powershell/ADL.HDI.PS.Remote.Desktop.png)

   Quando receber a solicitação, insira as credenciais fornecidas para o usuário da área de trabalho remota.
4. Na sessão remota, inicie o Windows PowerShell e use os comandos do sistema de arquivos HDFS para listar os arquivos no Repositório Data Lake do Azure.

   ```
   hdfs dfs -ls adl://<Data Lake Store account name>.azuredatalakestore.net:443/
   ```

   Isso deve listar o arquivo carregado anteriormente no Repositório Data Lake.

   ```
   15/09/17 21:41:15 INFO web.CaboWebHdfsFileSystem: Replacing original urlConnectionFactory with org.apache.hadoop.hdfs.web.URLConnectionFactory@21a728d6
   Found 1 items
   -rwxrwxrwx   0 NotSupportYet NotSupportYet     671388 2015-09-16 22:16 adl://mydatalakestore.azuredatalakestore.net:443/vehicle1_09142014.csv
   ```

   Você também pode usar o comando `hdfs dfs -put` para carregar alguns arquivos no Repositório Data Lake e usar `hdfs dfs -ls` para verificar se os arquivos foram carregados com êxito.

## <a name="next-steps"></a>Próximas etapas
* [Copiar dados de Armazenamento de Blobs do Azure para o Data Lake Store](data-lake-store-copy-data-wasb-distcp.md)



<!--HONumber=Nov16_HO4-->


