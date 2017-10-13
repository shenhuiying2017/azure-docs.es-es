---
title: "Introducción a los servidores back-end multiinquilino con Azure Application Gateway | Microsoft Docs"
description: "En esta página se proporciona una introducción a la compatibilidad de Application Gateway con los servidores back-end multiinquilino."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: c29ff60a50e68c75b4e8f62713d6d1fffd2123d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="application-gateway-support-for-multi-tenant-back-ends"></a>Compatibilidad de Application Gateway con servidores back-end multiinquilino

Azure Application Gateway admite conjuntos de escalado de máquinas virtuales, interfaces de red, IP públicas/privadas o nombres de dominio completos (FQDN) como parte de sus grupos de servidores back-end. De forma predeterminada, la puerta de enlace de aplicaciones no cambia el encabezado del host HTTP de entrada del cliente y lo envía sin alterar al back-end. Hay muchos servicios, como [Azure Web Apps](../app-service/app-service-web-overview.md) y [API Management](../api-management/api-management-key-concepts.md) que son multiinquilino por naturaleza y dependen de un encabezado de host específico o de una extensión SNI para resolver el punto de conexión correcto. Application Gateway admite ahora la posibilidad de que los usuarios sobrescriban el encabezado del host HTTP de entrada en función de la configuración de HTTP del back-end. Esta funcionalidad permite la compatibilidad con servidores back-end multiinquilino, Azure Web Apps y API Management. Esta funcionalidad está disponible para las SWU estándar y WAF. La compatibilidad con servidores back-end multiinquilino también funciona con escenarios de terminación de SSL y de SSL de extremo a extremo.

![escenario de aplicación web](./media/application-gateway-web-app-overview/scenario.png)

La posibilidad de especificar una invalidación del host se define en la configuración de HTTP y se puede aplicar a cualquier grupo de servidores back-end durante la creación de reglas. Los servidores back-end multiinquilino admiten las dos formas siguientes de invalidar el encabezado de host y la extensión SNI.

1. La posibilidad de establecer el nombre de host en un valor fijo en la configuración de HTTP. Esta funcionalidad garantiza que el encabezado de host se invalida con este valor para todo el tráfico que va al grupo de servidores back-end donde se aplica la configuración de HTTP. Al usar SSL de extremo a extremo, este nombre de host invalidado se usa en la extensión SNI. Esta funcionalidad permite escenarios donde un grupo de servidores back-end espera un encabezado de host que es diferente del encabezado de host del cliente de entrada.

2. La posibilidad de obtener el nombre de host de la dirección IP o FQDN de los miembros del grupo de servidores back-end. La configuración de HTTP también proporciona una opción para seleccionar el nombre de host del FQDN de un miembro del grupo de servidores back-end si está configurado con esta opción. Al usar SSL de extremo a extremo, este nombre de host se obtiene del FQDN y se usa en la extensión SNI. Esta funcionalidad permite escenarios donde un grupo de servidores back-end puede tener dos o más servicios PaaS multiinquilino, como Azure Web Apps y el encabezado de host de la solicitud para que cada miembro contenga el nombre de host obtenido de su FQDN.

> [!NOTE]
> En los dos casos anteriores, la configuración solo afecta al comportamiento del tráfico dinámico y no al del sondeo de estado. Los sondeos personalizados ya admiten la posibilidad de especificar un encabezado de host en la configuración de sondeo. También admiten ahora la posibilidad de obtener el comportamiento del encabezado de host de la configuración de HTTP actualmente configurada. Esta configuración puede especificarse mediante el parámetro `PickHostNameFromback endAddress` en la configuración de sondeo. Para que la funcionalidad de extremo a extremo funcione, el sondeo y la configuración de HTTP se deben modificar para reflejar la configuración correcta.

Con esta funcionalidad, los clientes especifican las opciones en la configuración de HTTP y los sondeos personalizados para la configuración adecuada. Esta configuración se asocia luego a un agente de escucha y un grupo de servidores back-end mediante una regla.

## <a name="next-steps"></a>Pasos siguientes

Para aprender a configurar una puerta de enlace de aplicaciones con una aplicación web como miembro del grupo de servidores back-end, visite [Configuración de App Service Web Apps con Application Gateway](application-gateway-web-app-powershell.md).
