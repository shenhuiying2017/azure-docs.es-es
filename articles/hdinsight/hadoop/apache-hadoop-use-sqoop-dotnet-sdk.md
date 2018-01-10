---
title: "Ejecución de trabajos de Sqoop con .NET y HDInsight (Azure) | Microsoft Docs"
description: "Aprenda a usar el SDK de .NET para HDInsight para ejecutar tareas de importación y exportación de Sqoop entre un clúster de Hadoop y una base de datos SQL Azure."
keywords: trabajo de Sqoop
editor: cgronlun
manager: jhubbard
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
ms.assetid: 87bacd13-7775-4b71-91da-161cb6224a96
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 41a1c7c0b7c0ad00746508bbf341eff448690725
ms.sourcegitcommit: 901a3ad293669093e3964ed3e717227946f0af96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="run-sqoop-jobs-by-using-net-sdk-for-hadoop-in-hdinsight"></a>Ejecución de trabajos de Sqoop con el SDK de .NET para Hadoop en HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Aprenda a usar el SDK de .NET para Azure HDInsight para ejecutar trabajos de Sqoop con el fin de llevar a cabo tareas de importación y exportación entre un clúster de HDInsight y una base de datos SQL Azure o SQL Server.

> [!NOTE]
> Aunque puede usar los procedimientos descritos en este artículo con cualquier clúster de HDInsight basado en Windows o Linux, solo funcionan desde un cliente de Windows. Para elegir otros métodos, use el selector de fichas de la parte superior de este artículo.
> 

## <a name="prerequisites"></a>requisitos previos
Antes de empezar este tutorial, debe contar con el elemento siguiente:

* Un clúster de Hadoop en HDInsight. Para más información, consulte [Creación del clúster y la base de datos SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## <a name="use-sqoop-on-hdinsight-clusters-with-the-net-sdk"></a>Uso de Sqoop en clústeres de HDInsight con el SDK de .NET
El SDK .NET para HDInsight proporciona bibliotecas de cliente .NET que facilitan el uso de los clústeres de HDInsight en .NET. En esta sección se crea una aplicación de consola de C# para exportar hivesampletable a la tabla de SQL Database creada anteriormente en este tutorial.

## <a name="submit-a-sqoop-job"></a>Enviar un trabajo de Sqoop

1. Cree una aplicación de consola en C# mediante Visual Studio.

2. En la Consola del Administrador de paquetes de Visual Studio, ejecute el siguiente comando de NuGet para importar el paquete:
   
        Install-Package Microsoft.Azure.Management.HDInsight.Job

3. Use el siguiente código en el archivo Program.cs:
   
        using System.Collections.Generic;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;
   
        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightJobManagementClient _hdiJobManagementClient;
   
                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";
   
                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");
   
                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);
   
                    SubmitSqoopJob();
   
                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }
   
                private static void SubmitSqoopJob()
                {
                    var sqlDatabaseServerName = "<SQLDatabaseServerName>";
                    var sqlDatabaseLogin = "<SQLDatabaseLogin>";
                    var sqlDatabaseLoginPassword = "<SQLDatabaseLoginPassword>";
                    var sqlDatabaseDatabaseName = "<DatabaseName>";
   
                    var tableName = "<TableName>";
                    var exportDir = "/tutorials/usesqoop/data";
   
                    // Connection string for using Azure SQL Database.
                    // Comment if using SQL Server
                    var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ".database.windows.net;user=" + sqlDatabaseLogin + "@" + sqlDatabaseServerName + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
                    // Connection string for using SQL Server.
                    // Uncomment if using SQL Server
                    //var connectionString = "jdbc:sqlserver://" + sqlDatabaseServerName + ";user=" + sqlDatabaseLogin + ";password=" + sqlDatabaseLoginPassword + ";database=" + sqlDatabaseDatabaseName;
   
                    var parameters = new SqoopJobSubmissionParameters
                    {
                        Files = new List<string> { "/user/oozie/share/lib/sqoop/sqljdbc41.jar" }, // This line is required for Linux-based cluster.
                        Command = "export --connect " + connectionString + " --table " + tableName + "_mobile --export-dir " + exportDir + "_mobile --fields-terminated-by \\t -m 1"
                    };
   
                    System.Console.WriteLine("Submitting the Sqoop job to the cluster...");
                    var response = _hdiJobManagementClient.JobManagement.SubmitSqoopJob(parameters);
                    System.Console.WriteLine("Validating that the response is as expected...");
                    System.Console.WriteLine("Response status code is " + response.StatusCode);
                    System.Console.WriteLine("Validating the response object...");
                    System.Console.WriteLine("JobId is " + response.JobSubmissionJsonResponse.Id);
                }
            }
        }

4. Para ejecutar el programa, seleccione la tecla **F5**. 

## <a name="limitations"></a>Limitaciones
HDInsight basado en Linux presenta las siguientes limitaciones:

* Exportación masiva: el conector de Sqoop que se usa para exportar datos a Microsoft SQL Server o Azure SQL Database no admite actualmente inserciones en masa.

* Procesamiento por lotes: con el modificador `-batch` para realizar inserciones, Sqoop realiza inserciones múltiples en lugar de procesar por lotes las operaciones de inserción.

## <a name="next-steps"></a>pasos siguientes
Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

* [Uso de Oozie con HDInsight](../hdinsight-use-oozie.md): use la acción Sqoop en un flujo de trabajo de Oozie.
* [Análisis de la información de retraso de vuelos con HDInsight](../hdinsight-analyze-flight-delay-data.md): use Hive para analizar la información de retraso de los vuelos y luego use Sqoop para exportar los datos a una base de datos SQL de Azure.
* [Carga de datos en HDInsight](../hdinsight-upload-data.md): encuentre otros métodos de cargar datos en HDInsight o Azure Blob Storage.

