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
ms.date: 07/17/2017
ms.author: dekapur
ms.openlocfilehash: 88f4a23f89a1c8fd88db1df3a7ff03ae5df64c0f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitoring-and-diagnostics-for-azure-service-fabric"></a>Supervisión y diagnóstico para Azure Service Fabric

La supervisión y el diagnóstico son fundamentales para la realización correcta de desarrollos, pruebas e implementaciones de aplicaciones y servicios en cualquier entorno. Las soluciones de Service Fabric funcionan mejor cuando diseña e implementa la supervisión y el diagnóstico que ayudan a asegurarse de que las aplicaciones y los servicios funcionen según lo previsto en un entorno de desarrollo local o en producción.

Los objetivos principales de la supervisión y diagnóstico son:
* Detectar y diagnosticar problemas de hardware y de infraestructura
* Detectar problemas de software y aplicaciones, y reducir el tiempo de inactividad del servicio
* Conocer el consumo de recursos y facilitar la toma de decisiones de operaciones
* Optimizar el rendimiento de la aplicación, el servicio y la infraestructura
* Generar información de la empresa e identificar áreas de mejora


El flujo de trabajo general de la supervisión y el diagnóstico consta de tres pasos:

1. **Generación de eventos**: incluye eventos (registros, seguimientos y eventos personalizados) en los niveles de infraestructura (clúster), plataforma y aplicación o servicio.
2. **Agregación de eventos**: es preciso recopilar y agregar los eventos generados antes de que se puedan mostrar.
3. **Análisis**: los eventos se deben visualizar y acceder en algún formato, para permitir su análisis y visualización cuando sea necesario.

Hay varios productos que abarcan las tres áreas, y puede elegir tecnologías diferentes para cada uno. Es importante asegurarse de que las diferentes piezas trabajen conjuntamente para ofrecer una solución de supervisión de un extremo a otro para el clúster.

## <a name="event-generation"></a>Generación de eventos

El primer paso del flujo de trabajo de supervisión y diagnóstico es la creación y generación de eventos y registros. Estos eventos, registros y seguimientos se generan en dos niveles: la capa de la plataforma (que incluye el clúster, los equipos o acciones de Service Fabric) o la capa de la aplicación (la instrumentación que se agrega a las aplicaciones y servicios que se implementan en el clúster). Los eventos de cada uno de estos niveles son personalizables, aunque Service Fabric proporciona instrumentación de forma predeterminada.

Obtenga más información sobre los [eventos de nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md) y los [eventos de nivel de aplicación](service-fabric-diagnostics-event-generation-app.md) para comprender lo que se proporciona y cómo agregar otras instrumentaciones.

Después de tomar una decisión sobre el proveedor de registro que le gustaría utilizar, debe asegurarse de que los registros se agregan y almacenan correctamente.

## <a name="event-aggregation"></a>Agregación de datos

Para recopilar los registros y eventos que van a generar sus aplicaciones y el clúster, se suele recomendar utilizar [Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) (más similar a la recopilación de registros basada en agentes) o [EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md) (recopilación de registros In-Process).

La recopilación de registros de aplicación mediante la extensión Azure Diagnostics es una buena opción para los servicios de Service Fabric si el conjunto de orígenes y destinos de los registros no cambia con frecuencia y hay una asignación directa entre los orígenes y sus destinos. La razón para esto es que la configuración de Azure Diagnostics se produce en el nivel de Resource Manager, por lo que realizar cambios significativos en la configuración requiere actualizar o volver a implementar el clúster. Además, se utiliza de forma óptima al asegurarse de que los registros se almacenan en algún lugar un poco más permanente, donde puede obtenerse acceso a ellos mediante distintas plataformas de análisis. Esto significa que, en último término, es una opción menos eficiente para la canalización que utilizar una opción como EventFlow.

