---
title: Desenvolver programas Java MapReduce para HDInsight baseado em Linux | Microsoft Docs
description: "Saiba como desenvolver programas MapReduce em Java como implantá-los no HDInsight baseado em Linux."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 10/11/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e22d28c76902fbfe549f2a7aeb22b62eb0a540fb


---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight-linux"></a>Desenvolver programas Java MapReduce para Hadoop no HDInsight Linux
Este documento orienta você quanto ao uso do Apache Maven para criar um aplicativo de MapReduce e implantar e executá-lo em um Hadoop baseado em Linux no cluster do HDInsight.

## <a name="a-nameprerequisitesaprerequisites"></a><a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, você deve ter o seguinte:

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 7 ou posterior (ou um equivalente, como OpenJDK)
* [Apache Maven](http://maven.apache.org/)
* **Uma assinatura do Azure**
* **CLI do Azure**
  
    [!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

## <a name="configure-environment-variables"></a>Configurar variáveis de ambiente
As seguintes variáveis de ambiente podem ser definidas quando você instala o Java e o JDK. No entanto, você deve verificar se elas existem e se contêm os valores corretos para o seu sistema.

* **JAVA_HOME** - deve apontar para o diretório onde o JRE (Java runtime environment) está instalado. Por exemplo, em um sistema OS x, Unix ou Linux, ele deve ter um valor semelhante a `/usr/lib/jvm/java-7-oracle`. No Windows, ele teria um valor semelhante a `c:\Program Files (x86)\Java\jre1.7`
* **PATH** - deve conter os seguintes caminhos:
  
  * **JAVA_HOME** (ou o caminho equivalente)
  * **JAVA_HOME\bin** (ou o caminho equivalente)
  * O diretório onde o Maven está instalado

## <a name="create-a-new-maven-project"></a>Criar um novo projeto Maven
1. Em uma sessão do terminal ou linha de comando em seu ambiente de desenvolvimento, mude os diretórios para o local em que você deseja armazenar o projeto.
2. Use o comando **mvn** , que é instalado com o Maven, para gerar o scaffolding para o projeto.
   
        mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   
    Isso criará um novo diretório no diretório atual, com o nome especificado pelo parâmetro **artifactID** (**wordcountjava** neste exemplo). Esse diretório conterá os itens a seguir:
   
   * **pom.xml** - o [POM (Modelo de Objeto de Projeto](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) ) contém informações e detalhes de configuração usados para criar o projeto.
   * **src** - o diretório que contém o diretório **main/java/org/apache/hadoop/examples**, no qual você criará seu aplicativo.
3. Exclua o arquivo **src/test/java/org/apache/hadoop/examples/apptest.java** , já que ele não será utilizado neste exemplo.

## <a name="add-dependencies"></a>Adicionar dependências
1. Edite o arquivo **pom.xml** e adicione o seguinte dentro da seção `<dependencies>`:
   
        <dependency>
          <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-mapreduce-client-common</artifactId>
            <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
        <dependency>
            <groupId>org.apache.hadoop</groupId>
            <artifactId>hadoop-common</artifactId>
            <version>2.5.1</version>
          <scope>provided</scope>
        </dependency>
   
    Isso informa ao Maven que o projeto exige as bibliotecas (listadas em &lt;artifactId\>) com uma versão específica (listada em &lt;version\>). No momento da compilação, ele será baixado do repositório padrão do Maven. Você pode usar a [pesquisa de repositório do Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para exibir mais informações.
   
    O `<scope>provided</scope>` informa o Maven que essas dependências não devem ser empacotadas com o aplicativo, pois serão fornecidas pelo cluster do HDInsight no tempo de execução.
2. Adicione o conteúdo a seguir ao arquivo **pom.xml** . Isso precisa estar dentro das tags `<project>...</project>` no arquivo; por exemplo, entre `</dependencies>` e `</project>`.
   
        <build>
            <plugins>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                  </transformer>
                </transformers>
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
            <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-compiler-plugin</artifactId>
              <configuration>
               <source>1.7</source>
               <target>1.7</target>
              </configuration>
            </plugin>
            </plugins>
        </build>
   
    O primeiro plug-in configura o [Plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), que é usado para criar um uberjar (às vezes chamado de fatjar), que contém as dependências exigidas pelo aplicativo. Ele também evita a duplicação de licenças no pacote jar, que pode causar problemas em alguns sistemas.
   
    O segundo plug-in configura o compilador do Maven, que é usado para definir a versão do Java exigida pelo aplicativo como a versão usada no cluster do HDInsight.
3. Salve o arquivo **pom.xml** .

## <a name="create-the-mapreduce-application"></a>Criar o aplicativo MapReduce
1. Vá até o diretório **wordcountjava/src/main/java/org/apache/hadoop/examples** e renomeie o arquivo **App.java** para **WordCount.java**.
2. Abra o arquivo **WordCount.java** em seu editor de texto e substitua o conteúdo pelo seguinte:
   
        package org.apache.hadoop.examples;
   
        import java.io.IOException;
        import java.util.StringTokenizer;
        import org.apache.hadoop.conf.Configuration;
        import org.apache.hadoop.fs.Path;
        import org.apache.hadoop.io.IntWritable;
        import org.apache.hadoop.io.Text;
        import org.apache.hadoop.mapreduce.Job;
        import org.apache.hadoop.mapreduce.Mapper;
        import org.apache.hadoop.mapreduce.Reducer;
        import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
        import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
        import org.apache.hadoop.util.GenericOptionsParser;
   
        public class WordCount {
   
          public static class TokenizerMapper
               extends Mapper<Object, Text, Text, IntWritable>{
   
            private final static IntWritable one = new IntWritable(1);
            private Text word = new Text();
   
            public void map(Object key, Text value, Context context
                            ) throws IOException, InterruptedException {
              StringTokenizer itr = new StringTokenizer(value.toString());
              while (itr.hasMoreTokens()) {
                word.set(itr.nextToken());
                context.write(word, one);
              }
            }
          }
   
          public static class IntSumReducer
               extends Reducer<Text,IntWritable,Text,IntWritable> {
            private IntWritable result = new IntWritable();
   
            public void reduce(Text key, Iterable<IntWritable> values,
                               Context context
                               ) throws IOException, InterruptedException {
              int sum = 0;
              for (IntWritable val : values) {
                sum += val.get();
              }
              result.set(sum);
              context.write(key, result);
            }
          }
   
          public static void main(String[] args) throws Exception {
            Configuration conf = new Configuration();
            String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
            if (otherArgs.length != 2) {
              System.err.println("Usage: wordcount <in> <out>");
              System.exit(2);
            }
            Job job = new Job(conf, "word count");
            job.setJarByClass(WordCount.class);
            job.setMapperClass(TokenizerMapper.class);
            job.setCombinerClass(IntSumReducer.class);
            job.setReducerClass(IntSumReducer.class);
            job.setOutputKeyClass(Text.class);
            job.setOutputValueClass(IntWritable.class);
            FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
            FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
            System.exit(job.waitForCompletion(true) ? 0 : 1);
          }
        }
   
    Observe que o nome do pacote é **org.apache.hadoop.examples** e o nome da classe é **WordCount**. Você usará esses nomes quando enviar o trabalho MapReduce.
3. Salve o arquivo.

## <a name="build-the-application"></a>Compilar o aplicativo
1. Altere para o diretório **wordcountjava** se ainda não estiver lá.
2. Use o comando a seguir para compilar um arquivo JAR contendo o aplicativo:
   
        mvn clean package
   
    Isso limpará quaisquer artefatos de compilações anteriores, baixará quaisquer dependências que ainda não tiverem sido instaladas e então compilará e criará o pacote do aplicativo.
3. Assim que o comando for concluído, o diretório **wordcountjava/target** conterá um arquivo chamado **wordcountjava-1.0-SNAPSHOT.jar**.
   
   > [!NOTE]
   > O arquivo **wordcountjava-1.0-SNAPSHOT.jar** é um uberjar, que contém não apenas o trabalho WordCount, mas também as dependências necessárias ao trabalho em tempo de execução.
   > 
   > 

## <a name="a-iduploadaupload-the-jar"></a><a id="upload"></a>Carregar o jar
Use o comando a seguir para carregar o arquivo jar para o nó do HDInsight:

    scp wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Isso copiará os arquivos do sistema local para o nó principal.

> [!NOTE]
> Se você usou uma senha para proteger sua conta SSH, você será solicitado pela senha. Se você usou uma chave SSH, talvez precise usar o parâmetro `-i` e o caminho para a chave privada. Por exemplo: `scp -i /path/to/private/key wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:`.
> 
> 

## <a name="a-namerunarun-the-mapreduce-job"></a><a name="run"></a>Executar o trabalho MapReduce
1. Conecte ao HDInsight usando o SSH conforme descrito nos seguintes artigos:
   
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
   * [Usar SSH com Hadoop baseado em Linux no HDInsight no Windows](hdinsight-hadoop-linux-use-ssh-windows.md)
2. Na sessão do SSH, use o seguinte comando para executar o aplicativo MapReduce:
   
        yarn jar wordcountjava.jar org.apache.hadoop.examples.WordCount wasbs:///example/data/gutenberg/davinci.txt wasbs:///example/data/wordcountout
   
    Isso usará o aplicativo WordCount do MapReduce para contar as palavras no arquivo davinci.txt e armazenará os resultados em **wasbs:///example/data/wordcountout**. Os arquivos de entrada e saída são armazenados no armazenamento padrão do cluster.
3. Após concluir o trabalho, use o seguinte para exibir os resultados:
   
        hdfs dfs -cat wasbs:///example/data/wordcountout/*
   
    Você deve receber uma lista de palavras e contagens, com valores semelhantes ao seguinte:
   
        zeal    1
        zelus   1
        zenith  2

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Próximas etapas
Neste documento, você aprendeu a desenvolver um trabalho MapReduce em Java. Consulte os seguintes documentos para ver outras maneiras de trabalhar com o HDInsight.

* [Usar o hive com o HDInsight][hdinsight-use-hive]
* [Usar o Pig com o HDInsight][hdinsight-use-pig]
* [Usar o MapReduce com o HDInsight](hdinsight-use-mapreduce.md)

Para obter mais informações, consulte também o [Centro de desenvolvedores do Java](https://azure.microsoft.com/develop/java/).

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx




<!--HONumber=Nov16_HO3-->


