---
title: "Integración de Application Gateway con Azure Security Center | Microsoft Docs"
description: "Esta página proporciona información sobre cómo se integra Application Gateway en Azure Security Center."
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: 
ms.assetid: e5ea5cf9-3b41-4b85-a12c-e758bff7f3ec
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.custom: 
ms.workload: infrastructure-services
ms.date: 06/07/2017
ms.author: davidmu
ms.openlocfilehash: abed4095bcacac4ca3995acfaafae2f16d5f1532
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-integration-between-application-gateway-and-azure-security-center"></a>Introducción a la integración entre Application Gateway y Azure Security Center

Conozca más información acerca de cómo Application Gateway y Security Center le ayudan a proteger los recursos de la aplicación web. El firewall de la aplicación web (WAF) de Application Gateway se integra con [Security Center](../security-center/security-center-intro.md) para proporcionar una vista integrada que evite, detecte y responda a las amenazas a las aplicaciones web no protegidas de su entorno.

## <a name="overview"></a>Información general

El WAF de Application Gateway es una recomendación de Security Center para proteger las aplicaciones web frente a ataques y vulnerabilidades. Los recursos con conexión que no están protegidos con WAF aparecen en Security Center como recomendaciones de gravedad alta. Las recomendaciones para los firewalls de aplicaciones web aparecen en la página **Introducción**, en **Aplicaciones**.

![integración con security center][1]

Al hacer clic en las recomendaciones sobre el firewall de aplicación web se abre una nueva hoja que muestra los detalles de la recomendación.

## <a name="add-a-web-application-firewall-to-an-existing-resource"></a>Adición del firewall de aplicaciones web a un recurso existente

Vaya a **Más servicios** > **Seguridad e identidad** > **Security Center** y en la hoja **Security Center - Información general**, haga clic en **Aplicaciones**. En la hoja **Security Center - Aplicaciones**, la tabla contiene una lista de las aplicaciones que Security Center ha detectado en su suscripción.

![aplicaciones web][3]

Al hacer clic en una aplicación web con un problema crítico, aparecerá la hoja **Estado de seguridad de la aplicación**. En la imagen siguiente, se ve la aplicación web que no está protegida por un firewall de aplicaciones web. 

![recursos web no protegidos][2]

Haga clic en **Agregar un firewall de aplicaciones web** en **Recomendaciones** para que se abra la hoja **Agregar un firewall de aplicaciones web**.

Si no tiene una instancia de Application Gateway existente o quiere crear una nueva, haga clic en **Crear nuevo** y en la hoja **Create a new Web Application Firewall** (Crear un nuevo firewall de aplicaciones web) y haga clic en **Microsoft - Application Gateway**. Esto le guiará por los pasos para crear una instancia de Application Gateway. En este punto, la aplicación web se agrega como un recurso protegido y Security Center realiza un seguimiento para asegurarse de que este recurso está protegido por un firewall de aplicaciones web. No se agrega como miembro del grupo de back-end.

Si ya tiene una instancia de Application Gateway, puede elegirla en **Usar solución existente**

![hoja de adición de firewall de aplicaciones web][4]

La adición de una aplicación web a una instancia de Application Gateway a través de Security Center no agrega el recurso como miembro del grupo de back-end. Esto se debe hacer directamente en el recurso de Application Gateway.

## <a name="add-a-resource-to-an-existing-web-application-firewall"></a>Adición de un recurso a un firewall de aplicaciones web existente

Vaya a **Más servicios** > **Seguridad e identidad** > **Security Center** y en la hoja **Security Center - Información general**, haga clic en **Soluciones de asociados**. Las instancias de Application Gateway existentes reconocidas en Security Center aparecen en la hoja **Soluciones de asociados**.

![soluciones de asociados][7]

Haga clic en **Vincular aplicación** para abrir la hoja **Vincular aplicaciones**. Aquí se proporcionan las opciones para seleccionar las aplicaciones existentes. Elija las aplicaciones que desea proteger y haga clic en **Aceptar**. Este procedimiento no agregará la aplicación web al grupo de back-end de la instancia de Application Gateway. Esto establece los recursos como recursos protegidos, por lo que Security Center puede realizar un seguimiento de ellos. Para agregar el recurso como un miembro del grupo de back-end, debe hacerlo en la instancia de Application Gateway. En la hoja actual puede hacer clic en **Consola de soluciones** para ir al recurso de Application Gateway donde podrá agregar la aplicación web al grupo de back-end.

![aplicaciones de soluciones de asociados][6]

## <a name="finalize-configuration"></a>Finalización de la configuración

Security Center realiza un seguimiento de las aplicaciones agregadas a una instancia de Application Gateway como recursos protegidos.  Supervisa el estado de este recurso y se asegura de que está protegido mediante una instancia de Application Gateway. El siguiente paso consiste en agregar la dirección IP privada, la dirección IP pública o el NIC de la máquina virtual al grupo de back-end de la instancia de Application Gateway. Hasta que esto se hace, aparece una recomendación adicional de **Finalizar protección de la aplicación** hasta que se agrega el recurso.

![hoja de adición de firewall de aplicaciones web][5]

## <a name="security-alerts"></a>Alertas de seguridad

En Security Center, vaya a **DETECCIÓN** > **Alertas de seguridad**.  Aquí encontrará las alertas de WAF para las instancias de Application Gateway. Las alertas se desglosan según las reglas de WAF.

![alertas de seguridad][8]

Si hace clic en una regla proporcionará una lista de alertas para esa regla de WAF específica. Cada alerta muestra detalles adicionales sobre la búsqueda. Los detalles de proporcionan un vínculo a la instancia de Application Gateway.
 
![detalles de alertas][9]

## <a name="next-steps"></a>Pasos siguientes

Para aprender a habilitar un firewall de aplicaciones web en una instancia existente de Application Gateway, visite [Creación o actualización de una instancia de Azure Application Gateway con el firewall de aplicaciones web](application-gateway-web-application-firewall-portal.md#add-web-application-firewall-to-an-existing-application-gateway)

[1]: ./media/application-gateway-integration-security-center/figure1.png
[2]: ./media/application-gateway-integration-security-center/figure2.png
[3]: ./media/application-gateway-integration-security-center/figure3.png
[4]: ./media/application-gateway-integration-security-center/figure4.png
[5]: ./media/application-gateway-integration-security-center/figure5.png
[6]: ./media/application-gateway-integration-security-center/figure6.png
[7]: ./media/application-gateway-integration-security-center/figure7.png
[8]: ./media/application-gateway-integration-security-center/securitycenter.png
[9]: ./media/application-gateway-integration-security-center/figure9.png