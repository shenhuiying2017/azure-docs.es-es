---
title: 'Uso de C# con Hive y Pig en Hadoop en HDInsight: Azure | Microsoft Docs'
description: "Vea cómo usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en HDInsight de Azure."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: d83def76-12ad-4538-bb8e-3ba3542b7211
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 12/05/2017
ms.author: larryfr
ms.openlocfilehash: 1ad6ba7126b210ddc671026244c4c614d7010000
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="use-c-user-defined-functions-with-hive-and-pig-streaming-on-hadoop-in-hdinsight"></a>Usar funciones definidas por el usuario de C# con el streaming de Hive y Pig en Hadoop de HDInsight

Aprenda a usar funciones definidas por el usuario (UDF) de C# con Apache Hive y Pig en HDInsight.

> [!IMPORTANT]
> Los pasos de este tutorial se aplican a los clústeres de HDInsight basados tanto en Linux como en Windows. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para obtener más información, consulte el artículo relativo al [control de versiones de componentes de HDInsight](../hdinsight-component-versioning.md).

Tanto Hive como Pig pueden pasar datos a aplicaciones externas para el procesamiento. Este proceso se conoce como _streaming_. Cuando se usa una aplicación .NET, los datos se pasan a la aplicación en STDIN y la aplicación devuelve los resultados en STDOUT. Para leer y escribir en STDIN y STDOUT, puede usar `Console.ReadLine()` y `Console.WriteLine()` desde una aplicación de la consola.

## <a name="prerequisites"></a>Requisitos previos

