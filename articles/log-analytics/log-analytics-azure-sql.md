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
ms.date: 09/26/2017
ms.author: banders
ms.openlocfilehash: 0b0d91b130172eb3506fdebb9547ab6ba5cc3780
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-azure-sql-database-using-azure-sql-analytics-preview-in-log-analytics"></a>Supervisión de Azure SQL Database mediante Azure SQL Analytics (versión preliminar) en Log Analytics

![Símbolo de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-symbol.png)

La solución Azure SQL Analytics de Azure Log Analytics recopila y muestra métricas de rendimiento importantes de Azure SQL. Mediante el uso de las métricas que se recopilan con la solución, puede crear alertas y reglas de supervisión personalizadas. Y puede supervisar Azure SQL Database y las métricas de los grupos elásticos de varias suscripciones y grupos elásticos de Azure y visualizarlos. La solución también le ayuda a identificar los problemas de cada nivel de la pila de la aplicación.  Usa las [métricas de diagnóstico de Azure](log-analytics-azure-storage.md) junto con las vistas de Log Analytics para presentar datos sobre todas sus instancias de Azure SQL Database y sus grupos elásticos en una sola área de trabajo de Log Analytics.

Actualmente, la versión preliminar de esta solución admite hasta 150 000 instancias de Azure SQL Database y 5000 grupos elásticos de SQL por área de trabajo.

La solución Azure SQL Analytics, al igual que otras disponibles para Log Analytics, le ayuda a supervisar y recibir notificaciones sobre el estado de los recursos de Azure y, en este caso, de Azure SQL Database. Microsoft Azure SQL Database es un servicio de base de datos relacional escalable que proporciona funcionalidades conocidas de tipo SQL Server para aplicaciones que se ejecutan en la nube de Azure. Log Analytics le ayuda a recopilar, correlacionar y visualizar datos estructurados y no estructurados.

## <a name="connected-sources"></a>Orígenes conectados

La solución Azure SQL Analytics no usa agentes para conectarse al servicio Log Analytics.

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución.

| Origen conectado | Soporte técnico | Descripción |
| --- | --- | --- |
| [Agentes de Windows](log-analytics-windows-agents.md) | No | La solución no utiliza agentes directos de Windows. |
| [Agentes de Linux](log-analytics-linux-agents.md) | No | La solución no utiliza agentes directos de Linux. |
| [Grupo de administración de SCOM](log-analytics-om-agents.md) | No | La solución no utiliza una conexión directa entre el agente de SCOM y Log Analytics. |
| [Cuenta de Azure Storage](log-analytics-azure-storage.md) | No | Log Analytics no lee los datos de una cuenta de almacenamiento. |
| [Diagnóstico de Azure](log-analytics-azure-storage.md) | Sí | Azure envía directamente los datos de métricas y registros de Azure a Log Analytics. |

## <a name="prerequisites"></a>Requisitos previos

