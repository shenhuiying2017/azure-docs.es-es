<properties
	pageTitle="Tutorial sobre la API de REST de supervisión de Azure | Microsoft Azure"
	description="Cómo autenticar las solicitudes y usar la API de REST de supervisión de Azure."
	authors="mcollier, rboucher"
	manager=""
	editor=""
	services="monitoring-and-diagnostics"
	documentationCenter="monitoring-and-diagnostics"/>

<tags
	ms.service="monitoring-and-diagnostics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/27/2016"
	ms.author="mcollier"/>

# Tutorial sobre la API de REST de supervisión de Azure
En este artículo se describe cómo realizar la autenticación, para que el código pueda usar la [referencia de API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

La API de Azure Monitor permite recuperar mediante programación las definiciones de métricas predeterminadas disponibles (el tipo de métrica de tiempo de CPU, las solicitudes, etc.), la granularidad y los valores de métricas. Una vez recuperados, los datos pueden guardarse en un almacén de datos independiente, como Azure SQL Database, DocumentDB o Azure Data Lake. Desde allí se pueden realizar análisis adicionales según sea necesario.

Además de trabajar con varios puntos de datos de métrica, como se muestra en este artículo, la API de Monitor permite enumerar las reglas de alertas, ver registros de actividades y mucho más. Para obtener una lista completa de las operaciones disponibles, vea la [referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## Autenticación de solicitudes de Azure Monitor

El primer paso consiste en autenticar la solicitud.

Todas las tareas ejecutadas en la API de Azure Monitor utilizan el modelo de autenticación de Azure Resource Manager. Por lo tanto, todas las solicitudes deben autenticarse con Azure Active Directory (Azure AD). Un enfoque para autenticar la aplicación cliente consiste en crear una entidad de servicio de Azure y recuperar el token de autenticación (JWT). El script de ejemplo siguiente muestra cómo crear una entidad de servicio de Azure AD a través de PowerShell. Para obtener un tutorial más detallado, vea la documentación sobre el [uso de Azure PowerShell para crear una entidad de servicio para acceder a recursos](../resource-group-authenticate-service-principal.md#authenticate-service-principal-with-password—powershell). También es posible [crear una entidad de servicio a través de Azure Portal](../resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"
$location = "centralus"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Insights" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar la API de Azure Monitor, la aplicación cliente debe utilizar la entidad de servicio creada anteriormente para la autenticación. El script de PowerShell de ejemplo siguiente muestra un enfoque, basado en utilizar la [Biblioteca de autenticación de Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) para ayudar a obtener el token de autenticación JWT. El token de JWT se pasa como parte de un parámetro de autorización HTTP en las solicitudes a la API de Insights para Azure.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.windows.net/${tenantId}"

$AuthContext = [Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext]$authUrl
$cred = New-Object -TypeName Microsoft.IdentityModel.Clients.ActiveDirectory.ClientCredential -ArgumentList ($clientId, $pwd)
 
$result = $AuthContext.AcquireToken("https://management.core.windows.net/", $cred)

# Build an array of HTTP header values 
$authHeader = @{
'Content-Type'='application/json'
'Accept'='application/json'
'Authorization'=$result.CreateAuthorizationHeader()
}
```

Una vez completado el paso de configuración de autenticación, las consultas se pueden ejecutar en la API de REST de Azure Monitor. Hay dos consultas útiles:

1. Lista de las definiciones de métricas de un recurso

2. Recuperación de los valores de métrica


## Recuperación de las definiciones de métricas
>[AZURE.NOTE] Para recuperar las definiciones de métricas usando la API de REST de Azure Monitor, use "2016-03-01" como la versión de API.

```PowerShell
$apiVersion = "2016-03-01"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metricDefinitions?api-version=${apiVersion}"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -Verbose
```
Para una aplicación lógica de Azure, las definiciones de métrica tendrían un aspecto similar a la captura de pantalla siguiente:

![Alt "Vista JSON de la respuesta de la definición de la métrica"](./media/monitoring-rest-api-walkthrough/available_metric_definitions_logic_app_json_response_clean.png)

Para obtener más información, vea la documentación sobre la [lista de las definiciones de métricas de un recurso en la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx).

## Recuperación de los valores de métrica
Una vez que se conocen las definiciones de métricas disponibles, es posible recuperar los valores de métricas relacionados. Utilice el “valor” del nombre de métrica (no “localizedValue”) para todas las solicitudes de filtro (por ejemplo, recuperación de puntos de datos de las métricas de “CpuTime” y “Requests”). Si no se especifica ningún filtro, se devuelve la métrica predeterminada.

>[AZURE.NOTE] Para recuperar los valores de métricas usando la API de REST de Azure Monitor, use "2016-06-01" como la versión de API.

**Método**: GET

**Identificador URI de la solicitud**: https://management.azure.com/subscriptions/_{subscription-id}_/resourceGroups/_{resource-group-name}_/providers/_{resource-provider-namespace}_/_{resource-type}_/_{resource-name}_/providers/microsoft.insights/metrics?$filter=_{filter}_&api-version=_{apiVersion}_

Por ejemplo, para recuperar los puntos de datos de métricas RunsSucceeded para el intervalo de tiempo determinado y para un intervalo de agregación de 1 hora, la solicitud sería como sigue:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2016-09-23 and endTime eq 2016-09-24 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

El resultado tendría un aspecto similar al que se presenta en la captura de pantalla de ejemplo siguiente:

![Alt "Respuesta JSON que muestra el valor de métrica del tiempo medio de respuesta"](./media/monitoring-rest-api-walkthrough/available_metrics_logic_app_json_response.png)

Para recuperar varios puntos de datos o agregación, agregue los nombres de definición de la métrica y los tipos de agregación para el filtro, tal como se muestra en el ejemplo siguiente:

```PowerShell
$apiVersion = "2016-06-01"
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2016-09-23T13:30:00Z and endTime eq 2016-09-23T14:30:00Z and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/${resourceProviderNamespace}/${resourceType}/${resourceName}/providers/microsoft.insights/metrics?`$filter=${filter}&api-version=${apiVersion}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

### Uso de ARMClient
Una alternativa al uso de PowerShell (como se indicó anteriormente) es usar [ARMClient](https://github.com/projectkudu/ARMClient) en el equipo Windows. ARMClient controla automáticamente la autenticación de Azure AD (y el token JWT resultante). Los siguientes pasos describen el uso de ARMClient para recuperar datos de métrica:

1. Instale [Chocolatey](https://chocolatey.org/) y [ARMClient](https://github.com/projectkudu/ARMClient).

2. En una ventana de terminal, escriba _inicio de sesión de armclient.exe_. Esto le pide que inicie sesión en Azure.

3. Escriba _armclient GET [identificador\_del\_recurso]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01_.

4. Escriba _armclient GET [identificador\_del\_recurso]/providers/microsoft.insights/metrics?api-version=2016-06-01_.


![Alt "Uso de ARMClient para trabajar con la API de REST de supervisión de Azure"](./media/monitoring-rest-api-walkthrough/armclient_metricdefinitions.png)


## Recuperación del identificador de recurso
Mediante la API de REST, puede ayudarlo a comprender las definiciones de métricas disponibles, la granularidad y los valores relacionados. Dicha información es útil cuando se usa la [biblioteca de administración de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para el código anterior, el identificador de recurso que se usa es la ruta de acceso completa al recurso de Azure deseado. Por ejemplo, para consultar en una aplicación web de Azure, el identificador de recurso sería:

*/subscriptions/{identificador-suscripción}/resourceGroups/{nombre-grupo-recursos}/providers/Microsoft.Web/sites/{nombre-sitio}/*

La lista siguiente contiene algunos ejemplos de formatos de identificador de recursos para los distintos recursos de Azure:

* **IoT Hub**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.Devices/IotHubs/_{nombre-iot-hub}_

* **Elastic SQL Pool**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.Sql/servers/_{db-grupo}_/elasticpools/_{nombre-grupo-sql}_

* **SQL Database (v12)**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.Sql/servers/_{nombre-servidor}_/databases/_{nombre-base datos}_.

* **Service Bus**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.ServiceBus/_{espacio-de-nombres}_/_{nombre-servicebus}_.

* **VM Scale Sets**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.Compute/virtualMachineScaleSets/_{nombre-vm}_.

* **Virtual Machines**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.Compute/virtualMachines/_{nombre-vm}_.

* **Event Hubs**: /subscriptions/_{identificador-suscripción}_/resourceGroups/_{nombre-grupo-recursos}_/providers/Microsoft.EventHub/namespaces/_{espacio de nombres-centro de eventos}_.


Hay enfoques alternativos para recuperar el identificador de recurso, incluido el uso del explorador de recursos de Azure, la visualización del recurso deseado en Azure Portal y a través de PowerShell o la CLI de Azure.

### Explorador de recursos de Azure
Para buscar el identificador de recurso para un recurso deseado, un enfoque útil consiste en utilizar el [Explorador de recursos de Azure](https://resources.azure.com). Vaya al recurso deseado y observe el identificador que se muestra, como en la captura de pantalla siguiente:

![Alt "Explorador de recursos de Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### Portal de Azure
El identificador de recurso también puede obtenerse desde Azure Portal. Para ello, desplácese hasta el recurso deseado y luego seleccione Propiedades. El identificador de recurso se muestra en la hoja Propiedades, como se muestra en la siguiente captura de pantalla:

![Alt "Identificador de recurso mostrado en la hoja Propiedades de Azure Portal"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### Azure PowerShell
El identificador de recurso también se puede recuperar mediante cmdlets de Azure PowerShell. Por ejemplo, para obtener el identificador de recurso de una aplicación web de Azure, ejecute el cmdlet Get-AzureRmWebApp, como se muestra en la siguiente captura de pantalla:

![Alt "Identificador de recurso obtenido a través de PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_powershell.png)

### Azure CLI
Para recuperar el identificador de recurso mediante la CLI de Azure, ejecute el comando “azure webapp show”, especificando la opción “--json”, como se muestra en la siguiente captura de pantalla:

![Alt "Identificador de recurso obtenido a través de PowerShell"](./media\monitoring-rest-api-walkthrough\resourceid_azurecli.png)

## Recuperación de datos del registro de actividades
Además de trabajar con definiciones de métrica y valores relacionados, también es posible recuperar información adicional interesante relacionada con recursos de Azure. Por ejemplo, es posible consultar los datos del [registro de actividades](https://msdn.microsoft.com/library/azure/dn931934.aspx). En el ejemplo siguiente se muestra cómo usar la API de REST de Azure Monitor para consultar los datos del registro de actividades dentro de un intervalo de fechas específico para una suscripción de Azure:

```PowerShell
$apiVersion = "2014-04-01"
$filter = "eventTimestamp ge '2016-09-23' and eventTimestamp le '2016-09-24'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/${subscriptionId}/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&`$filter=${filter}"
(Invoke-RestMethod -Uri $request `
                   -Headers $authHeader `
                   -Method Get `
                   -Verbose).Value | ConvertTo-Json
```

## Pasos siguientes
* Revise la [información general de supervisión](monitoring-overview.md).
* Vea las [métricas compatibles con Azure Monitor](monitoring-supported-metrics.md).
* Revise la [referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Revise la [biblioteca de administración de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

<!---HONumber=AcomDC_0928_2016-->