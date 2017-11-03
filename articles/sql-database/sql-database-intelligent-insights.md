---
title: "Supervisión de uso de bases de datos de Intelligent Insight: Azure SQL Database | Microsoft Docs"
description: Azure SQL Database Intelligent Insights usa inteligencia integrada para supervisar continuamente el uso de la base de datos mediante inteligencia artificial y detectar eventos potencialmente perjudiciales que provoquen un rendimiento bajo.
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
ms.openlocfilehash: 7e56baab6ae161736b05ea53b59c9e802a94633e
ms.sourcegitcommit: 5d772f6c5fd066b38396a7eb179751132c22b681
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="intelligent-insights"></a>Intelligent Insights

Azure SQL Database Intelligent Insights le permite saber lo que ocurre con el rendimiento de la base de datos.

Intelligent Insights usa inteligencia integrada para supervisar continuamente el uso de la base de datos mediante inteligencia artificial y detectar eventos potencialmente perjudiciales que provoquen un rendimiento bajo. Una vez detectados, se realiza un análisis detallado que genera un registro de diagnóstico con una evaluación inteligente del problema. Esta evaluación está formada por un análisis de la causa raíz del problema de rendimiento de la base de datos y, si es posible, recomendaciones para mejorar el rendimiento. 

## <a name="what-can-intelligent-insights-do-for-you"></a>¿Qué puede hacer Intelligent Insights por usted?

Intelligent Insights es una funcionalidad única de inteligencia integrada de Azure que permite lo siguiente:

- Supervisión proactiva
- Recomendaciones del rendimiento adaptadas
- Detección temprana de degradación del rendimiento de la base de datos
- El análisis de la causa raíz de los problemas detectados
- Recomendaciones de mejora del rendimiento
- Escalación horizontal de la capacidad en cientos de miles de bases de datos
- Impacto positivo para los recursos de DevOps y el costo total de propiedad

## <a name="how-does-intelligent-insights-work"></a>¿Cómo funciona Intelligent Insights?

Intelligent Insights analiza el rendimiento de SQL Database comparando la carga de trabajo de la base de datos de la última hora con la de referencia de los últimos siete días. La carga de trabajo de la base de datos se compone de consultas determinadas para que sean las más significativas para el rendimiento de la base de datos, como las consultas más repetidas y de mayor tamaño. Dado que cada base de datos es única según su estructura, datos, uso y aplicación, cada referencia de carga de trabajo generada es específica y única para una instancia individual. Intelligent Insights, que es independiente de la referencia de la carga de trabajo, también supervisa umbrales operativos absolutos y detecta problemas con los tiempos de espera excesivos, las excepciones críticas y los problemas con parametrizaciones de consulta que podrían afectar al rendimiento.

Una vez detectado un problema de degradación del rendimiento de varias métricas observadas mediante el uso de inteligencia artificial, se lleva a cabo el análisis. Se genera un registro de diagnóstico con una valoración inteligente de lo que sucede con la base de datos. Intelligent Insights facilita la realización de un seguimiento del problema de rendimiento de la base de datos desde su primera aparición hasta la resolución. Cada problema detectado se sigue a través de su ciclo de vida desde la detección inicial del mismo y la comprobación de la mejora en el rendimiento hasta su finalización. Las actualizaciones se proporcionan en el registro de diagnóstico cada 15 minutos. 

![Flujo de trabajo del análisis del rendimiento de las bases de datos](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)

Las métricas usadas para medir y detectar problemas de rendimiento de las bases de datos se basan en la duración de las consultas, las solicitudes con tiempo de espera agotado, tiempos de espera excesivos y solicitudes con errores. Para más información sobre las métricas, vea la sección [Métricas de detección](sql-database-intelligent-insights.md#detection-metrics) de este documento.

## <a name="degradations-detected"></a>Degradaciones detectadas

Las degradaciones en el rendimiento de Azure SQL Database se guardan en el registro de diagnóstico con entradas inteligentes que tienen las siguientes propiedades:

| Propiedad             | Detalles              |
| :------------------- | ------------------- |
| Información de base de datos | Metadatos acerca de una base de datos en la que se ha detectado una recomendación, por ejemplo, un URI de recurso. |
| Intervalo de tiempo observado | Hora inicial y final del período de la recomendación detectada. |
| Métricas afectadas | Métricas que provocan que se genere una recomendación: <ul><li>Aumento de la duración de la consulta [segundos].</li><li>Tiempo de espera excesivo [segundos].</li><li>Solicitudes con tiempo de espera agotado [porcentaje].</li><li>Solicitudes cerradas por un error [porcentaje].</li></ul>|
| Valor del impacto | Valor de una métrica medida. |
| Consultas afectadas y códigos de error | Código de error o hash de consulta. Se pueden usar para realizar una correlación fácilmente en las consultas afectadas. Se proporcionan métricas que consisten en un aumento de la duración de la consulta, tiempo de espera, recuentos de tiempo de espera o códigos de error. |
| Detecciones | Detección identificada en la base de datos durante el tiempo de un evento. Existen 15 patrones de detección. Para más información, consulte [Troubleshoot database performance issues with Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md) (Solución de problemas de rendimiento de la base de datos con Intelligent Insights). |
| Análisis de la causa raíz | Análisis de la causa raíz (RCA) del problema identificado en un formato legible. Algunas recomendaciones podrían contener una recomendación de mejora del rendimiento si es posible. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-complete"></a>Ciclo de vida del estado de los problemas: "Activo", "Comprobando" y "Completado"

Los problemas de rendimiento que se guardan en el registro de diagnóstico se marcan con uno de estos tres estados de ciclo de vida del problema: "Activo", "Comprobando" y "Completado". Una vez que se detecta un problema de rendimiento y siempre que la inteligencia integrada de SQL Database lo considere presente, el problema se marca como "Activo". Cuando el problema se considera mitigado, se comprueba y su estado se cambia a "Comprobando". Cuando la inteligencia integrada de SQL Database considera el problema resuelto, el estado del problema se marca como "Completado".

## <a name="use-intelligent-insights"></a>Intelligent Insights

Puede enviar el registro de diagnósticos de Intelligent Insights a Azure Log Analytics, Azure Event Hubs y Azure Storage. Para más información, consulte [Métricas y registros de diagnóstico de Azure SQL Database](sql-database-metrics-diag-logging.md). Una vez que envíe el registro a uno de estos destinos, se puede usar para desarrollar mecanismos personalizados de supervisión y alerta con herramientas de Microsoft o de terceros. 

Para más información sobre la solución de problemas de rendimiento de SQL Database con Intelligent Insights, vea [Solución de problemas de rendimiento de Azure SQL Database con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="built-in-intelligent-insights-analytics-with-log-analytics"></a>Análisis de Intelligent Insights integrado con Log Analytics 

La solución Log Analytics ofrece funcionalidades de informes y alertas en los datos de registro de diagnóstico de Intelligent Insights más importantes. El ejemplo siguiente muestra un informe de Intelligent Insights en Azure SQL Analytics:

![Informe de Intelligent Insights](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Una vez que se ha configurado el registro de diagnóstico de Intelligent Insights para transmitir datos a SQL Analytics, también puede [supervisar SQL Database mediante SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Integraciones personalizadas del registro de Intelligent Insights

Para más información sobre el desarrollo de mecanismos personalizados de supervisión y alerta, con herramientas de Microsoft o de terceros, vea [Use Intelligent Insights database performance diagnostics log](sql-database-intelligent-insights-use-diagnostics-log.md) (Registro de diagnóstico del rendimiento de bases de datos de Intelligent Insights).

## <a name="set-up-intelligent-insights-with-event-hubs"></a>Configuración de Intelligent Insights con Event Hubs

- Para configurar Intelligent Insights para transmitir los eventos de registro a Event Hubs, vea [Transmisión de registros de diagnóstico de Azure a un espacio de nombres de Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- Para usar Event Hubs para la supervisión y alerta personalizadas, vea [Qué hacer con las métricas y registros de diagnóstico en Event Hubs](sql-database-metrics-diag-logging.md#what-to-do-with-metrics-and-diagnostics-logs-in-event-hubs). 

## <a name="set-up-intelligent-insights-with-storage"></a>Configuración de Intelligent Insights con Storage

- Para configurar Intelligent Insights para que se almacene con Azure Storage, vea [Transmisión a Azure Storage](sql-database-metrics-diag-logging.md#stream-into-storage).

## <a name="detection-metrics"></a>Métricas de detección

Las métricas utilizadas para los modelos de detección que generan Intelligent Insights se basan en la supervisión:

- Duración de la consulta
- Solicitudes con tiempo de espera
- Tiempo de espera excesivo
- Solicitudes cerradas por un error

Duración de consulta y Solicitudes con tiempo de espera se usan como modelos principales en la detección de problemas con el rendimiento de carga de trabajo de la base de datos. Se usan porque miden directamente lo que ocurre con la carga de trabajo. Para detectar todos los casos posibles de degradación del rendimiento de la carga de trabajo, se usan solicitudes cerradas por un error y tiempo de espera excesivo como modelos adicionales para indicar los problemas que afectan al rendimiento de la carga de trabajo.

El sistema considera automáticamente los cambios en la carga de trabajo y en el número de solicitudes de consulta realizadas a la base de datos para determinar de forma dinámica los umbrales de rendimiento de la base de datos normales y fuera de lo normal.

Se consideran todas las métricas juntas en distintas relaciones a través de un modelo de datos derivado científicamente que categoriza cada problema de rendimiento detectado. La información proporcionada a través de una recomendación inteligente incluye:

* Detalles del problema de rendimiento detectado. 
* Análisis de la causa raíz del problema detectado. 
* Recomendaciones sobre cómo mejorar el rendimiento de la base de datos SQL supervisada, siempre que sea posible.

## <a name="query-duration"></a>Duración de la consulta

El modelo de degradación de la duración de la consulta analiza las consultas individuales y detecta el aumento del tiempo que se tarda en compilar y ejecutar una consulta en comparación con la línea base de rendimiento.

Si la inteligencia integrada de SQL Database detecta un aumento significativo en la compilación de consultas o en su tiempo de ejecución que afecta al rendimiento de la carga de trabajo, estas consultas se marcan como problema de degradación del rendimiento de duración de la consulta. 

El registro de diagnósticos de Intelligent Insights da como resultado el hash de consulta de la consulta degradada en el rendimiento. El hash de consulta indica si la degradación del rendimiento se relaciona con el aumento en el tiempo de ejecución o de compilación de la consulta, que aumentó la duración de la consulta.

## <a name="timeout-requests"></a>Solicitudes con tiempo de espera

El modelo de degradación de solicitudes de tiempo de espera analiza las consultas individuales y detecta un aumento en los tiempos de espera en el nivel de ejecución de la consulta y los tiempos de espera de solicitud totales en el nivel de base de datos en comparación con el período de línea base de rendimiento.

El tiempo de espera de algunas consultas podría incluso superarse antes de que lleguen a la fase de ejecución. A través de la comparación entre los trabajos anulados frente a las solicitudes realizadas, la inteligencia integrada de SQL Database mide y analiza todas las consultas que llegaron a la base de datos y si entraron en la fase de ejecución o no. 

Cuando el tiempo de espera de las consultas ejecutadas se haya agotado o bien los trabajos de solicitud se hayan anulado una cantidad de veces que supere el umbral administrado por el sistema, se rellena un registro de diagnóstico con Intelligent Insights.

La información que se genera contiene el número de solicitudes y el número de consultas que agotaron el tiempo de espera. La indicación de la degradación del rendimiento está relacionada con el aumento del tiempo de espera en la fase de ejecución, o se proporciona el nivel de base de datos general. Cuando el aumento de los tiempos de espera se considera significativo para el rendimiento de la base de datos, estas consultas se marcan como problema de degradación del rendimiento de tiempo de espera. 

## <a name="excessive-wait-times"></a>Tiempo de espera excesivo

El modelo de tiempo de espera excesivo supervisa las consultas de base de datos individuales. Detecta estadísticas de espera de consultas inusualmente altas que traspasan los umbrales absolutos administrados por el sistema. El comportamiento de las siguientes métricas de tiempo de espera excesivo de consulta se observa con la nueva característica de SQL Server, Query Store Wait Stats (sys.query_store_wait_stats) (Estadísticas de espera de Almacén de datos de consultas):

- Alcance de los límites de recursos
- Alcance de los límites de recursos del grupo elástico
- Número excesivo de subprocesos de sesión o trabajo
- Bloqueo de base de datos excesivo
- Presión de memoria
- Otras estadísticas de espera

Si se alcanzan los límites de recursos o los límites de recursos del grupo elástico, implica que el consumo de recursos disponibles en una suscripción o en el grupo elástico ha superado los umbrales absolutos. Estas estadísticas indican la degradación del rendimiento de la carga de trabajo. Un número excesivo de subprocesos de sesión o trabajo implica una condición en la que el número de sesiones o subprocesos de trabajo iniciado ha superado los umbrales absolutos. Estas estadísticas indican la degradación del rendimiento de la carga de trabajo.

El bloqueo de base de datos excesivo implica una condición en la que el número de bloqueos de una base de datos ha superado el umbral absoluto. Este estado indica una degradación del rendimiento de la carga de trabajo. La presión de memoria es una condición en la que el número de subprocesos que soliciten memoria concedidos superó un umbral absoluto. Este estado indica una degradación del rendimiento de la carga de trabajo.

La detección de otras estadísticas de espera indica una condición en la que varias métricas medidas a través de Query Store Wait Stats (Estadísticas de espera de Almacén de datos de consultas) han superado el umbral absoluto. Estas estadísticas indican la degradación del rendimiento de la carga de trabajo.

Una vez que se detectan tiempos de espera excesivos, en función de los datos disponibles, el registro de diagnóstico de Intelligent Insights crea un hash de las consultas afectadas con un rendimiento degradado, detalles de las métricas que provocan la espera de la ejecución de las consultas y el tiempo de espera medido.

## <a name="errored-requests"></a>Solicitudes cerradas por un error

El modelo de degradación de solicitudes cerradas por un error supervisa las consultas individuales y detecta un aumento en el número de consultas cerradas por un error en comparación con el período de línea base. Este modelo también supervisa las excepciones críticas que superasen los umbrales absolutos administrados por la inteligencia integrada de SQL Database. El sistema considera automáticamente el número de solicitudes de consulta realizadas en la base de datos y las cuentas para los cambios de carga de trabajo en el período supervisado.

Cuando se considera que el aumento medido en las solicitudes con errores relativas al número total de solicitudes realizadas es significativo en el rendimiento de la carga de trabajo, las consultas afectadas se marcan como problema de degradación del rendimiento de las solicitudes cerradas por un error.

El registro de Intelligent Insights da como resultado el número de solicitudes con errores. Indica si la degradación del rendimiento estaba relacionada con un aumento en las solicitudes con errores o con traspasar un umbral de excepción crítica supervisado y el tiempo medido de la degradación del rendimiento. 

En caso de que alguna de las excepciones críticas supervisadas supere los umbrales absolutos administrados por el sistema, se genera una instancia de Intelligent Insights con detalles de la excepción crítica.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [solucionar problemas de rendimiento de SQL Database con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Use el [Registro de diagnóstico de rendimiento de SQL Database de Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
* Aprenda a [supervisar SQL Database mediante SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
* Aprenda a [recopilar y usar los datos de registro provenientes de los recursos de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).


