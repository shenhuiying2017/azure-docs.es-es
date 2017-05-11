---
title: "Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory | Microsoft Docs"
description: Aprenda a personalizar las notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory
services: active-directory
documentationcenter: 
author: asmalser-msft
manager: femila
editor: 
ms.assetid: f1daad62-ac8a-44cd-ac76-e97455e47803
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/26/2016
ms.author: asmalser
ms.translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: e89a06de6232afef579c32d51137ddf577917436
ms.contentlocale: es-es
ms.lasthandoff: 04/27/2017


---
# <a name="customizing-claims-issued-in-the-saml-token-for-pre-integrated-apps-in-azure-active-directory"></a>Personalización de notificaciones emitidas en el token SAML para aplicaciones previamente integradas en Azure Active Directory
Hoy en día Azure Active Directory admite miles de aplicaciones previamente integradas en la galería de aplicaciones de Azure AD, de las cuales más de 150 admiten el inicio de sesión único mediante el protocolo SAML 2.0. Cuando un usuario se autentica en una aplicación a través de Azure AD con SAML, Azure AD envía un token a la aplicación (mediante una redirección HTTP 302). A continuación, la aplicación valida y usa el token para que el usuario inicie sesión en lugar de solicitar un nombre de usuario y una contraseña. Estos tokens SAML contienen trozos de información sobre el usuario conocidos como "notificaciones".

En términos de identidad, una "notificación" es información que declara un proveedor de identidades sobre un usuario dentro del token que emite para dicho usuario. En un [token SAML](http://en.wikipedia.org/wiki/SAML_2.0), estos datos se suelen encontrar en la instrucción SAML Attribute y el identificador único del usuario se representa habitualmente en SAML Subject.

De forma predeterminada, Azure AD emite un token SAML a la aplicación que contiene una notificación NameIdentifier, con un valor de nombre de usuario del usuario en Azure AD. Este valor solo puede identificar al usuario. El token SAML también contiene notificaciones adicionales con la dirección de correo electrónico, el nombre y el apellido del usuario.

Para ver o modificar las notificaciones emitidas en el token SAML a la aplicación, abra el registro de aplicación en el Portal de Azure clásico y seleccione la pestaña **Atributos** debajo de la aplicación.

![Pestaña Atributos][1]

Hay dos razones posibles por las que tendría que editar las notificaciones emitidas en el token SAML:
* La aplicación se ha creado para requerir un conjunto diferente de URI o valores de notificación. 
* La aplicación se ha implementado de forma que requiere que la notificación NameIdentifier tenga un valor que no sea el del nombre de usuario (también conocido como nombre principal de usuario) almacenado en Azure Active Directory. 

Puede editar cualquiera de los valores de notificación predeterminados. Seleccione el icono en forma de lápiz que aparece a la derecha cuando sitúa el mouse sobre una de las filas de la tabla de atributos del token SAML. También puede quitar notificaciones (excepto NameIdentifier) mediante el icono **X** y agregar notificaciones nuevas con el botón **Agregar atributo de usuario**.

## <a name="editing-the-nameidentifier-claim"></a>Edición de la notificación NameIdentifier
Para solucionar el problema en el que la aplicación se ha implementado con un nombre de usuario diferente, haga clic en el icono de lápiz junto a la notificación NameIdentifier. Con esta acción se mostrará un cuadro de diálogo con varias opciones:

![Editar atributo de usuario][2]

En el menú **Valor de atributo**, seleccione **user.mail** a fin de establecer la notificación NameIdentifier para que sea la dirección de correo electrónico del usuario en el directorio. O bien, seleccione **user.onpremisessamaccountname** para su establecimiento en el nombre de cuenta SAM del usuario que se ha sincronizado desde Azure AD local.

También puede usar la función ExtractMailPrefix() especial para quitar el sufijo de dominio de la dirección de correo electrónico o del nombre principal de usuario. Y, a continuación, solo la primera parte del nombre de usuario por la que se pasa (por ejemplo, "joe_smith" en lugar de joe_smith@contoso.com).

![Editar atributo de usuario][3]

## <a name="adding-claims"></a>Incorporación de notificaciones
Al agregar una notificación, puede especificar el nombre del atributo (que no tiene que seguir de forma estricta un patrón de identificador URI según la especificación SAML). Establezca el valor en cualquier atributo de usuario que se almacene en el directorio.

![Agregar atributo de usuario][4]

Por ejemplo, debe enviar el departamento al que pertenece el usuario en su organización como notificación (por ejemplo, Ventas). Puede especificar el valor de notificación esperado por la aplicación y, a continuación, seleccionar **user.department** como valor.

Si no hay ningún valor almacenado para el atributo seleccionado para un usuario determinado, esa notificación no se emite en el token.

**Nota:** **user.onpremisesecurityidentifier** y **user.onpremisesamaccountname** solo se admiten cuando se sincronizan los datos de usuario desde una instancia local de Active Directory mediante la [herramienta Azure AD Connect](../active-directory-aadconnect.md).

## <a name="next-steps"></a>Pasos siguientes
* [Índice de artículos sobre la administración de aplicaciones en Azure Active Directory](../active-directory-apps-index.md)
* [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](../active-directory-saas-custom-apps.md)
* [Cómo depurar el inicio de sesión único basado en SAML en aplicaciones de Azure Active Directory](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ../media/active-directory-saml-claims-customization/claimscustomization1.png
[2]: ../media/active-directory-saml-claims-customization/claimscustomization2.png
[3]: ../media/active-directory-saml-claims-customization/claimscustomization3.png
[4]: ../media/active-directory-saml-claims-customization/claimscustomization4.png
