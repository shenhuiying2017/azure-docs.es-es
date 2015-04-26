<properties title="Azure Stream Analytics monitor and manage jobs using Azure PowerShell" pageTitle="Análisis de transmisiones supervisa y administra trabajos mediante PowerShell | Azure" description="Aprenda a usar los cmdlets de PowerShell de Azure para supervisar y administrar los trabajos de análisis de secuencia" metaKeywords="" services="stream-analytics" solutions="" documentationCenter="" authors="jgao" videoId="" scriptId="" manager="paulettm" editor="cgronlun"/>

<tags ms.service="stream-analytics" ms.devlang="na" ms.topic="article" ms.tgt_pltfrm="na" ms.workload="data-services" ms.date="12/9/2014" ms.author="jgao" />


#Supervisar y administrar trabajos de Análisis de transmisiones con Azure PowerShell

Aprenda a administrar los recursos de Análisis de transmisiones de Azure mediante Azure PowerShell.

##En este artículo

- [Requisitos previos](#prerequisites)
- [Cmdlets de PowerShell de Análisis de transmisiones](#cmdlets)
	- [Get-AzureStreamAnalyticsJob](#Get-AzureStreamAnalyticsJob)
	- [Get-AzureStreamAnalyticsInput](#Get-AzureStreamAnalyticsInput)
	- [Get-AzureStreamAnalyticsOutput](#Get-AzureStreamAnalyticsOutput)
	- [Get-AzureStreamAnalyticsQuota](#Get-AzureStreamAnalyticsQuota)
	- [Get-AzureStreamAnalyticsTransformation](#Get-AzureStreamAnalyticsTransformation)
	- [New-AzureStreamAnalyticsInput](#New-AzureStreamAnalyticsInput)
	- [New-AzureStreamAnalyticsJob](#New-AzureStreamAnalyticsJob)
	- [New-AzureStreamAnalyticsOutput](#New-AzureStreamAnalyticsOutput)
	- [New-AzureStreamAnalyticsTransformation](#New-AzureStreamAnalyticsTransformation)
	- [Remove-AzureStreamAnalyticsInput](#Remove-AzureStreamAnalyticsInput)
	- [Remove-AzureStreamAnalyticsJob](#Remove-AzureStreamAnalyticsJob)
	- [Remove-AzureStreamAnalyticsOutput](#Remove-AzureStreamAnalyticsOutput)
	- [Start-AzureStreamAnalyticsJob](#Start-AzureStreamAnalyticsJob)
	- [Stop-AzureStreamAnalyticsJob](#Stop-AzureStreamAnalyticsJob)
	- [Test-AzureStreamAnalyticsInput](#Test-AzureStreamAnalyticsInput)
	- [Test-AzureStreamAnalyticsOutput](#Test-AzureStreamAnalyticsOutput)
- [Consulte también](#seealso)

## <a name="prerequisites"></a>Requisitos previos para ejecutar los cmdlets de PowerShell de Análisis de transmisiones

1.	Instalar y configurar Azure PowerShell

	Siga las instrucciones en [Instalación y configuración de Azure PowerShell][powershell-install] para instalar Azure PowerShell.

2.	Configurar el modo Azure

	Después de instalar Azure PowerShell, ejecute el cmdlet [Switch-AzureMode][msdn-switch-azuremode] para establecer el modo de Azure adecuado para tener acceso a los cmdlets de Análisis de transmisiones:

		Switch-AzureMode AzureResourceManager

>[WACOM.NOTE] Hay una limitación temporal en la cual los trabajos de Análisis de transmisiones creados a través de Azure PowerShell no tienen habilitada la supervisión.  Para solucionar este problema, vaya a la página Seguimiento del trabajo en el Portal de Azure y haga clic en el botón "Activar".  

##<a name="cmdlets"></a>Cmdlets de PowerShell de Análisis de transmisiones
En la tabla siguiente se enumeran los cmdlets que puede usar para supervisar y administrar los trabajos de Análisis de transmisiones de Azure con Azure PowerShell.

###<a name="Get-AzureStreamAnalyticsJob"></a>Get-AzureStreamAnalyticsJob
Enumera todos los trabajos de Análisis de transmisiones definidos en la suscripción de Azure o en el grupo de recursos especificado, u obtiene información del trabajo sobre un trabajo específico en un grupo de recursos.

**Ejemplo 1**

	Get-AzureStreamAnalyticsJob

Este comando devuelve información acerca de todos los trabajos de Análisis de transmisiones en la suscripción de Azure.

**Ejemplo 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US 
Este comando devuelve información acerca de todos los trabajos de Análisis de transmisiones en el grupo de recursos StreamAnalytics-Default-West-US.

**Ejemplo 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob
Este comando devuelve información sobre el trabajo de Análisis de transmisiones StreamingJob en el grupo de recursos StreamAnalytics-Default-West-US.

###<a name="Get-AzureStreamAnalyticsInput"></a>Get-AzureStreamAnalyticsInput
Enumera todas las entradas que se definen en un trabajo de Análisis de transmisiones u obtiene información sobre una entrada específica.

**Ejemplo 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob

Este comando devuelve información acerca de todas las entradas definidas en el trabajo StreamingJob.

**Ejemplo 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Este comando devuelve información acerca de la entrada denominada EntryStream definida en el trabajo StreamingJob.

###<a name="Get-AzureStreamAnalyticsOutput"></a>Get-AzureStreamAnalyticsOutput
Enumera todos los resultados que se definen en un trabajo de Análisis de transmisiones especificado u obtiene información sobre un resultado concreto.

**Ejemplo 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob
Este comando devuelve información sobre los resultados definidos en el trabajo StreamingJob.

**Ejemplo 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Este comando devuelve información sobre el resultado denominado Output definido en el trabajo StreamingJob.

###<a name="Get-AzureStreamAnalyticsQuota"></a>Get-AzureStreamAnalyticsQuota
Obtiene información sobre la cuota de la unidad de streaming de una región determinada.

**Ejemplo 1**

	Get-AzureStreamAnalyticsQuota -Location "West US" 
Este comando devuelve información sobre la cuota de la unidad de streaming y uso en la región Oeste de EE.UU.

###<a name="Get-AzureStreamAnalyticsTransformation"></a>Get-AzureStreamAnalyticsTransformation
Obtiene información sobre una transformación específica definida en el trabajo de Análisis de transmisiones.

**Ejemplo 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name StreamingJob
Este comando devuelve información acerca de la transformación denominada StreamingJob en el trabajo StreamingJob.

###<a name="New-AzureStreamAnalyticsInput"></a>New-AzureStreamAnalyticsInput
Crea una nueva entrada de un trabajo de Análisis de transmisiones o actualiza una entrada existente especificada.
  
El nombre de la entrada puede especificarse en el archivo .JSON o en la línea de comandos.  Si se especifican ambos, el nombre de la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica una entrada que ya existe y no se especifica el parámetro -Force, el cmdlet le preguntará si quiere reemplazar la entrada existente o no.

Si especifica el parámetro -Force y especifica el nombre de una entrada existente, la entrada se reemplazará sin pedir confirmación.

**Ejemplo 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" 
Este comando crea una nueva entrada desde el archivo Input.json.  Si ya se ha definido una entrada existente con el nombre especificado en el archivo de definición de entrada, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream
Este comando crea una nueva entrada en el trabajo denominada EntryStream.  Si ya se ha definido una entrada existente con este nombre, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -File "C:\Input.json" -Name EntryStream -Force
Este comando reemplaza la definición del origen de entrada existente denominado EntryStream con la definición desde el archivo.

###<a name="New-AzureStreamAnalyticsJob"></a>New-AzureStreamAnalyticsJob
Crea un nuevo trabajo de Análisis de transmisiones en Microsoft Azure o actualiza la definición de un trabajo existente especificado.

Puede especificarse el nombre del trabajo en el archivo .JSON o en la línea de comandos.  Si se especifican ambos, el nombre de la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica un nombre de trabajo que ya existe y no se especifica el parámetro -Force, el cmdlet le preguntará si quiere reemplazar el trabajo existente o no.

Si especifica el parámetro -Force y especifica un nombre de trabajo existente, se reemplazará la definición del trabajo sin confirmación.

**Ejemplo 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" 
Este comando crea un nuevo trabajo a partir de la definición de JobDefinition.json.  Si ya se ha definido un trabajo existente con el nombre especificado en el archivo de definición de trabajo, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\JobDefinition.json" -Name StreamingJob -Force
Este comando reemplaza la definición del trabajo para StreamingJob.

###<a name="New-AzureStreamAnalyticsOutput"></a>New-AzureStreamAnalyticsOutput
Crea un nuevo resultado en un trabajo de Análisis de transmisiones o actualiza un resultado existente.  

Puede especificarse el nombre del resultado en el archivo .JSON o en la línea de comandos.  Si se especifican ambos, el nombre de la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica un resultado que ya existe y no se especifica el parámetro -Force, el cmdlet le preguntará si quiere reemplazar el resultado existente o no.

Si especifica el parámetro -Force y especifica el nombre de un resultado existente, el resultado se reemplazará sin pedir confirmación.

**Ejemplo 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output
Este comando crea un nuevo resultado denominado "output" en el trabajo StreamingJob.  Si ya se ha definido un resultado existente con este nombre, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Output.json" -JobName StreamingJob -Name output -Force
Este comando reemplaza la definición de "output" en el trabajo StreamingJob.

###<a name="New-AzureStreamAnalyticsTransformation"></a>New-AzureStreamAnalyticsTransformation
Crea una nueva transformación en un trabajo de Análisis de transmisiones o actualiza la transformación existente.
  
Puede especificarse el nombre de la transformación en el archivo .JSON o en la línea de comandos.  Si se especifican ambos, el nombre de la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica una transformación que ya existe y no se especifica el parámetro -Force, el cmdlet le preguntará si quiere reemplazar la transformación existente o no.

Si especifica el parámetro -Force y especifica el nombre de una transformación existente, la transformación se reemplazará sin pedir confirmación.

**Ejemplo 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform
Este comando crea una nueva transformación denominada StreamingJobTransform en el trabajo StreamingJob.  Si ya hay definida una transformación existente con este nombre, el cmdlet le preguntará si quiere reemplazarla o no.

**Ejemplo 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-West-US -File "C:\Transformation.json" -JobName StreamingJob -Name StreamingJobTransform -Force
 Este comando reemplaza la definición de StreamingJobTransform en el trabajo StreamingJob.

###<a name="Remove-AzureStreamAnalyticsInput"></a>Remove-AzureStreamAnalyticsInput
Elimina de forma asincrónica una entrada específica desde un trabajo de Análisis de transmisiones en Microsoft Azure.  
Si especifica el parámetro -Force, la entrada se eliminará sin confirmación.

**Ejemplo 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EventStream
Este comando elimina la entrada EventStream en el trabajo StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsJob"></a>Remove-AzureStreamAnalyticsJob
Elimina de forma asincrónica un trabajo específico de Análisis de transmisiones en Microsoft Azure.  
Si especifica el parámetro -Force, el trabajo se eliminará sin confirmación.

**Ejemplo 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Este comando elimina el trabajo StreamingJob.  

###<a name="Remove-AzureStreamAnalyticsOutput"></a>Remove-AzureStreamAnalyticsOutput
Elimina de forma asincrónica un resultado concreto de un trabajo de Análisis de transmisiones en Microsoft Azure.  
Si especifica el parámetro -Force, el resultado se eliminará sin confirmación.

**Ejemplo 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Este comando elimina el resultado Output en el trabajo StreamingJob.  

###<a name="Start-AzureStreamAnalyticsJob"></a>Start-AzureStreamAnalyticsJob
Implementa e inicia un trabajo de Análisis de transmisiones de Microsoft Azure de forma asincrónica.

**Ejemplo 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Este comando inicia el trabajo StreamingJob.  

###<a name="Stop-AzureStreamAnalyticsJob"></a>Stop-AzureStreamAnalyticsJob
Detiene la ejecución de un trabajo de Análisis de transmisiones en Microsoft Azure y desasigna recursos que se usaban de forma asincrónica. La definición de trabajo y metadatos seguirá estando disponible en su suscripción a través del Portal de Azure y la API de Administración, para que el trabajo se pueda editar y reiniciar. No se realizará ningún cargo por un trabajo en estado Detenido.

**Ejemplo 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-West-US -Name StreamingJob 
Este comando detiene el trabajo StreamingJob.  

###<a name="Test-AzureStreamAnalyticsInput"></a>Test-AzureStreamAnalyticsInput
Comprueba la capacidad de Análisis de transmisiones para conectarse a una entrada especificada.

**Ejemplo 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name EntryStream
Comprueba el estado de conexión de la entrada EntryStream en StreamingJob.  

###<a name="Test-AzureStreamAnalyticsOutput"></a>Test-AzureStreamAnalyticsOutput
Comprueba la capacidad de Análisis de transmisiones para conectarse a una salida especificada.

**Ejemplo 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-West-US -JobName StreamingJob -Name Output
Prueba el estado de conexión del resultado Output en StreamingJob.  


##<a name="seealso"></a>Otras referencias

- [Introducción a Análisis de transmisiones de Azure][stream.analytics.introduction]
- [Empezar a trabajar con Análisis de transmisiones][stream.analytics.get.started]
- [Límites de la versión de vista previa de Análisis de transmisiones][stream.analytics.limitations]
- [Guía para desarrolladores para Análisis de transmisiones][stream.analytics.developer.guide]
- [Referencia del lenguaje de consulta para Análisis de transmisiones][stream.analytics.query.language.reference]
- [Referencia de API de REST para Análisis de transmisiones][stream.analytics.rest.api.reference]
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/es-es/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/es-es/documentation/articles/install-configure-powershell/


[stream-analytics-introduction]: ../
[stream-analytics-get-started]
[stream-analytics-limitations]
[stream-analytics-developer-guide]
[stream-analytics-query-language-reference]
[stream-analytics-rest-api-reference]
 


[stream.analytics.introduction]: ../stream-analytics-introduction/
[stream.analytics.get.started]: ../stream-analytics-get-started/
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide/
[stream.analytics.scale.jobs]: ../stream-analytics-scale-jobs/
[stream.analytics.limitations]: ../stream-analytics-limitations/
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301

<!--HONumber=35.2-->
