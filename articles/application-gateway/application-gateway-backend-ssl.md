---
title: "Habilitación de SSL de extremo a extremo en Azure Application Gateway | Microsoft Docs"
description: "En esta página se proporciona información general sobre la compatibilidad de Application Gateway con el protocolo SSL de extremo a extremo."
documentationcenter: na
services: application-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: 3976399b-25ad-45eb-8eb3-fdb736a598c5
ms.service: application-gateway
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.custom: H1Hack27Feb2017
ms.workload: infrastructure-services
ms.date: 07/19/2017
ms.author: amsriva
ms.openlocfilehash: 689ee54dc1db2ea371b08270718278fd98c65bb5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-end-to-end-ssl-with-application-gateway"></a>Introducción a SSL de extremo a extremo con Application Gateway

Application Gateway es compatible con la terminación SSL en la puerta de enlace; después, el tráfico fluye normalmente sin cifrar a los servidores back-end. Esta característica permite a los servidores web liberarse de la costosa sobrecarga de cifrado y descifrado. Sin embargo, para algunos clientes, la comunicación sin cifrar en los servidores back-end no es una opción aceptable. Esta comunicación sin cifrar podría deberse a los requisitos de seguridad, a los requisitos de cumplimiento o a la posibilidad de que la aplicación solo acepte una conexión segura. Para tales aplicaciones, Application Gateway admite el cifrado SSL de un extremo a otro.

## <a name="overview"></a>Información general

El protocolo SSL de un extremo a otro permite transmitir de forma segura información confidencial cifrada al back-end, al mismo tiempo que se siguen aprovechando las ventajas del equilibrio de carga de capa 7 que proporciona Application Gateway. Algunas de estas características son la afinidad de la sesión basada en las cookies, el enrutamiento basado en direcciones URL, la compatibilidad para el enrutamiento basado en sitios o la capacidad para insertar encabezados X-Forwarded-*.

Cuando se configura con el modo de comunicación de SSL de un extremo a otro, Application Gateway finaliza las sesiones SSL en la puerta de enlace y descifra el tráfico de usuario. Después, aplica las reglas configuradas para seleccionar una instancia de grupo de back-end adecuada en la que enrutar el tráfico. Posteriormente, Application Gateway inicia una nueva conexión SSL al servidor back-end y vuelve a cifrar los datos mediante el certificado de clave pública del servidor back-end antes de transmitir la solicitud al back-end. El protocolo SSL de un extremo a otro se habilita estableciendo la configuración de protocolo de BackendHTTPSetting en Https, que, luego, se aplica a un grupo de back-end. Cada servidor back-end del grupo de back-end con SSL de extremo a extremo habilitado debe configurarse con un certificado para permitir la comunicación segura.

![Escenario de SSL de un extremo a otro][1]

En este ejemplo, las solicitudes mediante TLS1.2 se enrutan a los servidores back-end en Pool1 con SSL de un extremo a otro.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de un extremo a otro e incorporación a listas blancas de certificados

Application Gateway solo se comunica con instancias de back-end conocidas, que tienen en la lista blanca su certificado con Application Gateway. Para habilitar la creación de listas blancas de certificados, debe cargar la clave pública de los certificados de servidor back-end en Application Gateway (no el certificado raíz). Solo se permiten conexiones en back-ends conocidos y en la lista blanca. Los back-ends restantes producen un error de puerta de enlace. Los certificados autofirmados son para fines de prueba que y no se recomiendan para cargas de trabajo de producción. Para poder usar estos certificados, deben estar en la lista blanca con Application Gateway, tal y como se describe en los pasos anteriores.

## <a name="next-steps"></a>Pasos siguientes

Después de obtener información sobre SSL de extremo a extremo, vaya a la página sobre [habilitación de SSL de extremo a extremo en Application Gateway](application-gateway-end-to-end-ssl-powershell.md) para crear una puerta de enlace de aplicaciones mediante SSL de extremo a extremo.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png