- Una suscripción de Azure. Si no tiene ninguna, puede crear una [gratis](https://azure.microsoft.com/free/).
- Un área de trabajo de Log Analytics. Puede usar una existente, o bien puede [crear una nueva](log-analytics-get-started.md) para empezar a usar esta solución.
- Habilite Diagnósticos de Azure para sus instancias de Azure SQL Database y para los grupos elásticos y [configúrelo para que envíe sus datos a Log Analytics](https://blogs.technet.microsoft.com/msoms/2017/01/17/enable-azure-resource-metrics-logging-using-powershell/).

## <a name="configuration"></a>Configuración

Realice los pasos siguientes para agregar la solución Azure SQL Analytics al área de trabajo.

1. Agregue la Azure SQL Analytics al área de trabajo desde [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/Microsoft.AzureSQLAnalyticsOMS?tab=Overview) o mediante el proceso descrito en el artículo sobre [incorporación de soluciones de Log Analytics desde la Galería de soluciones](log-analytics-add-solutions.md).
2. En Azure Portal, haga clic en **Nuevo** (el símbolo +) y, a continuación, en la lista de recursos, seleccione **Supervisión y administración**.  
    ![Supervisión + Administración](./media/log-analytics-azure-sql/monitoring-management.png)
3. En la lista **Supervisión y administración**, haga clic en **Ver todo**.
4. En la lista **Recomendado**, haga clic en **Más** y, a continuación, en la nueva lista, busque **Azure SQL Analytics (versión preliminar)** y selecciónelo.  
    ![Solución Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-solution-portal.png)
5. En el panel **Azure SQL Analytics (versión preliminar)**, haga clic en **Crear**.  
    ![Creación](./media/log-analytics-azure-sql/portal-create.png)
6. En el panel **Crear nueva solución**, seleccione el área de trabajo a la que desea agregar la solución y, a continuación, haga clic en **Crear**.  
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

>[!NOTE]
> Actualice el área de trabajo de Log Analytics para obtener la versión más reciente de Azure SQL Analytics.
>

Cuando la solución se agrega al área de trabajo, el icono de Azure SQL Analytics se agrega al área de trabajo y aparece en la introducción. El icono muestra el número de bases de datos y grupos elásticos de Azure SQL a los que está conectada la solución.

![Icono de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-tile.png)

### <a name="viewing-azure-sql-analytics-data"></a>Visualización de los datos de Azure SQL Analytics

Haga clic en el icono de **Azure SQL Analytics** para que se abra el panel de Azure SQL Analytics. El panel incluye la información general de todas las bases de datos que se supervisan a través de distintas perspectivas. Para que estas distintas perspectivas funcionen, debe habilitar las métricas o los registros apropiados en los recursos de SQL para que se transmitan al área de trabajo de Azure Log Analytics. 

![Información general de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-overview.png)

Si selecciona cualquiera de los iconos, se abre un informe detallado de la perspectiva específica.

![Tiempos de expiración de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-timeouts.png)

Cada perspectiva proporciona resúmenes en el nivel de suscripción, servidor, grupo elástico y base de datos. Además, cada perspectiva muestra a la derecha un informe específico de la perspectiva. Seleccione suscripción, servidor, grupo o base de datos en la lista para seguir obteniendo los detalles.

| Perspectiva | Descripción |
| --- | --- |
| Recurso por tipo | Perspectiva que considera todos los recursos supervisados. El informe detallado proporciona el resumen de las métricas de GB y DTU. |
| Información detallada | Ofrece un informe detallado jerárquico de Intelligent Insights. Más información sobre Intelligent Insights. |
| Errors | Ofrece un informe detallado jerárquico de los errores de SQL que se produjeron en las bases de datos. |
| Tiempos de expiración | Ofrece un informe detallado jerárquico de los tiempos de expiración de SQL que se produjeron en las bases de datos. |
| Bloqueos | Ofrece un informe detallado jerárquico de los bloqueos de SQL que se produjeron en las bases de datos. |
| Esperas de la base de datos | Ofrece un informe detallado jerárquico de las estadísticas de espera de SQL en el nivel de base de datos. Incluye resúmenes del tiempo de espera total y el tiempo de espera por tipo de espera. |
| Duración de la consulta | Ofrece un informe detallado jerárquico de las estadísticas de ejecución de consulta, como la duración de la consulta, el uso de CPU, el uso de E/S de datos y el uso de E/S de registro. |
| Esperas de consulta | Ofrece un informe detallado jerárquico de las estadísticas de espera de consulta por categoría de espera. |

### <a name="intelligent-insights-report"></a>Informe de Intelligent Insights

Todas las instancias de Intelligent Insights que se recopilan se pueden visualizar y acceder a través de la perspectiva de información detallada. [Haga clic aquí para más información sobre Intelligent Insights](../sql-database/sql-database-intelligent-insights.md)

![Información detallada de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-insights.png)

### <a name="elastic-pool-and-database-reports"></a>Informes de grupos elásticos y bases de datos

Tanto los grupos elásticos como las bases de datos tienen sus propios informes específicos que muestran cómo se recopilan todos los datos para el recurso en el tiempo especificado.

![Base de datos Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-database.png)

![Grupo elástico de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-pool.png)

### <a name="query-reports"></a>Informes de consulta

Con la perspectiva de duración de la cadena y esperas de la cadena, puede correlacionar el rendimiento de cualquier consulta a través del informe de consulta. En este informe se compara el rendimiento de la consulta en distintas bases de datos y facilita el proceso de identificar las bases de datos que se completan de manera correcta la consulta seleccionada en lugar de las lentas.

![Consultas de Azure SQL Analytics](./media/log-analytics-azure-sql/azure-sql-sol-queries.png)

### <a name="analyze-data-and-create-alerts"></a>Análisis de datos y creación de alertas

Las alertas se pueden crear fácilmente con los datos procedentes de los recursos de Azure SQL Database. Estas son un par de consultas de [búsqueda de registros](log-analytics-log-searches.md) útiles que puede usar para las alertas:

*DTU alta en Azure SQL Database*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/DATABASES/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

*DTU alta en el grupo elástico de Azure SQL Database*

```
AzureMetrics | where ResourceProvider=="MICROSOFT.SQL" and ResourceId contains "/ELASTICPOOLS/" and MetricName=="dtu_consumption_percent" | summarize avg(Maximum) by ResourceId
```

Puede usar estas consultas basadas en alertas para generar alertas sobre umbrales específicos para Azure SQL Database y los grupos elásticos. Configuración de una alerta para el área de trabajo de OMS:

#### <a name="to-configure-an-alert-for-your-workspace"></a>Configuración de una alerta para el área de trabajo

1. Abra el [portal de OMS](http://mms.microsoft.com/) e inicie sesión.
2. Abra el área de trabajo que ha configurado para la solución.
3. En la página Información general, haga clic en el icono de **Azure SQL Analytics (versión preliminar)**.
4. Ejecute una de las consultas de ejemplo.
5. En Búsqueda de registros, haga clic en **Alerta**.  
![crear alerta en la búsqueda](./media/log-analytics-azure-sql/create-alert01.png)
6. En la página **Agregar regla de alerta** página, configure las propiedades adecuadas y los umbrales específicos que desee y, a continuación, haga clic en **Guardar**.  
![agregar regla de alerta](./media/log-analytics-azure-sql/create-alert02.png)

## <a name="see-also"></a>Otras referencias

- Use [Búsquedas de registros](log-analytics-log-searches.md) en Log Analytics para ver datos detallados de Azure SQL.
- [Cree sus propios paneles](log-analytics-dashboards.md) que muestren datos de Azure SQL.
- [Cree alertas](log-analytics-alerts.md) cuando se produzcan eventos específicos de Azure SQL.
