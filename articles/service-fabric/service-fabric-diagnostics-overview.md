---
title: "Información general sobre supervisión y diagnóstico de Azure Service Fabric | Microsoft Docs"
description: "Obtenga información sobre la supervisión y el diagnóstico para los clústeres, las aplicaciones y los servicios de Azure Service Fabric."
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
ms.date: 01/10/2018
ms.author: dekapur
ms.openlocfilehash: 43a45a31efffcd623e6381049876c3607663ec4f
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Supervisión y diagnóstico para Azure Service Fabric

En este artículo se proporciona información general sobre la configuración de la supervisión y el diagnóstico para las aplicaciones que se ejecutan en los clústeres de Service Fabric. La supervisión y el diagnóstico son fundamentales para el desarrollo, las pruebas y la implementación de flujos de trabajo en cualquier entorno de la nube. La supervisión le permite realizar un seguimiento del uso de las aplicaciones, el uso de los recursos y el estado general del clúster. Puede utilizar esta información para diagnosticar y corregir cualquier problema en el clúster y para ayudar a prevenir problemas en el futuro. 

Los objetivos principales de la supervisión y diagnóstico son:
* Detectar y diagnosticar problemas de infraestructura
* Detectar problemas con la aplicación
* Conocer el consumo de recursos
* Realizar un seguimiento del rendimiento de la aplicación, el servicio y la infraestructura

En un clúster de Service Fabric, los datos de supervisión y diagnóstico proceden de tres niveles: aplicación, plataforma (clúster) e infraestructura. 
* El [nivel de aplicación](service-fabric-diagnostics-event-generation-app.md) incluye datos sobre el rendimiento de las aplicaciones y cualquier registro personalizado adicional que haya agregado. Los datos de supervisión de la aplicación deben acabar en Application Insights (AI) desde la propia aplicación. Pueden proceder del SDK de AI, de EventFlow o de otra canalización de su elección.
* El [nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md) incluye los eventos de las acciones que se llevan a cabo en el clúster, relacionadas con la administración del clúster y las aplicaciones que se ejecutan en él. Los datos de supervisión de la plataforma deben enviarse a Log Analytics de OMS, con la solución Service Fabric Analytics como ayuda para visualizar los eventos de entrada. Estos datos suelen enviarse a una cuenta de almacenamiento a través de la extensión Azure Diagnostics de Windows o Linux, desde la cual se accede mediante Log Analytics de OMS. 
* El nivel de infraestructura se centra en la [supervisión de rendimiento](service-fabric-diagnostics-event-generation-perf.md) y examina las métricas y los contadores de rendimiento fundamentales para determinar la utilización y la carga de los recursos. La supervisión de rendimiento puede realizarse mediante un agente: se recomienda utilizar al agente de OMS (Microsoft Monitoring) para que los datos de rendimiento acaben en el mismo lugar que los eventos de la plataforma, lo cual permite obtener una mejor experiencia de diagnóstico a medida que correlaciona los cambios en un clúster. 

![Gráfico de información general de diagnóstico](media/service-fabric-diagnostics-overview/diagnostics-overview.png)

## <a name="monitoring-scenarios"></a>Escenarios de supervisión

