---
title: "Elementos del correo electrónico de invitación para la colaboración B2B: Azure Active Directory | Microsoft Docs"
description: "Plantilla de correo electrónico de invitación de colaboración B2B de Azure Active Directory"
services: active-directory
documentationcenter: 
author: sasubram
manager: mtillman
editor: 
tags: 
ms.custom: seohack1
ms.service: active-directory
ms.topic: article
ms.workload: identity
ms.date: 05/23/2017
ms.author: sasubram
ms.openlocfilehash: b84dba6aa7ac11aef6d958d72564b8b83514a753
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/18/2018
---
# <a name="the-elements-of-the-b2b-collaboration-invitation-email---azure-active-directory"></a>Elementos del correo electrónico de invitación para la colaboración B2B: Azure Active Directory

Los correos electrónicos de invitación son un componente fundamental para incorporar a los asociados como usuarios de colaboración B2B en Azure AD. Puede usarlos para aumentar la confianza del destinatario. Puede agregar legitimidad y prueba social al correo electrónico para asegurarse de que el destinatario se sienta cómodo al seleccionar el botón **Empezar** para aceptar la invitación. Esta confianza es clave para reducir la fricción en el uso compartido. Y puede que también quiera que el correo electrónico tenga un buen aspecto.

![Correo electrónico de invitación de B2B de Azure AD](media/active-directory-b2b-invitation-email/invitation-email.png)

## <a name="explaining-the-email"></a>Explicación del correo electrónico
Se van a tratar algunos elementos del correo electrónico para saber cómo hacer el mejor uso de estas funcionalidades.

### <a name="subject"></a>Asunto
El asunto del correo electrónico sigue este patrón: Está invitado a la organización de &lt;nombreinquilino&gt;.

### <a name="from-address"></a>Dirección De
Se usa un patrón similar a LinkedIn para la dirección De.  Es necesario tener claro quién es el invitador y a qué empresa pertenece y también aclarar que el correo electrónico procede de una dirección de correo electrónico de Microsoft. El formato es: &lt;Nombre para mostrar del invitador&gt; de &lt;nombreinquilino&gt; (a través de Microsoft) invites@microsoft.com&gt;.

### <a name="reply-to"></a>Responder a
En la respuesta al correo electrónico se indica el correo electrónico del invitador si está disponible, para que al responder al correo electrónico se vuelva a enviar un correo al invitador.

### <a name="branding"></a>Personalización de marca
Los correos electrónicos de invitación del inquilino usan la personalización de marca de la empresa que puede haberse configurado para el inquilino. Si desea beneficiarse de esta funcionalidad, [aquí](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) se proporcionan los detalles sobre cómo configurarla. El logotipo del banner aparece en el correo electrónico. Siga el tamaño de la imagen y las instrucciones de calidad indicadas [aquí](https://docs.microsoft.com/azure/active-directory/active-directory-branding-custom-signon-azure-portal) para obtener los mejores resultados. Además, el nombre de la empresa también se presenta en la llamada a la acción.

### <a name="call-to-action"></a>Llamada a la acción
La llamada a la acción consta de dos partes: explicar por qué el destinatario ha recibido el correo electrónico y qué se pide al destinatario que haga al respecto.
- La sección “por qué” puede seguir este patrón: Se le ha invitado a acceder a aplicaciones en la organización de &lt;nombreinquilino&gt;.

- Y la sección “qué se le pide que haga” está indicada por la presencia del botón **Introducción**. Cuando el destinatario se agrega sin necesidad de invitaciones, este botón no se muestra.

### <a name="inviters-information"></a>Información sobre el invitador
El nombre para mostrar del invitador se incluye en el correo electrónico. Y, además, si ha configurado una imagen de perfil para la cuenta de Azure AD, el correo electrónico de invitación incluye también esa imagen. Ambas opciones están diseñadas para aumentar la confianza del destinatario en el correo electrónico.

Si aún no ha configurado la imagen del perfil, se muestra un icono con las iniciales del invitador en lugar de la imagen:

  ![mostrar las iniciales del invitador](media/active-directory-b2b-invitation-email/inviters-initials.png)

### <a name="body"></a>Cuerpo
Contiene el mensaje que el invitador escribe o se transmite a través de la API de invitación. Al ser un área de texto, por motivos de seguridad no se procesan las etiquetas HTML.

### <a name="footer-section"></a>Sección de pie de página
El pie de página contiene la marca de empresa de Microsoft y permite que el destinatario sepa si el correo electrónico se envía desde un alias no supervisado. Casos especiales:

- El invitador no tiene una dirección de correo electrónico en el espacio empresarial invitador.

  ![La imagen no tiene una dirección de correo electrónico en el espacio empresarial invitador.](media/active-directory-b2b-invitation-email/inviter-no-email.png)


- El destinatario no necesita canjear la invitación.

  ![Si el destinatario no necesita canjear la invitación.](media/active-directory-b2b-invitation-email/when-recipient-doesnt-redeem.png)


## <a name="next-steps"></a>pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)
* [Personalización y API de colaboración B2B de Active Directory Azure](active-directory-b2b-api.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
