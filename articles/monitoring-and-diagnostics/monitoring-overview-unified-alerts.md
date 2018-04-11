---
title: Exploración de la nueva experiencia de Alertas de Azure Monitor| Microsoft Docs
description: Información sobre cómo la nueva experiencia sencilla y escalable de alertas de Azure facilita la creación, visualización y administración de las alertas
author: manishsm-msft
manager: kmadnani1
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: ''
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2018
ms.author: mamit
ms.custom: ''
ms.openlocfilehash: 16e0fc493a257504e2708336e05c30b36d4bea15
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>La nueva experiencia de Alertas de Azure Monitor

## <a name="overview"></a>Información general

> [!NOTE]
> En este artículo se describen las alertas más recientes. Las alertas clásicas de Azure Monitor más antiguas se describen en el artículo de [introducción a las alertas clásicas](monitoring-overview-alerts.md). 
>
>

Alertas cuenta con una nueva experiencia. La experiencia de Alertas anterior se encuentra ahora en la pestaña Alertas (clásico). La nueva experiencia de Alertas presenta las siguientes ventajas en comparación con la experiencia de Alertas (clásico):

-   **Mejor sistema de notificación**: todas las nuevas alertas usan [grupos de acciones]( https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-action-groups), que se denominan grupos de notificaciones y acciones que pueden reutilizarse en varias alertas.  Las alertas de métricas clásicas y las alertas de Log Analytics más antiguas no usan grupos de acciones. 
- **Una experiencia de creación unificada**: la creación de todas las alertas para las métricas, los registros y el registro de actividades en Azure Monitor, Log Analytics y Application Insights está en un solo lugar. 
- **Visualización de las alertas desencadenadas de Log Analytics en Azure Portal**: ahora también puede ver las alertas desencadenadas de Log Analytics en su suscripción. Anteriormente, estas se encontraban en un portal independiente. 
- **Separación de las alertas desencadenadas y las reglas de alertas**: las reglas de alertas (la definición de la condición que desencadena una alerta) y las alertas desencadenadas (una instancia de la activación de la regla de alertas) están diferenciadas, es decir, que las vistas operativas y de configuración son independientes.
- **Mejor flujo de trabajo**: la nueva experiencia de creación de Alertas guía al usuario en el proceso de configuración de una regla de alertas, lo que facilita la detección de las condiciones correctas para que se active una alerta.
 
En concreto, las alertas de métricas más recientes presentan estas mejoras:
-   **Latencia mejorada**: las nuevas alertas de métricas se pueden ejecutar con una frecuencia de cada minuto. Las alertas de métricas anteriores siempre se ejecutan con una frecuencia de 5 minutos. Las alertas de registro todavía tienen un retraso de más de 1 minuto debido al tiempo que se tarda en ingerir los registros. 
-   **Compatibilidad con métricas multidimensionales**: puede enviar alertas sobre las métricas dimensionales que le permiten supervisar un segmento interesante de la métrica.
-   **Más control sobre las condiciones de las métricas**: puede definir reglas de alerta más avanzadas. Las nuevas alertas admiten la supervisión de los valores máximos, mínimos, promedio y totales de las métricas.
-   **Supervisión combinada de varias métricas**: se pueden supervisar varias métricas (actualmente hasta dos métricas) con una sola regla. Si ambas métricas incumplen sus respectivos umbrales durante el período especificado, se desencadena una alerta.
-   **Métricas de registros** (versión preliminar pública limitada): algunos datos de registro que van a Log Analytics ahora se pueden extraer y convertir en métricas de Azure Monitor y luego en alertas como cualquier otra métrica. 



En las secciones siguientes se describe con más detalle cómo funciona la nueva experiencia.

## <a name="alert-rules-terminology"></a>Terminología de las reglas de alertas
La nueva experiencia de Alertas usa los siguientes conceptos para separar los objetos de regla de alertas y alerta desencadenada al unificar la experiencia de creación con diferentes tipos de alerta.

