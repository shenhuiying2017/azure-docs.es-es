---
title: Análisis de eventos de Azure Service Fabric con Log Analytics | Microsoft Docs
description: Obtenga información sobre cómo visualizar y analizar eventos con Log Analytics para la supervisión y el diagnóstico de clústeres de Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: srrengar
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/16/2018
ms.author: dekapur; srrengar
ms.openlocfilehash: da78f88f0c79c0ad853dd644ef278f8402824760
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/18/2018
---
# <a name="event-analysis-and-visualization-with-log-analytics"></a>Análisis y visualización de eventos con Log Analytics

Log Analytics, conocido también como OMS (Operations Management Suite), es una colección de servicios de administración que simplifican la supervisión y el diagnóstico de aplicaciones y servicios hospedados en la nube. En este artículo se describe cómo ejecutar consultas en Log Analytics para obtener información de lo que está sucediendo en el clúster y solucionar problemas. Se tratan las siguientes preguntas habituales:

* ¿Cómo se solucionan los eventos de mantenimiento?
* ¿Cómo se puede saber si un nodo deja de funcionar?
* ¿Cómo se puede saber si los servicios de la aplicación se han iniciado o detenido?

## <a name="log-analytics-workspace"></a>Área de trabajo de Log Analytics

Log Analytics recopila datos de recursos administrados, incluidos un agente o una tabla de almacenamiento de Azure, y los mantiene en un repositorio central. Estos datos pueden utilizarse posteriormente para análisis, alertas, visualizaciones y tareas ulteriores de exportación. Log Analytics admite eventos, datos de rendimiento u otros datos personalizados. Consulte los [pasos para configurar la extensión de diagnósticos para agregar eventos](service-fabric-diagnostics-event-aggregation-wad.md) y los [pasos para crear un área de trabajo de Log Analytics para leer de los eventos de almacenamiento](service-fabric-diagnostics-oms-setup.md) para asegurarse de que los datos fluyen en Log Analytics.

Después de que Log Analytics recibe los datos, Azure dispone de varias *soluciones de administración*, que son soluciones preempaquetadas para supervisar los datos entrantes, personalizados para varios escenarios. Puede tratarse de una solución de *Service Fabric Analytics* y una solución de *Containers*, que son las dos opciones más importantes para diagnosticar y supervisar el uso de los clústeres de Service Fabric. En este artículo se describe cómo utilizar la solución de Service Fabric Analytics, que se crea con el área de trabajo.

## <a name="access-the-service-fabric-analytics-solution"></a>Acceso a la solución de Service Fabric Analytics

1. Vaya al grupo de recursos donde creó la solución Service Fabric Analytics. Seleccione el recurso **ServiceFabric\<nameOfOMSWorkspace\>** y vaya a su página de información general.

2. En la página de información general, haga clic en el vínculo junto a la parte superior para ir al portal de OMS.

    ![Vínculo al portal de OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-portal-link.png)

3. Ahora está en el portal de OMS y podrá ver las soluciones que haya habilitado. Haga clic en el grafo titulado Service Fabric (primera imagen abajo) para ir a la solución de Service Fabric (segunda imagen abajo).

    ![Solución SF en OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-workspace-all-solutions.png)

    ![Solución SF en OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new.png)

La imagen anterior es la página principal de la solución de Service Fabric Analytics. Se trata de una vista de instantánea de lo que sucede en el clúster. Si habilitó el diagnóstico durante la creación del clúster, puede ver eventos de 

* [Canal operativo](service-fabric-diagnostics-event-generation-operational.md): operaciones de nivel superior que realiza la plataforma Service Fabric (colección de servicios del sistema).
* [Eventos del modelo de programación de Reliable Actors](service-fabric-reliable-actors-diagnostics.md)
* [Eventos del modelo de programación de Reliable Services](service-fabric-reliable-services-diagnostics.md)

>[!NOTE]
>Además del canal operativo, se pueden recopilar eventos del sistema más detallados mediante la [actualización de la configuración de la extensión de diagnósticos](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations).

### <a name="view-operational-events-including-actions-on-nodes"></a>Visualización de eventos operativos, como acciones en nodos

