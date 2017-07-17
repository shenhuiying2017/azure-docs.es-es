---
title: "Análisis de registros para la red CDN de Azure | Microsoft Docs"
description: "Los clientes pueden habilitar el análisis de registros para la red CDN de Azure."
services: cdn
documentationcenter: 
author: lichard
manager: erikre
editor: 
ms.assetid: 95e18b3c-b987-46c2-baa8-a27a029e3076
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/12/2017
ms.author: rli
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: e3be8aced50a2ce330523d8633f524803af8c8a7
ms.contentlocale: es-es
ms.lasthandoff: 05/11/2017


---

# Registros de diagnóstico de red CDN de Azure
<a id="diagnostics-logs-for-azure-cdn" class="xliff"></a>

Después de habilitar CDN para la aplicación, es probable que quiere supervisar el uso de la red CDN, comprobar el estado de su entrega y solucionar posibles problemas. La red CDN de Azure proporciona estas funcionalidades con [análisis básico](cdn-analyze-usage-patterns.md).

Como usuario actual de la red CDN de Azure con un perfil estándar o premium de Verizon, ya puede ver análisis básicos en el portal complementario accesible mediante la opción "Administrar" de Azure Portal. Con la nueva característica [Registros de diagnóstico](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs), ahora puede ver análisis básicos y guardarlos en uno o varios destinos, como una cuenta de Azure Storage, una instancia de Azure Event Hubs o un [área de trabajo de Log Analytics (OMS)](https://docs.microsoft.com/azure/log-analytics/log-analytics-get-started). Esta característica está disponible para todos los puntos de conexión de red CDN que pertenecen a Verizon (estándar y premium) y perfiles de red CDN de Akamai (estándar).

Los registros de diagnóstico le permiten exportar métricas básicas de uso desde su punto de conexión CDN a diversos orígenes, de modo que pueda consumirlas de forma personalizada. Por ejemplo, puede realizar las tareas siguientes:

- Exportar datos a almacenamiento de blobs, exportar a CSV y generar gráficos en Excel.
- Exportar datos a los centros de eventos y correlacionarlos con los datos de otros servicios de Azure.
- Exportar datos a Log Analytics y verlos en su propia área de trabajo de OMS.


![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/OMS-workspace.png)

El siguiente tutorial le lleva por el esquema de los datos de análisis básico, los pasos que intervienen en la habilitación de la característica y la entrega de dichos datos a diversos destinos y su consumo desde estos destinos.

## Habilitación del registro con Azure Portal
<a id="enable-logging-with-azure-portal" class="xliff"></a>

Los registros de diagnósticos están **desactivados** de forma predeterminada. Para habilitarlos, siga estos pasos:


Inicie sesión en el [Portal de Azure](http://portal.azure.com). Si no se ha habilitado ya la red CDN para el flujo de trabajo, [habilítela](cdn-create-new-endpoint.md) antes de continuar.

1. En el portal, vaya a **Perfil de CDN**.
2. Seleccione un perfil de CDN y luego seleccione el punto de conexión de CDN para el que desea habilitar **Registros de diagnóstico**.
    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/Browse-to-Diagnostics-logs.png)
3. Vaya a la hoja **Registros de diagnóstico** en la sección **Supervisión**, y cambie el estado a **Activado**.
    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/Diagnostics-logs-options.png)
4. Seleccione y configure el destino de archivo deseado (cuenta de Storage, Event Hub, Log Analytics). 
    
    En este ejemplo, se usa Azure Storage para almacenar los registros; seleccione **Archivar en una cuenta de almacenamiento**, seleccione los días de retención y haga clic en **Análisis básico** en **Registro**. Actualmente solo se ofrece **Análisis básico**, pero en un futuro estarán disponibles más tipos de registro. Consulte a continuación la información de esquema, agregación y retraso sobre Análisis básico. 

    ![Portal: Registros de diagnóstico](./media/cdn-diagnostics-log/Diagnostics-logs-storage.png)
5.  Guarde la nueva configuración de diagnóstico.
    
    Los datos de registro de Verizon llevan 1 hora de retraso y tardan 2 horas en comenzar a aparecer tras la finalización de la propagación del punto de conexión.
    Los datos de registro de Akamai llevan 24 horas de retraso y tardan 24 horas en comenzar a aparecer si se crearon hace más de 24 horas. Si se acaban de crear, los registros tardan en comenzar a aparecer 25 horas.

## Habilitación del registro con PowerShell
<a id="enable-logging-with-powershell" class="xliff"></a>

A continuación se muestran dos ejemplos de cómo habilitar y obtener registros de diagnóstico mediante los cmdlets de Azure PowerShell.

