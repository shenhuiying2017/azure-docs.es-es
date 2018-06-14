---
title: Supervisión de contenedores en Azure Service Fabric con Log Analytics | Microsoft Docs
description: Use Log Analytics para supervisar los contenedores que se ejecutan en clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/1/2017
ms.author: dekapur
ms.openlocfilehash: 79d30a47b017379107b63b0006a35534f68c43b9
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34210783"
---
# <a name="monitor-containers-with-log-analytics"></a>Supervisión de contenedores con Log Analytics
 
En este artículo se describen los pasos necesarios para configurar el contenedor de Log Analytics de OMS que supervisa la solución para ver eventos de contenedor. Para configurar el clúster a fin de recopilar eventos de contenedor, consulte este [tutorial paso a paso](service-fabric-tutorial-monitoring-wincontainers.md).

## <a name="set-up-the-container-monitoring-solution"></a>Configuración de la solución de supervisión de contenedores

> [!NOTE]
> Log Analytics debe estar configurado en el clúster y Agente de OMS implementado en los nodos. De lo contrario, primero siga los pasos descritos en [Set-up Log Analytics](service-fabric-diagnostics-oms-setup.md) (Configuración de Log Analytics) y [Add the OMS Agent to a cluster](service-fabric-diagnostics-oms-agent.md) (Incorporación de Agente de OMS a un clúster).

1. Una vez que el clúster esté configurado con Log Analytics y Agente de OMS, implemente los contenedores. Espere a que los contenedores se implementen antes de pasar al paso siguiente.

2. En Azure Marketplace, busque *Container Monitoring Solution* y haga clic en el recurso **Container Monitoring Solution** que aparece debajo de la categoría Supervisión y administración.

    ![Adición de la solución Containers](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Cree la solución dentro de la misma área de trabajo que ya se ha creado para el clúster. Este cambio desencadena automáticamente el agente para iniciar la recopilación de datos de Docker en los contenedores. En unos 15 minutos, debería ver la solución iluminada con registros y estadísticas entrantes, como se muestra en la imagen siguiente.

    ![Panel de OMS básico](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

El agente habilita la recopilación de varios registros específicos de contenedores que se pueden consultar en OMS o se usan para los indicadores de rendimiento visualizados. Los tipos de registro que se recopilan son:

* ContainerInventory: muestra información acerca de la ubicación, nombre e imágenes del contenedor
* ContainerImageInventory: información acerca de las imágenes implementadas, lo que incluye sus identificadores o tamaños
* ContainerLog: registros de error concretos, registros de docker (stdout, etc.) y otras entradas
* ContainerServiceLog: comandos de demonio de docker que se han ejecutado
* Perf: contadores de rendimiento, entre los que se incluyen la cpu del contenedor, la memoria, el tráfico de red, la e/s de disco y métricas personalizadas de los equipos host



## <a name="next-steps"></a>Pasos siguientes
* Obtenga más información sobre la [solución de Supervisión de contenedores de Azure Log Analytics](../log-analytics/log-analytics-containers.md).
* Obtenga más información sobre la orquestación de contenedores en Service Fabric en [Service Fabric y contenedores](service-fabric-containers-overview.md)
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics
* Configure en Log Analytics reglas de [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico