---
title: "Tutorial sobre la API de REST de supervisión de Azure | Microsoft Docs"
description: "Cómo autenticar las solicitudes y usar la API de REST de Azure Monitor para recuperar las definiciones de métricas y valores de métricas disponibles."
author: mcollier
manager: 
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 565e6a88-3131-4a48-8b82-3effc9a3d5c6
ms.service: monitoring-and-diagnostics
ms.workload: 
ms.tgt_pltfrm: 
ms.devlang: 
ms.search.region: 
ms.search.scope: 
ms.search.validFrom: 
ms.dyn365.ops.version: 
ms.topic: article
ms.date: 09/18/2017
ms.author: mcollier
ms.openlocfilehash: 357a63c65a4f6864dca259aad8a76f83681cd501
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="azure-monitoring-rest-api-walkthrough"></a>Tutorial sobre la API de REST de supervisión de Azure
En este artículo se describe cómo realizar la autenticación, para que el código pueda usar la [referencia de API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).         

La API de Azure Monitor permite recuperar mediante programación las definiciones de métricas predeterminadas disponibles, la granularidad y los valores de métricas. Los datos pueden guardarse en un almacén de datos independiente, como Azure SQL Database, Azure Cosmos DB o Azure Data Lake. Desde allí se pueden realizar análisis adicionales según sea necesario.

Además de trabajar con varios puntos de datos de métrica, la API de Monitor también permite enumerar las reglas de alertas, ver registros de actividades y mucho más. Para obtener una lista completa de las operaciones disponibles, vea la [referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).

## <a name="authenticating-azure-monitor-requests"></a>Autenticación de solicitudes de Azure Monitor
El primer paso consiste en autenticar la solicitud.

