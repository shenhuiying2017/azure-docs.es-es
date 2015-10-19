<properties 
	pageTitle="Supervisar y administrar los trabajos de Análisis de transmisiones con PowerShell | Microsoft Azure" 
	description="Aprenda a usar los cmdlets de Azure PowerShell para supervisar y administrar trabajos de Análisis de transmisiones." 
	keywords="azure powershell,azure powershell cmdlets,powershell command"	
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="10/05/2015" 
	ms.author="jeffstok"/>


# Supervisar y administrar los trabajos de Análisis de transmisiones con los cmdlets de Azure PowerShell

Aprenda a supervisar y administrar los recursos de Análisis de transmisiones con los cmdlets de Azure PowerShell que se encargan de ejecutar las tareas básicas de Análisis de transmisiones.


## Requisitos previos para ejecutar cmdlets de Azure PowerShell en Análisis de transmisiones

1.	Instale y configure Azure PowerShell.

	Siga las instrucciones de [Instalación y configuración de Azure PowerShell][powershell-install] para instalar Azure PowerShell.

	Para conectarse a su suscripción de Azure mediante el método de Azure Active Directory:

		Add-AzureAccount

	Para seleccionar su suscripción de Azure con el servicio Análisis de transmisiones de Azure habilitado, siga el siguiente método:

		Select-AzureSubscription


2.	Configuración del modo de Azure.

	Después de instalar Azure PowerShell, ejecute el cmdlet [Switch-AzureMode][msdn-switch-azuremode] para establecer el modo de Azure adecuado para acceder a los cmdlets de Análisis de transmisiones:

		Switch-AzureMode AzureResourceManager

> [AZURE.NOTE]Los trabajos de Análisis de transmisiones creados mediante programación no tienen la supervisión habilitada de forma predeterminada. Puede habilitar la supervisión manualmente en el Portal de Azure navegando hasta la página Supervisión del trabajo y haciendo clic en el botón Habilitar. También puede hacerlo mediante programación siguiendo los pasos que se encuentran en [Análisis de transmisiones de Azure - Supervisión de trabajos de Análisis de transmisiones mediante programación](stream-analytics-monitor-jobs.md)

## Cmdlets de PowerShell de Azure en Análisis de transmisiones
Se pueden usar los siguientes cmdlets de PowerShell de Azure para supervisar y administrar trabajos de Análisis de transmisiones de Azure.

### Get-AzureStreamAnalyticsJob
Muestra todos los trabajos de Análisis de transmisiones definidos en la suscripción de Azure o en el grupo de recursos especificado, u obtiene información del trabajo sobre un trabajo específico en un grupo de recursos.

**Ejemplo 1**

	Get-AzureStreamAnalyticsJob

Este comando de PowerShell devuelve información acerca de todos los trabajos de Análisis de transmisiones en la suscripción de Azure.

**Ejemplo 2**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US 
Este comando de PowerShell devuelve información sobre todos los trabajos de Análisis de transmisiones del grupo de recursos StreamAnalytics-Default-Central-US.

**Ejemplo 3**

	Get-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob
Este comando de PowerShell devuelve información sobre el trabajo StreamingJob de Análisis de transmisiones del grupo de recursos StreamAnalytics-Default-Central-US.

### Get-AzureStreamAnalyticsInput
Muestra todas las entradas que se definen en un determinado trabajo de Análisis de transmisiones u obtiene información sobre una entrada concreta.

**Ejemplo 1**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob

Este comando de PowerShell devuelve información sobre todas las entradas que se definen en el trabajo StreamingJob.

**Ejemplo 2**

	Get-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name EntryStream
Este comando de PowerShell devuelve información sobre la entrada denominada EntryStream que se define en el trabajo StreamingJob.

### Get-AzureStreamAnalyticsOutput
Muestra todas las salidas que se definen en un determinado trabajo de Análisis de transmisiones u obtiene información sobre una salida concreta.

**Ejemplo 1**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob
Este comando de PowerShell devuelve información sobre las salidas que se definen en el trabajo StreamingJob.

**Ejemplo 2**

	Get-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name Output
Este comando de PowerShell devuelve información sobre la salida denominada Output que se define en el trabajo StreamingJob.

### Get-AzureStreamAnalyticsQuota
Obtiene información sobre la cuota de unidades de streaming de una región determinada.

**Ejemplo 1**

	Get-AzureStreamAnalyticsQuota –Location "Central US" 
Este comando de PowerShell devuelve información sobre la cuota de unidades de streaming y su uso en la región Centro de EE. UU.

### Get-AzureStreamAnalyticsTransformation
Obtiene información sobre una transformación específica definida en un trabajo de Análisis de transmisiones.

