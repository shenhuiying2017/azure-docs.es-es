---
title: P+F de Azure Active Directory | Microsoft Docs
description: "P+F de Azure Active Directory que proporciona respuestas a preguntas relativas al acceso a Azure y Azure Active Directory, la administración de contraseñas y el acceso a aplicaciones."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: b8207760-9714-4871-93d5-f9893de31c8f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2016
ms.author: markusvi
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 0f7070d9d691e2471978a2986025ebfdafbeaa7c


---
# <a name="azure-active-directory-faq"></a>P+F de Azure Active Directory
Azure Active Directory es una completa solución de identidad como servicio (IDaaS) que abarca todos los aspectos de la identidad, la administración de acceso y la seguridad.

Para más información, consulte [¿Qué es Azure Active Directory?](active-directory-whatis.md)

## <a name="accessing-azure-and-azure-active-directory"></a>Acceso a Azure y Azure Active Directory
**P: ¿Por qué veo "No se encontraron suscripciones" cuando intento acceder a Azure AD en el Portal de Azure clásico (https://manage.windowsazure.com)?**

**R:** Para acceder al Portal de Azure clásico, cada usuario debe tener permisos en una suscripción de Azure. Si tiene Office 365 o Azure AD de pago, vaya a [http://aka.ms/accessAAD](http://aka.ms/accessAAD) para la activación por única vez; de lo contrario, debe activar una [evaluación gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/) completa o una suscripción de pago. 

Para obtener información, consulte:

* [Asociación de las suscripciones de Azure con Azure Active Directory](active-directory-how-subscriptions-associated-directory.md)
* [Administración del directorio para la suscripción de Office 365 en Azure](active-directory-manage-o365-subscription.md)

- - -
**P: ¿Cuál es la relación entre AD de Azure, Office 365 y Azure?**

**R:** Azure Active Directory ofrece funcionalidades de acceso e identidad comunes a todos los servicios de Microsoft Online Services. Tanto si usa Office 365 como Microsoft Azure, Intune u otros servicios, ya está utilizando Azure AD para habilitar la administración del acceso y el inicio de sesión de todos estos servicios. 

De hecho, todos los usuarios que haya habilitado para Microsoft Online Services están definidos como cuentas de usuario en una o más instancias de Azure AD. Puede habilitar gratis en estas cuentas funcionalidades de Azure AD, como el acceso a aplicaciones en la nube.

Además, los servicios de pago de Azure AD (por ejemplo, Azure AD Básico, Premium, EMS, etc.) complementan a otros servicios de Online Services, como Office 365 y Microsoft Azure, con completas soluciones de administración y seguridad para empresas.

- - -
## <a name="getting-started-with-hybrid-azure-ad"></a>Introducción a Azure AD híbrido
**P: ¿Cómo conecto mi directorio local a Azure AD?**

**R:** Puede conectar su directorio local a Azure AD mediante **Azure AD Connect**. 

Para más información, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

- - -
**P: ¿Cómo se configura el SSO entre mi directorio local y las aplicaciones de nube?**

**R:** Solo es preciso configurar el SSO entre el directorio local y Azure AD. Mientras tenga acceso a las aplicaciones en la nube mediante Azure AD, el servicio lleva automáticamente a los usuarios a autenticarse correctamente con sus credenciales locales.

La implementación del SSO desde el entorno local se puede lograr fácilmente con soluciones de federación como ADFS o configurando la sincronización de los hash de contraseña. Puede implementar fácilmente ambas opciones con el Asistente para configuración de Azure AD Connect.

Para más información, consulte [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

- - -
**P: ¿Proporciona Azure Active Directory un portal de autoservicio para usuarios en mi organización?**

**R:** Sí, Azure Active Directory proporciona el [panel de acceso de Azure AD](http://myapps.microsoft.com) para el autoservicio de los usuarios y el acceso a las aplicaciones. Si es cliente de Office 365, encontrará muchas de las mismas funcionalidades en el portal de Office 365. 

Para más información, consulte [Introducción al panel de acceso](active-directory-saas-access-panel-introduction.md). 

- - -
**P: ¿Me ayuda Azure AD a administrar la infraestructura local?**

**R:** Efectivamente. La edición Azure AD Premium incluye **Connect Health**. Azure AD Connect Health le ayuda a supervisar y a comprender mejor su infraestructura de identidad local y los servicios de sincronización.  

Para más información, consulte [Supervisión de la infraestructura de identidad local y los servicios de sincronización en la nube](active-directory-aadconnect-health.md).  

- - -
## <a name="password-management"></a>Administración de contraseñas
**P: ¿Puedo usar la escritura diferida de contraseñas de Azure AD sin sincronización de contraseñas? (Es decir, me gustaría usar SSPR de Azure AD con la escritura diferida de contraseñas, pero no quiero que mis contraseñas se almacenen en la nube)**

**R:** No es necesario sincronizar las contraseñas de AD con Azure AD para habilitar la escritura diferida. En un entorno federado, el SSO Azure AD se basa en el directorio local para autenticar al usuario. En este caso no se necesita la contraseña local para realizar el seguimiento en Azure AD.

- - -
**P: ¿Cuánto se tarda en escribir una contraseña en diferido en AD local?**

**R:** La escritura diferida de contraseñas funciona en tiempo real. 

Para más información, consulte [Introducción a la administración de contraseñas](active-directory-passwords-getting-started.md) 

- - -
**P: ¿Puedo usar la escritura diferida de contraseñas con las que administra un administrador?**

**R:** Sí, si tiene la escritura diferida de contraseñas habilitada, las operaciones de contraseña realizadas por un administrador se escriben de manera diferida en el entorno local.  

Para ver más respuestas a preguntas relativas a las contraseñas, consulte [Preguntas más frecuentes sobre la administración de contraseñas](active-directory-passwords-faq.md).

- - -
## <a name="application-access"></a>Acceso a las aplicaciones
**P: ¿Dónde puedo encontrar una lista de las aplicaciones preintegradas en Azure AD y sus funcionalidades?**

**R:** Azure AD cuenta con más de 2600 aplicaciones preintegradas de Microsoft, proveedores de servicios de aplicaciones o asociados. Todas las aplicaciones preintegradas deben ser compatibles con el SSO. El SSO permite utilizar las credenciales de su organización para acceder a las aplicaciones. Algunas de las aplicaciones también admiten el aprovisionamiento y desaprovisionamiento automatizados.

Para ver una lista exhaustiva de las aplicaciones preintegradas, consulte [Active Directory Marketplace](https://azure.microsoft.com/marketplace/active-directory/).

- - -
**P: ¿Qué ocurre si no encuentro la aplicación que necesito en Marketplace de Azure AD?**

**R:** Con Azure AD Premium, puede agregar y configurar cualquier aplicación. Según las funcionalidades de la aplicación y sus preferencias, puede configurar el SSO y el aprovisionamiento automatizado.  

Para obtener información, consulte:

* [Configuración del inicio de sesión único en aplicaciones que no están en la Galería de aplicaciones de Azure Active Directory](active-directory-saas-custom-apps.md)
* [Uso de SCIM para habilitar el aprovisionamiento automático de usuarios y grupos de Azure Active Directory a aplicaciones](active-directory-scim-provisioning.md) 

- - -
**P: ¿Cómo inician los usuarios sesión en aplicaciones con Azure Active Directory?**

**R:** Azure Active Directory proporciona varias maneras de que los usuarios vean y accedan a sus aplicaciones, como:

* El Panel de acceso de Azure AD
* El iniciador de aplicaciones de Office 365
* Inicio de sesión directo en aplicaciones federadas
* Vínculos profundos a aplicaciones federadas, con contraseña o existentes

Para más información, consulte [Implementación de aplicaciones integradas en Azure AD a los usuarios](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users).

- - -
**P: ¿Cuáles son las distintas formas en que Azure Active Directory permite la autenticación y el inicio de sesión único en aplicaciones?**

**R:** Azure Active Directory admite muchos protocolos estandarizados para la autenticación y la autorización, como SAML 2.0, OpenID Connect, OAuth 2.0 y WS-Federation. Además, Azure AD admite las funcionalidades de inicio de sesión automatizado y de almacenamiento de contraseñas para las aplicaciones que solo sean compatibles con la autenticación basada en formularios.  

Para más información, consulte:

* [Escenarios de autenticación para Azure AD](active-directory-authentication-scenarios.md)
* [Protocolos de autenticación de Azure Active Directory](https://msdn.microsoft.com/library/azure/dn151124.aspx)
* [¿Cómo funciona el inicio de sesión único con Azure Active Directory?](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work)

- - -
**P: ¿Puedo agregar aplicaciones que ejecuto de manera local?**

**R:** El proxy de aplicación de Azure AD proporciona un acceso fácil y seguro a las aplicaciones web locales que elija. Puede acceder a ellas como si se trataran de sus aplicaciones SaaS en Azure Active Directory. No se necesita una VPN ni cambiar la infraestructura de red.  

Para más información, consulte [Provisión de acceso remoto seguro a aplicaciones locales](active-directory-application-proxy-get-started.md).

- - -
**P: ¿Cómo solicito MFA para que los usuarios accedan a una aplicación determinada?**

**R:** Con el acceso condicional de Azure AD, puede asignar una directiva de acceso única a cada aplicación. En la directiva, puede solicitar MFA siempre o cuando los usuarios no estén conectados a la red local.  

Para más información, consulte [Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access.md).

- - -
**P: ¿Qué es el aprovisionamiento automático de usuarios para aplicaciones SaaS?**

**R:** Azure Active Directory permite automatizar la creación, el mantenimiento y la eliminación de identidades de usuario en muchas aplicaciones (SaaS) en la nube conocidas. 

Para más información, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md)

- - -



<!--HONumber=Nov16_HO2-->


