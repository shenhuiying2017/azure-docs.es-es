---
title: "Limitación del número de SMS, correos electrónicos y webhooks | Microsoft Docs"
description: "Conozca cómo limita Azure el número de notificaciones de webhook, mensajes de correo electrónico o SMS posibles desde un grupo de acciones."
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
ms.openlocfilehash: bde645624ab1860d19ba18470f55845855a7d1fb
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="rate-limiting-for-sms-messages-emails-and-webhook-posts"></a>Limitación del número de mensajes SMS, correo electrónico y envíos webhook
La limitación de número supone una suspensión de las notificaciones que se produce cuando se envían demasiadas a un número de teléfono o dirección de correo electrónico determinados. La limitación del índice asegura que las alertas son fáciles de administrar y procesar.

Las reglas para SMS y correo electrónico son las mismas. El umbral de la limitación de número es:

 - **SMS**: 10 mensajes en una hora.
 - **Correo electrónico**: 100 mensajes en una hora.

## <a name="rate-limit-rules"></a>Reglas de limitación de número
- Un número de teléfono o correo electrónico particular se limitan cuando reciben más mensajes de los que permite el umbral.
- Un número de teléfono o correo electrónico puede formar parte de grupos de acciones de muchas suscripciones. La limitación de número se aplica en todas las suscripciones. En cuanto se alcanza el umbral, se aplica incluso si se envían los mensajes desde varias suscripciones.  
- Cuando un número de teléfono o un correo electrónico están limitados, se envía una notificación adicional del mismo tipo para comunicar la limitación. La notificación indica cuándo expira la limitación.

## <a name="rate-limit-of-webhooks"></a>Limitación de número para los webhooks ##
Actualmente, no hay ninguna limitación en vigor para webhooks.

## <a name="next-steps"></a>Pasos siguientes ##
* Más información sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
