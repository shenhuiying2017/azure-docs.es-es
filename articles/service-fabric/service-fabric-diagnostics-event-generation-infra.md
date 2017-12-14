---
title: "Supervisión del nivel de plataforma de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información sobre los eventos y los registros de nivel de plataforma que se usan para supervisar y diagnosticar los clústeres de Azure Service Fabric."
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
ms.date: 11/20/2017
ms.author: dekapur
ms.openlocfilehash: 8452b5ae733b21254b0beecaec44a968897ae491
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="platform-level-event-and-log-generation"></a>Generación de eventos y registros de nivel de plataforma

## <a name="monitoring-the-cluster"></a>Supervisar el clúster

Es importante supervisar en el nivel de infraestructura para determinar si el hardware y el clúster se comportan según lo previsto. Aunque Service Fabric puede mantener las aplicaciones en ejecución durante un error de hardware, es necesario diagnosticar si un error se produce en una aplicación o en la infraestructura subyacente. También debe supervisar los clústeres para planear mejor la capacidad, lo que ayuda a decidir si es necesario agregar o quitar hardware.

Service Fabric proporciona los siguientes canales de registro integrados:
* Canal operativo: operaciones de alto nivel que realiza Service Fabric y el clúster, incluyendo eventos para un nodo próximo, una nueva aplicación que se implementa o la reversión de una actualización, etc.
* Canal operativo: detallado: informes de estado y decisiones para el equilibrio de carga
* Canal de datos y mensajería: registros y eventos críticos generados en la ruta de acceso a mensajería (actualmente solo ReverseProxy) y a datos (modelos de servicios confiables).
* Canal de datos y mensajería: detallado: canal detallado que contiene todos los registros no críticos de datos y mensajería del clúster (este canal tiene un volumen de eventos muy alto)   

Además, se proporcionan dos canales EventSource estructurados, así como registros que se recopilan para que los use el servicio de soporte técnico.
* [Eventos de Reliable Services](service-fabric-reliable-services-diagnostics.md): eventos específicos del modelo de programación
* [Eventos de Reliable Actors](service-fabric-reliable-actors-diagnostics.md): contadores de rendimiento y eventos específicos del modelo de programación
* Registros de soporte técnico: registros del sistema que genera Service Fabric únicamente para que los usemos al proporcionar soporte técnico

Estos distintos canales cubren la mayor parte de los registros de nivel de plataforma que se recomiendan. Para mejorar el registro de nivel de plataforma, considere la posibilidad de dedicar tiempo a comprender mejor el modelo de mantenimiento y agregar informes de mantenimiento personalizados, e agregar **contadores de rendimiento** personalizados para comprender en tiempo real de qué manera afectan al clúster los servicios y las aplicaciones.

### <a name="azure-service-fabric-health-and-load-reporting"></a>Informes de carga y mantenimiento de Azure Service Fabric

Service Fabric tiene su propio modelo de mantenimiento, que se describe en detalle en estos artículos:
- [Introducción a la supervisión del mantenimiento de Service Fabric](service-fabric-health-introduction.md)
- [Notificación y comprobación del estado del servicio](service-fabric-diagnostics-how-to-report-and-check-service-health.md)
- [Incorporación de informes de mantenimiento de Service Fabric personalizados](service-fabric-report-health.md)
- [Vista de los informes de estado de Service Fabric](service-fabric-view-entities-aggregated-health.md)

La supervisión del mantenimiento es fundamental para diversos aspectos operativos de un servicio. La supervisión del mantenimiento es especialmente importante cuando Service Fabric lleva a cabo una actualización de la aplicación con nombre. Cuando estén actualizados y disponibles para los clientes los dominios del servicio, deben pasar las comprobaciones de mantenimiento para que la implementación pase al siguiente dominio de actualización. Si no se puede alcanzar un mantenimiento correcto, la implementación se revierte y deja la aplicación en un estado correcto conocido. Aunque algunos clientes resultaran afectados antes de que los servicios se pudieran revertir, la mayoría no experimenta ningún problema. Además, la resolución se produce de forma relativamente rápida y sin necesidad de esperar a la acción de un operador humano. Cuantas más comprobaciones de mantenimiento se integren en el código, más resistente será el servicio a los problemas de implementación.

