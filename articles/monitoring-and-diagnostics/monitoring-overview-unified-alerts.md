---
title: "Exploración de la nueva experiencia de Alertas (versión preliminar) de Azure Monitor| Microsoft Docs"
description: "Información sobre cómo la nueva experiencia sencilla y escalable de alertas de Azure facilita la creación, visualización y administración de las alertas"
author: manishsm-msft
manager: kmadnani1
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mamit
ms.custom: 
ms.openlocfilehash: 5ded43548d9aea106c6e083476df4e735b8c00a6
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="explore-the-new-alerts-preview-experience-in-azure-monitor"></a>Exploración de la nueva experiencia de Alertas (versión preliminar) de Azure Monitor

## <a name="overview"></a>Información general
 La experiencia de Alertas de Azure tiene un nuevo aspecto y una funcionalidad actualizada. Esta nueva experiencia está disponible en la pestaña **Alertas (versión preliminar)** en Azure Monitor. A continuación, se detallan algunas de las ventajas de usar la nueva experiencia de Alertas (versión preliminar):

 - **Separación de las alertas desencadenadas y las reglas de alertas**: en la experiencia de Alertas (versión preliminar), las reglas de alertas (la definición de la condición que desencadena una alerta) y las alertas desencadenadas (una instancia de la activación de la regla de alertas) están diferenciadas, es decir, que las vistas operativas y de configuración son independientes. 
 - **Una experiencia unificada de creación de alertas de métrica y de registro**: la nueva experiencia de creación de Alertas (versión preliminar) guía al usuario en el proceso de configuración de una regla de alertas, lo que facilita la detección de las condiciones correctas para que se active una alerta. 
 - **Visualización de las alertas desencadenadas de Log Analytics en Azure Portal**: en la experiencia de Alertas (versión preliminar), ahora también puede ver las alertas desencadenadas de Log Analytics en su suscripción.  

En las secciones siguientes se describe con más detalle cómo funciona la nueva experiencia. 

## <a name="taxonomy"></a>Taxonomía
La experiencia de Alertas (versión preliminar) usa los siguientes conceptos para separar los objetos de regla de alertas y alerta desencadenada al unificar la experiencia de creación con diferentes tipos de alerta.

- **Recurso de destino**: un destino puede ser cualquier recurso de Azure. El recurso de destino define el ámbito y las señales disponibles para las alertas. Destinos de ejemplo: una máquina virtual, una cuenta de almacenamiento, un conjunto de escalado de máquinas virtuales, un área de trabajo de Log Analytics o una solución. 

- **Criterios**: los criterios son la combinación de la señal y la lógica aplicadas en un recurso de destino. Ejemplos: Porcentaje de CPU > 70 %, Tiempo de respuesta del servidor > 4 ms, Recuento de resultados de una consulta de registros > 100, etc. 

- **Señal**: las señales las emite el recurso de destino y pueden ser de varios tipos. Esta versión preliminar admite tipos de señal de métrica y registro.

- **Lógica**: lógica definida por el usuario para comprobar si la señal está dentro del rango o los valores esperados.  
 
- **Acción**: una llamada específica que se envían al receptor de una notificación (por ejemplo, envío de un correo electrónico a una dirección o una publicación en una dirección URL de webhook). Las notificaciones normalmente pueden desencadenar varias acciones. Los tipos de alertas compatibles con esta versión admiten los grupos de acciones.  
 
- **Regla de alertas**: la definición de una condición que desencadenará la alerta. En esta versión preliminar, la regla de alertas captura el destino y los criterios para las alertas. La regla de alertas puede tener el estado deshabilitado o habilitado. 
 
- **Alerta desencadenada**: se crea cuando se activa una regla de alertas habilitada. El objeto de alerta desencadenada puede tener un estado desencadenado o sin resolver.

    > [!NOTE]
    > Esto difiere de la experiencia actual de alertas, donde la alerta representa tanto la regla como la alerta desencadenada y, por tanto, puede tener uno de los estados de advertencia, activa o deshabilitada.
    >

## <a name="single-place-to-view-and-manage-alerts"></a>Un único lugar para ver y administrar alertas
El objetivo de la experiencia de Alertas (versión preliminar) es que se puedan ver y administrar todas las alertas de Azure desde un único lugar. En las siguientes subsecciones se describen las funciones de cada pantalla individual de la nueva experiencia.

