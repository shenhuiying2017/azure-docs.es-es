---
title: "Administración de inicio de sesión único para aplicaciones empresariales en la versión preliminar de Azure Active Directory | Microsoft Docs"
description: "Aprenda a administrar el inicio de sesión único para aplicaciones empresariales con la versión preliminar de Azure Active Directory."
services: active-directory
documentationcenter: 
author: asmalser
manager: femila
editor: 
ms.assetid: bcc954d3-ddbe-4ec2-96cc-3df996cbc899
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/30/2016
ms.author: asmalser
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 2f03079498568f52802b34ce57242a414e648fe3


---
# <a name="preview-managing-single-sign-on-for-enterprise-apps-in-the-new-azure-portal"></a>Versión preliminar: Administración de inicio de sesión único para aplicaciones empresariales en el nuevo Azure Portal
> [!div class="op_single_selector"]
> * [Azure Portal](active-directory-enterprise-apps-manage-sso.md)
> * [Portal de Azure clásico](active-directory-sso-integrate-saas-apps.md)
> 
> 

En este artículo se describe cómo utilizar [Azure Portal](https://portal.azure.com) para administrar la configuración de inicio de sesión único para aplicaciones, especialmente las que se han agregado desde la [Galería de aplicaciones de Azure Active Directory (Azure AD)](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). La experiencia de administración de Azure AD para el inicio de sesión único está actualmente en versión preliminar pública y en este artículo se describen las nuevas características, así como algunas limitaciones temporales que tienen lugar durante el período de versión preliminar. [¿Qué hay en la versión preliminar?](active-directory-preview-explainer.md)

## <a name="finding-your-apps-in-the-new-portal"></a>Búsqueda de las aplicaciones en el nuevo portal
A partir de septiembre de 2016, todas las aplicaciones que se han configurado para un inicio de sesión único en un directorio, por un administrador de directorio mediante la [Galería de aplicaciones de Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) dentro del [Portal de Azure clásico](https://manage.windowsazure.com), ahora se pueden ver y administrar en Azure Portal.

Estas aplicaciones se pueden encontrar en la sección **Aplicaciones empresariales** del nuevo Azure Portal, un vínculo que se puede encontrar en la lista **Más servicios** del [portal](https://portal.azure.com). Las aplicaciones empresariales son aplicaciones que se han implementado y se están utilizando por los usuarios de su organización.

![Hoja Aplicaciones empresariales][1]

Seleccione **Todas las aplicaciones** para ver una lista de todas las aplicaciones que se han configurado, incluidas las aplicaciones que se han añadido desde la galería. Si selecciona una aplicación, se carga la hoja de recursos para esa aplicación, donde se pueden ver los informes de dicha aplicación y se pueden administrar diversas opciones de configuración.

Para administrar la configuración de inicio de sesión único, seleccione **Inicio de sesión único**.

![Hoja Recursos de aplicación][2]

## <a name="single-sign-on-modes"></a>Modos de inicio de sesión único
La hoja **Inicio de sesión único** comienza con un menú **Modo**, que permite la configuración del modo de inicio de sesión único. Las opciones disponibles incluyen:

* **SAML-based sign on** (Inicio de sesión basado en SAML): esta opción está disponible si la aplicación admite el inicio de sesión único federado completo con Azure Active Directory mediante el protocolo SAML 2.0.
* **Password-based sign on** (Inicio de sesión basado en contraseña): esta opción está disponible si Azure AD admite el rellenado de formularios de contraseña para esta aplicación.
* **Inicio de sesión vinculado**: antes conocida como "Inicio de sesión único existente", esta opción permite a los administradores agregar un vínculo a esta aplicación en el Panel de acceso de Azure AD o el iniciador de aplicaciones de Office 365 del usuario.

Para más información acerca de estos modos, consulte la sección [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>SAML-based sign on
La opción **SAML-based sign on** (Inicio de sesión basado en SAML) muestra una hoja dividida en cuatro secciones:

### <a name="domains-and-urls"></a>Dominios y direcciones URL
Aquí es donde se agregan todos los detalles sobre las direcciones URL y el dominio de la aplicación a su directorio de Azure AD. Todas las entradas necesarias para hacer funciona la aplicación de inicio de sesión único se muestran directamente en la pantalla, mientras que todas las entradas opcionales se pueden ver mediante la selección de la casilla **Show advanced URL settings** (Mostrar configuración de URL avanzada). La lista completa de las entradas admitidas es la siguiente:

* **URL de inicio de sesión** : cuando el usuario va a iniciar sesión en esta aplicación. Si la aplicación está configurada para realizar el inicio de sesión único iniciado por el proveedor de servicios, cuando un usuario navega a esta dirección URL, el proveedor de servicios realiza la redirección necesaria a Azure AD para autenticar al usuario e iniciar sesión. Si este campo se rellena, Azure AD utilizará esta dirección URL para iniciar la aplicación desde el panel de acceso de Azure AD y Office 365. Si se omite este campo, Azure AD realiza un inicio de sesión iniciado por el proveedor de identidades cuando se inicie la aplicación de Office 365, el panel de acceso de Azure AD, o desde la dirección URL de inicio de sesión único de Azure AD.
* **Identificador**: este URI debería identificar de forma exclusiva la aplicación para el inicio de sesión único que se está configurando. Este es el valor que Azure AD devuelve a la aplicación como el parámetro Audiencia del token SAML, y se espera que la aplicación lo valide. Este valor también aparece como el id. de entidad en los metadatos SAML proporcionados por la aplicación.
* **URL de respuesta** : la dirección URL de respuesta es el lugar donde la aplicación espera recibir el token SAML. Esto también se conoce como dirección URL del Servicio de consumidor de aserciones (ACS). Después de especificar dichas direcciones, haga clic en Siguiente para pasar a la pantalla siguiente. Esta pantalla proporciona información sobre lo que es preciso configurar en la propia aplicación para que pueda aceptar un token SAML de Azure AD.
* **Estado de la retransmisión**: el estado de retransmisión es un parámetro opcional que puede ayudar a indicar dónde redirigir al usuario una vez completada la autenticación de la aplicación. Por lo general, el valor suele ser una dirección URL válida en la aplicación; sin embargo, algunas aplicaciones usan este campo de forma diferente (consulte la documentación de inicio de sesión único de la aplicación para más información). La capacidad de establecer el estado de retransmisión es una característica nueva exclusiva para el nuevo Azure Portal.

### <a name="user-attributes"></a>Atributos de usuario
Aquí es donde los administradores pueden ver y modificar los atributos que se envían en el token SAML que emite Azure AD a la aplicación cada vez que los usuarios inician sesión.

Para la primera versión preliminar, el único atributo admitido que se puede modificar es el atributo **Identificador de usuario** . El valor de este atributo es el campo de Azure AD que identifica de forma única a cada usuario dentro de la aplicación. Por ejemplo, si la aplicación se ha implementado mediante la "dirección de correo electrónico" como nombre de usuario y un identificador único, el valor se establecería en el campo "user.mail" de Azure AD.

En una versión preliminar posterior se admitirá la modificación de atributos adicionales.

### <a name="saml-signing-certificate"></a>Certificado de firma SAML
En esta sección se muestran los detalles del certificado que Azure AD usa para firmar los tokens SAML que se emiten a la aplicación cada vez que el usuario se autentica. Permite la inspección de las propiedades del certificado actual, incluida la fecha de caducidad.

En una versión preliminar posterior se admitirá la capacidad de sustituir el certificado y de administrar opciones opcionales de certificados. Tenga en cuenta que la administración completa de certificados todavía puede realizarse en el [Portal de Azure clásico](active-directory-sso-certs.md).

### <a name="application-configuration"></a>Configuración de aplicaciones
La sección final proporciona la documentación y los controles necesarios para configurar la aplicación para que use Azure Active Directory como proveedor de identidades.

El menú emergente **Configurar aplicación** proporciona nuevas instrucciones concisas e insertadas para configurar la aplicación. Esta es otra característica nueva y exclusiva en el nuevo Azure Portal.

> [!NOTE]
> Para ver un ejemplo completo de documentación insertada, consulte la aplicación Salesforce.com. Se está agregando continuamente documentación de las aplicaciones adicionales durante la versión preliminar.
> 
> 

![Documentos insertados][3]

## <a name="password-based-sign-on"></a>Password-based sign on
Si se admite para la aplicación, si se selecciona el modo SSO basado en contraseña y se selecciona **Guardar** , se configura instantáneamente para el inicio de sesión único basado en contraseña. Para más información sobre la implementación del inicio de sesión único basado en contraseña, consulte la sección [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Password-based sign on][4]

## <a name="linked-sign-on"></a>Linked sign on
Si se admite para la aplicación, la selección del modo de inicio de sesión único vinculado permite especificar la dirección URL a la que desea que redirijan el Panel de acceso de Azure AD u Office 365 cuando los usuarios hacen clic en esta aplicación. Para más información sobre el inicio de sesión único vinculado, consulte la sección [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Inicio de sesión vinculado][5]

[1]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.PNG
[2]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.PNG
[3]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.PNG
[4]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.PNG
[5]: ./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.PNG



<!--HONumber=Dec16_HO4-->