Otro aspecto del estado del servicio es la notificación de métricas desde el servicio. Las métricas son importantes en Service Fabric, porque se usan para equilibrar el uso de los recursos. Además, son un indicador del estado del sistema. Por ejemplo, supongamos que tiene una aplicación con muchos servicios y que cada instancia informa sobre una métrica de solicitudes por segundo (RPS). Si uno de los servicios usa más recursos que otro, Service Fabric mueve instancias de servicio en el clúster para intentar mantener un uso uniforme de los recursos. Para una explicación más detallada sobre cómo funciona el uso de los recursos, consulte [Administración de consumo y carga de recursos en Service Fabric con métricas](service-fabric-cluster-resource-manager-metrics.md).

Las métricas también pueden ayudarle con una visión general de rendimiento del servicio. Con el tiempo, las métricas se pueden usar para comprobar que el servicio funciona con los parámetros previstos. Por ejemplo, si las tendencias muestran que a las 9 de la mañana del lunes, el promedio de solicitudes por segundo es de 1000, podría configurar un informe de mantenimiento que emita una alerta si descienden de 500 o aumentan por encima de las 1500. Todo puede estar funcionando correctamente, pero quizá valga la pena echar un vistazo para asegurarse de que los clientes disfrutan de una gran experiencia. El servicio puede definir un conjunto de métricas que se pueden notificar para controlar el mantenimiento, pero que no afecten al equilibrio de los recursos del clúster. Para ello, establezca la ponderación métrica en cero. Se recomienda iniciar todas las métricas con una ponderación de cero y no aumentarla hasta que se sepa con certeza la repercusión que tiene en el equilibrio de los recursos para el clúster.

> [!TIP]
> No use demasiadas métricas ponderadas. Puede ser difícil de entender por qué se mueven instancias de servicio para el equilibrio. Algunas pueden moverse muchísimo.

Cualquier información que pueda indicar el estado y el rendimiento de la aplicación es apta para los informes de mantenimiento y métricas. Un contador de rendimiento de la CPU puede indicar cómo se está usando el nodo, pero no indica si un servicio determinado está en buen estado, ya que pueden estar ejecutándose varios servicios en un solo nodo. Pero las métricas como RPS, los elementos procesados y la latencia de solicitud, pueden indicar el estado de un servicio específico.

Para notificar el estado, use código similar al siguiente:

  ```csharp
    if (!result.HasValue)
    {
        HealthInformation healthInformation = new HealthInformation("ServiceCode", "StateDictionary", HealthState.Error);
        this.Partition.ReportInstanceHealth(healthInformation);
    }
  ```

Para notificar una métrica, use código similar al siguiente:

  ```csharp
    this.Partition.ReportLoad(new List<LoadMetric> { new LoadMetric("MemoryInMb", 1234), new LoadMetric("metric1", 42) });
  ```

### <a name="service-fabric-support-logs"></a>Registros de soporte técnico de Service Fabric

Si tiene que ponerse en contacto con el soporte técnico de Microsoft para que le ayuden con el clúster de Azure Service Fabric, casi siempre necesitará los registros de soporte técnico. Si el clúster se hospeda en Azure, estos registros de soporte técnico se configuran y recopilan automáticamente en el proceso de creación del clúster. Los registros se almacenan en una cuenta de almacenamiento específica del grupo de recursos del clúster. La cuenta de almacenamiento no tiene nombre fijo, pero en ella verá contenedores de blobs y tablas cuyos nombres empiezan por *fabric*. Para obtener información acerca de cómo configurar las colecciones de registros para un clúster independiente, consulte el artículo sobre la [creación y la administración de un clúster independiente de Azure Service Fabric](service-fabric-cluster-creation-for-windows-server.md) y [Opciones de configuración clústeres de Windows independientes](service-fabric-cluster-manifest.md). Para instancias independientes de Service Fabric, los registros se deben enviar a un archivo local compartido. Es **necesario** que tenga estos registros para el soporte técnico, pero no se prevé que los utilice nadie que no pertenezca al equipo de soporte técnico al cliente de Microsoft.

