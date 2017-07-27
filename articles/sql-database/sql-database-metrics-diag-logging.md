---
title: "Métricas y registros de diagnóstico de Azure SQL Database | Microsoft Docs"
description: "Aprenda a configurar el recurso Azure SQL Database para almacenar estadísticas de uso de recursos, conectividad y ejecución de consultas."
services: sql-database
documentationcenter: 
author: vvasic
manager: jhubbard
editor: 
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: monitor & tune
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/24/2017
ms.author: vvasic
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: 71c286061e214898a7f10fa8dfd6c6d86af8db39
ms.contentlocale: es-es
ms.lasthandoff: 06/10/2017


---
# <a name="azure-sql-database-metrics-and-diagnostics-logging"></a>Métricas y registros de diagnóstico de Azure SQL Database 
Azure SQL Database puede emitir métricas y registros de diagnóstico para facilitar la supervisión. Azure SQL Database se puede configurar para que almacene el uso de recursos, los trabajadores y las sesiones y la conectividad en uno de estos recursos de Azure:
- **Azure Storage**: para archivar grandes cantidades de telemetría a un pequeño precio
- **Centro de eventos de Azure**: para integrar la telemetría de Azure SQL Database con la solución de supervisión personalizada o las canalizaciones activas
- **Azure Log Analytics**: para la solución de supervisión integrada con funcionalidades de generación de informes, alertas y mitigación 

    ![arquitectura](./media/sql-database-metrics-diag-logging/architecture.png)

## <a name="enable-logging"></a>Habilitación del registro

Las métricas y los registros de diagnóstico no están habilitados de forma predeterminada. Puede habilitar y administrar las métricas y los registros de diagnóstico con uno de los métodos siguientes:
- Portal de Azure
- PowerShell
- Azure CLI
- API de REST 
- Plantilla de Resource Manager

Al habilitar las métricas y los registros de diagnóstico, debe especificar el recurso de Azure donde se recopilan los datos seleccionados. Opciones disponibles:
- Log Analytics
- Centro de eventos
- Almacenamiento de Azure 

Puede aprovisionar un nuevo recurso de Azure o seleccionar uno existente. Después de seleccionar el recurso de almacenamiento, debe especificar qué datos se van a recopilar. Las opciones disponibles incluyen:

