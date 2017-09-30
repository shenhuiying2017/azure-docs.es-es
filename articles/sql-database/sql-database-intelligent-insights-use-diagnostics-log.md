---
title: "Registro de diagnóstico de rendimiento de Intelligent Insights: Azure SQL Database | Microsoft Docs"
description: "Intelligent Insights proporciona un registro de diagnóstico de los problemas de rendimiento de Azure SQL Database."
services: sql-database
documentationcenter: 
author: danimir
manager: drasumic
editor: carlrab
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: NA
ms.date: 09/25/2017
ms.author: v-daljep
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 4890baa4ead3323834a82b3f9340cf751bf0c755
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="use-intelligent-insights-azure-sql-database-performance-diagnostics-log"></a>Use el registro de diagnóstico de rendimiento de Azure SQL Database de Intelligent Insights.

Esta página proporciona información sobre cómo usar el registro de diagnóstico de rendimiento de Azure SQL Database generado por [Intelligent Insights](sql-database-intelligent-insights.md), el formato y los datos que contiene según sus necesidades de desarrollo personalizadas. Este registro de diagnóstico se puede enviar a [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage) o a una solución de terceros para las funcionalidades personalizadas de informes y alertas de DevOps.

## <a name="log-header"></a>Encabezado de registro

El registro de diagnóstico usa el formato estándar de JSON para generar los resultados de Intelligent Insights. La propiedad de categoría exacta para acceder al registro de Intelligent Insights es el valor fijo &#8220;**SQLInsights**&#8221;.

El encabezado del registro es común y consta de la marca de tiempo (TimeGenerated) cuando se crea una entrada, que incluye un identificador de recurso (ResourceId) que hace referencia a una instancia concreta de Azure SQL Database con la que se relaciona la entrada. La categoría (Category), el nivel (Level) y el nombre de la operación (OperationName) son propiedades fijas cuyo valor no cambia: indican que la entrada de registro es informativa y que provienen de Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Identificador del problema y base de datos afectada

La propiedad de identificación del problema (issueId_d) proporciona un método exclusivo de seguimiento de los problemas de rendimiento hasta su resolución. Intelligent Insights observa el ciclo de vida de cada problema como: activo (Active), comprobando (Verifying) y completado (Completed). Con cada una de estas fases de estado, Intelligent Insights puede registrar varios registros de eventos en el registro, y para cada una de estas entradas, el número de identificador del problema permanece único. Intelligent Insights realiza un seguimiento del problema a través de su ciclo de vida y genera datos en el registro de diagnóstico cada 15 minutos.

Una vez que se detecta un problema de rendimiento y mientras existe, el problema se notifica como "Active" en la propiedad de estado (Status). Una vez que se mitiga un problema detectado, se comprueba y notifica como "Verifying" en la propiedad de estado (Status). En el caso de que el problema ya no exista, la propiedad de estado (Status) informa de este problema como "Completed".
Junto con el identificador del problema, el registro de diagnóstico notifica las marcas de tiempo de inicio (intervalStartTime_t) y finalización (intervalEndTme_t) del evento concreto relacionado con un problema notificado en el registro de diagnóstico.

El grupo elástico de propiedad (elasticPoolName_s) indica a qué grupo elástico pertenece la base de datos con un problema. Si la base de datos no forma parte de ningún grupo elástico, esta propiedad no tiene ningún valor. La base de datos en la que se detecta un problema se divulga en la propiedad del nombre de la base de datos (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported timestamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported timestamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable) 
"databaseName_s" : "db_name",  // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: Active, Verifying and Complete
```

## <a name="detected-issues"></a>Problemas detectados

La sección siguiente del registro de rendimiento de Intelligent insights contiene problemas de rendimiento detectados mediante la inteligencia artificial integrada. Las detecciones se divulgan dentro de las detecciones de la propiedad JSON que constan de la categoría de un problema, el impacto de los problemas, las consultas afectadas y las métricas. Debe tenerse en cuenta que la propiedad de detecciones puede contener varios problemas de rendimiento detectados.

Los problemas de rendimiento detectados se notifican con la siguiente estructura de propiedad de las detecciones:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // tperformance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}] 
```

En la tabla siguiente se proporcionan los patrones de rendimiento detectables y los detalles que contiene el registro de diagnóstico.

### <a name="detection-category"></a>Categoría de detección

La propiedad de categoría (Category) describe la categoría de los patrones de rendimiento detectables. Consulte la siguiente tabla para ver todas las categorías posibles de patrones de rendimiento detectables. Puede encontrar más detalles en la página [Troubleshoot database performance issues with Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md) (Solucionar problemas de rendimiento de la base de datos con Intelligent Insights).

En función del problema de rendimiento detectado, los detalles que incluye el archivo de registro de diagnóstico difieren en consecuencia.

