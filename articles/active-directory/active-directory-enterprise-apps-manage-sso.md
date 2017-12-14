---
title: "Administración del inicio de sesión único para aplicaciones empresariales en Azure Active Directory | Microsoft Docs"
description: "Administre la configuración de inicio de sesión único para aplicaciones empresariales de su organización desde la galería de aplicaciones de Azure Active Directory."
services: active-directory
documentationcenter: 
author: curtand
manager: mtillman
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: curtand
ms.reviewer: asmalser
ms.openlocfilehash: 61579af23acf466a11ae59832a02cb75ba39dc26
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Administración de inicio de sesión único para aplicaciones empresariales

En este artículo se describe cómo utilizar [Azure Portal](https://portal.azure.com) para administrar la configuración de inicio de sesión único para aplicaciones empresariales. Las aplicaciones empresariales son aplicaciones que se implementan y se usan dentro de su organización. Este artículo se aplica especialmente a las aplicaciones agregadas desde la [Galería de aplicaciones de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Búsqueda de aplicaciones en el portal
Todas las aplicaciones empresariales configuradas para el inicio de sesión único se pueden ver y administrar en Azure Portal. Las aplicaciones pueden encontrarse en la sección **Más servicios** &gt; **Aplicaciones empresariales** del portal. 

![Hoja Aplicaciones empresariales][1]

Seleccione **Todas las aplicaciones** para ver una lista de todas las aplicaciones configuradas. Al seleccionar una aplicación se muestran los recursos de esa aplicación, donde se pueden ver informes de ella y se pueden administrar diversas opciones de configuración.

Para administrar la configuración de inicio de sesión único, seleccione **Inicio de sesión único**.

![Hoja Recursos de aplicación][2]

## <a name="single-sign-on-modes"></a>Modos de inicio de sesión único
La hoja **Inicio de sesión único** comienza con un menú **Modo**, que permite la configuración del modo de inicio de sesión único. Las opciones disponibles incluyen:

* **Inicio de sesión basado en SAML**: esta opción está disponible si la aplicación admite el inicio de sesión único federado completo con Azure Active Directory mediante el protocolo SAML 2.0, WS-Federation u otros protocolos de conexión OpenID.
* **Inicio de sesión con contraseña**: esta opción está disponible si Azure AD admite el rellenado de formularios de contraseña para esta aplicación.
* **Inicio de sesión vinculado**: antes conocida como "Inicio de sesión único existente", esta opción permite a los administradores agregar un vínculo a esta aplicación en el Panel de acceso de Azure AD o el iniciador de aplicaciones de Office 365 del usuario.

Para más información acerca de estos modos, consulte la sección [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>Inicio de sesión basado en SAML
La opción **Inicio de sesión basado en SAML** se divide en cuatro secciones:

### <a name="domains-and-urls"></a>Dominios y direcciones URL
Aquí es donde se agregan todos los detalles sobre las direcciones URL y el dominio de la aplicación a su directorio de Azure AD. Todas las entradas necesarias para hacer funciona la aplicación de inicio de sesión único se muestran directamente en la pantalla, mientras que todas las entradas opcionales se pueden ver mediante la selección de la casilla **Show advanced URL settings** (Mostrar configuración de URL avanzada). La lista completa de las entradas admitidas es la siguiente:

* **URL de inicio de sesión**: donde va el usuario para iniciar sesión en la aplicación. Si la aplicación está configurada para realizar el inicio de sesión único iniciado por el proveedor de servicios, cuando un usuario abre esta dirección URL, el proveedor de servicios le redirige a Azure AD para autenticarse e iniciar sesión. 
  * Si este campo se rellena, Azure AD usa la dirección URL para iniciar la aplicación desde el Panel de acceso de Azure AD y Office 365.
  * Si se omite este campo, Azure AD realiza un inicio de sesión iniciado por el proveedor de identidades cuando se inicia la aplicación de Office 365, el Panel de acceso de Azure AD o desde la dirección URL de inicio de sesión único de Azure AD.
* **Identificador**: este URI debería identificar de forma exclusiva la aplicación para la que se configura el inicio de sesión único. Este es el valor que Azure AD devuelve a la aplicación como el parámetro Audiencia del token SAML, y se espera que la aplicación lo valide. Este valor también aparece como el id. de entidad en los metadatos SAML proporcionados por la aplicación.
* **URL de respuesta** : la dirección URL de respuesta es el lugar donde la aplicación espera recibir el token SAML. Esto también se conoce como dirección URL del Servicio de consumidor de aserciones (ACS). Después de especificar dichas direcciones, haga clic en Siguiente para pasar a la pantalla siguiente. Esta pantalla proporciona información sobre lo que es preciso configurar en la propia aplicación para que pueda aceptar un token SAML de Azure AD.
* **Estado de la retransmisión**: el estado de retransmisión es un parámetro opcional que puede ayudar a indicar dónde redirigir al usuario una vez completada la autenticación de la aplicación. Por lo general, el valor suele ser una dirección URL válida en la aplicación; sin embargo, algunas aplicaciones usan este campo de forma diferente (consulte la documentación de inicio de sesión único de la aplicación para más información). La capacidad de establecer el estado de retransmisión es una característica nueva exclusiva para el nuevo Azure Portal.

### <a name="user-attributes"></a>Atributos de usuario
Aquí es donde los administradores pueden ver y modificar los atributos que se envían en el token SAML que emite Azure AD a la aplicación cada vez que los usuarios inician sesión.

El único atributo editable admitido es el atributo **Identificador de usuario**. El valor de este atributo es el campo de Azure AD que identifica de forma única a cada usuario dentro de la aplicación. Por ejemplo, si la aplicación se ha implementado mediante la "dirección de correo electrónico" como nombre de usuario y un identificador único, el valor se establecería en el campo "user.mail" de Azure AD.

### <a name="saml-signing-certificate"></a>Certificado de firma SAML
En esta sección se muestran los detalles del certificado que Azure AD usa para firmar los tokens SAML que se emiten a la aplicación cada vez que el usuario se autentica. Permite la inspección de las propiedades del certificado actual, incluida la fecha de caducidad.

### <a name="application-configuration"></a>Configuración de aplicaciones
La sección final proporciona la documentación y los controles necesarios para configurar la aplicación para que use Azure Active Directory como proveedor de identidades.

El menú emergente **Configurar aplicación** proporciona nuevas instrucciones concisas e insertadas para configurar la aplicación. Esta es otra característica nueva y exclusiva en el nuevo Azure Portal.

> [!NOTE]
> Para ver un ejemplo completo de documentación insertada, consulte la aplicación Salesforce.com. Continuamente se agrega documentación de otras aplicaciones.
> 
> 

![Documentos insertados][3]

## <a name="password-based-sign-on"></a>Inicio de sesión único con contraseña
Si se admite para la aplicación, si se selecciona el modo SSO basado en contraseña y se selecciona **Guardar** , se configura instantáneamente para el inicio de sesión único basado en contraseña. Para más información sobre la implementación del inicio de sesión único basado en contraseña, consulte la sección [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Inicio de sesión único con contraseña][4]

## <a name="linked-sign-on"></a>Inicio de sesión vinculado
Si se admite para la aplicación, la selección del modo de inicio de sesión único vinculado permite especificar la dirección URL a la que desea que redirijan el Panel de acceso de Azure AD u Office 365 cuando los usuarios hacen clic en esta aplicación. Para más información sobre el inicio de sesión único vinculado, consulte la sección [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Inicio de sesión vinculado][5]

##<a name="feedback"></a>Comentarios

Esperamos que le guste usa la experiencia mejorada de Azure AD. Envíenos sus comentarios. Publique sus comentarios y sugerencias para la mejora en la sección **Portal de administración** de nuestro [foro de comentarios](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Nos interesa cómo crear innovaciones estupendas todos los días y usamos sus comentarios para dar forma y definir qué será lo próximo que crearemos.

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG
