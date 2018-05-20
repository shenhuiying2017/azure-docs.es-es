---
title: Información general sobre supervisión y diagnóstico de Azure Service Fabric | Microsoft Docs
description: Obtenga información sobre la supervisión y el diagnóstico para los clústeres, las aplicaciones y los servicios de Azure Service Fabric.
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
ms.date: 04/25/2018
ms.author: dekapur;srrengar
ms.openlocfilehash: dd2446fda204f4026ac8080c658ca1aa9419f1bd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Supervisión y diagnóstico para Azure Service Fabric

En este artículo se proporciona información general acerca de la supervisión y del diagnóstico de Azure Service Fabric. La supervisión y el diagnóstico son fundamentales para el desarrollo, las pruebas y la implementación de flujos de trabajo en cualquier entorno de la nube. La supervisión le permite realizar un seguimiento del uso de las aplicaciones, el uso de los recursos y el estado general del clúster. Puede utilizar esta información para diagnosticar y corregir cualquier problema, así como para prevenir la aparición de problemas en el futuro. 

## <a name="application-monitoring"></a>Supervisión de aplicaciones
La supervisión de aplicaciones realiza un seguimiento del uso de las características y componentes de una aplicación. Le recomendamos supervisar las aplicaciones para asegurarse de que se detectan los problemas que afectan a los usuarios. La supervisión de las aplicaciones puede ser útil en los siguientes escenarios:
* Determinar la carga de aplicaciones y el tráfico de usuarios (¿necesita escalar los servicios para satisfacer las demandas del usuario o abordar un potencial cuello de botella en una aplicación?)
* Identificar problemas en la comunicación de los servicios y el acceso remoto en el clúster
* Averiguar lo que hacen los usuarios con la aplicación (la recopilación de datos de telemetría en sus aplicaciones puede ayudar a orientar el futuro desarrollo de las características y a obtener mejores diagnósticos de los errores de la aplicación)
* Supervisar lo que sucede en los contenedores en ejecución

Service Fabric admite muchas opciones para instrumentar el código de aplicación con los seguimientos y la telemetría apropiados. Se recomienda usar Application Insights (AI). La integración de AI con Service Fabric incluye herramientas para Visual Studio y Azure Portal, así como métricas específicas de Service Fabric, lo que proporciona una completa experiencia de registro de forma estándar. Aunque muchos registros se crean y recopilan automáticamente con AI, es aconsejable agregar un mayor registro personalizado a las aplicaciones para mejorar el diagnóstico. Obtenga más información acerca de cómo empezar a usar Application Insights con Service Fabric en [Análisis y visualización de eventos con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).

## <a name="platform-cluster-monitoring"></a>Supervisión de plataforma (clúster)
La supervisión del clúster de Service Fabric es fundamental para garantizar que la plataforma y todas las cargas de trabajo se ejecutan según lo previsto. Uno de los objetivos de Service Fabric es que las aplicaciones sean resistentes a los errores de hardware. Esto se logra gracias a la capacidad que tienen los servicios del sistema de la plataforma de detectar problemas de infraestructura y conmutar por error las cargas de trabajo rápidamente a otros nodos del clúster. Pero en este caso particular, ¿qué ocurre si los propios servicios del sistema tienen problemas? ¿O, si al intentar mover una carga de trabajo, se infringen las reglas de ubicación de los servicios? La supervisión del clúster le permite mantenerse informado sobre la actividad que está teniendo lugar en el clúster, lo cual resulta de ayuda al diagnosticar problemas y corregirlos de forma eficaz. Estos son algunos de los aspectos fundamentales a los que debe prestar atención:
* ¿Se comporta Service Fabric como esperaba, en cuanto a la ubicación de las aplicaciones y el equilibrio del trabajo en el clúster? 
* ¿Se confirman y ejecutan según lo previsto las acciones de usuario realizadas en el clúster? Esto es especialmente importante al escalar un clúster.
* ¿Gestiona Service Fabric los datos y la comunicación interna entre servicios del clúster correctamente?

