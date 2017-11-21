---
title: "Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight (Azure) | Microsoft Docs"
description: "Obtenga información sobre cómo enviar trabajos de MapReduce a HDInsight Hadoop de Azure mediante el SDK de .NET de HDInsight."
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: c85e44b0-85fd-4185-ad1c-c34a9fe5ef44
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2017
ms.author: jgao
ms.openlocfilehash: 3028edde4c2c4f3bc268ad9103e3a600379ec295
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="run-mapreduce-jobs-using-hdinsight-net-sdk"></a>Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight
[!INCLUDE [mapreduce-selector](../../../includes/hdinsight-selector-use-mapreduce.md)]

Aprenda a enviar trabajos de MapReduce mediante el SDK .NET de HDInsight. Los clústeres de HDInsight vienen con un archivo .jar con varios ejemplos de MapReduce. El archivo .jar se encuentra en */example/jars/hadoop-mapreduce-examples.jar*.  Uno de los ejemplos es *wordcount*. Desarrollará una aplicación de consola de C# para enviar un trabajo de recuento de palabras.  El trabajo lee el archivo */example/data/gutenberg/davinci.txt* y guarda el resultado en */example/data/davinciwordcount*.  Si desea volver a ejecutar la aplicación, deberá limpiar la carpeta de salida.

> [!NOTE]
> Los pasos de este artículo deben realizarse desde un cliente de Windows. Para obtener información sobre cómo usar un cliente Linux, OS X o Unix para trabajar con Hive, utilice el selector de pestañas que se muestra en la parte superior del artículo.
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar este artículo, debe tener los siguientes elementos:

* **Un clúster de Hadoop en HDInsight**. Consulte [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](apache-hadoop-linux-tutorial-get-started.md).
* **Visual Studio 2013/2015/2017**.

## <a name="submit-mapreduce-jobs-using-hdinsight-net-sdk"></a>Envío de trabajos de MapReduce mediante el SDK .NET de HDInsight
El SDK .NET de HDInsight ofrece bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight de .NET. 

**Para enviar trabajos**

1. Cree una aplicación de consola en C# mediante Visual Studio.
2. En la Consola del Administrador de paquetes NuGet, ejecute el siguiente comando:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job
3. Use el código siguiente:
   
        using System.Collections.Generic;
        using System.IO;
        using System.Text;
        using System.Threading;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string existingClusterName = "<Your HDInsight Cluster Name>";
                private const string existingClusterUri = existingClusterName + ".azurehdinsight.net";
                private const string existingClusterUsername = "<Cluster Username>";
                private const string existingClusterPassword = "<Cluster User Password>";
   
                private const string defaultStorageAccountName = "<Default Storage Account Name>"; //<StorageAccountName>.blob.core.windows.net
                private const string defaultStorageAccountKey = "<Default Storage Account Key>";
                private const string defaultStorageContainerName = "<Default Blob Container Name>";

                private const string sourceFile = "/example/data/gutenberg/davinci.txt";  
                private const string outputFolder = "/example/data/davinciwordcount";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = existingClusterUsername, Password = existingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(existingClusterUri, clusterCredentials);
   
                    SubmitMRJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitMRJob()
                {
                    List<string> args = new List<string> { { "/example/data/gutenberg/davinci.txt" }, { "/example/data/davinciwordcount" } };
   
                    var paras = new MapReduceJobSubmissionParameters
                    {
                        JarFile = @"/example/jars/hadoop-mapreduce-examples.jar",
                        JarClass = "wordcount",
                        Arguments = args
                    };
   
                    System.Console.WriteLine("Submitting the MR job to the cluster...");
                    var jobResponse = _hdiJobManagementClient.JobManagement.SubmitMapReduceJob(paras);
                    var jobId = jobResponse.JobSubmissionJsonResponse.Id;
                    System.Console.WriteLine("Response status code is " + jobResponse.StatusCode);
                    System.Console.WriteLine("JobId is " + jobId);
   
                    System.Console.WriteLine("Waiting for the job completion ...");
   
                    // Wait for job completion
                    var jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    while (!jobDetail.Status.JobComplete)
                    {
                        Thread.Sleep(1000);
                        jobDetail = _hdiJobManagementClient.JobManagement.GetJob(jobId).JobDetail;
                    }
   
                    // Get job output
                    System.Console.WriteLine("Job output is: ");
                    var storageAccess = new AzureStorageAccess(defaultStorageAccountName, defaultStorageAccountKey,
                        defaultStorageContainerName);
        
                    if (jobDetail.ExitValue == 0)
                    {
                        // Create the storage account object
                        CloudStorageAccount storageAccount = CloudStorageAccount.Parse("DefaultEndpointsProtocol=https;AccountName=" + 
                            defaultStorageAccountName + 
                            ";AccountKey=" + defaultStorageAccountKey);
        
                        // Create the blob client.
                        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();
        
                        // Retrieve reference to a previously created container.
                        CloudBlobContainer container = blobClient.GetContainerReference(defaultStorageContainerName);
        
                        CloudBlockBlob blockBlob = container.GetBlockBlobReference(outputFolder.Substring(1) + "/part-r-00000");
        
                        using (var stream = blockBlob.OpenRead())
                        {
                            using (StreamReader reader = new StreamReader(stream))
                            {
                                while (!reader.EndOfStream)
                                {
                                    System.Console.WriteLine(reader.ReadLine());
                                }
                            }
                        }
                    }
                    else
                    {
                        // fetch stderr output in case of failure
                        var output = _hdiJobManagementClient.JobManagement.GetJobErrorLogs(jobId, storageAccess); 
        
                        using (var reader = new StreamReader(output, Encoding.UTF8))
                        {
                            string value = reader.ReadToEnd();
                            System.Console.WriteLine(value);
                        }
        
                    }
                }
            }
        }
4. Presione **F5** para ejecutar la aplicación.

Para ejecutar el trabajo de nuevo, debe cambiar el nombre de la carpeta de salida del trabajo que, en el ejemplo, es "/example/data/davinciwordcount".

Cuando el trabajo se completa correctamente, la aplicación imprime el contenido del archivo de salida "part-r-00000".

## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido varias maneras de crear un clúster de HDInsight. Para obtener más información, consulte los artículos siguientes:

* Para enviar un trabajo de Hive, consulte [Ejecución de consultas de Hive mediante el SDK de .NET de HDInsight](apache-hadoop-use-hive-dotnet-sdk.md).
* Para obtener más información sobre cómo crear clústeres de HDInsight, consulte [Creación de clústeres de Hadoop basados en Linux en HDInsight](../hdinsight-hadoop-provision-linux-clusters.md).
* Para administrar clústeres de HDInsight, consulte [Administración de clústeres en HDInsight](../hdinsight-administer-use-portal-linux.md).
* Para obtener información sobre el SDK .NET de HDInsight, consulte ka [referencia del SDK de .NET de HDInsight](https://msdn.microsoft.com/library/mt271028.aspx).
* Para usar la autenticación no interactiva en Azure, consulte [Creación de aplicaciones .NET para HDInsight de autenticación no interactiva](../hdinsight-create-non-interactive-authentication-dotnet-applications.md).

