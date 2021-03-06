---
title: Desenvolver topologias baseadas em Java para o Apache Storm | Microsoft Docs
description: Aprenda a criar topologias Storm em Java criando uma topologia de contagem de palavras simples.
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: a8838f29-9c08-4fd9-99ef-26655d1bf6d7
ms.service: hdinsight
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/14/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 29dc6cec795107c447db08221867adb6e3a24f29


---
# <a name="develop-java-based-topologies-for-a-basic-word-count-application-with-apache-storm-and-maven-on-hdinsight"></a>Desenvolver topologias baseadas em Java para um aplicativo básico de contagem de palavras com Apache Storm e Maven no HDInsight
Aprenda como criar uma topologia baseada em Java para o Apache Storm no HDInsight usando o Maven. Você percorrerá o processo de criação de um aplicativo de contagem de palavras básico usando Maven e Java, em que a topologia é definida em Java. Em seguida, você aprenderá como definir a topologia usando a estrutura Flux.

> [!NOTE]
> A estrutura Flux está disponível no Storm 0.10.0 ou posterior. O Storm 0.10.0 está disponível com o HDInsight 3.3 e 3.4.
> 
> 

Depois de concluir as etapas neste documento, você terá uma topologia básica que pode implantar para o Apache Storm no HDInsight.

