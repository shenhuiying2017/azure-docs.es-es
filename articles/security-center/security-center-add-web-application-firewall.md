---
title: "Adición de un firewall de aplicaciones web en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo implementar las recomendaciones de agregar un firewall de aplicaciones web y de finalizar la protección de la aplicación de Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 8f56139a-4466-48ac-90fb-86d002cf8242
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2017
ms.author: terrylan
ms.openlocfilehash: e858db97c3e7a832ad01e16a60d486a758109d7c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="add-a-web-application-firewall-in-azure-security-center"></a>Adición de un firewall de aplicaciones web en el Centro de seguridad de Azure
Azure Security Center puede recomendarle agregar Firewall de aplicaciones web (WAF) de un asociado de Microsoft para proteger las aplicaciones web. Este documento guía a través de un ejemplo de cómo aplicar esta recomendación.

Se muestra una recomendación WAFS para cualquier IP pública (dirección IP de nivel de instancia o con equilibrio de carga) que tiene un grupo de seguridad de red asociado con puertos abiertos web entrantes (80 y 443).

Security Center le recomendará que aprovisione un WAF para defenderse de ataques dirigidos a las aplicaciones web que se encuentran tanto en las máquinas virtuales como en App Service Environment. Un entorno de App Service es una opción de plan de servicio [Premium](https://azure.microsoft.com/pricing/details/app-service/) de Azure App Service que proporciona un entorno plenamente aislado y dedicado para ejecutar de forma segura las aplicaciones de Azure App Service. Para más información acerca de ASE, consulte [Documentación de App Service Environment](../app-service/environment/intro.md).

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  Este documento no es una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En la hoja **Recomendaciones**, seleccione **Secure web application using web application firewall** (Proteger la aplicación web con el firewall de aplicaciones web).
   ![Aplicación web segura][1]
2. En la hoja **Proteger la aplicación web con Firewall de aplicaciones web** , seleccione una aplicación web. Se abre la hoja **Add a Web Application Firewall** (Agregar un Firewall de aplicaciones web).
   ![Add a web application firewall][2]
3. Puede elegir usar un firewall de aplicaciones web existentes, si está disponible, o puede crear uno nuevo. En este ejemplo no hay ningún WAF existente, por lo que vamos a crear uno.
4. Para crear un nuevo WAF, seleccione una solución de la lista de partners integrados. En ese ejemplo, seleccionaremos **Firewall de aplicaciones web de Barracuda**.
5. Se abrirá la hoja **Firewall de aplicaciones web de Barracuda** , que le proporcionará información sobre la solución del partner. Seleccione **Crear** en la hoja de información.

   ![Hoja de información del firewall][3]

6. Se abrirá la hoja **Nuevo firewall de aplicaciones web** donde puede **configurar la máquina virtual** y proporcionar **información sobre WAF**. Seleccione **Configuración de VM**.
7. En la hoja **Configuración de VM** , se ingresa la información requerida para poner en marcha la máquina virtual que ejecutará el WAF.
   ![VM configuration][4]
8. Vuelva a la hoja **Nuevo firewall de aplicaciones web** y seleccione **Información del WAF**. En la hoja **Información de WAF** , configure el WAF. El paso 7 le permite configurar la máquina virtual en que se ejecutará el WAF y el paso 8 le permite aprovisionar el WAF.

## <a name="finalize-application-protection"></a>Finalización de la protección de la aplicación
1. Vuelva a la hoja **Recomendaciones** . Después de crear el WAF, se generó una entrada nueva, denominada **Finalize application protection**(Finalizar la protección de la aplicación). Dicha le permite saber qué se necesita para completar el proceso de conectar el WAF dentro de la Red virtual de Azure, con el fin de que pueda proteger la aplicación.

   ![Finalización de la protección de la aplicación][5]

2. Seleccione **Finalize application protection**(Finalizar la protección de la aplicación). Se abre una nueva hoja. En ella puede ver que hay una aplicación web que necesita que su tráfico se vuelva a enrutar.
3. Seleccione la aplicación web. Se abre una hoja en la que encontrará los pasos necesarios para finalizar la configuración del Firewall de aplicaciones web. Complete los pasos y, a continuación, seleccione la opción de **restringir tráfico**. Después, Security Center realizará las conexiones automáticamente.

   ![Restringir tráfico][6]

> [!NOTE]
> Puede proteger varias aplicaciones web del Centro de seguridad si agrega estas aplicaciones a las implementaciones de WAF existentes.
>
>

Los registros de ese WAF ahora están totalmente integrados. El Centro de seguridad puede comenzar a recopilar y analizar automáticamente los registros, con el fin de poder exponer las alertas de seguridad importantes.

## <a name="next-steps"></a>Pasos siguientes
En este documento, mostramos cómo implementar la recomendación "Adición de una aplicación web" del Centro de seguridad. Para obtener más información sobre cómo configurar un firewall de aplicaciones web, vea lo siguiente:

* [Configuración de un firewall de aplicaciones web (WAF) para entornos del Servicio de aplicaciones](../app-service/environment/app-service-app-service-environment-web-application-firewall.md)

Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md) : obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : aprenda a administrar y responder a alertas de seguridad.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md) : recomendaciones que le ayudan a proteger los recursos de Azure.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md) : encuentre las preguntas más frecuentes sobre el uso del servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : encuentre publicaciones de blog sobre el cumplimiento y la seguridad de Azure.

<!--Image references-->
[1]: ./media/security-center-add-web-application-firewall/secure-web-application.png
[2]:./media/security-center-add-web-application-firewall/add-a-waf.png
[3]: ./media/security-center-add-web-application-firewall/info-blade.png
[4]: ./media/security-center-add-web-application-firewall/select-vm-config.png
[5]: ./media/security-center-add-web-application-firewall/finalize-waf.png
[6]: ./media/security-center-add-web-application-firewall/restrict-traffic.png
