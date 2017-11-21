---
title: "Supervisión del uso y estadísticas en un servicio Azure Search | Microsoft Docs"
description: "Realice el seguimiento del consumo de recursos y el tamaño de índice para Azure Search, un servicio de búsqueda hospedado en la nube en Microsoft Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: required
ms.date: 11/09/2017
ms.author: heidist
ms.openlocfilehash: fe852afedfc1cce99d81b8ab53c6c80df34ac6d6
ms.sourcegitcommit: bc8d39fa83b3c4a66457fba007d215bccd8be985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2017
---
# <a name="monitoring-an-azure-search-service"></a>Supervisión de un servicio de Azure Search

Azure Search ofrece varios recursos para realizar el seguimiento del uso y rendimiento de los servicios de búsqueda. Proporciona acceso a métricas, registros, estadísticas de índices y funcionalidades de supervisión extendidas en Power BI. En este artículo se describe cómo habilitar las diferentes estrategias de supervisión y cómo interpretar los datos resultantes.

## <a name="azure-search-metrics"></a>Métricas de Azure Search
Las métricas ofrecen visibilidad casi en tiempo real de cualquier servicio de búsqueda y están disponibles para todos los servicios, no es preciso realizar ningún tipo de configuración adicional. Le permiten realizar el seguimiento del rendimiento del servicio durante 30 días.

Azure Search recopila datos de tres métricas diferentes:

* Latencia de búsqueda: tiempo que ha necesitado el servicio de búsqueda para procesar las consultas de búsqueda, agregadas por minuto.
* Consultas de búsqueda por segundo (QPS): número de consultas de búsqueda recibidas por segundo, agregadas por minuto.
* Porcentaje de consultas de búsqueda limitadas: porcentaje de consultas de búsqueda que se han limitado, agregadas por minuto.

![Captura de pantalla de la actividad de QPS][1]

### <a name="set-up-alerts"></a>Configuración de alertas
En la página de detalles de la métrica puede configurar alertas para desencadenar una notificación por correo electrónico o una acción automática cuando una métrica supera un umbral definido.

Para más información acerca de las métricas, consulte la documentación completa de Azure Monitor.  

## <a name="how-to-track-resource-usage"></a>Realización del seguimiento del uso de los recursos
Llevar un seguimiento del crecimiento de los índices y el tamaño de los documentos puede ayudarle a ajustar la capacidad de forma proactiva antes de alcanzar el límite superior que haya establecido para el servicio. También puede hacerlo en el portal o mediante programación con la API de REST.

### <a name="using-the-portal"></a>Uso del portal

