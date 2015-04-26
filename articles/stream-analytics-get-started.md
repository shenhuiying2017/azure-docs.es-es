<properties 
	pageTitle="Introducción al uso de Azure Stream Analytics | Azure" 
	description="Introducción al uso de análisis de secuencias de Azure para procesar y transformar los eventos en el centro de eventos del Bus de servicio de Azure y almacenar los resultados en la base de datos de SQL Azure." 
	services="stream-analytics" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="2/17/2015" 
	ms.author="jgao" />


# Introducción al uso de Azure Stream Analytics

Azure Stream Analytics es un servicio totalmente administrado que proporciona un procesamiento complejo de eventos de baja latencia, alta disponibilidad y escalable a través de la transmisión de datos en la nube. Para obtener más información, consulte [Introducción a Azure Stream Analytics][stream.analytics.introduction] y la [documentación de Azure Stream Analytics][stream.analytics.documentation].

Para introducirse rápidamente en el uso de Stream Analytics, este tutorial le mostrará cómo consumir datos de lectura de temperatura de dispositivos de un [Centro de eventos de bus de servicio de Azure][azure.event.hubs.documentation] y cómo procesar los datos, con la salida de los resultados a una [base de datos SQL de Azure][azure.sql.database.documentation].  El siguiente diagrama muestra el flujo de eventos de entrada para el procesamiento a la salida:
  
![Azure Stream Analytics get started flow][img.get.started.flowchart]

##Generación de datos de ejemplo del centro de eventos
Este tutorial aprovechará la aplicación de introducción a los Centros de eventos de bus de servicio, un código de ejemplo de CodeGallery de MSDN, para crear un nuevo centro de eventos, generar lecturas de temperatura de dispositivos de ejemplo y enviar los datos de lectura del dispositivo al centro de eventos.

###Creación de un espacio de nombres de bus de servicio
La aplicación de ejemplo creará un centro de eventos en un espacio de nombres ya existente de bus de servicio.  Puede usar un espacio de nombres de bus de servicio que ya se ha aprovisionado o seguir los siguientes pasos para crear uno nuevo:

1.	Inicie sesión en el [Portal de administración de Azure][azure.management.portal].
2.	Haga clic en **CREAR** en la parte interior de la página Bus de servicio y siga las instrucciones para crear un espacio de nombres. Use el tipo **MENSAJERÍA**.
3.	Haga clic en el espacio de nombres recién creado y luego haga clic **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página.
4.	Copie la cadena de conexión. La usará más adelante en el tutorial.

###Creación de una cuenta de almacenamiento de Azure

Esta aplicación de ejemplo requiere una cuenta de almacenamiento de Azure o un emulador de almacenamiento para mantener el estado de la aplicación. Puede usar una cuenta de almacenamiento existente o seguir los pasos siguientes para crear una: 

1.	En el portal, cree una nueva cuenta de almacenamiento; para ello, haga clic en **NUEVO**, **SERVICIOS DE DATOS**, **ALMACENAMIENTO**, **CREACIÓN RÁPIDA** y siga las instrucciones.
2.	Seleccione la cuenta de almacenamiento recién creada y luego haga clic en **ADMINISTRAR CLAVES DE ACCESO** en la parte inferior de la página.
3.	Copie el nombre de la cuenta de almacenamiento y una de las claves de acceso.

###Generación de datos de ejemplo del centro de eventos

