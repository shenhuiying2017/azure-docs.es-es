---
title: Supervisión en Azure Database for MySQL
description: En este artículo se describen las métricas de supervisión y alerta de Azure Database for MySQL, incluidas las estadísticas de CPU, almacenamiento y conexión.
services: mysql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 03/15/2018
ms.openlocfilehash: c3cba00077fd65239382d6fdd98e73a55f926b3b
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/17/2018
---
# <a name="monitoring-in-azure-database-for-mysql"></a>Supervisión en Azure Database for MySQL
La supervisión de los datos sobre los servidores le permite solucionar problemas y optimizar la carga de trabajo. Azure Database for MySQL proporciona diversas métricas que proporcionan información sobre el comportamiento de los recursos que admiten el servidor MySQL. 

## <a name="metrics"></a>Métricas
Todas las métricas de Azure tienen una frecuencia de un minuto y cada métrica proporciona 30 días de historial. Puede configurar alertas en las métricas. Para obtener instrucciones paso a paso, consulte [How to set up alerts](howto-alert-on-metric.md) (Configuración de alertas). Otras tareas incluyen la configuración de acciones automatizadas, la realización de análisis avanzados y el archivo del historial. Para obtener más información, consulte [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

### <a name="list-of-metrics"></a>Lista de métricas
Estas métricas están disponibles para Azure Database for MySQL:

|Métrica|Nombre de métrica para mostrar|Unidad|DESCRIPCIÓN|
|---|---|---|---|---|
|cpu_percent|Porcentaje de CPU|Percent|Porcentaje de CPU en uso.|
|memory_percent|Porcentaje de memoria|Percent|Porcentaje de memoria en uso.|
|io_consumption_percent|Porcentaje de E/S|Percent|Porcentaje de E/S en uso.|
|storage_percent|Porcentaje de almacenamiento|Percent|Porcentaje de almacenamiento que se usa más allá del límite máximo del servidor.|
|storage_used|Almacenamiento utilizado|Bytes|Cantidad de almacenamiento en uso. El almacenamiento que usa el servicio incluye los archivos de base de datos, los registros de transacciones y los registros de servidor.|
|storage_limit|Límite de almacenamiento|Bytes|Almacenamiento máximo de este servidor.|
|active_connections|Conexiones activas totales|Recuento|Número de conexiones activas al servidor.|
|connections_failed|Conexiones con errores totales|Recuento|Número de conexiones con errores al servidor.|


## <a name="next-steps"></a>Pasos siguientes
- Consulte [How to set up alerts](howto-alert-on-metric.md) (Configuración de alertas) para obtener instrucciones sobre cómo crear una alerta en una métrica.
- Para obtener más información sobre cómo acceder a las métricas y exportarlas con Azure Portal, la API de REST o la CLI, consulte [Información general sobre las métricas en Microsoft Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).
