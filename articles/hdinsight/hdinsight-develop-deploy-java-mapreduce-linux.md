---
title: "Creación de programas MapReduce de Java para Hadoop: Azure HDInsight | Microsoft Docs"
description: "Aprenda a usar Apache Maven para crear una aplicación MapReduce basada en Java y ejecutarla en Hadoop de Azure HDInsight."
services: hdinsight
editor: cgronlun
manager: jhubbard
author: Blackmist
documentationcenter: 
tags: azure-portal
ms.assetid: 9ee6384c-cb61-4087-8273-fb53fa27c1c3
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 08/07/2017
ms.author: larryfr
ms.translationtype: HT
ms.sourcegitcommit: caaf10d385c8df8f09a076d0a392ca0d5df64ed2
ms.openlocfilehash: 11d63f22204eb2acb530378f53ac72f16a35a4f2
ms.contentlocale: es-es
ms.lasthandoff: 08/08/2017

---
# <a name="develop-java-mapreduce-programs-for-hadoop-on-hdinsight"></a>Desarrollo de programas MapReduce de Java para Hadoop en HDInsight

Aprenda a usar Apache Maven para crear una aplicación MapReduce basada en Java y ejecutarla en Hadoop de Azure HDInsight.

> [!NOTE]
> En este ejemplo se probó más recientemente en HDInsight 3.6.

## <a name="prerequisites"></a>Requisitos previos

