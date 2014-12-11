<properties title="Monitor and manage Azure Data Factory using Azure PowerShell" pageTitle="Supervisión y administración de la Factoría de datos de Azure mediante PowerShell de Azure" description="Learn how to use Azure PowerShell to monitor and manage Azure data factories you have created." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="spelluru" />

# Supervisión y administración de la Factoría de datos de Azure mediante PowerShell de Azure
La siguiente tabla enumera los cmdlets que puede utilizar para supervisar y administrar las factorías de datos de Azure mediante el uso de PowerShell de Azure. 

> [WACOM.NOTE] Consulte [Referencia de cmdlets de factoría de datos][cmdlet-reference] para obtener la documentación completa sobre los cmdlets de la factoría de datos. 


- [Get-AzureDataFactory](#get-azuredatafactory)
- [Get-AzureDataFactoryLinkedService](#get-azuredatafactorylinkedservice)
- [Get-AzureDataFactoryTable](#get-azuredatafactorytable)
- [Get-AzureDataFactoryPipeline](#get-azuredatafactorypipeline)
- [Get-AzureDataFactorySlice](#get-azuredatafactoryslice)
- [Get-AzureDataFactoryRun](#get-azuredatafactoryrun)
- [Save-AzureDataFactoryLog](#save-azuredatafactorylog)
- [Get-AzureDataFactoryGateway](#get-azuredatafactorygateway)
- [Set-AzureDataFactoryPipelineActivePeriod](#set-azuredatafactorypipelineactiveperiod)
- [Set-AzureDataFactorySliceStatus](#set-azuredatafactoryslicestatus)
- [Suspend-AzureDataFactoryPipeline](#suspend-azuredatafactorypipeline)
- [Resume-AzureDataFactoryPipeline](#resume-azuredatafactorypipeline)


##<a name="get-azuredatafactory"></a>Get-AzureDataFactory
Obtiene la información sobre una factoría de datos específica o sobre todas las factorías de datos de una suscripción de Azure dentro del grupo de recursos especificado.
 
###Ejemplo 1

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup

Este comando devuelve todas las factorías de datos del grupo de recursos ADFTutorialResourceGroup.
 
###Ejemplo 2

    Get-AzureDataFactory -ResourceGroupName ADFTutorialResourceGroup -Name ADFTutorialDataFactory

Este comando devuelve información sobre la factoría de datos ADFTutorialDataFactory del grupo de recursos ADFTutorialResourceGroup. 

## <a name="get-azuredatafactorylinkedservice"></a> Get-AzureDataFactoryLinkedService ##
El cmdlet Get-AzureDataFactoryLinkedService obtiene información sobre un servicio vinculado específico o todos los servicios vinculados de una factoría de datos de Azure.

### Ejemplo 1 ###

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
 
Este comando devuelve información sobre todos los servicios vinculados de la factoría de datos de Azure ADFTutorialDataFactory.


Puede usar el parámetro -DataFactory en lugar de usar los parámetros DataFactoryName y ResourceGroupName. Esto le ayuda a escribir los nombres del grupo de recursos y de la factoría una sola vez y usar el objeto Factoría de datos como parámetro para todos los cmdlets que toman ResourceGroupName y DataFactoryName como parámetros.

    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df 

### Ejemplo 2

    Get-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name MyBlobStore

Este comando devuelve información sobre el servicio vinculado MyBlobStore de la factoría de datos de Azure factoryADFTutorialDataFactory. 

Puede usar el parámetro DataFactory en lugar de los parámetros -ResourceGroup y -DataFactoryName tal como se muestra a continuación: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryLinkedService -DataFactory $df -Name MyBlobStore


## <a name="get-azuredatafactorytable"></a> Get-AzureDataFactoryTable
El cmdlet Get-AzureDataFactoryTable obtiene información sobre una tabla específica o todas las tablas de una factoría de datos de Azure. 

### Ejemplo 1

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Este comando devuelve información sobre todas las tablas de la factoría de datos de Azure ADFTutorialDataFactory.

Puede usar el parámetro DataFactory en lugar de los parámetros -ResourceGroup y -DataFactoryName tal como se muestra a continuación: 


    $df = Get-AzureDataFactory -ResourceGroup ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
	
	Get-AzureDataFactoryTable -DataFactory $df

### Ejemplo 2

    Get-AzureDataFactoryTable -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name EmpTableFromBlob

Este comando devuelve información sobre la tabla EmpTableFromBlob de la factoría de datos de Azure ADFTutorialDataFactory.



## <a name="get-azuredatafactorypipeline"></a>Get-AzureDataFactoryPipeline
El cmdlet Get-AzureDataFactoryPipeline obtiene información sobre una canalización específica o todas las canalizaciones de una factoría de datos de Azure.

### Ejemplo 1

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory

Este comando devuelve información sobre todas las canalizaciones de la factoría de datos de Azure ADFTutorialDataFactory.

### Ejemplo 2

    Get-AzureDataFactoryPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialPipeline

Obtiene información sobre la canalización ADFTutorialPipeline de la factoría de datos de Azure ADFTutorialDataFactory.

## <a name="get-azuredatafactoryslice"> </a> Get-AzureDataFactorySlice
El cmdlet Get-AzureDataFactorySlice obtiene todos los segmentos de una tabla de una factoría de datos de Azure que se producen antes de StartDateTime y despúes de EndDateTime. El segmento de datos con el estado Listo está listo para su consumo por segmentos dependientes.

En la tabla siguiente se enumeran todos los estados de un sector y sus descripciones.

<table border="1">	
	<tr>
		<th align="left">Status</th>
		<th align="left">Descripción</th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>El procesamiento de datos no se ha iniciado aún.</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>El procesamiento de datos está en curso.</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>El procesamiento de datos se ha completado y el segmento de datos está listo.</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>Error de ejecución de la ejecución que produce el segmento.</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Se omite el procesamiento del segmento.</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Reintento de la ejecución que produce el segmento.</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Se agotó el tiempo de espera del procesamiento de datos del sector.</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>El segmento de datos está esperando la validación contra las directivas de validación antes de procesarse.</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Reintento de la validación del segmento.</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Error de validación del segmento.</td>
	</tr>

	<tr>
		<td>LongRetry</td>
		<td>Un segmento estará en este estado si se especifica LongRetry en la tabla JSON y los reintentos periódicos del segmento han dado error.</td>
	</tr>

	<tr>
		<td>ValidationInProgress</td>
		<td>Se está realizando la validación del segmento (según las directivas definidas en la tabla JSON).</td>
	</tr>

</table>

Para cada uno de los segmentos, puede explorar en profundidad y ver más información sobre la ejecución que está produciendo el segmento mediante el uso de los cmdlets Get-AzureDataFactoryRun y Save-AzureDataFactoryLog.

### Ejemplo

    Get-AzureDataFactorySlice -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -StartDateTime 2014-05-20T10:00:00

Este comando obtiene todos los sectores de la tabla EmpSQLTable de la factoría de datos de Azure ADFTutorialDataFactory producidos después de 2014-05-20T10:00:00 (GTM). Reemplace la fecha y hora por la fecha y hora de inicio que ha especificado cuando ejecutó Set-AzureDataFactoryPipelineActivePeriod.

## <a name="get-azuredatafactoryrun"></a> Get-AzureDataFactoryRun

El cmdlet Get-AzureDataFactoryRun obtiene todas las ejecuciones de un segmento de datos de una tabla de una factoría de datos de Azure.  Una tabla de una factoría de datos de Azure se compone de segmentos sobre el eje de tiempo. El ancho de un segmento se determina por la programación: cada hora o diariamente. La ejecución es una unidad de procesamiento de un segmento. Podría haber una o varias ejecuciones de un segmento en caso de reintentos o si vuelve a ejecutar el segmento en caso de error. Un segmento se identifica mediante su hora de inicio. Por lo tanto, para el cmdlet Get-AzureDataFactoryRun, deberá pasar la hora de inicio del segmento de los resultados del cmdlet Get-AzureDataFactorySlice.

Por ejemplo, para obtener una ejecución para el siguiente segmento, utilice 2015-04-02T20:00:00. 

    ResourceGroupName  	: ADFTutorialResourceGroup
    DataFactoryName 	: ADFTutorialDataFactory
    TableName 			: EmpSQLTable
    Start 				: 5/2/2014 8:00:00 PM
    End 				: 5/3/2014 8:00:00 PM
    RetryCount 			: 0
    Status 				: Ready
    LatencyStatus 		:



### Ejemplo

    Get-AzureDataFactoryRun -DataFactoryName ADFTutorialDataFactory -TableName EmpSQLTable -ResourceGroupName ADFTutorialResourceGroup -StartDateTime 2014-05-21T16:00:00

Este comando obtiene todas las ejecuciones de los segmentos de la tabla EmpSQLTable de la factoría de datos de Azure ADFTutorialDataFactory desde 4 p.m. GMT de 21/05/2014.

## <a name="save-azuredatafactorylog"></a> Save-AzureDataFactoryLog
El cmdlet Save-AzureDataFactoryLog descarga los archivos de registro asociados con el procesamiento de HDInsight de Azure de proyectos Pig o Hive, o para actividades personalizadas, en el disco duro local. Primero ejecute el cmdlet Get-AzureDataFactoryRun para obtener un Id. para una ejecución de actividad de un segmento de datos y, a continuación, utilice ese Id. para recuperar los archivos de registro del almacenamiento de objetos grandes binarios (BLOB) asociados con el clúster de HDInsight. 

Si no especifica el parámetro **-DownloadLogs**, el cmdlet solo devuelve la ubicación de los archivos de registro. 

Si especifica el parámetro **-DownloadLogs** sin especificar un directorio de salida (el parámetro **-Output **), los archivos de registro se descargan en la carpeta de **documentos** predeterminada. 

Si especifica el parámetro **-DownloadLogs** junto con una carpeta de salida (**-Output**), los archivos de registro se descargan en la carpeta especificada. 


### Ejemplo 1
Este comando guarda los archivos de registro de la ejecución de actividad con el Id. 841b77c9-d56c-48d1-99a3-8c16c3e77d39 cuando la actividad pertenece a una canalización de la factoría de datos denominada LogProcessingFactory en el grupo de recursos denominado ADF. Los archivos de registro se guardan en la carpeta C:\Test. 

	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs -Output "C:\Test"
 

### Ejemplo 2
Este comando guarda los archivos de registro en la carpeta Documentos (predeterminada).


	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39" -DownloadLogs
 

### Ejemplo 3
Este comando devuelve la ubicación de los archivos de registro. Tenga en cuenta que no se especifica el parámetro - DownloadLogs. 
  
	Save-AzureDataFactoryLog -ResourceGroupName "ADF" -DataFactoryName "LogProcessingFactory" -Id "841b77c9-d56c-48d1-99a3-8c16c3e77d39"
 



## <a name="get-azuredatafactorygateway"></a> Get-AzureDataFactoryGateway
El cmdlet Get-AzureDataFactoryGateway obtiene información sobre una puerta de enlace específica o todas las puertas de enlace de una factoría de datos de Azure. Deberá instalar una puerta de enlace en el equipo local para poder agregar un servidor de SQL Server local como un servicio vinculado a una factoría de datos.

### Ejemplo 1
    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory
Este comando devuelve información sobre todas las puertas de enlace de la factoría de datos de Azure ADFTutorialDataFactory.

### Ejemplo 2

    Get-AzureDataFactoryGateway -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -Name ADFTutorialGateway

Este comando devuelve información sobre la puerta de enlace ADFTutorialGateway de la factoría de datos de Azure ADFTutorialDataFactory.

## <a name="set-azuredatafactorypipelineactiveperiod"></a> Set-AzureDataFactoryPipelineActivePeriod
Este cmdlet establece el período activo de los segmentos de datos que son procesados por la canalización. Si utiliza Set-AzureDataFactorySliceStatus, asegúrese de que la fecha de inicio y de finalización del segmento se encuentran en el período activo de la canalización.

Una vez creadas las canalizaciones, puede especificar la duración en la que se producirá el procesamiento de datos. Al especificar el período activo de un proceso, está definiendo el tiempo en el que se procesarán los segmentos de datos basándose en las propiedades de disponibilidad que se han definido para cada tabla ADF.

### Ejemplo

    Set-AzureDataFactoryPipelineActivePeriod  -Name ADFTutorialPipeline -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-05-21T16:00:00 -EndDateTime 2014-05-22T16:00:00

Este comando establece el período activo de los segmentos de datos que son procesados por la canalización ADFTutoiralPipeline en un período comprendido entre 21/5/2014 4 p.m. GMT y 22/5/2014 4 p.m. GMT.

## <a name="set-azuredatafactoryslicestatus"></a> Set-AzureDataFactorySliceStatus
Establece el estado de un segmento de una tabla. La fecha de inicio y de finalización del segmento deben estar en el período activo de la canalización.

### Valores admitidos para el estado
Cada segmento de datos de una tabla pasa por diversas etapas. Estas etapas son ligeramente diferentes en función de si se especifican directivas de validación.


- Si no se especifican  directivas de validación: PendingExecution -> InProgress -> Ready
- Si se especifican directivas de validación: PendingExecution -> Pending Validation -> InProgress -> Ready

En la tabla siguiente se proporcionan descripciones de los posibles estados de un segmento y se indica si el estado se puede establecer utilizando Set-AzureDataFactorySliceStatus o no.

<table border="1">	
	<tr>
		<th>Status</th>
		<th>Descripción</th>
		<th>Esto se puede establecer mediante el cmdlet></th>
	</tr>	

	<tr>
		<td>PendingExecution</td>
		<td>El procesamiento de datos no se ha iniciado aún.</td>
		<td>Y</td>
	</tr>	

	<tr>
		<td>InProgress</td>
		<td>El procesamiento de datos está en curso.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Ready</td>
		<td>El procesamiento de datos se ha completado y el segmento de datos está listo.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Failed</td>
		<td>Error de ejecución de la ejecución que produce el segmento.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Skip</td>
		<td>Se omite el procesamiento del segmento.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry</td>
		<td>Reintento de la ejecución que produce el segmento.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Timed Out</td>
		<td>Se agotó el tiempo de espera del procesamiento de datos.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>PendingValidation</td>
		<td>El segmento de datos está esperando la validación contra las directivas de validación antes de procesarse.</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>Retry Validation</td>
		<td>Reintento de la validación del segmento.</td>
		<td>N</td>
	</tr>

	<tr>
		<td>Failed Validation</td>
		<td>Error de validación del segmento.</td>
		<td>N</td>
	</tr>
	</table>


### Valores admitidos: tipo de actualización
En cada tabla de una factoría de datos de Azure, al establecer el estado de un segmento, necesitará especificar si la actualización del estado se aplica solamente a la tabla o si las actualizaciones de estado se propagan a todos los segmentos afectados.

<table border="1">	
	<tr>
		<th>Tipo de actualización</th>
		<th>Descripción</th>
		<th>Esto se puede establecer mediante el cmdlet</th>
	</tr>

	<tr>
		<td>Individual</td>
		<td>Establece el estado de cada segmento de la tabla en el intervalo de tiempo especificado</td>
		<td>Y</td>
	</tr>

	<tr>
		<td>UpstreamInPipeline</td>
		<td>Establece el estado de cada segmento de la tabla y de todas las tablas dependientes (ascendentes) que se utilizan como tablas de entrada para las actividades de la canalización.</td>
		<td>Y</td>
	</tr>

</table>
## <a name="suspend-azuredatafactorypipeline"></a> Suspend-AzureDataFactoryPipeline
El cmdlet Suspend-AzureDataFactoryPipeline suspende la canalización especificada de una factoría de datos de Azure. Puede reanudar la canalización más adelante mediante el cmdlet Resume-AzureDataFactoryPipeline.

### Ejemplo

    Suspend-AzureDataFactoryPipeline -Name ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Este comando suspende la canalización ADFTutorialPipeline de la factoría de datos de Azure ADFTutorialDataFactory.

## <a name="resume-azuredatafactorypipeline"></a> Resume-AzureDataFactoryPipeline
El cmdlet Resume-AzureDataFactoryPipeline reanuda la canalización especificada que está actualmente en estado suspendido en una factoría de datos de Azure. 

### Ejemplo

    Resume-AzureDataFactoryPipeline ADFTutorialPipeline -DataFactoryName ADFTutorialDataFactory -ResourceGroupName ADFTutorialResourceGroup

Este comando reanuda la canalización ADFTutorialPipeline de la factoría de datos de Azure ADFTutorialDataFactory que se suspendió antes mediante el comando Suspend-AzureDataFactoryPipeline.

## Otras referencias

Artículo | Descripción
------ | ---------------
[Supervisión y administración de la Factoría de datos de Azure mediante el Portal de vista previa de Azure][monitor-manage-using-portal] | Este artículo describe cómo supervisar y administrar una factoría de datos de Azure mediante el Portal de vista previa de Azure.
[Habilitación de las canalizaciones para que funcionen con datos locales][use-onpremises-datasources] | Este artículo incluye un tutorial que muestra cómo copiar datos desde una base de datos SQL Server local hasta un blob de Azure.
[Uso de Pig y Hive con la factoría de datos][use-pig-and-hive-with-data-factory] | Este artículo incluye un tutorial que muestra cómo usar la actividad de HDInsight para ejecutar un script de hive/pig con el fin de procesar datos de entrada para producir datos de salida. 
[Tutorial: Movimiento y procesamiento de los archivos de registro mediante la factoría de datos][adf-tutorial] | Este artículo proporciona un completo tutorial que muestra cómo implementar un escenario en tiempo real mediante el uso de la Factoría de datos de Azure para transformar los datos de los archivos de registro en información.
[Uso de actividades personalizadas en una factoría de datos][use-custom-activities] | Este artículo ofrece un tutorial con instrucciones paso a paso para crear una actividad personalizada y utilizarla en un proceso. 
[Solución de problemas de la factoría de datos][troubleshoot] | Este artículo describe cómo solucionar los problemas de la Factoría de datos de Azure.
[Referencia para desarrolladores de la Factoría de datos de Azure][developer-reference] | La referencia para desarrolladores incluye el contenido de referencia completo para cmdlets, scripts JSON, funciones, etc. 
[Referencia de cmdlets de Factoría de datos de Azure][cmdlet-reference] | Este contenido de referencia incluye detalles acerca de todos los **cmdlets de la factoría de datos**.

[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-portal]: ../data-factory-monitor-manage-using-management-portal

[troubleshoot]: ../data-factory-troubleshoot
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456