Para supervisar el uso de recursos, y ver los recuentos y las estadísticas del servicio en el [portal](https://portal.azure.com).

1. Inicie sesión en el [portal](https://portal.azure.com).
2. Abra el panel del servicio Azure Search. Puede buscar los mosaicos del servicio en la página principal, o puede desplazarse hasta el servicio usando Explorar, en la barra de salto.

La sección Uso incluye un medidor que indica la parte de los recursos disponibles que está en uso. Para más información sobre los límites de cada servicio en cuanto a índices, documentos y almacenamiento, consulte [Límites de servicio](search-limits-quotas-capacity.md).

  ![Icono de Uso][2]

> [!NOTE]
> La captura de pantalla anterior es del servicio Gratuito, que tiene como máximo una réplica y una partición y solo puede hospedar 3 índices, 10 000 documentos o 50 MB de datos, lo que ocurra primero. Los servicios creados en un nivel Básico o Estándar tienen límites de servicio mucho mayores. Para más información sobre cómo elegir un nivel, consulte [Selección de un nivel o una SKU](search-sku-tier.md).
>
>

### <a name="using-the-rest-api"></a>Uso de la API de REST
La API de REST de Azure Search y el SDK para .NET proporcionan acceso mediante programación a las métricas del servicio.  Si está utilizando [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx) para cargar un índice desde Azure SQL Database o desde Azure Cosmos DB, está disponible una API adicional para obtener las cifras que necesita.

* [Obtención de estadísticas de índice](/rest/api/searchservice/get-index-statistics)
* [Recuento de documentos](/rest/api/searchservice/count-documents)
* [Obtención del estado del indizador](/rest/api/searchservice/get-indexer-status)

## <a name="how-to-export-logs-and-metrics"></a>Exportación de registros y métricas

Puede exportar los registros de operaciones de un servicio y los datos sin procesar de las métricas que se describen en la sección anterior. Los registros de operaciones le permiten saber cómo se utiliza el servicio y se pueden consumir desde Power BI cuando se copian datos en una cuenta de almacenamiento. Azure Search proporciona un paquete de contenido de supervisión de Power BI para este fin.


### <a name="enabling-monitoring"></a>Habilitación de la supervisión
Abra el servicio Azure Search en [Azure Portal](http://portal.azure.com), en la opción Habilitar supervisión.

Elija los datos que desea exportar: registros, métricas o ambos. Puede copiarlos en una cuenta de almacenamiento, enviarlos a un centro de eventos o exportarlo a Log Analytics.

![Habilitación de la supervisión en el portal][3]

Para habilitar el uso de PowerShell o la CLI de Azure, consulte la documentación [aquí](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs#how-to-enable-collection-of-diagnostic-logs).

### <a name="logs-and-metrics-schemas"></a>Registros y esquemas de métricas
Cuando los datos se copian en una cuenta de almacenamiento, adoptan el formato JSON y se colocan en dos contenedores:

* insights-logs-operationlogs: para los registros del tráfico de búsqueda
* insights-metrics-pt1m: para las métricas

Hay un blob, por hora y por contenedor.

Ruta de acceso de ejemplo: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

#### <a name="log-schema"></a>Esquema de registro
Los blobs de registros contienen los registros de tráfico del servicio de búsqueda.
Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.
Cada blob tiene registros en todas las operaciones que tuvieron lugar durante la misma hora.

| Nombre | Tipo | Ejemplo | Notas |
| --- | --- | --- | --- |
| Twitter en tiempo |datetime |"2015-12-07T00:00:43.6872559Z" |Marca de tiempo de la operación |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Su ResourceId |
| operationName |string |"Query.Search" |El nombre de la operación |
| operationVersion |string |"2015-02-28" |La versión de la API usada |
| categoría |string |"OperationLogs" |constant |
| resultType |string |"Success" |Valores posibles: Success o Failure |
| resultSignature |int |200 |Código de resultado HTTP |
| durationMS |int |50 |Duración de la operación en milisegundos |
| propiedades |objeto |consulte la tabla siguiente |Objeto que contiene datos específicos de la operación |

**Esquema de propiedades**
| Nombre | Tipo | Ejemplo | Notas |
| --- | --- | --- | --- |
| Description |string |"GET /indexes('content')/docs" |Punto de conexión de la operación |
| Consultar |string |"?search=AzureSearch&$count=true&api-version=2015-02-28" |Los parámetros de consulta |
| Documentos |int |42 |Número de documentos procesados |
| IndexName |string |"testindex" |Nombre del índice asociado a la operación |

#### <a name="metrics-schema"></a>Esquema de métricas
| Nombre | Tipo | Ejemplo | Notas |
| --- | --- | --- | --- |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/>MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |el identificador de recurso |
| metricName |string |"Latency" |el nombre de la métrica |
| Twitter en tiempo |datetime |"2015-12-07T00:00:43.6872559Z" |la marca de tiempo de la operación |
| average |int |64 |El valor de media de las muestras sin procesar en el intervalo de tiempo de la métrica |
| minimum |int |37 |El valor mínimo de las muestras sin procesar en el intervalo de tiempo de la métrica |
| maximum |int |78 |El valor máximo de las muestras sin procesar en el intervalo de tiempo de la métrica |
| total |int |258 |El valor total de las muestras sin procesar en el intervalo de tiempo de la métrica |
| count |int |4 |El número de muestras sin procesar usadas para generar la métrica |
| timegrain |string |"PT1M" |El intervalo de agregación de la métrica en ISO 8601 |

Todas las métricas se notifican en intervalos de un minuto. Cada métrica expone los valores mínimo, máximo y promedio por minuto.

En el caso de la métrica SearchQueriesPerSecond, el mínimo es el valor más bajo de las consultas de búsqueda por segundo que se registró en ese minuto. Lo mismo sucede con el valor máximo. El promedio es el agregado en todo el minuto.
Piense en este escenario durante un minuto: un segundo de carga elevada que es el máximo en SearchQueriesPerSecond, seguido de 58 segundos de carga media y, por último, 1 segundo con solo una consulta (que es el mínimo).

En el caso de ThrottledSearchQueriesPercentage, el mínimo, máximo, promedio y total tendrán el mismo valor: el porcentaje de consultas de búsqueda con limitaciones, del número total de consultas de búsqueda durante un minuto.

## <a name="analyzing-your-data-with-power-bi"></a>Análisis de datos con Power BI

Se recomienda usar [Power BI](https://powerbi.microsoft.com) para explorar y visualizar los datos. Se puede conectar fácilmente a su cuenta de Azure Storage y empezar rápidamente a analizar los datos.

Azure Search proporciona un [paquete de contenido de Power BI](https://app.powerbi.com/getdata/services/azure-search) que le permite supervisar y conocer el tráfico de búsqueda con las tablas y gráficos predefinidos. Contiene un conjunto de informes de Power BI que se conectan automáticamente a sus datos y proporcionan información visual sobre el servicio de búsqueda. Para más información, consulte la [página de ayuda del paquete de contenido](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-search/).

![Panel de Power BI para Azure Search][4]

## <a name="next-steps"></a>Pasos siguientes
Examine [Escalado de réplicas y particiones](search-limits-quotas-capacity.md) como guía saber cómo equilibrar la asignación de particiones y réplicas para un servicio existente.

Visite [Administración de servicios de Azure Search en Microsoft Azure](search-manage.md) para más información sobre la administración de servicios, o [Rendimiento y optimización](search-performance-optimization.md) para que le sirva de guía de ajuste.

Obtenga más información sobre cómo crear informes increíbles. Consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-getting-started/) para obtener más información

<!--Image references-->
[1]: ./media/search-monitor-usage/AzSearch-Monitor-BarChart.PNG
[2]: ./media/search-monitor-usage/AzureSearch-Monitor1.PNG
[3]: ./media/search-monitor-usage/AzureSearch-Enable-Monitoring.PNG
[4]: ./media/search-monitor-usage/AzureSearch-PowerBI-Dashboard.png
