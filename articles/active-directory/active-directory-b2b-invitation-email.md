---
title: "Los elementos del correo electrónico de invitación de colaboración B2B de Azure Active Directory | Microsoft Docs"
description: "Plantilla de correo electrónico de invitación de colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: femila
editor: 
tags: 
ms.assetid: 
ms.service: active-directory
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: identity
ms.date: 02/09/2017
ms.author: sasubram
translationtype: Human Translation
ms.sourcegitcommit: a4e59dfa8a098f63c3173176c4d2675d6a59af00
ms.openlocfilehash: 46677085695b31d0e2b1102fa5a5105b6437adc6


---


# <a name="the-elements-of-the-b2b-collaboration-invitation-email"></a>Los elementos del correo electrónico de invitación de colaboración B2B

Los correos electrónicos de invitación son un componente fundamental para incorporar a los asociados como usuarios de colaboración B2B en Azure AD. El objetivo principal es aumentar la confianza en el destinatario y agregar legitimidad y prueba social al correo electrónico, para asegurarse de que el destinatario se sienta cómodo al seleccionar el botón **Introducción** para aceptar la invitación. Se trata de un componente clave para reducir la fricción de uso compartido. Y, por supuesto, la intención es que el correo electrónico presente el mejor aspecto.

![Correo electrónico de invitación de B2B de Azure AD](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>Explicación del correo electrónico
Se van a tratar algunos elementos del correo electrónico, para saber cómo hacer el mejor uso de estas funcionalidades.

### <a name="subject"></a>Asunto
El asunto del correo electrónico sigue este patrón: Está invitado a la organización de &lt;nombreinquilino&gt;.

### <a name="from-address"></a>Dirección De
Se usa un patrón similar a LinkedIn para la dirección De. El objetivo es aclarar quién es el invitador y a qué empresa pertenece y también aclarar que el correo electrónico procede de una dirección de correo electrónico de Microsoft. El formato es: &lt;Nombre para mostrar &gt; de &lt;nombreinquilino&gt; (a través de Microsoft) <invites@microsoft.com&gt;

### <a name="reply-to"></a>Responder a
En la respuesta al correo electrónico se indica el correo electrónico del invitador si está disponible, para que al responder al correo electrónico se vuelva a enviar un correo al invitador.

### <a name="branding"></a>Personalización de marca
Los correos electrónicos de invitación del inquilino usan la personalización de marca de la empresa que puede haberse configurado para el inquilino. Si desea beneficiarse de esta característica, [aquí](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) se proporcionan los detalles sobre cómo configurarla. El logotipo del banner se mostrará en el correo electrónico. Siga el tamaño de la imagen y las instrucciones de calidad indicadas [aquí](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) para obtener los mejores resultados. Además, el nombre de la empresa también se presenta en la llamada a la acción.

### <a name="call-to-action"></a>Llamada a la acción
La llamada a la acción consta de dos partes: explicar por qué el destinatario ha recibido el correo electrónico y qué se pide al destinatario que haga al respecto.
- La sección “por qué” puede seguir este patrón: Se le ha invitado a acceder a aplicaciones en la organización de &lt;nombreinquilino&gt;.

- Y la sección “qué se le pide que haga” está indicada por la presencia del botón **Introducción**. Cuando el destinatario se agrega sin necesidad de invitaciones, este botón no se muestra.

### <a name="inviters-information"></a>Información sobre el invitador
Nombre para mostrar del invitador se incluye en el correo electrónico. Y, además, si ha configurado una imagen de perfil para la cuenta de Azure AD, el correo electrónico de invitación incluye también esa imagen. Ambas opciones están diseñadas para aumentar la confianza del destinatario en el correo electrónico.

Si el invitador aún no ha configurado una imagen de perfil, Azure AD crea un icono con las iniciales del invitador en el lugar de la imagen, tal y como se muestra:

  ![mostrar las iniciales del invitador](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Cuerpo
Contiene el mensaje que el invitador escribe o se transmite a través de la API de invitación. Se trata de un área de texto, que no procesará etiquetas HTML por razones de seguridad.

### <a name="footer-section"></a>Sección de pie de página
El pie de página contiene la marca de empresa de Microsoft y permite que el destinatario sepa si el correo electrónico se envía desde un alias no supervisado. Casos especiales:

- El invitador no tiene una dirección de correo electrónico en el espacio empresarial invitador.

  ![La imagen no tiene una dirección de correo electrónico en el espacio empresarial invitador.](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- El destinatario no necesita canjear la invitación.

  ![Si el destinatario no necesita canjear la invitación.](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Azure Active Directory](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)



<!--HONumber=Feb17_HO2-->


