| Recurso | Limite padrão |
| --- | --- |
| Número máximo de contas de armazenamento por assinatura |200<sup>1</sup> |
| TB por conta de armazenamento |500 TB |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento |O único limite é a capacidade de conta de armazenamento de 500 TB |
| Tamanho máximo de um único contêiner de blob, tabela ou fila |500 TB |
| Número máximo de blocos em um blob de blocos ou de acréscimo |50\.000 |
| Tamanho máximo de um bloco em um blob de blocos ou de acréscimo |4 MB |
| Tamanho máximo de um blob de blocos ou de acréscimo |50\.000 x 4 MB (aproximadamente 195 GB) |
| Tamanho máximo de um blob de páginas |1 TB |
| Tamanho máximo de uma entidade de tabela |1 MB |
| Número máximo de propriedades em uma entidade de tabela |252 |
| Tamanho máximo de uma mensagem em uma fila |64 KB |
| Tamanho máximo de um compartilhamento de arquivo |5 TB |
| Tamanho máximo de um arquivo em um compartilhamento de arquivos |1 TB |
| Número máximo de arquivos em um compartilhamento de arquivo |O único limite é a capacidade total de 5 TB do compartilhamento de arquivo |
| Máx. de 8 KB de IOPS por compartilhamento |1000 |
| Número máximo de arquivos em um compartilhamento de arquivo |O único limite é a capacidade total de 5 TB do compartilhamento de arquivo |
| Número máximo de contêineres de blob, blobs, compartilhamentos de arquivo, tabelas, filas, entidades ou mensagens por conta de armazenamento |O único limite é a capacidade de conta de armazenamento de 500 TB |
| Número máximo de políticas de acesso armazenada por contêiner, compartilhamento de arquivos, tabela ou fila |5 |
| Taxa de solicitação total (presumindo um tamanho de objeto de 1KB) por conta de armazenamento |Até 20.000 IOPS, entidades por segundo ou mensagens por segundo |
| Taxa de transferência de destino para blob único |Até 60 MB por segundo, ou até 500 solicitações por segundo |
| Taxa de transferência de destino para fila única (mensagens de 1 KB) |Até 2000 mensagens por segundo |
| Meta de taxa de transferência para partição de tabela única (entidades de 1 KB) |Até 2000 entidades por segundo |
| Taxa de transferência de destino para compartilhamento de arquivo único |Até 60 MB por segundo |
| Entrada máxima<sup>2</sup> por conta de armazenamento (regiões dos EUA) |10 Gbps se o GRS/ZRS<sup>3</sup> estiver habilitado, 20 Gbps para o LRS |
| Saída máxima<sup>2</sup> por conta de armazenamento (regiões dos EUA) |20 Gbps se o RA-GRS/GRS/ZRS<sup>3</sup> estiver habilitado, 30 Gbps para o LRS |
| Entrada máxima<sup>2</sup> por conta de armazenamento (regiões da Ásia e Europa) |5 Gbps se o GRS/ZRS<sup>3</sup> estiver habilitado, 10 Gbps para o LRS |
| Saída máxima<sup>2</sup> por conta de armazenamento (regiões da Ásia e Europa) |10 Gbps se o RA-GRS/GRS/ZRS<sup>3</sup> estiver habilitado, 15 Gbps para o LRS |

<sup>1</sup>Isso inclui contas de armazenamento Standard e Premium. Se você precisar de mais de 200 contas de armazenamento, faça uma solicitação por meio do [Suporte do Azure](https://azure.microsoft.com/support/faq/). A equipe de Armazenamento do Azure examinará seu caso de negócios e poderá aprovar até 250 contas de armazenamento.

<sup>2</sup>*Entrada* refere-se a todos os dados (solicitações) que estão sendo enviados a uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) recebidos de uma conta de armazenamento.

<sup>3</sup>As opções de replicação do Armazenamento do Azure incluem:

* **RA-GRS**: armazenamento com redundância geográfica com acesso de leitura. Se o RA-GRS estiver habilitado, os destinos de saída para o local secundário serão idênticos àqueles para o local principal.
* **GRS**: armazenamento com redundância geográfica.
* **ZRS**: armazenamento com redundância de zona. Disponível apenas para blobs de blocos.
* **LRS**: armazenamento com redundância local.

<!---HONumber=AcomDC_0810_2016-->