Service Fabric proporciona un conjunto completo de eventos listos para usar. Estos [eventos de Service Fabric](service-fabric-diagnostics-events.md) están disponibles para el acceso a través de las API de EventStore o el canal operativo (canal de eventos expuesto por la plataforma). 
* EventStore: (disponible en Windows versión 6.2 y posteriores, así como en Linux en curso en la fecha de la última actualización de este artículo) expone estos eventos mediante un conjunto de API (accesibles a través de puntos de conexión de REST o de la biblioteca de cliente). Obtenga más información sobre EventStore en [Información general de EventStore](service-fabric-diagnostics-eventstore.md).
* Canales de eventos de Service Fabric: en Windows, los eventos de Service Fabric están disponibles en un solo proveedor ETW con un conjunto de `logLevelKeywordFilters` relevantes que se usan para elegir entre el canal operativo y el canal de datos y mensajería (es la manera en que se separan los eventos de Service Fabric salientes que se van a filtrar, si es necesario). En Linux, los eventos de Service Fabric proceden de LTTng y se colocan en una tabla de almacenamiento, desde la cual pueden filtrarse según sea necesario. Estos canales contienen eventos estructurados protegidos que pueden utilizarse para comprender mejor el estado del clúster. Los diagnósticos se habilitan de forma predeterminada en el momento de la creación del clúster, donde se crea una tabla de Azure Storage donde se envían los eventos de estos canales para que pueda consultarlos en el futuro. 

Se recomienda usar EventStore para el análisis rápido y la obtención de una instantánea de cómo funciona el clúster y de si todo ocurre tal como se espera. Para recopilar los registros y eventos que genera el clúster, se suele recomendar el uso de la [extensión de Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md). Esto se integra bien con Service Fabric Analytics, una solución específica de Service Fabric de Log Analytics de OMS, que proporciona un panel personalizado para la supervisión de clústeres de Service Fabric y permite consultar los eventos del clúster y configurar alertas. Para obtener más información al respecto, consulte [Análisis de eventos con OMS](service-fabric-diagnostics-event-analysis-oms.md). 

 Para obtener más información sobre la supervisión de clústeres, consulte [Generación de eventos y registros de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md).


 ![Solución SF en OMS](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-solution.png)

## <a name="performance-monitoring"></a>Supervisión del rendimiento
La supervisión de la infraestructura subyacente es una parte fundamental del conocimiento del estado del clúster y la utilización de los recursos. La medición del rendimiento del sistema depende de varios factores, cada uno de los cuales suele medirse mediante unos indicadores clave de rendimiento (KPI). Los KPI pertinentes de Service Fabric pueden asignarse a las métricas que pueden recopilarse de los nodos del clúster, como los contadores de rendimiento.
Estos KPI pueden ayudarle con lo siguiente:
* Conocer la utilización y carga de recursos (con el fin de escalar el clúster u optimizar los procesos de servicio).
* Predecir problemas de infraestructura [muchos problemas están precedidos por cambios bruscos (caídas) en el rendimiento, por lo que puede utilizar KPI, como las E/S de la red y la utilización de la CPU, para predecir y diagnosticar problemas infraestructurales].

Encontrará una lista de contadores de rendimiento que deberían recopilarse en el nivel de infraestructura en el artículo sobre [Métricas de rendimiento](service-fabric-diagnostics-event-generation-perf.md). 