Todas las tareas ejecutadas en la API de Azure Monitor utilizan el modelo de autenticación de Azure Resource Manager. Por lo tanto, todas las solicitudes deben autenticarse con Azure Active Directory (Azure AD). Un enfoque para autenticar la aplicación cliente consiste en crear una entidad de servicio de Azure y recuperar el token de autenticación (JWT). El script de ejemplo siguiente muestra cómo crear una entidad de servicio de Azure AD a través de PowerShell. Para obtener un tutorial más detallado, vea la documentación sobre el [uso de Azure PowerShell para crear una entidad de servicio para acceder a recursos](../azure-resource-manager/resource-group-authenticate-service-principal.md#create-service-principal-with-password). También se puede [crear una entidad de servicio a través de Azure Portal](../azure-resource-manager/resource-group-create-service-principal-portal.md).

```PowerShell
$subscriptionId = "{azure-subscription-id}"
$resourceGroupName = "{resource-group-name}"

# Authenticate to a specific Azure subscription.
Login-AzureRmAccount -SubscriptionId $subscriptionId

# Password for the service principal
$pwd = "{service-principal-password}"

# Create a new Azure AD application
$azureAdApplication = New-AzureRmADApplication `
                        -DisplayName "My Azure Monitor" `
                        -HomePage "https://localhost/azure-monitor" `
                        -IdentifierUris "https://localhost/azure-monitor" `
                        -Password $pwd

# Create a new service principal associated with the designated application
New-AzureRmADServicePrincipal -ApplicationId $azureAdApplication.ApplicationId

# Assign Reader role to the newly created service principal
New-AzureRmRoleAssignment -RoleDefinitionName Reader `
                          -ServicePrincipalName $azureAdApplication.ApplicationId.Guid

```

Para consultar la API de Azure Monitor, la aplicación cliente debe utilizar la entidad de servicio creada anteriormente para la autenticación. El script de PowerShell de ejemplo siguiente muestra un enfoque, basado en utilizar la [Biblioteca de autenticación de Active Directory](../active-directory/active-directory-authentication-libraries.md) (ADAL) para obtener el token de autenticación de JWT. El token de JWT se pasa como parte de un parámetro de autorización HTTP en las solicitudes a la API de REST de Azure Monitor.

```PowerShell
$azureAdApplication = Get-AzureRmADApplication -IdentifierUri "https://localhost/azure-monitor"

$subscription = Get-AzureRmSubscription -SubscriptionId $subscriptionId

$clientId = $azureAdApplication.ApplicationId.Guid
$tenantId = $subscription.TenantId
$authUrl = "https://login.microsoftonline.com/${tenantId}"

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

Después de la autenticación, las consultas pueden ejecutarse en la API de REST de Azure Monitor. Hay dos consultas útiles:

1. Lista de las definiciones de métricas de un recurso
2. Recuperación de los valores de métrica

## <a name="retrieve-metric-definitions-multi-dimensional-api"></a>Recuperación de las definiciones de métricas (API multidimensional)

Utilice la [API de REST de definiciones de métricas de Azure Monitor](https://docs.microsoft.com/rest/api/monitor/metricdefinitions) para acceder a la lista de métricas disponibles para un servicio.

**Método**: GET

**URI de solicitud**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por ejemplo, para recuperar las definiciones de métricas para una cuenta de Azure Storage, la solicitud se parecería a la siguiente:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricDefinitions?api-version=2017-05-01-preview"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contosostorage-metricdef-results.json" `
                  -Verbose

```
> [!NOTE]
> Para recuperar las definiciones de métricas usando la API de REST de Azure Monitor multidimensional, use "2017-05-01-preview" como la versión de API.
>
>

El cuerpo de respuesta JSON resultante sería similar al siguiente ejemplo: (tenga en cuenta que la segunda métrica tiene dimensiones)

```JSON
{
    "value": [
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/UsedCapacity",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Capacity",
            "name": {
                "value": "UsedCapacity",
                "localizedValue": "Used capacity"
            },
            "isDimensionRequired": false,
            "unit": "Bytes",
            "primaryAggregationType": "Average",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ]
        },
        {
            "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metricdefinitions/Transactions",
            "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage",
            "category": "Transaction",
            "name": {
                "value": "Transactions",
                "localizedValue": "Transactions"
            },
            "isDimensionRequired": false,
            "unit": "Count",
            "primaryAggregationType": "Total",
            "metricAvailabilities": [
                {
                    "timeGrain": "PT1M",
                    "retention": "P30D"
                },
                {
                    "timeGrain": "PT1H",
                    "retention": "P30D"
                }
            ],
            "dimensions": [
                {
                    "value": "ResponseType",
                    "localizedValue": "Response type"
                },
                {
                    "value": "GeoType",
                    "localizedValue": "Geo type"
                },
                {
                    "value": "ApiName",
                    "localizedValue": "API name"
                }
            ]
        },
        ...
    ]
}
```

## <a name="retrieve-dimension-values-multi-dimensional-api"></a>Recuperación de valores de dimensión (API multidimensional)
Una vez que se conocen las definiciones de métricas disponibles, puede haber algunas métricas que tengan dimensiones. Antes de consultar la métrica, puede que desee detectar cuál es el intervalo de valores que tiene una dimensión. Según estos valores de dimensión, a continuación puede elegir filtrar o segmentar las métricas en función de valores de dimensión al realizar la consulta para las métricas. Utilice el “valor” del nombre de métrica (no “localizedValue”) para todas las solicitudes de filtro (por ejemplo, recuperación de puntos de datos de las métricas de “CpuTime” y “Requests”). Si no se especifica ningún filtro, se devuelve la métrica predeterminada.

> [!NOTE]
> Para recuperar los valores de dimensión usando la API de REST de Azure Monitor, use "2017-05-01-preview" como la versión de API.
>
>

**Método**: GET

**URI de solicitud**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&amp;timespan=*{starttime/endtime}*&amp;$filter=*{filter}*&amp;resultType=metadata&amp;api-version=*{apiVersion}*

Por ejemplo, para recuperar la lista de posibles valores de “dimensión de nombre de la API” para la métrica “Transactions” durante un intervalo de tiempo determinado, la solicitud sería como sigue:

```PowerShell
$filter = "APIName eq '*'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-01T00:00:00Z/2017-09-10T00:00:00Z&resultType=metadata&$filter=${filter}&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-dimension-values.json" `
    -Verbose
```
El cuerpo de respuesta JSON resultante sería similar al siguiente ejemplo:

```JSON
{
  "timespan": "2017-09-01T00:00:00Z/2017-09-10T00:00:00Z",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "DeleteBlob"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "SetBlobProperties"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "PutPage"
            }
          ]
        },
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "Unknown"
            }
          ]
        },
        ...
      ]    
    }
  ]
}
```

## <a name="retrieve-metric-values-multi-dimensional-api"></a>Recuperación de valores de métrica (API multidimensional)
Una vez que se conocen las definiciones de métricas disponibles y los posibles valores de dimensión, es posible recuperar los valores de métricas relacionados. Use el nombre “value” de la métrica (no “localizedValue”) para cualquier solicitud de filtrado. Si no se especifica ningún filtro de dimensión, se devuelve la métrica agregada acumulada.

> [!NOTE]
> Para recuperar los valores de métrica multidimensionales usando la API de REST de Azure Monitor, use "2017-05-01-preview" como la versión de API.
>
>

**Método**: GET

**URI de solicitud**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?metric=*{metric}*&amp;timespan=*{starttime/endtime}*&amp;$filter=*{filter}*&amp;interval=*{timeGrain}*&amp;aggregation=*{aggreation}*&amp;api-version=*{apiVersion}*

Por ejemplo, para recuperar los valores de métrica de la métrica “Transactions” de almacenamiento durante un intervalo de 5 minutos, para todas las transacciones al nombre de API “GetBlobProperties”, la solicitud sería como sigue:

```PowerShell
$filter = "APIName eq 'GetBlobProperties'"
$request = "https://management.azure.com/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/microsoft.insights/metrics?metric=Transactions&timespan=2017-09-19T02:00:00Z/2017-09-19T02:05:00Z&$filter=${filter}&interval=PT1M&aggregation=Count&api-version=2017-05-01-preview"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contosostorage-metric-values.json" `
    -Verbose
