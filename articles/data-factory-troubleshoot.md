<properties 
	pageTitle="Solución de problemas de la Factoría de datos de Azure" 
	description="Obtenga información acerca de la solución de problemas relacionados con la factoría de datos de Azure." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Solución de problemas de la factoría de datos
Puede solucionar los problemas de la Factoría de datos de Azure mediante el Portal de Azure (o) cmdlets de Azure PowerShell. Este tema contiene tutoriales que muestran cómo usar el Portal de Azure para solucionar rápidamente los errores que se producen con la factoría de datos.

## Problema: No se puede ejecutar los cmdlets del generador de datos
Para resolver este problema, cambie el modo de Azure para **AzureResourceManager**:

Iniciar **Azure PowerShell** y ejecute el siguiente comando para cambiar a la **AzureResourceManager** modo. Los cmdlets de generador de datos de Azure están disponibles en la **AzureResourceManager** modo.

         switch-azuremode AzureResourceManager

## Problema: Error no autorizado cuando se ejecuta un cmdlet de generador de datos
Probablemente no está utilizando la cuenta de Azure derecha o suscripción con Azure PowerShell. Use los siguientes cmdlets para seleccionar la cuenta de Azure derecha y la suscripción para usar con Azure PowerShell.

1. Add-AzureAccount - Use el identificador de usuario y contraseña
2. Get-AzureSubscription - ver todas las suscripciones para la cuenta. 
3. SELECT-AzureSubscription <subscription name> -Seleccione la suscripción. Utilice el mismo que se utiliza para crear un generador de datos en el Portal de vista previa de Azure.

## Problema: No se pudo iniciar la instalación de Express de puerta de enlace de datos del Portal de Azure
El programa de instalación de Express para la puerta de enlace de datos requiere Internet Explorer o un explorador web compatible de Microsoft ClickOnce. Si se produce un error al iniciar el programa de instalación de Express, puede

1. Cambie a Internet Explorer si se produce un error con otros exploradores. O
2. Use los vínculos de "Instalación Manual" se muestra en el cuadro del mismo en el portal para realizar la instalación y, a continuación, copie la clave que se proporciona en la pantalla y pegar cuando la configuración de Data Management Gateway está lista. Si no se inicia, busque "Microsoft Data Management Gateway" en el menú Inicio y pegue la clave cuando se inicia. 


## Problema: No se pudo iniciar el Administrador de credenciales desde el Portal de Azure
Al configurar o actualizar un servicio de SQL Server vinculado mediante el Portal de Azure, el Administrador de credenciales que se inicia la aplicación para garantizar la seguridad. Requiere Internet Explorer o un explorador web compatible de Microsoft ClickOnce. Puede cambiar a Internet Explorer si se produce un error con otros exploradores.

## Problema: No se pueden conectar a local de SQL Server 
Compruebe que el servidor SQL Server es accesible desde la máquina donde está instalada la puerta de enlace. En el equipo donde está instalada la puerta de enlace, puede

1. Haga ping en el equipo donde está instalado el servidor SQL Server. O
2. Intente conectarse a la instancia de SQL Server con las credenciales especificadas en el Portal de Azure con SQL Server Management Studio (SSMS).


## Problema: Los intervalos de entrada están en estado PendingExecution o PendingValidation para siempre

Los intervalos pueden estar en **PendingExecution** o **PendingValidation** estado debido a varias razones y uno de los motivos habituales es que la **waitOnExternal** propiedad no se especifica en el **disponibilidad** sección de la primera tabla/conjunto de datos en la canalización. Cualquier conjunto de datos que se produce fuera del ámbito de la factoría de datos de Azure debe marcarse con **waitOnExternal** propiedad bajo **disponibilidad** sección. Esto indica que los datos externos y no con el respaldo de las canalizaciones en el generador de datos. Los segmentos de datos se marcan como **Listo** una vez que los datos están disponibles en el almacén correspondiente.

Vea el ejemplo siguiente, el uso de la **waitOnExternal** propiedad. Puede especificar **{} waitOnExternal** sin establecer los valores de propiedades de la sección para que se utilizan los valores predeterminados.

