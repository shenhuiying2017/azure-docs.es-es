---
title: "Introducción a la redirección para Azure Application Gateway| Microsoft Docs"
description: "Aprenda sobre la funcionalidad de redirección de Azure Application Gateway"
services: application-gateway
documentationcenter: na
author: amsriva
manager: timlt
editor: 
tags: azure-resource-manager
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/18/2017
ms.author: amsriva
ms.openlocfilehash: ea9ae8373ff67bf9557b06bbc8a4b0d82a03e2d0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-redirect-overview"></a>Introducción a la redirección de Application Gateway

Resulta un escenario común para muchas aplicaciones web el admitir la redirección automática de HTTP a HTTPS para asegurarse de que toda la comunicación entre la aplicación y sus usuarios se produce a través de una ruta de acceso cifrada. En el pasado, los clientes usaban técnicas como la creación de un grupo back-end dedicado cuya única finalidad era redirigir las solicitudes que recibía de HTTP a HTTPS.  La puerta de enlace de aplicaciones admite ahora la capacidad de redirigir el tráfico en Application Gateway. Esto simplifica la configuración de la aplicación, optimiza el uso de recursos y admite nuevos escenarios de redirección incluida la global y la basada en la ruta de acceso. La compatibilidad con la redirección de Application Gateway no se limita solo a la redirección de HTTP a HTTPS. Se trata de un mecanismo de redirección genérico, que permite la redirección del tráfico recibido en un agente de escucha a otro agente de escucha en Application Gateway. También admite la redirección a un sitio externo. La compatibilidad con la redirección de Application Gateway ofrece las siguientes funcionalidades:

1. Redirección global desde un agente de escucha a otro en la puerta de enlace. Permite la redirección de HTTP a HTTPS en un sitio.
2. Redirección basada en la ruta de acceso. Este tipo de redirección permite la redirección de HTTP a HTTPS solo en un área de un sitio específico, por ejemplo un área de carro de la compra que se indica mediante /carro/*.
3. Redirección a un sitio externo.

![redirección](./media/application-gateway-redirect-overview/redirect.png)

Con este cambio, los clientes tendrían que crear un nuevo objeto de configuración de redirección, que especifique el agente de escucha de destino o el sitio externo al que se desea dirigir la redirección. El elemento de configuración también admite opciones para anexar la cadena de consulta y la ruta de acceso URI para la dirección URL redirigida. Los clientes también pueden elegir si la redirección es un archivo temporal (código de estado HTTP 302) o a una redirección permanente (código de estado HTTP 301). Una vez creada esta configuración de redirección, se adjunta al agente de escucha de origen a través de una nueva regla. Cuando se usa una regla básica, la configuración de redirección se asocia a un agente de escucha de origen y es una redirección global. Cuando se utiliza una regla basada en la ruta de acceso, la configuración de redirección se define en la asignación de ruta de acceso de direcciones URL y, por tanto, solo se aplica al área de rutas de acceso específica de un sitio.

### <a name="next-steps"></a>Pasos siguientes

[Configuración de la redirección de direcciones URL en una puerta de enlace de aplicaciones](application-gateway-configure-redirect-powershell.md)