Service Fabric proporciona un conjunto de contadores de rendimiento para los modelos de programación de Reliable Services y Actors. Si usa alguno de estos modelos, estos contadores de rendimiento pueden proporcionar KPI que ayudan a garantizar que los actores se aceleran y desaceleran correctamente, o que las solicitudes de servicio de confianza se están gestionando lo suficientemente rápido. Para más información al respecto, consulte [Supervisión de comunicación remota de Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) y [Supervisión de rendimiento para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Además, Application Insights también cuenta con un conjunto de métricas de rendimiento que recopilará, si se configura con la aplicación.

Utilice el [agente de OMS](service-fabric-diagnostics-oms-agent.md) para recopilar los contadores de rendimiento correspondientes y consulte estos KPI en Log Analytics de OMS.

![Gráfico de información general de diagnóstico](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="health-monitoring"></a>Supervisión del estado
La plataforma Service Fabric incluye un modelo de estado, el cual proporciona informes de estado extensibles para el estado de las entidades de un clúster. Cada nodo, aplicación, servicio, participación, réplica o instancia tiene un estado de mantenimiento que se actualiza continuamente. El estado de mantenimiento puede ser "Correcto", "Advertencia" o "Error". El estado de mantenimiento se cambia a través de los informes de mantenimiento que se emiten para cada entidad, en función de los problemas del clúster. El estado de mantenimiento de las entidades puede comprobarse en cualquier momento en Service Fabric Explorer (SFX) tal y como se muestra a continuación, o puede consultarse a través de Health API de las plataformas. También puede personalizar los informes de estado y modificar el estado de mantenimiento de una entidad agregando sus propios informes de mantenimiento o mediante Health API. Encontrará más detalles sobre el modelo de estado en [Introducción a la supervisión del estado de Service Fabric](service-fabric-health-introduction.md).

![Panel de estado de SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Además de ver los informes de estado más recientes de SFX, cada informe también está disponible como un evento. Los eventos de mantenimiento pueden recopilarse a través del canal de operaciones (consulte [Agregación de eventos con Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#log-collection-configurations)) y almacenarse en Log Analytics para enviar alertas y realizar consultas en el futuro. Esto ayuda a detectar problemas que puedan afectar a la disponibilidad de las aplicaciones, por lo que se recomienda configurar alertas para los escenarios de error correspondientes (alertas personalizadas a través de Log Analytics).

## <a name="other-logging-solutions"></a>Otras soluciones de registro

Aunque las dos soluciones que se han recomendado, [OMS](service-fabric-diagnostics-event-analysis-oms.md) y [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md), tienen incorporada la integración con Service Fabric, muchos eventos se escriben a través de proveedores de ETW y son extensibles a otras soluciones de registro. También debe consultar [Elastic Stack](https://www.elastic.co/products) (especialmente si planea ejecutar un clúster en un entorno sin conexión), [Splunk](https://www.splunk.com/), [Dynatrace](https://www.dynatrace.com/) o cualquier otra plataforma que desee. 

Los puntos clave para cualquier plataforma que elija deben incluir su grado de comodidad con la interfaz de usuario y las opciones de consultas, la capacidad de visualizar datos y crear paneles fácilmente legibles, y las herramientas adicionales que proporcionen para mejorar la supervisión, como las alertas automatizadas.

## <a name="next-steps"></a>Pasos siguientes

* Para empezar a trabajar con la instrumentación de las aplicaciones, consulte [Generación de eventos y registros de nivel de aplicación](service-fabric-diagnostics-event-generation-app.md).
* Obtenga más información acerca de la supervisión de la plataforma y los eventos que proporciona Service Fabric en [Generación de eventos y registros de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md)
* Consulte los pasos necesarios para configurar Application Insights para una aplicación en [Supervisión y diagnóstico de una aplicación ASP.NET Core en Service Fabric](service-fabric-tutorial-monitoring-aspnet.md).
* Aprenda a configurar Log Analytics de OMS para supervisar contenedores: [Supervisión de contenedores de Windows en Service Fabric mediante OMS](service-fabric-tutorial-monitoring-wincontainers.md).
* Más información acerca de las recomendaciones generales de supervisión para los recursos de Azure: [Procedimientos recomendados: supervisión y diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 
