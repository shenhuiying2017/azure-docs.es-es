<properties
   pageTitle="Escalado de clúster en HDInsight | Azure"
   description="Cambie la cantidad de nodos de datos de un clúster que se ejecuta en HDInsight sin la necesidad de eliminar y volver a crear el clúster."
   services="hdinsight"
   documentationCenter=""
   authors="bradsev"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="bradsev"/>

# Escalado de clúster en HDInsight

La característica de escalado de clúster le permite cambiar la cantidad de nodos de datos que usa un clúster en ejecución en HDInsight sin la necesidad de eliminar y volver a crear el clúster. La operación se puede realizar con PowerShell, el SDK de HDInsight o desde el portal de Azure.

> [WACOM.NOTE] La versión actual solo es compatible con los tipos de clúster de Hadoop y de Storm. Próximamente se agregará la compatibilidad con los clústeres de HBase. 

## Detalles de la característica
Esta sección describe el impacto que tiene cambiar la cantidad de nodos de datos para cada tipo de clúster compatible con HDInsight:

* Hadoop
* Storm
* HBase 

## Hadoop 

### Incorporación de nodos de datos
Puede aumentar sin problemas la cantidad de nodos de datos en un clúster de Hadoop que se encuentra en ejecución sin afectar ningún trabajo pendiente o en ejecución. También se pueden enviar trabajos nuevos mientras la operación está en curso. Los errores que puedan surgir en una operación de escalado se enfrentan oportunamente, por lo que el clúster siempre queda en estado funcional.

### Eliminación de nodos de datos
Cuando se realiza la reducción vertical de un clúster de Hadoop al disminuir la cantidad de nodos de datos, se reinician algunos de los servicios del clúster. Esto provoca que todos los trabajos pendientes y en ejecución fallen al completarse la operación de escalado. Sin embargo, puede volver a enviar los trabajos una vez finalizada la operación.

## Storm
Puede agregar o quitar sin problemas nodos de datos de su clúster de Storm mientras se encuentra en ejecución. Sin embargo, después de la finalización correcta de la operación de escalado, deberá volver a equilibrar la topología.

Esto se puede realizar de dos formas a través de:

* La interfaz de usuario web de Storm
* Una herramienta CLI 

Consulte la [documentación de Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html) para obtener más detalles.

La interfaz de usuario web de Storm se encuentra disponible en el clúster de HDInsight:

![image1](./media/hdinsight-hadoop-cluster-scaling/StormUI.png)

El siguiente es un ejemplo de cómo usar el comando CLI para volver a equilibrar la topología de Storm:

	## Reconfigure the topology "mytopology" to use 5 worker processes,
	## the spout "blue-spout" to use 3 executors and
	## the bolt "yellow-bolt" to use 10 executors.

	$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

## HBase
En este momento, la operación de escalado de clúster no es compatible con los clústeres de tipo HBase.

## Requisitos previos:

* Solo son compatibles los clústeres con una versión 3.1.3 de HDInsight, o superior. Si no está seguro de la versión del clúster, puede comprobarla en el portal de Azure; para ello, haga clic en el nombre del clúster de HDInsight o ejecute el comando `Get-AzureHDInsightCluster -name <clustername>` desde Azure PowerShell.

* Se requiere Azure PowerShell versión 0.8.14 o superior para realizar la operación desde PowerShell. Puede descargar la versión más reciente de PowerShell desde la sección de herramientas de la línea de comandos en el sitio web [Descargas de Azure Downloads](http://azure.microsoft.com/ downloads/). Puede comprobar la versión de Azure PowerShell que tiene instalada con el siguiente comando desde una ventana de PowerShell: `(get-module Azure).Version`

## Uso del escalador de clúster

### Portal de Azure
Es posible cambiar el tamaño de un clúster en ejecución desde la pestaña **Escalar** del panel del clúster de HDInsight de Azure.

![](http://i.imgur.com/u5Mewwx.png)

### PowerShell
Para cambiar el tamaño del clúster de Hadoop con PowerShell, ejecute el siguiente comando desde un equipo cliente:

	Set-AzureHDInsightClusterSize -ClusterSizeInNodes <NewSize> -name <clustername>	

> [WACOM.NOTE] Para poder usar este comando, el equipo cliente debe tener instalado Azure PowerShell versión 0.8.14 o posterior.

### SDK
Para cambiar el tamaño del clúster de Hadoop con el SDK de HDInsight, use uno de los siguientes dos métodos: 

	ChangeClusterSize(string dnsName, string location, int newSize) 

o 

	ChangeClusterSizeAsync(string dnsName, string location, int newSize) 


Tanto la versión sincrónica como la versión asincrónica de este método devuelven el objeto [ClusterDetails](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterdetails_properties.aspx).

El siguiente es un código de ejemplo que muestra cómo usar la versión asincrónica de este método:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Text;
	using System.Threading.Tasks;
	using System.Security.Cryptography.X509Certificates;
	using Microsoft.WindowsAzure.Management.HDInsight;
	using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;

	namespace HDInsightClusterScaling
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            // Friendly name for the certificate your created earlier  
	            string certfriendlyname = "<CertificateFriendlyName>";     
	            string subscriptionid = "<SubscriptionID>";
	            string clustername = "<ClusterDNSName>";
	     		string location = "<ClusterLocation>"";
				int newSize = <NewClusterSize>;
	
	            // Get the certificate object from certificate store using the friendly name to identify it
	            X509Store store = new X509Store();
	            store.Open(OpenFlags.ReadOnly);
	            X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.FriendlyName == certfriendlyname);
	
	            // Create an HDInsightClient object
	            HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionid), cert);
	            var client = HDInsightClient.Connect(creds);
	
	            Console.WriteLine("Rescaling HDInsight cluster ...");
	
	            // Change cluster size
	     		ClusterDetails cluster = client.ChangeClusterSize(clustername, location, newSize);
	            
	            Console.WriteLine("Cluster Rescaled: {0} \n New Cluster Size = {1}", cluster.ConnectionUrl, cluster.ClusterSizeInNodes);
	            Console.WriteLine("Press ENTER to continue.");
	            Console.ReadKey();
	        }
	    }
	}


Consulte el tema [Aprovisionamiento de clústeres de Hadoop en HDInsight con opciones personalizadas](http://azure.microsoft.com/ documentation/articles/hdinsight-provision-clusters/) para obtener más información sobre el uso del SDK .NET de HDInsight.
<!--HONumber=45--> 
