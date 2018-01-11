---
title: Comportamiento de las alertas por SMS en los grupos de acciones | Microsoft Docs
description: "Formato de mensajes SMS y respuesta a los mensajes SMS de cancelación de suscripción, segunda suscripción o solicitud de ayuda."
author: anirudhcavale
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: ancav
ms.openlocfilehash: 012f001356463a8a7d9b95f186111959627f2c28
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="sms-alert-behavior-in-action-groups"></a>Comportamiento de las alertas por SMS en los grupos de acciones
## <a name="overview"></a>Información general ##
Los grupos de acciones permiten configurar una lista de destinatarios. Luego se pueden aprovechar estos grupos al definir alertas de registro de actividad, lo que garantiza que cuando se activa una alerta de registro de actividad, un grupo de acciones concreto recibe la notificación. Uno de los mecanismos de alerta que se admiten es el SMS; las alertas admiten la comunicación bidireccional. Un usuario puede responder a una alerta para:

- **Cancelar la suscripción a alertas:** un usuario puede cancelar la suscripción a todas las alertas por SMS para todos los grupos de acciones o para un grupo de acciones singular.  
- **Volver a suscribirse a alertas:** un usuario puede volver a suscribirse a todas las alertas por SMS para todos los grupos de acciones o para un grupo de acciones singular.  
- **Solicitar ayuda:** un usuario puede pedir más información acerca del SMS. Se le redirigirá a este artículo

En este artículo se trata el comportamiento de las alertas por SMS y las acciones de respuesta que el usuario puede realizar en función de su configuración regional:

## <a name="usacanada-sms-behavior"></a>Comportamiento de SMS en Estados Unidos y Canadá
### <a name="receiving-an-sms-alert"></a>Recepción de una alerta por SMS
Un receptor de SMS, que se configura como parte de un grupo de acciones, recibirá un SMS cuando se desencadene una alerta. El SMS contendrá la siguiente información:
* El nombre corto del grupo de acciones al que se envió esta alerta
- El título de la alerta

### <a name="unsubscribing-from-sms-alerts-for-one-action-group"></a>Cancelación de la suscripción a alertas por SMS para un grupo de acciones
Para que un usuario cancele la suscripción a los SMS para las alertas de un grupo de acciones, debe responder al código corto 29873 con las palabras clave: "DISABLE &lt;Nombre corto del grupo de acciones&gt;".

Ejemplo: Un usuario que desea cancelar la suscripción a las alertas de un grupo de acciones con el nombre corto "Azure", tendría que enviar el siguiente SMS al código corto 29873: "DISABLE Azure"

### <a name="unsubscribing-from-sms-alerts-for-all-action-groups"></a>Cancelación de la suscripción a alertas por SMS para todos los grupos de acciones
Un usuario puede cancelar la suscripción a todas las alertas por SMS para todos los grupos de acción respondiendo al código corto 29873 con cualquiera de las siguientes palabras clave:
* STOP

Ejemplo: Un usuario que desea cancelar la suscripción a todas las alertas por SMS para todos los grupos tendría que enviar el siguiente SMS al código corto 29873: "STOP"

>[!NOTE]
>Si un usuario ha cancelado la suscripción a las alertas por SMS, pero luego se agrega a un nuevo grupo de acciones; SE recibirán alertas por SMS para el grupo de acciones nuevo, pero permanecerán sin suscripción de todos los grupos de acciones anteriores.
>
>

### <a name="resubscribing-to-sms-alerts-for-one-action-group"></a>Segunda suscripción a alertas por SMS para un grupo de acciones
Para que un usuario vuelva a suscribirse a los SMS para las alertas de un grupo de acciones, debe responder al código corto 29873 con las palabras clave: "ENABLE &lt;Nombre corto del grupo de acciones&gt;".

Ejemplo: Un usuario que desea volver a suscribirse a las alertas de un grupo de acciones con el nombre corto "Azure", tendría que enviar el siguiente SMS al código corto 29873: "ENABLE Azure"

### <a name="resubscribing-to-sms-alerts-for-all-action-groups"></a>Segunda suscripción a alertas por SMS para todos los grupo de acciones
Un usuario puede volver a suscribirse a todas las alertas por SMS para todos los grupos de acción respondiendo al código corto 29873 con cualquiera de las siguientes palabras clave:

* START

Ejemplo: Un usuario que desea volver a suscribirse a todas las alertas por SMS para todos los grupos tendría que enviar el siguiente SMS al código corto 29873: "STOP"

### <a name="requesting-help-via-sms"></a>Solicitud de ayuda a través de SMS
Un usuario puede solicitar más información acerca de SMS que ha recibido. Para ello, debe responder al código corto 29873 con cualquiera de las siguientes palabras clave:
* HELP

Se enviará una respuesta al usuario con un vínculo a este artículo.

## <a name="next-steps"></a>Pasos siguientes
Obtener una [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprender cómo puede recibir alertas  
Más información sobre la [limitación de la tasa de SMS](monitoring-alerts-rate-limiting.md)  
Más información sobre los [grupos de acciones](monitoring-action-groups.md)
