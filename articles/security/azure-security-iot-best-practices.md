---
title: Procedimientos recomendados de seguridad de Internet de las cosas | Microsoft Docs
description: "El artículo proporciona una lista exclusiva de los procedimientos recomendados de seguridad de Internet de las cosas y recomendaciones generales."
services: security
documentationcenter: na
author: TomShinder
manager: StevenPo
editor: TomSh
ms.assetid: 2d5598c5-4c30-481d-b8f4-51ee024ea9a7
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/21/2017
ms.author: yurid
ms.openlocfilehash: 8937437652a78e05b94574cb9fe5df7962edb1ad
ms.sourcegitcommit: 62eaa376437687de4ef2e325ac3d7e195d158f9f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2017
---
# <a name="internet-of-things-security-best-practices"></a>Procedimientos recomendados de seguridad de Internet de las cosas
La protección de la infraestructura de Internet de las cosas (IoT) es una tarea crítica para cualquier persona involucrada con soluciones de IoT. Debido al número de dispositivos implicados y la naturaleza distribuida de estos dispositivos, el impacto de un evento de seguridad relacionado con el compromiso de millones de dispositivos IoT no es trivial y puede tener un impacto masivo.

Por este motivo, la seguridad de IoT necesita un enfoque de seguridad en profundidad. Los datos deben estar seguros en la nube y cuando se mueven en redes públicas y privadas. Es necesario implementar métodos para aprovisionar de forma segura los propios dispositivos de IoT. Cada capa, desde el dispositivo, hasta la red y el back-end en la nube debe tener garantías de seguridad.

Los procedimientos recomendados de IoT se pueden clasificar de la siguiente manera:

* Integrador o fabricante del hardware de IoT
* Desarrollador de soluciones de IoT
* Implementador de soluciones de IoT
* Operador de soluciones de IoT

En este artículo se resumen los [Procedimientos recomendados de seguridad de Internet de las cosas](../iot-suite/iot-security-best-practices.md). Consulte ese artículo para obtener información más detallada.

## <a name="iot-hardware-manufacturer-or-integrator"></a>Integrador o fabricante del hardware de IoT
Siga las prácticas recomendadas que aparecen a continuación si es integrador o fabricante de hardware IoT:

* **Acotar el hardware a unos requisitos mínimos:**el diseño del hardware debe incluir únicamente las características mínimas necesarias para el funcionamiento del hardware. 
* **Crear hardware a prueba de manipulaciones:**mecanismo integrado para detectar la alteración física del hardware; por ejemplo, abrir la cubierta del dispositivo, quitar una parte del dispositivo, etc. 
* **Basarse en hardware seguro:**si [COGS](https://en.wikipedia.org/wiki/Cost_of_goods_sold) lo permite, cree características de seguridad como el almacenamiento seguro y cifrado y la funcionalidad de arranque basada en el Módulo de plataforma segura (TPM).
* **Proteger las actualizaciones**: la actualización de firmware durante el ciclo de vida del dispositivo es inevitable.

## <a name="iot-solution-developer"></a>Desarrollador de soluciones de IoT
Siga las prácticas recomendadas que aparecen a continuación si es desarrollador de soluciones de IoT:

* **Seguir una metodología de desarrollo de software seguro:**el desarrollo de software seguro exige pensar bien la seguridad desde el inicio del proyecto hasta su implementación y prueba.
* **Elegir software de código abierto con cuidado**: el software de código abierto ofrece una oportunidad para desarrollar soluciones con rapidez.
* **Integrar con cuidado**: muchos de los errores de seguridad del software existen en el límite de bibliotecas y API. 

## <a name="iot-solution-deployer"></a>Implementador de soluciones de IoT
Siga las prácticas recomendadas que aparecen a continuación si es implementador de soluciones de IoT:

* **Implementar hardware de forma segura**: las implementaciones de IoT pueden requerir la implementación de hardware en ubicaciones no seguras, por ejemplo, espacios públicos o configuraciones regionales no supervisadas.
* **Mantener seguras las claves de autenticación**: durante la implementación, cada dispositivo requiere identificadores de dispositivo y claves de autenticación asociadas generadas por el servicio en la nube. Mantenga seguras estas claves físicamente incluso después de la implementación. Un dispositivo malintencionado puede usar cualquier clave en peligro para su enmascaramiento como dispositivo existente.

## <a name="iot-solution-operator"></a>Operador de soluciones de IoT
Siga las prácticas recomendadas que aparecen a continuación si es operador de soluciones de IoT:

* **Mantener los sistemas actualizados:**asegúrese de que los sistemas operativos del dispositivo y todos sus controladores están actualizados a las versiones más recientes. 
* **Proteger contra la actividad malintencionada**: si el sistema operativo lo permite, incluya las últimas funcionalidades antivirus y antimalware en cada sistema operativo del dispositivo. 
* **Auditar con frecuencia**: auditar infraestructura IoT para los problemas relacionados con la seguridad es clave al responder a incidentes de seguridad.
* **Proteger físicamente la infraestructura IoT**: los peores ataques de seguridad contra la infraestructura IoT se inician mediante el acceso físico a los dispositivos.
* **Proteger credenciales en la nube**: es posible que las credenciales de autenticación en la nube usadas para configurar y poner en funcionamiento una implementación de IoT sean la forma más fácil de obtener acceso y poner en peligro a un sistema IoT. 

