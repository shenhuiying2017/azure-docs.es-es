---
title: "Análisis de tráfico de búsqueda para Azure Search | Microsoft Docs"
description: "Habilite el análisis de tráfico de búsqueda para Búsqueda de Azure, un servicio de búsqueda hospedado en la nube en Microsoft Azure, para descubrir información acerca de los usuarios y los datos."
services: search
documentationcenter: 
author: bernitorres
manager: pablocas
editor: 
ms.assetid: b31d79cf-5924-4522-9276-a1bb5d527b13
ms.service: search
ms.devlang: multiple
ms.workload: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/22/2017
ms.author: betorres
translationtype: Human Translation
ms.sourcegitcommit: cdcf121e52eaf6a1fc45194b7a4f5a02e9e5c001
ms.openlocfilehash: f6b354caf37f94906865b5a2f334e2b7a02f9d5b

---

# <a name="enabling-and-using-search-traffic-analytics"></a>Habilitación y uso de Análisis de tráfico de búsqueda
Análisis de tráfico de búsqueda es una característica de Azure Search que permite tener visibilidad en el servicio de búsqueda y descubrir información acerca de los usuarios y su comportamiento. Cuando se habilita esta característica, los datos del servicio de búsqueda se copian en una cuenta de almacenamiento de su elección. Estos datos incluyen los registros del servicio de búsqueda y las métricas operacionales agregadas que puede procesar y manipular para su posterior análisis.

## <a name="how-to-enable-search-traffic-analytics"></a>Habilitación de Análisis de tráfico de búsqueda
Necesita una cuenta de Azure Storage en la misma región y suscripción que el servicio de búsqueda.

> [!IMPORTANT]
> Se aplican gastos estándares para esta cuenta de almacenamiento
>
>

Análisis de tráfico de búsqueda se puede habilitar en el portal o a través de PowerShell. Una vez habilitado, los datos comenzarán a llegar a su cuenta de almacenamiento en un período de 5 a 10 minutos, en concreto a estos dos contenedores de blobs:

    insights-logs-operationlogs: search traffic logs
    insights-metrics-pt1m: aggregated metrics


