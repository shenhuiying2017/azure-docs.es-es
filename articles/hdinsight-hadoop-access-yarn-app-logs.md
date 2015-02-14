<properties 
	pageTitle="Acceder a los registros de aplicación HDInsight mediante programación| Azure" 
	description="Acceder a los registros de aplicación HDInsight mediante programación" 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/21/2014" 
	ms.author="bradsev"/>

# Acceder a los registros de aplicación HDInsight mediante programación

En este tema se explica cómo enumerar las aplicaciones de YARN mediante programación que se han completado en un clúster de Hadoop en HDInsight y cómo acceder mediante programación a los registros de aplicación sin necesidad de tener RDP en los clústeres. De manera específica, se ha agregado un nuevo componente y una nueva API:

  1. Se ha habilitado el servidor de historial de aplicación genérico en clústeres de HDInsight. Es un componente dentro del servidor de escala de tiempo de YARN que administra el almacenamiento y la recuperación de información genérica acerca de las aplicaciones completadas.
  2. Las nuevas API del SDK de .Net de HDInsight .de Azure se han agregado para habilitarle para enumerar aplicaciones mediante programación que se han ejecutado en los clústeres y para descargar la aplicación o los registros específicos de contenedor relevantes (en texto sin formato) para ayudar con la depuración de cualquier problema de aplicación que se produzca.


## Requisitos previos

El SDK de HDInsight de Azure se requiere para usar el código presentado en este tema en una aplicación .NET. La compilación más reciente publicada del SDK está disponible en [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started). 

Para instalar el SDK de HDInsight desde una aplicación de Visual Studio, vaya al menú **Herramientas**, haga clic en el **Administrador de paquetes de Nuget** y, a continuación, haga clic en **Consola del Administrador de paquetes**. Ejecute los siguientes comandos en la consola para instalar los paquetes.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

Este comando agrega bibliotecas de .NET para HDInsight y hace referencia a ellas en el proyecto actual de Visual Studio.

## En este artículo