1. En la página de Service Fabric Analytics del portal de OMS, haga clic en el grafo para el canal operativo.

    ![Canal operativo de solución de SF de OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-new-operational.png)

2. Haga clic en Tabla para ver los eventos en una lista. Una vez aquí, verá todos los eventos del sistema que se han recopilado. Como referencia, proceden de WADServiceFabricSystemEventsTable en la cuenta de Azure Storage y, de manera similar, los eventos de Reliable Services y Reliable Actors que ve a continuación provienen de esas tablas respectivas.
    
    ![Canal operativo de consulta de OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-operational-channel.png)

También puede hacer clic en la lupa de la izquierda y usar el lenguaje de consulta Kusto para encontrar lo está buscando. Por ejemplo, para buscar todos los eventos relacionados con las acciones realizadas en los nodos por parte del clúster, puede usar la consulta siguiente. Los identificadores de evento que se usan a continuación se encuentran en la [referencia de eventos del canal operativo](service-fabric-diagnostics-event-generation-operational.md).

```kusto
ServiceFabricOperationalEvent
| where EventId < 29627 and EventId > 29619 
```
Puede consultar en muchos más campos, como los nodos específicos (Computer), el servicio del sistema (TaskName), etc.

### <a name="view-service-fabric-reliable-service-and-actor-events"></a>Visualización de eventos de Reliable Services y Reliable Actors

1. En la página de Service Fabric Analytics del portal de OMS, haga clic en el grafo de Reliable Services.

    ![Reliable Services de solución de SF de OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics-reliable-services.png)

2. Haga clic en Tabla para ver los eventos en una lista. Aquí puede ver eventos de Reliable Services. Puede ver eventos diferentes para cuando el servicio runasync se inicia y se completa, lo que ocurre habitualmente en las implementaciones y las actualizaciones. 

    ![Reliable Services de consulta de OMS](media/service-fabric-diagnostics-event-analysis-oms/oms-query-reliable-services.png)

Los eventos de Reliable Actors pueden verse de forma similar. Para configurar más eventos detallados para Reliable Actors, necesita cambiar `scheduledTransferKeywordFilter` en la configuración de la extensión de diagnóstico (se muestra a continuación). Los detalles de los valores de estos están en la [referencia de eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md#keywords).

```json
"EtwEventSourceProviderConfiguration": [
                {
                    "provider": "Microsoft-ServiceFabric-Actors",
                    "scheduledTransferKeywordFilter": "1",
                    "scheduledTransferPeriod": "PT5M",
                    "DefaultEvents": {
                    "eventDestination": "ServiceFabricReliableActorEventTable"
                    }
                },
```

El lenguaje de consulta Kusto es eficaz. Otra consulta valiosa que puede ejecutar consiste en averiguar qué nodos están generando la mayoría de los eventos. La consulta de la captura de pantalla siguiente muestra un evento de Reliable Services agregado con el servicio y el nodo específicos.

![Eventos de consultas de OMS por nodo](media/service-fabric-diagnostics-event-analysis-oms/oms-query-events-per-node.png)

## <a name="next-steps"></a>Pasos siguientes

* Para habilitar la supervisión de la infraestructura; es decir, los contadores de rendimiento, vea cómo [agregar el agente de OMS](service-fabric-diagnostics-oms-agent.md). El agente recopila los contadores de rendimiento y los agrega al área de trabajo existente.
* Si se trata de clústeres locales, OMS ofrece una puerta de enlace (proxy de reenvío HTTP) que puede usarse para enviar datos a OMS. Obtenga más información en [Conexión de equipos sin acceso a Internet a OMS mediante OMS Gateway](../log-analytics/log-analytics-oms-gateway.md).
* Configure OMS para configurar [alertas automáticas](../log-analytics/log-analytics-alerts.md) que ayuden en la detección y el diagnóstico.
* Familiarícese con las funciones de [búsqueda de registros y consulta](../log-analytics/log-analytics-log-searches.md) que se ofrecen como parte de Log Analytics
* Para obtener información general más detallada sobre Log Analytics y lo que ofrece, vea [¿Qué es Azure Log Analytics?](../operations-management-suite/operations-management-suite-overview.md)