### <a name="alerts-preview-overview-page"></a>Página de información general de Alertas (versión preliminar)
En la página de información general de **Supervisar: Alertas (versión preliminar)** se muestra el resumen agregado de todas las alertas desencadenadas, así como el total de reglas de alertas configuradas y habilitadas. También se muestra una lista de todas las alertas desencadenadas. Al cambiar las suscripciones o los parámetros de filtro se actualizan los agregados y la lista de alertas desencadenadas.

> [!NOTE]
> Las alertas desencadenadas mostradas en Alertas (versión preliminar) se limitan a las alertas de métrica y registro compatibles; Azure Monitor muestra el número de alertas desencadenadas, incluidas las de la experiencia de Alertas de Azure anterior.

 ![alerts-preview-overview](./media/monitoring-overview-unified/alerts-preview-overview.png) 

### <a name="alert-rules-management"></a>Administración de reglas de alertas
**Supervisar: Alertas (versión preliminar) > Reglas** es una única página en la que se pueden administrar todas las reglas de alertas de las suscripciones de Azure. Se enumeran todas las reglas de alertas (habilitadas o deshabilitadas), que se pueden ordenar según los recursos de destino, los grupos de recursos, el nombre de regla o el estado. Las reglas de alertas se pueden deshabilitar, habilitar o editar en esta página.  

 ![alerts-preview-rules](./media/monitoring-overview-unified/alerts-preview-rules.png)


## <a name="one-alert-authoring-experience-across-all-monitoring-sources"></a>Una experiencia de creación de alertas en todos los orígenes de supervisión
En la experiencia Alertas (versión preliminar), se pueden crear alertas de forma coherente con independencia del tipo de señal o del servicio de supervisión. Todas las alertas desencadenadas y los detalles relacionados están disponibles en una sola página.  
 
La creación de una alerta es una tarea de tres pasos en la que el usuario primero elige un destino para la alerta, después selecciona la señal correcta y especifica la lógica que se aplicará a la señal como parte de la regla de alertas. Este proceso simplificado de creación ya no requiere que el usuario sepa las señales o el origen de supervisión admitidos antes de seleccionar un recurso de Azure. La experiencia de creación común filtra automáticamente la lista de señales disponibles según el recurso de destino seleccionado y guía el proceso de creación de la lógica de la alerta.

Puede obtener más información sobre cómo crear los siguientes tipos de alertas [aquí](monitor-alerts-unified-usage.md). 
- Alertas de métrica (denominadas Alertas de métricas de Near Real Time en la experiencia actual)
- Alertas de registro (Log Analytics)
 

## <a name="alert-types-supported-in-this-preview"></a>Tipos de alertas compatibles con esta versión preliminar


| **Tipo de señal** | **Origen de supervisión** | **Descripción** | 
|-------------|----------------|-------------|
| Métrica | Azure Monitor | Estas alertas de métrica se llaman [**Alertas de métricas de Near Real Time**](monitoring-near-real-time-metric-alerts.md) en la experiencia actual y permiten evaluar las condiciones de métrica con una frecuencia de un minuto y admiten reglas de varias métricas. Hay disponible una lista de los tipos de recursos admitidos [aquí](monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for). Otras alertas de métrica, según se define [aquí](monitoring-overview-alerts.md#alerts-in-different-azure-services), no se admiten en la experiencia de Alertas (versión preliminar).|
| Registros  | Log Analytics | Reciba notificaciones o ejecute acciones automáticas cuando una consulta de búsqueda de registros sobre datos de métrica o de evento cumple determinados criterios.|
| Registros  | Registros de actividad | Incompatible con la experiencia de Alertas (versión preliminar). |
| Registros  | Application Insights | Incompatible con la experiencia de Alertas (versión preliminar). |
| Métrica | Application Insights | Incompatible con la experiencia de Alertas (versión preliminar). |
| Pruebas de disponibilidad | Application Insights | Incompatible con la experiencia de Alertas (versión preliminar). |


## <a name="next-steps"></a>pasos siguientes
- [Obtenga información sobre cómo usar la nueva experiencia de Alertas (versión preliminar) para crear, ver y administrar alertas](monitor-alerts-unified-usage.md).
- [Obtenga información sobre las alertas de registros en la experiencia de Alertas (versión preliminar)](monitor-alerts-unified-log.md).
- [Obtenga información sobre las alertas de métrica en la experiencia de Alertas (versión preliminar)](monitoring-near-real-time-metric-alerts.md).