###Habilitación de registros de diagnóstico en una cuenta de Storage
<a id="enabling-diagnostic-logs-in-a-storage-account" class="xliff"></a>

Para habilitar los registros de diagnóstico en una cuenta de Storage, use este comando:

```powershell
    Set-AzureRmDiagnosticSetting -ResourceId "/subscriptions/{subscriptionId}/resourcegroups/{resourceGroupName}/providers/Microsoft.Cdn/profiles/{profileName}/endpoints/{endpointName}" -StorageAccountId "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ClassicStorage/storageAccounts/{storageAccountName}" -Enabled $true -Categories CoreAnalytics
```

## Consumo de registros del almacenamiento
<a id="consuming-logs-from-storage" class="xliff"></a>
En esta sección se describe el esquema del análisis básico de CDN, cómo se organiza dentro de una cuenta de Azure Storage, y se proporciona código de ejemplo para descargar los registros en un archivo CSV.

### Uso del Explorador de Microsoft Azure Storage
<a id="using-microsoft-azure-storage-explorer" class="xliff"></a>
Antes de poder acceder a los datos de análisis básico desde la cuenta de Azure Storage, primero necesita una herramienta para acceder a los contenidos de una cuenta de almacenamiento. Aunque hay varias herramientas disponibles en el mercado, la única que recomendamos es el Explorador de Microsoft Azure Storage. Puede descargar la herramienta desde [aquí](http://storageexplorer.com/). Después de descargar e instalar el software, configúrelo para usar la misma cuenta de Azure Storage que configuró como destino para Registros de diagnóstico de la red CDN.

1.    Abra el **Explorador de Microsoft Azure Storage**
2.    Busque la cuenta de almacenamiento.
3.    Vaya al nodo **"Contenedores de blob”** en esta cuenta de almacenamiento y expanda el nodo.
4.    Seleccione el contenedor llamado **"insights-logs-coreanalytics"** y haga doble clic en él.
5.    Los resultados se muestran en el panel derecho, comenzando por el primer nivel que se parece a **"resourceId="**. Siga haciendo clic hasta que vea el archivo **PT1H.json**. Consulte la nota a continuación para ver la explicación de la ruta de acceso.
6.    Cada blob **PT1H.json** representa los registros de análisis durante una hora de un punto de conexión de red CDN concreto o de su dominio personalizado.
7.    El esquema del contenido de este archivo JSON se describe en la sección Esquema de los registros de análisis básico.


> [!NOTE]
> **Formato de ruta de acceso de blob**
> 
> Los registros de análisis básico se generan cada hora. Todos los datos de una hora se recopilan y almacenan dentro de un único blob de Azure como una carga JSON. La ruta de acceso a este blob de Azure aparece como si hubiera una estructura jerárquica. Este se debe a que la herramienta Explorador de Storage interpreta "/" como un separador de directorio y muestra la jerarquía por motivos de comodidad. En realidad, la ruta de acceso completa solo representa el nombre del blob. Este nombre del blob sigue la convención de nomenclatura siguiente:    
    
    resourceId=/SUBSCRIPTIONS/{Subscription Id}/RESOURCEGROUPS/{Resource Group Name}/PROVIDERS/MICROSOFT.CDN/PROFILES/{Profile Name}/ENDPOINTS/{Endpoint Name}/ y={Year}/m={Month}/d={Day}/h={Hour}/m={Minutes}/PT1H.json

**Descripción de los campos:**

|value|Description|
|-------|---------|
|Id. de suscripción    |Identificador de la suscripción de Azure. Está en formato Guid.|
|Recurso |Nombre del grupo de recursos al que pertenecen los recursos de red CDN.|
|Nombre del perfil |Nombre del perfil de CDN|
|Nombre del punto de conexión |Nombre del punto de conexión de CDN|
|Year|    Representación del año en formato de cuatro dígitos, por ejemplo, 2017.|
|Mes|    Representación del mes en formato de dos dígitos. 01= enero 12=diciembre|
|Día|    Representación del día del mes en formato de dos dígitos.|
|PT1H.json|    Archivo JSON real donde se almacenan los datos de análisis|

### Exportación de los datos de análisis básico a un archivo CSV
<a id="exporting-the-core-analytics-data-to-a-csv-file" class="xliff"></a>

Para facilitar el acceso al análisis básico, se proporciona un código de ejemplo para una herramienta, que permite descargar los archivos JSON en un formato plano de archivo separado por comas que se puede usar para crear fácilmente gráficos y otras agregaciones.

A continuación se muestra cómo puede usar la herramienta:

1.    Visite el vínculo de GitHub: [https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv ](https://github.com/Azure-Samples/azure-cdn-samples/tree/master/CoreAnalytics-ExportToCsv )
2.    Descargar el código
3.    Siga las instrucciones para compilarlo y configurarlo.
4.    Ejecute la herramienta.
5.    El archivo CSV resultante muestra los datos de análisis en una jerarquía sencilla plana.

## Tipos de registros de diagnóstico
<a id="diagnostic-logs-types" class="xliff"></a>

Actualmente solo se ofrecen los registros de análisis básico que contienen métricas que muestran estadísticas de respuesta HTTP y estadísticas de salida, como se ven desde los servidores POP/perimetrales de la red CDN. Con el tiempo, se agregarán tipos de registros adicionales.

### Detalles de las métricas de análisis básico
<a id="core-analytics-metrics-details" class="xliff"></a>
A continuación se muestra una lista de métricas disponibles en los registros de análisis básico. No todas las métricas están disponibles en todos los proveedores, si bien tales diferencias son mínimas. En la tabla siguiente se muestra si una determinada métrica está disponible en un proveedor. Tenga en cuenta que las métricas solo están disponibles para esos puntos de conexión de CDN que contienen tráfico.


|Métrica                     | Descripción   | Verizon  | Akamai 
|---------------------------|---------------|---|---|
| RequestCountTotal         |Número total de solicitudes durante este periodo| Sí  |Sí   |
| RequestCountHttpStatus2xx |Recuento de todas las solicitudes que dieron lugar a un código HTTP 2xx (por ejemplo, 200, 202)              | Sí  |Sí   |
| RequestCountHttpStatus3xx | Recuento de todas las solicitudes que dieron lugar a un código HTTP 3xx (por ejemplo, 300, 302)              | Sí  |Sí   |
| RequestCountHttpStatus4xx |Recuento de todas las solicitudes que dieron lugar a un código HTTP 4xx (por ejemplo, 400, 404)               | Sí   |Sí   |
| RequestCountHttpStatus5xx | Recuento de todas las solicitudes que dieron lugar a un código HTTP 5xx (por ejemplo, 500, 504)              | Sí  |Sí   |
| RequestCountHttpStatusOthers |  Recuento de todos los demás códigos HTTP (fuera del intervalo 2xx-5xx) | Sí  |Sí   |
| RequestCountHttpStatus200 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 200              |No   |Sí   |
| RequestCountHttpStatus206 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 206              |No   |Sí   |
| RequestCountHttpStatus302 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 302              |No   |Sí   |
| RequestCountHttpStatus304 |  Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 304             |No   |Sí   |
| RequestCountHttpStatus404 | Recuento de todas las solicitudes que dieron lugar a una respuesta de código HTTP 404              |No   |Sí   |
| RequestCountCacheHit |Recuento de todas las solicitudes que dieron lugar a un acierto de caché. Esto significa que el recurso se atendió directamente desde el servidor POP al cliente.               | Sí  |No   |
| RequestCountCacheMiss | Recuento de todas las solicitudes que dieron lugar a un error de caché. Esto significa que el recurso no se encontró en el servidor POP más cercano al cliente y, por tanto, se recupera del origen.              |Sí   | No  |
| RequestCountCacheNoCache | Recuento de todas las solicitudes a un recurso a las que se les impide almacenarse en caché debido a una configuración de usuario en el servidor perimetral.              |Sí   | No  |
| RequestCountCacheUncacheable | Recuento de todas las solicitudes a recursos cuyos encabezados Cache-Control y Expires impiden que se almacenen en caché. Estos encabezados indican que no se deben almacenar en caché en un servidor POP o por el cliente HTTP.                |Sí   |No   |
| RequestCountCacheOthers | Recuento de todas las solicitudes con un estado de caché no cubierto por lo anterior.              |Sí   | No  |
| EgressTotal | Transferencia de datos salientes en GB              |Sí   |Sí   |
| EgressHttpStatus2xx | Transferencia de datos salientes* para respuestas con códigos de estado HTTP 2xx en GB            |Sí   |No   |
| EgressHttpStatus3xx | Transferencia de datos salientes para respuestas con códigos de estado HTTP 3xx en GB              |Sí   |No   |
| EgressHttpStatus4xx | Transferencia de datos de salida para respuestas con códigos de estado HTTP 4xx en GB               |Sí   | No  |
| EgressHttpStatus5xx | Transferencia de datos de salida para respuestas con códigos de estado HTTP 5xx en GB               |Sí   |  No |
| EgressHttpStatusOthers | Transferencia de datos de salida para respuestas con otros códigos de estado HTTP en GB                |Sí   |No   |
| EgressCacheHit |  Transferencia de datos de salida para respuestas que se entregaron directamente desde la caché de la red CDN en los servidores POP/perimetrales de la red CDN    |Sí   |  No |
| EgressCacheMiss | Transferencia de datos de salida para respuestas que no se encontraron en el servidor POP más cercano y que se recuperaron del servidor de origen              |Sí   |  No |
| EgressCacheNoCache | Transferencia de datos de salida para recursos a los que se les impide almacenarse en caché debido a una configuración de usuario en el servidor perimetral.                |Sí   |No   |
| EgressCacheUncacheable | Transferencia de datos de salida para recursos cuyos encabezados Cache-Control o Expires impiden que se almacenen en caché. Estos encabezados indican que no se deben almacenar en caché en un servidor POP o por el cliente HTTP.                    |Sí   | No  |
| EgressCacheOthers |  Transferencias de datos de salida para otros escenarios de caché.             |Sí   | No  |

*Con transferencia de datos de salida nos referimos al tráfico entregado al cliente desde los servidores POP de la red CDN.


### Esquema de los registros de análisis básico
<a id="schema-of-the-core-analytics-logs" class="xliff"></a> 

Todos los registros se almacenan en formato JSON y cada entrada tiene campos de cadena que siguen el siguiente esquema:

```json
    "records": [
        {
            "time": "2017-04-27T01:00:00",
            "resourceId": "<ARM Resource Id of the CDN Endpoint>",
            "operationName": "Microsoft.Cdn/profiles/endpoints/contentDelivery",
            "category": "CoreAnalytics",
            "properties": {
                "DomainName": "<Name of the domain for which the statistics is reported>",
                "RequestCountTotal": integer value,
                "RequestCountHttpStatus2xx": integer value,
                "RequestCountHttpStatus3xx": integer value,
                "RequestCountHttpStatus4xx": integer value,
                "RequestCountHttpStatus5xx": integer value,
                "RequestCountHttpStatusOthers": integer value,
                "RequestCountHttpStatus200": integer value,
                "RequestCountHttpStatus206": integer value,
                "RequestCountHttpStatus302": integer value,
                "RequestCountHttpStatus304": integer value,
                "RequestCountHttpStatus404": integer value,
                "RequestCountCacheHit": integer value,
                "RequestCountCacheMiss": integer value,
                "RequestCountCacheNoCache": integer value,
                "RequestCountCacheUncacheable": integer value,
                "RequestCountCacheOthers": integer value,
                "EgressTotal": double value,
                "EgressHttpStatus2xx": double value,
                "EgressHttpStatus3xx": double value,
                "EgressHttpStatus4xx": double value,
                "EgressHttpStatus5xx": double value,
                "EgressHttpStatusOthers": double value,
                "EgressCacheHit": double value,
                "EgressCacheMiss": double value,
                "EgressCacheNoCache": double value,
                "EgressCacheUncacheable": double value,
                "EgressCacheOthers": double value,
            }
        }

    ]
}
```

Donde "time" representa la hora de inicio del límite horario cuyas estadísticas se notifican. Tenga en cuenta que cuando un proveedor de CDN no admite una métrica, en lugar de un valor doble o entero, habrá un valor nulo. Este valor nulo indica la ausencia de una métrica, y esto es diferente de un valor de 0. Tenga en cuenta también que habrá un conjunto de estas métricas por dominio configurado en el punto de conexión.

Propiedades de ejemplo:

```json
{
     "DomainName": "manlingakamaitest2.azureedge.net",
     "RequestCountTotal": 480,
     "RequestCountHttpStatus2xx": 480,
     "RequestCountHttpStatus3xx": 0,
     "RequestCountHttpStatus4xx": 0,
     "RequestCountHttpStatus5xx": 0,
     "RequestCountHttpStatusOthers": 0,
     "RequestCountHttpStatus200": 480,
     "RequestCountHttpStatus206": 0,
     "RequestCountHttpStatus302": 0,
     "RequestCountHttpStatus304": 0,
     "RequestCountHttpStatus404": 0,
     "RequestCountCacheHit": null,
     "RequestCountCacheMiss": null,
     "RequestCountCacheNoCache": null,
     "RequestCountCacheUncacheable": null,
     "RequestCountCacheOthers": null,
     "EgressTotal": 0.09,
     "EgressHttpStatus2xx": null,
     "EgressHttpStatus3xx": null,
     "EgressHttpStatus4xx": null,
     "EgressHttpStatus5xx": null,
     "EgressHttpStatusOthers": null,
     "EgressCacheHit": null,
     "EgressCacheMiss": null,
     "EgressCacheNoCache": null,
     "EgressCacheUncacheable": null,
     "EgressCacheOthers": null
}

```
## Recursos adicionales
<a id="additional-resources" class="xliff"></a>

* [Registros de diagnóstico de Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs)
* [Análisis básico mediante el portal complementario de la red CDN](https://docs.microsoft.com/azure/cdn/cdn-analyze-usage-patterns)