- [Servidor de escala de tiempo de YARN](#YARNTimelineServer)
- [Registros y aplicaciones de YARN](#YARNAppsAndLogs)
- [Enumeración de aplicaciones y descarga de registros mediante programación](#enumerate-and-download)


## <a name="YARNTimelineServer"></a>Servidor de escala de tiempo de YARN

Muestra <a href="http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html" target="_blank">Servidor de escala de tiempo de YARN</a> proporciona información genérica acerca de las aplicaciones completadas, así como información de aplicación específicas de marco a través de dos interfaces diferentes. Concretamente:

* Se ha habilitado el almacenamiento y la recuperación de información de aplicación en clústeres de HDInsight con las versiones 3.1.1.374 o superiores. 
* El componente de información de aplicaciones específica del marco del servidor de la escala de tiempo no está disponible actualmente en los clústeres de HDInsight.


La información genérica sobre aplicaciones incluye los siguientes tipos de datos: 

* **ApplicationId** (identificador único de una aplicación), 
* el **usuario** que ha iniciado la aplicación, 
* la información acerca de los **intentos** realizados para completar la aplicación y 
* los **contenedores** usados por cualquier intento de aplicación proporcionado. 

En los clústeres de HDInsight, esta información se almacenará en el Administrador de recursos para un almacén de historial en el contenedor predeterminado de la cuenta de almacenamiento predeterminada. Estos datos genéricos sobre aplicaciones completadas se pueden recuperar a través de una API de REST:

    GET on https://<cluster-dns-name>.azurehdinsight.net/ws/v1/applicationhistory/apps

Hemos agregado las nuevas API al SDK de .Net de HDInsight para facilitar la recuperación de estos datos mediante programación. Tenga en cuenta que los datos genéricos también se pueden recuperar mediante la ejecución de comandos CLI de YARN directamente en sus nodos de clúster (después de conectarse al clúster mediante RDP).

## <a name="YARNAppsAndLogs"></a>Registros y aplicaciones de YARN

YARN (Yet Another Resource Negotiator, otro negociador de recursos) admite varios modelos de programación (MapReduce es uno de ellos) al desacoplar la administración de recursos de la programación y supervisión de aplicaciones. Esto se realiza a través de un  *Resource Manager* (RM) global, por nodo de trabajo *Node Managers* (NMs) y por aplicación *Application Masters* (AMs). El AM por aplicación negocia recursos (CPU, memoria, disco, red) para ejecutar la aplicación con el RM. El RM funciona con NM para conceder estos recursos, que se conceden como  *Containers*. El AM es responsable del seguimiento del progreso de los contenedores asignados a él por el RM. Una aplicación puede requerir muchos contenedores según la naturaleza de la aplicación. 

Además, cada aplicación puede constar de varios  *Application Attempts* para completar la aplicación en el caso de bloqueos o debido a la pérdida de comunicación entre una AM y un RM. Por lo tanto, los contenedores se conceden a un intento específico de una aplicación. En cierto sentido, un contenedor ofrece el contexto para la unidad básica de trabajo realizado por una aplicación YARN y todo el trabajo que se realiza en el contexto de un contenedor se lleva a cabo en el nodo de trabajo en el que se ha asignado el contenedor. Consulte [Conceptos de YARN][YARN-concepts] para más referencia.

Los registros de aplicación (y los registros de contenedor asociados) son fundamentales en la depuración de las aplicaciones de Hadoop problemáticas. YARN proporciona un buen marco para recopilar, agregar y almacenar registros de aplicaciones con la característica [Agregación de registro][log-aggregation]. La característica Agregación de registro hace que el acceso a los registros de aplicaciones sea más determinante ya que agrega los registros en todos los contenedores en un nodo de trabajo y los almacena como un archivo de registro agregado por nodo de trabajo en el sistema de archivos predeterminado después de que se complete una aplicación. Su aplicación puede utilizar cientos o miles de contenedores, pero los registros para todos los contenedores que se ejecutan en un nodo de trabajo único se agregarán siempre en un archivo único, lo que da como resultado un archivo de registro por nodo de trabajo usado por la aplicación. La agregación de registro está habilitada de forma predeterminada en los clústeres de HDInsight (versión 3.0 y posteriores) y se pueden encontrar registros agregados en el contenedor predeterminado del clúster en la siguiente ubicación:

	wasb:///app-logs/<user>/logs/<applicationId>

donde "*usuario*" es el nombre del usuario que inició la aplicación y "*applicationId*" es el identificador único de una aplicación según la asigna el Administrador de recursos de YARN.

Los registros agregados no son legibles directamente tal como se escriben en un [TFile][T-file], [formato binario][binary-format] indizado por el contenedor. YARN ofrece herramientas CLI para volcar estos registros como texto sin formato para aplicaciones o contenedores de interés. Puede ver estos registros como texto sin formato al ejecutar uno de los siguiente comandos de YARN directamente en los nodos del clúster (después de conectarse a él a través de RDP):

	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
	yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address> 

En la siguiente sección se habla acerca de cómo se puede acceder a los registros específicos de contenedor o de aplicación mediante programación, sin tener que aplicar el RDP para sus clústeres de HDInsight.

## <a name="enumerate-and-download"></a>Enumeración de aplicaciones y descarga de registros mediante programación

Para utilizar los siguientes ejemplos de código, debe satisfacer los requisitos previos descritos anteriormente descargando la versión más recientes del SDK de .Net de HDInsight. Vea las instrucciones proporcionadas allí.

En el código siguiente se muestra cómo usar las nuevas API para enumerar las aplicaciones y descargar los registros de aplicaciones completadas.

> [AZURE.NOTE] Las siguientes API siguientes solo funcionarán con clústeres de Hadoop "En ejecución" con la versión 3.1.1.374 o superior. Agregue las siguientes directivas.

	using Microsoft.Hadoop.Client;
	using Microsoft.WindowsAzure.Management.HDInsight;

Hacen referencia a las API recientemente definidas en el código siguiente. En el siguiente fragmento de código crea un cliente de historial de aplicaciones en un clúster "En ejecución" en su suscripción.

	string subscriptionId = "<your-subscription-id>";
	string clusterName = "<your-cluster-name>";
	string certName = "<your-subscription-management-cert-name>";
	
	// Create an HDInsight Client
	X509Store store = new X509Store(StoreName.My, StoreLocation.LocalMachine);
	store.Open(OpenFlags.ReadOnly);
	X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>()
	                            .Single(x => x.FriendlyName == certName);
	
	HDInsightCertificateCredential creds = 
				new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
	
	IHDInsightClient client = HDInsightClient.Connect(creds);
	
	// Get the cluster on which your applications were run
	// The cluster needs to be in "Running" state
	ClusterDetails cluster = client.GetCluster(clusterName);
	
	// Create an Application History client against your cluster
	IHDInsightApplicationHistoryClient appHistoryClient = 
				cluster.CreateHDInsightApplicationHistoryClient(TimeSpan.FromMinutes(5));


 Ahora puede usar el cliente de historial de aplicaciones para las aplicaciones completadas, filtrar las aplicaciones según los criterios y descargar los registros de aplicación relevantes. El siguiente fragmento de código muestra la manera en que se realiza mediante programación.

	// Local download folder location where the logs will be placed
	string downloadLocation = "E:\\YarnApplicationLogs";
	
	// List completed applications on your cluster that were submitted in the last 24 hours but failed
	// Search for applications based on application name
	string appNamePrefix = "your-app-name-prefix";
	DateTime endTime = DateTime.UtcNow;
	DateTime startTime = endTime.AddHours(-24);
	IEnumerable<ApplicationDetails> applications = appHistoryClient
	                .ListCompletedApplications(startTime, endTime)
	                .Where(app => 
	                    app.GetApplicationFinalStatusAsEnum() == ApplicationFinalStatus.Failed 
	                    && app.Name.StartsWith(appNamePrefix));
	
	// Download logs for failed or killed applications
	// This will generate one log file for each application
	foreach (ApplicationDetails application in applications)
	{
	    appHistoryClient.DownloadApplicationLogs(application, downloadLocation);
	}

El código anterior muestra o encuentra aplicaciones de interés mediante el cliente de historial de aplicaciones y, a continuación, descarga los registros para esas aplicaciones en una carpeta local. 

Como alternativa, el fragmento de código siguiente descarga registros para una aplicación cuyo "ApplicationId" (su identificador único) es conocido. El ApplicationId es un identificador único global de una aplicación como se le asignó por el Administrador de recursos. Se construye usando la hora de inicio del Administrador de recursos junto con un contador de progreso monotono para las aplicaciones que se envían a él. El ApplicationId tiene el formato "application\_&lt;RM-start-time&gt;\_&lt;Counter&gt;". Tenga en cuenta que ApplicationId y JobId son distintos. JobId es un concepto específico del marco MapReduce mientras que ApplicationId es un concepto de YARN independiente de marco. En YARN, un JobId identifica un trabajo MapReduce específico como controlado por el maestro de aplicación de una aplicación de MapReduce enviada al administrador de recursos.

	// Download application logs for an application whose application Id is known
	string applicationId = "application_1416017767088_0028";
	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	appHistoryClient.DownloadApplicationLogs(someApplication, downloadLocation);

Si es necesario, también puede descargar los registros para cada contenedor (o cualquier contenedor específico) utilizado por una aplicación, como se muestra a continuación.

	ApplicationDetails someApplication = appHistoryClient.GetApplicationDetails(applicationId);
	
	// Download logs separately for each container of application(s) of interest
	// This will generate one log file per container
	IEnumerable<ApplicationAttemptDetails> applicationAttempts =
				appHistoryClient.ListApplicationAttempts(someApplication);
	
	ApplicationAttemptDetails finalAttempt = applicationAttempts
	    		.Single(x => x.ApplicationAttemptId == someApplication.LatestApplicationAttemptId);
	
	IEnumerable<ApplicationContainerDetails> containers =
				appHistoryClient.ListApplicationContainers(finalAttempt);
	
	foreach (ApplicationContainerDetails container in containers)
	{
	    appHistoryClient.DownloadApplicationLogs(container, downloadLocation);
	}



[YARN-timeline-server]:http://hadoop.apache.org/docs/r2.4.0/hadoop-yarn/hadoop-yarn-site/TimelineServer.html
[log-aggregation]:http://hortonworks.com/blog/simplifying-user-logs-management-and-access-in-yarn/
[T-file]:https://issues.apache.org/jira/browse/HADOOP-3315
[binary-format]:https://issues.apache.org/jira/secure/attachment/12396286/TFile%20Specification%2020081217.pdf 
[YARN-concepts]:http://hortonworks.com/blog/apache-hadoop-yarn-concepts-and-applications/
<!--HONumber=42-->
