A Atividade de Cópia no Data Factory copia os dados de um repositório de dados de origem para um repositório de dados de coletor. A Data Factory dá suporte aos repositórios de dados a seguir. Os dados de qualquer origem podem ser gravados em qualquer coletor. Clique em um repositório de dados para saber como copiar dados dentro e fora do repositório.

| Categoria | Armazenamento de dados | Tem suporte como origem | Tem suporte como coletor |
|:--- |:--- |:--- |:--- |
| As tabelas |[Armazenamento de Blobs do Azure](../articles/data-factory/data-factory-azure-blob-connector.md) <br/> [Azure Data Lake Store](../articles/data-factory/data-factory-azure-datalake-connector.md) <br/> [Banco de Dados SQL do Azure](../articles/data-factory/data-factory-azure-sql-connector.md) <br/> [SQL Data Warehouse do Azure](../articles/data-factory/data-factory-azure-sql-data-warehouse-connector.md) <br/> [Armazenamento de Tabelas do Azure](../articles/data-factory/data-factory-azure-table-connector.md) <br/> [Azure DocumentDB](../articles/data-factory/data-factory-azure-documentdb-connector.md) <br/> |✓ <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |
| Bancos de dados |[SQL Server](../articles/data-factory/data-factory-sqlserver-connector.md)\* <br/> [Oracle](../articles/data-factory/data-factory-onprem-oracle-connector.md)\* <br/> [MySQL](../articles/data-factory/data-factory-onprem-mysql-connector.md)\* <br/> [DB2](../articles/data-factory/data-factory-onprem-db2-connector.md)\* <br/> [Teradata](../articles/data-factory/data-factory-onprem-teradata-connector.md)\* <br/> [PostgreSQL](../articles/data-factory/data-factory-onprem-postgresql-connector.md)\* <br/> [Sybase](../articles/data-factory/data-factory-onprem-sybase-connector.md)\* <br/>[Cassandra](../articles/data-factory/data-factory-onprem-cassandra-connector.md)\* <br/>[MongoDB](../articles/data-factory/data-factory-on-premises-mongodb-connector.md)\*<br/>[Amazon Redshift](../articles/data-factory/data-factory-amazon-redshift-connector.md) |✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓ <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |✓  <br/> ✓  <br/> &nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp;<br/> &nbsp; <br/>&nbsp; |
| Arquivo |[Sistema de Arquivos](../articles/data-factory/data-factory-onprem-file-system-connector.md)\* <br/> [HDFS](../articles/data-factory/data-factory-hdfs-connector.md)\* <br/> [Amazon S3](../articles/data-factory/data-factory-amazon-simple-storage-service-connector.md) <br/> [FTP](../articles/data-factory/data-factory-ftp-connector.md) |✓ <br/> ✓  <br/> ✓  <br/> ✓  |✓  <br/> &nbsp;<br/>&nbsp; |
| Outros |[Salesforce](../articles/data-factory/data-factory-salesforce-connector.md)<br/> [ODBC Genérico](../articles/data-factory/data-factory-odbc-connector.md)\* <br/> [OData Genérico](../articles/data-factory/data-factory-odata-connector.md) <br/> [Tabela da Web (tabela de HTML)](../articles/data-factory/data-factory-web-table-connector.md) <br/> [GE Historian](../articles/data-factory/data-factory-odbc-connector.md#ge-historian-store)* |✓ <br/> ✓  <br/> ✓  <br/> ✓  <br/> ✓  |&nbsp; <br/> &nbsp; <br/> &nbsp; <br/> &nbsp;<br/> &nbsp;<br/> &nbsp; |

> [!NOTE]
> Os repositórios de dados com * podem estar no local ou no Azure IaaS e exigem a instalação do [Gateway de Gerenciamento de Dados](../articles/data-factory/data-factory-data-management-gateway.md) em um computador Azure IaaS/local.
> 
> 



<!--HONumber=Nov16_HO2-->


