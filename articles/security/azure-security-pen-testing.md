---
title: "Pruebas de penetración | Microsoft Docs"
description: "El artículo proporciona información general sobre el proceso de pruebas de penetración y cómo realizar dichas pruebas en sus aplicaciones que se ejecutan en la infraestructura de Azure."
services: security
documentationcenter: na
author: YuriDio
manager: swadhwa
editor: TomSh
ms.assetid: 695d918c-a9ac-4eba-8692-af4526734ccc
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/08/2017
ms.author: yurid
ms.openlocfilehash: dd5c874406ec184a2526a9eb0843cd6f3b6b3aa1
ms.sourcegitcommit: 094061b19b0a707eace42ae47f39d7a666364d58
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/08/2017
---
# <a name="pen-testing"></a>Pruebas de penetración
Una de las ventajas del uso de Azure para las pruebas y la implementación de aplicaciones es que puede crear entornos rápidamente.  No tiene que preocuparse del pedido, la adquisición, la "instalación en bastidor y apilamiento" de su propio hardware local.

Esto es genial, pero debe asegurarse de realizar sus diligencias de seguridad normales. Una de las cosas que debe hacer es realizar pruebas de penetración de las aplicaciones que implemente en Azure.

Es posible que ya sepa que realiza Microsoft realiza [pruebas de penetración de nuestro entorno de Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Esto permite impulsar las mejoras de Azure. 

No realizamos pruebas de penetración de su aplicación, pero sabemos que se desea y necesita realizar dichas pruebas en sus propias aplicaciones. Eso es bueno, ya que al mejorar la seguridad de sus aplicaciones, ayuda a hacer que todo el ecosistema de Azure sea más seguro.

¿Qué debe hacer?

Desde el 15 de junio de 2017, Microsoft ya no requiere la aprobación previa para llevar a cabo pruebas de penetración con recursos de Azure. Se recomienda a los clientes que quieran documentar formalmente los compromisos de futuras pruebas de penetración con Microsoft Azure que completen el [formulario de notificación de pruebas de penetración de servicios de Azure](https://portal.msrc.microsoft.com/engage/pentest). Este proceso solo está relacionado con Microsoft Azure y no es aplicable ningún otro servicio de Microsoft Cloud. 

>[!IMPORTANT] 
>Aunque notificar a Microsoft de las actividades de pruebas de penetración ya no es obligatorio, los clientes deben cumplir las [reglas de compromiso de las pruebas de penetración unificadas de Microsoft Cloud](https://technet.microsoft.com/en-us/mt784683) de todos modos. 

Entre las pruebas estándar que puede realizar se incluyen:

* Pruebas en los puntos de conexión para descubrir las [10 principales vulnerabilidades del Proyecto de seguridad de aplicación web abierta (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Pruebas de vulnerabilidad ante datos aleatorios o inesperados](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de los puntos de conexión
* [Exploración de puertos](https://en.wikipedia.org/wiki/Port_scanner) de los puntos de conexión

Un tipo de prueba que no puede realizar es ningún tipo de ataque [de denegación de servicio (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Esto incluye iniciar un ataque de denegación de servicio o la realización de pruebas relacionadas que puedan determinar, demostrar o simular cualquier tipo de ataque de denegación de servicio.

## <a name="next-steps"></a>Pasos siguientes

- ¿Está listo para empezar a realizar pruebas de penetración de sus aplicaciones hospedadas en Microsoft Azure? Si es así, diríjase a la página [Penetration Test Overview](https://technet.microsoft.com/library/mt784683.aspx) (Información general de pruebas de penetración) y haga clic en el botón Create a Testing Request (Crear una solicitud de prueba) en la parte inferior de la página. 