- **[Métricas de 1 minuto](sql-database-metrics-diag-logging.md#1-minute-metrics)**: contiene porcentaje de DTU; límite de DTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; conexiones correctas, erróneas o bloqueadas por el firewall; porcentaje de sesiones; porcentaje de trabajadores; almacenamiento; porcentaje de almacenamiento y porcentaje de almacenamiento de XTP.

Si especifica una cuenta de Centro de eventos o Azure Storage, puede indicar una directiva de retención para especificar que los datos que sean más antiguos que un período de tiempo seleccionado se eliminen. Si especifica Log Analytics, la directiva de retención depende del plan de tarifa seleccionado. Más información sobre [Log Analytics Precios](https://azure.microsoft.com/pricing/details/log-analytics/). 

Se recomienda leer los artículos [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para comprender no solo cómo habilitar los registros, sino también las categorías de métricas y registro que admiten los distintos servicios de Azure.

### <a name="azure-portal"></a>Portal de Azure

Para habilitar la recopilación de métricas y registros de diagnóstico en Azure Portal, vaya a la página de Azure SQL Database o del grupo elástico y haga clic en **Configuración de diagnóstico**.

   ![habilitar en Azure Portal](./media/sql-database-metrics-diag-logging/enable-portal.png)

### <a name="powershell"></a>PowerShell

Para habilitar las métricas y los registros de diagnóstico con PowerShell, use los siguientes comandos:

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

- Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your service bus rule id] -Enabled $true
   ```

   El id. de regla de bus de servicio es una cadena con este formato:

   ```powershell
   {service bus resource ID}/authorizationrules/{key name}
   ``` 

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```powershell
   Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
   ```

- Puede obtener el identificador de recurso de su área de trabajo de Log Analytics con el comando siguiente:

   ```powershell
   (Get-AzureRmOperationalInsightsWorkspace).ResourceId
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="cli"></a>CLI

Para habilitar las métricas y los registros de diagnóstico con CLI de Azure, use los siguientes comandos:

- Para habilitar el almacenamiento de registros de diagnóstico en una cuenta de almacenamiento, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --storageId <storageAccountId> --enabled true
   ```

   El identificador de la cuenta de almacenamiento es el identificador de recurso para la cuenta de almacenamiento a la que desea enviar los registros.

- Para habilitar el streaming de registros de diagnóstico a un centro de eventos, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --serviceBusRuleId <serviceBusRuleId> --enabled true
   ```

   El id. de regla de bus de servicio es una cadena con este formato:

   ```azurecli-interactive
   {service bus resource ID}/authorizationrules/{key name}
   ```

- Para habilitar el envío de registros de diagnóstico a un área de trabajo de Log Analytics, use este comando:

   ```azurecli-interactive
   azure insights diagnostic set --resourceId <resourceId> --workspaceId <resource id of the log analytics workspace> --enabled true
   ```

Puede combinar estos parámetros para habilitar varias opciones de salida.

### <a name="rest-api"></a>API de REST

Lea sobre cómo [cambiar la configuración de diagnóstico con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx). 

### <a name="resource-manager-template"></a>Plantilla de Resource Manager

Lea sobre cómo [habilitar la configuración de diagnóstico al crear recursos con la plantilla de Resource Manager](../monitoring-and-diagnostics/monitoring-enable-diagnostic-logs-using-template.md). 

## <a name="stream-into-log-analytics"></a>Transmisión a Log Analytics 
Las métricas y los registros de diagnóstico de Azure SQL Database se pueden transmitir a Log Analytics mediante la opción integrada "Enviar a Log Analytics" del portal o al habilitar Log Analytics en una configuración de diagnóstico por medio de cmdlets de Azure PowerShell, CLI de Azure o API de REST de Azure Monitor.

### <a name="installation-overview"></a>Introducción a la instalación

La supervisión de la línea de Azure SQL Database es sencilla con Log Analytics. Se necesitan tres pasos:

1.  Crear el recurso Log Analytics
2.  Configurar bases de datos para registrar las métricas y los registros de diagnóstico en el recurso Log Analytics creado
3.  Instalar la solución **Azure SQL Analytics** desde la galería en Log Analytics

### <a name="create-log-analytics-resource"></a>Crear el recurso Log Analytics

1. Haga clic en **Nuevo** en el menú del lateral izquierdo.
2. Haga clic en **Supervisión y administración**.
3. Haga clic en **Log Analytics**.
4. Rellene el formulario de Log Analytics con la información adicional necesaria: nombre de área de trabajo, suscripción, grupo de recursos, ubicación y plan de tarifa.

   ![Log Analytics](./media/sql-database-metrics-diag-logging/log-analytics.png)

### <a name="configure-databases-to-record-metrics-and-diagnostic-logs"></a>Configurar bases de datos para registrar las métricas y los registros de diagnóstico

La manera más sencilla de configurar la ubicación en que las bases de datos registran sus métricas es mediante Azure Portal. En Azure Portal, vaya al recurso Azure SQL Database y haga clic en **Configuración de diagnóstico**. 

### <a name="install-the-azure-sql-analytics-solution-from-gallery"></a>Instalar la solución Azure SQL Analytics desde la galería  

1. Una vez que el recurso Log Analytics se ha creado y que los datos están llegando a él, instale la solución Azure SQL Analytics. Puede hacerlo mediante la **Galería de soluciones**, que encontrará en la página principal de OMS y en el menú lateral. En la galería, busque y haga clic en la solución **Azure SQL Analytics** y luego en **Agregar**.

   ![solución de supervisión](./media/sql-database-metrics-diag-logging/monitoring-solution.png)

2. En la página principal de OMS aparece un nuevo icono llamado **Azure SQL Analytics**. Al seleccionar este icono se abre el panel de Azure SQL Analytics.

### <a name="using-azure-sql-analytics-solution"></a>Uso de la solución Azure SQL Analytics

Azure SQL Analytics es un panel jerárquico que permite navegar por la jerarquía de recursos de Azure SQL Database. Esta capacidad permite realizar supervisión de alto nivel y además limitar la supervisión a solo el conjunto adecuado de recursos.
El panel contiene las listas de los recursos incluidos en el recurso seleccionado. Por ejemplo, en una suscripción seleccionada puede ver todos los servidores, los grupos elásticos y las bases de datos que pertenecen a esa suscripción. Además, en los grupos elásticos y las bases de datos puede ver las métricas de uso de ese recurso. Esto incluye gráficos de DTU, CPU, E/S, registro, sesiones, trabajadores, conexiones y almacenamiento en GB.

## <a name="stream-into-azure-event-hub"></a>Transmisión al Centro de eventos de Azure

Las métricas y los registros de diagnóstico de Azure SQL Database se pueden transmitir al Centro de eventos mediante la opción integrada "Transmitir a un centro de eventos" del portal o al habilitar el id. de regla de bus de servicio en una configuración de diagnóstico por medio de cmdlets de Azure PowerShell, CLI de Azure o API de REST de Azure Monitor. 

### <a name="what-to-do-with-metrics-and-diagnostic-logs-in-event-hub"></a>Utilidad de las métricas y los registros de diagnóstico del Centro de eventos
Una vez que los datos seleccionados se transmiten al Centro de eventos, está un paso más cerca de habilitar escenarios de supervisión avanzados. Centros de eventos actúa como la "puerta principal" de una canalización de eventos y, una vez que los datos se recopilan en un Centro de eventos, se pueden transformar y almacenar con cualquier proveedor de análisis en tiempo real o adaptadores de procesamiento por lotes/almacenamiento. Centros de eventos desacopla la producción de un flujo de eventos desde el consumo de los eventos, para que los consumidores de eventos pueden tener acceso a los eventos según su propia programación. Para más información sobre el Centro de eventos, vea:

- [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
- [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)


Estas son solo algunas formas en que se podría usar la funcionalidad de transmisión:

-   Visualización del estado del servicio mediante la transmisión de datos de "ruta de acceso activa" a PowerBI: con Event Hubs, Stream Analytics y Power BI, puede transformar fácilmente las métricas y los datos de diagnóstico en información prácticamente en tiempo real sobre los servicios de Azure. Para obtener una introducción sobre cómo configurar Event Hubs, procesar datos con Stream Analytics y usar Power BI como salida, vea [Stream Analytics y Power BI](../stream-analytics/stream-analytics-power-bi-dashboard.md).
-   Transmisión de registros a registros de terceros y flujos de telemetría: con la transmisión de Event Hubs puede enviar las métricas y los registros de diagnóstico a distintas soluciones de supervisión y análisis de registros de terceros. 
-   Creación de una plataforma personalizada de registro y telemetría: si ya tiene una plataforma de telemetría personalizada o está pensando en crear una, la naturaleza altamente escalable de suscripción y publicación de Event Hubs permite introducir registros de diagnóstico de manera flexible. Vea la [guía de Dan Rosanova para usar Event Hubs en una plataforma de telemetría de escala global](https://azure.microsoft.com/documentation/videos/build-2015-designing-and-sizing-a-global-scale-telemetry-platform-on-azure-event-Hubs/).

## <a name="stream-into-azure-storage"></a>Transmisión a Azure Storage

Las métricas y los registros de diagnóstico de Azure SQL Database se pueden almacenar en Azure Storage mediante la opción integrada "Archivar en una cuenta de almacenamiento" de Azure Portal o al habilitar Azure Storage en una configuración de diagnóstico por medio de cmdlets de Azure PowerShell, CLI de Azure o API de REST de Azure Monitor.

### <a name="schema-of-metrics-and-diagnostic-logs-in-the-storage-account"></a>Esquema de métricas y registros de diagnóstico en la cuenta de almacenamiento

Una vez que ha configurado la recopilación de métricas y los registros de diagnóstico, se crea un contenedor de almacenamiento en la cuenta de almacenamiento seleccionada cuando las primeras filas de datos están disponibles. La estructura de estos blobs es:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ databases/{database_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```
    
O, sencillamente:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/{resource Id}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Por ejemplo, un nombre de blob para métricas de 1 minuto podría ser:

```powershell
insights-metrics-minute/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.SQL/ servers/Server1/databases/database1/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Si quiere registrar los datos del grupo elástico, el nombre de blob es un poco distinto:

```powershell
insights-{metrics|logs}-{category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/ RESOURCEGROUPS/{resource group name}/PROVIDERS/Microsoft.SQL/servers/{resource_server}/ elasticPools/{elastic_pool_name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

### <a name="download-metrics-and-logs-from-azure-storage"></a>Descargar métricas y registros de Azure Storage

Vea [Download metrics and diagnostic logs from Azure Storage](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs) (Descargar métricas y registros de diagnóstico de Azure Storage).

## <a name="1-minute-metrics"></a>métricas de 1 minuto

| |  |
|---|---|
|**Recurso**|**Métricas**|
|Base de datos|Porcentaje de DTU; DTU usada; límite de DTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; conexiones correctas, erróneas o bloqueadas por el firewall; porcentaje de sesiones; porcentaje de trabajadores; almacenamiento; porcentaje de almacenamiento; porcentaje de almacenamiento de XTP e interbloqueos |
|Grupo elástico|porcentaje de eDTU; eDTUusada; límite de eDTU; porcentaje de CPU; porcentaje de lectura de datos físicos; porcentaje de escritura en registro; porcentaje de sesiones; porcentaje de trabajadores; almacenamiento; porcentaje de almacenamiento; límite de almacenamiento y porcentaje de almacenamiento de XTP |
|||

## <a name="next-steps"></a>Pasos siguientes

- Lea los artículos [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md) e [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) para comprender no solo cómo habilitar los registros, sino también las categorías de métricas y registro que admiten los distintos servicios de Azure.
- Lea estos artículos para obtener información sobre Event Hubs:
   - [¿Qué es Azure Event Hubs?](../event-hubs/event-hubs-what-is-event-hubs.md)
   - [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)
- Vea [Download metrics and diagnostic logs from Azure Storage](../storage/storage-dotnet-how-to-use-blobs.md#download-blobs) (Descargar métricas y registros de diagnóstico de Azure Storage).

