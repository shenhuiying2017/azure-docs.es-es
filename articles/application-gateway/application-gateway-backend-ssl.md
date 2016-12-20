---
title: "Habilitación de directiva de SSL y SSL de extremo a extremo en Application Gateway | Microsoft Docs"
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
ms.workload: infrastructure-services
ms.date: 11/10/2016
ms.author: amsriva
translationtype: Human Translation
ms.sourcegitcommit: 288c616c5698288c9fd467836125c1259d0418d7
ms.openlocfilehash: 51c7899d72fb06c44c52685927b1c5dc40ab4cd3


---
# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Habilitación de directiva de SSL y SSL de extremo a extremo en Application Gateway
## <a name="overview"></a>Información general
Application Gateway es compatible con la terminación SSL en la puerta de enlace; después, el tráfico fluye normalmente sin cifrar a los servidores back-end. De esta forma, los servidores web se libran de la costosa sobrecarga de cifrado y descifrado. Sin embargo, para algunos clientes, la comunicación sin cifrar en los servidores back-end no es una opción aceptable. Esto podría deberse a los requisitos de seguridad y cumplimiento normativo, o bien a la posibilidad de que la aplicación solo acepte una conexión segura. Para tales aplicaciones, Application Gateway ahora admite el cifrado SSL de extremo a extremo.

El protocolo SSL de un extremo a otro permite transmitir de forma segura información confidencial cifrada al back-end, al mismo tiempo que se siguen aprovechando las ventajas del equilibrio de carga de capa 7 que proporciona Application Gateway, como la afinidad de la cookie, el enrutamiento basado en URL, la compatibilidad con el enrutamiento basado en sitios o la posibilidad de insertar encabezados X-Forwarded-*.

Cuando se configura con el modo de comunicación de SSL de extremo a extremo, Application Gateway finaliza las sesiones SSL del usuario en la puerta de enlace y descifra el tráfico de usuario. Después, aplica las reglas configuradas para seleccionar una instancia de grupo de back-end adecuada en la que enrutar el tráfico. Posteriormente, Application Gateway inicia una nueva conexión SSL al servidor back-end y vuelve a cifrar los datos mediante el certificado de clave pública del servidor back-end antes de transmitir la solicitud al back-end. El protocolo SSL de extremo a extremo se habilita estableciendo la configuración del protocolo de BackendHTTPSetting en Https, que, luego, se aplica a un grupo de back-end. Cada servidor back-end del grupo de back-end con SSL de extremo a extremo habilitado debe configurarse con un certificado para permitir la comunicación segura.

![Escenario de SSL de un extremo a otro][1]

En este ejemplo, las solicitudes mediante TLS1.2 se enrutan a los servidores back-end en Pool1 con SSL de un extremo a otro.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>SSL de un extremo a otro e incorporación a listas blancas de certificados
Application Gateway solo se comunica con instancias de back-end conocidas, que tienen en la lista blanca su certificado con Application Gateway. Para habilitar la creación de listas blancas de certificados, debe cargar la clave pública de los certificados de servidor back-end en Application Gateway (no el certificado raíz). Solo se permiten conexiones en back-ends conocidos y en la lista blanca. Los back-ends restantes producen un error de puerta de enlace. Los certificados autofirmados son para fines de prueba que y no se recomiendan para cargas de trabajo de producción. Antes de poder usar estos certificados, también deben estar en la lista blanca con Application Gateway, tal y como se describe en los pasos anteriores.

## <a name="application-gateway-ssl-policy"></a>Directiva SSL de Application Gateway
Application Gateway admite directivas de negociación SSL configurables, que permiten a los clientes mayor control de las conexiones SSL en Application Gateway.

1. SSL 2.0 y 3.0 están deshabilitados de forma predeterminada en todas las instancias de Application Gateway. No pueden configurarse.
2. La definición de directivas SSL proporciona la opción de deshabilitar cualquiera de los siguientes tres protocolos: TLSv12\_0, TLSv1\_1 y TLSv1\_2.
3. Si no se define ninguna directiva SSL, se habilitan los tres (TLSv1\_0, TLSv1\_1 y TLSv1_2).

## <a name="next-steps"></a>Pasos siguientes
Después de obtener información sobre la directiva SSL y SSL de un extremo a otro, vaya a la página sobre [habilitación de SSL de un extremo a otro en Application Gateway](application-gateway-end-to-end-ssl-powershell.md) para crear una puerta de enlace de aplicaciones con capacidad para enviar tráfico a los back-ends de forma cifrada.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png



<!--HONumber=Nov16_HO2-->


