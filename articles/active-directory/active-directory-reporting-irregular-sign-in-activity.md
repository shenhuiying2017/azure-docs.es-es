---
title: Actividad de inicio de sesión irregular
description: Informe que incluye inicios de sesión que nuestros algoritmos de aprendizaje automático identificaron como anómalos.
services: active-directory
documentationcenter: ''
author: SSalahAhmed
manager: gchander
editor: ''

ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff

---
# Actividad de inicio de sesión irregular
Los inicios de sesión irregulares son aquellos que han identificado los algoritmos de aprendizaje automático, de acuerdo con una condición de "viaje imposible" combinada con una ubicación y un dispositivo de inicio de sesión anómalos. Esto puede indicar que un hacker ha inicio sesión con esta cuenta. Se enviará una notificación por correo electrónico a los administradores globales si encontramos 10 o más eventos de inicio de sesión anómalos dentro de un intervalo de 30 días o menos. Asegúrese de incluir aad-alerts-noreply@mail.windowsazure.com en la lista de remitentes seguros.

<!---HONumber=AcomDC_0309_2016-->