1.	Descargue y descomprima [Service Bus Event Hubs Getting Started.zip](https://code.msdn.microsoft.com/windowsapps/Service-Bus-Event-Hub-286fd097) en su estación de trabajo.
2.	Abra el archivo de la solución **EventHubSample.sln** en Visual Studio.
3.	Abra **app.config**.
4.	Especifique las cadenas de conexión Bus de servicio y Cuenta de almacenamiento. Los nombres de claves son **Microsoft.ServiceBus.ConnectionString** y **AzureStorageConnectionString**. La cadena de conexión de la cuenta de almacenamiento estará en el formato siguiente: 	

		DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<yourAccountKey>;
5.	Compile la solución.
6.	Ejecute la aplicación desde la carpeta bin.  El uso es como sigue: 

		BasicEventHubSample <eventhubname> <NumberOfMessagesToSend> <NumberOfPartitions> 

	En el ejemplo siguiente se crea un nuevo centro de eventos llamado **devicereadings** con **16** particiones, y luego se envían **200** eventos al centro de eventos: 

		BasicEventHubSample devicereadings 200 16

 	![insert image here][img.stream.analytics.event.hub.client.output] 
 	

###Creación de una directiva de acceso compartido de centro de eventos
Aunque ya hay una directiva de acceso compartido en el espacio de nombres del bus de servicio que puede utilizarse para conectarse a cualquier contenido dentro del espacio de nombres, los procedimientos de seguridad recomendados son crear una directiva aparte solo para el centro de eventos.

1.	En el área de trabajo del bus de servicio en el portal, haga clic en el nombre del espacio de nombres del bus de servicio.
2.	Haga clic en **CENTROS DE EVENTOS** en la parte superior de la página.
3.	Haga clic en **devicereadings**, el centro de eventos en este tutorial.
4.	Haga clic en **CONFIGURAR** en la parte superior de la página.
5.	En Directivas de acceso compartido, cree una nueva directiva con permisos para **Administrar**.

	![][img.stream.analytics.event.hub.shared.access.policy.config]
6.	Haga clic en **GUARDAR** en la parte inferior de la página.
7.	Si el centro de eventos se encuentra en una suscripción diferente a aquella en la que estará el trabajo de Stream Analytics, deberá copiar y guardar la información de conexión para más adelante.  Para ello, haga clic en **PANEL**, y luego en **INFORMACIÓN DE CONEXIÓN** en la parte inferior de la página y guarde la cadena de conexión.


##Preparación de una base de datos SQL Azure para almacenar los datos de salida
Azure Stream Analytics puede generar datos de salida a una bases de datos SQL de Azure, al almacenamiento de blobs de Azure y al centro de eventos de Azure. En este tutorial, definirá un trabajo que genera salidas a una base de datos SQL de Azure. Para obtener información, consulte Introducción a Base de datos SQL de Microsoft Azure..

###Creación de una base de datos SQL de Azure
Si ya tiene una base de datos SQL Azure que va a usar en este tutorial, omita esta sección.

1.	En el Portal de administración, haga clic en **NUEVO**, **SERVICIOS DE DATOS**, **BASE DE DATOS SQL**, **CREACIÓN RÁPIDA**.  Especifique un nombre de base de datos en un servidor de base de datos SQL nuevo o existente.
2.	Seleccione la base de datos recién creada.
3.	Haga clic en **PANEL**, haga clic en **Mostrar cadenas de conexión** en el panel derecho de la página y luego copie la cadena de conexión **ADO.NET**. La usará más adelante en el tutorial.  
4.	Asegúrese de que la configuración de firewall en el nivel de servidor le permite conectarse a la base de datos.  Para ello, agregue una nueva regla IP en la pestaña de configuración del servidor. Para obtener más detalles, junto con información sobre cómo gestionar la IP dinámica, consulte [http://msdn.microsoft.com/library/azure/ee621782.aspx](http://msdn.microsoft.com/library/azure/ee621782.aspx).

###Creación de tablas de salida
1.	Abra Visual Studio o SQL Server Management Studio.
2.	Conéctese a Base de datos SQL de Azure.
3.	Use las siguientes instrucciones de T-SQL para crear dos tablas en la base de datos:

		CREATE TABLE [dbo].[PassthroughReadings] (
		    [DeviceId]      BIGINT NULL,
			[Temperature] BIGINT    NULL
		);

		GO
		CREATE CLUSTERED INDEX [PassthroughReadings]
		    ON [dbo].[PassthroughReadings]([DeviceId] ASC);
		GO

		CREATE TABLE [dbo].[AvgReadings] (
		    [WinStartTime]   DATETIME2 (6) NULL,
		    [WinEndTime]     DATETIME2 (6) NULL,
		    [DeviceId]      BIGINT NULL,
			[AvgTemperature] FLOAT (53)    NULL,
			[EventCount] BIGINT null
		);
		
		GO
		CREATE CLUSTERED INDEX [AvgReadings]
		    ON [dbo].[AvgReadings]([DeviceId] ASC);

	>[WACOM.NOTE] Todas las tablas de Base de datos DQL requieren índices agrupados para poder insertar datos.
	   
##Creación de un trabajo de Stream Analytics

Una vez que haya creado el centro de eventos del bus de servicio de Azure, la base de datos SQL de Azure y las tablas de salida, ya está listo para crear un trabajo de Stream Analytics.

###Aprovisionamiento de un trabajo de Stream Analytics
1.	En el Portal de administración, haga clic en **NUEVO**,**SERVICIOS DE DATOS**, **STREAM ANALYTICS**, **CREACIÓN RÁPIDA**. 
2.	Especifique los valores siguientes y luego haga clic en **CREAR TRABAJO DE STREAM ANALYTICS**:

	- **NOMBRE DEL TRABAJO**: escriba un nombre para el trabajo. Por ejemplo, **TemperaturasDispositivos**.
	- **REGIÓN**: Seleccione la región donde desea ejecutar el trabajo. Azure Stream Analytics solo está disponible actualmente en 2 regiones durante la versión preliminar. Para obtener más información, consulte [Limitaciones y problemas conocidos de Azure Stream Analytics][stream.analytics.limitations]. Considere la posibilidad de colocar el trabajo y el centro de eventos en la misma región para garantizar un mejor rendimiento y que no tenga que pagar por transferir datos entre regiones.
	- **CUENTA DE ALMACENAMIENTO**: elija la cuenta de almacenamiento que desea usar para almacenar datos de supervisión de todos los trabajos de Stream Analytics que se ejecutan dentro de esta región. Tiene la opción de elegir una cuenta de almacenamiento existente o crear una.
	
3.	Haga clic en **STREAM ANALYTICS** en el panel izquierdo para mostrar los trabajos de Stream Analytics.

	![][img.stream.analytics.portal.button]
 
	El nuevo trabajo se mostrará con un estado de **NO INICIADO**.  Observe que el botón **INICIAR** de la parte inferior de la página está deshabilitado. Debe configurar la entrada, la salida y la consulta del trabajo antes de iniciar el trabajo. 

###Especificación de la entrada del trabajo

1.	Haga clic en el nombre del trabajo.
2.	Haga clic en **ENTRADAS** en la parte superior de la página y luego haga clic en **AGREGAR ENTRADA**. El cuadro de diálogo que se abre le guiará a través de una serie de pasos para configurar la entrada.
3.	Seleccione **SECUENCIA DE DATOS** y luego haga clic en el botón derecho.
4.	Seleccione **CENTRO DE EVENTOS** y luego haga clic en el botón derecho.
5.	Escriba o seleccione los valores siguientes en la tercera página: 

	- **ALIAS DE ENTRADA**: escriba un nombre descriptivo para esta entrada de trabajo. Tenga en cuenta que va a utilizar este nombre en la consulta más adelante.
	- **CENTRO DE EVENTOS**: si el centro de eventos que ha creado está en la misma suscripción que el trabajo de Stream Analytics, seleccione el espacio de nombres en el que está el centro de eventos.

		Si el centro de eventos está en una suscripción diferente, seleccione **Usar centro de eventos de otra suscripción** y escriba manualmente los valores para **ESPACIO DE NOMBRES DE SERVICE BUS**, **NOMBRE DE CENTRO DE EVENTOS**, **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**, **CLAVE DE DIRECTIVA DE CENTRO DE EVENTOS** y **RECUENTO DE PARTICIONES DE CENTRO DE EVENTOS**.  

		>[WACOM.NOTE] En este ejemplo se usa el número predeterminado de particiones, que es 16.
		
	- **NOMBRE DE CENTRO DE EVENTOS**: seleccione el nombre del centro de eventos de Azure que ha creado. En este tutorial, use **devicereadings**.
	- **NOMBRE DE DIRECTIVA DE CENTRO DE EVENTOS**: seleccione la directiva de centro de eventos que creó anteriormente en este tutorial.
 
	![][img.stream.analytics.config.input]

6.	Haga clic en el botón derecho.
7.	Especifique los siguientes valores:

	- **FORMATO DEL SERIALIZADOR DE EVENTOS**: JSON
	- **CODIFICACIÓN**: UTF8

8.	Haga clic en el botón de comprobación para agregar este origen y comprobar que Stream Analytics puede conectarse correctamente al centro de eventos.

###Especificación de la salida del trabajo
1.	Haga clic en **SALIDA** en la parte superior de la página y luego haga clic en **AGREGAR SALIDA**.
2.	Seleccione **BASE DE DATOS SQL** y luego haga clic en el botón derecho.
3.	Escriba o seleccione los siguientes valores.  Use la cadena de conexión ADO.NET desde la base de datos para rellenar los campos siguientes:

	- **BASE DE DATOS SQL**: elija la base de datos SQL que creó anteriormente en el tutorial.  Si se encuentra en la misma suscripción, seleccione la base de datos en el menú desplegable.   Si no, escriba manualmente los campos de nombre de servidor y base de datos. 
	- **NOMBRE DE USUARIO**: escriba el nombre de inicio de sesión de la base de datos SQL.
	- **CONTRASEÑA**: escriba la contraseña de inicio de sesión de la base de datos SQL.
	- **TABLA**: especifique la tabla a la que desea enviar la salida.  Por ahora, use **PassthroughReadings**.

	![][img.stream.analytics.config.output]

4.	Haga clic en el botón de comprobación para crear la salida y verificar que Stream Analytics puede conectarse correctamente a la base de datos SQL tal y como se especifica.

###Especificación de la consulta de trabajo
Stream Analytics admite un modelo de consulta declarativo sencillo para describir las transformaciones.  Para obtener más información sobre el lenguaje, consulte la referencia de lenguaje de consulta de Azure Stream Analytics.  

Este tutorial comenzará con una consulta de paso sencilla que genera lecturas de temperatura de dispositivo a una tabla de Base de datos SQL.

1.	Haga clic en **Consulta** en la parte superior de la página.
2.	Agregue lo siguiente al editor de código:

		SELECT DeviceId, Temperature FROM input
Asegúrese de que el nombre del origen de entrada coincida con el nombre de la entrada que especificó anteriormente.
3.	Haga clic en **GUARDAR** en la parte inferior de la página y en **SÍ** para confirmar.

##Inicio del trabajo
De forma predeterminada, los trabajos de Stream Analytics empiezan con la lectura de los eventos de entrada desde el mismo momento en que se inicia el trabajo.  Como el centro de eventos contiene datos existentes para procesar, debemos configurar el trabajo para que consuma estos datos históricos.  

1.	Haga clic en **CONFIGURAR** en la parte superior de la página.
2.	Cambie el valor de **INICIAR SALIDA** a **PERSONALIZAR HORA** y especifique una hora de inicio.  Asegúrese de que la hora de inicio sea anterior a la hora en que ejecutó BasicEventHubSample.  
3.	Haga clic en **GUARDAR** en la parte inferior de la página y en **SÍ** para confirmar.
3.	Haga clic en **PANEL** en la parte superior de la página y luego en **INICIAR** en la parte inferior de la página y en **SÍ** para confirmar.  En el panel de **vista rápida**, el **ESTADO** cambiará a **Iniciando** y el proceso de inicio puede tardar un par de minutos en completarse y pasar al estado **Ejecutando**.   


##Vista de la salida del trabajo

1.	En Visual Studio o SQL Server Management Studio, conéctese a la base de datos SQL y ejecute la consulta siguiente: 

		SELECT * FROM PassthroughReadings

2.	Verá los registros correspondientes a los eventos de lectura del centro de eventos.   

	![][img.stream.analytics.job.output1]

	Puede volver a ejecutar la aplicación BasicEventHubSample para generar nuevos eventos y ver que se propagan a la salida en tiempo real mediante la ejecución repetida de la consulta SELECT *.
	
	Si tiene algún problema con salidas que faltan o inesperadas, vea los registros de operaciones del trabajo, vinculados en el panel derecho de la página Panel.

##Detención, actualización y reinicio del trabajo
Ahora vamos a hacer una consulta más interesante sobre los datos.
1.	En la página **PANEL** o **SUPERVISAR**, haga clic en **DETENER**.
2.	E la página **CONSULTA**, sustituya la consulta existente por lo siguiente y haga clic en **GUARDAR**:

		SELECT DateAdd(second,-5,System.TimeStamp) as WinStartTime, system.TimeStamp as WinEndTime, DeviceId, Avg(Temperature) as AvgTemperature, Count(*) as EventCount 
		FROM input
		GROUP BY TumblingWindow(second, 5), DeviceId

	Esta nueva consulta utilizará como marca de tiempo la hora en que se insertó el evento en el centro de eventos, y buscará y proyectará la lectura de temperatura media cada 5 segundos y el número de eventos que se incluirán en esa ventana de 5 segundos.
3.	En la página **SALIDA**, haga clic en **EDITAR**.  Cambie la tabla de salida de PassthroughReadings a AvgReadings y, a continuación, haga clic en el icono de comprobación.

4.	En la página **PANEL**, haga clic en **INICIAR**.

##Vista de la salida del trabajo

1.	En Visual Studio o SQL Server Management Studio, conéctese a la base de datos SQL y ejecute la consulta siguiente:

		SELECT * from AvgReadings

2.	Verá registros durante intervalos de 5 segundos que muestran la temperatura media y el número de eventos para cada dispositivo: 

	![][img.stream.analytics.job.output2]
 
3.	 Para continuar viendo los eventos procesados por el trabajo en ejecución, vuelva a ejecutar la aplicación BasicEventHubSample.







##<a name="nextsteps"></a>Pasos siguientes
En este tutorial, ha aprendido a usar Stream Analytics para procesar los datos meteorológicos. Para obtener más información, consulte los artículos siguientes:


- [Introducción a Azure Stream Analytics][stream.analytics.introduction]
- [Guía para desarrolladores de Azure Stream Analytics][stream.analytics.developer.guide]
- [Escalación de trabajos de Azure Stream Analytics][stream.analytics.scale]
- [Problemas conocidos y limitaciones de Azure Stream Analytics][stream.analytics.limitations]
- [Referencia del lenguaje de consultas de Azure Stream Analytics][stream.analytics.query.language.reference]
- [Referencia de la API de REST de administración de Azure Stream Analytics][stream.analytics.rest.api.reference]




[img.stream.analytics.event.hub.client.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHClientOuput.png
[img.stream.analytics.event.hub.shared.access.policy.config]: .\media\stream-analytics-get-started\AzureStreamAnalyticsEHSharedAccessPolicyConfig.png
[img.stream.analytics.job.output2]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput2.png
[img.stream.analytics.job.output1]: .\media\stream-analytics-get-started\AzureStreamAnalyticsSQLOutput1.png
[img.stream.analytics.config.output]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureOutput.png
[img.stream.analytics.config.input]: .\media\stream-analytics-get-started\AzureStreamAnalyticsConfigureInput.png



[img.get.started.flowchart]: ./media/stream-analytics-get-started/StreamAnalytics.get.started.flowchart.png
[img.job.quick.create]: ./media/stream-analytics-get-started/StreamAnalytics.quick.create.png
[img.stream.analytics.portal.button]: ./media/stream-analytics-get-started/StreamAnalyticsPortalButton.png
[img.event.hub.policy.configure]: ./media/stream-analytics-get-started/StreamAnalytics.Event.Hub.policy.png
[img.create.table]: ./media/stream-analytics-get-started/StreamAnalytics.create.table.png
[img.stream.analytics.job.output]: ./media/stream-analytics-get-started/StreamAnalytics.job.output.png
[img.stream.analytics.operation.logs]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.png
[img.stream.analytics.operation.log.details]: ./media/stream-analytics-get-started/StreamAnalytics.operation.log.details.png


[azure.sql.database.firewall]: http://msdn.microsoft.com/library/azure/ee621782.aspx
[azure.event.hubs.documentation]: http://azure.microsoft.com/services/event-hubs/
[azure.sql.database.documentation]: http://azure.microsoft.com/services/sql-database/

[sql.database.introduction]: http://azure.microsoft.com/services/sql-database/
[event.hubs.introduction]: http://azure.microsoft.com/services/event-hubs/
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.sdk.net]: ../dotnet-sdk/

[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.scale]: ../stream-analytics-scale-jobs/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.documentation]: http://go.microsoft.com/fwlink/?LinkId=512093




[azure.management.portal]: https://manage.windowsazure.com


<!--HONumber=46--> 

<!--HONumber=46--> 
