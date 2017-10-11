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
ms.date: 08/24/2017
ms.author: yurid
ms.openlocfilehash: 070e848f753452953b9e5dfe94799e7c0a314530
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="pen-testing"></a>Pruebas de penetración
Una de las grandes ventajas de usar Microsoft Azure para la prueba e implementación de aplicaciones es que no es necesario crear una infraestructura local para desarrollar, probar e implementar sus aplicaciones. Sos servicios de plataforma Microsoft Azure se ocupan de toda la infraestructura. No tiene que preocuparse del pedido, la adquisición, la "instalación en bastidor y apilamiento" de su propio hardware local.

Esto es genial, pero debe asegurarse de realizar sus diligencias de seguridad normales. Una de las cosas que debe hacer es realizar pruebas de penetración de las aplicaciones que implemente en Azure.

Es posible que ya sepa que realiza Microsoft realiza [pruebas de penetración de nuestro entorno de Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Esto nos ayuda a mejorar nuestra plataforma y guía nuestras acciones para mejorar los controles de seguridad, introducir nuevos controles de seguridad y mejorar nuestros procesos de seguridad.

No realizamos pruebas de penetración de su aplicación, pero sabemos que se desea y necesita realizar dichas pruebas en sus propias aplicaciones. Eso es bueno, ya que al mejorar la seguridad de sus aplicaciones, ayuda a hacer que todo el ecosistema de Azure sea más seguro.

Al realizar pruebas de penetración de sus aplicaciones, podría parecer que nos ataca. Nosotros [supervisamos continuamente](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) para detectar patrones de ataque e iniciamos un proceso de respuesta a incidentes si es necesario. No ayuda a nadie si se desencadena una respuesta a incidentes debido a sus propias pruebas de penetración.

¿Qué debe hacer?

Cuando esté listo para realizar pruebas de penetración de sus aplicaciones hospedadas en Azure, tiene la opción de [hacérnoslo saber](https://portal.msrc.microsoft.com/en-us/engage/pentest). Una vez que sepamos que va a realizar pruebas específicas, no le des conectaremos accidentalmente (por ejemplo, al bloquear la dirección IP desde la que está realizando la prueba), siempre y cuando las pruebas se ajusten a los términos y condiciones de las pruebas de penetración de Azure que se describen en [Reglas de interacción de prueba de penetración de Microsoft Cloud Unified](https://technet.microsoft.com/en-us/mt784683).
Entre las pruebas estándar que puede realizar se incluyen:

* Pruebas en los puntos de conexión para descubrir las [10 principales vulnerabilidades del Proyecto de seguridad de aplicación web abierta (OWASP)](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
* [Pruebas de vulnerabilidad ante datos aleatorios o inesperados](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) de los puntos de conexión
* [Exploración de puertos](https://en.wikipedia.org/wiki/Port_scanner) de los puntos de conexión

Un tipo de prueba que no puede realizar es ningún tipo de ataque [de denegación de servicio (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Esto incluye iniciar un ataque de denegación de servicio o la realización de pruebas relacionadas que puedan determinar, demostrar o simular cualquier tipo de ataque de denegación de servicio.

¿Está listo para empezar a realizar pruebas de penetración de sus aplicaciones hospedadas en Microsoft Azure? Si es así, diríjase a la página [Penetration Test Overview](https://technet.microsoft.com/library/mt784683.aspx) (Información general de pruebas de penetración) y haga clic en el botón Create a Testing Request (Crear una solicitud de prueba) en la parte inferior de la página. También encontrará más información sobre los términos y condiciones de las pruebas de penetración, así como vínculos útiles sobre cómo informar de problemas de seguridad relacionados con Azure o cualquier otro servicio de Microsoft.
