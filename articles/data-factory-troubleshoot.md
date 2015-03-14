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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Solución de problemas de la factoría de datos
Puede solucionar los problemas de la Factoría de datos de Azure mediante el Portal de Azure (o) cmdlets de Azure PowerShell. Este tema contiene tutoriales que muestran cómo usar el Portal de Azure para solucionar rápidamente los errores que se producen con la factoría de datos. 

## En este artículo

- [Tutorial: Solución de un error con la copia de datos](#copywalkthrough)
- [Tutorial: Solución de un error con el procesamiento de Hive/Pig](#pighivewalkthrough)

## <a name="copywalkthrough"></a>Tutorial: Solución de un error con la copia de datos
En este tutorial, presentará un error en el tutorial del artículo Introducción a la factoría de datos y aprenderá a usar el Portal de Azure para solucionar el error.

### Requisitos previos
1. Complete el tutorial del artículo [Introducción a la factoría de datos][adfgetstarted].
2. Confirme que **ADFTutorialDataFactory** genera datos de la tabla **emp** en Base de datos SQL de Azure.  
3. Ahora, elimine la tabla **emp** (**drop table emp**) de Base de datos SQL de Azure. Esto presentará un error.
4. Ejecute el siguiente comando en **Azure PowerShell** para actualizar el período activo para la canalización para que intente escribir datos en la tabla **emp**, que ya no existe.

         
		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipeline
	
	> [WACOM.NOTE] Reemplace el valor de <b>StartDateTime</b> por el día actual y el valor de <b>EndDateTime</b>por el día siguiente. 


### Uso del Portal de vista previa de Azure para solucionar el error

1.	Inicie sesión en el [Portal de vista previa de Azure][azure-preview-portal]. 
2.	Haga clic en **ADFTutorialDataFactory** en el **Panel de inicio**. Si no ve el vínculo de la factoría de datos en el **Panel de inicio**, haga clic en Centro **EXAMINAR** y, a continuación, haga clic en **Todo**. Haga clic en **Factorías de datos...** en la hoja **Examinar** y haga clic en **ADFTutorialDataFactory**.
3.	Observe que aparece **Con errores** en el mosaico **Conjuntos de datos**. Haga clic en **Con errores**. Debe ver la hoja **Conjuntos de datos con errores**.

	![Data Factory with Errors link][image-data-factory-troubleshoot-with-error-link]

4. En la hoja **Conjuntos de datos con errores**, haga clic en **EmpSQLTable** para ver la hoja **TABLA**.	

	![Datasets with errors blade][image-data-factory-troubleshoot-datasets-with-errors-blade]

5. En la hoja **TABLA**, debería ver los segmentos con problemas, es decir, los segmentos con un error en la lista **Segmentos con problemas** en la parte inferior. También puede ver todos los segmentos recientes con errores en la lista **Segmentos recientes**. Haga clic en un segmento en la lista **Segmentos con problemas**. 

	![Table blade with problem slices][image-data-factory-troubleshoot-table-blade-with-problem-slices]

	Si hace clic en **Segmentos con problemas** (no en un problema específico), verá la hoja **SEGMENTOS DE DATOS** y, a continuación, haga clic en un **segmento con problema específico**para ver el segmento **SEGMENTO DE DATOS** para el segmento de datos seleccionado.

6. En la hoja **SEGMENTO DE DATOS** para **EmpSQLTable**, verá todas las **ejecuciones de actividad** para el segmento en la lista de la parte inferior. Haga clic en una **ejecución de actividad** en la lista que dio error.

	![Data Slice blade with active runs][image-data-factory-troubleshoot-dataslice-blade-with-active-runs]


7. En la hoja **Detalles de ejecución de actividad** para la ejecución de actividad que ha seleccionado, deberá ver los detalles sobre el error. En este escenario, verá: **Nombre de objeto no válido 'emp'**.

	![Activity run details with an error][image-data-factory-troubleshoot-activity-run-with-error]

Para resolver este problema, cree la tabla **emp** mediante el script SQL del artículo [Introducción a la factoría de datos][adfgetstarted].


### Uso de cmdlets de Azure PowerShell para solucionar el error
1.	Inicie **Azure PowerShell**. 
2.	Cambie al modo **AzureResourceManager**, ya que los cmdlets de la factoría de datos solo están disponibles en este modo.

         
		switch-azuremode AzureResourceManager

3. Ejecute el comando Get-AzureDataFactorySlice para ver los segmentos y sus estados. Debería ver un segmento con el estado: Error.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-10-15

	> [WACOM.NOTE] Reemplace **StartDateTime** por el valor de StartDateTime especificado para **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName 		: ADFTutorialResourceGroup
		DataFactoryName   		: ADFTutorialDataFactory
		TableName         		: EmpSQLTable
		Start             		: 10/15/2014 4:00:00 PM
		End               		: 10/15/2014 5:00:00 PM
		RetryCount        		: 0
		Status            		: Failed
		LatencyStatus     		:
		LongRetryCount    		: 0

	Anote la hora **Inicio** para el segmento con problemas (el segmento con **Estado**establecido en **Error**) en la salida. 
4. Ahora, ejecute el cmdlet **Get-AzureDataFactoryRun** para obtener detalles acerca de la ejecución de actividad para el segmento.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime "10/15/2014 4:00:00 PM"

	El valor de **StartDateTime** es la hora de inicio para el segmento con error o problema que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
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

 

## <a name="pighavewalkthrough"></a>Tutorial: Solución de un error con el procesamiento de Hive/Pig
Este tutorial proporciona pasos para solucionar un error con el procesamiento de Hive/Pig mediante el Portal de vista previa de Azure y Azure PowerShell. 


### Tutorial: Uso del Portal de Azure para solucionar un error con el procesamiento de Pig/Hive
En este escenario, el conjunto de datos está en estado de error debido a un error en el procesamiento de Hive en un clúster de HDInsight.

1. Haga clic en **Con errores** en el título **Conjuntos de datos** en la página principal **FACTORÍA DE DATOS**.

	![With errors link on Datasets tile][image-data-factory-troubleshoot-walkthrough2-with-errors-link]

2. En la hoja **Conjuntos de datos con errores**, haga clic en la **tabla** en la que está interesado.

	![Datasets with errors blade][image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]

3. En la hoja **TABLA**, haga clic en el **segmento con problemas** con **ESTADO** establecido en **Error**.

	![Table with problem slices][image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]

4. En la hoja **SEGMENTO DE DATOS**, haga clic en el campo **Ejecución de actividad** que tuvo el error.

	![Data slice with failed runs][image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]

5. En la hoja **DETALLES DE EJECUCIÓN DE ACTIVIDAD**, puede descargar los archivos asociados con el procesamiento de HDInsight. Haga clic en **Descargar** correspondiente a **Status/stderr** para descargar el archivo de registro de errores que contiene detalles sobre el error.

	![Activity run details with download link][image-data-factory-troubleshoot-activity-run-details]

    
### Tutorial: Uso de Azure PowerShell para solucionar un error con el procesamiento de Pig/Hive
1.	Inicie **Azure PowerShell**. 
2.	Cambie al modo **AzureResourceManager**, ya que los cmdlets de la factoría de datos solo están disponibles en este modo.

         
		switch-azuremode AzureResourceManager

3. Ejecute el comando Get-AzureDataFactorySlice para ver los segmentos y sus estados. Debería ver un segmento con el estado: Error.	

         
		Get-AzureDataFactorySlice -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime 2014-05-04 20:00:00

	> [WACOM.NOTE] Reemplace **StartDateTime** por el valor de StartDateTime especificado para **Set-AzureDataFactoryPipelineActivePeriod**. 

		ResourceGroupName : ADF
		DataFactoryName   : LogProcessingFactory
		TableName         : EnrichedGameEventsTable
		Start             : 5/5/2014 12:00:00 AM
		End               : 5/6/2014 12:00:00 AM
		RetryCount        : 0
		Status            : Failed
		LatencyStatus     :
		LongRetryCount    : 0


	Anote la hora **Inicio** para el segmento con problemas (el segmento con **Estado**establecido en **Error**) en la salida. 
4. Ahora, ejecute el cmdlet **Get-AzureDataFactoryRun** para obtener detalles acerca de la ejecución de actividad para el segmento.
         
		Get-AzureDataFactoryRun -ResourceGroupName ADF -DataFactoryName LogProcessingFactory -TableName EnrichedGameEventsTable -StartDateTime "5/5/2014 12:00:00 AM"

	El valor de **StartDateTime** es la hora de inicio para el segmento con error o problema que anotó en el paso anterior. La fecha y hora debe ir encerrada entre comillas dobles.
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

6. Puede ejecutar el cmdlet **Save-AzureDataFactoryLog** con el valor de identificador que ve en la salida anterior y descargar los archivos de registro mediante la opción **-DownloadLogs** para el cmdlet.

## Sugerencias de solución de problemas

### No puede conectarse al servidor SQL Server local 
Compruebe que el servidor SQL Server es accesible desde la máquina donde está instalada la puerta de enlace.


1. Ejecute un ping en la máquina donde está instalado el servidor SQL Server.
2. En la máquina donde está instalada la puerta de enlace, intente conectarse a la instancia de SQL Server mediante las credenciales especificadas en el Portal de Azure con SQL Server Management Studio (SSMS).

### Error de la operación de copia
1. Inicie el Administrador de configuración de Data Management Gateway en la máquina en la que se instaló la puerta de enlace. Compruebe que **Nombre de la puerta de enlace** se establece en el nombre lógico de la puerta de enlace en el **Portal de Azure**, **Estado de la clave de la puerta de enlace** es **registrado** y **Estado del servicio** es **Iniciado**. 
2. Inicie el **Visor de eventos**. Expanda **Registros de aplicaciones y servicios** y haga clic en **Data Management Gateway**. Vea si hay errores relacionados con Data Management Gateway. 



## Otras referencias

Artículo | Descripción
------ | ---------------
[Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] | Este artículo incluye un tutorial que muestra cómo copiar datos desde una base de datos SQL Server local hasta un blob de Azure.
[Uso de Pig y Hive con la factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo incluye un tutorial que muestra cómo usar la actividad de HDInsight para ejecutar un script de hive/pig con el fin de procesar datos de entrada para producir datos de salida. 
[Tutorial: Movimiento y procesamiento de los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un completo tutorial que muestra cómo implementar un escenario en tiempo real mediante el uso de la Factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Uso de actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones paso a paso para crear una actividad personalizada y utilizarla en un proceso. 
[Supervisión y administración de la Factoría de datos de Azure con PowerShell][monitor-manage-using-powershell] | Este artículo describe cómo supervisar una Factoría de datos de Azure mediante cmdlets de Azure PowerShell. 
[Solución de problemas de la factoría de datos][troubleshoot] | Este artículo describe cómo solucionar problemas de la factoría de datos de Azure. Puede probar el tutorial de este artículo en ADFTutorialDataFactory introduciendo un error (eliminación de la tabla en la Base de datos SQL de Azure). 
[Referencia para desarrolladores de la Factoría de datos de Azure][developer-reference] | La referencia para desarrolladores incluye el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 
[Referencia de cmdlets de Factoría de datos de Azure][cmdlet-reference] | Este contenido de referencia incluye detalles acerca de todos los **cmdlets de la factoría de datos**.

[adfgetstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[azure-preview-portal]: https://portal.azure.com/

[image-data-factory-troubleshoot-with-error-link]: ./media/data-factory-troubleshoot/DataFactoryWithErrorLink.png

[image-data-factory-troubleshoot-datasets-with-errors-blade]: ./media/data-factory-troubleshoot/DatasetsWithErrorsBlade.png

[image-data-factory-troubleshoot-table-blade-with-problem-slices]: ./media/data-factory-troubleshoot/TableBladeWithProblemSlices.png

[image-data-factory-troubleshoot-activity-run-with-error]: ./media/data-factory-troubleshoot/DataSliceBladeWithActivityRuns.png

[image-data-factory-troubleshoot-dataslice-blade-with-active-runs]: ./media/data-factory-troubleshoot/ActivityRunDetailsWithError.png

[image-data-factory-troubleshoot-walkthrough2-with-errors-link]: ./media/data-factory-troubleshoot/Walkthrough2WithErrorsLink.png

[image-data-factory-troubleshoot-walkthrough2-datasets-with-errors]: ./media/data-factory-troubleshoot/Walkthrough2DataSetsWithErrors.png

[image-data-factory-troubleshoot-walkthrough2-table-with-problem-slices]: ./media/data-factory-troubleshoot/Walkthrough2TableProblemSlices.png

[image-data-factory-troubleshoot-walkthrough2-slice-activity-runs]: ./media/data-factory-troubleshoot/Walkthrough2DataSliceActivityRuns.png

[image-data-factory-troubleshoot-activity-run-details]: ./media/data-factory-troubleshoot/Walkthrough2ActivityRunDetails.png

<!--HONumber=46--> 
