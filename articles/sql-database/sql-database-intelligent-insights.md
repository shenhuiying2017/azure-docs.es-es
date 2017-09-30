---
title: "Supervisión de uso de base de datos de Intelligent Insight: Azure SQL Database | Microsoft Docs"
description: Intelligent Insights le permite saber lo que ocurre con el rendimiento de la base de datos.
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
ms.openlocfilehash: c3b11dd50fa8c94d3bf80e02a8a319030c375133
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---
# <a name="intelligent-insights"></a>Intelligent Insights

***Intelligent Insights le permite saber lo que ocurre con el rendimiento de la base de datos.***

La inteligencia incorporada de Azure SQL Database supervisa continuamente el uso de la base de datos a través de la inteligencia artificial y detecta eventos potencialmente perjudiciales que provocan un rendimiento bajo. Una vez detectados, se realiza un análisis detallado y se genera un registro de diagnóstico con una evaluación inteligente del problema. Esta evaluación está formada por un análisis de la causa raíz del problema de rendimiento de la base de datos, y si es posible, recomendaciones para mejorar el rendimiento. Esto es ***Intelligent Insights.*** 

## <a name="what-can-intelligent-insights-do-for-you"></a>¿Qué puede hacer Intelligent Insights por usted?

Intelligent Insights es una capacidad única de inteligencia incorporada de Azure que proporciona el valor siguiente:

- Supervisión proactiva
- Recomendaciones del rendimiento adaptadas
- Detección temprana de degradación del rendimiento de la base de datos
- Análisis de causa raíz (RCA) de problemas detectados
- Recomendaciones de mejora del rendimiento
- Escalación horizontal de la capacidad en cientos de miles de bases de datos
- Impacto positivo para los recursos de DevOps y el costo total de propiedad

## <a name="how-does-intelligent-insights-work"></a>¿Cómo funciona Intelligent Insights?

Intelligent Insights analiza el rendimiento de Azure SQL Database comparando la carga de trabajo de la base de datos de la última hora con la carga trabajo de línea base de los últimos 7 días. La carga de trabajo de base de datos se compone de consultas determinadas para que sean las más significantes para el rendimiento de la base de datos: son, por ejemplo, las consultas más repetidas y de mayor tamaño. Cada base de datos es única según su estructura, datos, uso y aplicación debido a que cada línea base de carga de trabajo generada es específica y única para una instancia individual. Intelligent Insights, que es independientemente de la línea base de carga de trabajo, también supervisa umbrales operativos absolutos y detecta problemas con los tiempos de espera excesivos, las excepciones críticas y los problemas con parametrizaciones de consulta que podrían afectar al rendimiento.

Una vez detectado un problema de degradación del rendimiento de varias métricas observadas con inteligencia artificial, se realiza el análisis proporcionando un registro de diagnóstico con una recomendación inteligente sobre lo que sucede con la base de datos. Intelligent Insights facilita la realización de un seguimiento del problema de rendimiento de la base de datos desde su primera aparición hasta la resolución. Esto se consigue a través de los estados de seguimiento de cada problema detectado a través de su ciclo de vida desde la detección inicial del problema, comprobación de mejora del rendimiento y su finalización. Las actualizaciones se proporcionan en el registro de diagnóstico cada 15 minutos. 

<center>![Servidor](./media/sql-database-intelligent-insights/intelligent-insights-concept.png)</center>

