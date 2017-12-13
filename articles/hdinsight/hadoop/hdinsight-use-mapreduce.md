---
title: MapReduce con Hadoop en HDInsight | Microsoft Docs
description: "Obtenga información sobre cómo ejecutar trabajos de MapReduce en Hadoop en clústeres de HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7f321501-d62c-4ffc-b5d6-102ecba6dd76
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/01/2017
ms.author: larryfr
ms.openlocfilehash: ad12dee2eb01f839db07985fcb0805bf961354cc
ms.sourcegitcommit: be0d1aaed5c0bbd9224e2011165c5515bfa8306c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2017
---
# <a name="use-mapreduce-in-hadoop-on-hdinsight"></a>Uso de MapReduce en Hadoop en HDInsight

Obtenga información sobre cómo ejecutar trabajos de MapReduce en clústeres de HDInsight. Utilice la tabla siguiente para conocer las distintas formas de usar MapReduce con HDInsight:

| **Use esto**... | **...para hacer esto** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Uso del comando Hadoop mediante **SSH** |Linux |Linux, Unix, Mac OS X o Windows |
| [REST](apache-hadoop-use-mapreduce-curl.md) |Enviar el trabajo de forma remota mediante **REST** (los ejemplos usan cURL) |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Enviar el trabajo de forma remota mediante **Windows PowerShell** |Linux o Windows |Windows |
| [Escritorio remoto](apache-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 y 3.3) |Uso del comando Hadoop mediante **Escritorio remoto** |Windows |Windows |

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="whatis"></a>Qué es MapReduce

MapReduce de Hadoop es un marco de software para escribir trabajos que procesan enormes cantidades de datos. Los datos de entrada se dividen en fragmentos independientes. Cada fragmento se procesa en paralelo en todos los nodos del clúster. Un trabajo de MapReduce consta de dos funciones:

* **Asignador**: consume datos de entrada, los analiza (normalmente con un filtro y operaciones de ordenación) y emite tuplas (pares de clave-valor)

* **Reductor**: consume tuplas emitidas por el asignador y realiza una operación de resumen que crea un resultado combinado más pequeño de los datos del asignador

En el siguiente diagrama se muestra un ejemplo de trabajo de MapReduce de recuento de palabras básico:

![HDI.ProgramaRecuentoPalabras][image-hdi-wordcountdiagram]

La salida de este trabajo es un recuento de las veces que aparece cada palabra en el texto.

* El asignador utiliza cada línea del texto de entrada como una entrada y la desglosa en palabras. Emite un par clave-valor cada vez que se detecta una palabra, seguida por un 1. La salida se ordena antes de enviarla al reductor.
* El reductor suma estos recuentos individuales de cada palabra y emite un solo par clave-valor que contiene la palabra seguido de la suma de sus apariciones.

MapReduce se puede implementar en varios lenguajes. Java es la implementación más común y se utiliza para fines de demostración en este documento.

## <a name="development-languages"></a>Lenguajes de desarrollo

Los lenguajes o marcos de trabajo basados en Java y la máquina virtual de Java se pueden ejecutar directamente como un trabajo de MapReduce. El ejemplo usado en este documento es una aplicación de MapReduce de Java. Los lenguajes que no son Java, como C# o Python, o ejecutables independientes, deben usar **streaming de Hadoop**.

El streaming de Hadoop se comunica con el asignador y el reductor a través de STDIN y STDOUT. El asignador y reductor leen datos línea a línea desde STDIN y escriben el resultado en STDOUT. Cada línea leída o emitida por el asignador y el reductor debe estar en el formato de un par de clave-valor delimitado por un carácter de tabulación:

    [key]/t[value]

Para obtener más información, consulte [Streaming de Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Para obtener ejemplos del uso del streaming de Hadoop con HDInsight, vea los documentos siguientes:

* [Desarrollar trabajos de MapReduce de C#](apache-hadoop-dotnet-csharp-mapreduce-streaming.md)

* [Desarrollar trabajos de MapReduce de Python](apache-hadoop-streaming-python.md)

## <a id="data"></a>Datos de ejemplo

HDInsight proporciona diversos conjuntos de datos de ejemplo que se almacenan en los directorios `/example/data` y `/HdiSamples`. Estos directorios están en el almacenamiento predeterminado de su clúster. En este documento, se utiliza el archivo `/example/data/gutenberg/davinci.txt`. Este archivo contiene los blocs de notas de Leonardo Da Vinci.

## <a id="job"></a>MapReduce de ejemplo

En el clúster de HDInsight se incluye un MapReduce de ejemplo de aplicación de recuento de palabras. En este ejemplo se encuentra en `/example/jars/hadoop-mapreduce-examples.jar` en el almacenamiento predeterminado del clúster.

El código Java siguiente es el origen de la aplicación de MapReduce contenida en el archivo `hadoop-mapreduce-examples.jar`:

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

Para obtener instrucciones sobre cómo escribir sus propias aplicaciones de MapReduce, consulte los documentos siguientes:

* [Desarrollo de aplicaciones MapReduce de Java para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Desarrollo de aplicaciones MapReduce de Python para HDInsight](apache-hadoop-streaming-python.md)

## <a id="run"></a>Ejecución de MapReduce

HDInsight puede ejecutar trabajos de HiveQL mediante varios métodos. Use la tabla siguiente para decidir cuál es el método adecuado para usted luego siga el vínculo para ver un tutorial.

| **Use esto**... | **...para hacer esto** | ...con este **sistema operativo de clúster** | ...desde este **sistema operativo de cliente** |
|:--- |:--- |:--- |:--- |
| [SSH](apache-hadoop-use-mapreduce-ssh.md) |Uso del comando Hadoop mediante **SSH** |Linux |Linux, Unix, Mac OS X o Windows |
| [Curl](apache-hadoop-use-mapreduce-curl.md) |Enviar el trabajo de forma remota mediante **REST** |Linux o Windows |Linux, Unix, Mac OS X o Windows |
| [Windows PowerShell](apache-hadoop-use-mapreduce-powershell.md) |Enviar el trabajo de forma remota mediante **Windows PowerShell** |Linux o Windows |Windows |
| [Escritorio remoto](apache-hadoop-use-mapreduce-remote-desktop.md) (HDInsight 3.2 y 3.3) |Uso del comando Hadoop mediante **Escritorio remoto** |Windows |Windows |

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

## <a id="nextsteps"></a>Pasos siguientes

Para conocer más acerca del trabajo con datos en HDInsight, consulte los siguientes documentos:

* [Desarrollo de programas MapReduce de Java para HDInsight](apache-hadoop-develop-deploy-java-mapreduce-linux.md)

* [Desarrollo de programas de MapReduce de streaming de Python para HDInsight](apache-hadoop-streaming-python.md)

* [Uso de Hive con HDInsight][hdinsight-use-hive]

* [Uso de Pig con HDInsight][hdinsight-use-pig]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]:apache-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: apache-hadoop-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]:../hdinsight-use-hive.md
[hdinsight-use-pig]:hdinsight-use-pig.md


[powershell-install-configure]: /powershell/azureps-cmdlets-docs

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif
