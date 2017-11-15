---
title: "Supervisión de contenedores en Azure Service Fabric con Log Analytics de OMS | Microsoft Docs"
description: "Use Log Analytics de OMS para supervisar los contenedores que se ejecutan en clústeres con Azure Service Fabric."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 17121041520160d0d76832bbdbe74ad6a649fdd8
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2017
---
# <a name="monitor-containers-with-oms-log-analytics"></a>Supervisión de contenedores con Log Analytics de OMS
 
En este artículo se tratan los pasos necesarios para configurar la supervisión del contenedor para el clúster. Para más información, vea [Supervisión de contenedores en Service Fabric](service-fabric-diagnostics-event-analysis-oms.md#monitoring-containers). Para consultar un tutorial detallado sobre esto, vea [Supervisión de contenedores de Windows en Service Fabric mediante OMS](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configuración de la solución de supervisión de contenedores

> [!NOTE]
> Debe tener Log Analytics de OMS configurado en el clúster y el Agente de OMS implementado en los nodos. De lo contrario, primero siga los pasos descritos en [Set-up OMS Log Analytics](service-fabric-diagnostics-oms-setup.md) (Configuración de Log Analytics de OMS) y [Add the OMS Agent to a cluster](service-fabric-diagnostics-oms-agent.md) (Incorporación del Agente de OMS a un clúster).

1. Una vez que el clúster está configurado con Log Analytics de OMS y el Agente de OMS, implemente los contenedores. Espere a que los contenedores se implementen antes de pasar al paso siguiente.

2. En Azure Marketplace, busque *Container Monitoring Solution* y haga clic en el recurso **Container Monitoring Solution** que aparece debajo de la categoría Supervisión y administración.

    ![Adición de la solución Containers](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Cree la solución dentro de la misma área de trabajo que ya se ha creado para el clúster. Este cambio desencadena automáticamente el agente para iniciar la recopilación de datos de Docker en los contenedores. En unos 15 minutos, debería ver la solución iluminada con registros y estadísticas entrantes.

## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la orquestación de contenedores en Service Fabric en [Service Fabric y contenedores](service-fabric-containers-overview.md)
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics
* Configure OMS para establecer reglas de [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y diagnóstico