* [Java JDK](http://www.oracle.com/technetwork/java/javase/downloads/) 8 o posterior (o un equivalente como OpenJDK).
    
    > [!NOTE]
    > Las versiones de HDInsight 3.4 y anteriores usan Java 7. HDInsight 3.5 y versiones posteriores usa Java 8.

* [Apache Maven](http://maven.apache.org/)

## <a name="configure-development-environment"></a>Configuración del entorno de desarrollo

Pueden establecer las siguientes variables de entorno al instalar Java y el JDK. Sin embargo, debe comprobar que existen y que contienen los valores correctos para su sistema.

* `JAVA_HOME`: debe apuntar al directorio donde está instalado Java Runtime Environment (JRE). Por ejemplo, en un sistema OS X, Unix o Linux, debe tener un valor similar a `/usr/lib/jvm/java-7-oracle`. En Windows, tendría un valor similar a `c:\Program Files (x86)\Java\jre1.7`

* `PATH`: debe contener las rutas de acceso siguientes:
  
  * `JAVA_HOME` (o la ruta de acceso equivalente)

  * `JAVA_HOME\bin` (o la ruta de acceso equivalente)

  * El directorio donde está instalado Maven

## <a name="create-a-maven-project"></a>Creación de un proyecto de Maven

1. Desde una sesión de terminal o la línea de comandos de su entorno de desarrollo, cambie los directorios por la ubicación en la cual desea guardar el proyecto.

2. Use el comando `mvn`, que se instala con Maven, para generar el scaffolding del proyecto.

   ```bash
   mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```

    > [!NOTE]
    > Si va a usar PowerShell, deberá poner comillas dobles alrededor de los parámetros `-D`.
    >
    > `mvn archetype:generate "-DgroupId=org.apache.hadoop.examples" "-DartifactId=wordcountjava" "-DarchetypeArtifactId=maven-archetype-quickstart" "-DinteractiveMode=false"`

    Esta acción creará un directorio en el directorio actual, con el nombre especificado por el parámetro `artifactID` (**hbaseapp** en este ejemplo). Este directorio raíz contiene los siguientes elementos:

   * `pom.xml`: el [modelo de objetos de proyectos (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html) que contiene la información y los detalles de configuración para compilar el proyecto.

   * `src`: el directorio que contiene la aplicación.

3. Elimine el archivo `src/test/java/org/apache/hadoop/examples/apptest.java`. No lo vamos a usar en este ejemplo.

## <a name="add-dependencies"></a>Adición de dependencias

1. Edite el archivo `pom.xml` y agregue el texto siguiente dentro de la sección `<dependencies>`:
   
   ```xml
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-examples</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-mapreduce-client-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>org.apache.hadoop</groupId>
        <artifactId>hadoop-common</artifactId>
        <version>2.7.3</version>
        <scope>provided</scope>
    </dependency>
   ```

    Esto define las bibliotecas requeridas (enumeradas en &lt;artifactId\>) de una versión específica (enumerada en &lt;version\>). En tiempo de compilación, estas dependencias se descargan del repositorio de Maven predeterminado. Puede usar la [búsqueda del repositorio de Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) para ver más información.
   
    `<scope>provided</scope>` indica a Maven que no hace falta que empaquete esas dependencias con la aplicación, puesto que las proporciona el clúster de HDInsight en tiempo de ejecución.

    > [!IMPORTANT]
    > La versión utilizada debe coincidir con la versión de Hadoop presente en el clúster. Para obtener más información sobre las versiones, vea el documento de [versiones de componentes de HDInsight](hdinsight-component-versioning.md).

2. Agregue al archivo `pom.xml` lo siguiente. Este texto debe estar dentro de las etiquetas `<project>...</project>` en el archivo; por ejemplo, entre `</dependencies>` y `</project>`.

   ```xml
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
            <version>3.6.1</version>
            <configuration>
            <source>1.8</source>
            <target>1.8</target>
            </configuration>
        </plugin>
        </plugins>
    </build>
   ```

    El primer complemento configura el [complemento Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/)que se usará para compilar un uberjar (en ocasiones denominado fatjar), que contiene todas las dependencias que necesita la aplicación. Asimismo, se evita que se dupliquen licencias en el paquete JAR, lo que suele causar problemas en algunos sistemas.

    El segundo complemento configura la versión de Java de destino.

    > [!NOTE]
    > HDInsight 3.4 y versiones anteriores utilizan Java 7. HDInsight 3.5 y versiones posteriores usa Java 8.

3. Guarde el archivo `pom.xml`.

## <a name="create-the-mapreduce-application"></a>Creación de la aplicación MapReduce

1. Vaya a directorio `wordcountjava/src/main/java/org/apache/hadoop/examples` y cambie el nombre del archivo `App.java` a `WordCount.java`.

2. Abra el archivo `WordCount.java` en un editor de texto y reemplace el contenido por el texto siguiente:
   
    ```java
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
    ```
   
    Tenga en cuenta que el nombre del paquete es `org.apache.hadoop.examples` y el nombre de clase es `WordCount`. Estos nombres se utilizan al enviar el trabajo de MapReduce.

3. Guarde el archivo .

## <a name="build-the-application"></a>Compilar la aplicación

1. Cambie al directorio `wordcountjava` si aún no está allí.

2. Use el siguiente comando para compilar un archivo JAR que contenga la aplicación:

   ```
   mvn clean package
   ```

    Este comando elimina los artefactos de compilación anteriores, descarga las dependencias que aún no se han instalado y luego compila y empaqueta la aplicación.

3. Una vez que finaliza el comando, el directorio `wordcountjava/target` contiene un archivo denominado `wordcountjava-1.0-SNAPSHOT.jar`.
   
   > [!NOTE]
   > El archivo `wordcountjava-1.0-SNAPSHOT.jar` es un uberjar que contiene no solo el trabajo WordCount, sino también las dependencias que el trabajo necesita en tiempo de ejecución.

## <a id="upload"></a>Carga del archivo .jar

Use el siguiente comando para cargar el archivo .jar al nodo principal de HDInsight:

   ```bash
   scp target/wordcountjava-1.0-SNAPSHOT.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:
   ```

    Replace __USERNAME__ with your SSH user name for the cluster. Replace __CLUSTERNAME__ with the HDInsight cluster name.

Este comando copia los archivos del sistema local al nodo principal. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="run"></a>Ejecución del trabajo de MapReduce en Hadoop

1. Conéctese a HDInsight mediante SSH. Para más información, consulte [Uso SSH con HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

2. En la sesión SSH, use el siguiente comando para ejecutar la aplicación MapReduce:
   
   ```bash
   yarn jar wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /example/data/gutenberg/davinci.txt /example/data/wordcountout
   ```
   
    Este comando inicia la aplicación WordCount MapReduce. El archivo de entrada es `/example/data/gutenberg/davinci.txt`, y el directorio de salida, `/example/data/wordcountout`. Tanto el archivo de entrada como el de salida se almacenan en el almacenamiento predeterminado del clúster.

3. Una vez completado el trabajo, use el siguiente comando para ver los resultados:
   
   ```bash
   hdfs dfs -cat /example/data/wordcountout/*
   ```

    Debería ver una lista de palabras y números con valores similares a los siguientes:
   
        zeal    1
        zelus   1
        zenith  2

## <a id="nextsteps"></a>Pasos siguientes

Gracias a este documento, ha aprendido a desarrollar un trabajo MapReduce de Java. Consulte los siguientes documentos para obtener información acerca de otras formas de trabajar con HDInsight.

* [Uso de Hive con HDInsight][hdinsight-use-hive]
* [Uso de Pig con HDInsight][hdinsight-use-pig]
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

Para más información, consulte también el [Centro de desarrolladores de Java](https://azure.microsoft.com/develop/java/).

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


