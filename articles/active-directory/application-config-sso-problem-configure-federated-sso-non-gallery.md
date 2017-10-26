---
title: "Problemas en la configuración del inicio de sesión único federado para una aplicación ajena a la galería | Microsoft Docs"
description: "Resolver algunos de los problemas comunes que pueden surgir al configurar un inicio de sesión único federado con una aplicación SAML personalizada que se muestre en la galería de aplicaciones de Azure AD"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: d8fb9c71ede7558d12ce3e22dcd359ed82527e43
ms.sourcegitcommit: 1131386137462a8a959abb0f8822d1b329a4e474
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/13/2017
---
# <a name="problem-configuring-federated-single-sign-on-for-a-non-gallery-application"></a>Problemas en la configuración del inicio de sesión único federado para una aplicación ajena a la galería

Si se produce un problema al configurar una aplicación. Compruebe que ha seguido todos los pasos del artículo [Configuración del inicio de sesión único en aplicaciones que no están en la galería de aplicaciones de Azure Active Directory.](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery)

## <a name="cant-add-another-instance-of-the-application"></a>No se puede agregar otra instancia de la aplicación

Para agregar una segunda instancia de una aplicación, tiene que poder:

-   Configurar un identificador único para la segunda instancia. No podrá configurar el mismo identificador que usó para la primera instancia.

-   Configurar un certificado diferente al utilizado para la primera instancia.

Si la aplicación no es compatible con cualquiera de los pasos anteriores. Entonces, no podrá configurar una segunda instancia.

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>¿Dónde se puede establecer el formato de EntityID (Identificador de usuario)?

No podrá seleccionar el formato de EntityID (Identificador de usuario) que Azure AD envía a la aplicación en la respuesta a la autenticación de usuario.

AD Azure selecciona el formato del atributo NameID (Identificador de usuario) en función del valor seleccionado o del formato que solicite la aplicación en el elemento AuthRequest de SAML. Para más información, visite la sección NameIDPolicy del artículo [Protocolo SAML de inicio de sesión único](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest).

## <a name="where-do-i-get-the-application-metadata-or-certificate-from-azure-ad"></a>¿Dónde se obtienen los metadatos de la aplicación o el certificado de Azure AD?

Para descargar el certificado o los metadatos de la aplicación de Azure AD, siga estos pasos:

1.  Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **Administrador global** o **Coadministrador**.

2.  Abra la **extensión de Azure Active Directory** haciendo clic en **More services** (Más servicios) en la parte inferior del menú de navegación izquierdo principal.

3.  Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.

4.  Haga clic en **Aplicaciones empresariales** en el menú de navegación izquierdo de Azure Active Directory.

5.  Haga clic en **Todas las aplicaciones** para ver una lista de todas las aplicaciones.

   * Si no ve la aplicación que desea que aparezca aquí, use el control **Filtro** de la parte superior de la lista **Todas las aplicaciones** y establezca la opción **Mostrar** en **Todas las aplicaciones.**

6.  Seleccione la aplicación para la que ha configurado el inicio de sesión único.

7.  Cuando se cargue la aplicación, haga clic en **Inicio de sesión único** desde el menú de navegación izquierdo de la aplicación.

8.  Vaya a la sección **Certificado de firma de SAML** y haga clic en el valor de la columna **Descargar**. Según lo que necesite la aplicación para configurar el inicio de sesión único, verá la opción para descargar el archivo XML de metadatos o el certificado.

Azure AD no proporciona una dirección URL para obtener los metadatos. Solo se pueden recuperar como un archivo XML.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>No sabe cómo personalizar las notificaciones SAML que se han enviado a una aplicación

Para obtener información sobre cómo personalizar las notificaciones de atributo SAML que se han enviado a su aplicación, vea [Asignación de notificaciones en Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping).

## <a name="next-steps"></a>Pasos siguientes
[Administración de aplicaciones con Azure Active Directory](active-directory-enable-sso-scenario.md)
