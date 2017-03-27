---
title: Desarrollo de trabajos de MapReduce para Python con HDInsight | Microsoft Docs
description: "Aprenda a crear y ejecutar trabajos de MapReduce para Python en clústeres de HDInsight basado en Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 7631d8d9-98ae-42ec-b9ec-ee3cf7e57fb3
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/06/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: cf233279c0a26c3d2970596b1bb515508da20b89
ms.openlocfilehash: ad696f14d48452840805bc413d890309e523ce34
ms.lasthandoff: 02/07/2017


---
# <a name="develop-python-streaming-programs-for-hdinsight"></a>Desarrollo de programas de streaming para HDInsight

Hadoop proporciona una API de streaming para MapReduce que le permite escribir mapas y reducir funciones en lenguajes distintos de Java. En este artículo, aprenderá a usar Python para realizar operaciones de MapReduce.

Este artículo se basa en información y en los ejemplos publicados por Michael Noll en [Writing an Hadoop MapReduce Program in Python](http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/).

## <a name="prerequisites"></a>Requisitos previos

Para completar los pasos de este artículo, necesitará lo siguiente:

* Un clúster de Hadoop en HDInsight basado en Linux

  > [!IMPORTANT]
  > Los pasos descritos en este documento requieren un clúster de HDInsight que use Linux. Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Para más información, consulte [El contrato de nivel de servicio para las versiones de clúster de HDInsight](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Un editor de texto
  
  > [!IMPORTANT]
  > El editor de texto debe usar LF como final de línea. Si usa CRLF, se producirán errores al ejecutar el trabajo MapReduce en clústeres de HDInsight basados en Linux. Si no está seguro, use el paso opcional de la sección [Ejecución de MapReduce](#run-mapreduce) para convertir cualquier CRLF que haya en LF.

* **Familiaridad con SSH y SCP**. Para obtener más información sobre el uso de SSH y SCP con HDInsight, vea lo siguiente:
  
  * **Clientes Linux, Unix u OS X**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Linux, Unix u OS X (vista previa)](hdinsight-hadoop-linux-use-ssh-unix.md)

  * **Clientes Windows**: vea [Utilización de SSH con Hadoop en HDInsight basado en Linux desde Windows (vista previa)](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="word-count"></a>Recuento de palabras

Para este ejemplo, implementará un recuento de palabras básico mediante el uso de un asignador y un reductor. El asignador divide las oraciones en palabras individuales y el reductor agrega las palabras y los recuentos para generar la salida.

El siguiente diagrama de flujo ilustra lo que sucede durante las fases de asignación y reducción.

![ilustración de reducción del mapa](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

## <a name="why-python"></a>¿Por qué Python?

Python es un lenguaje de programación de uso general y alto nivel que le permite expresar conceptos en menos líneas de código que muchos de los otros lenguajes. Se volvió popular recientemente entre los científicos de datos como un lenguaje para la creación de prototipos porque su naturaleza interpretada, sus tipos dinámicos y su sintaxis elegante lo hacen apto para un desarrollo de aplicaciones rápido.

Python está instalado en todos los clústeres de HDInsight.

## <a name="streaming-mapreduce"></a>Transmisión de MapReduce

Hadoop le permite especificar un archivo que contiene la lógica de asignación y reducción que usa un trabajo. Los requisitos específicos de la lógica de asignación y reducción son:

* **Entrada**: los componentes de asignación y reducción deben leer los datos de entrada desde STDIN.
* **Salida**: los componentes de asignación y reducción deben escribir los datos de salida en STDOUT.
* **Formato de datos**: los datos consumidos y producidos deben ser un par clave-valor, separado por un carácter de tabulación.

Python puede controlar fácilmente estos requisitos si usa el módulo **sys** para leer desde STDIN y **print** para imprimir a STDOUT. La tarea restante consiste simplemente en dar formato a los datos con un carácter de tabulación (`\t`) entre la clave y el valor.

## <a name="create-the-mapper-and-reducer"></a>Creación del asignador y del reductor

El asignador y el reductor son archivos de texto, en este caso **mapper.py** y **reducer.py** (para que quede claro qué hace cada uno). Puede crearlos con el editor que prefiera.

### <a name="mapperpy"></a>Mapper.py

Cree un archivo nuevo llamado **mapper.py** y use el siguiente código como el contenido:

```python
#!/usr/bin/env python

# Use the sys module
import sys

# 'file' in this case is STDIN
def read_input(file):
    # Split each line into words
    for line in file:
        yield line.split()

def main(separator='\t'):
    # Read the data using read_input
    data = read_input(sys.stdin)
    # Process each words returned from read_input
    for words in data:
        # Process each word
        for word in words:
            # Write to STDOUT
            print '%s%s%d' % (word, separator, 1)

if __name__ == "__main__":
    main()
```

Dedique un momento para leer el código y comprender lo que hace.

### <a name="reducerpy"></a>reducer.py

Cree un archivo nuevo llamado **reducer.py** y use los siguientes elementos como el contenido:

```python
#!/usr/bin/env python

# import modules
from itertools import groupby
from operator import itemgetter
import sys

# 'file' in this case is STDIN
def read_mapper_output(file, separator='\t'):
    # Go through each line
    for line in file:
        # Strip out the separator character
        yield line.rstrip().split(separator, 1)

def main(separator='\t'):
    # Read the data using read_mapper_output
    data = read_mapper_output(sys.stdin, separator=separator)
    # Group words and counts into 'group'
    #   Since MapReduce is a distributed process, each word
    #   may have multiple counts. 'group' will have all counts
    #   which can be retrieved using the word as the key.
    for current_word, group in groupby(data, itemgetter(0)):
        try:
            # For each word, pull the count(s) for the word
            #   from 'group' and create a total count
            total_count = sum(int(count) for current_word, count in group)
            # Write to stdout
            print "%s%s%d" % (current_word, separator, total_count)
        except ValueError:
            # Count was not a number, so do nothing
            pass

if __name__ == "__main__":
    main()
```

## <a name="upload-the-files"></a>Carga de los archivos

Tanto **mapper.py** como **reducer.py** deben estar en el nodo principal del clúster antes de poder ejecutarlos. Esta sección proporciona un comando `scp` de ejemplo y un script de Azure PowerShell que se puede utilizar para cargar los archivos en el clúster.

> [!IMPORTANT]
> Hay una diferencia importante entre el uso de `scp` y el de PowerShell para cargar los archivos:
>
> * Si usa `scp` los archivos se colocan en el nodo primario principal del clúster. Esto da por supuesto que posteriormente se conectará al nodo principal y ejecutará el trabajo desde una sesión de SSH.
> * Si usa el script de PowerShell los archivos se colocarán en el almacenamiento predeterminado del clúster. Esto da por supuesto que, más adelante, usará un script de PowerShell para ejecutar el trabajo desde un cliente remoto.

### <a name="upload-using-scp"></a>Carga mediante scp

Desde el entorno de desarrollo, en el mismo directorio en que se encuentran **mapper.py** y **reducer.py**, use el comando siguiente. Reemplace **username** por el nombre de usuario SSH de su clúster y **clustername** por el nombre de su clúster.

`scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`

De esta manera, se copiarán los archivos del sistema local al nodo principal.

> [!NOTE]
> Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada, por ejemplo, `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`.

### <a name="upload-using-powershell"></a>Carga mediante PowerShell

1. En el entorno de desarrollo, cree un nuevo archivo denominado `Put-FilesInHDInsight.ps1` y use lo siguiente como contenido del archivo:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source,
        [Parameter(Mandatory = $true)]
        [String]$destination
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and upload the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Upload the file
            Set-AzureStorageBlobContent `
                -File $source `
                -Blob $destination `
                -Container $storageContainer `
                -Context $context
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Upload the file. Prepend the destination with the cluster root
            Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $source -Destination "$clusterRoot$destination"
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Para utilizar este script para cargar un archivo, utilice lo siguiente en un símbolo del sistema de Azure PowerShell:

    `.\Put-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source mapper.py -destination mapper.py`

    Cuando se le solicite, especifique las credenciales de inicio de sesión HTTP del clúster.

3. Repita el comando, reemplazando `mapper.py` por `reducer.py`. Esto permite cargar los archivos del asignador y del reductor al almacenamiento predeterminado del clúster.

## <a name="run-mapreduce-ssh"></a>Ejecución de MapReduce (SSH)

Siga estos pasos para conectarse al clúster y ejecutar el trabajo de streaming de MapReduce desde una sesión de SSH.

1. Conéctese al clúster mediante SSH:
   
   `ssh username@clustername-ssh.azurehdinsight.net`
   
   > [!NOTE]
   > Si usó una contraseña para proteger la cuenta SSH, se le preguntará la contraseña. Si usó una clave SSH, es posible que deba usar el parámetro `-i` y la ruta de acceso a la clave privada, por ejemplo, `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`.

2. (Opcional) Si, al crear los archivos mapper.py y reducer.py, usó un editor de texto en el que se emplea LF como final de línea, o bien no sabe qué final de línea usa su editor, use los siguientes comandos para convertir todas las apariciones de CRLF en mapper.py y reducer.py a LF.
   
    `perl -pi -e 's/\r\n/\n/g' mappery.py`
    `perl -pi -e 's/\r\n/\n/g' reducer.py`

3. Use el comando siguiente para iniciar el trabajo de MapReduce.
   
    `yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input /example/data/gutenberg/davinci.txt -output /example/wordcountout`
   
    Este comando cuenta con las siguientes partes:
   
   * **hadoop-streaming.jar**: se usa cuando se realizan operaciones de streaming de MapReduce. Crea una interfaz de Hadoop con el código de MapReduce externo que proporciona.

   * **-files**: indica a Hadoop que los archivos especificados son necesarios en este trabajo de MapReduce y que se deben copiar a todos los nodos de trabajo.

   * **-mapper**: indica a Hadoop qué archivo debe usar como asignador.

   * **-reducer**: indica a Hadoop qué archivo debe usar como reductor.

   * **-input**: el archivo de entrada en el cual debemos contar las palabras.

   * **-output**: el directorio al que se escribirá la salida.
     
     > [!NOTE]
     > El trabajo creará este directorio.

Debiera ver gran cantidad de instrucciones **INFO** cuando el trabajo se inicie y, finalmente, debiera ver las operaciones **map** y **reduce** que aparecen como porcentajes.

    15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
    15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
    15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Recibirá información sobre el estado del trabajo cuando finalice.

## <a name="run-mapreduce-powershell"></a>Ejecución de MapReduce (PowerShell)

Utilice los pasos siguientes en el entorno de desarrollo para ejecutar las operaciones de MapReduce del streaming desde PowerShell. El script de PowerShell ejecuta el trabajo conectándose al clúster de HDInsight con WebHCat.

1. Cree un nuevo archivo denominado `Start-HDInsightStreamingPythonJob` y use lo siguiente como contenido del archivo:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$inputPath,
        [Parameter(Mandatory = $true)]
        [String]$outputPath
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the login (HTTPS) credentials for the cluster
    $creds=Get-Credential -Message "Enter the login for the cluster" -UserName "admin"

    # Create the streaming job definition
    # Note: This assumes that the mapper.py and reducer.py
    #       are in the root of default storage. If you put them in a 
    #       subdirectory, change the -Files parameter to the correct path.
    $jobDefinition = New-AzureRmHDInsightStreamingMapReduceJobDefinition `
        -Files "/mapper.py", "/reducer.py" `
        -Mapper "mapper.py" `
        -Reducer "reducer.py" `
        -InputPath $inputPath `
        -OutputPath $outputPath

    # Start the job
    $job = Start-AzureRmHDInsightJob `
        -ClusterName $clusterName `
        -JobDefinition $jobDefinition `
        -HttpCredential $creds

    # Wait for the job to complete
    Wait-AzureRmHDInsightJob `
        -JobId $job.JobId `
        -ClusterName $clusterName `
        -HttpCredential $creds
    ```

2. Use lo siguiente desde un símbolo del sistema de Azure PowerShell para ejecutar el trabajo:

    `.\Start-HDInsightStreamingPythonJob.ps1 -clusterName <your HDInsight cluster name> -inputPath "/example/data/gutenberg/davinci.txt" -outputPath "/example/wordcountout"`

    Se usarán los archivos `mapper.py` y `reducer.py` cargados anteriormente en el clúster para contar las palabras del archivo `davinci.txt`. La salida se almacena en la carpeta `/example/wordcount` del almacenamiento predeterminado del clúster.

    Cuando el trabajo se complete, aparecerá una información similar a la siguiente:

    ```
    Cluster         : myhdicluster
    HttpEndpoint    : myhdicluster.azurehdinsight.net
    State           : SUCCEEDED
    JobId           : job_1486046226168_0004
    ParentId        :
    PercentComplete : map 100% reduce 100%
    ExitValue       : 0
    User            : admin
    Callback        :
    Completed       : done
    StatusFolder    : 2017-02-06T19-14-28-a3dda3ca-f489-4287-afc9-b5e16e2e7c7a
    ```

## <a name="view-the-output"></a>Visualización de la salida

La salida del trabajo se almacena en el directorio `/example/wordcountout`. Puede ver la salida en una sesión de SSH o descargarla de forma local y verla desde PowerShell.

Para ver los datos desde una sesión de SSH en el clúster, use el siguiente comando:

`hdfs dfs -text /example/wordcountout/part-00000`

Esto hace que aparezca una lista de palabras y cuántas veces aparecieron. El siguiente es un ejemplo de los datos de salida:

```
wrenching       1
wretched        6
wriggling       1
wrinkled,       1
wrinkles        2
wrinkling       2
```

Para ver la salida desde el entorno de desarrollo con PowerShell, realice los pasos siguientes:

1. Cree un nuevo archivo denominado `Get-FilesInHDInsight.ps1` y use lo siguiente como contenido del archivo:

    ```PowerShell
    param(
        [Parameter(Mandatory = $true)]
        [String]$clusterName,
        [Parameter(Mandatory = $true)]
        [String]$source
    )
    # Login to your Azure subscription
    # Is there an active Azure subscription?
    $sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
    if(-not($sub))
    {
        Add-AzureRmAccount
    }

    # Get the cluster info
    $clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
    $storageInfo = $clusterInfo.DefaultStorageAccount.split('.')

    # What type of default storage are we using?
    switch ($storageInfo[1])
    {
        "blob" {
            # Get the blob storage information for the cluster
            $resourceGroup = $clusterInfo.ResourceGroup
            $storageAccountName=$storageInfo[0]
            $storageContainer=$clusterInfo.DefaultStorageContainer
            $storageAccountKey=(Get-AzureRmStorageAccountKey `
                -Name $storageAccountName `
                -ResourceGroupName $resourceGroup)[0].Value
            # Create a storage context and download the file
            $context = New-AzureStorageContext `
                -StorageAccountName $storageAccountName `
                -StorageAccountKey $storageAccountKey
            # Download the file
            Get-AzureStorageBlobContent `
                -Container $storageContainer `
                -Blob $source `
                -Context $context `
                -Destination "./output.txt"
            # Display the output
            Get-Content "./output.txt"
        }
        "azuredatalakestore" {
            # Get the Data Lake Store name
            $dataLakeStoreName=$storageInfo[0]
            # Get the root of the HDInsight cluster azuredatalakestore
            $clusterRoot=$clusterInfo.DefaultStorageRootPath
            # Download the file. Prepend the destination with the cluster root
            # NOTE: Unlike getting a blob, this just gets the content and no
            #       file is created locally.
            Get-AzureRmDataLakeStoreItemContent -Account $dataLakeStoreName -Path $clusterRoot$source -Confirm
        }
        default {
            Throw "Unknown storage type: $storageInfo[1]"
        }
    }
    ```

2. Desde un símbolo del sistema de Azure PowerShell, use lo siguiente para ejecutar el script y ver la salida:

    `Get-FilesInHDInsight.ps1 -clusterName <your HDInsight cluster name> -source "example/wordcountout/part-00000"`

    Esto hace que aparezca una lista de palabras y cuántas veces aparecieron. El siguiente es un ejemplo de los datos de salida:

    ```
    wrenching       1
    wretched        6
    wriggling       1
    wrinkled,       1
    wrinkles        2
    wrinkling       2
    ```

## <a name="next-steps"></a>Pasos siguientes

Ahora que aprendió a usar los trabajos de transmisión de MapReduce con HDInsight, use los siguientes vínculos para explorar otras formas de trabajar con HDInsight de Azure.

* [Uso de Hive con HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con HDInsight](hdinsight-use-pig.md)
* [Uso de trabajos de MapReduce con HDInsight](hdinsight-use-mapreduce.md)


