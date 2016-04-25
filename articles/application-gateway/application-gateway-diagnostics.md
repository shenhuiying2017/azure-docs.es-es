<properties 
   pageTitle="Supervisión de los registros de acceso y rendimiento para la Puerta de enlace de aplicaciones | Microsoft Azure"
   description="Obtenga información acerca de cómo habilitar y administrar registros de acceso y rendimiento para la Puerta de enlace de aplicaciones."
   services="application-gateway"
   documentationCenter="na"
   authors="amitsriva"
   manager="rossort"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="04/11/2016"
   ms.author="amitsriva" />

#Registro de diagnóstico para la Puerta de enlace de aplicaciones

Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas de Puertas de enlace de aplicaciones. Se puede acceder a algunos de estos registros a través del portal y se pueden extraer todos los registros desde un almacenamiento de blobs de Azure y verse en distintas herramientas, como Excel y PowerBI. Puede obtener más información acerca de los diferentes tipos de registros en la lista siguiente.

- **Registros de auditoría:** puede usar los [registros de auditoría de Azure](../azure-portal/insights-debugging-with-events.md) (anteriormente conocido como registros operativos) para ver todas las operaciones enviadas a sus suscripciones de Azure, así como su estado. Los registros de auditoría están habilitados de forma predeterminada y se pueden ver en el Portal de vista previa de Azure.
- **Registros de acceso:** puede utilizar este registro para ver el patrón de acceso de la Puerta de enlace de aplicaciones y analizar importante información, como la IP del autor de llamada, la URL solicitada, la latencia de respuesta, el código de retorno y los bytes de entrada y de salida. El registro de acceso se recopila cada 300 segundos. Este registro contiene un registro por cada instancia de la Puerta de enlace de aplicaciones. La instancia de la Puerta de enlace de aplicaciones puede identificarse por la propiedad 'instanceId'.
- **Registros de rendimiento:** puede utilizar este registro para ver el rendimiento de las instancias de la Puerta de enlace de aplicaciones. Este registro captura la información de rendimiento de cada instancia, incluida la cantidad total de solicitudes atendidas, el rendimiento en bytes, la cantidad de solicitudes con error y la cantidad de instancias de back-end completadas correcta e incorrectamente. El registro de rendimiento se recopila cada 60 segundos.

>[AZURE.WARNING] Los registros solo están disponibles para los recursos implementados en el modelo de implementación del Administrador de recursos. No puede usar los registros de recursos del modelo de implementación clásica. Para entender mejor los dos modelos, consulte el artículo [Descripción de la implementación del Administrador de recursos y la implementación clásica](../resource-manager-deployment-model.md).

##Habilitación del registro
El registro de auditoría se habilita automáticamente siempre para todos los recursos del Administrador de recursos. Debe habilitar el registro de acceso y rendimiento para iniciar la recopilación de los datos disponibles a través de esos registros. Para habilitar el registro, siga estos pasos.

1. Observe el identificador de recurso de la cuenta de almacenamiento, donde se almacenarán los datos de registro. Este tendrá el formato siguiente: /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name>. Es posible utilizar cualquier cuenta de almacenamiento de la suscripción. Puede usar el portal de vista previa para buscar esta información.
![Portal de vista previa: diagnóstico de la Puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics1.png)
 
2. Observe el identificador de recurso de la Puerta de enlace de aplicaciones para la que se está habilitando el registro. Este tendrá el formato siguiente: /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name>. Puede usar el portal de vista previa para buscar esta información.
![Portal de vista previa: diagnóstico de la Puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics2.png)

3. Habilite el registro de diagnóstico mediante el siguiente cmdlet de PowerShell.

		Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true 	

>[AZURE.INFORMATION] Los registros de auditoría no requieren una cuenta de almacenamiento separada. El uso del almacenamiento para el registro de acceso y rendimiento supondrá un costo adicional de servicio.


## Registro de auditoría
Azure genera este registro (anteriormente conocido como "registro operativo") de forma predeterminada. Los registros se conservan durante 90 días en el almacén de registros de eventos de Azure. Para obtener más información sobre estos registros, consulte el artículo [Visualización de eventos y registros de auditoría](../azure-portal/insights-debugging-with-events.md).

## Registro de acceso
Este registro solo se genera si lo habilitó para cada Puerta de enlace de aplicaciones, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Cada acceso de la Puerta de enlace de aplicaciones se registra en formato JSON, tal como se muestra a continuación.

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resoource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayAccess",
		"time": "2016-04-11T04:24:37Z",
		"category": "ApplicationGatewayAccessLog",
		"properties": {
			"instanceId":"ApplicationGatewayRole_IN_0",
			"clientIP":"37.186.113.170",
			"clientPort":"12345",
			"httpMethod":"HEAD",
			"requestUri":"/xyz/portal",
			"requestQuery":"",
			"userAgent":"-",
			"httpStatus":"200",
			"httpVersion":"HTTP/1.0",
			"receivedBytes":"27",
			"sentBytes":"202",
			"timeTaken":"359",
			"sslEnabled":"off"
		}
	}


## Registro de rendimiento
Este registro solo se genera si lo habilitó para cada Puerta de enlace de aplicaciones, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Se registran los datos siguientes:

	{
		"resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
		"operationName": "ApplicationGatewayPerformance",
		"time": "2016-04-09T00:00:00Z",
		"category": "ApplicationGatewayPerformanceLog",
		"properties": 
		{
			"instanceId":"ApplicationGatewayRole_IN_1",
			"healthyHostCount":"4",
			"unHealthyHostCount":"0",
			"requestCount":"185",
			"latency":"0",
			"failedRequestCount":"0",
			"throughput":"119427"
		}
	}

## Visualización y análisis del registro de auditoría
Puede ver y analizar los datos del registro de auditoría mediante el uso de cualquiera de los métodos siguientes:

- **Herramientas de Azure:** puede recuperar información de los registros de auditoría a través de Azure PowerShell, de la interfaz de la línea de comandos (CLI) de Azure, la API de REST de Azure o el Portal de vista previa de Azure. En el artículo [Operaciones de auditoría con el Administrador de recursos](../resource-group-audit.md) se detallan instrucciones paso a paso de cada método.
- **Power BI:** si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing), puede probarlo gratis. Con el [paquete de contenido de los registros de auditoría de Azure para Power BI](https://powerbi.microsoft.com/es-ES/documentation/powerbi-content-pack-azure-audit-logs/) puede analizar los datos con los paneles preconfigurados que puede usar tal cual o personalizarlos.

## Visualización y análisis del registro de eventos y de contadores 
Debe conectarse a la cuenta de almacenamiento y recuperar las entradas del registro de JSON para los registros de eventos y de contadores. Cuando descargue los archivos JSON, se pueden convertir a CSV y consultarlos en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

>[AZURE.TIP] Si está familiarizado con Visual Studio y con los conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [herramientas convertidoras de registros](https://github.com/Azure-Samples/networking-dotnet-log-converter), disponibles en Github.

## Pasos siguientes

- Entrada de blog [Visualize your Azure Audit Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar los registros de auditoría de Azure con Power BI).
- Entrada de blog [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) (Ver y analizar registros de auditoría de Azure en Power BI y más).

<!---HONumber=AcomDC_0413_2016-->