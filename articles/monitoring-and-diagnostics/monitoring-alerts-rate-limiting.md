---
title: "Limitación de número de SMS, correos electrónicos y webhooks | Microsoft Docs"
description: "Reciba notificaciones por SMS, webhook y correo electrónico cuando se produzcan determinados eventos en el registro de actividad."
author: anirudhcavale
manager: carmonm
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
ms.translationtype: Human Translation
ms.sourcegitcommit: 64bd7f356673b385581c8060b17cba721d0cf8e3
ms.openlocfilehash: 1b4196d3b1d41458c7dd20b6986cc09100ae318c
ms.contentlocale: es-es
ms.lasthandoff: 05/02/2017


---

# <a name="rate-limiting-for-sms-emails-and-webhooks"></a>Limitación de número de SMS, correos electrónicos y webhooks
La limitación de número supone una suspensión de las notificaciones que se produce cuando se envían demasiadas a un número de teléfono o correo electrónico determinado. Esta limitación de número garantiza que las comunicaciones relacionadas con las alertas del registro de actividad y las de estado del servicio son manejables y se puede actuar sobre ellas.

Las reglas para SMS y correo electrónico son las mismas. El umbral de la limitación de número para
 - SMS: 10 mensajes en una hora
 - Correo electrónico: 100 mensajes en una hora

## <a name="rate-limit-rules"></a>Reglas de limitación de número
- Un número de teléfono particular o correo electrónico están limitados cuando recibe más notificaciones que las especificadas en el umbral.
- Un número de teléfono o correo electrónico puede formar parte de grupos de acciones de muchas suscripciones. La limitación de número se aplica en todas las suscripciones, es decir, se aplica en cuanto se alcanza el umbral, incluso si las notificaciones proceden de varias suscripciones.  
- Cuando un número de teléfono o correo electrónico están limitados, se envía un mensaje adicional del mismo tipo para comunicar la limitación de número. El SMS o correo electrónico indica cuándo expira la limitación de número.

## <a name="rate-limit-of-webhooks"></a>Limitación de número para los webhooks ##
Actualmente, no hay ninguna limitación en vigor para webhooks.

## <a name="next-steps"></a>Pasos siguientes ##
Más información sobre el [comportamiento de alertas por SMS](monitoring-sms-alert-behavior.md)  
Obtener una [introducción a las alertas del registro de actividad](monitoring-overview-alerts.md) y aprender cómo puede recibir alertas  
Cómo [configurar alertas siempre que se publique una notificación de estado de un servicio](monitoring-activity-log-alerts-on-service-notifications.md)