En esta sección se describen los escenarios principales para la supervisión de un clúster de Service Fabric: aplicación, clúster, rendimiento y supervisión del estado. Para cada escenario, se describen la intención y el enfoque general de la supervisión. Encontrará más detalles sobre estas y otras recomendaciones generales de supervisión para los recursos de Azure en [Procedimientos recomendados: supervisión y diagnóstico](https://docs.microsoft.com/azure/architecture/best-practices/monitoring). 

Estos escenarios también se asignan de forma flexible a los tres niveles de datos de supervisión y diagnóstico tal y como se describe anteriormente, es decir, para que cada escenario se gestione correctamente en el clúster, los datos de supervisión y diagnóstico deben entrar al nivel correspondiente. El escenario de supervisión del estado es una excepción, ya que abarca el clúster y todo lo que se ejecuta en él.

## <a name="application-monitoring"></a>Supervisión de aplicaciones
La supervisión de aplicaciones realiza un seguimiento del uso de las funciones y componentes de una aplicación que haya compilado. Le recomendamos supervisar las aplicaciones para asegurarse de que se detectan los problemas que afectan a los usuarios. La supervisión de las aplicaciones puede ser útil en los siguientes casos:
* Determinar la carga de la aplicación y el tráfico de usuarios: ¿es necesario escalar los servicios para cumplir con las exigencias del usuario o abordar un cuello de botella potencial en la aplicación?
* Identificar problemas con la comunicación remota del servicio en el clúster
* Averiguar lo que hacen los usuarios con la aplicación: la instrumentación de las aplicaciones puede ayudar a orientar el futuro desarrollo de las características y a obtener mejores diagnósticos de errores de la aplicación

Para la supervisión en el nivel de aplicación de Service Fabric, se recomienda utilizar Application Insights (AI). La integración de AI con Service Fabric incluye experiencias con herramientas para Visual Studio y Azure Portal, así como una explicación sobre el contexto y la comunicación remota del servicio de Service Fabric y el panel de AI y el mapa de aplicación, que da lugar a una experiencia de registro integrada completa. Aunque muchos registros se crean y recopilan automáticamente al utilizar AI, se recomienda agregar registros personalizados adicionales a las aplicaciones y que se muestren en AI junto con lo que se proporciona para crear una experiencia de diagnóstico más avanzada para gestionar problemas en el futuro. Obtenga más información sobre el uso de AI con Service Fabric en [Análisis de eventos con Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md). 

Para empezar a trabajar con la instrumentación del código para supervisar las aplicaciones, consulte [Generación de eventos y registros de nivel de aplicación](service-fabric-diagnostics-event-generation-app.md). 

### <a name="monitoring-application-availability"></a>Disponibilidad de la supervisión de la aplicación
Es posible que parezca que todo el contenido del clúster se esté ejecutando según lo previsto y los informes muestren un estado correcto, pero las aplicaciones parece que sean inaccesibles. Aunque no hay muchos casos en los que se pueda producir esto, es importante saber cuándo sucede poder solucionarlo tan pronto como sea posible. La supervisión de disponibilidad está unida en gran medida al seguimiento de la disponibilidad de los componentes del sistema para definir el "tiempo de actividad" general del sistema. En un clúster, nos centramos en la disponibilidad desde la perspectiva de la aplicación: la plataforma funciona para garantizar que los escenarios de administración del ciclo de vida de la aplicación no generan tiempos de inactividad. No obstante, varios problemas en el clúster pueden afectar al tiempo de actividad de la aplicación y, en estos casos, como propietario de la aplicación, quiere que se le informe inmediatamente. Se recomienda, junto con todas las demás aplicaciones, implementar un guardián en el clúster. El propósito de este guardián sería comprobar los puntos de conexión correspondientes para la aplicación en un intervalo de tiempo establecido e informar que están accesibles. También puede hacerlo mediante un servicio externo que haga ping en el punto de conexión y devuelva un informe en el intervalo de tiempo especificado.

## <a name="cluster-monitoring"></a>Supervisión de clústeres
La supervisión del clúster de Service Fabric es fundamental para garantizar que la plataforma y todas las cargas de trabajo que se ejecutan en ella se están ejecutando según lo previsto. Uno de los objetivos de Service Fabric es que las aplicaciones se sigan ejecutando aunque haya errores de hardware. Esto se logra gracias a la capacidad que tienen los servicios del sistema de la plataforma de detectar problemas de infraestructura y conmutar por error las cargas de trabajo rápidamente a otros nodos del clúster. Pero en este caso particular, ¿qué ocurre si los propios servicios del sistema tienen problemas? ¿O, si al intentar mover una carga de trabajo, se infringen las reglas de ubicación de los servicios? La supervisión del clúster le permite mantenerse informado sobre la actividad que está teniendo lugar en el clúster, lo cual resulta de ayuda al diagnosticar problemas y corregirlos de forma eficaz. Estos son algunos de los aspectos fundamentales a los que debe prestar atención:
* ¿Se comporta Service Fabric como esperaba, en cuanto a la ubicación de las aplicaciones y el equilibrio del trabajo en el clúster? 
* ¿Se toman medidas para modificar la configuración del clúster y estas proporcionan los resultados previstos? Esto es especialmente importante al escalar un clúster.
* ¿Gestiona Service Fabric los datos y la comunicación interna entre servicios del clúster correctamente?

Service Fabric proporciona un conjunto completo de eventos integrados, a través de los canales operativos y de datos y mensajería. En Windows, estos toman la forma de un solo proveedor de ETW con un conjunto de `logLevelKeywordFilters` pertinente que se utiliza para elegir entre distintos canales. En Linux, todos los eventos de la plataforma proceden de LTTng y se colocan en una tabla, desde la cual pueden filtrarse según sea necesario. 

Estos canales contienen eventos estructurados protegidos que pueden utilizarse para comprender mejor el estado del clúster. "Diagnostics" se habilita de forma predeterminada en el momento de la creación del clúster y configura una tabla de Azure Storage donde se envían los eventos de estos canales para que pueda consultarlos en el futuro. Para obtener más información sobre la supervisión de clústeres, consulte [Generación de eventos y registros de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md). Se recomienda utilizar Log Analytics de OMS para supervisar el clúster. Log Analytics de OMS ofrece una solución específica de Service Fabric (Service Fabric Analytics), que proporciona un panel personalizado para la supervisión de clústeres de Service Fabric, y le permite consultar los eventos del clúster y configurar alertas. Para obtener más información al respecto, consulte [Análisis de eventos con OMS](service-fabric-diagnostics-event-analysis-oms.md). 

### <a name="watchdogs"></a>Guardianes
Por lo general, un guardián es un servicio independiente que puede vigilar el estado y la carga en los servicios, hacer ping en los puntos de conexión e informar sobre el estado de cualquier componente en el clúster. Esto puede ayudar a evitar errores que se pasan por alto con la vista de un solo servicio. Los guardianas también son un buen lugar donde hospedar el código que realiza acciones de subsanación sin interacción del usuario (por ejemplo, limpiar los archivos de registro de almacenamiento a determinados intervalos de tiempo). [Aquí](https://github.com/Azure-Samples/service-fabric-watchdog-service) puede encontrar la implementación de un servicio guardián de ejemplo.

## <a name="performance-monitoring"></a>Supervisión del rendimiento
La supervisión de la infraestructura subyacente es una parte fundamental del conocimiento del estado del clúster y la utilización de los recursos. La medición del rendimiento del sistema depende de varios factores, cada uno de los cuales suele medirse mediante unos indicadores clave de rendimiento (KPI). Los KPI pertinentes de Service Fabric pueden asignarse a las métricas que pueden recopilarse de los nodos del clúster, como los contadores de rendimiento.
Estos KPI pueden ayudarle con lo siguiente:
* Describir la utilización y carga de recursos: con el fin de escalar el clúster u optimizar los procesos de servicio.
* Predecir problemas de infraestructura: muchos problemas están precedidos por cambios bruscos (caídas) en el rendimiento, por lo que puede utilizar KPI, como las E/S de la red y la utilización de la CPU, para predecir y diagnosticar problemas infraestructurales.

Encontrará una lista de contadores de rendimiento que deberían recopilarse en el nivel de infraestructura en el artículo sobre [Métricas de rendimiento](service-fabric-diagnostics-event-generation-perf.md). 

Para la supervisión de rendimiento en el nivel de aplicación, Service Fabric proporciona un conjunto de contadores de rendimiento para los modelos de programación de Reliable Services y Actors. Si usa alguno de estos modelos, estos contadores de rendimiento pueden proporcionar KPI que ayudan a garantizar que los actores se aceleran y desaceleran correctamente, o que las solicitudes de servicio de confianza se están gestionando lo suficientemente rápido. Para obtener más información al respecto, consulte [Supervisión de comunicación remota de Reliable Service](service-fabric-reliable-serviceremoting-diagnostics.md#performance-counters) y [Supervisión de rendimiento para Reliable Actors](service-fabric-reliable-actors-diagnostics.md#performance-counters). Además, Application Insights también cuenta con un conjunto de métricas de rendimiento que recopilará, si se configura con la aplicación.

Utilice el agente de OMS para recopilar los contadores de rendimiento correspondientes y consulte estos KPI en Log Analytics de OMS. También puede utilizar la extensión del agente de Azure Diagnostics (o cualquier otro agente similar) para recopilar y almacenar las métricas para el análisis. 

## <a name="health-monitoring"></a>Supervisión del estado
La plataforma Service Fabric incluye un modelo de estado, el cual proporciona informes de estado extensibles para el estado de las entidades de un clúster. Cada nodo, aplicación, servicio, participación, réplica o instancia tiene un estado de mantenimiento que se actualiza continuamente. El estado de mantenimiento puede ser "Correcto", "Advertencia" o "Error". El estado de mantenimiento se cambia a través de los informes de mantenimiento que se emiten para cada entidad, en función de los problemas del clúster. El estado de mantenimiento de las entidades puede comprobarse en cualquier momento en Service Fabric Explorer (SFX) tal y como se muestra a continuación, o puede consultarse a través de Health API de las plataformas. También puede personalizar los informes de estado y modificar el estado de mantenimiento de una entidad agregando sus propios informes de mantenimiento o mediante Health API. Encontrará más detalles sobre el modelo de estado en [Introducción a la supervisión del estado de Service Fabric](service-fabric-health-introduction.md).

![Panel de estado de SFX](media/service-fabric-diagnostics-overview/sfx-healthstatus.png)

Además de ver los informes de estado más recientes de SFX, cada informe también está disponible como un evento. Los eventos de mantenimiento pueden recopilarse a través del canal operativo (consulte [Agregación de eventos con Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md#collect-health-and-load-events)) y almacenarse en Log Analytics de OMS para enviar alertas y realizar consultas en el futuro. Esto ayuda a detectar problemas que puedan afectar a la disponibilidad de las aplicaciones, por lo que se recomienda configurar alertas para los escenarios de error correspondientes (alertas personalizadas a través de OMS).

## <a name="monitoring-workflow"></a>Flujo de trabajo de supervisión 

El flujo de trabajo general de la supervisión y el diagnóstico consta de tres pasos:

1. **Generación de eventos**: incluye eventos (registros, seguimientos y eventos personalizados) en los niveles de infraestructura, plataforma (clúster) y aplicación.
2. **Agregación de eventos**: efectivamente, esta fase es la canalización para que los eventos acaben en una herramienta donde pueda analizarlos, lo cual incluye la extensión Azure Diagnostics o EventFlow.
3. **Análisis**: los eventos deben ser accesibles en una herramienta, para permitir el análisis y la visualización, la realización de consultas, el envío de alertas, etc.

Hay varios productos que abarcan las tres áreas, y puede elegir tecnologías diferentes para cada uno. Es importante asegurarse de que las diferentes piezas trabajen conjuntamente para ofrecer una solución de supervisión de un extremo a otro para el clúster.

## <a name="event-generation"></a>Generación de eventos

El primer paso del flujo de trabajo de supervisión y diagnóstico es la configuración de la creación y generación de datos de eventos y registros. Estos eventos, registros y contadores de rendimiento se emiten en los tres niveles: el nivel de aplicación (cualquier instrumentación que se agrega a las aplicaciones y servicios implementados en el clúster), el nivel de plataforma (eventos emitidos desde el clúster en función de la operación de Service Fabric) y el nivel de infraestructura (métricas de rendimiento de cada nodo). Los datos de diagnóstico que se recopilan en cada uno de estos niveles son personalizables, aunque Service Fabric habilita algunas técnicas predeterminadas de instrumentación. 

Obtenga más información sobre los [eventos de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md) y los [eventos de nivel de aplicación](service-fabric-diagnostics-event-generation-app.md) para comprender lo que se proporciona y cómo agregar otras instrumentaciones. La decisión principal que tiene que tomar aquí es cómo va a instrumentar la aplicación. Para las aplicaciones .NET, se recomienda utilizar ILogger, pero también puede explorar EventSource, Serilog y otras bibliotecas similares. Para Java, se recomienda utilizar Log4j. Aparte de las mencionadas, hay algunas otras opciones disponibles que pueden utilizarse en función de la naturaleza de la aplicación. No dude en explorar la que sería mejor para su caso de uso específico, o elija una que le resulte más cómoda de utilizar. 

Después de tomar una decisión sobre el proveedor de registro que le gustaría utilizar, debe asegurarse de que los registros se agregan y almacenan correctamente.

## <a name="event-aggregation"></a>Agregación de datos

Para recopilar los registros y eventos que generan sus aplicaciones y el clúster, en general recomendamos que use la [extensión Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (más similar a la recopilación de registros basada en agentes) o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (recopilación de registros In-Process). Si usa Application Insights y desarrolla con .NET o Java, se recomienda utilizar el SDK de Application Insights en lugar de EventFlow.

Aunque es posible realizar un trabajo similar mediante uno solo, en la mayoría de los casos combinar el agente de extensión de Azure Diagnostics con una canalización de recopilación In-Process (SDK de AI o EventFlow) da lugar a un flujo de trabajo de supervisión más fiable e integral. El agente de extensión de Azure Diagnostics será la ruta de acceso para eventos en el nivel de plataforma, aunque utilice el SDK de AI o EventFlow (colección In-Process) para sus registros en el nivel de aplicación. 

En caso de que desee utilizar uno solo, estos son algunos aspectos fundamentales a tener en cuenta.
* La recopilación de registros de aplicación mediante la extensión Azure Diagnostics es una buena opción para los servicios de Service Fabric si el conjunto de orígenes y destinos de los registros no cambia con frecuencia y hay una asignación directa entre los orígenes y sus destinos. La razón de esto es que la configuración de Azure Diagnostics se produce en la capa del Administrador de recursos, por lo que realizar cambios significativos en la configuración requiere actualizar todo el clúster. Esto significa que, en último término, suele ser una opción menos eficiente que utilizar el SDK de AI o EventFlow.
* Usar EventFlow le permite que los servicios envíen los registros directamente a una plataforma de análisis y visualización, o al almacenamiento. También podrían utilizarse otras bibliotecas (ILogger, Serilog, etc.) para el mismo propósito, pero EventFlow tiene la ventaja de que ha sido diseñada específicamente para la recopilación de registros In-Process y para admitir los servicios de Service Fabric. Esto suele tener varias ventajas en cuanto a la facilidad de configuración e implementación y ofrece más flexibilidad para admitir diferentes bibliotecas de registros y herramientas de análisis. 

## <a name="event-analysis"></a>Análisis de eventos

Hay varias plataformas excelentes en el mercado para los análisis y la visualización de datos de supervisión y diagnóstico. Las dos que se recomiendan son [OMS](service-fabric-diagnostics-event-analysis-oms.md) y [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) debido a su integración con Service Fabric. También debe consultar [Elastic Stack](https://www.elastic.co/products) (especialmente si planea ejecutar un clúster en un entorno sin conexión), [Splunk](https://www.splunk.com/) o cualquier otra plataforma de su preferencia. 

Los puntos clave para cualquier plataforma que elija deben incluir su grado de comodidad con la interfaz de usuario y las opciones de consultas, la capacidad de visualizar datos y crear paneles fácilmente legibles, y las herramientas adicionales que proporcionen para mejorar la supervisión, como las alertas automatizadas.

Además de la plataforma que seleccione, al configurar un clúster de Service Fabric como un recurso de Azure, también obtendrá acceso a la supervisión de rendimiento integrada en Azure para las máquinas.

### <a name="azure-monitor"></a>Azure Monitor

Puede usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para supervisar muchos de los recursos de Azure que incorporan un clúster de Service Fabric. En Azure Portal, se recopila y se muestra automáticamente un conjunto de métricas para el [conjunto de escalado de máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) y las [máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuales. Para ver la información recopilada, en Azure Portal, seleccione el grupo de recursos que contenga el clúster de Service Fabric y seleccione el conjunto de escalado de máquinas virtuales que desee ver. En la sección **Supervisión** (en la navegación de la izquierda), seleccione **Métricas** para ver un gráfico con los valores.

![Vista de Azure Portal con la información de métrica recopilada](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Para personalizar los gráficos, siga las instrucciones del artículo de [introducción a las métricas en Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). También puede crear alertas basadas en estas métricas, como se describe en [Creación de alertas en Azure Monitor para servicios de Azure - Azure Portal](../monitoring-and-diagnostics/monitoring-overview-alerts.md). 

## <a name="next-steps"></a>pasos siguientes

* Obtenga más información sobre la supervisión de la plataforma y los eventos que proporciona Service Fabric en [Generación de eventos y registros de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md)
* Para empezar a trabajar con la instrumentación de las aplicaciones, consulte [Generación de eventos y registros de nivel de aplicación](service-fabric-diagnostics-event-generation-app.md)
* Consulte el tutorial [Supervisar y diagnosticar una aplicación ASP.NET Core en Service Fabric](service-fabric-tutorial-monitoring-aspnet.md)