**Ejemplo 1**

	Get-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –Name StreamingJob
Este comando de PowerShell devuelve información sobre la transformación denominada StreamingJob en el trabajo StreamingJob.

### New-AzureStreamAnalyticsInput
Crea otra entrada en un trabajo de Análisis de transmisiones o actualiza una determinada entrada existente.
  
El nombre de la entrada puede especificarse en el archivo .JSON o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica una entrada que ya existe y no especifica el parámetro –Force, el cmdlet le preguntará si quiere reemplazar la entrada existente.

Si especifica el parámetro –Force y el nombre de una entrada existente, la entrada se reemplazará sin pedir confirmación.

Para obtener información detallada sobre la estructura de archivos JSON y el contenido, consulte la sección [Creación de entrada (Análisis de transmisiones de Azure)][msdn-rest-api-create-stream-analytics-input] de la [Biblioteca de referencia de API de REST de administración de Análisis de transmisiones][stream.analytics.rest.api.reference].

**Ejemplo 1**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" 
Este comando de PowerShell crea una nueva entrada desde el archivo Input.json. Si ya se ha definido una entrada existente con el nombre especificado en el archivo de definición de entrada, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 2**
	
	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream
Este comando de PowerShell crea una entrada en el trabajo denominada EntryStream. Si ya se ha definido una entrada existente con este nombre, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 3**

	New-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US -JobName StreamingJob –File "C:\Input.json" –Name EntryStream -Force
Este comando de PowerShell reemplaza la definición del origen de entrada existente denominado EntryStream por la definición del archivo.

### New-AzureStreamAnalyticsJob
Crea un trabajo de Análisis de transmisiones en Microsoft Azure o actualiza la definición de un determinado trabajo existente.

El nombre del trabajo puede especificarse en el archivo .JSON o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica un nombre de trabajo que ya existe y no especifica el parámetro –Force, el cmdlet le preguntará si quiere reemplazar el trabajo existente.

Si especifica el parámetro –Force y el nombre de un trabajo existente, la definición del trabajo se reemplazará sin pedir confirmación.

Para obtener información detallada sobre la estructura de archivos JSON y el contenido, consulte la sección [Creación de trabajo de Análisis de transmisiones][msdn-rest-api-create-stream-analytics-job] de la [Biblioteca de referencia de API de REST de administración de Análisis de transmisiones][stream.analytics.rest.api.reference].

**Ejemplo 1**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" 
Este comando de PowerShell crea un nuevo trabajo a partir de la definición de JobDefinition.json. Si ya se ha definido un trabajo existente con el nombre especificado en el archivo de definición de trabajo, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 2**

	New-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\JobDefinition.json" –Name StreamingJob -Force
Este comando de PowerShell reemplaza la definición del trabajo de StreamingJob.

### New-AzureStreamAnalyticsOutput
Crea una salida en un trabajo de Análisis de transmisiones o actualiza una salida existente.

El nombre de la salida puede especificarse en el archivo .JSON o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica una salida que ya existe y no especifica el parámetro –Force, el cmdlet le preguntará si quiere reemplazar la salida existente.

Si especifica el parámetro –Force y el nombre de una salida existente, la salida se reemplazará sin pedir confirmación.

Para obtener información detallada sobre la estructura de archivos JSON y el contenido, consulte la sección [Creación de salida (Análisis de transmisiones de Azure)][msdn-rest-api-create-stream-analytics-output] de la [Biblioteca de referencia de API de REST de administración de Análisis de transmisiones][stream.analytics.rest.api.reference].

**Ejemplo 1**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output
Este comando de PowerShell crea una nueva salida denominada "output" en el trabajo StreamingJob. Si ya se ha definido un resultado existente con este nombre, el cmdlet le preguntará si quiere reemplazarlo o no.

**Ejemplo 2**

	New-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Output.json" –JobName StreamingJob –Name output -Force
Este comando de PowerShell reemplaza la definición de "output" en el trabajo StreamingJob.

### New-AzureStreamAnalyticsTransformation
Crea una transformación en un trabajo de Análisis de transmisiones o actualiza la transformación existente.
  
El nombre de la transformación puede especificarse en el archivo .JSON o en la línea de comandos. Si se especifican ambos, el nombre en la línea de comandos debe coincidir con el que aparece en el archivo.

Si especifica una transformación que ya existe y no se especifica el parámetro –Force, el cmdlet le preguntará si quiere reemplazar la transformación existente.

Si especifica el parámetro –Force y el nombre de una transformación existente, la transformación se reemplazará sin pedir confirmación.

