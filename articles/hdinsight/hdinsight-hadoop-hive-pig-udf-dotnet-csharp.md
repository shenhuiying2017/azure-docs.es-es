---
title: Uso de C# con Hive y Pig en Hadoop en HDInsight | Microsoft Docs
description: "Vea cómo usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en HDInsight de Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/02/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 257138fddc75b39985ba974b1314e978a554b1e2
ms.lasthandoff: 03/07/2017


---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en Hadoop de HDInsight

Hive y Pig resultan excelentes para trabajar con datos en Azure HDInsight, pero en ocasiones se necesita un lenguaje con una finalidad más general. Hive y Pig permiten efectuar llamadas a código externo a través de funciones definidas por el usuario (UDF) o de streaming.

Con este documento, aprenderá a usar C# con Hive y Pig.

> [!IMPORTANT]
> Los pasos descritos en este documento requieren un clúster de HDInsight que usa Windows como sistema operativo. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Requisitos previos

* Windows 7 o posterior.

* Visual Studio con las siguientes versiones:

  * Visual Studio 2012 Professional/Premium/Ultimate con [actualización 4](http://www.microsoft.com/download/details.aspx?id=39305)

  * Visual Studio 2013 Comunidad/Professional/Premium/Ultimate con [actualización 4](https://www.microsoft.com/download/details.aspx?id=44921)

  * Visual Studio 2015

  * Visual Studio 2017

* Hadoop en un clúster de HDInsight, consulte [Aprovisionamiento de un clúster de HDInsight](hdinsight-provision-clusters.md) para conocer los pasos que le permitirán crear un clúster.

* Herramientas de Hadoop para Visual Studio o herramientas de Data Lake para Visual Studio. Consulte [Introducción al uso de herramientas de Hadoop de HDInsight para Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) para conocer los pasos que le permitirán instalar y configurar las herramientas.

    > [!NOTE]
    > Las herramientas de Data Lake se instalan en Visual Studio 2017 seleccionando la carga de trabajo __Desarrollo de Azure__ durante la instalación.

## <a name="net-on-hdinsight"></a>.NET en HDInsight

Common Language Runtime (CLR) de .NET y los marcos se instalan de forma predeterminada en los clústeres de HDInsight basados en Windows. El CLR de .NET permite usar las aplicaciones de C# con el streaming de Hive y Pig (los datos se pasan entre Hive y Pig, y la aplicación de C# a través de stdin y stdout).

> [!NOTE]
> Actualmente, no se pueden ejecutar UDF de .NET Framework en clústeres de HDInsight basados en Linux.


## <a name="net-and-streaming"></a>.NET y streaming

El streaming obliga a Hive y Pig a pasar datos a una aplicación externa a través de stdout y a recibir los resultados a través de stdin. Para las aplicaciones de C#, use `Console.ReadLine()` y `Console.WriteLine()`.

Dado que Hive y Pig deben invocar la aplicación en el tiempo de ejecución, hay que usar la plantilla de **Aplicación de consola (.NET Framework)** en los proyectos de C#.

## <a name="hive-and-c35"></a>Hive y C&#35;

### <a name="create-the-c-project"></a>Creación de un proyecto de C#

1. Abra Visual Studio y cree una solución. Para el tipo de proyecto, elija **Aplicación de consola (.NET Framework)** y asigne al nuevo proyecto el nombre **HiveCSharp**.

2. Reemplace el contenido de **Program.cs** por lo siguiente:

    ```csharp
    using System;
    using System.Security.Cryptography;
    using System.Text;
    using System.Threading.Tasks;

    namespace HiveCSharp
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Parse the string, trimming line feeds
                    // and splitting fields at tabs
                    line = line.TrimEnd('\n');
                    string[] field = line.Split('\t');
                    string phoneLabel = field[1] + ' ' + field[2];
                    // Emit new data to stdout, delimited by tabs
                    Console.WriteLine("{0}\t{1}\t{2}", field[0], phoneLabel, GetMD5Hash(phoneLabel));
                }
            }
            /// <summary>
            /// Returns an MD5 hash for the given string
            /// </summary>
            /// <param name="input">string value</param>
            /// <returns>an MD5 hash</returns>
            static string GetMD5Hash(string input)
            {
                // Step 1, calculate MD5 hash from input
                MD5 md5 = System.Security.Cryptography.MD5.Create();
                byte[] inputBytes = System.Text.Encoding.ASCII.GetBytes(input);
                byte[] hash = md5.ComputeHash(inputBytes);

                // Step 2, convert byte array to hex string
                StringBuilder sb = new StringBuilder();
                for (int i = 0; i < hash.Length; i++)
                {
                    sb.Append(hash[i].ToString("x2"));
                }
                return sb.ToString();
            }
        }
    }
    ```

3. Compile el proyecto.

### <a name="upload-to-storage"></a>Carga en el almacenamiento

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure** y, después, haga lo mismo con **HDInsight**.

3. Si se le solicitan, escriba sus credenciales de suscripción de Azure y, a continuación, haga clic en **Iniciar sesión**.

4. Expanda el clúster de HDInsight en el que desea implementar esta aplicación y, a continuación, expanda **Cuenta de almacenamiento predeterminada**.

    ![Explorador de servidores en el que se muestra la cuenta de almacenamiento para el clúster](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

5. Haga doble clic en **Contenedor predeterminado** para el clúster para ver el contenido del contenedor predeterminado.
6. Haga clic en el icono de carga y, a continuación, vaya a la carpeta **bin\debug** del proyecto **HiveCSharp**. Por último, elija el archivo **HiveCSharp.exe** y haga clic en **Aceptar**.

    ![icono para cargar](./media/hdinsight-hadoop-hive-pig-udf-dotnet-csharp/upload.png)

7. Una vez que finalice la carga, se puede usar la aplicación desde una consulta de Hive.

### <a name="hive-query"></a>Consulta de Hive

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure** y, después, haga lo mismo con **HDInsight**.

3. Haga clic con el botón derecho en el clúster en el que ha implementado la aplicación **HiveCSharp** y, a continuación, elija **Escribir una consulta de Hive**.

4. Use el siguiente texto para la consulta de Hive:

    ```hiveql
    add file wasbs:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    Esta consulta selecciona los campos `clientid`, `devicemake` y `devicemodel` de `hivesampletable`, y los pasa a la aplicación HiveCSharp.exe. La consulta espera que la aplicación devuelva tres campos, que están almacenados como `clientid`, `phoneLabel` y `phoneHash`. La consulta también espera que HiveCSharp.exe esté en la raíz del contenedor de almacenamiento predeterminado (`add file wasbs:///HiveCSharp.exe`).

5. Haga clic en **Enviar** para enviar el trabajo al clúster de HDInsight. Se abre la ventana **Resumen del trabajo de Hive**.

6. Haga clic en **Actualizar** para actualizar el resumen hasta que el valor de **Estado del trabajo** cambie a **Completado**. Para ver la salida del trabajo, haga clic en **Salida de trabajo**.

## <a name="pig-and-c35"></a>Pig y C&#35;

### <a name="create-the-c-project"></a>Creación de un proyecto de C#

1. Abra Visual Studio y cree una solución. Para el tipo de proyecto, elija **Aplicación de consola**. Asigne al nuevo proyecto el nombre **PigUDF**.

2. Reemplace el contenido del archivo **Program.cs** por el código siguiente:

    ```csharp
    using System;

    namespace PigUDF
    {
        class Program
        {
            static void Main(string[] args)
            {
                string line;
                // Read stdin in a loop
                while ((line = Console.ReadLine()) != null)
                {
                    // Fix formatting on lines that begin with an exception
                    if(line.StartsWith("java.lang.Exception"))
                    {
                        // Trim the error info off the beginning and add a note to the end of the line
                        line = line.Remove(0, 21) + " - java.lang.Exception";
                    }
                    // Split the fields apart at tab characters
                    string[] field = line.Split('\t');
                    // Put fields back together for writing
                    Console.WriteLine(String.Join("\t",field));
                }
            }
        }
    }
    ```

    Esta aplicación analiza las líneas que se envían desde Pig y cambia el formato de las que comiencen por `java.lang.Exception`.

3. Guarde **Program.cs**y, a continuación, compile el proyecto.

### <a name="upload-the-application"></a>Cargue la aplicación.

1. El streaming de Pig espera que la aplicación sea local en el sistema de archivos del clúster. Habilite el Escritorio remoto para el clúster de HDInsight y conéctese a él siguiendo las instrucciones dadas en [Conexión a los clústeres de HDInsight con RDP](hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. Una vez conectado, copie **PigUDF.exe** desde el directorio **bin/debug** para el proyecto PigUDF en el equipo local y péguelo en el directorio **% PIG_HOME %** del clúster.

### <a name="use-the-application-from-pig-latin"></a>Uso de la aplicación desde Pig Latin

1. Desde la sesión de escritorio remoto, use el icono de **línea de comandos de Hadoop** del escritorio para iniciar la línea de comandos de Hadoop.

2. Utilice lo siguiente para iniciar la línea de comandos de Pig:

        cd %PIG_HOME%
        bin\pig

    Se muestra un símbolo del sistema `grunt>`.
    
3. Escriba lo siguiente para ejecutar un trabajo de Pig con la aplicación de .NET Framework:

        DEFINE streamer `pigudf.exe` SHIP('pigudf.exe');
        LOGS = LOAD 'wasbs:///example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    La instrucción `DEFINE` crea un alias de `streamer` para las aplicaciones de pigudf.exe, y `SHIP` lo distribuye entre los nodos del clúster. Más adelante, `streamer` se usa con el operador `STREAM` para procesar las líneas individuales incluidas en el registro y devolver los datos como una serie de columnas.

    > [!NOTE]
    > El nombre de la aplicación que se usa para el streaming debe estar encerrado entre caracteres \` (acento grave) cuando se usa como alias, y entre caracteres ' (comilla sencilla) cuando se utiliza con `SHIP`.

4. Después de escribir la última línea, debe iniciarse el trabajo. Devuelve un resultado similar al texto siguiente:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="summary"></a>Resumen

En este documento, ha aprendido a utilizar una aplicación de .NET Framework desde Hive y Pig en HDInsight. Si desea obtener información sobre cómo utilizar Python con Hive y Pig, consulte [Uso de Python con Hive y Pig en HDInsight](hdinsight-python.md).

Para conocer otras formas de usar Pig y Hive, y para obtener información sobre el uso de MapReduce, vea los siguientes documentos:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)

