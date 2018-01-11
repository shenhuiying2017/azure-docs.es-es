---
title: "Limitación del número de SMS, correos electrónicos, notificaciones push de App de Azure y webhooks | Microsoft Docs"
description: "Conozca cómo limita Azure el número de notificaciones de webhook o notificaciones push de App de Azure, mensajes de correo electrónico o SMS posibles de un grupo de acciones."
author: dukek
manager: chrad
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/8/2017
ms.author: dukek
ms.openlocfilehash: c76bf5cf51f18a32b33060d528c64d119e31dbbd
ms.sourcegitcommit: 42ee5ea09d9684ed7a71e7974ceb141d525361c9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2017
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitación del número de SMS, correos electrónicos, notificaciones push de App de Azure y publicaciones de webhooks
La limitación de número supone una suspensión de las notificaciones que se produce cuando se envían demasiadas a un número de teléfono, dirección de correo electrónico o dispositivo determinados. La limitación del índice asegura que las alertas son fáciles de administrar y procesar.

Los umbrales del límite de número son:

 - **SMS**: no más de 1 SMS cada 5 minutos.
 - **Correo electrónico**: 100 mensajes en una hora.
 - **Notificaciones push de App de Azure**: no hay ninguna limitación de número para las notificaciones push.
 - **Webhooks**: actualmente, no hay ninguna limitación sobre la cantidad de webhooks.

## <a name="rate-limit-rules"></a>Reglas de limitación de número
- Un número de teléfono o correo electrónico particular se limitan cuando reciben más mensajes de los que permite el umbral.
- Un número de teléfono o correo electrónico puede formar parte de grupos de acciones de muchas suscripciones. La limitación de número se aplica en todas las suscripciones. En cuanto se alcanza el umbral, se aplica incluso si se envían los mensajes desde varias suscripciones.  
- Cuando una dirección de correo electrónico está limitada, se envía una notificación adicional del mismo tipo para comunicar la limitación. La notificación indica cuándo expira la limitación.

## <a name="next-steps"></a>Pasos siguientes ##
* Más información sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