Vea el tema de las tablas en [referencia de secuencias de comandos de JSON][json-scripting-reference] para obtener más detalles acerca de esta propiedad.
	
	{
	    "name": "CustomerTable",
	    "properties":
	    {
	        "location":
	        {
	            "type": "AzureBlobLocation",
	            "folderPath": "MyContainer/MySubFolder/",
	            "linkedServiceName": "MyLinkedService",
	            "format":
	            {
	                "type": "TextFormat",
	                "columnDelimiter": ",",
	                "rowDelimiter": ";"
	            }
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1,
	            "waitOnExternal":
	            {
	                "dataDelay": "00:10:00",
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

 Para resolver el error, agregue el **waitOnExternal** sección a la definición de JSON de la tabla de entrada y volver a crear la tabla.

## Problema: Se produce un error de la operación de copia de híbrido
Para obtener información más detallada:

1. Inicie Data Management Gateway Configuration Manager en el equipo en el que se instaló la puerta de enlace. Compruebe que la **nombre de puerta de enlace** se establece en el nombre lógico de la puerta de enlace en el **Portal Azure**, **estado clave de puerta de enlace** es **registrado** y **estado del servicio** es **Started**. 
2. Iniciar **Visor de sucesos**. Expanda **registros de aplicaciones y servicios** y haga clic en **Data Management Gateway**. Vea si hay errores relacionados con Data Management Gateway. 

## Problema: En demanda HDInsight aprovisionamiento falla con Error

Al utilizar un servicio de tipo HDInsightOnDemandLinkedService vinculado, debe especificar un linkedServiceName que apunta al almacenamiento de blobs de Azure. Esta cuenta de almacenamiento se utilizará para copiar todos los registros y archivos auxiliares para el clúster de HDInsight a petición. A veces puede producir un error de la actividad que realiza el aprovisionamiento a petición en HDInsight con el siguiente error:

		Failed to create cluster. Exception: Unable to complete the cluster create operation. Operation failed with code '400'. Cluster left behind state: 'Error'. Message: 'StorageAccountNotColocated'.

Este error suele indicar que no es la ubicación de la cuenta de almacenamiento especificada en el linkedServiceName en la misma ubicación de centro de datos como donde ocurre el aprovisionamiento de HDInsight. Por ejemplo, si la ubicación del generador de datos de Azure es oeste de EE. y el aprovisionamiento de HDInsight a petición se produce en oeste de Estados Unidos, pero la ubicación de cuenta de almacenamiento de blobs de Azure se establece en East US, el aprovisionamiento a petición se producirá un error.

Además, hay una segunda additionalLinkedServiceNames de propiedad JSON donde se pueden especificar las cuentas de almacenamiento adicional en HDInsight a petición. Las cuentas de almacenamiento vinculado adicional deben estar en la misma ubicación que el clúster de HDInsight o se producirá un error con el mismo error.



## Problema: Error de actividad personalizada
Al usar una actividad personalizada en la factoría de datos de Azure (tipo de actividad de canalización CustomActivity), la aplicación personalizada se ejecuta en el servicio vinculado especificado para HDInsight como un mapa de transmisión por secuencias solo trabajo de MapReduce.

Cuando se ejecuta la actividad personalizada, generador de datos de Azure será capaz de capturar los resultados que el clúster de HDInsight y guárdelo en el *adfjobs* contenedor de almacenamiento en su cuenta de almacenamiento Blob de Azure. Si se produce un error, puede leer el texto de **stderr** archivo de texto de salida después de que se ha producido un error. Los archivos sean accesibles y legible en el portal de Azure propio en el explorador web, o mediante el uso de herramientas del explorador de almacenamiento para tener acceso a los archivos que se mantienen en el contenedor de almacenamiento en el almacenamiento de blobs de Azure directamente.

Para enumerar y leer los registros para una actividad personalizada, puede seguir uno de los tutoriales se muestra más adelante en esta página. En resumen:

1.  En el portal de Azure **Examinar** para buscar su generador de datos.
2.  Utilice la **diagrama** botón para ver el diagrama de generador de datos y haga clic en el **Dataset** tabla siguiente específico **canalización** que tiene la actividad personalizada. 
3.  En el **tabla** blade, haga clic en el sector de interés en el **sectores problema** para el período de tiempo a investigar.
4.  La detallada **segmento de datos** blade aparecerá y pueden enumerar varios **actividad se ejecuta** del sector. Haga clic en un **actividad** en la lista. 
5.  El **Detalles de ejecución de la actividad** blade aparecerán. Obtendrá una lista de los **mensaje de Error** en medio de la tarjeta y varios **archivos de registro** aparece en la parte inferior de la tarjeta que ejecutar la actividad que está asociado.
	- Registros, system-0.log
	- Estado
	- Estado o salida
	- Estado/stderr
	- Estado/stdout

6. Haga clic en la primera **archivo de registro** elemento de la lista y el registro se abrirá en un nuevo cuadro con el texto completo que se muestra para que su lectura. Revise el texto de cada registro haciendo clic en cada uno de ellos. Se abrirá el cuadro de texto del Visor. Puede hacer clic en el **descargar** botón para descargar el archivo de texto para verlos sin conexión opcional.

Una **error común** desde una actividad personalizada es la ejecución del paquete errónea con código de salida '1'. Vea ' wasb://adfjobs@storageaccount.blob.core.windows.net/PackageJobs/<guid>/<jobid>/estado/stderr ' para obtener más detalles.

Para ver más detalles de este tipo de error, abra el **stderr** archivo. Un error común ver aquí es una condición de tiempo de espera como ésta: mapreduce INFO. Trabajo: Id. de tarea: attempt_1424212573646_0168_m_000000_0, estado: no se pudo AttemptID:attempt_1424212573646_0168_m_000000_0 agotado tras 600 segundos

Este mismo error puede aparecer varias veces, si el trabajo ha intentado ejecutar tres veces, por ejemplo, en el intervalo de 30 o más minutos.

Este error de tiempo de espera indica un 600 segundos (10 minutos) tuvo lugar el tiempo de espera. Normalmente esto significa que la aplicación de .net personalizadas no ha emitido ninguna actualización del estado de 10 minutos. Si la aplicación se bloquea o detenido esperando en algo demasiado largo, 10 minutos tiempo de espera es un mecanismo de seguridad para evitar que lo espera indefinidamente y retrasar la canalización de la factoría de datos de Azure.

Este tiempo de espera se origina en la configuración del clúster de HDInsight que está vinculada a la actividad personalizada. El valor es **mapred.task.timeout**, que de forma predeterminada 600000 milisegundos, como se documenta en la configuración de Apache predeterminada aquí: http://hadoop.apache.org/docs/r2.4.0/hadoop-mapreduce-client/hadoop-mapreduce-client-core/mapred-default.xml

Puede reemplazar este valor predeterminado cambiando los valores predeterminados en el momento de aprovisionamiento de su clúster de aprovisionamiento de HDInsight. Cuando se utiliza el generador de datos de Azure y **HDInsight a petición** vinculados de servicio, la propiedad JSON se pueden agregar cerca de las propiedades HDInsightOnDemandLinkedService JSON. Por ejemplo, puede aumentar el valor a 20 minutos con esta propiedad JSON.
		
		"mapReduceConfiguration" :
		{
			"mapreduce.task.timeout":"1200000"
		}
		

Para más contexto y un ejemplo completo de JSON para editar estos mapa reducir de configuración las propiedades, vea Ejemplo 3 en la documentación de MSDN https://msdn.microsoft.com/library/azure/dn893526.aspx

## Problema: PowerShell solicitud produce un error con el error 400 Solicitud incorrecta "Ningún proveedor de recursos registrado encuentra..."

A partir del 10 de marzo de 2015, se suspenderá las generador de datos de Azure PowerShell preview privada versiones anteriores 2014-05-01-preview, vista previa 2014-07-01 y 2014-08-01-vista previa. Recomendamos que utilice la versión más reciente de los cmdlets ADF, que ahora son parte de Azure PowerShell de descarga, como la descarga de esta dirección URL http://go.microsoft.com/?linkid=9811175&clcid=0x409

Si utiliza las versiones del SDK de Azure PowerShell discontinuos recibirá los siguientes errores:

		HTTP/1.1 400 Bad Request
		Cache-Control: no-cache
		Pragma: no-cache
		Content-Type: application/json; charset=utf-8
		Expires: -1
		x-ms-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-correlation-request-id: e07181e4-e421-46be-8a08-1f71d5e90494
		x-ms-routing-request-id: WESTUS:20150306T234829Z:e07181e4-e421-46be-8a08-1f71d5e90494
		Strict-Transport-Security: max-age=31536000; includeSubDomains
		Date: Fri, 06 Mar 2015 23:48:29 GMT
		Content-Length: 157
		{"error":{"code":"NoRegisteredProviderFound","message":"No registered resource provider found for location 'west US' and API version '2014-05-01-preview'."}}


## <a name="copywalkthrough"></a> Tutorial: Error al copiar datos de solución de problemas
En este tutorial, presentará un error en el tutorial del artículo Introducción a la factoría de datos y aprenderá a usar el Portal de Azure para solucionar el error.

### Requisitos previos
1. Completar el Tutorial en el [empezar a trabajar con el generador de datos de Azure][adfgetstarted] artículo.
2. Confirme que la **ADFTutorialDataFactory** genera datos en el **emp** tabla en la base de datos de SQL Azure.  
3. Ahora, elimine la **emp** tabla (** colocar tabla emp **) de la base de datos de SQL Azure. Esto presentará un error.
4. Ejecute el siguiente comando el **Azure PowerShell** actualizar el período activo para la canalización para que intenta escribir datos en el **emp** tabla, que ya no existe.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 –EndDateTime 2014-09-30 –Name ADFTutorialPipeline
	
	> [AZURE.NOTE]Reemplace <b>StartDateTime</b> valor con la fecha actual y <b>finalización</b> valor con el día siguiente.


### Uso del Portal de vista previa de Azure para solucionar el error

1.	Inicio de sesión [Portal de vista previa de Azure][azure-preview-portal]. 
2.	Haga clic en **ADFTutorialDataFactory** desde el **panel de inicio**. Si no ve el generador de datos de vínculo en el **panel de inicio**, haga clic en **Examinar** concentrador y haga clic en **todo**. Haga clic en **generadores de datos...** en el **Examinar** blade y haga clic en **ADFTutorialDataFactory**.
3.	Observe que verá **con errores** en el **conjuntos de datos** de mosaico. Haga clic en **con errores**. Debería ver **conjuntos de datos con errores** blade.

	![Generador de datos con enlace de errores][image-data-factory-troubleshoot-with-error-link]

4. En el **conjuntos de datos** con tarjeta de errores, haga clic en **EmpSQLTable** para ver el **tabla** blade.

	![Conjuntos de datos con el módulo de errores][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. En el **tabla** blade, debería ver los sectores del problema, es decir, segmentos con un error en la **sectores problema** lista en la parte inferior. También puede ver los sectores recientes con errores en el **recientes sectores** lista. Haga clic en un sector en el **sectores problema** lista.

	![Módulo de tabla con sectores de problema][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Si hace clic en **sectores problema** (no en un problema específico), verá la **REBANADAS de datos** blade y, a continuación, haga clic en un **sector de problema específico** para ver el **segmento de datos** diapositiva para el segmento de datos seleccionado.

6. En el **segmento de datos** módulo para **EmpSQLTable**, verá todas **se ejecuta la actividad** para el sector de la lista en la parte inferior. Haga clic en un **actividad se ejecute** de la lista de errores.

	![Módulo de segmento de datos con Ejecuciones activas][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. En el **Detalles de ejecución de la actividad** ejecutar blade para la actividad seleccionada, deberá ver los detalles sobre el error. En este escenario, verá: **nombre de objeto no válido 'emp'**.

	![Detalles de ejecución con un error de actividad][image-data-factory-troubleshoot-activity-run-with-error]

Para resolver este problema, cree el **emp** tabla utilizando la instrucción SQL de secuencias de comandos de [comenzar con el generador de datos][adfgetstarted] artículo.


### Uso de cmdlets de Azure PowerShell para solucionar el error
1.	Iniciar **PowerShell Azure**. 
2.	Cambiar a **AzureResourceManager** modo que los cmdlets del generador de datos solo están disponibles en este modo.

         
		switch-azuremode AzureResourceManager

3. Ejecute el comando Get-AzureDataFactorySlice para ver los segmentos y sus estados. Debería ver un sector con el estado: error.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [AZURE.NOTE]Reemplace **StartDateTime** con el StartDateTime valor especificado para la **AzureDataFactoryPipelineActivePeriod Set**.

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Tenga en cuenta el **iniciar** tiempo para el sector del problema (el sector con **estado** establecido en **error**) en la salida. 
4. Ahora, ejecute la **AzureDataFactoryRun Get** cmdlet para obtener detalles acerca de la actividad se ejecute para el sector.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	El valor de **StartDateTime** es la hora de inicio para el intervalo de error o problema que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
5. Debería ver la salida con detalles sobre el error (similar a la siguiente):

		Id                  	: 2b19475a-c546-473f-8da1-95a9df2357bc
		ResourceGroupName   	: ADFTutorialResourceGroup
		DataFactoryName     	: ADFTutorialDataFactory
		TableName           	: EmpSQLTable
		ResumptionToken    		:
		ContinuationToken   	:
		ProcessingStartTime 	: 10/15/2014 11:13:39 PM
		ProcessingEndTime  	 	: 10/15/2014 11:16:59 PM
		PercentComplete     	: 0
		DataSliceStart     		: 10/15/2014 4:00:00 PM
		DataSliceEnd       		: 10/15/2014 5:00:00 PM
		Status              	: FailedExecution
		Timestamp           	: 10/15/2014 11:13:39 PM
		RetryAttempt       		: 0
		Properties          	: {}
		ErrorMessage        	: Unknown error in CopyActivity: System.Data.SqlClient.SqlException (0x80131904): **Invalid object name 'emp'.**
                         at System.Data.SqlClient.SqlConnection.OnError(SqlException exception, Boolean
                      breakConnection, Action`1 wrapCloseInAction)
                         at System.Data.SqlClient.TdsParser.ThrowExceptionAndWarning(TdsParserStateObject stateObj,

 

## <a name="pighivewalkthrough"></a> Tutorial: Solucionar un error con el procesamiento de Hive y Pig
Este tutorial proporciona pasos para solucionar un error con el procesamiento de Hive/Pig mediante el Portal de vista previa de Azure y Azure PowerShell.


### Tutorial: Utilizar Portal de Azure para solucionar un error con el procesamiento de Pig y Hive
En este escenario, el conjunto de datos está en estado de error debido a un error en el procesamiento de Hive en un clúster de HDInsight.

1. Haga clic en **con errores** en **conjuntos de datos** de mosaico en el **factoría de datos** página principal.

	![Con vínculo de errores en mosaico de conjuntos de datos][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. En el **conjuntos de datos con errores** blade, haga clic en el **tabla** que le interesa.

	![Conjuntos de datos con el módulo de errores][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. En el **tabla** blade, haga clic en el **sector problema** con **estado** establecido en **error**.

	![Tabla con sectores de problema][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. En el **segmento de datos** blade, haga clic en el **actividad ejecutar** que produjo un error.

	![Segmento de datos con ejecuciones erróneas][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. En el **Detalles de ejecución de la actividad** módulo, puede descargar los archivos asociados con el procesamiento de HDInsight. Haga clic en **descargar** para **estado/stderr** para descargar el archivo de registro de errores que contiene detalles sobre el error.

	![Detalles de ejecución con el vínculo de descarga de actividad][image-data-factory-troubleshoot-activity-run-details]

    
### Tutorial: Usar Azure PowerShell para solucionar un error con el procesamiento de Pig y Hive
1.	Iniciar **PowerShell Azure**. 
2.	Cambiar a **AzureResourceManager** modo que los cmdlets del generador de datos solo están disponibles en este modo.

         
		switch-azuremode AzureResourceManager

3. Ejecute el comando Get-AzureDataFactorySlice para ver los segmentos y sus estados. Debería ver un sector con el estado: error.

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [AZURE.NOTE]Reemplace **StartDateTime** con el StartDateTime valor especificado para la **AzureDataFactoryPipelineActivePeriod Set**.

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Tenga en cuenta el **iniciar** tiempo para el sector del problema (el sector con **estado** establecido en **error**) en la salida. 
4. Ahora, ejecute la **AzureDataFactoryRun Get** cmdlet para obtener detalles acerca de la actividad se ejecute para el sector.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	El valor de **StartDateTime** es la hora de inicio para el intervalo de error o problema que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
5. Debería ver la salida con detalles sobre el error (similar a la siguiente):

		Id                  : 841b77c9-d56c-48d1-99a3-8c16c3e77d39
		ResourceGroupName   : ADF
		DataFactoryName     : LogProcessingFactory3
		TableName           : EnrichedGameEventsTable
		ProcessingStartTime : 10/10/2014 3:04:52 AM
		ProcessingEndTime   : 10/10/2014 3:06:49 AM
		PercentComplete     : 0
		DataSliceStart      : 5/5/2014 12:00:00 AM
		DataSliceEnd        : 5/6/2014 12:00:00 AM
		Status              : FailedExecution
		Timestamp           : 10/10/2014 3:04:52 AM
		RetryAttempt        : 0
		Properties          : {}
		ErrorMessage        : Pig script failed with exit code '5'. See 'wasb://adfjobs@spestore.blob.core.windows.net/PigQuery
								Jobs/841b77c9-d56c-48d1-99a3-8c16c3e77d39/10_10_2014_03_04_53_277/Status/stderr' for more details.
		ActivityName        : PigEnrichLogs
		PipelineName        : EnrichGameLogsPipeline
		Type                :

6. Puede ejecutar **Guardar AzureDataFactoryLog** cmdlet con el valor de identificador en la salida anterior ver y descargar el registro de archivos mediante el **- DownloadLogs** opción para el cmdlet.



[adfgetstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
[json-scripting-reference]: http://go.microsoft.com/fwlink/?LinkId=516971

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!---HONumber=GIT-SubDir-->