## <a name="enabling-diagnostics-for-a-cluster"></a>Habilitar Diagnostics para un clúster

Para sacar provecho de estos registros, se recomienda encarecidamente habilitar Diagnostics durante la creación del clúster. Al activar los diagnósticos, cuando se implementa el clúster, Microsoft Azure Diagnostics puede reconocer los canales operativo, de Reliable Services y de Reliable Actors y almacenar los datos como se explica en [Agregación de eventos con Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md).

Como ya se ha visto, también hay un campo opcional para agregar una clave de instrumentación de Application Insights (AI). Si decide usar AI para los análisis de eventos (obtenga más información en [Análisis de eventos con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md)), incluya la clave de instrumentación de recursos de AppInsights (GUID).


Si va a implementar contenedores en el clúster, habilite WAD para recopilar estadísticas de Docker. Para ello, agregue lo siguiente a "WadCfg > DiagnosticMonitorConfiguration":

```json
"DockerSources": {
    "Stats": {
        "enabled": true,
        "sampleRate": "PT1M"
    }
},

```

## <a name="measuring-performance"></a>Medir el rendimiento

La medición del rendimiento del clúster le ayudará a entender cómo controla la carga y toma decisiones sobre el escalado del clúster (obtenga más información sobre el escalado de un clúster [en Azure](service-fabric-cluster-scale-up-down.md) o [localmente](service-fabric-cluster-windows-server-add-remove-nodes.md)). Al analizar los registros en el futuro, los datos de rendimiento también son útiles cuando se comparan con las acciones que usted o las aplicaciones han llevado a cabo. 

Para obtener una lista de contadores de rendimiento para recopilar datos cuando se usa Service Fabric, vea [Performance Counters in Service Fabric](service-fabric-diagnostics-event-generation-perf.md) (Contadores de rendimiento de Service Fabric).

A continuación se indican dos formas habituales de configurar la recopilación de datos de rendimiento del clúster:

* Usar un agente: se trata de la mejor manera de recopilar datos de rendimiento de un equipo, ya que los agentes suelen incluir una lista de las métricas de rendimiento que se pueden recopilar, y el proceso de elegir o cambiar las métricas que se van a recopilar es relativamente sencillo. Lea los artículos dedicados a [cómo configurar OMS para Service Fabric](service-fabric-diagnostics-event-analysis-oms.md) y [cómo configurar el agente de OMS de Windows](../log-analytics/log-analytics-windows-agent.md) para obtener más información sobre el agente de OMS, que es un agente de supervisión que puede recopilar datos de rendimiento de las máquinas virtuales del clúster y los contenedores implementados.

* Configurar los diagnósticos de modo que escriban los contadores de rendimiento en una tabla: en el caso de los clústeres de Azure esto significa cambiar la configuración de Azure Diagnostics, de modo que recopile los contadores de rendimiento correspondientes a las máquinas virtuales del clúster y pueda reunir estadísticas de Docker si va a implementar contenedores. Obtenga más información sobre cómo configurar [contadores de rendimiento de WAD](service-fabric-diagnostics-event-aggregation-wad.md) en Service Fabric para configurar una colección de contadores de rendimiento.

## <a name="next-steps"></a>Pasos siguientes

Debe agregar primero los registros y los eventos para que se puedan enviar a una plataforma de análisis. Obtenga información sobre [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) y [WAD](service-fabric-diagnostics-event-aggregation-wad.md) para entender mejor algunas de las opciones recomendadas.
