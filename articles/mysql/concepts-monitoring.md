---
title: "Supervisión en Azure Database for MySQL | Microsoft Docs"
description: "En este artículo se describen las métricas de supervisión y alerta de Azure Database for MySQL, incluyendo las estadísticas de conexión, almacenamiento, límites y CPU."
services: mysql
author: rachel-msft
ms.author: raagyema
manager: jhubbard
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 10/24/2017
ms.openlocfilehash: 9af447d54faa8ee96e4b79beb274b437eea57626
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Supervisión en Azure Database for MySQL
La supervisión de los datos sobre los servidores le permite solucionar problemas y optimizar la carga de trabajo. Azure Database for MySQL proporciona diversas métricas que proporcionan información sobre el comportamiento de los recursos que admiten el servidor MySQL. 

## <a name="metrics"></a>Métricas
Todas las métricas de Azure tienen una frecuencia de un minuto y cada métrica proporciona 30 días de historial. 

Puede configurar alertas en las métricas. Para obtener instrucciones paso a paso, consulte [How to set up alerts](howto-alert-on-metric.md) (Configuración de alertas). 

Otras tareas incluyen la configuración de acciones automatizadas, la realización de análisis avanzados y el archivo del historial. Para obtener más información, consulte [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas están disponibles para Azure Database for MySQL:

|Métrica|Nombre de métrica para mostrar|Unidad|Descripción|
|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Porcentaje de CPU en uso.|
|compute_limit|Límite de unidad de proceso|Recuento|Número máximo de unidades de proceso de este servidor|
|compute_consumption_percent|Porcentaje de unidad de proceso|Percent|Porcentaje de unidades de proceso que se usa más allá del límite máximo del servidor.|
|memory_percent|Porcentaje de memoria|Percent|Porcentaje de memoria en uso.|
|io_consumption_percent|Porcentaje de E/S|Percent|Porcentaje de E/S en uso.|
|storage_percent|Porcentaje de almacenamiento|Percent|Porcentaje de almacenamiento que se usa más allá del límite máximo del servidor.|
|storage_used|Almacenamiento utilizado|Bytes|Cantidad de almacenamiento en uso. El almacenamiento que usa el servicio incluye los archivos de base de datos, los registros de transacciones y los registros de servidor.|
|storage_limit|Límite de almacenamiento|Bytes|Almacenamiento máximo de este servidor.|
|active_connections|Conexiones activas totales|Recuento|Número de conexiones activas al servidor.|
|connections_failed|Conexiones con errores totales|Recuento|Número de conexiones con errores al servidor.|


> [!NOTE]
> Una unidad de proceso se compone de memoria y CPU. El porcentaje de la unidad de proceso es máximo (% de memoria, % de cpu). Examine los gráficos de memoria y CPU para indicar cuál está contribuyendo a los cambios de porcentaje de la unidad de proceso. Para obtener más información, consulte la explicación de las [unidades de proceso](concepts-compute-unit-and-storage.md).

## <a name="next-steps"></a>Pasos siguientes
- Para obtener instrucciones paso a paso, consulte [How to set up alerts](howto-alert-on-metric.md) (Configuración de alertas). 
- Para obtener más información sobre cómo acceder a las métricas y exportarlas con Azure Portal, la API de REST o la CLI, consulte [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
