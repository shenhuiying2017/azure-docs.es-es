---
title: Personalización y API de colaboración B2B de Active Directory Azure| Microsoft Docs
description: La colaboración con Azure Active Directory B2B posibilita las relaciones entre empresas al permitir que los asociados empresariales accedan de forma selectiva a las aplicaciones corporativas.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/11/2017
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: f1bd93ac2ef6aa75e07eeec3e3cb2222b6febc1c
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
ms.locfileid: "33932377"
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
En el contexto de solo aplicación, la aplicación necesita el ámbito User.Invite.All para que la invitación se realice correctamente.

Para más información, consulte: https://graph.microsoft.io/docs/authorization/permission_scopes


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

También puede consultar la referencia de API de invitación en [https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation](https://developer.microsoft.com/graph/docs/api-reference/v1.0/resources/invitation).

## <a name="next-steps"></a>Pasos siguientes

- [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)
- [Los elementos del correo electrónico de invitación de colaboración B2B](active-directory-b2b-invitation-email.md)
- [Canje de invitación de colaboración B2B](active-directory-b2b-redemption-experience.md)
- [Incorporación de usuarios de colaboración B2B sin invitación](active-directory-b2b-add-user-without-invite.md)

