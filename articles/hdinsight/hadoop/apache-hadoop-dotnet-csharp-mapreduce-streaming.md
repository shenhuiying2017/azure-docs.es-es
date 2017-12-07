---
title: Uso de C# con MapReduce en Hadoop en HDInsight (Azure) | Microsoft Docs
description: "Descubra cómo utilizar C# para crear soluciones de MapReduce con Hadoop en Azure HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.custom: hdinsightactive
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: cb07f173a0ff669cc8fa56d1ba37a4a3df8d8753
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-c-with-mapreduce-streaming-on-hadoop-in-hdinsight"></a>Uso de C# con el streaming de MapReduce en Hadoop en HDInsight

Descubra cómo utilizar C# para crear una solución de MapReduce en HDInsight.

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el artículo relativo al [control de versiones de componentes de HDInsight](../hdinsight-component-versioning.md).

El streaming de Hadoop es una utilidad que permite ejecutar trabajos de MapReduce mediante un script o ejecutable. En este ejemplo, se emplea .NET para implementar el asignador y reductor en una solución de recuento de palabras.

## <a name="net-on-hdinsight"></a>.NET en HDInsight

Los clústeres de __HDInsight basado en Linux__ se sirven de [Mono (https://mono-project.com)](https://mono-project.com) para ejecutar aplicaciones .NET. La versión 4.2.1 de Mono está incluida en la versión 3.5 de HDInsight. Para obtener más información sobre la versión de Mono incluida en HDInsight, consulte el artículo relativo a las [versiones de componentes de HDInsight](../hdinsight-component-versioning.md). Para usar una versión específica de Mono, consulte el documento [Instalación o actualización de Mono](../hdinsight-hadoop-install-mono.md).

Si desea conocer más detalles sobre la compatibilidad entre Mono y las versiones de .NET Framework, consulte la página en la que se trata la [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

## <a name="how-hadoop-streaming-works"></a>Funcionamiento del streaming de Hadoop

A continuación se expone el proceso básico que se emplea para realizar streaming en este documento:

1. Hadoop transfiere datos al asignador (mapper.exe en este ejemplo) en STDIN.
2. El asignador procesa los datos y envía pares clave-valor delimitados por tabulaciones a STDOUT.
3. Hadoop lee los resultados, que, a continuación, se transfieren al reductor (reducer.exe en este ejemplo) en STDIN.
4. El reductor lee los pares clave-valor delimitados por tabulaciones, procesa los datos y, después, envía el resultado en forma de pares clave-valor delimitados por tabulaciones en STDOUT.
5. Hadoop lee los resultados, que, a continuación, se escriben en el directorio de salida.

Para obtener más información sobre el streaming, consulte la página relativa al [streaming de Hadoop (https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html)](https://hadoop.apache.org/docs/r2.7.1/hadoop-streaming/HadoopStreaming.html).

## <a name="prerequisites"></a>Requisitos previos

* Estar familiarizado con la escritura y la compilación del código C# orientado a .NET Framework 4.5. En los pasos descritos en este documento se utiliza Visual Studio 2017.

* Una forma de cargar archivos .exe en el clúster. En los pasos descritos en este documento se emplean las herramientas Data Lake para Visual Studio para cargar los archivos en el almacenamiento principal del clúster.

* Azure PowerShell o un cliente de SSH.

* Hadoop en un clúster de HDInsight. Para obtener más información sobre cómo crear un clúster, consulte [Creación de un clúster de HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="create-the-mapper"></a>Creación del asignador

En Visual Studio, cree una nueva __aplicación de consola__ denominada __mapper__. Use el código siguiente para la aplicación:

```csharp
using System;
using System.Text.RegularExpressions;

namespace mapper
{
    class Program
    {
        static void Main(string[] args)
        {
            string line;
            //Hadoop passes data to the mapper on STDIN
            while((line = Console.ReadLine()) != null)
            {
                // We only want words, so strip out punctuation, numbers, etc.
                var onlyText = Regex.Replace(line, @"\.|;|:|,|[0-9]|'", "");
                // Split at whitespace.
                var words = Regex.Matches(onlyText, @"[\w]+");
                // Loop over the words
                foreach(var word in words)
                {
                    //Emit tab-delimited key/value pairs.
                    //In this case, a word and a count of 1.
                    Console.WriteLine("{0}\t1",word);
                }
            }
        }
    }
}
```

Después de crear la aplicación, compílela para generar el archivo `/bin/Debug/mapper.exe` en el directorio del proyecto.

## <a name="create-the-reducer"></a>Creación del reductor

En Visual Studio, cree una nueva __aplicación de consola__ denominada __reducer__. Use el código siguiente para la aplicación:

```csharp
using System;
using System.Collections.Generic;

namespace reducer
{
    class Program
    {
        static void Main(string[] args)
        {
            //Dictionary for holding a count of words
            Dictionary<string, int> words = new Dictionary<string, int>();

            string line;
            //Read from STDIN
            while ((line = Console.ReadLine()) != null)
            {
                // Data from Hadoop is tab-delimited key/value pairs
                var sArr = line.Split('\t');
                // Get the word
                string word = sArr[0];
                // Get the count
                int count = Convert.ToInt32(sArr[1]);

                //Do we already have a count for the word?
                if(words.ContainsKey(word))
                {
                    //If so, increment the count
                    words[word] += count;
                } else
                {
                    //Add the key to the collection
                    words.Add(word, count);
                }
            }
            //Finally, emit each word and count
            foreach (var word in words)
            {
                //Emit tab-delimited key/value pairs.
                //In this case, a word and a count of 1.
                Console.WriteLine("{0}\t{1}", word.Key, word.Value);
            }
        }
    }
}
```

Después de crear la aplicación, compílela para generar el archivo `/bin/Debug/reducer.exe` en el directorio del proyecto.

## <a name="upload-to-storage"></a>Carga en el almacenamiento

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure** y, después, haga lo mismo con **HDInsight**.

3. Si se le solicitan, escriba sus credenciales de suscripción de Azure y, a continuación, haga clic en **Iniciar sesión**.

4. Expanda el clúster de HDInsight en el que desee implementar esta aplicación. Aparecerá una entrada con el texto __(Cuenta de almacenamiento predeterminada)__ en la lista.

    ![Explorador de servidores en el que se muestra la cuenta de almacenamiento para el clúster](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/storage.png)

    * Si se puede expandir esta entrada, significa que está utilizando una __cuenta de Azure Storage__ como almacenamiento predeterminado para el clúster. Para ver los archivos incluidos en el almacenamiento predeterminado del clúster, expanda la entrada y, a continuación, haga doble clic en __(Contenedor predeterminado)__.

    * Si, por el contrario, no puede expandirse, quiere decir que está usando __Azure Data Lake Store__ como almacenamiento predeterminado para el clúster. Para ver los archivos incluidos en el almacenamiento predeterminado del clúster, haga doble clic en la entrada __(Cuenta de almacenamiento predeterminada)__.

5. Para cargar los archivos .exe, siga uno de estos métodos:

    * Si utiliza una __cuenta de Azure Storage__, haga clic en el icono de carga y, a continuación, navegue hasta la carpeta **bin\debug** para localizar el proyecto **mapper**. Por último, seleccione el archivo **mapper.exe** y haga clic en **Aceptar**.

        ![icono para cargar](./media/apache-hadoop-dotnet-csharp-mapreduce-streaming/upload.png)
    
    * Si usa __Azure Data Lake Store__, haga clic con el botón derecho en un área vacía de la lista de archivos y, después, elija __Cargar__. Por último, seleccione el archivo **mapper.exe** y haga clic en **Abrir**.

    Una vez que la carga del archivo __mapper.exe__ haya finalizado, repita el proceso de carga con el archivo __reducer.exe__.

## <a name="run-a-job-using-an-ssh-session"></a>Ejecución de un trabajo mediante una sesión de SSH

1. Use SSH para conectarse al clúster de HDInsight. Para más información, consulte [Uso SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Use uno de los siguientes comandos para iniciar el trabajo de MapReduce:

    * Si utiliza __Data Lake Store__ como almacenamiento predeterminado:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files adl:///mapper.exe,adl:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```
    
    * Si emplea __Azure Storage__ como almacenamiento predeterminado:

        ```bash
        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files wasb:///mapper.exe,wasb:///reducer.exe -mapper mapper.exe -reducer reducer.exe -input /example/data/gutenberg/davinci.txt -output /example/wordcountout
        ```

    En la lista siguiente, se describen las funciones de cada parámetro:

    * `hadoop-streaming.jar`: el archivo .jar que contiene la funcionalidad de streaming de MapReduce.
    * `-files`: agrega los archivos `mapper.exe` y `reducer.exe` al trabajo. El texto `adl:///` o `wasb:///` que aparece antes de cada archivo es la ruta a la raíz del almacenamiento predeterminado del clúster.
    * `-mapper`: especifica el archivo que implementa el asignador.
    * `-reducer`: indica qué archivo implementa el reductor.
    * `-input`: los datos de entrada.
    * `-output`: el directorio de salida.

3. Una vez completado el trabajo de MapReduce, use lo siguiente para ver los resultados:

    ```bash
    hdfs dfs -text /example/wordcountout/part-00000
    ```

    Este texto es un ejemplo de los datos que devuelve este comando:

        you     1128
        young   38
        younger 1
        youngest        1
        your    338
        yours   4
        yourself        34
        yourselves      3
        youth   17

## <a name="run-a-job-using-powershell"></a>Ejecución de un trabajo mediante PowerShell

Sírvase del siguiente script de PowerShell para ejecutar un trabajo de MapReduce y descargar los resultados.

[!code-powershell[main](../../../powershell_scripts/hdinsight/use-csharp-mapreduce/use-csharp-mapreduce.ps1?range=5-87)]

Este script le solicitará el nombre de la cuenta de inicio de sesión y la contraseña del clúster, junto con el nombre del clúster de HDInsight. Una vez que el trabajo se complete, el resultado se descarga en un archivo denominado `output.txt`. A continuación, se muestra un ejemplo de los datos que contiene el archivo `output.txt`:

    you     1128
    young   38
    younger 1
    youngest        1
    your    338
    yours   4
    yourself        34
    yourselves      3
    youth   17

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el uso de MapReduce con HDInsight, consulte [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md).

Para obtener información acerca del uso de C# con Hive y Pig, consulte [Usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en Hadoop de HDInsight](apache-hadoop-hive-pig-udf-dotnet-csharp.md).

Para obtener información sobre cómo emplear C# con Storm en HDInsight, consulte el artículo relativo al [desarrollo de topologías de C# para Storm en HDInsight](../storm/apache-storm-develop-csharp-visual-studio-topology.md).