Las métricas usadas para medir y detectar problemas de rendimiento de la base de datos se basan en la duración de la consulta, solicitudes con tiempo de espera agotado, tiempos de espera excesivos y una solicitud con errores, y puede obtener más información sobre ellas en la sección [Métricas de detección](sql-database-intelligent-insights.md#detection-metrics) de este documento.

## <a name="degradations-detected"></a>Degradaciones detectadas

Una vez identificado el rendimiento de Azure SQL Database, las degradaciones se guardan en el registro de diagnóstico con entradas inteligentes que disponen de las siguientes propiedades:

| Propiedad             | Detalles              |
| :------------------- | ------------------- |
| Información de base de datos  | Metadatos acerca de una base de datos en la que se ha detectado una recomendación, por ejemplo, un URI de recurso. |
| Intervalo de tiempo observado | Hora inicial y final para el período de la recomendación detectada. |
| Métricas afectadas | Métricas que provocan que se genere una recomendación: <ul><li>Aumento de la duración de la consulta [segundos]</li><li>Tiempo de espera excesivo [segundos]</li><li>Solicitudes con tiempo de espera agotado [porcentaje]</li><li>Solicitudes cerradas por un error [porcentaje]</li></ul>|
| Valor del impacto | Valor de una métrica medida. |
| Consultas afectadas y códigos de error | Código de error o hash de consulta. Se pueden usar para realizar una correlación fácilmente en las consultas afectadas. Se proporcionan métricas que consisten en un aumento de la duración de la consulta, tiempo de espera, recuentos de tiempo de espera o códigos de error. |
| Detecciones | Detección identificada en la base de datos durante el tiempo de un evento. Existen 15 patrones de detección. Vea [Troubleshoot database performance issues with Intelligent Insight](sql-database-intelligent-insights-troubleshoot-performance.md) (Solución de problemas de rendimiento de la base de datos con Intelligent Insight). |
| Análisis de causa raíz | Análisis de causa de raíz (RCA) sobre el problema identificado en un formato legible. Algunas recomendaciones podrían contener una recomendación de mejora del rendimiento si es posible. |
|||

## <a name="issues-state-lifecycle-active-verifying-and-completed"></a>Ciclo de vida del estado de los problemas: Activo, Comprobando y Completado

Los problemas de rendimiento generados en el registro de diagnóstico están marcados con uno de estos tres estados de ciclo de vida del problema: Activo, Comprobando y Completado. Una vez que se detecta un problema de rendimiento, y siempre que la recomendación integrada de Azure SQL lo considere presente, el problema se marca como ***Activo***. En el momento en el que el problema se considera mitigado, se comprueba y se cambia el estado del problema a ***Comprobando***. Una vez que la inteligencia incorporada de SQL Azure considere el problema mitigado, el estado del problema se marcará como &#8220; ***Completado***&#8221;.

## <a name="using-intelligent-insights"></a>Uso de Intelligent Insights

El registro de diagnóstico de Intelligent Insights puede enviarse a Azure Log Analytics, Centro de eventos de Azure y Azure Storage como se describe en [Métricas y registros de diagnóstico de Azure SQL Database](sql-database-metrics-diag-logging.md). Una vez que el registro se envíe a uno de estos destinos, el registro se puede usar para el desarrollo de supervisión y alerta personalizado con Microsoft o herramientas de terceros. Para ver el procedimiento de solución de problemas de rendimiento de Azure SQL Database con Intelligent Insights, vea [Solucionar problemas de rendimiento de Azure SQL Database con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).

## <a name="built-in-intelligent-insights-analytics-with-azure-log-analytics"></a>Análisis de Intelligent Insights integrado con Azure Log Analytics 

La solución de Azure Log Analytics ofrece capacidades de informes y alertas sobre los datos de registro de diagnóstico de Intelligent Insights más importantes. A continuación se muestra un ejemplo del informe de Intelligent Insights en Azure SQL Analytics.

![Server](./media/sql-database-intelligent-insights/intelligent-insights-azure-sql-analytics.png)

Una vez que se ha configurado el registro de diagnóstico de Intelligent Insights para transmitir datos a Azure SQL Analytics, también puede [supervisar Azure SQL Database mediante Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).

## <a name="custom-integrations-of-intelligent-insights-log"></a>Integraciones personalizadas del registro de Intelligent Insights

Para un desarrollo de supervisión y alerta personalizado, con Microsoft o herramientas de terceros, vea [Use Intelligent Insights database performance diagnostics log](sql-database-intelligent-insights-use-diagnostics-log.md) (Uso del registro de diagnóstico de rendimiento de la base de datos de Intelligent Insights).

## <a name="how-to-set-up-intelligent-insights-with-azure-event-hub"></a>Configuración de Intelligent Insights con Centro de eventos de Azure

- Configure Intelligent Insights para transmitir los eventos de registro en el Centro de eventos de Azure en [Transmisión de registros de diagnóstico de Azure a Centros de eventos](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md).
- Use el Centro de eventos de Azure para la supervisión y alerta personalizadas en [What to do with metrics and diagnostics logs in Event Hub](sql-database-metrics-diag-logging.md#stream-into-azure-storage) (Qué hacer con las métricas y registros de diagnóstico en Centro de eventos). 

## <a name="how-to-set-up-intelligent-insights-with-azure-storage"></a>Configuración de Intelligent Insights con Azure Storage

- Configure Intelligent Insights para que se almacene con Azure Storage en [Transmisión a Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage).

## <a name="detection-metrics"></a>Métricas de detección

Las métricas utilizadas para los modelos de detección que generan Intelligent Insights se basan en la supervisión:

- Duración de la consulta
- Solicitudes con tiempo de espera
- Tiempo de espera excesivo 
- Solicitudes cerradas por un error

Duración de consulta y Solicitudes con tiempo de espera se usan como modelos principales en la detección de problemas con el rendimiento de carga de trabajo de la base de datos. Esto se debe a que miden directamente lo que ocurre con la carga de trabajo. Para detectar todos los posibles casos de degradación del rendimiento de la carga de trabajo, se usan Tiempo de espera excesivo y Solicitudes cerradas por un error como modelos adicionales para indicar los problemas que afectan al rendimiento de la carga de trabajo.

El sistema tiene en cuenta automáticamente los cambios en la carga de trabajo y cambia el número de solicitudes de consulta realizadas a la base de datos para determinar de forma dinámica los umbrales de rendimiento de la base de datos normales y fuera de lo normal.

Se tienen en cuenta todas las métricas juntas en distintas relaciones a través de un modelo de datos derivado científicamente que categoriza cada problema de rendimiento detectado. La información proporcionada a través de Intelligent Insight incluye detalles del problema de rendimiento detectado, una análisis de la causa raíz de la ocurrencia del problema y recomendaciones sobre cómo mejorar el rendimiento de la instancia de Azure SQL Database supervisada en los casos en los que sea posible.

## <a name="query-duration"></a>Duración de la consulta

El modelo de degradación de la duración de la consulta analiza las consultas individuales y detecta el aumento del tiempo que tarda en compilar y ejecutar una consulta en comparación con la línea base de rendimiento.

Cuando la inteligencia integrada de Azure SQL Database detecta un aumento significativo en la compilación de consultas o en el tiempo de ejecución de las consultas que afecta al rendimiento de la carga de trabajo, estas consultas se marcan como problema de degradación del rendimiento de duración de la consulta. 

El registro de diagnóstico de Intelligent Insights genera el hash de consulta con un rendimiento degradado, una indicación de si la degradación del rendimiento estaba relacionada con la compilación de la consulta o con el aumento del tiempo de ejecución, y un aumento del tiempo de duración de la consulta.

## <a name="timeout-requests"></a>Solicitudes con tiempo de espera

El modelo de degradación de solicitudes de tiempo de espera analiza las consultas individuales y detecta un aumento en los tiempos de espera en el nivel de ejecución de la consulta y los tiempos de espera de solicitud totales en el nivel de base de datos en comparación con el período de línea base de rendimiento.

Puesto que algunas de las consultas pueden agotar el tiempo de espera incluso antes de llegar a la fase de ejecución, la inteligencia integrada de Azure SQL Database también mide y analiza, por medio de trabajos cancelados frente a solicitudes realizadas, todas las consultas que se han realizado en la base de datos independientemente de si se ha llegado a la fase de ejecución o no. 

Una vez que la cantidad de tiempo de expiración para consultas ejecutadas o la cantidad de trabajos de solicitud cancelada ha superado el umbral administrado por el sistema, se completa un registro de diagnóstico con Intelligent Insights.

Las recomendaciones generadas contienen una serie de solicitudes con tiempo de espera agotado, una serie de consultas con tiempo de espera agotado y una indicación de si la degradación del rendimiento estaba relacionada con el aumento del tiempo de espera en la fase de ejecución o el nivel general de la base de datos. Cuando el aumento de los tiempos de espera se considera significativo para el rendimiento de la base de datos, estas consultas se marcan como problema de degradación del rendimiento de tiempo de espera. 

## <a name="excessive-wait-times"></a>Tiempo de espera excesivo

El modelo de tiempo de espera excesivo supervisa las consultas de base de datos individuales y detecta las estadísticas de espera de consulta inusualmente altas que sean superiores a los umbrales absolutos administrados por el sistema. A continuación se muestra el comportamiento de las siguientes métricas de tiempo de espera excesivo de consulta con la nueva característica Query Store Wait Stats (sys.query_store_wait_stats) (Estadísticas de espera de almacén de datos de consultas) de SQL Server:

- Alcance de los límites de recursos
- Alcance de los límites de recursos del grupo elástico
- Número excesivo de subprocesos de sesión o trabajo
- Bloqueo de base de datos excesivo
- Presión de memoria
- Otras estadísticas de espera

El alcance de los límites de recursos o de los límites de recursos del grupo elástico implica que el consumo de recursos disponibles en una suscripción, o en el grupo elástico, ha superado los umbrales absolutos que indican la degradación del rendimiento de la carga de trabajo. El número excesivo de subprocesos de sesión o trabajo implica una condición en la que el número de sesiones o subprocesos de trabajo iniciado ha superado los umbrales absolutos que indican la degradación del rendimiento de la carga de trabajo.

El bloqueo de base de datos excesivo implica una condición en la que el número de bloqueos de una base de datos ha superado el umbral absoluto que indica la degradación del rendimiento de la carga de trabajo. La presión de memoria es una condición en la que el número de subprocesos que solicita concesiones de memoria ha superado el umbral absoluto que indica la degradación del rendimiento de la carga de trabajo.

La detección de otras estadísticas de espera indica una condición en la que varias métricas medidas a través de Query Store Wait Stats (Estadísticas de espera de almacén de datos de consultas) han superado el umbral absoluto que indica la degradación del rendimiento de la carga de trabajo.

Una vez que se detectan tiempos de espera excesivos, se genera un registro de diagnóstico de Intelligent Insight, según los datos disponibles, se crea un hash de las consultas afectadas con un rendimiento degradado, detalles de las métricas que provocan la espera de la ejecución de las consultas y el tiempo de espera medido.

## <a name="errored-requests"></a>Solicitudes cerradas por un error

El modelo de degradación de solicitudes cerradas por un error supervisa las consultas individuales y detecta un aumento en el número de consultas cerradas por un error en comparación con el período de línea base. Este modelo también supervisa las excepciones críticas que hayan superado los umbrales absolutos administrados por la inteligencia integrada de Azure Database. El sistema tiene en cuenta automáticamente el número de solicitudes de consulta realizadas en la base de datos y las cuentas para los cambios de carga de trabajo en el período supervisado.

Cuando se considera que el aumento medido en las solicitudes con errores en relación con el número total de solicitudes realizadas es significativo en el rendimiento de la carga de trabajo, las consultas afectadas se marcan como problema de degradación del rendimiento de las solicitudes cerradas por un error.

El registro de Intelligent Insights genera el número de solicitudes cerradas por un error, la indicación de si la degradación del rendimiento estaba relacionada con el aumento de las solicitudes cerradas por un error o con la superación de los umbrales de excepción crítica supervisados y el tiempo medido de degradación del rendimiento. 

En caso de que cualquiera de las excepciones críticas supervisadas supere los umbrales absolutos administrados por el sistema, se genera una instancia de Intelligent Insight con detalles de la excepción crítica.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [solucionar problemas de rendimiento de Azure SQL Database con Intelligent Insights](sql-database-intelligent-insights-troubleshoot-performance.md).
* Use el [Registro de diagnóstico de rendimiento de Azure SQL Database de Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
* Aprenda a [supervisar Azure SQL Database mediante Azure SQL Analytics](../log-analytics/log-analytics-azure-sql.md).
* Aprenda a [recopilar y usar los datos de registro provenientes de los recursos de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md).



