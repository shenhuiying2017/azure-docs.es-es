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
ms.openlocfilehash: 356988e8ae743d73c8e2cc7cc106cbc5b0d1a423
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/28/2018
---
# <a name="the-new-alerts-experience-in-azure-monitor"></a>La nueva experiencia de Alertas de Azure Monitor

## <a name="overview"></a>Información general
Alertas cuenta con una nueva experiencia. La experiencia de Alertas anterior se encuentra ahora en la pestaña Alertas (clásico). La nueva experiencia de Alertas presenta las siguientes ventajas en comparación con la experiencia de Alertas (clásico):

 - **Separación de las alertas desencadenadas y las reglas de alertas**: las reglas de alertas (la definición de la condición que desencadena una alerta) y las alertas desencadenadas (una instancia de la activación de la regla de alertas) están diferenciadas, es decir, que las vistas operativas y de configuración son independientes.
 - **Una experiencia de creación unificada**: la creación de todas las alertas para las métricas, los registros y el registro de actividades en Azure Monitor, Log Analytics y Application Insights está en un solo lugar. 
 - **Visualización de las alertas desencadenadas de Log Analytics en Azure Portal**: ahora también puede ver las alertas desencadenadas de Log Analytics en su suscripción. Anteriormente, estas se encontraban en un portal independiente. 
 - **Mejor flujo de trabajo**: la nueva experiencia de creación de Alertas guía al usuario en el proceso de configuración de una regla de alertas, lo que facilita la detección de las condiciones correctas para que se active una alerta.
 

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

 ![Información general de las alertas](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Administración de reglas de alertas
**Supervisar: Alertas > Reglas** es una única página en la que se pueden administrar todas las reglas de alertas de las suscripciones de Azure. Se enumeran todas las reglas de alertas (habilitadas o deshabilitadas), que se pueden ordenar según los recursos de destino, los grupos de recursos, el nombre de regla o el estado. Las reglas de alertas se pueden deshabilitar, habilitar o editar en esta página.  

 ![alertas-reglas](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Una experiencia de creación de alertas en todos los orígenes de supervisión
En la experiencia de Alertas, se pueden crear alertas de forma coherente con independencia del tipo de señal o del servicio de supervisión. Todas las alertas desencadenadas y los detalles relacionados están disponibles en una sola página.  
 
La creación de una alerta es una tarea de tres pasos en la que el usuario primero elige un destino para la alerta, después selecciona la señal correcta y especifica la lógica que se aplicará a la señal como parte de la regla de alertas. Este proceso simplificado de creación ya no requiere que el usuario sepa las señales o el origen de supervisión admitidos antes de seleccionar un recurso de Azure. La experiencia de creación común filtra automáticamente la lista de señales disponibles según el recurso de destino seleccionado y guía el proceso de creación de la lógica de la alerta.

Puede obtener más información sobre cómo crear los siguientes tipos de alertas [aquí](monitor-alerts-unified-usage.md).
- Alertas de métricas
- Alertas de registro (Log Analytics)
- Alertas de registro (Registros de actividad)
- Alertas de registro (Application Insights)

 

## <a name="alert-types-supported"></a>Tipos de alerta admitidos


| **Tipo de señal** | **Origen de supervisión** | **Descripción** | 
|-------------|----------------|-------------|
| Métrica | Azure Monitor | Estas alertas de métrica también se llaman [**Alertas de métricas de Near Real Time**](monitoring-near-real-time-metric-alerts.md) y permiten evaluar las condiciones de métrica con una frecuencia de un minuto y admiten reglas de varias métricas. Hay disponible una lista de los tipos de recursos admitidos [aquí](monitoring-near-real-time-metric-alerts.md#metrics-and-dimensions-supported). Otras alertas de métrica, según se define [aquí](monitoring-overview-alerts.md#alerts-in-different-azure-services), no se admiten en la nueva experiencia de Alertas. Puede encontrarlas en Alertas (clásico)|
| Registros  | Log Analytics | Reciba notificaciones o ejecute acciones automáticas cuando una consulta de búsqueda de registros sobre datos de métrica o de evento cumple determinados criterios.|
| Registro de actividad | Registros de actividad | Esta categoría contiene los registros de todas las acciones de creación, actualización y eliminación realizadas a través del destino seleccionado (recurso/grupo de recursos/suscripción). |
| Registros  | Registros de Service Health | Incompatible con la experiencia de Alertas.   |
| Registros  | Application Insights | Esta categoría contiene registros con los detalles de rendimiento de la aplicación. Mediante la consulta de análisis, puede definir las condiciones para las acciones que realizar, en función de los datos de la aplicación. |
| Métrica | Application Insights | Incompatible con la experiencia de Alertas. Las encontrará en Alertas (clásico) |
| Pruebas de disponibilidad | Application Insights | Incompatible con la experiencia de Alertas. Las encontrará en Alertas (clásico) |


## <a name="next-steps"></a>Pasos siguientes
- [Obtenga información sobre cómo usar la nueva experiencia de Alertas para crear, ver y administrar alertas](monitor-alerts-unified-usage.md).
- [Obtenga información sobre las alertas de registros en la experiencia de Alertas](monitor-alerts-unified-log.md).
- [Obtenga información sobre las alertas de métrica en la experiencia de Alertas](monitoring-near-real-time-metric-alerts.md).
- [Obtenga información sobre las alertas de registros de actividad en la experiencia de Alertas](monitoring-activity-log-alerts-new-experience.md).
