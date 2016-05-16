<properties
	pageTitle="Uso de Sqoop de Hadoop en HDInsight | Microsoft Azure"
	description="Obtenga información sobre cómo utilizar el SDK de .NET de HDInsight para ejecutar trabajos de Sqoop con el fin de llevar a cabo tareas de importación y exportación entre un clúster de Hadoop y una base de datos SQL de Azure."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/06/2016"
	ms.author="jgao"/>

#Ejecución de trabajos de Sqoop con el SDK de .NET para Hadoop en HDInsight

[AZURE.INCLUDE [sqoop-selector](../../includes/hdinsight-selector-use-sqoop.md)]

Obtenga información sobre cómo utilizar el SDK de .NET de HDInsight en HDInsight para ejecutar trabajos de Sqoop con el fin de llevar a cabo tareas de importación y exportación entre un clúster de HDInsight y una base de datos SQL de Azure o una de SQL Server.

> [AZURE.NOTE] Los pasos descritos en este artículo pueden utilizarse con cualquier un clúster de HDInsight basado en Windows o Linux ; sin embargo, estos pasos sólo funcionarán desde un cliente Windows. Utilice el selector de pestañas de la parte superior de este artículo para elegir otros métodos.

###Requisitos previos

Antes de empezar este tutorial, debe contar con lo siguiente:

- **Un clúster de Hadoop en HDInsight**. Consulte el artículo [Creación del clúster y la base de datos SQL](hdinsight-use-sqoop.md#create-cluster-and-sql-database).

## Ejecutar Sqoop mediante SDK de .NET

El SDK .NET de HDInsight ofrece bibliotecas de cliente .NET que facilitan el trabajo con los clústeres de HDInsight de .NET. En esta sección, creará una aplicación de consola de C# para exportar hivesampletable a la tabla de Base de datos SQL que creó anteriormente en este tutorial.


**Para enviar un trabajo de Sqoop**

1. Cree una aplicación de consola en C# mediante Visual Studio.
2. En la Consola del administrador de paquetes de Visual Studio, ejecute el siguiente comando de Nuget para importar el paquete.

		Install-Package Microsoft.Azure.Common.Authentication -Pre
        Install-Package Microsoft.Azure.Management.HDInsight -Pre
        Install-Package Microsoft.Azure.Management.HDInsight.Job -Pre
        
3. Use el siguiente código en el archivo Program.cs:

        using System;
        using System.Collections.Generic;
        using System.Security;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Common.Authentication;
        using Microsoft.Azure.Common.Authentication.Factories;
        using Microsoft.Azure.Common.Authentication.Models;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.HDInsight;
        using Microsoft.Azure.Management.HDInsight.Job;
        using Microsoft.Azure.Management.HDInsight.Job.Models;
        using Hyak.Common;

        namespace SubmitHDInsightJobDotNet
        {
            class Program
            {
                private static HDInsightManagementClient _hdiManagementClient;
                private static HDInsightJobManagementClient _hdiJobManagementClient;

                private static Guid SubscriptionId = new Guid("<Your Subscription ID>");
                private const string ResourceGroupName = "<Your Resource Group Name>";

                private const string ExistingClusterName = "<Your HDInsight Cluster Name>";
                private const string ExistingClusterUri = ExistingClusterName + ".azurehdinsight.net";
                private const string ExistingClusterUsername = "<Cluster Username>";
                private const string ExistingClusterPassword = "<Cluster User Password>";

                private const string DefaultStorageAccountName = "<Default Storage Account Name>";
                private const string DefaultStorageAccountKey = "<Default Storage Account Key>";
                private const string DefaultStorageContainerName = "<Default Blob Container Name>";

                static void Main(string[] args)
                {
                    System.Console.WriteLine("The application is running ...");

                    var tokenCreds = GetTokenCloudCredentials();
                    var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

                    var resourceManagementClient = new ResourceManagementClient(subCloudCredentials);
                    var rpResult = resourceManagementClient.Providers.Register("Microsoft.HDInsight");

                    _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

                    var clusterCredentials = new BasicAuthenticationCloudCredentials { Username = ExistingClusterUsername, Password = ExistingClusterPassword };
                    _hdiJobManagementClient = new HDInsightJobManagementClient(ExistingClusterUri, clusterCredentials);

                    SubmitSqoopJob();

                    System.Console.WriteLine("Press ENTER to continue ...");
                    System.Console.ReadLine();
                }

                public static TokenCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
                {
                    var authFactory = new AuthenticationFactory();

                    var account = new AzureAccount { Type = AzureAccount.AccountType.User };

                    if (username != null && password != null)
                        account.Id = username;

                    var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

                    var accessToken =
                        authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                            .AccessToken;

                    return new TokenCloudCredentials(accessToken);
                }

                public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(TokenCloudCredentials creds, Guid subId)
                {
                    return new TokenCloudCredentials(subId.ToString(), creds.Token);
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
                        Files = new List<string>{"/user/oozie/share/lib/sqoop/sqljdbc41.jar"}, // This line is required for Linux-based cluster.
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
        
4. Presione **F5** para ejecutar el programa.

##Pasos siguientes

Ahora ya ha aprendido a usar Sqoop. Para obtener más información, consulte:

- [Uso de Oozie con HDInsight](hdinsight-use-oozie.md): use la acción Sqoop en un flujo de trabajo de Oozie.
- [Análisis de la información de retraso de vuelos con HDInsight](hdinsight-analyze-flight-delay-data.md): use Hive para analizar la información de retraso de los vuelos y luego use Sqoop para exportar los datos a una base de datos SQL de Azure.
- [Carga de datos en HDInsight](hdinsight-upload-data.md): busque otros métodos para cargar datos en HDInsight o el almacenamiento de blobs de Azure.

<!---HONumber=AcomDC_0504_2016-->