> [!NOTE]
> uma versão completa das topologias criadas neste documento está disponível em [https://github.com/Azure-Samples/hdinsight-java-storm-wordcount](https://github.com/Azure-Samples/hdinsight-java-storm-wordcount).
> 
> 

## <a name="prerequisites"></a>Pré-requisitos
* <a href="https://www.oracle.com/technetwork/java/javase/downloads/jdk7-downloads-1880260.html" target="_blank">Java Developer Kit (JDK) versão 7</a>
* <a href="https://maven.apache.org/download.cgi" target="_blank">Maven</a>: o Maven é um sistema de criação de projetos para projetos Java.
* Um editor de texto, como o Bloco de Notas, o <a href="http://www.gnu.org/software/emacs/" target="_blank">Emacs<a>, o <a href="http://www.sublimetext.com/" target="_blank">Sublime Text</a>, o <a href="https://atom.io/" target="_blank">Atom.io</a>, o <a href="http://brackets.io/" target="_blank">Brackets.io</a>. Ou você pode usar um ambiente de desenvolvimento integrado (IDE) como o <a href="https://eclipse.org/" target="_blank">Eclipse</a> (versão Luna ou posterior).
  
  > [!NOTE]
  > Seu editor ou IDE pode ter uma funcionalidade específica para trabalhar com o Maven que não é abordada neste documento. Para obter informações sobre os recursos do seu ambiente de edição, consulte a documentação do produto que você está usando.
  > 
  > 

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente
As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - deve apontar para o diretório onde o JRE (Java runtime environment) está instalado. Por exemplo, em uma distribuição Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - deve conter os seguintes caminhos:
  
  * **JAVA_HOME** (ou o caminho equivalente)
  * **JAVA_HOME\bin** (ou o caminho equivalente)
  * O diretório onde o Maven está instalado

## <a name="create-a-new-maven-project"></a>Criar um novo projeto Maven
Na linha de comando, use o seguinte para criar um novo projeto Maven chamado **WordCount**:

    mvn archetype:generate -DarchetypeArtifactId=maven-archetype-quickstart -DgroupId=com.microsoft.example -DartifactId=WordCount -DinteractiveMode=false

Isso criará um novo diretório chamado **Contagem de Palavras** no local atual, que contém um projeto Maven básico.

O diretório **Contagem de Palavras** conterá os seguintes itens:

* **pom.xml**: contém configurações para o projeto Maven.
* **src\main\java\com\microsoft\example**: contém o código do aplicativo.
* **src\test\java\com\microsoft\example**: contém os testes do seu aplicativo. Neste exemplo, não criaremos testes.

### <a name="remove-the-example-code"></a>Remover o código de exemplo
Já que estamos criando nosso aplicativo, exclua os arquivos de aplicativo e de teste gerados:

* **src\test\java\com\microsoft\example\AppTest.java**
* **src\main\java\com\microsoft\example\App.java**

## <a name="add-properties"></a>Adicionar propriedades
O Maven permite que você defina valores de nível de projeto chamados propriedades. Adicione o seguinte após a linha `<url>http://maven.apache.org</url>` :

    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <!--
        Storm 0.10.0 is for HDInsight 3.3 and 3.4.
        To find the version information for earlier HDInsight cluster
        versions, see https://azure.microsoft.com/en-us/documentation/articles/hdinsight-component-versioning/
        -->
        <storm.version>0.10.0</storm.version>
    </properties>

Agora podemos usar esses valores em outras seções. Por exemplo, ao especificar a versão de componentes do Storm, podemos usar `${storm.version}` em vez de fazer hard-coding de um valor.

## <a name="add-dependencies"></a>Adicionar dependências
Como essa é uma topologia Storm, você deverá adicionar uma dependência a componentes do Storm. Abra o arquivo **POM** e adicione o seguinte código no seção **&lt;dependencies >**:

    <dependency>
      <groupId>org.apache.storm</groupId>
      <artifactId>storm-core</artifactId>
      <version>${storm.version}</version>
      <!-- keep storm out of the jar-with-dependencies -->
      <scope>provided</scope>
    </dependency>

Em tempo de compilação, o Maven usa essas informações para pesquisar **storm-core** no repositório Maven. Ele primeiro procura no repositório em seu computador local. Se os arquivos não estiverem lá, ele irá baixá-los do repositório Maven público e armazená-los no repositório local.

> [!NOTE]
> Observe a linha `<scope>provided</scope>` na seção que adicionamos. Isso informa ao Maven para excluir o **storm-core** de qualquer arquivo JAR criado, pois ele será fornecido pelo sistema. Isso permite que os pacotes criados sejam um pouco menores e garante que eles usarão os bits do **storm-core** incluídos no Storm no cluster HDInsight.
> 
> 

## <a name="build-configuration"></a>Configuração de compilação
Os plugins do Maven permitem que você personalize os estágios de compilação do projeto, como a forma que o projeto é compilado ou como compactá-lo em um arquivo JAR. Abra o arquivo **pom.xml** e adicione o código a seguir diretamente acima da linha `</project>`.

    <build>
      <plugins>
      </plugins>
      <resources>
      </resources>
    </build>

Esta seção será usada para adicionar plug-ins, recursos e outras opções de configuração de compilação. Para obter uma referência completa do arquivo **pom.xml**, consulte [http://maven.apache.org/pom.html](http://maven.apache.org/pom.html).

### <a name="add-plug-ins"></a>Adicionar plug-ins
Para topologias Storm, o <a href="http://mojo.codehaus.org/exec-maven-plugin/" target="_blank">plug-in Maven Exec</a> é útil porque permite que você execute com facilidade a topologia localmente em seu ambiente de desenvolvimento. Adicione o seguinte à seção `<plugins>` do arquivo **pom.xml** para incluir o plug-in Maven Exec:

    <plugin>
      <groupId>org.codehaus.mojo</groupId>
      <artifactId>exec-maven-plugin</artifactId>
      <version>1.4.0</version>
      <executions>
        <execution>
        <goals>
          <goal>exec</goal>
        </goals>
        </execution>
      </executions>
      <configuration>
        <executable>java</executable>
        <includeProjectDependencies>true</includeProjectDependencies>
        <includePluginDependencies>false</includePluginDependencies>
        <classpathScope>compile</classpathScope>
        <mainClass>${storm.topology}</mainClass>
      </configuration>
    </plugin>

> [!NOTE]
> Observe que a entrada `<mainClass>` usa `${storm.topology}`. Não definimos isso anteriormente na seção de propriedades (mas poderíamos ter definido). Em vez disso, definiremos esse valor na linha de comando ao executar a topologia em seu ambiente de desenvolvimento em uma etapa posterior.
> 
> 

Outro plug-in útil é o <a href="http://maven.apache.org/plugins/maven-compiler-plugin/" target="_blank">Plug-in do compilador Apache Maven</a>, que é usado para alterar opções de compilação. O principal motivo pelo qual precisamos disso é alterar a versão de Java que o Maven usa para origem e destino de seu aplicativo. Queremos a versão 1.7.

Adicione o seguinte à seção `<plugins>` do arquivo **pom.xml** para incluir o plug-in Compilador do Apache Maven e defina as versões de origem e de destino como 1.7.

    <plugin>
      <groupId>org.apache.maven.plugins</groupId>
      <artifactId>maven-compiler-plugin</artifactId>
      <version>3.3</version>
      <configuration>
        <source>1.7</source>
        <target>1.7</target>
      </configuration>
    </plugin>

### <a name="configure-resources"></a>Configurar recursos
A seção de recursos permite que você inclua recursos que não são código, por exemplo, arquivos de configuração necessários aos componentes na topologia. Para este exemplo, adicione o seguinte na seção `<resources>` do arquivo **pom.xml** .

    <resource>
        <directory>${basedir}/resources</directory>
        <filtering>false</filtering>
        <includes>
          <include>log4j2.xml</include>
        </includes>
    </resource>

Isso adiciona o diretório de recursos na raiz do projeto (`${basedir}`) como um local que contém os recursos e inclui o arquivo denominado **log4j2.xml**. Esse arquivo é usado para configurar quais informações são registradas pela topologia.

## <a name="create-the-topology"></a>Criar a topologia
Uma topologia Storm baseada em Java consiste em três componentes que você deve criar (ou referenciar) como uma dependência.

* **Spouts**: lê dados de fontes externas e a emite fluxos de dados para a topologia.
* **Bolts**: executa processamento em fluxos emitidos por spouts ou outros bolts e emite um ou mais fluxos.
* **Topologia**: define como os spouts e bolts são organizados e fornece o ponto de entrada para a topologia.

### <a name="create-the-spout"></a>Criar o spout
Para reduzir os requisitos para configurar fontes de dados externas, o seguinte spout simplesmente emite sentenças aleatórias. É uma versão modificada de um spout fornecido com os [exemplos Storm-Starter](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter).

> [!NOTE]
> Para obter um exemplo de um spout que lê de uma fonte de dados externa, consulte um dos exemplos a seguir:
> 
> * [TwitterSampleSPout](https://github.com/apache/storm/blob/0.10.x-branch/examples/storm-starter/src/jvm/storm/starter/spout/TwitterSampleSpout.java): um spout de exemplo que lê do Twitter
> * [Storm-Kafka](https://github.com/apache/storm/tree/0.10.x-branch/external/storm-kafka): um spout que lê do Kafka
> 
> 

Para o spout, crie um novo arquivo denominado **RandomSentenceSpout.java** no diretório **src\main\java\com\microsoft\example** e use o seguinte como o conteúdo:

    /**
     * Licensed to the Apache Software Foundation (ASF) under one
     * or more contributor license agreements.  See the NOTICE file
     * distributed with this work for additional information
     * regarding copyright ownership.  The ASF licenses this file
     * to you under the Apache License, Version 2.0 (the
     * "License"); you may not use this file except in compliance
     * with the License.  You may obtain a copy of the License at
     *
     * http://www.apache.org/licenses/LICENSE-2.0
     *
     * Unless required by applicable law or agreed to in writing, software
     * distributed under the License is distributed on an "AS IS" BASIS,
     * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     * See the License for the specific language governing permissions and
     * limitations under the License.
     */

     /**
      * Original is available at https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/storm/starter/spout/RandomSentenceSpout.java
      */

    package com.microsoft.example;

    import backtype.storm.spout.SpoutOutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichSpout;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Values;
    import backtype.storm.utils.Utils;

    import java.util.Map;
    import java.util.Random;

    //This spout randomly emits sentences
    public class RandomSentenceSpout extends BaseRichSpout {
      //Collector used to emit output
      SpoutOutputCollector _collector;
      //Used to generate a random number
      Random _rand;

      //Open is called when an instance of the class is created
      @Override
      public void open(Map conf, TopologyContext context, SpoutOutputCollector collector) {
      //Set the instance collector to the one passed in
        _collector = collector;
        //For randomness
        _rand = new Random();
      }

      //Emit data to the stream
      @Override
      public void nextTuple() {
      //Sleep for a bit
        Utils.sleep(100);
        //The sentences that will be randomly emitted
        String[] sentences = new String[]{ "the cow jumped over the moon", "an apple a day keeps the doctor away",
            "four score and seven years ago", "snow white and the seven dwarfs", "i am at two with nature" };
        //Randomly pick a sentence
        String sentence = sentences[_rand.nextInt(sentences.length)];
        //Emit the sentence
        _collector.emit(new Values(sentence));
      }

      //Ack is not implemented since this is a basic example
      @Override
      public void ack(Object id) {
      }

      //Fail is not implemented since this is a basic example
      @Override
      public void fail(Object id) {
      }

      //Declare the output fields. In this case, an sentence
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("sentence"));
      }
    }

Dedique uns momentos para ler os comentários do código para entender como esse spout funciona.

> [!NOTE]
> Embora essa topologia use apenas um spout, outras pessoas poderão ter vários que alimentam dados de origens diferentes na topologia.
> 
> 

### <a name="create-the-bolts"></a>Criar os bolts
Bolts manipulam o processamento de dados. Para esta topologia, temos dois bolts:

* **SplitSentence**: divide as sentenças emitidas por **RandomSentenceSpout** em palavras individuais.
* **WordCount**: conta quantas vezes cada palavra ocorreu.

> [!NOTE]
> Os bolts podem fazer literalmente qualquer coisa, por exemplo, computação, persistência ou conversar com componentes externos.
> 
> 

Crie dois novos arquivos, **SplitSentence.java** e **WordCount.Java** no diretório **src\main\java\com\microsoft\example**. Use o seguinte como o conteúdo dos arquivos:

**SplitSentence**

    package com.microsoft.example;

    import java.text.BreakIterator;

    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class SplitSentence extends BaseBasicBolt {

      //Execute is called to process tuples
      @Override
      public void execute(Tuple tuple, BasicOutputCollector collector) {
        //Get the sentence content from the tuple
        String sentence = tuple.getString(0);
        //An iterator to get each word
        BreakIterator boundary=BreakIterator.getWordInstance();
        //Give the iterator the sentence
        boundary.setText(sentence);
        //Find the beginning first word
        int start=boundary.first();
        //Iterate over each word and emit it to the output stream
        for (int end=boundary.next(); end != BreakIterator.DONE; start=end, end=boundary.next()) {
          //get the word
          String word=sentence.substring(start,end);
          //If a word is whitespace characters, replace it with empty
          word=word.replaceAll("\\s+","");
          //if it's an actual word, emit it
          if (!word.equals("")) {
            collector.emit(new Values(word));
          }
        }
      }

      //Declare that emitted tuples will contain a word field
      @Override
      public void declareOutputFields(OutputFieldsDeclarer declarer) {
        declarer.declare(new Fields("word"));
      }
    }

**WordCount**

    package com.microsoft.example;

    import java.util.HashMap;
    import java.util.Map;
    import java.util.Iterator;

    import backtype.storm.Constants;
    import backtype.storm.topology.BasicOutputCollector;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseBasicBolt;
    import backtype.storm.tuple.Fields;
    import backtype.storm.tuple.Tuple;
    import backtype.storm.tuple.Values;
    import backtype.storm.Config;

    // For logging
    import org.apache.logging.log4j.Logger;
    import org.apache.logging.log4j.LogManager;

    //There are a variety of bolt types. In this case, we use BaseBasicBolt
    public class WordCount extends BaseBasicBolt {
        //Create logger for this class
        private static final Logger logger = LogManager.getLogger(WordCount.class);
        //For holding words and counts
        Map<String, Integer> counts = new HashMap<String, Integer>();
        //How often we emit a count of words
        private Integer emitFrequency;

        // Default constructor
        public WordCount() {
            emitFrequency=5; // Default to 60 seconds
        }

        // Constructor that sets emit frequency
        public WordCount(Integer frequency) {
            emitFrequency=frequency;
        }

        //Configure frequency of tick tuples for this bolt
        //This delivers a 'tick' tuple on a specific interval,
        //which is used to trigger certain actions
        @Override
        public Map<String, Object> getComponentConfiguration() {
            Config conf = new Config();
            conf.put(Config.TOPOLOGY_TICK_TUPLE_FREQ_SECS, emitFrequency);
            return conf;
        }

        //execute is called to process tuples
        @Override
        public void execute(Tuple tuple, BasicOutputCollector collector) {
            //If it's a tick tuple, emit all words and counts
            if(tuple.getSourceComponent().equals(Constants.SYSTEM_COMPONENT_ID)
                    && tuple.getSourceStreamId().equals(Constants.SYSTEM_TICK_STREAM_ID)) {
                for(String word : counts.keySet()) {
                    Integer count = counts.get(word);
                    collector.emit(new Values(word, count));
                    logger.info("Emitting a count of " + count + " for word " + word);
                }
            } else {
                //Get the word contents from the tuple
                String word = tuple.getString(0);
                //Have we counted any already?
                Integer count = counts.get(word);
                if (count == null)
                    count = 0;
                //Increment the count and store it
                count++;
                counts.put(word, count);
            }
        }

        //Declare that we will emit a tuple containing two fields; word and count
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            declarer.declare(new Fields("word", "count"));
        }
    }

Reserve um tempo para ler os comentários do código para entender o funcionamento de cada bolt.

### <a name="define-the-topology"></a>Definir a topologia
A topologia vincula os spouts e bolts em um gráfico, que define como os dados fluem entre os componentes. Ele também fornece dicas de paralelismo que o Storm usará ao criar instâncias dos componentes de dentro do cluster.

A seguir, um diagrama básico do gráfico de componentes para esta topologia.

![diagrama mostrando a organização de spouts e bolts](./media/hdinsight-storm-develop-java-topology/wordcount-topology.png)

Para implementar a topologia, crie um novo arquivo denominado **WordCountTopology.jav** no diretório **src\main\java\com\microsoft\example**. Use o seguinte como o conteúdo do arquivo:

    package com.microsoft.example;

    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.topology.TopologyBuilder;
    import backtype.storm.tuple.Fields;

    import com.microsoft.example.RandomSentenceSpout;

    public class WordCountTopology {

      //Entry point for the topology
      public static void main(String[] args) throws Exception {
      //Used to build the topology
        TopologyBuilder builder = new TopologyBuilder();
        //Add the spout, with a name of 'spout'
        //and parallelism hint of 5 executors
        builder.setSpout("spout", new RandomSentenceSpout(), 5);
        //Add the SplitSentence bolt, with a name of 'split'
        //and parallelism hint of 8 executors
        //shufflegrouping subscribes to the spout, and equally distributes
        //tuples (sentences) across instances of the SplitSentence bolt
        builder.setBolt("split", new SplitSentence(), 8).shuffleGrouping("spout");
        //Add the counter, with a name of 'count'
        //and parallelism hint of 12 executors
        //fieldsgrouping subscribes to the split bolt, and
        //ensures that the same word is sent to the same instance (group by field 'word')
        builder.setBolt("count", new WordCount(), 12).fieldsGrouping("split", new Fields("word"));

        //new configuration
        Config conf = new Config();
        //Set to false to disable debug information
        // when running in production mode.
        conf.setDebug(false);

        //If there are arguments, we are running on a cluster
        if (args != null && args.length > 0) {
          //parallelism hint to set the number of workers
          conf.setNumWorkers(3);
          //submit the topology
          StormSubmitter.submitTopology(args[0], conf, builder.createTopology());
        }
        //Otherwise, we are running locally
        else {
          //Cap the maximum number of executors that can be spawned
          //for a component to 3
          conf.setMaxTaskParallelism(3);
          //LocalCluster is used to run locally
          LocalCluster cluster = new LocalCluster();
          //submit the topology
          cluster.submitTopology("word-count", conf, builder.createTopology());
          //sleep
          Thread.sleep(10000);
          //shut down the cluster
          cluster.shutdown();
        }
      }
    }

Reserve um tempo para ler os comentários do código para entender como a topologia é definida e enviada para o cluster.

### <a name="configure-logging"></a>Configurar o registro em log
O Storm usa o Apache Log4j para registrar informações em log. Se você não configurar o registro em log, a topologia emitirá muitas informações de diagnóstico, que podem ser difíceis de ler. Para controlar o que é registrado, crie um arquivo denominado **log4j2.xml** no diretório **recursos**. Use o seguinte como o conteúdo do arquivo.

    <?xml version="1.0" encoding="UTF-8"?>
    <Configuration>
    <Appenders>
        <Console name="STDOUT" target="SYSTEM_OUT">
            <PatternLayout pattern="%d{HH:mm:ss} [%t] %-5level %logger{36} - %msg%n"/>
        </Console>
    </Appenders>
    <Loggers>
        <Logger name="com.microsoft.example" level="trace" additivity="false">
            <AppenderRef ref="STDOUT"/>
        </Logger>
        <Root level="error">
            <Appender-Ref ref="STDOUT"/>
        </Root>
    </Loggers>
    </Configuration>

Isso configura um novo agente para a classe **com.microsoft.example**, que inclui os componentes nesta topologia de exemplo. O nível é definido como rastreamento para esse agente, que capturará informações de registro em log emitidas pelos componentes nesta topologia. Se você revisar o código deste projeto, observará que apenas o arquivo WordCount.java implementa o registro em log; ele registrará a contagem de cada palavra.

A seção `<Root level="error">` configura o nível-raiz do log (tudo que não está em **com.microsoft.example**) para registrar apenas as informações de erro.

> [!IMPORTANT]
> Embora isso reduza consideravelmente as informações registradas ao testar uma topologia em seu ambiente de desenvolvimento, não remove todas as informações de depuração produzidas durante a execução em um cluster em produção. Para reduzir essas informações, você também deve definir a depuração como falso na configuração enviada ao cluster. Consulte o código WordCountTopology.java neste documento para obter um exemplo. 
> 
> 

Para saber mais sobre como configurar o registro em log para Log4j, confira [http://logging.apache.org/log4j/2.x/manual/configuration.html](http://logging.apache.org/log4j/2.x/manual/configuration.html).

> [!NOTE]
> O Storm, versão 0.10.0, usa Log4j 2.x. Versões mais antigas do storm usavam Log4j 1.x, que usava um formato diferente para a configuração de log. Para saber mais sobre a configuração mais antiga, confira [http://wiki.apache.org/logging-log4j/Log4jXmlFormat](http://wiki.apache.org/logging-log4j/Log4jXmlFormat).
> 
> 

## <a name="test-the-topology-locally"></a>Testar a topologia localmente
Depois de salvar os arquivos, use o comando a seguir para testar a topologia localmente.

    mvn compile exec:java -Dstorm.topology=com.microsoft.example.WordCountTopology

À medida que é executada, a topologia exibirá informações de inicialização. Em seguida, ela começa a exibir linhas semelhantes às opções a seguir à medida que frases são emitidas do spout e processadas pelos bolts.

    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
    17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
    17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
    17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word snow

Ao examinar o registro em log emitido pelo bot WordCount, é possível ver que ‘e’ foi emitido 113 vezes. A contagem continuará a crescer enquanto a topologia for executada porque o spout emite continuamente as mesmas sentenças.

Também haverá um intervalo de 5 segundos entre a emissão de palavras e as contagens. Isso ocorre porque o componente **WordCount** está configurado para emitir informações somente quando chega uma tupla marcada e solicita que essas tuplas sejam entregues somente a cada cinco segundos por padrão.

## <a name="convert-the-topology-to-flux"></a>Converter a topologia para Flux
Flux é uma nova estrutura disponível com o Storm 0.10.0, que permite que você separe a configuração da implementação. Os componentes (bolts e spouts) ainda são definidos em Java, mas a topologia é definida usando um arquivo YAML.

O arquivo YAML define os componentes a usar para a topologia, como os dados fluem entre eles e quais valores usar ao inicializar os componentes. Você pode incluir um arquivo YAML como parte do arquivo jar que contém o projeto quando o implanta, ou você pode usar um arquivo YAML externo ao iniciar a topologia.

1. Mova o arquivo **WordCountTopology.java** para fora do projeto. Anteriormente isso definia a topologia, mas nós não o utilizaremos para o Flux.
2. No diretório **recursos**, crie um novo arquivo denominado **topology.yaml**. Use o conteúdo a seguir para este arquivo.
   
        # topology definition
   
        # name to be used when submitting. This is what shows up...
        # in the Storm UI/storm command-line tool as the topology name
        # when submitted to Storm
        name: "wordcount"
   
        # Topology configuration
        config:
        # Hint for the number of workers to create
        topology.workers: 1
   
        # Spout definitions
        spouts:
        - id: "sentence-spout"
            className: "com.microsoft.example.RandomSentenceSpout"
            # parallelism hint
            parallelism: 1
   
        # Bolt definitions
        bolts:
        - id: "splitter-bolt"
            className: "com.microsoft.example.SplitSentence"
            parallelism: 1
   
        - id: "counter-bolt"
            className: "com.microsoft.example.WordCount"
            constructorArgs:
            - 10
            parallelism: 1
   
        # Stream definitions
        streams:
        - name: "Spout --> Splitter" # name isn't used (placeholder for logging, UI, etc.)
            # The stream emitter
            from: "sentence-spout"
            # The stream consumer
            to: "splitter-bolt"
            # Grouping type
            grouping:
            type: SHUFFLE
   
        - name: "Splitter -> Counter"
            from: "splitter-bolt"
            to: "counter-bolt"
            grouping:
            type: FIELDS
            # field(s) to group on
            args: ["word"]
   
    Dedique um tempo para ler e entender o que cada seção faz e como ela se relaciona com a definição baseada em Java no arquivo **WordCountTopology.java**.
3. Faça as alterações a seguir no arquivo **pom.xml**.
   
   * Adicione a nova dependência a seguir à seção `<dependencies>` :
     
           <!-- Add a dependency on the Flux framework -->
           <dependency>
               <groupId>org.apache.storm</groupId>
               <artifactId>flux-core</artifactId>
               <version>${storm.version}</version>
           </dependency>
   * Adicione o plug-in a seguir à seção `<plugins>` . Este plug-in cuida da criação de um pacote (arquivo jar) para o projeto e aplica algumas transformações específicas ao Flux durante a criação do pacote.
     
           <!-- build an uber jar -->
           <plugin>
               <groupId>org.apache.maven.plugins</groupId>
               <artifactId>maven-shade-plugin</artifactId>
               <version>2.3</version>
               <configuration>
                   <transformers>
                       <!-- Keep us from getting a "can't overwrite file error" -->
                       <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer" />
                       <transformer implementation="org.apache.maven.plugins.shade.resource.ServicesResourceTransformer" />
                       <!-- We're using Flux, so refer to it as main -->
                       <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                           <mainClass>org.apache.storm.flux.Flux</mainClass>
                       </transformer>
                   </transformers>
                   <!-- Keep us from getting a bad signature error -->
                   <filters>
                       <filter>
                           <artifact>*:*</artifact>
                           <excludes>
                               <exclude>META-INF/*.SF</exclude>
                               <exclude>META-INF/*.DSA</exclude>
                               <exclude>META-INF/*.RSA</exclude>
                           </excludes>
                       </filter>
                   </filters>
               </configuration>
               <executions>
                   <execution>
                       <phase>package</phase>
                       <goals>
                           <goal>shade</goal>
                       </goals>
                   </execution>
               </executions>
           </plugin>
   * Na seção **exec-maven-plugin** `<configuration>`, altere o valor de `<mainClass>` para `org.apache.storm.flux.Flux`. Isso permite que o Flux cuide da execução da topologia quando o executamos localmente durante o desenvolvimento.
   * Na seção `<resources>`, adicione o seguinte a `<includes>`. Isso inclui o arquivo YAML que define a topologia como parte do projeto.
     
           <include>topology.yaml</include>

## <a name="test-the-flux-topology-locally"></a>Testar a topologia do Flux localmente
1. Use o seguinte para compilar e executar a topologia do Flux usando o Maven.
   
        mvn compile exec:java -Dexec.args="--local -R /topology.yaml"
   
    Se você estiver usando o PowerShell, use o seguinte:
   
        mvn compile exec:java "-Dexec.args=--local -R /topology.yaml"
   
    Se você estiver em um sistema Unix/Linux/OS X e tiver [instalado Storm em seu ambiente de desenvolvimento](http://storm.apache.org/releases/0.10.0/Setting-up-development-environment.html), você poderá usar os seguintes comandos em vez disso:
   
        mvn compile package
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local -R /topology.yaml
   
    O parâmetro `--local` executa a topologia no modo local no ambiente de desenvolvimento. O parâmetro `-R /topology.yaml` usa o recurso de arquivo `topology.yaml` do arquivo jar para definir a topologia.
   
    À medida que é executada, a topologia exibirá informações de inicialização. Em seguida, ela começa a exibir linhas semelhantes às opções a seguir à medida que frases são emitidas do spout e processadas pelos bolts.
   
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word snow
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 56 for word white
        17:33:27 [Thread-12-count] INFO  com.microsoft.example.WordCount - Emitting a count of 112 for word seven
        17:33:27 [Thread-16-count] INFO  com.microsoft.example.WordCount - Emitting a count of 195 for word the
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 113 for word and
        17:33:27 [Thread-30-count] INFO  com.microsoft.example.WordCount - Emitting a count of 57 for word dwarfs
   
    Haverá um atraso de 10 segundos entre lotes de informações registradas em log, visto que o arquivo `topology.yaml` transmite um valor de `10` quando o componente de WordCount é criado. Isso define o intervalo de atraso para a tupla de escala como 10 segundos.
2. Faça uma cópia do arquivo `topology.yaml` do projeto. Dê a ele um nome semelhante a `newtopology.yaml`. No arquivo, localize a seção a seguir e altere o valor de `10` para `5`. Isso altera o intervalo entre as emissões de lotes de contagens de palavras de 10 segundos para 5.
   
         - id: "counter-bolt"
           className: "com.microsoft.example.WordCount"
           constructorArgs:
           - 5
           parallelism: 1
3. Para executar a topologia, use o seguinte comando:
   
        mvn exec:java -Dexec.args="--local /path/to/newtopology.yaml"
   
    Ou, se você tiver o Storm em seu ambiente de desenvolvimento do Unix/Linux/OS X:
   
        storm jar target/WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --local /path/to/newtopology.yaml
   
    Altere o `/path/to/newtopology.yaml` para o caminho para o arquivo newtopology.yaml criado por você na etapa anterior. Esse comando usará o newtopology.yaml como a definição da topologia. Como não incluímos o parâmetro `compile` , o Maven reutilizará a versão do projeto criado nas etapas anteriores.
   
    Depois que a topologia começar, você deverá observar uma alteração no tempo entre os lotes emitidos refletindo o valor em newtopology.yaml. Veja então que você pode alterar sua configuração por meio de um arquivo YAML sem ter que recompilar a topologia.

Vários outros recursos que o Flux oferece não são discutidos aqui, como a substituição de variáveis no arquivo YAML com base em parâmetros passados em tempo de execução, ou de variáveis de ambiente. Para obter mais informações sobre esses e outros recursos da estrutura do Flux, consulte [Flux (https://storm.apache.org/releases/0.10.0/flux.html)](https://storm.apache.org/releases/0.10.0/flux.html).

## <a name="trident"></a>Trident
O Trident é uma abstração de alto nível fornecida pelo Storm. Ele dá suporte ao processamento com monitoramento de estado. A principal vantagem do Trident é que ele pode garantir que todas as mensagens que entrarem na topologia sejam processadas somente uma vez. Isso é difícil de atingir em uma topologia bruta do Java, que garante que as mensagens serão processadas pelo menos uma vez. Também existem outras diferenças, como componentes internos que podem ser usados em vez da criação de bolts. Na verdade, os bolts são completamente substituídos por componentes menos genéricos, como filtros, projeções e funções.

Os aplicativos Trident podem ser criados usando projetos Maven. Use as mesmas etapas básicas como apresentado anteriormente neste artigo — somente o código é diferente. O Trident também (atualmente) não pode ser usado com a estrutura do Flux.

Para obter mais informações sobre o Trident, consulte a <a href="http://storm.apache.org/documentation/Trident-API-Overview.html" target="_blank">Visão geral da API do Trident</a>.

Para obter um exemplo de um aplicativo Trident, consulte [Trending topics do Twitter com Apache Storm no HDInsight](hdinsight-storm-twitter-trending.md).

## <a name="next-steps"></a>Próximas etapas
Você aprendeu a criar uma topologia do Storm usando Java. Agora saiba como:

* [Implantar e gerenciar topologias Apache Storm no HDInsight](hdinsight-storm-deploy-monitor-topology.md)
* [Desenvolver topologias C# para o Apache Storm no HDInsight usando o Visual Studio](hdinsight-storm-develop-csharp-visual-studio-topology.md)

Para obter mais topologias Storm, consulte [Topologias de exemplo para o Storm no HDInsight](hdinsight-storm-example-topology.md).




<!--HONumber=Nov16_HO3-->