- **Recurso de destino**: un destino puede ser cualquier recurso de Azure. El recurso de destino define el ámbito y las señales disponibles para las alertas. Destinos de ejemplo: una máquina virtual, una cuenta de almacenamiento, un conjunto de escalado de máquinas virtuales, un área de trabajo de Log Analytics o un recurso de Application Insights.

- **Criterios**: los criterios son la combinación de la señal y la lógica aplicadas en un recurso de destino. Ejemplos: Porcentaje de CPU > 70 %, Tiempo de respuesta del servidor > 4 ms, Recuento de resultados de una consulta de registros > 100, etc. 

- **Señal**: las señales las emite el recurso de destino y pueden ser de varios tipos. **Métrica**, **Registro de actividad**, **Application Insights** y **Registro** son tipos de señal admitidos.

- **Lógica**: lógica definida por el usuario para comprobar si la señal está dentro del rango o los valores esperados.  
 
- **Acción**: una acción específica llevada a cabo al desencadenarse la alerta. Por ejemplo, enviar un mensaje de correo electrónico a una dirección de correo electrónico o llamar a una dirección URL de webhook. Se pueden producir varias acciones al desencadenarse una alerta. Estas alertas admiten grupos de acciones.  
 
- **Regla de alertas**: la condición que desencadenará la alerta. La regla de alertas captura el destino y los criterios para las alertas. La regla de alertas puede tener el estado deshabilitado o habilitado.
 
    > [!NOTE]
    > Esto difiere de la experiencia de Alertas (clásico), donde la alerta representa tanto la regla como la alerta desencadenada y, por tanto, puede tener uno de los estados de advertencia, activa o deshabilitada.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Un único lugar para ver y administrar alertas
El objetivo de la experiencia de Alertas es que se puedan ver y administrar todas las alertas de Azure desde un único lugar. En las siguientes subsecciones se describen las funciones de cada pantalla individual de la nueva experiencia.

### <a name="alerts-overview-page"></a>Página de información general de las alertas
En la página de información general de **Supervisar: Alertas** se muestra el resumen agregado de todas las alertas desencadenadas, así como el total de reglas de alertas configuradas y habilitadas. También se muestra una lista de todas las alertas desencadenadas. Al cambiar las suscripciones o los parámetros de filtro se actualizan los agregados y la lista de alertas desencadenadas.

> [!NOTE]
> Las alertas desencadenadas mostradas en Alertas se limitan a las alertas de métrica y registro de actividad compatibles; Información general de Azure Monitor muestra el número de alertas desencadenadas, incluidas las de la experiencia de Alertas de Azure anterior.

 ![Información general de las alertas](./media/monitoring-overview-unified-alerts/alerts-preview-overview2.png) 