Para obtener información detallada sobre la estructura de archivos JSON y el contenido, consulte la sección [Creación de transformación (Análisis de transmisiones de Azure)][msdn-rest-api-create-stream-analytics-transformation] de la [Biblioteca de referencia de API de REST de administración de Análisis de transmisiones][stream.analytics.rest.api.reference].

**Ejemplo 1**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform
Este comando de PowerShell crea una nueva transformación denominada StreamingJobTransform en el trabajo StreamingJob. Si ya hay definida una transformación existente con este nombre, el cmdlet le preguntará si quiere reemplazarla o no.

**Ejemplo 2**

	New-AzureStreamAnalyticsTransformation -ResourceGroupName StreamAnalytics-Default-Central-US –File "C:\Transformation.json" –JobName StreamingJob –Name StreamingJobTransform -Force
 Este comando de PowerShell reemplaza la definición de StreamingJobTransform en el trabajo StreamingJob.

### Remove-AzureStreamAnalyticsInput
Elimina de forma asincrónica una entrada específica desde un trabajo de Análisis de transmisiones en Microsoft Azure. Si especifica el parámetro –Force, la entrada se eliminará sin confirmación.

**Ejemplo 1**
	
	Remove-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EventStream
Este comando de PowerShell elimina la entrada EventStream en el trabajo StreamingJob.

### Remove-AzureStreamAnalyticsJob
Elimina de forma asincrónica un trabajo específico de Análisis de transmisiones en Microsoft Azure. Si especifica el parámetro –Force, el trabajo se eliminará sin confirmación.

**Ejemplo 1**

	Remove-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Este comando de PowerShell elimina el trabajo StreamingJob.

### Remove-AzureStreamAnalyticsOutput
Elimina de forma asincrónica un resultado concreto de un trabajo de Análisis de transmisiones en Microsoft Azure. Si especifica el parámetro –Force, la salida se eliminará sin confirmación.

**Ejemplo 1**

	Remove-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Este comando de PowerShell elimina la salida Output en el trabajo StreamingJob.

### Start-AzureStreamAnalyticsJob
Implementa e inicia un trabajo de Análisis de transmisiones de Microsoft Azure de forma asincrónica.

**Ejemplo 1**

	Start-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US -Name StreamingJob -OutputStartMode CustomTime -OutputStartTime 2012-12-12T12:12:12Z

Este comando de PowerShell inicia el trabajo StreamingJob con una hora de inicio de salida personalizada establecida en el 12 de diciembre de 2012, 12:12:12 UTC.


### Stop-AzureStreamAnalyticsJob
Detiene la ejecución de un trabajo de Análisis de transmisiones en Microsoft Azure y desasigna recursos que se usaban de forma asincrónica. La definición del trabajo y los metadatos seguirán estando disponible en su suscripción a través del Portal de Azure y de las API de administración, para que el trabajo se pueda editar y reiniciar. No se realizará ningún cobro por un trabajo en estado Detenido.

**Ejemplo 1**

	Stop-AzureStreamAnalyticsJob -ResourceGroupName StreamAnalytics-Default-Central-US –Name StreamingJob 
Este comando de PowerShell detiene el trabajo StreamingJob.

### Test-AzureStreamAnalyticsInput
Comprueba la capacidad de Análisis de transmisiones para conectarse a una entrada especificada.

**Ejemplo 1**

	Test-AzureStreamAnalyticsInput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name EntryStream
Este comando de PowerShell comprueba el estado de conexión de la entrada EntryStream en StreamingJob.

###Test-AzureStreamAnalyticsOutput
Comprueba la capacidad de Análisis de transmisiones para conectarse a una salida especificada.

**Ejemplo 1**

	Test-AzureStreamAnalyticsOutput -ResourceGroupName StreamAnalytics-Default-Central-US –JobName StreamingJob –Name Output
Este comando de PowerShell comprueba el estado de conexión de la salida Output en StreamingJob.

## Obtención de soporte técnico
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/ES-ES/home?forum=AzureStreamAnalytics).


## Pasos siguientes

- [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
- [Introducción al uso de Análisis de transmisiones de Azure](stream-analytics-get-started.md)
- [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
- [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referencia de API de REST de administración de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 



[msdn-switch-azuremode]: http://msdn.microsoft.com/library/dn722470.aspx
[powershell-install]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
[msdn-rest-api-create-stream-analytics-job]: https://msdn.microsoft.com/library/dn834994.aspx
[msdn-rest-api-create-stream-analytics-input]: https://msdn.microsoft.com/library/dn835010.aspx
[msdn-rest-api-create-stream-analytics-output]: https://msdn.microsoft.com/library/dn835015.aspx
[msdn-rest-api-create-stream-analytics-transformation]: https://msdn.microsoft.com/library/dn835007.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=Oct15_HO2-->