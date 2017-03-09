---
title: "Solución Azure SQL Analytics de Log Analytics | Microsoft Docs"
description: "La solución Azure SQL Analytics le ayuda a administrar las instancias de Azure SQL Database."
services: log-analytics
documentationcenter: 
author: bandersmsft
manager: carmonm
editor: 
ms.assetid: b2712749-1ded-40c4-b211-abc51cc65171
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/22/2017
ms.author: banders
translationtype: Human Translation
ms.sourcegitcommit: 503cf4afba4575492984891a681c187a8683a553
ms.openlocfilehash: 9d8fd7ec594671e1ea7bf06459494f1c3a1adbdf
ms.lasthandoff: 02/23/2017


---


# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Supervisión de Azure SQL Database mediante Azure SQL Analytics (versión preliminar) en Log Analytics

La solución Azure SQL Monitoring en Azure Log Analytics recopila y muestra métricas de rendimiento importantes de Azure SQL. Mediante el uso de las métricas que se recopilan con la solución, puede crear alertas y reglas de supervisión personalizadas. Y puede supervisar Azure SQL Database y las métricas de los grupos elásticos de varias suscripciones y grupos elásticos de Azure y visualizarlos. La solución también le ayuda a identificar los problemas de cada nivel de la pila de la aplicación.  Usa las [métricas de diagnóstico de Azure](log-analytics-azure-storage.md) junto con las vistas de Log Analytics para presentar datos sobre todas sus instancias de Azure SQL Database y sus grupos elásticos en una sola área de trabajo de Log Analytics.

Actualmente, la versión preliminar de esta solución admite hasta 150 000 instancias de Azure SQL Database y 5000 grupos elásticos de SQL por área de trabajo.

La solución Azure SQL Monitoring, al igual que otras disponibles para Log Analytics, le ayuda a supervisar y recibir notificaciones sobre el estado de los recursos de Azure y, en este caso, de Azure SQL Database. Microsoft Azure SQL Database es un servicio de base de datos relacional escalable que proporciona funcionalidades conocidas de tipo SQL Server para aplicaciones que se ejecutan en la nube de Azure. Log Analytics le ayuda a recopilar, correlacionar y visualizar datos estructurados y no estructurados.

## <a name="connected-sources"></a>Orígenes conectados

A diferencia de la mayoría de las otras soluciones de Log Analytics, la solución Azure SQL Monitoring no usa agentes para conectarse al servicio Log Analytics.

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Soporte técnico | Descripción |
| --- | --- | --- |
| [Agentes de Windows](log-analytics-windows-agents.md) | No | La solución no utiliza agentes directos de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | No | La solución no utiliza agentes directos de Linux. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | No | La solución no utiliza una conexión directa entre el agente de SCOM y Log Analytics. |
| [Cuenta de Azure Storage](log-analytics-azure-storage.md) | Sí | Se envían datos de métricas de Azure a Log Analytics mediante la cuenta de almacenamiento. |

## <a name="prerequisites"></a>Requisitos previos

