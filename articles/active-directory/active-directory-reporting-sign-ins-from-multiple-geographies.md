---
title: "Inicios de sesión desde varias ubicaciones geográficas"
description: "Informe que señala usuarios en los que dos inicios de sesión parecían originarse en distintas regiones y, por el tiempo transcurrido entre inicios de sesión, resultaba imposible que el usuario viajase entre dichas regiones."
services: active-directory
documentationcenter: 
author: SSalahAhmed
manager: gchander
editor: 
ms.assetid: 79259c8a-2388-4747-b41e-c07434ea9a02
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/04/2016
ms.author: saah;kenhoff
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 1de57f64692ade442f9ef8d1e3b587ffee35d7cf


---
# <a name="sign-ins-from-multiple-geographies"></a>Inicios de sesión desde varias ubicaciones geográficas
En este informe se incluyen inicios de sesión correctos de un usuario en los que parece que dos inicios de sesión se originaron desde distintas regiones y que, según el tiempo transcurrido entre ellos, parece imposible que el usuario haya viajado entre dichas regiones. Entre las posibles causas se incluyen las siguientes:

* El usuario comparte su contraseña con otros usuarios.
* El usuario usa un escritorio remoto para iniciar un navegador web para el inicio de sesión.
* Un hacker inició sesión en la cuenta de un usuario desde un país diferente.
* El usuario está utilizando un proxy o una VPN.
* El usuario ha iniciado sesión desde varios dispositivos al mismo tiempo, como un equipo de escritorio y un teléfono móvil, y la dirección IP del teléfono móvil es poco común.

Los resultados de este informe mostrarán los eventos de inicio de sesión correctos, así como el tiempo transcurrido entre ellos, las regiones de donde parecían provenir y el tiempo de viaje estimado entre dichas regiones. El tiempo de viaje mostrado constituye solo una estimación y puede diferir del tiempo de viaje real entre las ubicaciones.

![Inicios de sesión desde varias ubicaciones geográficas](./media/active-directory-reporting-sign-ins-from-multiple-geographies/signInsFromMultipleGeographies.PNG)




<!--HONumber=Nov16_HO3-->


