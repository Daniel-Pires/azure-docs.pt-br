---
title: Como implementar o particionamento do lado no cliente com os SDKs | Microsoft Docs
description: "Saiba como usar os SDKs do Banco de Dados de Documentos do Azure para particionar (fragmentar) dados e rotear solicitações em várias coleções"
services: documentdb
author: arramac
manager: jhubbard
editor: cgronlun
documentationcenter: 
ms.assetid: ab2a63f0-4601-42d8-b5e5-ba943319c1c8
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2016
ms.author: arramac
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c31763a9676ec45e493cdecc76997db8fe64c38a


---
# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>Como particionar dados usando o suporte do lado do cliente no Banco de Dados de Documentos
O Banco de Dados de Documentos do Azure dá suporte para o [particionamento automático das coleções](documentdb-partition-data.md). No entanto, há casos de uso em que é útil ter um controle refinado sobre o comportamento de particionamento. Para reduzir o código clichê necessário para tarefas de particionamento, adicionamos funcionalidades aos SDKs .NET, Node.js e Java que facilitam a criação de aplicativos que serão escalados verticalmente por várias coleções.

Neste artigo, vamos dar uma olhada nas classes e interfaces no SDK do .NET e como usá-los para desenvolver aplicativos particionados. Outros SDKs como Java, Node.js e Python dão suporte a métodos e a interfaces semelhantes para o particionamento no lado cliente.

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>Particionamento do lado do cliente com o SDK do Banco de Dados de Documentos
Antes de nos aprofundarmos mais no particionamento, vamos recapitular alguns conceitos básicos do Banco de Dados de Documentos relacionados ao particionamento. Cada conta de banco de dados do Banco de Dados de Documentos do Azure é formada por um conjunto de bancos de dados, cada um contendo diversas coleções, cada uma delas podendo conter procedimentos armazenados, gatilhos, UDFs, documentos e anexos relacionados. As coleções podem ter uma partição ou podem particionar a si próprias e ter as seguintes propriedades:

* As coleções de oferecem isolamento de desempenho. Portanto, há um benefício de desempenho em agrupar documentos semelhantes dentro da mesma coleção. Por exemplo, para dados de série temporal, você pode querer colocar dados para o último mês, que frequentemente são consultados, dentro de uma coleção com uma taxa de transferência maior provisionada, e colocar os dados mais antigos em de coleções com uma taxa de transferência menor provisionada.
* Transações ACID, ou seja, procedimentos armazenados e gatilhos não podem abranger uma coleção. O escopo das transações é definido para um único valor de chave de partição dentro de uma coleção.
* As coleções não impõem um esquema, portanto, podem ser usadas para documentos JSON do mesmo tipo ou tipos diferentes.

A partir da versão [1.5.x dos SDKs do DocumentDB do Azure](documentdb-sdk-dotnet.md), você pode executar operações de documentos diretamente em um banco de dados. Internamente, o [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) usa o PartitionResolver que você especificou para o banco de dados para rotear solicitações para a coleção apropriada.

> [!NOTE]
> [Particionamento do lado do servidor](documentdb-partition-data.md) introduzido na API REST 2015-12-16 e nos SDKs 1.6.0 ou posteriores, substitui a abordagem de resolução da partição do lado do cliente para casos de uso simples. No entanto, o particionamento do lado do cliente é mais flexível e permite controlar o isolamento de desempenho em chaves de partição, controlar o grau de paralelismo durante a leitura de resultados de várias partições e usar as abordagens de particionamento espacial/de intervalo versus hash.
> 
> 