1. Una suscripción de Azure. Si no tiene ninguna, puede crear una [gratis](https://azure.microsoft.com/free/).
2. Un área de trabajo de Log Analytics. Puede usar una existente, o bien puede [crear una nueva](log-analytics-get-started.md) para empezar a usar esta solución.
3. Habilite Diagnósticos de Azure para sus instancias de Azure SQL Database y para los grupos elásticos y [configúrelo para que envíe sus datos a Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configuración

Realice los pasos siguientes para agregar la solución Azure SQL Monitoring al área de trabajo.

1. Agregue la solución Azure SQL Monitoring al área de trabajo mediante el proceso descrito en [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md).
2. En Azure Portal, haga clic en **Nuevo** (el símbolo +) y, a continuación, en la lista de recursos, seleccione **Supervisión y administración**.  
    ![Supervisión + Administración](./media/log-analytics-azure-sql/monitoring-management.png)
3. En la lista **Supervisión y administración**, haga clic en **Ver todo**.
4. En la lista **Recomendado**, haga clic en **Más** y, a continuación, en la nueva lista, busque **Azure SQL Analytics (versión preliminar)** y selecciónelo.  
    ![Solución Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. En la hoja **Azure SQL Analytics (versión preliminar)**, haga clic en **Crear**.  
    ![Creación](./media/log-analytics-azure-sql/portal-create.png)
6. En la hoja **Crear nueva solución**, seleccione el área de trabajo a la que desea agregar la solución y, a continuación, haga clic en **Crear**.  
    ![Agregar a área de trabajo](./media/log-analytics-azure-sql/add-to-workspace.png)


### <a name="to-configure-multiple-azure-subscriptions"></a>Configuración de varias suscripciones de Azure

Para admitir varias suscripciones, use el script de PowerShell de [Enable Azure resource metrics logging using PowerShell](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/) (Habilitar registro de métricas de recursos de Azure mediante PowerShell). Proporcione el identificador de recurso del área de trabajo como un parámetro al ejecutar el script para enviar los datos de diagnóstico de los recursos de una suscripción de Azure a un área de trabajo de otra suscripción de Azure.

**Ejemplo**

```
PS C:\> $WSID = "/subscriptions/<subID>/resourcegroups/oms/providers/microsoft.operationalinsights/workspaces/omsws"
```

```
PS C:\> .\Enable-AzureRMDiagnostics.ps1 -WSID $WSID
```

## <a name="using-the-solution"></a>Uso de la solución

Cuando la solución se agrega al área de trabajo, el icono de Azure SQL Analytics se agrega al área de trabajo y aparece en la introducción. El icono muestra el número de bases de datos y grupos elásticos de Azure SQL a los que está conectada la solución.

![Icono de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-monitoring-data"></a>Visualización de los datos de Azure SQL Monitoring

Haga clic en el icono de **Azure SQL Monitoring** para que se abra el panel de Azure SQL Analytics. El panel incluye las columnas de la tabla siguiente. Cada columna muestra hasta los diez principales elementos que coinciden con los criterios de esa columna para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que devuelva todos los registros haciendo clic en **Ver todo** en la parte inferior de la columna o haciendo clic en el encabezado de la columna.

Para más información, consulte [Opciones y rendimiento de SQL Database: descripción de lo que está disponible en cada nivel de servicio](../sql-database/sql-database-service-tiers.md).



![Panel de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-dash-01.png)



![Panel de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-dash-02.png)

| Columna | Descripción |
| --- | --- |
| **Azure SQL Database Analytics** | &nbsp; |
| Las N bases de datos primeras según el uso de DTU &gt; 90% | Este panel muestra el número de instancias de Azure SQL Database cuyo uso de DTU es superior al 90% durante el tiempo seleccionado. El icono superior muestra el número de bases de datos que han consumido más del 90% de la disponibilidad de DTU asignada total durante el tiempo especificado para todas las bases de datos que está supervisando en Log Analytics.  Haga clic en el nombre de una base de datos para ejecutar una búsqueda de registros que muestre un gráfico de líneas que compare el uso de DTU de la base de datos con el de todas las demás bases de datos supervisadas por el área de trabajo. |
| Las N bases de datos primeras según el uso de CPU &gt; 90% | Este panel muestra el número de instancias de Azure SQL Database cuyo uso de CPU es superior al 90% durante el tiempo seleccionado. El icono superior muestra el número de bases de datos que han consumido más del 90% de la disponibilidad de CPU asignada total durante el tiempo especificado para todas las bases de datos que está supervisando en Log Analytics.  Haga clic en el nombre de una base de datos para ejecutar una búsqueda de registros que muestre un gráfico de líneas que compare el uso de CPU de la base de datos con el de todas las demás bases de datos supervisadas por el área de trabajo. |
| Las N bases de datos primeras según el consumo de almacenamiento &gt; 90% | Este panel muestra el número de instancias de Azure SQL Database que han consumido un valor superior al 90% de su asignación de almacenamiento para el tiempo seleccionado. El icono superior muestra el número de bases de datos que han superado el umbral del 90% durante el tiempo especificado para todas las bases de datos que está supervisando en Log Analytics.  Haga clic en el nombre de una base de datos para ejecutar una búsqueda de registros que muestre un gráfico de líneas que compare el consumo de almacenamiento de la base de datos con el de todas las demás bases de datos supervisadas por el área de trabajo. |
| **Grupos elásticos de Azure SQL** | &nbsp; |
| Los N grupos elásticos primeros según DTU &gt; 90% | Este panel muestra el número de grupos elásticos de Azure SQL que han consumido un valor superior al 90% de su asignación de DTU para el tiempo seleccionado. El icono superior muestra el número de grupos elásticos que han superado el umbral del 90% durante el tiempo especificado para todos los grupos elásticos que está supervisando en Log Analytics.  Haga clic en el nombre de un grupo elástico para ejecutar una búsqueda de registros que muestre un gráfico de líneas que compare el consumo de almacenamiento de los grupos elásticos con el de todos los demás grupos elásticos supervisados por el área de trabajo. |
| Los N grupos elásticos primeros según CPU &gt; 90% | Este panel muestra el número de grupos elásticos de Azure SQL cuyo uso de CPU es superior al 90% durante el tiempo seleccionado. El icono superior muestra el número de grupos elásticos que han superado el umbral del 90% durante el tiempo especificado para todos los grupos elásticos que está supervisando en Log Analytics.  Haga clic en el nombre de un grupo elástico para ejecutar una búsqueda de registros que muestre un gráfico de líneas que compare la utilización de CPU de los grupos elásticos con la de todos los demás grupos elásticos supervisados por el área de trabajo. |
| Los N grupos elásticos primeros según el consumo de almacenamiento &gt; 90% | Este panel muestra el número de grupos elásticos de Azure SQL que han consumido un valor superior al 90% de su asignación de almacenamiento para el tiempo seleccionado. El icono superior muestra el número de grupos elásticos que han superado el umbral del 90% durante el tiempo especificado para todos los grupos elásticos que está supervisando en Log Analytics.  Haga clic en el nombre de un grupo elástico para ejecutar una búsqueda de registros que muestre un gráfico de líneas que compare el consumo de almacenamiento de los grupos elásticos con el de todos los demás grupos elásticos supervisados por el área de trabajo. |
| **Registros de actividad de Azure SQL** | &nbsp; |
| Auditoría de actividad de Azure SQL | Este panel muestra el número de registros de actividad de Azure relacionados con Azure SQL durante el tiempo seleccionado. Haga clic en un elemento para ejecutar una búsqueda de registros que muestre detalles adicionales sobre el elemento. |



### <a name="analyze-data-and-create-alerts"></a>Análisis de datos y creación de alertas

La solución incluye consultas útiles para ayudarle a analizar los datos. Si se desplaza a la derecha, el panel mostrará varias consultas comunes en las que puede hacer clic para realizar una [búsqueda de registros](log-analytics-log-searches.md) para datos de Azure SQL.

![Consultas](./media/log-analytics-azure-sql/azure-sql-queries.png)

La solución incluye algunas *consultas basadas en alertas*, tal y como se muestra más arriba, que puede usar para generar alertas sobre umbrales específicos para las bases de datos y grupos elásticos de Azure SQL.

#### <a name="to-configure-an-alert-for-your-workspace"></a>Configuración de una alerta para el área de trabajo

1. Abra el [portal de OMS](http://mms.microsoft.com/) e inicie sesión.
2. Abra el área de trabajo que ha configurado para la solución.
3. En la página Información general, haga clic en el icono de **Azure SQL Analytics (versión preliminar)**.
4. Desplácese hacia la derecha y haga clic en una consulta para empezar a crear una alerta.  
![consulta de alerta](./media/log-analytics-azure-sql/alert-query.png)
5. En Búsqueda de registros, haga clic en **Alerta**.  
![crear alerta en la búsqueda](./media/log-analytics-azure-sql/create-alert01.png)
6. En la página **Agregar regla de alerta** página, configure las propiedades adecuadas y los umbrales específicos que desee y, a continuación, haga clic en **Guardar**.  
![agregar regla de alerta](./media/log-analytics-azure-sql/create-alert02.png)

### <a name="act-on-azure-sql-monitoring-data"></a>Actuación sobre datos de Azure SQL Monitoring

A modo de ejemplo, una de las consultas más útiles que puede realizar es comparar el uso de DTU de todos los grupos elásticos de Azure SQL de todas sus suscripciones de Azure. La unidad de rendimiento de base de datos (DTU) proporciona una manera de describir la capacidad relativa de un nivel de rendimiento de las bases de datos y grupos elásticos de los niveles Básico, Estándar y Premium. Las DTU se basan en una medición combinada de CPU, memoria y número de lecturas y escrituras. A medida que aumentan las DTU, aumenta la capacidad que ofrece el nivel de rendimiento. Por ejemplo, un nivel de rendimiento con 5 DTU es cinco veces más potente que un nivel de rendimiento con 1 DTU. Se aplica una cuota máxima de DTU para cada servidor y grupo elástico.

Si ejecuta la siguiente consulta de búsqueda de registros, podrá saber fácilmente si sus grupos elásticos de Azure SQL se están utilizando por debajo de sus posibilidades o en exceso.

```
Type=AzureMetrics ResourceId=*"/ELASTICPOOLS/"* MetricName=dtu_consumption_percent | measure avg(Average) by Resource | display LineChart
```

En el ejemplo siguiente, puede ver que un grupo elástico tiene un uso elevado de casi el 100% de DTU, mientras que otros tienen muy poco uso. Puede investigar más en profundidad para solucionar los posibles cambios recientes de su entorno mediante los registros de actividad de Azure.

![Resultados de la búsqueda de registros: uso intensivo](./media/log-analytics-azure-sql/log-search-high-util.png)

## <a name="see-also"></a>Consulte también

- Use [Búsquedas de registros](log-analytics-log-searches.md) en Log Analytics para ver datos detallados de Azure SQL.
- [Cree sus propios paneles](log-analytics-dashboards.md) que muestren datos de Azure SQL.
- [Cree alertas](log-analytics-alerts.md) cuando se produzcan eventos específicos de Azure SQL.

