---
title: "Personalización de las notificaciones emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory | Microsoft Docs"
description: Aprenda a personalizar las notificaciones emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory
services: active-directory
documentationcenter: 
author: jeevansd
manager: mtillman
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: jeedes
ms.custom: aaddev
ms.openlocfilehash: 7394857f55493b072e6ea549c8eeec54a808f5e5
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="customizing-claims-issued-in-the-saml-token-for-enterprise-applications-in-azure-active-directory"></a>Personalización de las notificaciones emitidas en el token SAML para aplicaciones empresariales en Azure Active Directory
Hoy en día Azure Active Directory admite el inicio de sesión único con la mayoría de las aplicaciones empresariales, incluidas las aplicaciones previamente integradas en la galería de aplicaciones de Azure AD, así como las aplicaciones personalizadas. Cuando un usuario se autentica en una aplicación a través de Azure AD con el protocolo SAML 2.0, Azure AD envía un token a la aplicación (mediante HTTP POST). A continuación, la aplicación valida y usa el token para que el usuario inicie sesión en lugar de solicitar un nombre de usuario y una contraseña. Estos tokens SAML contienen trozos de información sobre el usuario conocidos como "notificaciones".

En términos de identidad, una "notificación" es información que declara un proveedor de identidades sobre un usuario dentro del token que emite para dicho usuario. En [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), estos datos suelen incluirse en la instrucción SAML Attribute. El identificador único del usuario suele representarse en SAML Subject, también denominado NameIdentifier.

De forma predeterminada, Azure Active Directory emite un token SAML a la aplicación que contiene una notificación NameIdentifier, con un valor de nombre de usuario del usuario (también conocido como nombre principal de usuario) en Azure AD. Este valor solo puede identificar al usuario. El token SAML también contiene notificaciones adicionales con la dirección de correo electrónico, el nombre y el apellido del usuario.

Para ver o editar las notificaciones emitidas en el token SAML a la aplicación, abra la aplicación en Azure Portal. A continuación, active la casilla **Ver y editar todos los demás atributos de usuario** en la sección **Atributos de usuario** de la aplicación.

![Sección Atributos de usuario][1]

Hay dos razones posibles por las que tendría que editar las notificaciones emitidas en el token SAML:
* La aplicación se ha creado para requerir un conjunto diferente de URI o valores de notificación.
* La aplicación se ha implementado de forma que requiere que la notificación NameIdentifier tenga un valor que no sea el del nombre de usuario (también conocido como nombre principal de usuario) almacenado en Azure Active Directory.

Puede editar cualquiera de los valores de notificación predeterminados. Seleccione la fila de notificación en la tabla de atributos de token SAML. De este modo se abre la sección **Editar atributo** y, a continuación, puede editar el nombre de la notificación, el valor y el espacio de nombres asociados a la notificación.

![Editar atributo de usuario][2]

También puede quitar notificaciones (distintas de NameIdentifier) mediante el menú contextual, que se abre haciendo clic en el icono **...**.  También puede agregar nuevas notificaciones mediante el botón **Agregar atributo**.

![Editar atributo de usuario][3]

## <a name="editing-the-nameidentifier-claim"></a>Edición de la notificación NameIdentifier
Para solucionar el problema en el que la aplicación se ha implementado con un nombre de usuario diferente, haga clic en la lista desplegable **UserIdentifier** de la sección **Atributos de usuario**. Con esta acción se mostrará un cuadro de diálogo con varias opciones:

![Editar atributo de usuario][4]

En la lista desplegable, seleccione **user.mail** a fin de establecer la notificación NameIdentifier para que sea la dirección de correo electrónico del usuario en el directorio. O bien, seleccione **user.onpremisessamaccountname** para su establecimiento en el nombre de cuenta SAM del usuario que se ha sincronizado desde Azure AD local.

También puede usar la función **ExtractMailPrefix()** para quitar el sufijo de dominio de la dirección de correo electrónico, el nombre de cuenta SAM o el nombre principal de usuario. De este modo se extrae solo la primera parte del nombre de usuario por la que se pasa (por ejemplo, "joe_smith" en lugar de joe_smith@contoso.com).

![Editar atributo de usuario][5]

Ahora también hemos agregado la función **join()** para combinar el dominio comprobado con el valor del identificador de usuario. Al seleccionar la función join() en **UserIdentifier**, seleccione primero el identificador de usuario como dirección de correo electrónico o nombre principal de usuario y, a continuación, en la segunda lista desplegable, seleccione el dominio comprobado. Si selecciona la dirección de correo electrónico con el dominio comprobado, Azure AD extrae el nombre de usuario del primer valor joe_smith de joe_smith@contoso.com y lo anexa con contoso.onmicrosoft.com. Consulte el ejemplo siguiente:

![Editar atributo de usuario][6]

## <a name="adding-claims"></a>Incorporación de notificaciones
Al agregar una notificación, puede especificar el nombre del atributo (que no tiene que seguir de forma estricta un patrón de identificador URI según la especificación SAML). Establezca el valor en cualquier atributo de usuario que se almacene en el directorio.

![Agregar atributo de usuario][7]

Por ejemplo, debe enviar el departamento al que pertenece el usuario en su organización como notificación (por ejemplo, Ventas). Especifique el nombre de la notificación del modo esperado por la aplicación y, a continuación, seleccione **user.department** como valor.

> [!NOTE]
> Si no hay ningún valor almacenado para el atributo seleccionado para un usuario determinado, esa notificación no se emite en el token.

> [!TIP]
> **user.onpremisesecurityidentifier** y **user.onpremisesamaccountname** solo se admiten cuando se sincronizan los datos de usuario desde una instancia local de Active Directory mediante la [herramienta Azure AD Connect](../active-directory-aadconnect.md).

## <a name="restricted-claims"></a>Notificaciones restringidas

Hay algunas notificaciones restringidas en SAML. Si agrega estas notificaciones, Azure AD no enviará estas notificaciones. A continuación se muestra el conjunto de notificaciones restringidas de SAML:

    | Tipo de notificación (URI) |
    | ------------------- |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/expired |
    | http://schemas.microsoft.com/identity/claims/accesstoken |
    | http://schemas.microsoft.com/identity/claims/openid2_id |
    | http://schemas.microsoft.com/identity/claims/identityprovider |
    | http://schemas.microsoft.com/identity/claims/objectidentifier |
    | http://schemas.microsoft.com/identity/claims/puid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier[MR1] |
    | http://schemas.microsoft.com/identity/claims/tenantid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod |
    | http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groups |
    | http://schemas.microsoft.com/claims/groups.link |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/role |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/wids |
    | http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant |
    | http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown |
    | http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged |
    | http://schemas.microsoft.com/2014/03/psso |
    | http://schemas.microsoft.com/claims/authnmethodsreferences |
    | http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn |
    | http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent |
    | http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier |
    | http://schemas.microsoft.com/identity/claims/scope |

## <a name="next-steps"></a>Pasos siguientes
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](../active-directory-apps-index.md)
* [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saml-claims-customization/user-attribute-section.png
[2]: ./media/active-directory-saml-claims-customization/edit-claim-name-value.png
[3]: ./media/active-directory-saml-claims-customization/delete-claim.png
[4]: ./media/active-directory-saml-claims-customization/user-identifier.png
[5]: ./media/active-directory-saml-claims-customization/extractemailprefix-function.png
[6]: ./media/active-directory-saml-claims-customization/join-function.png
[7]: ./media/active-directory-saml-claims-customization/add-attribute.png