### <a name="alert-rules-management"></a>Administración de reglas de alertas
**Supervisar: Alertas > Reglas** es una única página en la que se pueden administrar todas las reglas de alertas de las suscripciones de Azure. Se enumeran todas las reglas de alertas (habilitadas o deshabilitadas), que se pueden ordenar según los recursos de destino, los grupos de recursos, el nombre de regla o el estado. Las reglas de alertas se pueden deshabilitar, habilitar o editar en esta página.  

 ![alertas-reglas](./media/monitoring-overview-unified-alerts/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Una experiencia de creación de alertas en todos los orígenes de supervisión
En la experiencia de Alertas, se pueden crear alertas de forma coherente con independencia del tipo de señal o del servicio de supervisión. Todas las alertas desencadenadas y los detalles relacionados están disponibles en una sola página.  
 
La creación de una alerta es una tarea de tres pasos en la que el usuario primero elige un destino para la alerta, después selecciona la señal correcta y especifica la lógica que se aplicará a la señal como parte de la regla de alertas. Este proceso simplificado de creación ya no requiere que el usuario sepa las señales o el origen de supervisión admitidos antes de seleccionar un recurso de Azure. La experiencia de creación común filtra automáticamente la lista de señales disponibles según el recurso de destino seleccionado y guía el proceso de creación de la lógica de la alerta.

Puede obtener más información sobre cómo crear los siguientes tipos de alertas [aquí](monitor-alerts-unified-usage.md).
- Alertas de métricas
- Alertas de registro (Log Analytics)
- Alertas de registro (Registros de actividad)
- Alertas de registro (Application Insights)

 
## <a name="alerts-supported-in-new-experience"></a>Alertas compatibles con la nueva experiencia
Las alertas están disponibles a través de varios servicios de supervisión de Azure. Para más información sobre cómo y cuándo se deben utilizar estos servicios, [consulte este artículo](./monitoring-overview.md). Este es un desglose de los tipos de alerta disponibles en Azure y del contenido que actualmente es compatible con la nueva experiencia de alertas. 


| **Tipo de señal** | **Origen de supervisión** | **Descripción** | 
|-------------|----------------|-------------|
| Métrica | Azure Monitor | También se conocen como [alertas de métricas casi en tiempo real](monitoring-near-real-time-metric-alerts.md) y permiten evaluar las condiciones de métrica con una frecuencia de un minuto y admiten reglas de varias métricas y de métricas multidimensionales. Hay disponible una lista de los tipos de recursos admitidos [aquí](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). |
| Métrica | Azure Monitor | Las [alertas de métricas clásicas más antiguas](monitoring-overview-alerts.md) no se admiten en la nueva experiencia de alertas. Puede encontrarlas en Alertas (clásicas) en Azure Portal. Las alertas clásicas admiten algunos tipos de métricas que todavía no migran a las alertas más recientes. Para ver una lista completa, consulte las [métricas compatibles](https://docs.microsoft.com/en-us/azure/monitoring-and-diagnostics/monitoring-supported-metrics).
| Registros  | Log Analytics | Reciba notificaciones o ejecute acciones automáticas cuando una consulta de búsqueda de registros sobre datos de métrica o de evento cumple determinados criterios. Las alertas más antiguas de Log Analytics siguen estando disponibles, pero [se están copiando a la nueva experiencia](monitoring-alerts-extend.md). Además, hay disponible una [versión preliminar de los *registros de Log Analytics como métricas*](monitoring-alerts-extend-tool.md). Esta versión preliminar permite tomar algunos tipos de registros y convertirlos en métricas, donde puede enviarle alertas mediante la nueva experiencia de alertas. Esta versión preliminar es útil si tiene registros que no son de Azure que desea obtener junto con las métricas nativas de Azure Monitor. |
| Registro de actividad | Registros de actividad (general) | Contiene los registros de todas las acciones de creación, actualización y eliminación realizadas a través del destino seleccionado (recurso/grupo de recursos/suscripción). |
| Registro de actividad  | Service Health | No es compatible con la nueva experiencia de alertas. Consulte [Creación de alertas del registro de actividad en notificaciones del servicio](monitoring-activity-log-alerts-on-service-notifications.md).  |
| Registros  | Application Insights | Contiene registros con los detalles de rendimiento de la aplicación. Mediante la consulta de análisis, puede definir las condiciones para las acciones que realizar, en función de los datos de la aplicación. |
| Métrica | Application Insights | No es compatible con la nueva experiencia de alertas. Consulte [Alertas de métricas](../application-insights/app-insights-alerts.md). |
| Pruebas de disponibilidad web | Application Insights | Incompatible con la experiencia de Alertas.  Consulte [Alertas de pruebas web](../application-insights/app-insights-monitor-web-app-availability.md). Disponible para cualquier sitio web instrumentado para enviar datos a Application Insights. Reciba una notificación cuando la disponibilidad o la capacidad de respuesta de un sitio web está por debajo de las expectativas. |




## <a name="next-steps"></a>Pasos siguientes
- [Obtenga información sobre cómo usar la nueva experiencia de Alertas para crear, ver y administrar alertas](monitor-alerts-unified-usage.md).
- [Obtenga información sobre las alertas de registros en la experiencia de Alertas](monitor-alerts-unified-log.md).
- [Obtenga información sobre las alertas de métrica en la experiencia de Alertas](monitoring-near-real-time-metric-alerts.md).
- [Obtenga información sobre las alertas de registros de actividad en la experiencia de Alertas](monitoring-activity-log-alerts-new-experience.md).
