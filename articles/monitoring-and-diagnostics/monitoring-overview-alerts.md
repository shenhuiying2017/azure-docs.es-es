---
title: Información general sobre las alertas clásicas en Microsoft Azure y Azure Monitor | Microsoft Docs
description: Las alertas permiten supervisar los registros, los eventos o las métricas de recursos de Azure y recibir una notificación cuando se cumple una condición especificada.
author: rboucher
manager: carmonm
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: a6dea224-57bf-43d8-a292-06523037d70b
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/28/2018
ms.author: robb
ms.openlocfilehash: 06ba05f71cf1f696033099c04448526a0421ad42
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/03/2018
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>¿Qué son las alertas clásicas en Microsoft Azure?

> [!NOTE]
> En este artículo se describe cómo crear alertas de métrica clásicas más antiguas. Azure Monitor ahora es compatible con [alertas de métrica casi en tiempo real más recientes](monitoring-overview-unified-alerts.md).
>

Este artículo se describen los diversos orígenes de las alertas en Microsoft Azure, cuáles son los propósitos de dichas alertas, sus ventajas y cómo empezar a usarlas. Se aplica específicamente a las alertas clásicas de Azure Monitor. Las alertas ofrecen un método de supervisión en Azure que le permiten configurar las condiciones en los datos y recibir notificaciones cuando las condiciones coinciden con los datos de supervisión más actualizados.


## <a name="taxonomy-of-azure-monitor-classic-alerts"></a>Taxonomía de las alertas clásicas de Azure Monitor
Azure utiliza los siguientes términos para describir las alertas clásicas y sus funciones:
* **Alerta**: una definición de criterios (una o más reglas o condiciones) que se activa cuando se cumplen.
* **Activo**: el estado en el que se cumplen los criterios definidos por una alerta clásica.
* **Resuelto**: el estado en el que ya no se cumplen los criterios definidos por una alerta clásica, después que se cumplieran con anterioridad.
* **Notificación**: la acción realizada en función de la activación de una alerta clásica.
* **Acción**: una llamada específica que se envían al receptor de una notificación (por ejemplo, envío de un correo electrónico a una dirección o una publicación en una dirección URL de webhook). Las notificaciones normalmente pueden desencadenar varias acciones.


## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre datos de Azure Monitor
Existen tres tipos de alertas de los datos disponibles en Azure Monitor: alertas métricas, alertas de métricas casi en tiempo real y alertas de registro de actividad.

* **Alertas de métricas clásicas**: la alerta se desencadena cuando el valor de una métrica específica cruza un umbral que se le haya asignado. La alerta genera una notificación cuando la alerta está "Activada" (si se sobrepasa el umbral y se cumple la condición de alerta), así como cuando queda en estado "Resuelto" (cuando se vuelve a sobrepasa el umbral y ya no se cumple la condición). Estas son alertas de métricas más antiguas. Para las alertas de métricas más recientes, consulte la información siguiente.

* **Alertas de métricas casi en tiempo real** (experiencia de alertas más reciente): se trata de una nueva generación de alertas de métricas con características mejoradas cuando se comparan con alertas de métricas anteriores. Estas alertas se pueden ejecutar con una frecuencia de 1 minuto. También admiten la supervisión varias métricas (actualmente dos).  La alerta genera una notificación cuando la alerta está “Activada” (si se sobrepasa el umbral de cada métrica a la vez y se cumple la condición de la alerta), así como cuando queda en estado “Resuelto” (cuando al menos una métrica vuelve a cruzar el umbral y ya no se cumple la condición).

* **Alertas clásicas de registro de actividad**: una alerta de registro de streaming que se desencadena cuando se genera un evento de registro de actividad que coincide con los criterios de filtro que se han asignado. Estas alertas tienen solo un estado "Activado", puesto que el motor de alertas simplemente aplica los criterios de filtro a cualquier evento nuevo. Se pueden utilizar estas alertas para recibir notificaciones cuando se produzca un incidente de Service Health o cuando un usuario o una aplicación realiza una operación en su suscripción, por ejemplo, "Eliminar máquina virtual".

Para los datos de registro de diagnóstico disponibles a través de Azure Monitor, se recomienda enrutar los datos a Log Analytics y el uso de una alerta de Log Analytics. El diagrama siguiente resume los orígenes de datos en Azure Monitor y, de forma conceptual, la manera en la que se puede se puede alertar sobre esos datos.

![Alertas explicadas](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="how-do-i-receive-a-notification-on-an-azure-monitor-classic-alert"></a>¿Cómo se puede recibir una notificación sobre una alerta clásica de Azure Monitor?
Históricamente, las alertas de Azure de distintos servicios usan sus propios métodos de notificación integrados. A partir de ahora, Azure Monitor ofrece una agrupación de notificación reutilizable denominada Grupos de acciones. Los Grupos de acciones especifican un conjunto de receptores para una notificación (cualquier número de direcciones de correo electrónico, números de teléfono para SMS o direcciones URL de webhook) y cada vez que se activa una alerta que hace referencia al Grupo de acciones, todos los receptores reciben esa notificación. Esto permite reutilizar una agrupación de receptores (por ejemplo, la lista de ingenieros de guardia) a través de muchos objetos de alerta. Actualmente, solo las alertas de registro de actividad hacen uso de lo Grupos de acciones, pero se está trabajando para que otros tipos de alerta de Azure utilicen también los Grupos de acciones.

Los Grupos de acciones admiten la notificación a través de una publicación en una dirección URL de webhook, además de direcciones de correo electrónico y números SMS. Esto permite la automatización y corrección, por ejemplo, mediante:
    - Runbook de Azure Automation
    - Función de Azure
    - Aplicación lógica de Azure
    - un servicio de terceros

Las alertas de métricas casi en tiempo real y las alertas de registro de actividad usan Grupos de acciones.

Las alertas de métricas anteriores disponibles en Alertas (clásica) no usan grupos de acciones. En una alerta de métrica individual puede configurar notificaciones para:
* Enviar notificaciones de correo electrónico al administrador de servicios o a los coadministradores, o a las direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook, que permite iniciar acciones de automatización adicionales.

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre las reglas de alertas y su configuración mediante:

* Más información sobre las [métricas](monitoring-overview-metrics.md)
* Configuración de [alertas de métricas clásicas a través de Azure Portal](insights-alerts-portal.md)
* Configuración de [alertas de métricas clásicas a través de PowerShell](insights-alerts-powershell.md)
* Configuración de [alertas de métricas clásicas con la interfaz de línea de comandos (CLI)](insights-alerts-command-line-interface.md)
* Configuración de [alertas de métricas clásicas con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931945.aspx)
* Más información sobre el [registro de actividad](monitoring-overview-activity-logs.md)
* Configuración de [alertas del registro de actividad a través de Azure Portal](monitoring-activity-log-alerts.md)
* Configuración de [alertas del registro de actividad a través de Resource Manager](monitoring-create-activity-log-alerts-with-resource-manager-template.md)
* Revisión del [esquema de webhook de alertas del registro de actividad](monitoring-activity-log-alerts-webhook.md)
* Obtenga más información acerca de las [alertas de métricas más recientes](monitoring-near-real-time-metric-alerts.md)
* Más información sobre las [notificaciones del servicio](monitoring-service-notifications.md)
* Más información sobre los [grupos de acciones](monitoring-action-groups.md)
* Configurar [alertas](monitor-alerts-unified-usage.md)