| Patrones de rendimiento detectables | Detalles generados |
| :------------------- | ------------------- |
| Alcance de los límites de recursos | <li>Recursos afectados</li><li>Códigos hash de consulta</li><li>Porcentaje de consumo del recurso</li> |
| Aumento de la carga de trabajo | <li>Número de consultas cuya ejecución aumentó</li><li>Códigos hash de las consultas que contribuyen más al aumento de la carga de trabajo</li> |
| Presión de memoria | <li>Distribuidor de memoria</li> |
| Bloqueo | <li>Códigos hash de consulta afectados</li><li>Bloqueo de los códigos hash de consulta</li> |
| Aumento de MAXDOP | <li>Códigos hash de consulta</li><li>Tiempos de espera de CXP</li><li>Tiempos de espera</li> |
| Contención de PAGELATCH | <li>Códigos hash de las consultas que provocan la contención</li> |
| Carencia de un índice | <li>Códigos hash de consulta</li> |
| Nueva consulta | <li>Hash de consulta de las nuevas consultas</li> |
| Estadística de espera inusual | <li>Tipos de espera inusuales</li><li>Códigos hash de consulta</li><li>Tiempos de espera de la consulta</li> |
| Contención de TempDB | <li>Códigos hash de las consultas que provocan la contención</li><li>Atribución de consulta al tiempo de espera de contención de PAGELATCH de base de datos general [%]</li> |
| Escasez de DTU en el grupo elástico | <li>Grupo elástico</li><li>Base de datos que más consume DTU</li><li>Porcentaje de DTU de grupo que usa el mayor consumidor</li> |
| Regresión de un plan | <li>Hash de consulta</li><li>Identificadores de planes correctos</li><li>Identificadores de planes inadecuados</li><li>Códigos hash de consulta</li> |
| Cambio del valor de configuración de ámbito de base de datos | <li>Cambios en la configuración de ámbito de base de datos en comparación con los valores predeterminados</li> |
| Cliente lento | <li>Códigos hash de consulta</li><li>Tiempos de espera</li> |
| Degradación del plan de tarifa | <li>Notificación de texto</li> |

### <a name="impact"></a>Impacto

La propiedad de impacto (Impact) describe en qué medida contribuye un comportamiento detectado en el problema que experimenta la base de datos. Oscila entre 1 y 3, donde 3 es la contribución más alta, 2 es moderada y 1 es la contribución más baja. El valor de impacto podría usarse como entrada para la automatización personalizada de las alertas, en función de sus necesidades específicas. La propiedad de consultas afectadas (QueryHashes) proporciona una lista de códigos hash de consulta que se vieron afectados por una detección particular.

### <a name="impacted-queries"></a>Consultas afectadas

En la sección siguiente del registro de Intelligent Insights se proporciona información sobre consultas concretas que resultaron afectadas por los problemas de rendimiento detectados. Esta información se divulga como una matriz de objetos insertados en la propiedad impact_s. La propiedad Impact consta de entidades y métricas. Las entidades hacen referencia a una consulta determinada (type: Query) y al hash de consulta único divulgado en el valor de propiedad (Value). Además, cada una de las consultas divulgadas va seguida de una métrica y un valor, que indican que se detectó un problema de rendimiento.

En el siguiente ejemplo de registro, se puede observar que se detectó un aumento en la duración de la ejecución de la consulta con el hash 0x9102EXZ4 (métrica: DurationIncreaseSeconds) con el valor de 110 segundos, que indica que esta consulta en particular tardó 110 segundos más en ejecutarse. Debe tenerse en cuenta que se pueden detectar varias consultas debido a que esta sección de registro concreta puede incluir varias entradas de consulta.

```json
"impact" : [{
"entity" : { 
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Métricas

La unidad de medida de cada métrica notificada se proporciona en virtud de la propiedad de métrica (Metric) con los valores posibles: segundos, número y porcentaje. El valor de una métrica medida se notifica en la propiedad de valor (Value).

La propiedad DurationIncreaseSeconds proporciona la unidad de medida en segundos, y para CriticalErrorCount, la unidad de medida es un número que representa un recuento de errores.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Recomendaciones de mejoras y análisis de la causa principal

La última parte del registro de rendimiento de Intelligent Insights pertenece al análisis automatizado de la causa principal del problema de degradación de rendimiento identificado en un vocabulario comprensible a través de la propiedad de análisis de la causa principal (rootCauseAnalysis_s). Se incluyen recomendaciones de mejora en las palabras del registro en los casos en que esto es posible.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

El registro de rendimiento de Intelligent Insights se puede usar con [Azure Log Analytics]( https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql) o una solución de terceros para las funcionalidades personalizadas de informes y alertas de DevOps.

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información acerca de los conceptos de [Intelligent Insights](sql-database-intelligent-insights.md).
- Aprenda a [solucionar problemas de rendimiento de Azure SQL Database con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
- Aprenda a [supervisar Azure SQL Database mediante Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- [Recopile y use los datos de registro provenientes de los recursos de Azure](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).