```
El cuerpo de respuesta JSON resultante sería similar al siguiente ejemplo:

```JSON
{
  "cost": 0,
  "timespan": "2017-09-19T02:00:00Z/2017-09-19T02:05:00Z",
  "interval": "PT1M",
  "value": [
    {
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/accounts/ContosoStorage/providers/Microsoft.Insights/metrics/Transactions",
      "type": "Microsoft.Insights/metrics",
      "name": {
        "value": "Transactions",
        "localizedValue": "Transactions"
      },
      "unit": "Count",
      "timeseries": [
        {
          "metadatavalues": [
            {
              "name": {
                "value": "apiname",
                "localizedValue": "apiname"
              },
              "value": "GetBlobProperties"
            }
          ],
          "data": [
            {
              "timeStamp": "2017-09-19T02:00:00Z",
              "count": 2.0
            },
            {
              "timeStamp": "2017-09-19T02:01:00Z",
              "count": 1.0
            },
            {
              "timeStamp": "2017-09-19T02:02:00Z",
              "count": 3.0
            },
            {
              "timeStamp": "2017-09-19T02:03:00Z",
              "count": 7.0
            },
            {
              "timeStamp": "2017-09-19T02:04:00Z",
              "count": 2.0
            }
          ]
        }
      ]
    }
  ]
}
```

## <a name="retrieve-metric-definitions"></a>Recuperación de las definiciones de métricas
Utilice la [API de REST de definiciones de métricas de Azure Monitor](https://msdn.microsoft.com/library/mt743621.aspx) para acceder a la lista de métricas disponibles para un servicio.

**Método**: GET

**URI de solicitud**: https://management.azure.com/subscriptions/*{subscriptionId}*/resourceGroups/*{resourceGroupName}*/providers/*{resourceProviderNamespace}*/*{resourceType}*/*{resourceName}*/providers/microsoft.insights/metricDefinitions?api-version=*{apiVersion}*

Por ejemplo, para recuperar las definiciones de métricas para una aplicación lógica de Azure, la solicitud se parecería a la siguiente:

```PowerShell
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01"

Invoke-RestMethod -Uri $request `
                  -Headers $authHeader `
                  -Method Get `
                  -OutFile ".\contostweets-metricdef-results.json" `
                  -Verbose
