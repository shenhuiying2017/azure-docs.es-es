---
title: "Información general sobre las alertas en Microsoft Azure y Azure Monitor | Microsoft Docs"
description: "Las alertas permiten supervisar los registros, los eventos o las métricas de recursos de Azure y recibir una notificación cuando se cumple una condición especificada."
author: rboucher
manager: carmonm
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: robb
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c1f0182f27cfb8441a09abd2031b365a4ab4315a
ms.sourcegitcommit: b7adce69c06b6e70493d13bc02bd31e06f291a91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2017
---
# <a name="what-are-alerts-in-microsoft-azure"></a>¿Qué son las alertas en Microsoft Azure?
Este artículo se describen los diversos orígenes de las alertas en Microsoft Azure, cuáles son los propósitos de dichas alertas, sus ventajas y cómo empezar a usarlas. Se aplica específicamente a Azure Monitor, pero brinda indicaciones sobre otros servicios que también tienen alertas. Las alertas ofrecen un método de supervisión en Azure que le permiten configurar las condiciones en los datos y recibir notificaciones cuando las condiciones coinciden con los datos de supervisión más actualizados.


## <a name="taxonomy-of-azure-alerts"></a>Taxonomía de las alertas de Azure
Azure utiliza los siguientes términos para describir las alertas y sus funciones:
* **Alerta**: una definición de criterios (una o más reglas o condiciones) que se activa cuando se cumplen.
* **Activo**: el estado en el que se cumplen los criterios definidos por una alerta.
* **Resuelto**: el estado en el que ya no se cumplen los criterios definidos por una alerta, después que se cumplieran con anterioridad.
* **Notificación**: la acción realizada en función de la activación de una alerta.
* **Acción**: una llamada específica que se envían al receptor de una notificación (por ejemplo, envío de un correo electrónico a una dirección o una publicación en una dirección URL de webhook). Las notificaciones normalmente pueden desencadenar varias acciones.

    > [!NOTE]
    > Como parte de la evolución de las alertas en Azure, hay disponible una versión preliminar de una experiencia unificada. La nueva experiencia Alerts (versión preliminar) usa una taxonomía diferente. Más información sobre [Alerts (versión preliminar)](monitoring-overview-unified-alerts.md). 
    >

## <a name="alerts-in-different-azure-services"></a>Alertas en los distintos servicios de Azure
Las alertas están disponibles a través de varios servicios de supervisión de Azure. Para más información sobre cómo y cuándo se deben utilizar estos servicios, [consulte este artículo](./monitoring-overview.md). Este es un desglose de los tipos de alerta disponibles en Azure:


| Servicio | Tipo de alerta | Servicios admitidos | DESCRIPCIÓN |
|---|---|---|---|
| Azure Monitor | [Alertas de métricas](./insights-alerts-portal.md) | [Métricas compatibles con Azure Monitor](./monitoring-supported-metrics.md) | Reciba una notificación cuando cualquier métrica de nivel de plataforma cumple una condición específica (por ejemplo, el porcentaje de CPU en una máquina virtual supera el 90 % durante los últimos 5 minutos). |
|Azure Monitor | [Alertas de métricas casi en tiempo real (versión preliminar)](./monitoring-near-real-time-metric-alerts.md)| [Recursos compatibles de Azure Monitor](./monitoring-near-real-time-metric-alerts.md#what-resources-can-i-create-near-real-time-metric-alerts-for) | Reciba una notificación con más rapidez que las alertas de métricas cuando una o varias métricas de nivel de plataforma cumplan las condiciones especificadas (por ejemplo, el % de CPU en una máquina virtual es mayor que 90 y Datos de entrada de red es mayor que 500 MB durante los últimos 5 minutos). |
| Azure Monitor | [Alertas de registro de actividad](./monitoring-activity-log-alerts.md) | Todos los tipos de recursos disponibles en Azure Resource Manager | Reciba una notificación cuando cualquier nuevo evento en el [registro de actividad de Azure](./monitoring-overview-activity-logs.md) coincide con las condiciones específicas (por ejemplo, cuando se produce una operación "Eliminar VM" en myProductionResourceGroup o cuando aparece un nuevo evento de Service Health con estado "Activo"). |
| Application Insights | [Alertas de métricas](../application-insights/app-insights-alerts.md) | Cualquier aplicación instrumentada para enviar datos a Application Insights | Reciba una notificación cuando cualquier métrica de nivel de plataforma cumple una condición específica (por ejemplo, el tiempo de respuesta del servidor es superior a 2 segundos). |
| Application Insights | [Alertas de prueba web](../application-insights/app-insights-monitor-web-app-availability.md) | Cualquier sitio web instrumentado para enviar datos a Application Insights | Reciba una notificación cuando la disponibilidad o la capacidad de respuesta de un sitio web está por debajo de las expectativas. |
| Log Analytics | [Alertas de Log Analytics](../log-analytics/log-analytics-alerts.md) | Cualquier servicio configurado para enviar datos a Log Analytics | Reciba una notificación cuando una consulta de búsqueda de Log Analytics sobre datos de métrica o de evento cumple determinados criterios. |

## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre datos de Azure Monitor
Existen tres tipos de alertas de los datos disponibles en Azure Monitor: alertas métricas y alertas de métricas casi en tiempo real (versión preliminar) alertas de registro de actividad.

* **Alertas de métricas**: la alerta se desencadena cuando el valor de una métrica específica cruza un umbral que se le haya asignado. La alerta genera una notificación cuando la alerta está "Activada" (si se sobrepasa el umbral y se cumple la condición de alerta), así como cuando queda en estado "Resuelto" (cuando se vuelve a sobrepasa el umbral y ya no se cumple la condición). Para ver una lista creciente de las métricas disponibles compatibles con Azure Monitor, consulte la [lista de métricas que se admiten en Azure Monitor](monitoring-supported-metrics.md).
* **Alertas de métricas casi en tiempo real (versión preliminar)**: estas alertas son similares a las alertas de métricas, pero difieren de varias maneras. En primer lugar, como lo sugiere su nombre, estas alertas pueden desencadenarse casi en tiempo real (tan rápido como 1 minuto). También admiten la supervisión de varias métricas (actualmente dos).  La alerta genera una notificación cuando la alerta está “Activada” (si se sobrepasa el umbral de cada métrica a la vez y se cumple la condición de la alerta), así como cuando queda en estado “Resuelto” (cuando al menos una métrica vuelve a cruzar el umbral y ya no se cumple la condición).

    > [!NOTE]
    > Las alertas de métricas casi en tiempo real se encuentran actualmente en versión preliminar pública. La funcionalidad y la experiencia del usuario están sujetas a cambios.
    >
    >

* **Alertas de registro de actividad**: una alerta de registro de streaming que se desencadena cuando se genera un evento de registro de actividad que coincide con los criterios de filtro que se han asignado. Estas alertas tienen solo un estado "Activado", puesto que el motor de alertas simplemente aplica los criterios de filtro a cualquier evento nuevo. Se pueden utilizar estas alertas para recibir notificaciones cuando se produzca un incidente de Service Health o cuando un usuario o una aplicación realiza una operación en su suscripción, por ejemplo, "Eliminar máquina virtual".

Para los datos de registro de diagnóstico disponibles a través de Azure Monitor, se recomienda enrutar los datos a Log Analytics y el uso de una alerta de Log Analytics. El diagrama siguiente resume los orígenes de datos en Azure Monitor y, de forma conceptual, la manera en la que se puede se puede alertar sobre esos datos.

![Alertas explicadas](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-alert"></a>¿Cómo se puede recibir una notificación sobre una alerta de Azure Monitor?
Históricamente, las alertas de Azure de distintos servicios usan sus propios métodos de notificación integrados. A partir de ahora, Azure Monitor ofrece una agrupación de notificación reutilizable denominada Grupos de acciones. Los Grupos de acciones especifican un conjunto de receptores para una notificación (cualquier número de direcciones de correo electrónico, números de teléfono para SMS o direcciones URL de webhook) y cada vez que se activa una alerta que hace referencia al Grupo de acciones, todos los receptores reciben esa notificación. Esto permite reutilizar una agrupación de receptores (por ejemplo, la lista de ingenieros de guardia) a través de muchos objetos de alerta. Actualmente, solo las alertas de registro de actividad hacen uso de lo Grupos de acciones, pero se está trabajando para que otros tipos de alerta de Azure utilicen también los Grupos de acciones.

Los Grupos de acciones admiten la notificación a través de una publicación en una dirección URL de webhook, además de direcciones de correo electrónico y números SMS. Esto permite la automatización y corrección, por ejemplo, mediante:
    - Runbook de Azure Automation
    - Función de Azure
    - Aplicación lógica de Azure
    - un servicio de terceros

Las alertas de métricas casi en tiempo real (versión preliminar) y las alertas de registro de actividad usan Grupos de acciones.

Las alertas de métricas aún no utilizan los Grupos de acciones. En una alerta de métrica individual puede configurar notificaciones para:
* Enviar notificaciones de correo electrónico al administrador de servicios o a los coadministradores, o a las direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook, que permite iniciar acciones de automatización adicionales.

## <a name="next-steps"></a>pasos siguientes
Obtenga información sobre las reglas de alertas y su configuración mediante:

* Más información sobre las [métricas](monitoring-overview-metrics.md)
* Configuración de [alertas de métricas a través de Azure Portal](insights-alerts-portal.md)
* Configuración de [alertas de métricas con PowerShell](insights-alerts-powershell.md)
* Configuración de [alertas de métricas con la interfaz de línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* Configuración de [alertas de métricas con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Más información sobre el [registro de actividad](monitoring-overview-activity-logs.md)
* Configuración de [alertas del registro de actividad a través de Azure Portal](monitoring-activity-log-alerts.md)
* Configuración de [alertas del registro de actividad a través de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Revisión del [esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md)
* Más información sobre [alertas de métricas casi en tiempo real](monitoring-near-real-time-metric-alerts.md)
* Más información sobre las [notificaciones del servicio](monitoring-service-notifications.md)
* Más información sobre los [grupos de acciones](monitoring-action-groups.md)
* Configuración de las [alertas con Alerts (versión preliminar)](monitor-alerts-unified-usage.md)
