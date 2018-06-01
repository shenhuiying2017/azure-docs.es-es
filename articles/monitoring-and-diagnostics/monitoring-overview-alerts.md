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
ms.date: 05/15/2018
ms.author: robb
ms.openlocfilehash: 039ab7226b4ea7e011e1c0cbb4cac528b5237483
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34204012"
---
# <a name="what-are-classic-alerts-in-microsoft-azure"></a>¿Qué son las alertas clásicas en Microsoft Azure?

> [!NOTE]
> En este artículo se describe cómo crear alertas de métrica clásicas más antiguas. Azure Monitor ahora es compatible con [una nueva experiencia de alertas y las más recientes alertas de métrica casi en tiempo real](monitoring-overview-unified-alerts.md). 
>

Las alertas le permiten configurar las condiciones en los datos y recibir notificaciones cuando las condiciones coinciden con los datos de supervisión más actualizados.


## <a name="alerts-on-azure-monitor-data"></a>Alertas sobre datos de Azure Monitor
Hay dos tipos de alertas clásicas disponibles: alertas de métricas y alertas de registro de actividad.

* **Alertas de métricas clásicas**: la alerta se desencadena cuando el valor de una métrica específica cruza un umbral que se le haya asignado. La alerta genera una notificación cuando la alerta está "Activada" (si se sobrepasa el umbral y se cumple la condición de alerta), así como cuando queda en estado "Resuelto" (cuando se vuelve a sobrepasa el umbral y ya no se cumple la condición). Para las alertas de métricas más recientes, consulte la información siguiente.

* **Alertas clásicas de registro de actividad**: una alerta de registro de streaming que se desencadena cuando se genera un evento de registro de actividad que coincide con los criterios de filtro que se han asignado. Estas alertas tienen solo un estado "Activado", puesto que el motor de alertas simplemente aplica los criterios de filtro a cualquier evento nuevo. Se pueden utilizar estas alertas para recibir notificaciones cuando se produzca un incidente de Service Health o cuando un usuario o una aplicación realiza una operación en su suscripción, por ejemplo, "Eliminar máquina virtual".

Para los datos de registro de diagnóstico disponibles a través de Azure Monitor, se recomienda enrutar los datos a Log Analytics (anteriormente, OMS) y usar una alerta de Log Analytics. Log Analytics utiliza ahora el [nuevo método de alertas](monitoring-overview-unified-alerts.md) 

El diagrama siguiente resume los orígenes de datos en Azure Monitor y, de forma conceptual, la manera en la que se puede se puede alertar sobre esos datos.

![Alertas explicadas](./media/monitoring-overview-alerts/Alerts_Overview_Resource_v4.png)

## <a name="taxonomy-of-azure-monitor-alerts-classic"></a>Taxonomía de las alertas (clásicas) de Azure Monitor
Azure utiliza los siguientes términos para describir las alertas clásicas y sus funciones:
* **Alerta**: una definición de criterios (una o más reglas o condiciones) que se activa cuando se cumplen.
* **Activo**: estado en el que se cumplen los criterios definidos por una alerta clásica.
* **Resuelto**: estado en el que ya no se cumplen los criterios definidos por una alerta clásica, después de que se cumplieran con anterioridad.
* **Notificación**: acción que se realiza a partir de la activación de una alerta clásica.
* **Acción**: una llamada específica que se envían al receptor de una notificación (por ejemplo, envío de un correo electrónico a una dirección o una publicación en una dirección URL de webhook). Las notificaciones normalmente pueden desencadenar varias acciones.

## <a name="how-do-i-receive-a-notification-from-an-azure-monitor-classic-alert"></a>¿Cómo se puede recibir una notificación de una alerta clásica de Azure Monitor?
Históricamente, las alertas de Azure de distintos servicios usan sus propios métodos de notificación integrados. 

Azure Monitor ofrece ahora una agrupación de notificación reutilizable denominada *Grupos de acciones*. Los grupos de acciones especifican un conjunto de receptores para una notificación y, cada vez que se activa una alerta que hace referencia al Grupo de acciones, todos los receptores reciben esa notificación. Esto permite reutilizar una agrupación de receptores (por ejemplo, la lista de ingenieros de guardia) a través de muchos objetos de alerta. Los grupos de acciones admiten la notificación mediante la publicación en una URL de webhook, además de direcciones de correo electrónico, números de SMS y otras acciones.  Para más información, vea [Grupos de acciones](monitoring-action-groups.md). 

Las anteriores alertas de registro de actividad clásicas usan grupos de acciones.

Pero las anteriores alertas de métricas aún no los utilizan. En cambio, se pueden configurar las siguientes acciones: 
* Enviar notificaciones de correo electrónico al administrador de servicios o a los coadministradores, o a las direcciones de correo electrónico adicionales que especifique.
* Llamar a un webhook, que permite iniciar acciones de automatización adicionales.

Los webhooks permiten la automatización y corrección, por ejemplo, mediante:
    - Runbook de Azure Automation
    - Función de Azure
    - Aplicación lógica de Azure
    - un servicio de terceros

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
* Más información sobre los [grupos de acciones](monitoring-action-groups.md)
* Configuración de [alertas más recientes](monitor-alerts-unified-usage.md)
