---
title: "Personalización y API de colaboración B2B de Active Directory Azure| Microsoft Docs"
description: "La colaboración con Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas."
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
ms.date: 04/11/2017
ms.author: sasubram
ms.openlocfilehash: c85e05b38b4a9525e13ec510a17b7ef4841198d7
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2b-collaboration-api-and-customization"></a>Personalización y API de colaboración B2B de Active Directory Azure

Hemos tenido muchos clientes que nos han dicho que querían personalizar el proceso de invitación de una forma que se adapte menor a sus organizaciones. Con nuestra API, pueden hacer justamente eso. [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation)

## <a name="capabilities-of-the-invitation-api"></a>Funcionalidades de la API de invitación
La API ofrece las siguientes funcionalidades:

1. Invite a un usuario externo con *cualquier* dirección de correo electrónico.

    ```
    "invitedUserDisplayName": "Sam"
    "invitedUserEmailAddress": "gsamoogle@gmail.com"
    ```

2. Personalice a qué página desea que lleguen los usuarios finales después de aceptar la invitación.

    ```
    "inviteRedirectUrl": "https://myapps.microsoft.com/"
    ```

3. Seleccione enviar el correo electrónico de invitación estándar por medio de nosotros

    ```
    "sendInvitationMessage": true
    ```

  con un mensaje al destinatario que pueda personalizar.

    ```
    "customizedMessageBody": "Hello Sam, let's collaborate!"
    ```

4. Luego, ponga en copia a los usuarios a los que informar de que ha invitado a este colaborador.

5. También puede personalizar completamente su invitación y el flujo de trabajo de incorporación decidiendo no enviar notificaciones a través de Azure AD.

    ```
    "sendInvitationMessage": false
    ```

  En este caso, obtendrá una URL de canje a través de la API, que puede incrustar en una plantilla de correo electrónico, mensaje instantáneo u otro método de distribución que prefiera.

6. Finalmente, si es administrador, puede invitar al usuario como miembro.

    ```
    "invitedUserType": "Member"
    ```


## <a name="authorization-model"></a>Modelo de autorización
La API se puede ejecutar en los siguientes modos de autorización:

### <a name="app--user-mode"></a>Aplicación y modo de usuario
En este modo, el usuario que usa la API debe tener los permisos necesarios para crear invitaciones de B2B.

### <a name="app-only-mode"></a>Modo de solo aplicación
En el contexto de solo aplicación, la aplicación necesita los ámbitos User.ReadWrite.All o Directory.ReadWrite.All para que la invitación se realice correctamente.

Para obtener más información, consulte: https://graph.microsoft.io/docs/authorization/permission_scopes.


## <a name="powershell"></a>PowerShell
Ahora es posible usar PowerShell para agregar e invitar a usuarios externos a una organización fácilmente. Cree una nueva invitación mediante el cmdlet:

```
New-AzureADMSInvitation
```

Puede usar las siguientes opciones:

* -InvitedUserDisplayName
* -InvitedUserEmailAddress
* -SendInvitationMessage
* -InvitedUserMessageInfo

También puede comprobar la referencia de la API de invitación en [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Pasos siguientes

Examine nuestros otros artículos sobre la colaboración B2B de Azure AD:

* [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
* [¿Cómo agregan los administradores de Azure Active Directory usuarios de colaboración B2B?](active-directory-b2b-admin-add-users.md)
* [¿Cómo agregan los trabajadores de la información usuarios de colaboración B2B?](active-directory-b2b-iw-add-users.md)
* [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
* [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
* [Concesión de licencias de colaboración B2B de Azure AD](active-directory-b2b-licensing.md)
* [Solución de problemas de colaboración B2B de Azure Active Directory](active-directory-b2b-troubleshooting.md)
* [Preguntas frecuentes sobre la colaboración B2B de Azure Active Directory (P+F)](active-directory-b2b-faq.md)
* [Autenticación multifactor para usuarios de colaboración B2B](active-directory-b2b-mfa-instructions.md)
* [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](active-directory-apps-index.md)
