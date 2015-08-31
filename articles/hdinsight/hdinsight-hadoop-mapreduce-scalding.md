<properties
 pageTitle="Desarrollo de trabajos de MapReduce de Scalding con Maven | Microsoft Azure"
 description="Obtenga información sobre cómo usar Maven para crear un trabajo de MapReduce de Scalding y, a continuación, implemente y ejecute el trabajo en un Hadoop en un clúster de HDInsight."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>
<tags 
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="07/06/2015"
 ms.author="larryfr"/>

# Desarrollo de trabajos de MapReduce de Scalding con Hadoop Apache en HDInsight

Scalding es una biblioteca de Scala que facilita la creación de trabajos de MapReduce de Hadoop. Ofrece una sintaxis concisa, así como la estrecha integración con Scala.

En este documento, aprenderá a cómo usar Maven para crear un trabajo de MapReduce de recuento de palabras básico escrito en Scalding. A continuación, aprenderá a implementar y ejecutar este trabajo en un clúster de HDInsight.

## Requisitos previos

- **Una suscripción de Azure**. Vea [Obtener evaluación gratuita de Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Un clúster de Hadoop en HDInsight basado en Windows o Linux**. Vea [Aprovisionamiento de Handoop basado en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md) o [Aprovisionamiento de Handoop basado en Windows en HDInsight](hdinsight-provision-clusters.md) para obtener más información.

* **[Maven](http://maven.apache.org/)**

* **[JDK de la plataforma Java 7](http://www.oracle.com/technetwork/java/javase/downloads/index.html) o posterior**

## Cree y compile el proyecto.

1. Utilice el comando siguiente para crear un nuevo proyecto de Maven:

        mvn archetype:generate -DgroupId=com.microsoft.example -DartifactId=scaldingwordcount -DarchetypeGroupId=org.scala-tools.archetypes -DarchetypeArtifactId=scala-archetype-simple -DinteractiveMode=false

    Este comando creará un nuevo directorio denominado **scaldingwordcount** y creará el scaffolding para una aplicación de Scala.

2. En el directorio **scaldingwordcount**, abra el archivo **pom.xml** y reemplace el contenido por lo siguiente:

        <project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
          <modelVersion>4.0.0</modelVersion>
          <groupId>com.microsoft.example</groupId>
          <artifactId>scaldingwordcount</artifactId>
          <version>1.0-SNAPSHOT</version>
          <name>${project.artifactId}</name>
          <properties>
            <maven.compiler.source>1.6</maven.compiler.source>
            <maven.compiler.target>1.6</maven.compiler.target>
            <encoding>UTF-8</encoding>
          </properties>
          <repositories>
            <repository>
              <id>conjars</id>
              <url>http://conjars.org/repo</url>
            </repository>
            <repository>
              <id>maven-central</id>
              <url>http://repo1.maven.org/maven2</url>
            </repository>
          </repositories>
          <dependencies>
            <dependency>
              <groupId>com.twitter</groupId>
              <artifactId>scalding-core_2.11</artifactId>
              <version>0.13.1</version>
            </dependency>
            <dependency>
              <groupId>org.apache.hadoop</groupId>
              <artifactId>hadoop-core</artifactId>
              <version>1.2.1</version>
              <scope>provided</scope>
            </dependency>
          </dependencies>
          <build>
            <sourceDirectory>src/main/scala</sourceDirectory>
            <plugins>
              <plugin>
                <groupId>org.scala-tools</groupId>
                <artifactId>maven-scala-plugin</artifactId>
                <version>2.15.2</version>
                <executions>
                  <execution>
                    <id>scala-compile-first</id>
                    <phase>process-resources</phase>
                    <goals>
                      <goal>add-source</goal>
                      <goal>compile</goal>
                    </goals>
                  </execution>
                </executions>
              </plugin>
              <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-shade-plugin</artifactId>
                <version>2.3</version>
                <configuration>
                  <transformers>
                    <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
                    </transformer>
                  </transformers>
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
                    <configuration>
                      <transformers>
                        <transformer implementation="org.apache.maven.plugins.shade.resource.ManifestResourceTransformer">
                          <mainClass>com.twitter.scalding.Tool</mainClass>
                        </transformer>
                      </transformers>
                    </configuration>
                  </execution>
                </executions>
              </plugin>
            </plugins>
          </build>
        </project>

    Este archivo describe el proyecto, las dependencias y los complementos. Estas son las entradas importantes:

    * **maven.compiler.source** y **maven.compiler.target**: establece la versión de Java para este proyecto.

    * **repositories**: los repositorios que contienen los archivos de dependencia utilizados por este proyecto.

    * **scalding-core\_2.11** y **hadoop-core**: este proyecto depende de los paquetes principales de Scalding y Hadoop.

    * **maven-scala-plugin**: complemento para compilar aplicaciones de Scala

    * **maven-shade-plugin**: complemento para crear productos sombreados (fat). Este complemento aplica filtros y transformaciones; específicamente:

        * **filtros**: los filtros aplicados modifican la información de metadatos incluida con el archivo jar. Para evitar las excepciones de firma en tiempo de ejecución, se excluyen los diversos archivos de firma que se pueden incluir con dependencias.

        * **executions**: la configuración de ejecución de la fase del paquete especifica la clase **com.twitter.scalding.Tool** como la clase principal para el paquete. Sin esto, necesitaría especificar com.twitter.scalding.Tool, así como la clase que contiene la lógica de la aplicación cuando se ejecuta el trabajo con el comando de hadoop.

3. Elimine el directorio **src/test**, ya que no creará pruebas en este ejemplo.

4. Abra el archivo **src/main/scala/com/microsoft/example/app.scala** y reemplace el contenido por lo siguiente:

        package com.microsoft.example

        import com.twitter.scalding._

        class WordCount(args : Args) extends Job(args) {
          // 1. Read lines from the specified input location
          // 2. Extract individual words from each line
          // 3. Group words and count them
          // 4. Write output to the specified output location
          TextLine(args("input"))
            .flatMap('line -> 'word) { line : String => tokenize(line) }
            .groupBy('word) { _.size }
            .write(Tsv(args("output")))

          //Tokenizer to split sentance into words
          def tokenize(text : String) : Array[String] = {
            text.toLowerCase.replaceAll("[^a-zA-Z0-9\\s]", "").split("\\s+")
          }
        }

    De esta forma, se implementa un trabajo de recuento de palabras básico.

5. Guarde y cierre los archivos.

6. Utilice el siguiente comando desde el directorio **scaldingwordcount** para compilar y empaquetar la aplicación:

        mvn package

    Al finalizar este trabajo, el paquete que contiene la aplicación WordCount puede encontrarse en **target/scaldingwordcount-1.0-SNAPSHOT.jar**.

## Ejecución del trabajo en un clúster basado en Linux

> [AZURE.NOTE]Los pasos siguientes usan SSH y el comando de Hadoop. Para otros métodos de ejecución de trabajos de MapReduce, consulte [Uso de MapReduce en Hadoop en HDInsight](hdinsight-use-mapreduce.md).

1. Use el comando siguiente para cargar el paquete en el clúster de HDInsight:

        scp target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:

    De esta manera, se copiarán los archivos del sistema local al nodo principal.

    > [AZURE.NOTE]Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada. Por ejemplo, `scp -i /path/to/private/key target/scaldingwordcount-1.0-SNAPSHOT.jar username@clustername-ssh.azurehdinsight.net:.`

2. Use el siguiente comando para conectarse al nodo principal del clúster:

        ssh username@clustername-ssh.azurehdinsight.net

    > [AZURE.NOTE]Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada. Por ejemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`

3. Una vez conectado al nodo principal, use el siguiente comando para ejecutar el trabajo de recuento de palabras.

        hadoop jar scaldingwordcount-1.0-SNAPSHOT.jar com.microsoft.example.WordCount --hdfs --input wasb:///example/data/gutenberg/davinci.txt --output wasb:///example/wordcountout

    De esta forma, se ejecuta la clase de WordCount que implementó anteriormente. `--hdfs` indica al trabajo que utilice HDFS. `--input` Especifica el archivo de texto de entrada y `--output` especifica la ubicación de salida.

4. Una vez completado el trabajo, utilice lo siguiente para ver el resultado.

        hadoop fs -text wasb:///example/wordcountout/part-00000

    Debería ver información similar a la siguiente:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

## Ejecución del trabajo en un clúster basado en Windows

> [AZURE.NOTE]Los pasos siguientes usan Windows PowerShell. Para otros métodos de ejecución de trabajos de MapReduce, consulte [Uso de MapReduce en Hadoop en HDInsight](hdinsight-use-mapreduce.md).

1. [Instale y configure Azure PowerShell](../install-configure-powershell.md).

2. Descargue [hdinsight tools.psm1](https://github.com/Blackmist/hdinsight-tools/blob/master/hdinsight-tools.psm1) y guarde en un archivo denominado **hdinsight-tools.psm1**.

3. Abra una sesión de **Azure PoweShell** y especifique el comando siguiente. Si hdinsight-tools.psm1 no se encuentra en el directorio actual, proporcione la ruta de acceso al archivo:

        import-module hdinsight-tools.psm1

    De esta forma, se importan varias funciones para trabajar con archivos en HDInsight.

4. Use los siguientes comandos para cargar el archivo jar que contiene el trabajo WordCount. Reemplace `CLUSTERNAME` por el nombre del clúster de HDInsight:

        $clusterName="CLUSTERNAME"
        Add-HDInsightFile -clusterName $clusterName -localPath \path\to\scaldingwordcount-1.0-SNAPSHOT.jar -destinationPath example/jars/scaldingwordcount-1.0-SNAPSHOT.jar

5. Una vez que se haya completado la carga, use los siguientes comandos para ejecutar el trabajo:

        $jobDef=New-AzureHDInsightMapReduceJobDefinition -JobName ScaldingWordCount -JarFile wasb:///example/jars/scaldingwordcount-1.0-SNAPSHOT.jar -ClassName com.microsoft.example.WordCount -arguments "--hdfs", "--input", "wasb:///example/data/gutenberg/davinci.txt", "--output", "wasb:///example/wordcountout"
        $job = start-azurehdinsightjob -cluster $clusterName -jobdefinition $jobDef
        wait-azurehdinsightjob -Job $job -waittimeoutinseconds 3600

6. Una vez completado el trabajo, utilice lo siguiente para descargar la salida del trabajo:

        Get-HDInsightFile -clusterName $clusterName -remotePath example/wordcountout/part-00000 -localPath output.txt

7. La salida consta de los valores de recuento y palabras delimitados por tabuladores. Utilice el siguiente comando para mostrar los resultados.

        cat output.txt

    El archivo debe contener valores similares al siguiente:

        writers 9
        writes  18
        writhed 1
        writing 51
        writings        24
        written 208
        writtenthese    1
        wrong   11
        wrongly 2
        wrongplace      1
        wrote   34
        wrotefootnote   1
        wrought 7

7. Si la salida está vacía, o el archivo no existe, puede usar el siguiente para ver los errores que se produjeron al ejecutar el trabajo:

        Get-AzureHdinsightJobOutput -cluster $clusterName -jobId $job.JobId -standarderror

## Pasos siguientes

Ahora que sabe usar Scalding para crear trabajos de MapReduce para HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con Azure HDInsight.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)

* [Uso de Pig con HDInsight](hdinsight-use-pig.md)

* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=August15_HO8-->