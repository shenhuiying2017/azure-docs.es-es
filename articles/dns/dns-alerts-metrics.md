---
title: Métricas y alertas de Azure DNS | Microsoft Docs
description: Obtenga más información sobre las métricas y alertas de Azure DNS.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: kumud
ms.openlocfilehash: 54c4df446ee5c1bf8d29dd6c33b304f39ce8f1b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31591626"
---
# <a name="azure-dns-metrics-and-alerts"></a>Métricas y alertas de Azure DNS
Azure DNS es un servicio de hospedaje para dominios DNS que ofrece resolución de nombres mediante la infraestructura de Microsoft Azure. En este artículo se describen las métricas y alertas del servicio Azure DNS.

## <a name="azure-dns-metrics"></a>Métricas de Azure DNS

Azure DNS ofrece métricas para que los clientes las habiliten con el fin de supervisar aspectos concretos de las zonas DNS hospedadas en el servicio. Además, con las métricas de Azure DNS, puede configurar y recibir alertas en función de las condiciones de interés. Las métricas se ofrecen a través del [servicio Azure Monitor](../monitoring-and-diagnostics/index.yml). Azure DNS ofrece las siguientes métricas de las zonas DNS a través de Azure Monitor:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

También puede ver la [definición de estas métricas](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) en la página de documentación de Azure Monitor.
>[!NOTE]
> En este momento, estas métricas solo están disponibles para las zonas DNS públicas hospedadas en Azure DNS. Si tiene zonas privadas hospedadas en Azure DNS, las métricas no proporcionarán datos sobre esas zonas. Además, la característica de métricas y alertas solo se admite en la nube pública de Azure. Más adelante se hablará de la compatibilidad con nubes soberanas. 

El nivel pormenorizado de dimensión de estas métricas es la zona DNS.

### <a name="query-volume"></a>Volumen de consultas

La métrica *Query Volume* (Volumen de consultas) de Azure DNS muestra el volumen de consultas DNS (tráfico de consultas) que se recibe en Azure DNS para la zona DNS. La unidad de medida es Recuento y la agregación es el total de todas las consultas que se reciben a lo largo de un período de tiempo. Para ver esta métrica, seleccione la experiencia del Explorador de métricas (versión preliminar) en la pestaña Supervisar de Azure Portal. Seleccione la zona DNS de la lista desplegable Recursos, elija la métrica Query Volume (Volumen de consultas) y luego Suma como Agregación. La siguiente captura de pantalla muestra un ejemplo.  Para obtener más información sobre la experiencia y los gráficos del Explorador de métricas, vea [Explorador de métricas de Azure Monitor](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Volumen de consultas](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Figura: Métrica de volumen de consultas de Azure DNS*

### <a name="record-set-count"></a>Recuento de conjunto de registros
La métrica *Record Set Count* (Número de conjuntos de registros) muestra el número de conjuntos de registros de Azure DNS para la zona DNS. Se cuentan todos los conjuntos de registros definidos en la zona. La unidad de medida es Recuento y la agregación es el número máximo de conjuntos de registros. Para ver esta métrica, seleccione la experiencia del **Explorador de métricas (versión preliminar)** en la pestaña **Supervisar** de Azure Portal. Seleccione la zona DNS de la lista desplegable **Recursos**, elija la métrica **Record Set Count** (Número de conjuntos de registros) y luego **Máx.** como **Agregación**. Para obtener más información sobre la experiencia y los gráficos del Explorador de métricas, vea [Explorador de métricas de Azure Monitor](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Recuento de conjunto de registros](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Figura: Métricas de número de conjuntos de registros de Azure DNS* (Número de conjuntos de registros)


### <a name="record-set-capacity-utilization"></a>Uso de capacidad de conjunto de registros
La métrica *Record Set Capacity Utilization* (Uso de capacidad de conjunto de registros) de Azure DNS muestra el porcentaje de uso de la capacidad de conjuntos de registros para una zona DNS. Todas las zonas DNS de Azure DNS están sujetas a un límite de conjuntos de registros que define el número máximo de conjuntos de registros que se permiten para la zona (vea [Límites de DNS](dns-zones-records.md#limits)). Por lo tanto, esta métrica muestra lo cerca que se está de alcanzar el límite de conjuntos de registros. Por ejemplo, si tiene 500 conjuntos de registros configurados para la zona DNS y la zona tiene el límite de conjuntos de registros predeterminado de 5000, la métrica RecordSetCapacityUtilization mostrará el valor de 10 % (que se obtiene al dividir 500 por 5000). La unidad de medida es **Porcentaje** y el tipo de **Agregación** es **Máximo**. Para ver esta métrica, seleccione la experiencia del Explorador de métricas (versión preliminar) en la pestaña Supervisar de Azure Portal. Seleccione la zona DNS de la lista desplegable Recursos, elija la métrica Record Set Capacity Utilization (Uso de capacidad de conjunto de registros) y luego Máx. como Agregación. La siguiente captura de pantalla muestra un ejemplo. Para obtener más información sobre la experiencia y los gráficos del Explorador de métricas, vea [Explorador de métricas de Azure Monitor](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Recuento de conjunto de registros](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Figura: Métricas de uso de capacidad de conjunto de registros de Azure DNS*

## <a name="alerts-in-azure-dns"></a>Alertas de Azure DNS
Azure Monitor ofrece la capacidad de alertar con respecto a los valores de métricas disponibles. Las métricas DNS están disponibles en la nueva experiencia de Configuración de alertas. Tal y como se describe detalladamente en la [documentación de alertas de Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), puede seleccionar Zona DNS como recurso, elegir el tipo de señal Métrica y configurar la lógica de alertas y otros parámetros como **Período** y **Frecuencia**. Puede definir más específicamente un [Grupo de acciones](../monitoring-and-diagnostics/monitoring-action-groups.md) para que, si se cumple la condición de alerta, esta se entregue por medio de las acciones elegidas. Para obtener más información sobre cómo configurar alertas para métricas de Azure Monitor, vea [Creación, visualización y administración de alertas mediante Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Pasos siguientes
- Obtenga más información sobre [Azure DNS](dns-overview.md).
