---
title: Solucionar problemas de rendimiento de Azure SQL Database con Intelligent Insights | Documentos de Microsoft
description: "\"Intelligent Insights le permite solucionar problemas de rendimiento de Azure SQL Database\"."
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
ms.openlocfilehash: edd8c526a681c9cd5226ede6110f21c3362aaef6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-azure-sql-database-performance-issues-with-intelligent-insights"></a>Solucionar problemas de rendimiento de Azure SQL Database con Intelligent Insights

Esta página proporciona información sobre los problemas de rendimiento de Azure SQL Database detectados a través del registro de diagnóstico de rendimiento de la base de datos de [Intelligent Insights](sql-database-intelligent-insights.md) Este registro de diagnóstico se puede enviar a [Azure Log Analytics](../log-analytics/log-analytics-azure-sql.md), [Azure Event Hubs](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md), [Azure Storage](sql-database-metrics-diag-logging.md#stream-into-azure-storage) o a una solución de terceros para las funcionalidades personalizadas de informes y alertas de DevOps.

> [!NOTE]
> Para obtener una guía rápida para la solución de problemas de rendimiento de SQL Azure Database a través de Intelligent Insights, consulte el diagrama de flujo [Flujo recomendado de solución de problemas](sql-database-intelligent-insights-troubleshoot-performance.md#recommended-troubleshooting-flow) en este documento.
>

## <a name="detectable-database-performance-patterns"></a>Patrones de rendimiento de la base de datos detectables

Intelligent Insights detecta automáticamente los problemas de rendimiento con Azure SQL Database en función de los tiempos de expiración, los errores y los tiempos de expiración de la ejecución de una consulta y genera los patrones de rendimiento detectados en el registro de diagnóstico. Los patrones de rendimiento detectables se resumen en la tabla siguiente:

| Patrones de rendimiento detectables | Detalles generados |
| :------------------- | ------------------- |
| [Alcance de los límites de recursos](sql-database-intelligent-insights-troubleshoot-performance.md#reaching-resource-limits) | Se alcanzó el límite del consumo de los recursos disponibles (DTU), los subprocesos de trabajo de la base de datos o las sesiones de inicio de sesión a la base de datos disponibles en la suscripción supervisada y esto provoca problemas de rendimiento de Azure SQL Database. |
| [Aumento de la carga de trabajo](sql-database-intelligent-insights-troubleshoot-performance.md#workload-increase) | Se detectó un aumento de la carga de trabajo o una acumulación continua de la carga de trabajo en la base de datos y esto provoca problemas de rendimiento de Azure SQL Database. |
| [Presión de memoria](sql-database-intelligent-insights-troubleshoot-performance.md#memory-pressure) | Los trabajos que solicitan concesiones de memoria están esperando asignaciones de memoria para cantidades de tiempo estadísticamente significativas, o existe una acumulación en el aumento de trabajos que solicitan concesiones de memoria que afectan al rendimiento de Azure SQL Database. |
| [Bloqueo](sql-database-intelligent-insights-troubleshoot-performance.md#locking) | Se detectó un bloqueo excesivo de la base de datos y esto afecta al rendimiento de Azure SQL Database. |
| [Aumento de MAXDOP](sql-database-intelligent-insights-troubleshoot-performance.md#increased-maxdop) | La opción de grado máximo de paralelismo (MAXDOP) cambió y afecta a la eficacia de la ejecución de la consulta.  |
| [Contención de PAGELATCH](sql-database-intelligent-insights-troubleshoot-performance.md#pagelatch-contention) | Se detectó una contención de PAGELATCH que afecta al rendimiento de Azure SQL Database. Hay varios subprocesos que están intentando acceder de forma simultánea a las mismas páginas de búfer de datos en memoria y esto provoca un aumento de los tiempos de espera que afecta al rendimiento de Azure SQL Database. |
| [Carencia de un índice](sql-database-intelligent-insights-troubleshoot-performance.md#missing-index) | Se detectó un problema por la falta de un índice y esto afecta al rendimiento de Azure SQL Database. |
| [Nueva consulta](sql-database-intelligent-insights-troubleshoot-performance.md#new-query) | Se detectó una nueva consulta que afecta al rendimiento general de Azure SQL Database.  |
| [Estadística de espera inusual](sql-database-intelligent-insights-troubleshoot-performance.md#unusual-wait-statistic) | Se detectaron tiempos de espera inusuales de la base de datos que afectan al rendimiento de Azure SQL Database. |
| [Contención de TempDB](sql-database-intelligent-insights-troubleshoot-performance.md#tempdb-contention) | Varios subprocesos están intentando obtener acceso a los mismos recursos de TempDB, lo que produce un cuello de botella que afecta al rendimiento de Azure SQL Database. |
| [Escasez de DTU en el grupo elástico](sql-database-intelligent-insights-troubleshoot-performance.md#elastic-pool-dtu-shortage) | La escasez de las eDTU disponibles en el grupo elástico está afectando al rendimiento de Azure SQL Database. |
| [Regresión de un plan](sql-database-intelligent-insights-troubleshoot-performance.md#plan-regression) | Se detectó un nuevo plan o un cambio en la carga de trabajo de un plan existente que afecta al rendimiento de Azure SQL Database. |
| [Cambio del valor de configuración de ámbito de base de datos](sql-database-intelligent-insights-troubleshoot-performance.md#database-scoped-configuration-value-change) | Un cambio en la configuración de la base de datos afecta al rendimiento de Azure SQL Database. |
| [Cliente lento](sql-database-intelligent-insights-troubleshoot-performance.md#slow-client) | Se detectó un cliente de aplicación lento, que no puede usar la salida de Azure SQL Database lo suficientemente rápido, y esto afecta al rendimiento de Azure SQL Database |
| [Degradación del plan de tarifa](sql-database-intelligent-insights-troubleshoot-performance.md#pricing-tier-downgrade) | La acción de degradación del plan de tarifa redujo los recursos disponibles y esto afecta al rendimiento de Azure SQL Database. |

> [!TIP]
> A fin de que el rendimiento de Azure SQL Database se optimice de forma continua, le recomendamos que considere la posibilidad de habilitar [Azure SQL Database Automatic Tuning](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-automatic-tuning): una característica exclusiva de la inteligencia integrada de Azure SQL que supervisa continuamente su instancia de Azure SQL Database, ajusta automáticamente los índices y aplica las correcciones del plan de ejecución de consultas.
>

En la sección siguiente se describen los patrones de rendimiento detectables enumerados anteriormente con más detalle.

## <a name="reaching-resource-limits"></a>Alcance de los límites de recursos

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable combina los problemas de rendimiento relacionados con el alcance de los límites de recursos disponibles, los límites de trabajo y los límites de la sesión. Una vez que se detecta este problema de rendimiento, un campo de descripción del registro de diagnóstico indica si el problema de rendimiento está relacionado con el recurso, el trabajo o los límites de la sesión.

Normalmente, se hace referencia a los recursos de Azure SQL Database como [recursos DTU](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-what-is-a-dtu), que consisten en una medida combinada de los recursos de la CPU y E/S (E/S del registro de transacciones y datos). El patrón de alcance de los límites de recursos se reconoce cuando la degradación detectada del rendimiento de consultas se produce debido a que se alcanza alguno de los límites de los recursos medidos.

El recurso de límites de sesión denota el número de inicios de sesión simultáneos disponibles en Azure SQL Database. Este patrón de rendimiento se reconoce en el caso de que las aplicaciones que se conectan a las instancias de Azure SQL Database hayan alcanzado el número de inicios de sesión simultáneos disponibles para la base de datos. En caso de que las aplicaciones intenten usar más sesiones que las que hay disponibles en una base de datos, se verá afectado el rendimiento de consultas.

El alcance de los límites de los trabajos es un caso concreto del alcance de los límites de los recursos, ya que no se cuentan los trabajos disponibles en el uso de DTU. Si se alcanzan los límites de trabajo de una base de datos, se puede producir un aumento de los tiempos de espera específicos de un recurso y, por lo tanto, una degradación del rendimiento de consultas.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico genera los códigos hash de las consultas que han afectado al rendimiento y los porcentajes de consumo de recursos. Puede usar esta información como punto de partida para optimizar la carga de trabajo de su base de datos. En concreto, le recomendamos que considere la posibilidad de optimizar las consultas que afectan a la degradación del rendimiento, agregan índices u optimizan las aplicaciones con una distribución de carga de trabajo más uniforme. En caso de que no pueda reducir las cargas de trabajo o realizar la optimización, es posible que quiera considerar la posibilidad de aumentar el plan de tarifa de su suscripción de Azure SQL Database a fin de aumentar la cantidad de recursos disponibles.

Si alcanzó los límites de sesiones disponibles, puede considerar la posibilidad de optimizar las aplicaciones con la reducción de inicios de sesión realizados en la base de datos. Si no puede reducir el número de inicios de sesión desde las aplicaciones a la base de datos, le recomendamos que considere la posibilidad de aumentar el plan de tarifa de la base de datos, o también puede dividir su base de datos y moverla a varias bases de datos para obtener una distribución más equilibrada de la carga de trabajo.

Para obtener más sugerencias sobre cómo resolver los límites de sesiones, consulte [How to deal with the limits of Azure SQL Database maximum logins](https://blogs.technet.microsoft.com/latam/2015/06/01/how-to-deal-with-the-limits-of-azure-sql-database-maximum-logins/) (Cómo abordar los límites del número máximo de inicios de sesión de Azure SQL Database). Para averiguar los límites de los recursos disponibles de su nivel de suscripción, consulte [Límites de recursos de Azure SQL Database](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-resource-limits).

## <a name="workload-increase"></a>Aumento de la carga de trabajo

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento identifica los problemas causados por el aumento de la carga de trabajo, o en su forma más grave, una pila de cargas de trabajo.

Esta detección se realiza a través de la combinación de varias estadísticas. La métrica básica medida consiste en detectar un aumento de la carga de trabajo en comparación con la base de referencia de la carga de trabajo anterior. La otra forma de detección se basa en medir un gran aumento de los subprocesos de trabajo activos que son lo suficientemente grandes como para afectar al rendimiento de la consulta.

Y, en su forma más grave, que la carga de trabajo se siga apilando debido a que Azure SQL Database no puede administrarla. El resultado es que siga aumentando el tamaño de la carga de trabajo, es decir, la condición de la pila de la carga de trabajo. Debido a esta condición, el tiempo de espera de la carga de trabajo para la ejecución aumenta y representa uno de los problemas más graves de rendimiento de la base de datos. Este problema se detecta mediante la supervisión del aumento del número de subprocesos de trabajo anulados. 

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona el número de consultas cuya ejecución ha aumentado y el código hash de la consulta que contribuye más al aumento de la carga de trabajo. Puede usar esta información como punto de partida para la optimización de la carga de trabajo y, especialmente, para la consulta identificada por haber contribuido más en el aumento de la carga de trabajo.

También le recomendamos que considere la posibilidad de distribuir las cargas de trabajo de forma más uniforme en la base de datos. Asimismo, puede que le interese optimizar la consulta que afecta al rendimiento y agrega índices. Otro enfoque que se debe tener en cuenta puede ser la distribución de la carga de trabajo entre varias bases de datos. Si no es posible, puede que quiera considerar la posibilidad de aumentar el plan de tarifa de su suscripción de Azure SQL Database a fin de aumentar la cantidad de recursos disponibles.

## <a name="memory-pressure"></a>Presión de memoria

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento indica una degradación en el rendimiento de la base de datos actual provocada por la presión de memoria, o en su forma más grave, una condición de pila de memoria comparada con la base de referencia de rendimiento de los 7 días anteriores.

La presión de memoria indica una condición de rendimiento en que hay un número suficientemente grande de subprocesos de trabajo que solicita concesiones de memoria en Azure SQL Database. Esto provoca una condición de uso de memoria alta en la que Azure SQL Database no puede asignar de forma eficiente la memoria para todos los trabajados que lo solicitan. Uno de los motivos más comunes de este problema está relacionado, por un lado, con la cantidad de memoria disponible para Azure SQL Database y, por otro, con un aumento de la carga de trabajo que está causando el aumento de subprocesos de trabajo y la presión de memoria.

Una forma más grave de la presión de memoria es la condición de pila de la memoria que indica que hay un número más elevado de subprocesos de trabajo que solicitan concesiones de memoria que el número de consultas que liberan memoria. También es posible que este número de subprocesos de trabajo que solicitan concesiones de memoria aumente continuamente (es decir, se apile), ya que el motor no puede asignar memoria de una forma suficientemente eficaz como para satisfacer la demanda. La condición de pila de memoria representa uno de los problemas de rendimiento de la base de datos más graves.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona los detalles del almacén de objetos de la memoria con el distribuidor (que es el subproceso de trabajo) marcado como el mayor motivo del uso de memoria alta y las marcas de tiempo pertinentes. Esta información se podría usar como base para la solución de problemas. 

Quizá quiera considerar la posibilidad de optimizar o quitar consultas relacionadas con los distribuidores que realizan un mayor uso de memoria. También puede que quiera asegurarse de que no está consultando datos que no tiene planeado usar. Como procedimiento recomendado, use siempre la cláusula WHERE en sus consultas. Además, se recomienda que cree índices no agrupados para buscar los datos, en lugar de examinarlos.

También es posible que quiera reducir, optimizar o distribuir la carga de trabajo a través de varias bases de datos. Otro enfoque que se debe tener en cuenta puede ser la distribución de la carga de trabajo entre varias bases de datos. Si no es posible, puede que quiera considerar la posibilidad de aumentar el plan de tarifa de su suscripción de Azure SQL Database a fin de aumentar la cantidad de recursos de memoria disponibles en la base de datos.

Para obtener sugerencias de solución de problemas adicionales, consulte Memory Grants Meditation: [The mysterious SQL Server memory consumer with Many Names](https://blogs.msdn.microsoft.com/sqlmeditation/2013/01/01/memory-meditation-the-mysterious-sql-server-memory-consumer-with-many-names/) (Meditación de concesiones de memoria: el consumidor de memoria misterioso de SQL Server con muchos nombres).

## <a name="locking"></a>Bloqueo

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento indica una degradación del rendimiento de la base de datos actual en que se detectó un bloqueo de base de datos excesivo en comparación con la base de referencia de rendimiento de los 7 días anteriores. 

En el sistema de administración de bases de datos relacionales moderno, el bloqueo es esencial para implementar sistemas de varios subprocesos en que, siempre que sea posible, el rendimiento se maximiza a través de la ejecución de varios trabajos simultáneos y transacciones de la base de datos paralelas. En otras palabras, el bloqueo en este contexto hace referencia al mecanismo de acceso integrado en que solo una única transacción puede tener acceso exclusivo a filas, páginas, tablas y archivos necesarios y no competir con ninguna otra transacción para los recursos. Cuando la transacción que ha bloqueado los recursos para el uso se realiza en ellos, se libera el bloqueo en estos recursos y se permite que otras transacciones accedan a los recursos necesarios. Para obtener más información sobre el bloqueo, consulte [Locking in the Database Engine](https://msdn.microsoft.com/library/ms190615.aspx) (Bloqueo del Motor de base de datos).

En el caso de que las transacciones ejecutadas por el motor SQL estén esperando durante largos períodos de tiempo para tener acceso a recursos bloqueados para usarlos, este tiempo de espera afecta a la reducción del rendimiento de la ejecución de la carga de trabajo. 

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico genera detalles de bloqueo que pueden usarse como base para la solución de problemas. Es posible que quiera analizar las consultas de bloqueo notificadas, es decir, las consultas que presentan la degradación del rendimiento de bloqueo, y quitarlas. En algunos casos, es posible que la optimización de las consultas de bloqueo se realice correctamente.
La forma más sencilla y segura de mitigar el problema es mantener cortas las transacciones y reducir la superficie de bloqueo de las consultas más costosas. Puede que quiera considerar la posibilidad de dividir un lote grande de operaciones en operaciones más pequeñas. Le recomendamos que reduzca la superficie de bloqueo de consulta. Para ello, haga que la consulta sea tan eficaz como sea posible. Considere la posibilidad de reducir las exploraciones grandes a medida que aumenten las posibilidades de interbloqueos y afecten negativamente al rendimiento general de la base de datos. En el caso de las consultas identificadas que provocan el bloqueo, le recomendamos que considere la posibilidad de crear nuevos índices o de agregar columnas al índice existente para evitar los recorridos de tabla. Para obtener más sugerencias, vea [How to resolve blocking problems that are caused by lock escalation in SQL Server](https://support.microsoft.com/en-us/help/323630/how-to-resolve-blocking-problems-that-are-caused-by-lock-escalation-in) (Cómo resolver los problemas de bloqueo causados por la extensión de bloqueo en SQL Server).

## <a name="increased-maxdop"></a>Aumento de MAXDOP

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una condición en la que se ha paralelizado un plan de ejecución de consulta elegido más de lo que debería. El optimizador de consultas de Azure SQL Database mejora el rendimiento de la carga de trabajo. Para ello, opta por ejecutar la consulta en paralelo para acelerar los procesos siempre que sea posible. Sin embargo, en algunos casos, los trabajos paralelos que procesan una consulta dedican más tiempo esperándose entre sí para sincronizar y combinar los resultados que ejecutando la misma consulta con menos trabajos en paralelo, o incluso, en algunos casos, un subproceso de trabajo único.

El sistema experto analiza el rendimiento de la base de datos actual en comparación con el del período de la base de referencia, y determina si una consulta se ejecuta de forma más lenta porque el plan de ejecución de consulta está más paralelizado de lo que debería.

La opción de configuración de servidor MAXDOP de Azure SQL Database se usa para controlar el número de núcleos de CPU que se pueden usar para ejecutar la misma consulta en paralelo. 

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico genera códigos hash de consulta relacionados con las consultas para las que ha aumentado la duración de la ejecución porque están más paralelizadas de lo que deberían. El registro también genera tiempos de espera CXP. Este tiempo representa el tiempo en que un subproceso de organizador o coordinador único (subproceso 0) espera a que todos los demás subprocesos finalicen, antes de combinar los resultados y seguir avanzando. Además, el registro de diagnóstico proporciona los tiempos de espera de las consultas con un rendimiento deficiente durante la ejecución general. Esta información constituye la base de la solución del problema.

Es posible que primero quiera considerar la posibilidad de optimizar o simplificar las consultas complejas. Le recomendamos que divida los trabajos por lotes largos en partes más pequeñas. Además, asegúrese de que tiene índices creados que admiten sus consultas. También puede considerar la posibilidad de aplicar manualmente la opción Grado máximo de paralelismo (MAXDOP) para una consulta determinada que se ha marcado con un rendimiento deficiente. Esta operación se configura mediante T-SQL; consulte [Establecer la opción de configuración del servidor Grado máximo de paralelismo](https://docs.microsoft.com/en-us/sql/database-engine/configure-windows/configure-the-max-degree-of-parallelism-server-configuration-option).

Si se establece la opción de configuración de servidor MAXDOP en cero (0) como valor predeterminado, se indica que Azure SQL Database puede usar todos los núcleos de CPU lógicos disponibles para paralelizar subprocesos ejecutando una sola consulta. Si se establece MAXDOP en uno (1) se indica que solo se puede usar un núcleo para la ejecución de una consulta; en la práctica, esto significaría que el paralelismo está desactivado. En función de cada caso, de los núcleos disponibles para la base de datos y la información de registro de diagnóstico, es posible que quiera ajustar la opción MAXDOP al número de núcleos para la ejecución de consultas en paralelo que resolvió el problema en su caso.

## <a name="pagelatch-contention"></a>Contención de PAGELATCH

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento indica la degradación del rendimiento actual de la carga de trabajo de la base de datos debido a una contención de PAGELATCH en comparación con la base de referencia de la carga de trabajo de los 7 días anteriores.

Los bloqueos temporales son mecanismos de sincronización ligeros que usa Azure SQL Database para habilitar el multithreading y para garantizar la coherencia de las estructuras en memoria, incluidos los índices, las páginas de datos y otras estructuras internas.

Existen muchos tipos de bloqueos temporales en Azure SQL Database. Por motivos de simplicidad, los bloqueos temporales de búfer se usan para proteger las páginas en memoria en el grupo de búferes, y los bloqueos temporales de E/S se usan para proteger las páginas que aún no se han cargado en el grupo de búferes. Siempre que se escriben o se leen datos en una página del grupo de búferes, primero es necesario que un subroceso de trabajo adquiera un bloqueo temporal de búferes para la página. Cada vez que un subproceso de trabajo intenta tener acceso a una página que aún no está disponible en el grupo de búferes en memoria, se realiza la solicitud de E/S para cargar la información necesaria desde el almacenamiento, lo que indica una forma más grave de la degradación del rendimiento.

La contención en los bloqueos temporales de páginas se produce cuando varios subprocesos intentan adquirir simultáneamente bloqueos temporales en la misma estructura en memoria al introducir un tiempo de espera mayor en la ejecución de la consulta. En el caso de la contención de E/S de PAGELATCH cuando es necesario acceder a los datos desde el almacenamiento, este tiempo de espera aún es mayor y puede afectar considerablemente al rendimiento de la carga de trabajo. La contención de PAGELATCH es el escenario más común de subprocesos que se esperan mutuamente y compiten por recursos en varios sistemas de CPU.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona los detalles de la contención de PAGELATCH que pueden usarse como base para solucionar el problema.

Dado que PAGELATCH es un mecanismo de control interno de Azure SQL Database, determina automáticamente cuándo se deben usar. Las decisiones de aplicación, incluido el diseño de esquema, pueden afectar al comportamiento de PAGELATCH debido al comportamiento determinista de los bloqueos temporales.
Un método para controlar la contención de bloqueos temporales es reemplazar una clave de índice secuencial por una clave no secuencial a fin de distribuir uniformemente las inserciones en un intervalo de índices. Normalmente, esto se realiza con una columna inicial en el índice que distribuye la carga de trabajo de forma proporcional. Otro método que se debe tener en cuenta es la creación de particiones de tablas. La creación de un esquema de creación de particiones por hash con una columna calculada en una tabla con particiones es un enfoque común para mitigar la contención de bloqueos temporales excesivos. En el caso de la contención de E/S de PAGELATCH, es recomendable introducir índices, ya que resulta beneficioso al mitigar este problema de rendimiento. Para obtener más información, consulte [Diagnosing and Resolving Latch Contention on SQL Server](http://download.microsoft.com/download/B/9/E/B9EDF2CD-1DBF-4954-B81E-82522880A2DC/SQLServerLatchContention.pdf) (Diagnosticar y resolver la contención de bloqueos temporales en SQL Server) (descarga de PDF).

## <a name="missing-index"></a>Carencia de un índice

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento indica la degradación del rendimiento actual de la carga de trabajo de la base de datos en comparación con la base de referencia de los 7 días anteriores debido a que falta un índice.

Se usa un índice para acelerar el rendimiento de las consultas. Proporciona acceso rápido a los datos de la tabla al reducir el número de páginas del conjunto de datos que se deben visitar o examinar.

Las consultas específicas que han causado una degradación del rendimiento se identifican mediante esta detección para la que la creación de índices resultaría beneficiosa para el rendimiento.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico genera códigos hash para las consultas identificadas porque afectan al rendimiento de la carga de trabajo. Es posible que quiera considerar la posibilidad de compilar índices para estas consultas. También puede que quiera tener en cuenta la optimización o eliminación de estas consultas si no son necesarias. Evitar la consulta de datos que no se usan puede contribuir a mejorar el rendimiento.

> [!TIP]
> ¿Sabía que la inteligencia integrada de Azure puede administrar automáticamente los índices con mejor rendimiento para sus bases de datos?
>
> A fin de que el rendimiento de Azure SQL Database se optimice de forma continua, le recomendamos que habilite [Azure SQL Database Automatic Tuning](sql-database-automatic-tuning.md): una característica exclusiva de la inteligencia integrada de Azure SQL que supervisa continuamente su instancia de Azure SQL Database y ajusta y crea índices automáticamente para sus bases de datos.
>

## <a name="new-query"></a>Nueva consulta

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica que se detectó una nueva consulta que presenta un rendimiento deficiente y que afecta al rendimiento de la carga de trabajo en comparación con la base de referencia de rendimiento de 7 días.

Escribir una consulta que tenga un buen rendimiento a veces puede ser una tarea complicada. Para obtener más información sobre cómo escribir consultas, consulte [Writing SQL Queries](https://msdn.microsoft.com/library/bb264565.aspx) (Escribir consultas SQL), y para optimizar el rendimiento de las consultas existentes, consulte [Query Tuning](https://msdn.microsoft.com/library/ms176005.aspx) (Ajuste de las consultas).

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona información de hasta las dos (2) nuevas consultas que realizan un mayor consumo de la CPU, incluidos sus códigos hash de consulta. Dado que la consulta detectada afecta al rendimiento de la carga de trabajo, puede que quiera considerar la posibilidad de optimizar su consulta. Le recomendamos que no recupere datos que no usa, que use consultas con la cláusula WHERE y que simplifique las consultas complejas y las divida en otras consultas más pequeñas. También le puede resultar útil dividir consultas por lotes grandes en consultas por lotes más pequeñas. La introducción de índices para las nuevas consultas suele ayudar a mitigar este problema de rendimiento.

Es posible que quiera considerar la posibilidad de usar [Información de rendimiento de consultas de Azure SQL Database](sql-database-query-performance.md).

## <a name="unusual-wait-statistic"></a>Estadística de espera inusual

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una degradación del rendimiento de la carga de trabajo en la que se identifican consultas con un rendimiento deficiente en comparación con la base de referencia de carga de trabajo de los 7 días anteriores.

En este caso, el sistema no se pudo clasificar las consultas con un rendimiento deficiente en ninguna otra categoría de rendimiento detectable estándar, pero ha detectado la estadística de espera responsable de la regresión y, por tanto, considera que estas consultas tienen &#8220;*estadísticas de espera inusuales*&#8221; en las que también se expone la estadística de espera inusual responsable de la regresión. 

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico genera información sobre los detalles de los tiempos de espera inusuales, los códigos hash de las consultas afectadas y los tiempos de espera.

En este caso, dado que el sistema no pudo identificar correctamente la causa principal de las consultas con un rendimiento deficiente, la información de diagnóstico sobre las consultas con un rendimiento deficiente puede ser un buen punto de partida para la solución de problemas manual. Es posible que quiera considerar la posibilidad de optimizar el rendimiento de estas consultas. Normalmente, le resultará útil no capturar los datos que no usa y simplificar y dividir las consultas complejas en otras más pequeñas Para obtener más información acerca de cómo optimizar el rendimiento de las consultas, vea [Query Tuning](https://msdn.microsoft.com/library/ms176005.aspx) (Ajuste de las consultas).

## <a name="tempdb-contention"></a>Contención de TempDB

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una condición de rendimiento de la base de datos en la que existe un cuello de botella de subprocesos que intentan obtener acceso a recursos de TempDB (esta condición no está relacionada con E/S). El escenario típico de este problema de rendimiento consiste en cientos de consultas simultáneas que crean, usan y colocan tablas pequeñas de TempDB, que son las tablas de TempDB. El sistema ha detectado que ha aumentado el número de consultas simultáneas que usan las mismas tablas de TempDB con una significación estadística que afecta al rendimiento de la base de datos en comparación con la base de referencia de rendimiento de los últimos 7 días.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona detalles de contención de TempDB que pueden usarse como punto de partida para solucionar los problemas de rendimiento. Hay un par de cosas que puede llevar a cabo para mitigar este tipo de contención y mejorar el rendimiento de la carga de trabajo global. Puede dejar de usar las tablas temporales. También es posible que quiera considerar la posibilidad de usar tablas optimizadas para memoria; consulte el artículo [Introducción a las tablas optimizadas para memoria](https://docs.microsoft.com/en-us/sql/relational-databases/in-memory-oltp/introduction-to-memory-optimized-tables). 

## <a name="elastic-pool-dtu-shortage"></a>Escasez de DTU en el grupo elástico

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una degradación del rendimiento actual de la carga de trabajo de la base de datos en comparación con la base de referencia de los últimos 7 días debido a la escasez de DTU disponibles en el grupo elástico de su suscripción. 

Normalmente, se hace referencia a los recursos de Azure SQL Database como [recursos DTU](sql-database-what-is-a-dtu.md), que consisten en una medida combinada de los recursos de la CPU y E/S (E/S del registro de transacciones y datos). Los [recursos del grupo elástico de Azure ](sql-database-elastic-pool.md) se usan como un grupo de recursos disponibles de eDTU que se comparten entre varias bases de datos con finalidades de escala. Cuando se da el caso de que los recursos de eDTU disponibles en su grupo elástico no son lo suficientemente grandes como para admitir todas las bases de datos del grupo, el sistema detecta el problema de rendimiento por escasez de DTU en el grupo elástico.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona información sobre el grupo elástico, enumera las bases de datos que realizan un mayor consumo de DTU y proporciona un porcentaje de la DTU de grupo que usa la base de datos que más consume.

Dado que esta condición de rendimiento está relacionada con varias bases de datos que usan el mismo grupo de eDTU en el grupo elástico, los pasos de solución de problemas deben centrarse en las bases de datos que más consumen DTU. Le recomendamos que considere la posibilidad de reducir la carga de trabajo en las bases de datos que más consumen, incluida la optimización de las consultas que más consumen en estas bases de datos. Asimismo, también querrá asegurarse de que no consulta datos que no usa. Otro enfoque consiste en optimizar las aplicaciones que usan las bases de datos que realizan un mayor consumo de DTU y redistribuir la carga de trabajo entre varias bases de datos.

Si no es posible reducir y optimizar la carga de trabajo actual en las bases de datos que más consumen DTU, tenga en cuenta la posibilidad de aumentar el plan de tarifa de su grupo elástico. Dicho aumento da como resultado el aumento de las DTU disponibles en el grupo elástico.

## <a name="plan-regression"></a>Regresión de un plan

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una condición en la que se inicia Azure SQL Database mediante un plan de ejecución de consulta poco óptimo. El plan poco óptimo normalmente provoca un aumento en la ejecución de consultas, lo que incrementa los tiempos de espera de las consultas actuales y otras.

Azure SQL Database determina el plan de ejecución de consultas con el menor costo para la ejecución de una consulta. Sin embargo, dado que el tipo de las consultas y las cargas de trabajo cambian, a veces los planes existentes ya no son eficaces, o es posible que Azure SQL Database no haya hecho una buena evaluación. A fin de corregir esto, es posible forzar manualmente los planes de ejecución de consulta.

Este patrón de rendimiento detectable combina tres casos diferentes de regresión de un plan: regresión de un plan nuevo, regresión de un plan anterior y carga de trabajo cambiada en los planes existentes. Los tipos de regresiones de planes concretos que se producen se proporcionan en la propiedad &#8220;*details*&#8221; del registro de diagnóstico.

La nueva condición de regresión de un plan nuevo hace referencia a un estado en el que Azure SQL Database empieza a ejecutar un nuevo plan de ejecución de consulta que no es tan eficaz como el anterior. La condición de regresión de un plan anterior se refiere al estado en que Azure SQL Database pasa de usar un nuevo plan más eficaz al plan anterior que no es tan eficaz como el nuevo. La regresión de la carga de trabajo cambiada en los planes existentes se refiere al estado en el que dos planes (el nuevo y el anterior) se alternan continuamente, aunque la balanza se decanta más por el plan de rendimiento deficiente.

Para obtener más información sobre las regresiones de un plan, consulte [What is plan regression in SQL Server](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/06/09/what-is-plan-regression-in-sql-server/) (Qué es la regresión de un plan en SQL Server). 

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico genera los códigos hash de consulta, el identificador de plan correcto, el identificador de plan inadecuado y los identificadores de consulta que puede usarse como punto de partida para solucionar problemas de rendimiento de esta condición.

Le recomendamos que analice qué plan le puede ir mejor para sus consultas específicas que puede identificar con los códigos hash de consulta proporcionados. Una vez determine el plan que funciona mejor para sus consultas, puede forzarlo manualmente. Para obtener más información, consulte [How SQL Server prevents plan regressions](https://blogs.msdn.microsoft.com/sqlserverstorageengine/2017/04/25/you-shall-not-regress-how-sql-server-2017-prevents-plan-regressions/) (Cómo SQL Server impide las regresiones de plan).

> [!TIP]
> ¿Sabía que la inteligencia integrada de Azure puede administrar automáticamente los planes de ejecución de consultas con mejor rendimiento para sus bases de datos?
>
> A fin de que el rendimiento de Azure SQL Database se optimice de forma continua, le recomendamos que habilite [Azure SQL Database Automatic Tuning](sql-database-automatic-tuning.md): una característica exclusiva de la inteligencia integrada de Azure SQL que supervisa continuamente su instancia de Azure SQL Database y ajusta y crea automáticamente los planes de ejecución de consultas con mejor rendimiento para sus bases de datos.
>

## <a name="database-scoped-configuration-value-change"></a>Cambio del valor de configuración de ámbito de base de datos

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una condición en la que se ha producido un cambio en la configuración del ámbito de base de datos que está provocando la regresión de rendimiento detectada en comparación con el comportamiento de la carga de trabajo de base de datos de los 7 días anteriores. Esto indica que el cambio reciente realizado en la configuración de ámbito de base de datos no parece ser beneficioso para el rendimiento de la base de datos.

El cambio de configuración de ámbito de base de datos se puede establecer para cada base de datos individual. Esta configuración se usa en función de cada caso para optimizar el rendimiento individual de su base de datos. Se pueden configurar las siguientes opciones para cada base de datos: MAXDOP, LEGACY_CARDINALITY_ESTIMATION, PARAMETER_SNIFFING, QUERY_OPTIMIZER_HOTFIXES y CLEAR PROCEDURE_CACHE.

### <a name="troubleshooting"></a>Solución de problemas

El registro de diagnóstico proporciona los cambios en la configuración de ámbito de base de datos realizados recientemente que han provocado la degradación del rendimiento en comparación con el comportamiento de la carga de trabajo de los 7 días anteriores. Conviene que considere la posibilidad de revertir los cambios en la configuración a los valores anteriores, o quizá de realizar un ajuste valor por valor hasta que alcance el nivel de rendimiento deseado. Es posible que también quiera considerar la posibilidad de copiar los valores de configuración de ámbito de base de datos de una base de datos similar con un rendimiento satisfactorio. Si no puede solucionar los problemas de rendimiento, debe tener en cuenta la reversión a los valores predeterminados de Azure SQL Database e intentar ajustarlos a partir de su base de referencia.

Para obtener más información acerca de cómo optimizar la configuración de ámbito de base de datos y la sintaxis de T-SQL para cambiar la configuración, consulte [Modificar configuración de ámbito de base de datos (Transact-SQL)](https://msdn.microsoft.com/en-us/library/mt629158.aspx).

## <a name="slow-client"></a>Cliente lento

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una condición en la que el cliente que usa Azure SQL Database no puede consumir la salida de la base de datos lo suficientemente rápido como para que la base de datos pueda enviar los resultados. Dado que Azure SQL Database no encuentra los resultados de las consultas ejecutadas en un búfer, se ralentiza y espera a que el cliente consuma los resultados de consulta transmitidos antes de continuar. Esta condición también puede estar relacionada con una red lenta que no puede transmitir resultados lo suficientemente rápido de Azure SQL Database al cliente de consumo.

Esta condición solo se genera si se detecta una regresión del rendimiento en comparación con el comportamiento de la carga de trabajo de la base de datos de los 7 días anteriores. Esto garantiza que este problema de rendimiento se detecte únicamente si se ha producido una degradación del rendimiento estadísticamente significativa en comparación con el comportamiento de rendimiento anterior.

### <a name="troubleshooting"></a>Solución de problemas

Este patrón de rendimiento detectable indica una condición del lado cliente para la que es necesario encontrar una solución en la aplicación o la red del lado cliente. El registro de diagnóstico genera los códigos hash de consulta y los tiempos de espera que parecen estar esperando al máximo hasta que el cliente los consuma en las últimas dos horas de tiempo. Estas opciones pueden usarse como base para la solución de problemas.

Es posible que quiera considerar la posibilidad de optimizar el rendimiento de su aplicación para el consumo de estas consultas. Asimismo, también puede que quiera tener en cuenta los posibles problemas de latencia de red. Dado que el problema de degradación del rendimiento se basó en el cambio de la base de referencia de rendimiento de los 7 días anteriores, quizá querrá investigar si ha habido cambios en la aplicación o en la condición de la red dentro del período reciente que hayan podido provocar este evento de regresión del rendimiento. 

## <a name="pricing-tier-downgrade"></a>Degradación del plan de tarifa

### <a name="what-is-happening"></a>Qué pasa

Este patrón de rendimiento detectable indica una condición en la que se degrada el plan de tarifa de Azure SQL Database. Debido a la reducción de los recursos (DTU) disponibles en la base de datos, el sistema detecta una disminución en el rendimiento de la base de datos actual medido según la base de referencia de los 7 días anteriores.

Asimismo, podría haber una condición en la que el plan de tarifa de su suscripción a Azure SQL Database se degradara y, luego, se actualizara al nivel más alto en un período de tiempo muy corto. Esto indica una detección de degradación del rendimiento temporal que se describe en la sección de detalles del registro de diagnóstico como degradación y actualización del plan de tarifa.

### <a name="troubleshooting"></a>Solución de problemas

Si ha reducido su plan de tarifa y, por lo tanto, también han disminuido las DTU disponibles en Azure SQL Database, pero está satisfecho con el rendimiento, no es necesario que realice ninguna acción en este momento. Si después de la reducción de su plan de tarifa no está satisfecho con el rendimiento de Azure SQL Database, conviene que considere la posibilidad de reducir las cargas de trabajo de la base de datos o aumentar el plan de tarifa en un nivel superior.

## <a name="recommended-troubleshooting-flow"></a>Flujo de solución de problemas recomendado

Siga el diagrama de flujo siguiente para obtener un enfoque recomendado para solucionar los problemas de rendimiento mediante Intelligent Insights.

Navegue a Azure SQL Analytics y acceda a Intelligent Insights a través de Azure Portal. Intente ubicar la alerta de rendimiento entrante y haga clic en ella. Identifique qué ocurre en la página de detecciones. Observe el análisis de la causa principal proporcionado del problema, el texto de la consulta, las tendencias del tiempo de consulta y la evolución del incidente. Para resolverlo, use la recomendación de Intelligent Insights para intentar mitigar el problema de rendimiento . 

[![Diagrama de flujo de solución de problemas](./media/sql-database-intelligent-insights/intelligent-insights-troubleshooting-flowchart.png)](https://github.com/Microsoft/sql-server-samples/blob/master/samples/features/intelligent-insight/Troubleshoot%20Azure%20SQL%20Database%20performance%20issues%20using%20Intelligent%20Insight.pdf)

> [!TIP]
> Haga clic en el diagrama de flujo para descargar una versión en PDF de este diagrama de flujo.

Intelligent Insights normalmente necesita una hora para realizar el análisis de la causa principal del problema de rendimiento. En el caso de que no pueda encontrar el problema en Intelligent Insights (en la mayoría de los casos, se trata de problemas que han aparecido menos de una hora antes) y, si este problema es crucial para usted, use el Almacén de datos de consultas (QDS) para identificar manualmente la causa principal del problema de rendimiento. Para obtener más información, consulte [Supervisión del rendimiento mediante el Almacén de consultas](https://docs.microsoft.com/en-us/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store)

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información acerca de los conceptos de [Intelligent Insights](sql-database-intelligent-insights.md).
- Use el [Registro de diagnóstico de rendimiento de Azure SQL Database de Intelligent Insights](sql-database-intelligent-insights-use-diagnostics-log.md).
- Supervise [Azure SQL Database mediante Azure SQL Analytics](https://docs.microsoft.com/en-us/azure/log-analytics/log-analytics-azure-sql).
- Aprenda a [recopilar y usar los datos de registro provenientes de los recursos de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