```
> [!NOTE]
> Para recuperar las definiciones de métricas usando la API de REST de Azure Monitor, use "2016-03-01" como la versión de API.
>
>

El cuerpo de respuesta JSON resultante sería similar al siguiente ejemplo:
```JSON
{
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions",
  "value": [
    {
      "name": {
        "value": "RunsStarted",
        "localizedValue": "Runs Started"
      },
      "category": "AllMetrics",
      "startTime": "0001-01-01T00:00:00Z",
      "endTime": "0001-01-01T00:00:00Z",
      "unit": "Count",
      "primaryAggregationType": "Total",
      "resourceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "resourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets",
      "metricAvailabilities": [
        {
          "timeGrain": "PT1M",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        },
        {
          "timeGrain": "PT1H",
          "retention": "P30D",
          "location": null,
          "blobLocation": null
        }
      ],
      "properties": null,
      "dimensions": null,
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricdefinitions/RunsStarted",
      "supportedAggregationTypes": [ "None", "Average", "Minimum", "Maximum", "Total", "Count" ]
    }
  ]
}
```

Para obtener más información, vea la documentación sobre la [lista de las definiciones de métricas de un recurso en la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/mt743621.aspx) .

## <a name="retrieve-metric-values"></a>Recuperación de los valores de métrica
Una vez que se conocen las definiciones de métricas disponibles, es posible recuperar los valores de métricas relacionados. Utilice el “valor” del nombre de métrica (no “localizedValue”) para todas las solicitudes de filtro (por ejemplo, recuperación de puntos de datos de las métricas de “CpuTime” y “Requests”). Si no se especifica ningún filtro, se devuelve la métrica predeterminada.

> [!NOTE]
> Para recuperar los valores de métricas usando la API de REST de Azure Monitor, use "2016-09-01" como la versión de API.
>
>

**Método**: GET

**URI de solicitud**: https://management.azure.com/subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/*{resource-provider-namespace}*/*{resource-type}*/*{resource-name}*/providers/microsoft.insights/metrics?$filter=*{filter}*&amp;api-version=*{apiVersion}*

Por ejemplo, para recuperar los puntos de datos de métricas RunsSucceeded para el intervalo de tiempo determinado y para un intervalo de agregación de 1 hora, la solicitud sería como sigue:

```PowerShell
$filter = "(name.value eq 'RunsSucceeded') and aggregationType eq 'Total' and startTime eq 2017-08-18T19:00:00 and endTime eq 2017-08-18T23:00:00 and timeGrain eq duration'PT1H'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-results.json" `
    -Verbose
```