* Estar familiarizado con la escritura y la compilación del código C# orientado a .NET Framework 4.5.

    * Use el IDE que prefiera. Recomendamos [Visual Studio](https://www.visualstudio.com/vs) 2015, 2017 o [Visual Studio Code](https://code.visualstudio.com/). En los pasos descritos en este documento se utiliza Visual Studio 2017.

* Una manera de cargar archivos .exe en el clúster y ejecutar trabajos de Pig y Hive. Es aconsejable usar las herramientas de Data Lake para Visual Studio, Azure PowerShell y CLI de Azure. En los pasos descritos en este documento se emplean las herramientas Data Lake para Visual Studio para cargar los archivos y ejecutar la consulta de ejemplo de Hive.

    Para obtener información sobre otros modos de ejecutar consultas de Hive y tareas de Pig, consulte los documentos siguientes:

    * [Uso de Apache Hive con HDInsight](hdinsight-use-hive.md)

    * [Uso de Apache Pig con HDInsight](hdinsight-use-pig.md)

* Hadoop en un clúster de HDInsight. Para obtener más información sobre cómo crear un clúster, consulte [Creación de un clúster de HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).

## <a name="net-on-hdinsight"></a>.NET en HDInsight

* Los clústeres de __HDInsight basado en Linux__ se sirven de [Mono (https://mono-project.com)](https://mono-project.com) para ejecutar aplicaciones .NET. La versión 4.2.1 de Mono está incluida en la versión 3.5 de HDInsight.

    Si desea conocer más detalles sobre la compatibilidad entre Mono y las versiones de .NET Framework, consulte la página en la que se trata la [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

    Para usar una versión específica de Mono, consulte el documento [Instalación o actualización de Mono](../hdinsight-hadoop-install-mono.md).

* Los clústeres de __HDInsight basados en Windows__ usan Microsoft .NET CLR para ejecutar aplicaciones. NET.

Para obtener más información sobre la versión de .NET framework y Mono incluida en las versiones de HDInsight, consulte el artículo relativo a las [versiones de componentes de HDInsight](../hdinsight-component-versioning.md).

## <a name="create-the-c-projects"></a>Creación de proyectos de C\#

### <a name="hive-udf"></a>UDF de Hive

1. Abra Visual Studio y cree una solución. Para el tipo de proyecto, elija **Aplicación de consola (.NET Framework)** y asigne al nuevo proyecto el nombre **HiveCSharp**.

    > [!IMPORTANT]
    > Seleccione __.NET Framework 4.5__ si está usando un clúster de HDInsight basado en Linux. Si desea conocer más detalles sobre la compatibilidad entre Mono y las versiones de .NET Framework, consulte la página en la que se trata la [compatibilidad de Mono](http://www.mono-project.com/docs/about-mono/compatibility/).

2. Reemplace el contenido de **Program.cs** por el código siguiente:

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

### <a name="pig-udf"></a>UDF de Pig

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

    Este código analiza las líneas que se envían desde Pig y cambia el formato de las que comienzan por `java.lang.Exception`.

3. Guarde **Program.cs**y, a continuación, compile el proyecto.

## <a name="upload-to-storage"></a>Carga en el almacenamiento

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure** y, después, haga lo mismo con **HDInsight**.

3. Si se le solicitan, escriba sus credenciales de suscripción de Azure y, a continuación, haga clic en **Iniciar sesión**.

4. Expanda el clúster de HDInsight en el que desee implementar esta aplicación. Aparecerá una entrada con el texto __(Cuenta de almacenamiento predeterminada)__ en la lista.

    ![Explorador de servidores en el que se muestra la cuenta de almacenamiento para el clúster](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/storage.png)

    * Si se puede expandir esta entrada, significa que está utilizando una __cuenta de Azure Storage__ como almacenamiento predeterminado para el clúster. Para ver los archivos incluidos en el almacenamiento predeterminado del clúster, expanda la entrada y, a continuación, haga doble clic en __(Contenedor predeterminado)__.

    * Si, por el contrario, no puede expandirse, quiere decir que está usando __Azure Data Lake Store__ como almacenamiento predeterminado para el clúster. Para ver los archivos incluidos en el almacenamiento predeterminado del clúster, haga doble clic en la entrada __(Cuenta de almacenamiento predeterminada)__.

6. Para cargar los archivos .exe, siga uno de estos métodos:

    * Si utiliza una __cuenta de Azure Storage__, haga clic en el icono de carga y, a continuación, navegue hasta la carpeta **bin\debug** para localizar el proyecto **HiveCSharp**. Por último, elija el archivo **HiveCSharp.exe** y haga clic en **Aceptar**.

        ![icono para cargar](./media/apache-hadoop-hive-pig-udf-dotnet-csharp/upload.png)
    
    * Si usa __Azure Data Lake Store__, haga clic con el botón derecho en un área vacía de la lista de archivos y, después, elija __Cargar__. Por último, elija el archivo **HiveCSharp.exe** y haga clic en **Abrir**.

    Una vez que la carga del archivo __HiveCSharp.exe__ haya finalizado, repita el proceso de carga con el archivo __PigUDF.exe__.

## <a name="run-a-hive-query"></a>Ejecución de una consulta de Hive

1. En Visual Studio, abra el **Explorador de servidores**.

2. Expanda **Azure** y, después, haga lo mismo con **HDInsight**.

3. Haga clic con el botón derecho en el clúster en el que ha implementado la aplicación **HiveCSharp** y, a continuación, elija **Escribir una consulta de Hive**.

4. Use el siguiente texto para la consulta de Hive:

    ```hiveql
    -- Uncomment the following if you are using Azure Storage
    -- add file wasb:///HiveCSharp.exe;
    -- Uncomment the following if you are using Azure Data Lake Store
    -- add file adl:///HiveCSharp.exe;

    SELECT TRANSFORM (clientid, devicemake, devicemodel)
    USING 'HiveCSharp.exe' AS
    (clientid string, phoneLabel string, phoneHash string)
    FROM hivesampletable
    ORDER BY clientid LIMIT 50;
    ```

    > [!IMPORTANT]
    > Quite la marca de comentario de la instrucción `add file` que coincide con el tipo de almacenamiento predeterminado que se usa para su clúster.

    Esta consulta selecciona los campos `clientid`, `devicemake` y `devicemodel` de `hivesampletable`, y los pasa a la aplicación HiveCSharp.exe. La consulta espera que la aplicación devuelva tres campos, que están almacenados como `clientid`, `phoneLabel` y `phoneHash`. La consulta también espera que HiveCSharp.exe esté en la raíz del contenedor de almacenamiento predeterminado.

5. Haga clic en **Enviar** para enviar el trabajo al clúster de HDInsight. Se abre la ventana **Resumen del trabajo de Hive**.

6. Haga clic en **Actualizar** para actualizar el resumen hasta que el valor de **Estado del trabajo** cambie a **Completado**. Para ver la salida del trabajo, haga clic en **Salida de trabajo**.

## <a name="run-a-pig-job"></a>Ejecución de un trabajo de Pig

1. Use uno de los métodos siguientes para conectar su clúster de HDInsight:

    * Si está usando un clúster de HDInsight __basado en Linux__, use SSH. Por ejemplo: `ssh sshuser@mycluster-ssh.azurehdinsight.net`. Para obtener más información, consulte [Uso de SSH con HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).
    
    * Si usa un clúster de HDInsight __basado en Windows__, [conecte con el clúster mediante Escritorio remoto](../hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp).

2. Utilice uno de los siguientes comandos para iniciar la línea de comandos de Pig:

        pig

    > [!IMPORTANT]
    > Si usa un clúster basado en Windows, use en su lugar los siguientes comandos:
    > ```
    > cd %PIG_HOME%
    > bin\pig
    > ```

    Se muestra un símbolo del sistema `grunt>`.

3. Escriba lo siguiente para ejecutar un trabajo de Pig que usa la aplicación de .NET Framework:

        DEFINE streamer `PigUDF.exe` CACHE('/PigUDF.exe');
        LOGS = LOAD '/example/data/sample.log' as (LINE:chararray);
        LOG = FILTER LOGS by LINE is not null;
        DETAILS = STREAM LOG through streamer as (col1, col2, col3, col4, col5);
        DUMP DETAILS;

    La instrucción `DEFINE` crea un alias de `streamer` para las aplicaciones de pigudf.exe y `CACHE` lo carga desde el almacenamiento predeterminado del clúster. Más adelante, `streamer` se usa con el operador `STREAM` para procesar las líneas individuales incluidas en el registro y devolver los datos como una serie de columnas.

    > [!NOTE]
    > El nombre de la aplicación que se usa para el streaming debe estar encerrado entre caracteres \` (acento grave) cuando se usa como alias, y entre caracteres ' (comilla sencilla) cuando se utiliza con `SHIP`.

4. Después de escribir la última línea, debe iniciarse el trabajo. Devuelve un resultado similar al texto siguiente:

        (2012-02-03 20:11:56 SampleClass5 [WARN] problem finding id 1358451042 - java.lang.Exception)
        (2012-02-03 20:11:56 SampleClass5 [DEBUG] detail for id 1976092771)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1317358561)
        (2012-02-03 20:11:56 SampleClass5 [TRACE] verbose detail for id 1737534798)
        (2012-02-03 20:11:56 SampleClass7 [DEBUG] detail for id 1475865947)

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a utilizar una aplicación de .NET Framework desde Hive y Pig en HDInsight. Si desea obtener información sobre cómo utilizar Python con Hive y Pig, consulte [Uso de Python con Hive y Pig en HDInsight](python-udf-hdinsight.md).

Para conocer otras formas de usar Pig y Hive, y para obtener información sobre el uso de MapReduce, vea los siguientes documentos:

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de MapReduce con HDInsight](hdinsight-use-mapreduce.md)
