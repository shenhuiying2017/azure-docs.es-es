---
title: "Provisión de detalles de contacto de seguridad en Azure Security Center | Microsoft Docs"
description: "En este documento se muestra cómo proporcionar detalles de contacto de seguridad en Azure Security Center."
services: security-center
documentationcenter: na
author: TerryLanfear
manager: MBaldwin
editor: 
ms.assetid: 26b5dcb4-ce3f-4f22-8d56-d2bf743cfc90
ms.service: security-center
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/15/2017
ms.author: terrylan
ms.openlocfilehash: 726b59c45e2eb18eebe28a180db23336ae141408
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="provide-security-contact-details-in-azure-security-center"></a>Proporcionar detalles de contacto de seguridad en Azure Security Center
Azure Security Center recomendará que proporcione los detalles de contacto de seguridad para su suscripción de Azure si no lo ha hecho ya. Esta información la utilizará Microsoft para ponerse en contacto con usted si Microsoft Security Response Center (MSRC) detecta que un tercero no autorizado o ilegal ha accedido a los datos de clientes. MSRC lleva a cabo una selecta supervisión de seguridad de la red e infraestructura de Azure y recibe información sobre amenazas y quejas sobre abusos de terceros.

Se envía una notificación de correo electrónico en la primera repetición diaria de una alerta y solo en aquellas con un nivel de gravedad elevado. Las preferencias de correo electrónico solo pueden configurarse para las directivas de suscripción. Los grupos de recursos de una suscripción heredan esta configuración.

> [!NOTE]
> En este documento se presenta el servicio mediante una implementación de ejemplo.  No se trata de una guía paso a paso.
>
>

## <a name="implement-the-recommendation"></a>Implementación de la recomendación
1. En **Recomendaciones**, seleccione **Proporcionar detalles de contacto de seguridad**.
   ![Proporcionar contacto de seguridad][1]
2. Seleccione la suscripción de Azure sobre la que desea proporcionar información de contacto.
3. Se abre **Directiva de seguridad - Notificaciones por correo electrónico**.

   ![Proporcionar datos de los contactos de seguridad][2]

   * Escriba la dirección de correo electrónico del contacto de seguridad o direcciones separadas por comas. No hay un límite en el número de direcciones de correo electrónico que se pueden escribir.
   * Escriba un número de teléfono internacional de seguridad.
   * Para recibir correos electrónicos de alertas de gravedad alta, active la opción **Send me emails about alert**(Enviar correos electrónicos de alertas).
   * Más adelante, tendrá la opción de enviar notificaciones por correo electrónico a los propietarios de la suscripción. De momento, esta opción aparece atenuada.
   * Seleccione **Aceptar** para aplicar la información de contacto de seguridad a su suscripción.

## <a name="see-also"></a>Otras referencias
Para más información sobre el Centro de seguridad, consulte los siguientes recursos:

* [Establecimiento de directivas de seguridad en Azure Security Center](security-center-policies.md) : aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
* [Administración de recomendaciones de seguridad en Azure Security Center](security-center-recommendations.md): recomendaciones que le ayudan a proteger los recursos de Azure.
* [Supervisión del estado de seguridad en Azure Security Center](security-center-monitoring.md): obtenga información sobre cómo supervisar el mantenimiento de los recursos de Azure.
* [Administración y respuesta a las alertas de seguridad en Azure Security Center](security-center-managing-and-responding-alerts.md) : obtenga información sobre cómo administrar y responder a alertas de seguridad.
* [Supervisión de las soluciones de asociados con Azure Security Center](security-center-partner-solutions.md): aprenda a supervisar el estado de mantenimiento de las soluciones de asociados.
* [Preguntas más frecuentes sobre Azure Security Center](security-center-faq.md): busque las preguntas más frecuentes sobre cómo usar el servicio.
* [Blog de seguridad de Azure](http://blogs.msdn.com/b/azuresecurity/) : obtenga las últimas noticias e información sobre la seguridad en Azure.

<!--Image references-->
[1]: ./media/security-center-provide-security-contacts/provide-contacts.png
[2]:./media/security-center-provide-security-contacts/provide-contact-details.png
