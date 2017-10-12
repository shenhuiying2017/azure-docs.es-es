---
title: "Generación de recomendaciones mediante Mahout HDInsight desde PowerShell (Azure) | Microsoft Docs"
description: "Aprenda a usar la biblioteca de aprendizaje automático de Apache Mahout para generar recomendaciones de películas con HDInsight (Hadoop) desde un script de PowerShell que se ejecuta en el cliente."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 07b57208-32aa-4e59-900a-6c934fa1b7a7
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/14/2017
ms.author: larryfr
ms.openlocfilehash: 934de9ca2df48b29ef7a56d5729d59d77875ea7b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="generate-movie-recommendations-by-using-apache-mahout-with-hadoop-in-hdinsight-powershell"></a>Generación de recomendaciones de películas mediante Apache Mahout con Hadoop en HDInsight (PowerShell)

[!INCLUDE [mahout-selector](../../includes/hdinsight-selector-mahout.md)]

Aprenda a usar la biblioteca de aprendizaje automático de [Apache Mahout](http://mahout.apache.org) con HDInsight de Azure para generar recomendaciones de películas con HDInsight. El ejemplo de este documento usa Azure PowerShell para ejecutar trabajos de Mahout.

## <a name="prerequisites"></a>Requisitos previos

* Un clúster de HDInsight basado en Linux Para obtener información sobre cómo crear uno, consulte [Introducción al uso de Hadoop en HDInsight basado en Linux][getstarted].

> [!IMPORTANT]
> Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

* [Azure PowerShell](/powershell/azure/overview)

## <a name="recommendations"></a>Generación de recomendaciones mediante Azure PowerShell

> [!WARNING]
> El trabajo en esta sección funciona mediante Azure PowerShell. Muchas de las clases proporcionadas con Mahout actualmente no funcionan con Azure PowerShell. Para ver una lista de las clases que no funcionan con Azure PowerShell, consulte la sección [Solución de problemas](#troubleshooting).
>
> Para ver un ejemplo del uso de SSH para conectarse a HDInsight y ejecutar ejemplos de Mahout directamente en el clúster, consulte [Generación de recomendaciones de películas mediante Mahout y HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

Una de las funciones que proporciona Mahout es un motor de recomendaciones. Este motor acepta datos en formato de `userID`, `itemId` y `prefValue` (la preferencia de los usuarios por el elemento). Mahout usa los datos para determinar los usuarios con preferencias de elementos similares, lo que se puede usar para realizar recomendaciones.

El ejemplo siguiente es un tutorial simplificado de cómo funciona el proceso de recomendación:

* **Ocurrencia conjunta**: a José, Alicia y Roberto les gusta *La Guerra de las galaxias*, *El imperio contraataca* y *El retorno del Jedi*. Mahout determina que a los usuarios que les gusta alguna de estas películas también les gustan las otras dos.

* **Ocurrencia conjunta**: a Roberto y Alicia también les gusta *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*. Mahout determina que a los usuarios que les gustan las tres películas anteriores también les gustan estas tres otras.

* **Recomendación basada en similitud**: como a José le gustan las tres primeras películas, Mahout examina películas que a otros usuarios con preferencias similares les han gustado, pero que José no ha visto (gustado/valorado). En este caso, Mahout recomendaría *La amenaza fantasma*, *El ataque de los clones* y *La venganza de los Sith*.

### <a name="understanding-the-data"></a>Descripción de los datos

[GroupLens Research][movielens] proporciona calificaciones de películas en un formato compatible con Mahout. Estos datos están disponibles en el almacenamiento predeterminado del clúster en `/HdiSamples//HdiSamples/MahoutMovieData`.

Existen dos archivos, `moviedb.txt` (información sobre las películas) y `user-ratings.txt`. El archivo `user-ratings.txt` se utiliza durante el análisis. El archivo `moviedb.txt` se usa para proporcionar texto descriptivo que muestre los resultados del análisis.

Los datos del archivo user-ratings.txt tienen una estructura de `userID`, `movieID`, `userRating` y `timestamp`, que nos indica qué valoración le dio cada usuario a una película. A continuación se muestra un ejemplo de los datos:

    196    242    3    881250949
    186    302    3    891717742
    22    377    1    878887116
    244    51    2    880606923
    166    346    1    886397596

### <a name="run-the-job"></a>Ejecución del trabajo

Use el siguiente script de Windows PowerShell para ejecutar un trabajo que use el motor de recomendaciones de Mahout con los datos de las películas:

> [!NOTE]
> Este archivo le pide información que se usa para conectarse a su clúster de HDInsight y ejecutar trabajos. Los trabajos pueden tardar varios minutos en finalizar y descargar el archivo output.txt.

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=5-98)]

> [!NOTE]
> Los trabajos de Mahout no eliminan los datos temporales creados durante el procesamiento del trabajo. El parámetro `--tempDir` se especifica en el trabajo de ejemplo para aislar los archivos temporales en un directorio específico.

El trabajo de Mahout no devuelve la salida a STDOUT. En su lugar, lo almacena en el directorio de salida especificado como **part-r-00000**. El script descarga este archivo en **output.txt** en el directorio actual de la estación de trabajo.

El texto siguiente es un ejemplo del contenido de este archivo:

    1    [234:5.0,347:5.0,237:5.0,47:5.0,282:5.0,275:5.0,88:5.0,515:5.0,514:5.0,121:5.0]
    2    [282:5.0,210:5.0,237:5.0,234:5.0,347:5.0,121:5.0,258:5.0,515:5.0,462:5.0,79:5.0]
    3    [284:5.0,285:4.828125,508:4.7543354,845:4.75,319:4.705128,124:4.7045455,150:4.6938777,311:4.6769233,248:4.65625,272:4.649266]
    4    [690:5.0,12:5.0,234:5.0,275:5.0,121:5.0,255:5.0,237:5.0,895:5.0,282:5.0,117:5.0]

La primera columna es `userID`. Los valores contenidos en '[' y ']' son `movieId`:`recommendationScore`.

El script también descarga los archivos `moviedb.txt` y `user-ratings.txt`, que son necesarios para formatear la salida a fin de que sea más legible.

### <a name="view-the-output"></a>Visualización de la salida

Aunque puede que el resultado generado esté bien para usarse en una aplicación, no es muy descriptivo. El archivo `moviedb.txt` del servidor se puede usar para resolver el objeto `movieId` en el nombre de una película. Use el siguiente script de PowerShell para mostrar recomendaciones con nombres de película:

[!code-powershell[main](../../powershell_scripts/hdinsight/mahout/use-mahout.ps1?range=106-180)]

Use el comando siguiente para mostrar las recomendaciones en un formato descriptivo: 

```powershell
.\show-recommendation.ps1 -userId 4 -userDataFile .\user-ratings.txt -movieFile .\moviedb.txt -recommendationFile .\output.txt
```

La salida será similar al siguiente texto:

    Reading movies descriptions
    Reading rated movies
    Reading recommendations
    Rated movies
    ---------------------------
    Movie                                    Rating
    -----                                    ------
    Devil's Own, The (1997)                  1
    Alien: Resurrection (1997)               3
    187 (1997)                               2
    (lines ommitted)

    ---------------------------
    Recommended movies
    ---------------------------

    Movie                                    Score
    -----                                    -----
    Good Will Hunting (1997)                 4.6504064
    Swingers (1996)                          4.6862745
    Wings of the Dove, The (1997)            4.6666665
    People vs. Larry Flynt, The (1996)       4.834559
    Everyone Says I Love You (1996)          4.707071
    Secrets & Lies (1996)                    4.818182
    That Thing You Do! (1996)                4.75
    Grosse Pointe Blank (1997)               4.8235292
    Donnie Brasco (1997)                     4.6792455
    Lone Star (1996)                         4.7099237

## <a name="troubleshooting"></a>Solución de problemas

### <a name="cannot-overwrite-files"></a>No se pueden sobrescribir los archivos

Los trabajos de Mahout no limpian los archivos temporales creados durante el procesamiento. Además, los trabajos no sobrescriben el archivo de salida existente.

Para evitar errores al ejecutar trabajos de Mahout, elimine los archivos temporales y de salida entre una ejecución y otra. Para quitar los archivos creados por los scripts anteriores en este documento, use el siguiente script de PowerShell:

```powershell
# Login to your Azure subscription
# Is there an active Azure subscription?
$sub = Get-AzureRmSubscription -ErrorAction SilentlyContinue
if(-not($sub))
{
    Add-AzureRmAccount
}

# Get cluster info
$clusterName = Read-Host -Prompt "Enter the HDInsight cluster name"
$creds=Get-Credential -Message "Enter the login for the cluster"

#Get the cluster info so we can get the resource group, storage, etc.
$clusterInfo = Get-AzureRmHDInsightCluster -ClusterName $clusterName
$resourceGroup = $clusterInfo.ResourceGroup
$storageAccountName = $clusterInfo.DefaultStorageAccount.split('.')[0]
$container = $clusterInfo.DefaultStorageContainer
$storageAccountKey = (Get-AzureRmStorageAccountKey `
    -Name $storageAccountName `
-ResourceGroupName $resourceGroup)[0].Value

#Create a storage context and upload the file
$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey $storageAccountKey

#Azure PowerShell can't delete blobs using wildcard,
#so have to get a list and delete one at a time
# Start with the output
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/out"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
# Next the temp files
$blobs = Get-AzureStorageBlob -Container $container -Context $context -Prefix "example/temp"
foreach($blob in $blobs)
{
    Remove-AzureStorageBlob -Blob $blob.Name -Container $container -context $context
}
```

### <a name="nopowershell"></a>Clases que no funcionan con Azure PowerShell

Los trabajos de Mahout que usan las siguientes clases devuelven diversos mensajes de error cuando se usan desde Windows PowerShell:

* org.apache.mahout.utils.clustering.ClusterDumper
* org.apache.mahout.utils.SequenceFileDumper
* org.apache.mahout.utils.vectors.lucene.Driver
* org.apache.mahout.utils.vectors.arff.Driver
* org.apache.mahout.text.WikipediaToSequenceFile
* org.apache.mahout.clustering.streaming.tools.ResplitSequenceFiles
* org.apache.mahout.clustering.streaming.tools.ClusterQualitySummarizer
* org.apache.mahout.classifier.sgd.TrainLogistic
* org.apache.mahout.classifier.sgd.RunLogistic
* org.apache.mahout.classifier.sgd.TrainAdaptiveLogistic
* org.apache.mahout.classifier.sgd.ValidateAdaptiveLogistic
* org.apache.mahout.classifier.sgd.RunAdaptiveLogistic
* org.apache.mahout.classifier.sequencelearning.hmm.BaumWelchTrainer
* org.apache.mahout.classifier.sequencelearning.hmm.ViterbiEvaluator
* org.apache.mahout.classifier.sequencelearning.hmm.RandomSequenceGenerator
* org.apache.mahout.classifier.df.tools.Describe

Para ejecutar trabajos que usan estas clases, conéctese al clúster de HDInsight mediante SSH y ejecute los trabajos desde la línea de comandos. Para ver un ejemplo del uso de SSH para ejecutar trabajos de Mahout, consulte [Generación de recomendaciones de películas mediante Mahout y HDInsight (SSH)](hdinsight-hadoop-mahout-linux-mac.md).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a usar a Mahout, descubra otras formas de trabajar con datos en HDInsight:

* [Hive con HDInsight](hdinsight-use-hive.md)
* [Pig con HDInsight](hdinsight-use-pig.md)
* [MapReduce con HDInsight](hdinsight-use-mapreduce.md)

[build]: http://mahout.apache.org/developers/buildingmahout.html
[aps]: /powershell/azureps-cmdlets-docs
[movielens]: http://grouplens.org/datasets/movielens/
[100k]: http://files.grouplens.org/datasets/movielens/ml-100k.zip
[getstarted]: hdinsight-hadoop-linux-tutorial-get-started.md
[upload]: hdinsight-upload-data.md
[ml]: http://en.wikipedia.org/wiki/Machine_learning
[forest]: http://en.wikipedia.org/wiki/Random_forest
[enableremote]: ./media/hdinsight-mahout/enableremote.png
[connect]: ./media/hdinsight-mahout/connect.png
[hadoopcli]: ./media/hdinsight-mahout/hadoopcli.png
[tools]: https://github.com/Blackmist/hdinsight-tools
