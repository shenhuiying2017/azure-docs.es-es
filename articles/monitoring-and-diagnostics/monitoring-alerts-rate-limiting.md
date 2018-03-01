---
title: "Limitación del número de SMS, correos electrónicos, notificaciones push de App de Azure y webhooks | Microsoft Docs"
description: "Conozca cómo limita Azure el número de notificaciones de webhook o notificaciones push de App de Azure, mensajes de correo electrónico o SMS posibles de un grupo de acciones."
author: dkamstra
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
ms.date: 2/16/2018
ms.author: dukek
ms.openlocfilehash: a4f97cc79945d266edd0af577e37fc9da2aa97bf
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="rate-limiting-for-sms-messages-emails-azure-app-push-notifications-and-webhook-posts"></a>Limitación del número de SMS, correos electrónicos, notificaciones push de App de Azure y publicaciones de webhooks
La limitación de número supone una suspensión de las notificaciones que se produce cuando se envían demasiadas a un número de teléfono, dirección de correo electrónico o dispositivo determinados. La limitación del índice asegura que las alertas son fáciles de administrar y procesar.

Los umbrales del límite de número son:

 - **SMS**: no más de 1 SMS cada 5 minutos.
 - **Correo electrónico**: no más de 100 mensajes de correo electrónico en una hora.
 
 Otras acciones no tienen velocidad limitada.

## <a name="rate-limit-rules"></a>Reglas de limitación de número
- Un número de teléfono o correo electrónico particular se limitan cuando reciben más mensajes de los que permite el umbral.
- Un número de teléfono o correo electrónico puede formar parte de grupos de acciones de muchas suscripciones. La limitación de número se aplica en todas las suscripciones. En cuanto se alcanza el umbral, se aplica incluso si se envían los mensajes desde varias suscripciones.
- Cuando una dirección de correo electrónico está limitada, se envía una notificación adicional del mismo tipo para comunicar la limitación. La notificación indica cuándo expira la limitación.

## <a name="next-steps"></a>pasos siguientes ##
* Más información sobre el [comportamiento de las alertas por SMS](monitoring-sms-alert-behavior.md).
* Consulte la [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprenda cómo puede recibir alertas.  
* Aprenda a [configurar alertas siempre que se publique una notificación de mantenimiento de un servicio](monitoring-activity-log-alerts-on-service-notifications.md).
