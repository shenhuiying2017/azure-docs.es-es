---
title: Uso de MapReduce y PowerShell con Hadoop (Azure HDInsight)| Microsoft Docs
description: "Obtenga información sobre cómo usar PowerShell para ejecutar trabajos de MapReduce de forma remota con Hadoop en HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 21b56d32-1785-4d44-8ae8-94467c12cfba
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 11/27/2017
ms.author: larryfr
ms.openlocfilehash: cfed3617f20074f361629c65f14ce38c1012c702
ms.sourcegitcommit: f847fcbf7f89405c1e2d327702cbd3f2399c4bc2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/28/2017
---
# <a name="run-mapreduce-jobs-with-hadoop-on-hdinsight-using-powershell"></a>Ejecución de trabajos de MapReduce con Hadoop en HDInsight con PowerShell

[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Este documento proporciona un ejemplo de uso de Azure PowerShell para ejecutar un trabajo de MapReduce en un Hadoop de un clúster de HDInsight.

## <a id="prereq"></a>Requisitos previos

* **Un clúster de HDInsight (Hadoop en HDInsight)**.

  > [!IMPORTANT]
  > Linux es el único sistema operativo que se usa en la versión 3.4 de HDInsight, o en las superiores. Consulte la información sobre la [retirada de HDInsight en Windows](../hdinsight-component-versioning.md#hdinsight-windows-retirement).

* **Una estación de trabajo con Azure PowerShell**.

## <a id="powershell"></a>Ejecución de un trabajo de MapReduce mediante Azure PowerShell

Azure PowerShell proporciona *cmdlets* que le permiten ejecutar de manera remota trabajos de MapReduce en HDInsight. Internamente, PowerShell hace llamadas de REST a un objeto [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat) (anteriormente llamado Templeton) que se ejecuta en el clúster de HDInsight.

Los siguientes cmdlets se utilizan al ejecutar trabajos de MapReduce en un clúster de HDInsight remoto.

* **Login-AzureRmAccount**: autentica Azure PowerShell en la suscripción de Azure.

* **New-AzureRmHDInsightMapReduceJobDefinition**: crea una nueva *definición de trabajo* usando la información especificada de MapReduce.

* **Start-AzureRmHDInsightJob**: envía la definición del trabajo a HDInsight e inicia el trabajo. Se devuelve un objeto *job*.

* **Wait-AzureRmHDInsightJob**: usa el objeto de trabajo para comprobar el estado del trabajo. Esperará hasta que el trabajo se complete o se supere el tiempo de espera.

* **Get-AzureRmHDInsightJobOutput**: se usa para recuperar la salida del trabajo.

Los pasos siguientes muestran cómo usar estos cmdlets para ejecutar un trabajo en el clúster de HDInsight.

1. Mediante un editor, guarde el código siguiente como **mapreducejob.ps1**.

    [!code-powershell[main](../../../powershell_scripts/hdinsight/use-mapreduce/use-mapreduce.ps1?range=5-69)]

2. Abra un nuevo símbolo del sistema de **Azure PowerShell** . Cambie los directorios a la ubicación del archivo **mapreducejob.ps1** y, a continuación, utilice el siguiente comando para ejecutar el script:

        .\mapreducejob.ps1

    Si ejecuta el script, se le pedirá el nombre del clúster de HDInsight y el inicio de sesión del clúster. Puede que también se le pida que autentique su suscripción de Azure.

3. Cuando se complete el trabajo, obtendrá un texto similar al siguiente:

        Cluster         : CLUSTERNAME
        ExitCode        : 0
        Name            : wordcount
        PercentComplete : map 100% reduce 100%
        Query           :
        State           : Completed
        StatusDirectory : f1ed2028-afe8-402f-a24b-13cc17858097
        SubmissionTime  : 12/5/2014 8:34:09 PM
        JobId           : job_1415949758166_0071

    Esto indica que el trabajo se ha completado correctamente.

    > [!NOTE]
    > Si **ExitCode** es un valor distinto de 0, consulte [Solución de problemas](#troubleshooting).

    En este ejemplo también se almacenarán los archivos descargados en el archivo **output.txt**, en el directorio desde el que se ejecuta el script.

### <a name="view-output"></a>Visualización de la salida

Para ver las palabras y los recuentos generados por el trabajo, abra el archivo **output.txt** en un editor de texto.

> [!NOTE]
> Los archivos de salida de un trabajo de MapReduce no se pueden mover. Por lo tanto, si vuelve a ejecutar esta muestra, debe cambiar el nombre del archivo de salida.

## <a id="troubleshooting"></a>Solución de problemas

Si una vez completado el trabajo no se devuelve información, consulte los errores del trabajo. Para ver información de error para este trabajo, agregue el siguiente comando al final del archivo **mapreducejob.ps1** , guárdelo y luego ejecútelo de nuevo.

```powershell
# Print the output of the WordCount job.
Write-Host "Display the standard output ..." -ForegroundColor Green
Get-AzureRmHDInsightJobOutput `
        -Clustername $clusterName `
        -JobId $wordCountJob.JobId `
        -HttpCredential $creds `
        -DisplayOutputType StandardError
```

Este cmdlet devuelve la información que se escribió en STDERR mientras se ejecutaba el trabajo.

## <a id="summary"></a>Resumen

Como puede ver, Azure PowerShell proporciona una manera fácil de ejecutar trabajos de MapReduce en un clúster de HDInsight, de supervisar el estado del trabajo y de recuperar el resultado.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener información general sobre los trabajos de MapReduce en HDInsight:

* [Uso de MapReduce en Hadoop de HDInsight](hdinsight-use-mapreduce.md)

Para obtener información sobre otras maneras de trabajar con Hadoop en HDInsight:

* [Uso de Hive con Hadoop en HDInsight](hdinsight-use-hive.md)
* [Uso de Pig con Hadoop en HDInsight](hdinsight-use-pig.md)