### <a name="a-using-the-portal"></a>A. Uso del portal
Abra el servicio Azure Search en [Azure Portal](http://portal.azure.com). En Configuración, busque la opción Análisis de tráfico de búsqueda.

![][1]

Cambie el estado a **Activado**, seleccione la cuenta Azure Storage que se vaya a usar y elija los datos que desee copiar: registros, métricas o ambos. Se recomienda copiar los registros y métricas.
Puede establecer la directiva de retención de los datos entre 1 y 365 días. Para conservar los datos de forma indefinida, establezca el período de retención (días) en 0.

![][2]

### <a name="b-using-powershell"></a>B. Con PowerShell
Primero, asegúrese de que ha instalado los [cmdlets de Azure PowerShell](https://github.com/Azure/azure-powershell/releases) más recientes.

A continuación, obtenga los identificadores de recursos para el servicio de búsqueda y la cuenta de almacenamiento. Puede buscarlos en el portal. Para ello, vaya a Configuración -> Propiedades -> ResourceId.

![][3]

```PowerShell
Login-AzureRmAccount
$SearchServiceResourceId = "Your Search service resource id"
$StorageAccountResourceId = "Your Storage account resource id"
Set-AzureRmDiagnosticSetting -ResourceId $SearchServiceResourceId StorageAccountId $StorageAccountResourceId -Enabled $true
```

## <a name="understanding-the-data"></a>Descripción de los datos
Los datos se almacenan en blobs de Almacenamiento de Azure con formato JSON.

Hay un blob, por hora y por contenedor.

Ruta de acceso de ejemplo: `resourceId=/subscriptions/<subscriptionID>/resourcegroups/<resourceGroupName>/providers/microsoft.search/searchservices/<searchServiceName>/y=2015/m=12/d=25/h=01/m=00/name=PT1H.json`

### <a name="logs"></a>Registros
Los blobs de registros contienen los registros de tráfico del servicio de búsqueda.
Cada blob tiene un objeto raíz llamado **registros** que contiene una matriz de objetos de registro.
Cada blob tiene registros en todas las operaciones que tuvieron lugar durante la misma hora.

#### <a name="log-schema"></a>Esquema de registro
| Nombre | Tipo | Ejemplo | Notas |
| --- | --- | --- | --- |
| Twitter en tiempo |datetime |"2015-12-07T00:00:43.6872559Z" |Marca de tiempo de la operación |
| resourceId |string |"/SUBSCRIPTIONS/11111111-1111-1111-1111-111111111111/<br/>RESOURCEGROUPS/DEFAULT/PROVIDERS/<br/> MICROSOFT.SEARCH/SEARCHSERVICES/SEARCHSERVICE" |Su ResourceId |
| operationName |string |"Query.Search" |El nombre de la operación |
| operationVersion |string |"2016-09-01" |La versión de la API usada |
| categoría |string |"OperationLogs" |constant |
| resultType |string |"Success" |Valores posibles: Success o Failure |
| resultSignature |int |200 |Código de resultado HTTP |
| durationMS |int |50 |Duración de la operación en milisegundos |
| propiedades |objeto |consulte la tabla siguiente |Objeto que contiene datos específicos de la operación |

#### <a name="properties-schema"></a>Esquema de propiedades
| Nombre | Tipo | Ejemplo | Notas |
| --- | --- | --- | --- |
| Description |string |"GET /indexes('content')/docs" |Punto de conexión de la operación |
| Consultar |string |"?search=AzureSearch&$count=true&api-version=2016-09-01" |Los parámetros de consulta |
| Documentos |int |42 |Número de documentos procesados |
| IndexName |string |"testindex" |Nombre del índice asociado a la operación |

### <a name="metrics"></a>Métricas
Los blobs de métricas contienen valores agregados para el servicio de búsqueda.
Cada archivo tiene un objeto raíz llamado " **registros** " que contiene una matriz de objetos de métricas. Este objeto raíz contiene las métricas de cada minuto durante el que estuvieron disponibles los datos.

Métricas disponibles:

* SearchLatency: tiempo que necesita el servicio para procesar las consultas de búsqueda (agregado por minuto).
* SearchQueriesPerSecond: número de consultas de búsqueda recibidas por segundo (agregado por minuto).
* ThrottledSearchQueriesPercentage: porcentaje de consultas de búsqueda con limitaciones (agregado por minuto).

> [!IMPORTANT]
> Las limitaciones se producen cuando se envían demasiadas consultas, con lo que se agota la capacidad de los recursos aprovisionados del servicio. Considere la posibilidad de agregar más réplicas al servicio.
>
>

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

## <a name="analyzing-your-data"></a>Análisis de datos
Los datos están en su propia cuenta de almacenamiento. Le animamos a explorar estos datos de la manera más adecuada para su caso.

Como punto de partida, se recomienda usar [Power BI](https://powerbi.microsoft.com) para explorar y visualizar los datos. Puede conectarse fácilmente a su cuenta de Almacenamiento de Azure y comenzar rápidamente a analizar los datos.

#### <a name="power-bi-online"></a>Power BI en línea
[Paquete de contenido de Power BI](https://app.powerbi.com/getdata/services/azure-search): cree un panel de Power BI y un conjunto de informes de Power BI que muestren los datos automáticamente y proporcionen información visual sobre el servicio de búsqueda. Consulte la [página de ayuda sobre el paquete de contenido](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-search/).

![][4]

#### <a name="power-bi-desktop"></a>Power BI Desktop
[Power BI Desktop](https://powerbi.microsoft.com/en-us/desktop): explore los datos y cree sus propias visualizaciones de los datos. Consulte la consulta de inicio en la sección siguiente:

1. Abra un nuevo informe de PowerBI Desktop.

2. Seleccione Obtener datos -> Más...

    ![][5]

3. Seleccione Microsoft Azure Blob Storage y Conectar.

    ![][6]

4. Especifique el nombre y la clave de cuenta de la cuenta de almacenamiento.

5. Seleccione "insight-logs-operationlogs" e "insights-metrics-pt1m" y haga clic en Editar.

6. Cuando se abra el Editor de consultas, asegúrese de que "insight-logs-operationlogs" está seleccionado a la izquierda. Ahora abra el Editor avanzado seleccionando Ver -> Editor avanzado.

    ![][7]
    
7. Mantenga las dos primeras líneas y reemplace el resto por la consulta siguiente:

   ~~~~
   > # "insights-logs-operationlogs" = Source{[Name="insights-logs-operationlogs"]}[Data],
   > # "Sorted Rows" = Table.Sort(#"insights-logs-operationlogs",{{"Date modified", Order.Descending}}),
   > # "Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # "Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # "Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # "Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # "Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # "Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}, {"time", "resourceId", "operationName", "operationVersion", "category", "resultType", "resultSignature", "durationMS", "properties"}),
   > # "Expanded properties" = Table.ExpandRecordColumn(#"Expanded records1", "properties", {"Description", "Query", "IndexName", "Documents"}, {"Description", "Query", "IndexName", "Documents"}),
   > # "Renamed Columns" = Table.RenameColumns(#"Expanded properties",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"operationName", "OperationName"}, {"operationVersion", "OperationVersion"}, {"category", "Category"}, {"resultType", "ResultType"}, {"resultSignature", "ResultSignature"}, {"durationMS", "Duration"}}),
   > # "Added Custom2" = Table.AddColumn(#"Renamed Columns", "QueryParameters", each Uri.Parts("http://tmp" & [Query])),
   > # "Expanded QueryParameters" = Table.ExpandRecordColumn(#"Added Custom2", "QueryParameters", {"Query"}, {"Query.1"}),
   > # "Expanded Query.1" = Table.ExpandRecordColumn(#"Expanded QueryParameters", "Query.1", {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}, {"search", "$skip", "$top", "$count", "api-version", "searchMode", "$filter"}),
   > # "Removed Columns1" = Table.RemoveColumns(#"Expanded Query.1",{"OperationVersion"}),
   > # "Changed Type" = Table.TransformColumnTypes(#"Removed Columns1",{{"Datetime", type datetimezone}, {"ResourceId", type text}, {"OperationName", type text}, {"Category", type text}, {"ResultType", type text}, {"ResultSignature", type text}, {"Duration", Int64.Type}, {"Description", type text}, {"Query", type text}, {"IndexName", type text}, {"Documents", Int64.Type}, {"search", type text}, {"$skip", Int64.Type}, {"$top", Int64.Type}, {"$count", type logical}, {"api-version", type text}, {"searchMode", type text}, {"$filter", type text}}),
   > # "Inserted Date" = Table.AddColumn(#"Changed Type", "Date", each DateTime.Date([Datetime]), type date),
   > # "Duplicated Column" = Table.DuplicateColumn(#"Inserted Date", "ResourceId", "Copy of ResourceId"),
   > # "Split Column by Delimiter" = Table.SplitColumn(#"Duplicated Column","Copy of ResourceId",Splitter.SplitTextByEachDelimiter({"/"}, null, true),{"Copy of ResourceId.1", "Copy of ResourceId.2"}),
   > # "Changed Type1" = Table.TransformColumnTypes(#"Split Column by Delimiter",{{"Copy of ResourceId.1", type text}, {"Copy of ResourceId.2", type text}}),
   > # "Removed Columns2" = Table.RemoveColumns(#"Changed Type1",{"Copy of ResourceId.1"}),
   > # "Renamed Columns1" = Table.RenameColumns(#"Removed Columns2",{{"Copy of ResourceId.2", "ServiceName"}}),
   > # "Lowercased Text" = Table.TransformColumns(#"Renamed Columns1",{{"ServiceName", Text.Lower}}),
   > # "Added Custom" = Table.AddColumn(#"Lowercased Text", "DaysFromToday", each Duration.Days(DateTimeZone.UtcNow() - [Datetime])),
   > # "Changed Type2" = Table.TransformColumnTypes(#"Added Custom",{{"DaysFromToday", Int64.Type}})
   > in
   >
   > # "Changed Type2"
   >
   ~~~~

8. Haga clic en Done (Listo).

9. Seleccione ahora "insights-metrics-pt1m" en la consulta que se encuentra menos a la izquierda y abra el editor avanzado de nuevo. Mantenga las dos primeras líneas y reemplace el resto por la consulta siguiente:

   ~~~~
   > # "insights-metrics-pt1m1" = Source{[Name="insights-metrics-pt1m"]}[Data],
   > # "Sorted Rows" = Table.Sort(#"insights-metrics-pt1m1",{{"Date modified", Order.Descending}}),
   > # "Kept First Rows" = Table.FirstN(#"Sorted Rows",744),
   > # "Removed Columns" = Table.RemoveColumns(#"Kept First Rows",{"Name", "Extension", "Date accessed", "Date modified", "Date created", "Attributes", "Folder Path"}),
   > # "Parsed JSON" = Table.TransformColumns(#"Removed Columns",{},Json.Document),
   > # "Expanded Content" = Table.ExpandRecordColumn(#"Parsed JSON", "Content", {"records"}, {"records"}),
   > # "Expanded records" = Table.ExpandListColumn(#"Expanded Content", "records"),
   > # "Expanded records1" = Table.ExpandRecordColumn(#"Expanded records", "records", {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}, {"resourceId", "metricName", "time", "average", "minimum", "maximum", "total", "count", "timeGrain"}),
   > # "Filtered Rows" = Table.SelectRows(#"Expanded records1", each ([metricName] = "Latency")),
   > # "Removed Columns1" = Table.RemoveColumns(#"Filtered Rows",{"timeGrain"}),
   > # "Renamed Columns" = Table.RenameColumns(#"Removed Columns1",{{"time", "Datetime"}, {"resourceId", "ResourceId"}, {"metricName", "MetricName"}, {"average", "Average"}, {"minimum", "Minimum"}, {"maximum", "Maximum"}, {"total", "Total"}, {"count", "Count"}}),
   > # "Changed Type" = Table.TransformColumnTypes(#"Renamed Columns",{{"ResourceId", type text}, {"MetricName", type text}, {"Datetime", type datetimezone}, {"Average", type number}, {"Minimum", Int64.Type}, {"Maximum", Int64.Type}, {"Total", Int64.Type}, {"Count", Int64.Type}}),
   > Rounding = Table.TransformColumns(#"Changed Type",{{"Average", each Number.Round(_, 2)}}),
   >
   > # "Changed Type1" = Table.TransformColumnTypes(Rounding,{{"Average", type number}}),
   > # "Inserted Date" = Table.AddColumn(#"Changed Type1", "Date", each DateTime.Date([Datetime]), type date)
   > in
   >
   > # "Inserted Date"
   >
   ~~~~

10. Haga clic en Listo y seleccione Cerrar y aplicar en la pestaña Inicio.

11. Los datos de los últimos 30 días están ahora listos para su consumo. Prosiga y cree unas cuantas [visualizaciones](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-report-view/).

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la sintaxis de búsqueda y los parámetros de consulta. Vea [Búsqueda de documentos (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx) para información detallada.

Obtenga más información sobre cómo crear informes increíbles. Consulte [Introducción a Power BI Desktop](https://powerbi.microsoft.com/en-us/documentation/powerbi-desktop-getting-started/) para obtener más información

<!--Image references-->

[1]: ./media/search-traffic-analytics/SettingsBlade.png
[2]: ./media/search-traffic-analytics/DiagnosticsBlade.png
[3]: ./media/search-traffic-analytics/ResourceId.png
[4]: ./media/search-traffic-analytics/Dashboard.png
[5]: ./media/search-traffic-analytics/GetData.png
[6]: ./media/search-traffic-analytics/BlobStorage.png
[7]: ./media/search-traffic-analytics/QueryEditor.png



<!--HONumber=Jan17_HO4-->