Por exemplo, no .NET, cada classe PartitionResolver é uma implementação concreta de uma interface do [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) que tem três métodos - [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) e [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Consultas LINQ e iteradores ReadFeed usam internamente o método ResolveForRead para iterar todas as coleções que correspondem à chave de partição para a solicitação. Da mesma forma, operações de criação usam o método ResolveForCreate para rotear criações à partição correta. Nenhuma alteração é necessária para Substituir, Excluir e Ler, uma vez que essas opções usam documentos que já contêm a referência à coleção correspondente.

Os SDKs também incluem duas classes que dão suporte a duas técnicas de particionamento canônicas, hash e consultas de intervalo, por meio de [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) e [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). Você pode usar essas classes adicionar facilmente uma lógica de particionamento para seu aplicativo.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>Adicionar lógica de particionamento e registrar o PartitionResolver
Aqui está um trecho de código que mostra como criar um [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) e registrar com o DocumentClient para um banco de dados.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>Criar documentos em uma partição
Quando o PartitionResolver estiver registrado, você pode executar criações e consultas diretamente no banco de dados, conforme mostrado abaixo. Neste exemplo, o SDK usa o PartitionResolver para extrair o UserId, executar hash e, em seguida, usar esse valor para rotear a operação de criação para a coleção correta.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>Criar consultas em partições
Você pode consultar usando o método [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) passando-o no banco de dados e em uma chave de partição. A consulta retorna um único conjunto de resultados em todas as coleções no banco de dados que é mapeado para a chave de partição.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>Criar consultas em todas as coleções no banco de dados
Você também pode consultar todas as coleções no banco de dados e enumerar os resultados como mostrado abaixo, ignorando o argumento da chave de partição.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>Resolvedor de partição hash
No particionamento hash, as partições são atribuídas com base no valor de uma função hash, permitindo distribuir uniformemente solicitações e dados entre várias partições. Essa abordagem costuma ser usada para particionar dados produzidos ou consumidos de um grande número de clientes diferentes e é útil para armazenar perfis de usuários, itens de catálogo e dados de telemetria de IoT (Internet das Coisas). O particionamento hash também é usado pelo suporte ao particionamento do lado do servidor do Banco de Dados de Documentos em uma coleção.

**Hash Partitioning:**
![diagrama ilustrando como o particionamento hash distribui igualmente as solicitações nas partições](media/documentdb-sharding/partition-hash.png)

Um esquema de particionamento hash simples nas coleções *N* seria usar qualquer documento e computar *hash(d) mod N* para determinar em qual coleção ele está posicionado. Mas um problema com essa técnica simples é que ela não funciona bem ao adicionar novas coleções, ou remover coleções, pois isso exigiria que quase todos os dados fossem embaralhados novamente. [hash consistente](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) é um algoritmo bem conhecido que soluciona isso ao implementar um esquema de hash que minimiza a quantidade de movimento de dados necessário durante a adição ou remoção de coleções.

A classe [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implementa a lógica para criar um anel de hash consistente sobre a função de hash especificada na interface [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). Por padrão, o HashPartitionResolver usa uma função de hash MD5, mas você pode trocá-la por sua própria implementação de hash. O HashPartitionResolver cria internamente 16 hashes ou "nós virtuais" dentro do anel de hash para cada coleção para alcançar uma distribuição mais uniforme de documentos entre as coleções, mas esse número pode variar para compensar a distorção de dados com a quantidade de computação do lado do cliente.

**Hash consistente com HashPartitionResolver:**
![diagrama ilustrando como HashPartitionResolver cria um anel de hash](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>Resolvedor de partição de intervalo
No particionamento por intervalos, as partições são atribuídas com base no intervalo em que se encontra a chave de partição. Normalmente, ele é usado para fazer o particionamento com propriedades de carimbo de data/hora (por exemplo, eventTime entre 1º de abril de 2015 e 14 de abril de 2015). A classe [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) ajuda a manter um mapeamento entre Range\<T\> e um vínculo automático da coleção. 

[Range\<T\>](https://msdn.microsoft.com/library/azure/mt126048.aspx) é uma classe simples que gerencia intervalos de qualquer tipo que implementam IComparable\<T\> e IEquatable\<T\>, como sequências de caracteres ou números. Para leituras e criações, você pode passar qualquer intervalo arbitrário e o resolvedor identifica todas as coleções possíveis, identificando os intervalos das partições que fazem interseção com o intervalo solicitado. Essa funcionalidade pode ser útil ao executar consultas de intervalo em dados de série temporal.

**Particionamento por intervalos:**  

![ Diagrama ilustrando como o particionamento de intervalos distribui igualmente as solicitações nas partições](media/documentdb-sharding/partition-range.png)  

Um caso especial de particionamento por intervalos é quando o intervalo é de apenas um único valor discreto, às vezes chamado de "particionamento de pesquisa". Normalmente, isso é usado para particionar por região (por exemplo, a partição da Escandinávia contém Noruega, Dinamarca e Suécia) ou para particionar locatários em um aplicativo multilocatários.

## <a name="samples"></a>Exemplos
Dê uma olhada no [Projeto Github de exemplos de particionamento do DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning), contendo trechos de código sobre como usar esses PartitionResolvers e estendê-los para implementar seus próprios resolvedores para se adaptar a casos de uso específicos, como o seguinte: 

* Como especificar uma expressão lambda arbitrária para GetPartitionKey e usá-la para implementar chaves compostas de particionamento ou para particionar diferentes tipos de objetos de maneira diferente.
* Como criar um [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) simples que usa uma tabela de pesquisa manual para executar o particionamento. Esse padrão geralmente é usado para particionar com base nos valores discretos como região, ID de locatário ou nome do aplicativo.
* Como criar um [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) que cria coleções automaticamente com base em um modelo que define um esquema de nomeação, IndexingPolicy e procedimentos armazenados que precisam ser registrados em novas coleções.
* Como criar um [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) sem esquema que simplesmente cria novas coleções conforme as coleções antigas são preenchidas.
* Como serializar e desserializar o estado PartitionResolver como JSON, para que você possa compartilhar entre processos e desligamentos. Você pode persisti-los em arquivos de configuração, ou mesmo em uma coleção do Banco de Dados de Documentos.
* Uma classe [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) para adicionar e remover partições dinamicamente em um banco de dados particionado com base em hash consistente. Ele usa internamente um [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) para rotear leituras e gravações durante a migração usando um dos quatro modos - leitura do antigo esquema de particionamento (ReadCurrent), do novo (ReadNext), mesclar resultados de ambos (ReadBoth) ou ficar indisponível durante a migração (None).

Os exemplos são de software livre e nós o encorajamos a enviar solicitações pull com contribuições que poderiam beneficiar outros desenvolvedores do Banco de Dados de Documentos. Consulte as [Diretrizes de contribuição](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obter instruções sobre como contribuir.  

> [!NOTE]
> As criações da coleção têm sua taxa limitada pelo Banco de Dados de Documentos, por isso, alguns dos métodos de exemplo mostrados aqui podem levar alguns minutos para serem concluídos.
> 
> 

## <a name="faq"></a>Perguntas frequentes
**O Banco de Dados de Documentos tem suporte para particionamento no lado do servidor?**

Sim, o Banco de Dados de Documentos dá suporte ao [particionamento do lado servidor](documentdb-partition-data.md). O Banco de Dados de Documentos também dá suporte para particionamento do lado do cliente por meio de resolvedores de partição do lado do cliente para casos de uso mais avançados.

**Quando devo usar o particionamento do lado do servidor em relação ao particionamento do lado do cliente?**
 Para a maioria dos casos de uso, recomendamos usar o particionamento do lado do servidor, uma vez que ele lida com as tarefas administrativas de particionar dados e rotear solicitações. No entanto, se você precisar de particionamento por intervalos ou tiver um caso de uso especializado para isolamento de desempenho entre os diferentes valores de chaves de partição, particionamento do lado do cliente pode ser a melhor abordagem.

**Como adicionar ou remover uma coleção ao meu esquema de particionamento?**

Observe a implementação do DocumentClientHashPartitioningManager no projeto de exemplos para obter um exemplo de como você pode implementar o reparticionamento.

**Como persistir ou compartilhar minha configuração de particionamento com outros clientes?**

Você pode serializar o estado do particionador como JSON e armazená-lo em arquivos de configuração, ou mesmo dentro de coleções do Banco de Dados de Documentos. Observe o método RunSerializeDeserializeSample no projeto de exemplos para obter um exemplo.

**Como posso encadear várias técnicas de particionamento?**

É possível encadear PartitionResolvers implementando seu próprio IPartitionResolver que usa internamente um ou mais resolvedores existentes. Observe o TransitionHashPartitionResolver no projeto de exemplos para obter um exemplo.

## <a name="references"></a>Referências
* [Particionamento do lado servidor no Banco de Dados de Documentos](documentdb-partition-data.md)
* [Coleções e níveis de desempenho do Banco de Dados de Documentos](documentdb-performance-levels.md)
* [Exemplos de código de particionamento no Github](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Documentação do SDK .NET do Banco de Dados de Documentos no MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Amostras do .NET do Banco de Dados de Documentos](https://github.com/Azure/azure-documentdb-net)
* [Limites do Banco de Dados de Documentos](documentdb-limits.md)
* [Blog do Banco de Dados de Documentos sobre dicas de desempenho](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)




<!--HONumber=Nov16_HO3-->