El cuerpo de respuesta JSON resultante sería similar al siguiente ejemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T19:00:00Z",
          "total": 0.0
        },
        {
          "timeStamp": "2017-08-18T20:00:00Z",
          "total": 159.0
        },
        {
          "timeStamp": "2017-08-18T21:00:00Z",
          "total": 174.0
        },
        {
          "timeStamp": "2017-08-18T22:00:00Z",
          "total": 97.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

Para recuperar varios puntos de datos o agregación, agregue los nombres de definición de la métrica y los tipos de agregación para el filtro, tal como se muestra en el ejemplo siguiente:

```PowerShell
$filter = "(name.value eq 'ActionsCompleted' or name.value eq 'RunsSucceeded') and (aggregationType eq 'Total' or aggregationType eq 'Average') and startTime eq 2017-08-18T21:00:00 and endTime eq 2017-08-18T21:30:00 and timeGrain eq duration'PT1M'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metrics?$filter=${filter}&api-version=2016-09-01"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -OutFile ".\contostweets-metrics-multiple-results.json" `
    -Verbose
```
El cuerpo de respuesta JSON resultante sería similar al siguiente ejemplo:

```JSON
{
  "value": [
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/ActionsCompleted",
      "name": {
        "value": "ActionsCompleted",
        "localizedValue": "Actions Completed "
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "data": [
        {
          "timeStamp": "2017-08-18T21:03:00Z",
          "total": 5.0,
          "average": 1.0
        },
        {
          "timeStamp": "2017-08-18T21:04:00Z",
          "total": 7.0,
          "average": 1.0
        }
      ],
      "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/Microsoft.Insights/metrics/RunsSucceeded",
      "name": {
        "value": "RunsSucceeded",
        "localizedValue": "Runs Succeeded"
      },
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```

### <a name="use-armclient"></a>Uso de ARMClient
Un enfoque más consiste en utilizar [ARMClient](https://github.com/projectkudu/armclient) en la máquina Windows. ARMClient controla automáticamente la autenticación de Azure AD (y el token JWT resultante). Los siguientes pasos describen el uso de ARMClient para recuperar datos de métricas:

1. Instale [Chocolatey](https://chocolatey.org/) y [ARMClient](https://github.com/projectkudu/armclient).
2. En una ventana de terminal, escriba *inicio de sesión de armclient.exe*. Al hacerlo, le pide que inicie sesión en Azure.
3. Escriba *armclient GET [your_resource_id]/providers/microsoft.insights/metricdefinitions?api-version=2016-03-01*
4. Escriba *armclient GET [your_resource_id]/providers/microsoft.insights/metrics?api-version=2016-09-01*

Por ejemplo, para recuperar las definiciones de métricas para una aplicación lógica específica, ejecute el comando siguiente:
```
armclient GET /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets/providers/microsoft.insights/metricDefinitions?api-version=2016-03-01
```


## <a name="retrieve-the-resource-id"></a>Recuperación del identificador de recursos
Mediante la API de REST, puede ayudarlo a comprender las definiciones de métricas disponibles, la granularidad y los valores relacionados. Dicha información es útil cuando se usa la [biblioteca de administración de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).

Para el código anterior, el identificador de recurso que se usa es la ruta de acceso completa al recurso de Azure deseado. Por ejemplo, para consultar en una aplicación web de Azure, el identificador de recurso sería:

*/subscriptions/{identificador-suscripción}/resourceGroups/{nombre-grupo-recursos}/providers/Microsoft.Web/sites/{nombre-sitio}/*

La lista siguiente contiene algunos ejemplos de formatos de identificador de recursos para los distintos recursos de Azure:

* **IoT Hub** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Devices/IotHubs/*{iot-hub-name}*
* **Grupo elástico SQL** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{pool-db}*/elasticpools/*{sql-pool-name}*
* **SQL Database (v12)** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Sql/servers/*{server-name}*/databases/*{database-name}*
* **Service Bus** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.ServiceBus/*{namespace}*/*{servicebus-name}*
* **Conjunto de escalado de máquinas virtuales**: /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachineScaleSets/*{vm-name}*
* **VM** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.Compute/virtualMachines/*{vm-name}*
* **Event Hubs** - /subscriptions/*{subscription-id}*/resourceGroups/*{resource-group-name}*/providers/Microsoft.EventHub/namespaces/*{eventhub-namespace}*

Hay enfoques alternativos para recuperar el identificador de recurso, incluido el uso del explorador de recursos de Azure, la visualización del recurso deseado en Azure Portal y a través de PowerShell o la CLI de Azure.

### <a name="azure-resource-explorer"></a>Explorador de recursos de Azure
Para buscar el identificador de recurso para un recurso deseado, un enfoque útil consiste en utilizar el [Explorador de recursos de Azure](https://resources.azure.com) . Vaya al recurso deseado y observe el identificador que se muestra, como en la captura de pantalla siguiente:

![Alt "Explorador de recursos de Azure"](./media/monitoring-rest-api-walkthrough/azure_resource_explorer.png)

### <a name="azure-portal"></a>Portal de Azure
El identificador de recurso también puede obtenerse desde Azure Portal. Para ello, desplácese hasta el recurso deseado y luego seleccione Propiedades. El identificador de recurso se muestra en la sección Propiedades, como se muestra en la siguiente captura de pantalla:

![Alt "Identificador de recurso mostrado en la hoja Propiedades de Azure Portal"](./media/monitoring-rest-api-walkthrough/resourceid_azure_portal.png)

### <a name="azure-powershell"></a>Azure PowerShell
El identificador de recurso también se puede recuperar mediante cmdlets de Azure PowerShell. Por ejemplo, para obtener el identificador de recurso de una aplicación lógica de Azure, ejecute el cmdlet Get-AzureLogicApp, como se muestra en el ejemplo siguiente:

```PowerShell
Get-AzureRmLogicApp -ResourceGroupName azmon-rest-api-walkthrough -Name contosotweets
```

El resultado debería asemejarse al ejemplo siguiente:
```
Id             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Logic/workflows/ContosoTweets
Name           : ContosoTweets
Type           : Microsoft.Logic/workflows
Location       : centralus
ChangedTime    : 8/21/2017 6:58:57 PM
CreatedTime    : 8/18/2017 7:54:21 PM
AccessEndpoint : https://prod-08.centralus.logic.azure.com:443/workflows/f3a91b352fcc47e6bff989b85446c5db
State          : Enabled
Definition     : {$schema, contentVersion, parameters, triggers...}
Parameters     : {[$connections, Microsoft.Azure.Management.Logic.Models.WorkflowParameter]}
SkuName        :
AppServicePlan :
PlanType       :
PlanId         :
Version        : 08586982649483762729
```


### <a name="azure-cli"></a>CLI de Azure
Para recuperar el identificador de recurso para una cuenta de Azure Storage mediante la CLI de Azure, ejecute el comando “az storage account show”, tal como se muestra en el ejemplo siguiente:

```
az storage account show -g azmon-rest-api-walkthrough -n contosotweets2017
```

El resultado debería asemejarse al ejemplo siguiente:
```JSON
{
  "accessTier": null,
  "creationTime": "2017-08-18T19:58:41.840552+00:00",
  "customDomain": null,
  "enableHttpsTrafficOnly": false,
  "encryption": null,
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/azmon-rest-api-walkthrough/providers/Microsoft.Storage/storageAccounts/contosotweets2017",
  "identity": null,
  "kind": "Storage",
  "lastGeoFailoverTime": null,
  "location": "centralus",
  "name": "contosotweets2017",
  "networkAcls": null,
  "primaryEndpoints": {
    "blob": "https://contosotweets2017.blob.core.windows.net/",
    "file": "https://contosotweets2017.file.core.windows.net/",
    "queue": "https://contosotweets2017.queue.core.windows.net/",
    "table": "https://contosotweets2017.table.core.windows.net/"
  },
  "primaryLocation": "centralus",
  "provisioningState": "Succeeded",
  "resourceGroup": "azmon-rest-api-walkthrough",
  "secondaryEndpoints": null,
  "secondaryLocation": "eastus2",
  "sku": {
    "name": "Standard_GRS",
    "tier": "Standard"
  },
  "statusOfPrimary": "available",
  "statusOfSecondary": "available",
  "tags": {},
  "type": "Microsoft.Storage/storageAccounts"
}
```

> [!NOTE]
> Las aplicaciones lógicas de Azure no están aún disponibles a través de la CLI de Azure, por lo tanto, en el ejemplo anterior se muestra una cuenta de Azure Storage.
>
>

## <a name="retrieve-activity-log-data"></a>Recuperación de datos del registro de actividad
Además las definiciones de métricas y los valores relacionados, también es posible usar la API de REST de Azure Monitor para recuperar información adicional interesante relacionada con recursos de Azure. Por ejemplo, es posible consultar los datos del [registro de actividades](https://msdn.microsoft.com/library/azure/dn931934.aspx) . En el ejemplo siguiente se muestra cómo usar la API de REST de Azure Monitor para consultar los datos del registro de actividades dentro de un intervalo de fechas específico para una suscripción de Azure:

```PowerShell
$apiVersion = "2015-04-01"
$filter = "eventTimestamp ge '2017-08-18' and eventTimestamp le '2017-08-19'and eventChannels eq 'Admin, Operation'"
$request = "https://management.azure.com/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/providers/microsoft.insights/eventtypes/management/values?api-version=${apiVersion}&$filter=${filter}"
Invoke-RestMethod -Uri $request `
    -Headers $authHeader `
    -Method Get `
    -Verbose
```

## <a name="next-steps"></a>Pasos siguientes
* Revise la [información general de supervisión](monitoring-overview.md).
* Vea las [métricas compatibles con Azure Monitor](monitoring-supported-metrics.md).
* Revise la [referencia de la API de REST de Microsoft Azure Monitor](https://msdn.microsoft.com/library/azure/dn931943.aspx).
* Revise la [biblioteca de administración de Azure](https://msdn.microsoft.com/library/azure/mt417623.aspx).