Usar [EventFlow](https://github.com/Azure/diagnostics-eventflow) le permite que los servicios envíen los registros directamente a una plataforma de análisis y visualización, o al almacenamiento. Podrían utilizarse otras bibliotecas (ILogger, Serilog, etc.) para el mismo propósito, pero EventFlow tiene la ventaja de que ha sido diseñada específicamente para la recopilación de registros In-Process y para admitir los servicios de Service Fabric. Esto suele tener varias ventajas potenciales:

* Fácil configuración e implementación
    * La configuración de la recopilación de datos de diagnóstico es parte de la configuración del servicio. Es fácil mantenerla siempre "sincronizada" con el resto de la aplicación.
    * Se puede realizar fácilmente la configuración por aplicación o por servicio.
    * La configuración de los destinos de datos a través de EventFlow es tan solo cuestión de agregar el paquete de NuGet adecuado y cambiar el archivo *eventFlowConfig.json*.
* Flexibilidad
    * La aplicación puede enviar los datos dondequiera que deban ir siempre que haya una biblioteca de cliente que admita el sistema de almacenamiento de datos de destino. Se pueden agregar tantos destinos nuevos como se desee.
    * Se pueden implementar reglas complejas de captura, filtrado y agregación de datos.
* Acceso a los datos y el contexto internos de la aplicación
    * El subsistema de diagnóstico que se ejecuta dentro del proceso de la aplicación o servicio puede ampliar fácilmente los seguimientos con información contextual.

Hay que destacar es que estas dos opciones no son mutuamente excluyentes y, si bien es posible conseguir resultados similares con el uso de uno u otro, también podría serle útil configurar ambos. En la mayoría de los casos, la combinación de un agente con la recopilación In-Process puede ofrecer un flujo de trabajo de supervisión más confiable. La extensión de Azure Diagnostics (agente) podría ser la ruta de acceso elegida para registros de nivel de plataforma, aunque podría utilizar EventFlow (colección In-Process) para sus registros de nivel de aplicación. Una vez que haya descubierto qué funciona mejor para usted, es el momento de pensar en cómo desea que se muestren y se analicen los datos.

## <a name="event-analysis"></a>Análisis de eventos

Hay varias plataformas excelentes en el mercado para los análisis y la visualización de datos de supervisión y diagnóstico. Los dos que recomendamos son [OMS](service-fabric-diagnostics-event-analysis-oms.md) y [Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md) debido a su mejor integración con Service Fabric, pero también debe tomar en consideración [Elastic Stack](https://www.elastic.co/products) (especialmente si se va a ejecutar un clúster en un entorno sin conexión), [Splunk](https://www.splunk.com/) o cualquier otra plataforma que prefiera.

Los puntos clave para cualquier plataforma que elija deben incluir su grado de comodidad con la interfaz de usuario y las opciones de consultas, la capacidad de visualizar datos y crear paneles fácilmente legibles, y las herramientas adicionales que proporcionen para mejorar la supervisión, como las alertas automatizadas.

Además de la plataforma que seleccione, al configurar un clúster de Service Fabric como un recurso de Azure, también obtendrá acceso la supervisión integrada en Azure para las máquinas, que puede ser útil para tareas de supervisión específicas de métricas y rendimiento.

### <a name="azure-monitor"></a>Azure Monitor

Puede usar [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview.md) para supervisar muchos de los recursos de Azure que incorporan un clúster de Service Fabric. En Azure Portal, se recopila y se muestra automáticamente un conjunto de métricas para el [conjunto de escalado de máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets) y las [máquinas virtuales](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesetsvirtualmachines) individuales. Para ver la información recopilada, en Azure Portal, seleccione el grupo de recursos que contenga el clúster de Service Fabric y seleccione el conjunto de escalado de máquinas virtuales que desee ver. En la sección **Supervisión**, seleccione **Métricas** para ver un gráfico con los valores.

![Vista de Azure Portal con la información de métrica recopilada](media/service-fabric-diagnostics-overview/azure-monitoring-metrics.png)

Para personalizar los gráficos, siga las instrucciones del artículo de [introducción a las métricas en Microsoft Azure](../monitoring-and-diagnostics/insights-how-to-customize-monitoring.md). También puede crear alertas basadas en estas métricas, como se describe en [Creación de alertas en Azure Monitor para servicios de Azure - Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Puede enviar alertas a un servicio de notificación mediante webhooks, tal y como se describe en [Configuración de un webhook en una alerta de métrica de Azure](../monitoring-and-diagnostics/insights-webhooks-alerts.md). Azure Monitor es compatible con una sola suscripción. Si tiene que supervisar varias suscripciones o necesita características adicionales, [Log Analytics](https://azure.microsoft.com/documentation/services/log-analytics/), parte de Microsoft Operations Management Suite, ofrece una solución integral de administración de TI para la infraestructura local y basada en la nube. Los datos de Azure Monitor se pueden enrutar directamente a Log Analytics para ver los registros y las métricas de todo el entorno desde un único lugar.

## <a name="next-steps"></a>Pasos siguientes

### <a name="watchdogs"></a>Guardianes

Un guardián es un servicio independiente que vigila el estado y la carga en los servicios e informa sobre el estado de cualquier componente en la jerarquía del modelo de mantenimiento. Esto puede ayudar a evitar errores que se pasan por alto con la vista de un solo servicio. Los guardianas también son un buen lugar donde hospedar el código que realiza acciones de subsanación sin interacción del usuario (por ejemplo, limpiar los archivos de registro de almacenamiento a determinados intervalos de tiempo). [Aquí](https://github.com/Azure-Samples/service-fabric-watchdog-service) puede encontrar la implementación de un servicio guardián de ejemplo.

Empiece por comprender cómo se generan los eventos y registros en el [nivel de plataforma](service-fabric-diagnostics-event-generation-infra.md) y en el [nivel de aplicación](service-fabric-diagnostics-event-generation-app.md).