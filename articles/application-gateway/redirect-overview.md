---
title: Introducción al redireccionamiento para Azure Application Gateway
description: Aprenda sobre la funcionalidad de redirección de Azure Application Gateway
services: application-gateway
documentationcenter: na
author: amsriva
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 3/19/2018
ms.author: amsriva
ms.openlocfilehash: 65c631ca9beb5eab5d8fe2b7e71daa0cf3b768fa
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/04/2018
ms.locfileid: "33204384"
---
# <a name="application-gateway-redirect-overview"></a>Introducción a la redirección de Application Gateway

Resulta un escenario común para muchas aplicaciones web el admitir la redirección automática de HTTP a HTTPS para asegurarse de que toda la comunicación entre la aplicación y sus usuarios se produce a través de una ruta de acceso cifrada. En el pasado, los clientes usaban técnicas como la creación de un grupo back-end dedicado cuya única finalidad era redirigir las solicitudes que recibía de HTTP a HTTPS.

Application Gateway admite ahora la capacidad de redirigir el tráfico en la puerta de enlace. Esto simplifica la configuración de la aplicación, optimiza el uso de recursos y admite nuevos escenarios de redirección incluida la global y la basada en la ruta de acceso. La compatibilidad con la redirección de Application Gateway no se limita solo a la redirección de HTTP a HTTPS. Cuenta con un mecanismo de redireccionamiento genérico, que permite el redireccionamiento del tráfico recibido en un agente de escucha a otro agente de escucha en Application Gateway. También se admite el redireccionamiento de sitios externos.

La compatibilidad con la redirección de Application Gateway ofrece las siguientes funcionalidades:

-  **Redireccionamiento global**

   Redirecciona desde un agente de escucha a otro en la puerta de enlace. Permite la redirección de HTTP a HTTPS en un sitio.
- **Redireccionamiento basado en la ruta de acceso**

   Este tipo de redirección permite la redirección de HTTP a HTTPS solo en un área de un sitio específico, por ejemplo un área de carro de la compra que se indica mediante /carro/*.
- **Redirección a un sitio externo**

![redirección](./media/redirect-overview/redirect.png)

Con este cambio, los clientes tienen que crear un nuevo objeto de configuración de redireccionamiento, que especifique el agente de escucha de destino o el sitio externo al que se desea dirigir el redireccionamiento. El elemento de configuración también admite opciones para anexar la cadena de consulta y la ruta de acceso URI para la dirección URL redirigida. También se puede elegir si el redireccionamiento es temporal (código de estado HTTP 302) o permanente (código de estado HTTP 301). Una vez creada esta configuración de redireccionamiento, se adjunta al agente de escucha de origen a través de una nueva regla. Cuando se usa una regla básica, la configuración de redirección se asocia a un agente de escucha de origen y es una redirección global. Cuando se utiliza una regla basada en rutas de acceso, la configuración de redireccionamiento se define en el mapa de rutas de acceso de dirección URL. Por lo tanto, solo se aplica al área específica de la ruta de acceso de un sitio.

### <a name="next-steps"></a>Pasos siguientes

[Configuración de la redirección de direcciones URL en una puerta de enlace de aplicaciones](tutorial-url-redirect-powershell.md)
