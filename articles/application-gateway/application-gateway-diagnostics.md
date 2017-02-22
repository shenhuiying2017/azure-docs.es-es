---
title: "Supervisión de los registros de acceso, rendimiento, estado de back-end y métricas de Application Gateway | Microsoft Docs"
description: "Obtenga información acerca de cómo habilitar y administrar registros de acceso y rendimiento para la Puerta de enlace de aplicaciones."
services: application-gateway
documentationcenter: na
author: amitsriva
manager: rossort
editor: tysonn
tags: azure-resource-manager
ms.assetid: 300628b8-8e3d-40ab-b294-3ecc5e48ef98
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: amitsriva
translationtype: Human Translation
ms.sourcegitcommit: d65b354bc972c8268f1b4f072843b5bf4977a7c4
ms.openlocfilehash: 2b37bf92ce8945996eb64477c28bea845b7df516


---
# <a name="backend-health-diagnostics-logging-and-metrics-for-application-gateway"></a>Estado de back-end, registro de diagnóstico y métricas de Application Gateway

Azure proporciona la funcionalidad de supervisar recursos con registros y métricas. Application Gateway proporciona estas funcionalidades con estado de back-end, registro y métricas.

[**Estado de back-end**](#backend-health): Application Gateway proporciona la funcionalidad de supervisar el estado de los servidores en los grupos de back-end a través del portal y de PowerShell. También se puede encontrar el estado de los grupos de back-end en los registros de diagnóstico de rendimiento.

[**Registro**](#enable-logging-with-powershell): permite que los datos de rendimiento, acceso y otros registros se guarden o consuman desde un recurso con fines de supervisión.

[**Métricas**](#metrics) : en estos momentos, Application Gateway tiene una métrica. Esta métrica mide el rendimiento de la puerta de enlace de aplicaciones en bytes por segundo.

## <a name="backend-health"></a>Estado de back-end

Application Gateway proporciona la funcionalidad de supervisar el estado de los miembros individuales de los grupos de back-end el portal, PowerShell y la CLI. En los registros de diagnóstico de rendimiento se puede encontrar un resumen de estado agregado de los grupos back-end. El informe de estado de back-end refleja el resultado del sondeo de estado de Application Gateway en las instancias de back-end. Cuando el sondeo es correcto y el back-end puede atender el tráfico, se considera que el estado es correcto; en caso contrario, se considera incorrecto.

> [!important]
> Si hay un grupo de seguridad de red en la subred de Application Gateway, se deberían abrir los intervalos de puertos 65503-65534 en las instancias de Application Gateway.

### <a name="view-backend-health-through-the-portal"></a>Visualización del estado de back-end a través del portal

No hay nada que deba hacerse para ver el estado de back-end. En una puerta de enlace de aplicaciones existente, vaya a **Supervisión** > **Backend health** (Estado de back-end). Cada miembro en el grupo de back-end se muestra en esta página (ya sea una NIC, IP o FQDN). Se muestran el nombre del grupo back-end, el puerto, la configuración de HTTP de back-end y el estado de mantenimiento. Los valores válidos para el estado de mantenimiento son "Correcto", "Incorrecto" y "Desconocido".

> [!WARNING]
> Si ve un estado de mantenimiento de back-end como **Desconocido**, asegúrese de que el acceso al back-end no está bloqueado por una regla de grupo de seguridad de red (NSG) o un DNS personalizado en la red virtual.

![estado de back-end][10]

### <a name="view-backend-health-with-powershell"></a>Visualización de estado de back-end con PowerShell

El estado de back-end también está disponible para que se pueda recuperar a través de PowerShell. El código de PowerShell siguiente muestra cómo extraer el estado de back-end con el cmdlet `Get-AzureRmApplicationGatewayBackendHealth`.

```powershell
Get-AzureRmApplicationGatewayBackendHealth -Name ApplicationGateway1 -ResourceGroupName Contoso
```

Se devuelven los resultados y se muestra un ejemplo de la respuesta en el siguiente fragmento de código.

```json
{
"BackendAddressPool": {
    "Id": "/subscriptions/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendAddressPools/appGatewayBackendPool"
},
"BackendHttpSettingsCollection": [
    {
    "BackendHttpSettings": {
        "Id": "/00000000-0000-0000-000000000000/resourceGroups/ContosoRG/providers/Microsoft.Network/applicationGateways/applicationGateway1/backendHttpSettingsCollection/appGatewayBackendHttpSettings"
    },
    "Servers": [
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        },
        {
        "Address": "hostname.westus.cloudapp.azure.com",
        "Health": "Healthy"
        }
    ]
    }
]
}
```

## <a name="diagnostic-logging"></a>Registro de diagnósticos

Puede usar diferentes tipos de registros en Azure para administrar y solucionar problemas de Application Gateway. Se puede acceder a algunos de estos registros a través del portal y se pueden extraer todos los registros desde un almacenamiento de blobs de Azure y verse en distintas herramientas, como [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md), Excel y PowerBI. Puede obtener más información sobre los diferentes tipos de registros en la lista siguiente:

* **Registros de actividades:** puede usar el [registro de actividades de Azure](../monitoring-and-diagnostics/insights-debugging-with-events.md) (anteriormente conocido como registros operativos y registros de auditoría) para ver todas las operaciones enviadas a sus suscripciones de Azure, así como su estado. Las entradas del registro de actividades están habilitadas de forma predeterminada y se pueden ver en Azure Portal.
* **Registros de acceso** : este registro se puede utilizar para ver el patrón de acceso de la puerta de enlace de aplicaciones y analizar información importante, como la IP del autor de llamada, la dirección URL solicitada, la latencia de respuesta, el código de retorno y los bytes de entrada y de salida. El registro de acceso se recopila cada 300 segundos. Este registro contiene un registro por cada instancia de Application Gateway. La instancia de Application Gateway puede identificarse por la propiedad 'instanceId'.
* **Registros de rendimiento** : este registro se puede utilizar para ver el rendimiento de las instancias de Application Gateway. Este registro captura la información de rendimiento de cada instancia, incluida la cantidad total de solicitudes atendidas, el rendimiento en bytes, la cantidad de solicitudes con error y la cantidad de instancias back-end completadas correcta e incorrectamente. El registro de rendimiento se recopila cada 60 segundos.
* **Registros de firewall** : puede utilizar este registro para ver las solicitudes que se registran con el modo de detección o prevención de una puerta de enlace de aplicaciones que está configurada con Web Application Firewall.

> [!WARNING]
> Los registros solo están disponibles para los recursos implementados en el modelo de implementación del Administrador de recursos. No puede usar los registros de recursos del modelo de implementación clásica. Para entender mejor los dos modelos, consulte el artículo [Descripción de la implementación del Administrador de recursos y la implementación clásica](../azure-resource-manager/resource-manager-deployment-model.md) .

Para los registros, se pueden elegir tres opciones distintas para almacenarlos.

* Cuenta de almacenamiento: cuentas que resultan especialmente útiles en registros cuando los registros se almacenan durante mucho tiempo y se revisan cuando sea necesario.
* Event Hubs: son una buena opción para la integración con otras herramientas SEIM para obtener alertas en los recursos
* Log Analytics: se usa para la supervisión de la hora real y general de la aplicación o para examinar las tendencias.

### <a name="enable-logging-with-powershell"></a>Habilitación del registro con PowerShell

El registro de actividades se habilita automáticamente para todos los recursos de Resource Manager. Debe habilitar el registro de acceso y rendimiento para iniciar la recopilación de los datos disponibles a través de esos registros. Para habilitar el registro, realice los siguientes pasos:

1. Observe el identificador de recurso de la cuenta de almacenamiento, donde se almacenarán los datos de registro. Este valor tendría el siguiente formato: /subscriptions/\<idSuscripción\>/resourceGroups/\<nombreDeGrupoDeRecursos\>/providers/Microsoft.Storage/storageAccounts/\<nombreDeCuentaDeAlmacenamiento\>. Es posible utilizar cualquier cuenta de almacenamiento de la suscripción. Para buscar esta información, se puede usar el portal de vista previa.

    ![Portal de vista previa: diagnóstico de la Puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics1.png)

2. Observe el identificador de recurso de la puerta de enlace de aplicaciones para la que se está habilitando el registro. Este valor tendría el siguiente formato: /subscriptions/\<idSuscripción\>/resourceGroups/\<nombreDeGrupoDeRecursos\>/providers/Microsoft.Network/applicationGateways/\<nombreDePuertaDeEnlaceDeAplicaciones\>. Para buscar esta información, se puede usar el portal de vista previa.

    ![Portal de vista previa: diagnóstico de la Puerta de enlace de aplicaciones](./media/application-gateway-diagnostics/diagnostics2.png)

3. Habilite el registro de diagnóstico mediante el siguiente cmdlet de PowerShell:

    ```powershell
    Set-AzureRmDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/applicationGateways/<application gateway name> -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> -Enabled $true     
    ```
    
> [!TIP] 
>Los registros de actividades no requieren una cuenta de almacenamiento separada. El uso del almacenamiento para el registro de acceso y rendimiento supondrá un costo adicional de servicio.

### <a name="enable-logging-with-azure-portal"></a>Habilitación del registro con Azure Portal

#### <a name="step-1"></a>Paso 1

En Azure Portal, vaya al recurso. Haga clic en **Registros de diagnóstico**. Si es la primera vez que configura diagnósticos, el aspecto de la hoja será como el de la siguiente imagen:

Para Application Gateway, hay 3 registros disponibles.

* Registro de acceso
* Registro de rendimiento
* Registro de firewall

Para empezar a recopilar los datos, clic en **Activar diagnósticos**.

![Hoja Configuración de diagnóstico][1]

#### <a name="step-2"></a>Paso 2

En la hoja **Configuración de diagnóstico** , se establecen las opciones de configuración de los registros de diagnóstico. En este ejemplo, se utiliza Log Analytics para almacenar los registros. Haga clic en la opción **Configurar** de **Log Analytics** para configurar el área de trabajo. Se pueden utilizar también Events Hubs y la cuenta de almacenamiento para guardar los registros de diagnóstico.

![Hoja Diagnósticos][2]

#### <a name="step-3"></a>Paso 3

Elija un área de trabajo de OMS existente o cree uno. En este ejemplo se utiliza uno que ya existe.

![áreas de trabajo de oms][3]

#### <a name="step-4"></a>Paso 4

Cuando haya terminado, confirme la configuración y haga clic en **Guardar** para guardar la configuración.

![Confirmación de la selección][4]

### <a name="activity-log"></a>Registro de actividades

Azure genera este registro (anteriormente conocido como "registro operativo") de forma predeterminada.  Los registros se conservan durante 90 días en el almacén de registros de eventos de Azure. Para más información sobre estos registros, consulte el artículo [Visualización de eventos y registros de actividades](../monitoring-and-diagnostics/insights-debugging-with-events.md).

### <a name="access-log"></a>Registro de acceso

Este registro solo se genera si lo habilitó para cada puerta de enlace de aplicaciones, tal como se indicó en los pasos anteriores. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Cada acceso de Application Gateway se registra en formato JSON, tal como se muestra en el ejemplo siguiente:

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscription id>/RESOURCEGROUPS/<resource group name>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<application gateway name>",
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
```

### <a name="performance-log"></a>Registro de rendimiento

Este registro solo se genera si lo habilitó para cada puerta de enlace de aplicaciones, tal como se indicó anteriormente. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Se registran los datos siguientes:

```json
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
```

> [!NOTE]
> La latencia se calcula desde el momento en que se recibe el primer byte de la solicitud HTTP a la vez que se envía el último byte de la respuesta HTTP. Es la suma del tiempo de procesamiento de Application Gateway más el costo de red en el back-end, más el tiempo que tarda el back-end en procesar la solicitud.

### <a name="firewall-log"></a>Registro de firewall

Este registro solo se genera si lo habilitó para cada puerta de enlace de aplicaciones, tal como se indicó anteriormente. Este registro también requiere que el firewall de aplicaciones web esté configurado en una puerta de enlace de aplicaciones. Los datos se almacenan en la cuenta de almacenamiento que especificó cuando habilitó el registro. Se registran los datos siguientes:

```json
{
    "resourceId": "/SUBSCRIPTIONS/<subscriptionId>/RESOURCEGROUPS/<resourceGroupName>/PROVIDERS/MICROSOFT.NETWORK/APPLICATIONGATEWAYS/<applicationGatewayName>",
    "operationName": "ApplicationGatewayFirewall",
    "time": "2016-09-20T00:40:04.9138513Z",
    "category": "ApplicationGatewayFirewallLog",
    "properties":     {
        "instanceId":"ApplicationGatewayRole_IN_0",
        "clientIp":"108.41.16.164",
        "clientPort":1815,
        "requestUri":"/wavsep/active/RXSS-Detection-Evaluation-POST/",
        "ruleId":"OWASP_973336",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "action":"Logged",
        "site":"Global",
        "message":"XSS Filter - Category 1: Script Tag Vector",
        "details":{"message":" Warning. Pattern match "(?i)(<script","file":"/owasp_crs/base_rules/modsecurity_crs_41_xss_attacks.conf","line":"14"}}
}
```

### <a name="view-and-analyze-the-activity-log"></a>Visualización y análisis del registro de actividades

Puede ver y analizar los datos del registro de actividades mediante el uso de cualquiera de los métodos siguientes:

* **Herramientas de Azure:** puede recuperar información de los registros de actividades a través de Azure PowerShell, de la interfaz de la línea de comandos (CLI) de Azure, la API de REST de Azure o el Portal de vista previa de Azure.  En el artículo [Operaciones de actividades con Resource Manager](../azure-resource-manager/resource-group-audit.md) se detallan instrucciones paso a paso de cada método.
* **Power BI:** si todavía no tiene una cuenta de [Power BI](https://powerbi.microsoft.com/pricing) , puede probarlo gratis. Con el [paquete de contenido de los registros de actividades de Azure para Power BI](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/) puede analizar los datos con los paneles preconfigurados que puede usar tal cual o personalizarlos.

## <a name="view-and-analyze-the-access-performance-and-firewall-log"></a>Visualización y análisis del registro de acceso, rendimiento y firewall

[Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) de Azure puede recopilar el contador de registro de eventos desde la cuenta de Almacenamiento de blobs e incluye visualizaciones y eficaces funcionalidades de búsqueda para analizar los registros.

También puede conectarse a la cuenta de almacenamiento y recuperar las entradas del registro de JSON de los registros de acceso y rendimiento. Cuando descargue los archivos JSON, se pueden convertir a CSV y consultarlos en Excel, PowerBI o cualquier otra herramienta de visualización de datos.

> [!TIP]
> Si está familiarizado con Visual Studio y con los conceptos básicos de cambiar los valores de constantes y variables de C#, puede usar las [herramientas convertidoras de registros](https://github.com/Azure-Samples/networking-dotnet-log-converter) , disponibles en Github.
> 
> 

## <a name="metrics"></a>Métricas

Métricas es una característica de determinados recursos de Azure donde puede ver contadores de rendimiento en el portal. Para Application Gateway, hay disponible una métrica en el momento de redactar este artículo. Esta métrica es la de rendimiento y puede consultarse en el portal. Navegue a una puerta de enlace de aplicaciones y haga clic en **Métricas**. Para ver los valores, seleccione el rendimiento en la sección **Métricas disponibles**. En la siguiente imagen, puede ver un ejemplo con los filtros que pueden utilizarse para mostrar los datos en intervalos de tiempo diferentes.

Para ver una lista de métricas compatibles actuales, consulte el artículo de [métricas compatibles con Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md)

![Vista de métrica][5]

### <a name="alert-rules"></a>Las reglas de alertas

Las reglas de alertas se pueden iniciar en función de las métricas de un recurso. En lo que respecta a Application Gateway, una alerta pueda llamar a un webhook o enviar un correo electrónico a un administrador si el rendimiento de la puerta de enlace de aplicaciones está por encima o debajo de un umbral (o al mismo nivel) durante un periodo especificado.

En el ejemplo siguiente, se explica paso a paso cómo crear una regla de alerta que envíe un correo electrónico a un administrador después de que se haya superado un umbral de rendimiento.

#### <a name="step-1"></a>Paso 1

Haga clic en **Agregar métrica de alerta** para iniciarla. A esta hoja también se puede acceder desde la de métricas.

![Hoja Reglas de alertas][6]

#### <a name="step-2"></a>Paso 2

En la hoja **Agregar regla**, rellene las secciones de nombre, condición y notificación y haga clic en **Aceptar** cuando haya terminado.

El selector **Condición** permite 4 valores, **Mayor que**, **Mayor o igual que**, **Menor que** o **Menor o igual que**.

El selector **Periodo** permite seleccionar un periodo de 5 minutos a 6 horas.

Al seleccionar **Lectores, colaboradores y propietarios de correo electrónico**, el correo electrónico puede ser dinámico según los usuarios que tengan acceso a ese recurso. De lo contrario, se puede proporcionar una lista separada por comas de los usuarios en el cuadro de texto **Correos electrónicos de administrador adicionales** .

![Hoja Agregar regla][7]

Si se supera el umbral, el correo electrónico será similar al que se muestra en la siguiente imagen:

![Correo electrónico de superación de umbral][8]

Se muestra una lista de alertas cuando se crea una alerta de métrica y se proporciona un resumen de todas las reglas de alerta.

![Vista de regla de alerta][9]

Consulte [Recibir notificaciones de alerta](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

Para conocer más detalles sobre los webhooks y cómo usarlos con alertas, visite [Cómo configurar webhooks para las alertas](../monitoring-and-diagnostics/insights-webhooks-alerts.md)

## <a name="next-steps"></a>Pasos siguientes

* Visualización del contador y de registros de eventos con [Log Analytics](../log-analytics/log-analytics-azure-networking-analytics.md)
* Consulte la entrada de blog [Visualize your Azure Activity Logs with Power BI](http://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) (Visualizar los registros de actividades de Azure con Power BI).
* Consulte la entrada de blog [View and analyze Azure Audit Logs in Power BI and more](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/)(Consulta y análisis de registros de auditoría de Azure en Power BI y más).

[1]: ./media/application-gateway-diagnostics/figure1.png
[2]: ./media/application-gateway-diagnostics/figure2.png
[3]: ./media/application-gateway-diagnostics/figure3.png
[4]: ./media/application-gateway-diagnostics/figure4.png
[5]: ./media/application-gateway-diagnostics/figure5.png
[6]: ./media/application-gateway-diagnostics/figure6.png
[7]: ./media/application-gateway-diagnostics/figure7.png
[8]: ./media/application-gateway-diagnostics/figure8.png
[9]: ./media/application-gateway-diagnostics/figure9.png
[10]: ./media/application-gateway-diagnostics/figure10.png


<!--HONumber=Feb17_HO2-->


