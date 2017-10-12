---
title: "Acceso condicional en el Portal de Azure clásico | Microsoft Docs"
description: "Use el control de acceso condicional en el Portal de Azure clásico para comprobar la existencia de condiciones específicas durante la autenticación para acceder a aplicaciones."
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: da3f0a44-1399-4e0b-aefb-03a826ae4ead
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: calebb
ms.custom: oldportal
ms.openlocfilehash: e93bfacfd74c0e8f34bc1bab372cc287193e992f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="conditional-access-in-the-azure-classic-portal"></a>Acceso condicional en el Portal de Azure clásico

Este tema trata sobre el acceso condicional en el Portal de Azure clásico. Para ver la información más reciente sobre el acceso condicional en Azure Active Directory, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).


Las funcionalidades de control del acceso condicional en Azure Active Directory (Azure AD) ofrecen maneras sencillas de proteger los recursos locales y en la nube. Las directivas de acceso condicional, como la autenticación multifactor, contribuyen a proteger frente al riesgo de que las credenciales sean objeto de robo y suplantación de identidad (phishing). Otras directivas de acceso condicional pueden ayudar a mantener seguros los datos de su organización. Por ejemplo, además de requerir credenciales, podría tener una directiva por la cual solo los dispositivos que estén inscritos en un sistema de administración de dispositivos móviles como Microsoft Intune puedan acceder a los servicios confidenciales de su organización.

## <a name="prerequisites"></a>Requisitos previos
El acceso condicional de Azure AD es una característica de [Azure Active Directory Premium](http://www.microsoft.com/identity). Cada usuario que acceda a una aplicación a la que se hayan aplicado directivas de acceso condicional debe tener una licencia de Azure AD Premium. Puede obtener más información sobre los requisitos de licencia en [Informe de uso sin licencia](https://aka.ms/utc5ix).

## <a name="how-is-conditional-access-control-enforced"></a>¿Cómo se aplica el control de acceso condicional?
Con el control de acceso condicional instaurado, Azure AD comprueba si se dan las condiciones específicas que establezca para que un usuario acceda a una aplicación. Una vez que estos requisitos se cumplen, se autentica al usuario, quien puede acceder a la aplicación.  

![Introducción al acceso condicional](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## <a name="conditions"></a>Condiciones
Puede incluir las siguientes condiciones en una directiva de acceso condicional:

* **Pertenencia a grupos**. Controle el acceso de un usuario en función de su pertenencia a un grupo.
* **Ubicación**. Use la ubicación del usuario para desencadenar la autenticación multifactor y use controles de bloqueo cuando un usuario no se encuentre en una red de confianza.
* **Plataforma de dispositivo**. Use la plataforma de dispositivo, como iOS, Android, Windows Mobile o Windows, como condición para aplicar la directiva.
* **Dispositivo habilitado**. El estado del dispositivo, habilitado o deshabilitado, se valida durante la evaluación de la directiva de dispositivo. Si deshabilita un dispositivo perdido o robado en el directorio, dejará de cumplir los requisitos de la directiva.
* **Inicio de sesión y riesgo de usuario**. Puede usar [Azure AD Identity Protection](active-directory-identityprotection.md) para directivas de riesgo de acceso condicional. Las directivas de riesgo de acceso condicional proporcionan a su organización una protección anticipada en función de eventos de riesgo y actividades de inicio de sesión no habituales.

## <a name="controls"></a>Controles
Estos son los controles que puede usar para aplicar una directiva de acceso condicional:

* **Autenticación multifactor**. Puede requerir una autenticación sólida por medio de la autenticación multifactor. Puede usar la autenticación multifactor con Azure Multi-Factor Authentication o mediante un proveedor de autenticación multifactor local, en combinación con Servicios de federación de Active Directory (AD FS). La autenticación multifactor ayuda a proteger los recursos ante el acceso por parte de un usuario no autorizado que haya conseguido el nombre de usuario y la contraseña de un usuario válido.
* **Bloqueo**. Puede aplicar condiciones como la ubicación del usuario para bloquear el acceso. Por ejemplo, puede bloquear el acceso cuando el usuario no esté en una red de confianza.
* **Dispositivos compatibles**. Puede establecer directivas de acceso condicional en el nivel de dispositivo. Podría configurar una directiva para que solo puedan acceder a los recursos de su organización aquellos equipos que estén unidos a un dominio o aquellos dispositivos móviles que estén inscritos en una aplicación de administración de dispositivos móviles. Por ejemplo, puede usar Intune para comprobar el cumplimiento del dispositivo y después notificarlo a Azure AD para que aplique la directiva cuando el usuario intente acceder a una aplicación. Para instrucciones detalladas sobre cómo usar Intune para proteger datos y aplicaciones, consulte [Proteger aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). También puede usar Intune para aplicar la protección de datos a dispositivos perdidos o robados. Para obtener más información, consulte [Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## <a name="applications"></a>Aplicaciones
Puede aplicar una directiva de acceso condicional en el nivel de aplicación. Establezca niveles de acceso para las aplicaciones y los servicios locales o en la nube. La directiva se aplica directamente al servicio o sitio web. La directiva se aplica para el acceso al explorador, y a las aplicaciones que acceden al servicio.

## <a name="device-based-conditional-access"></a>Acceso condicional basado en dispositivos
Puede restringir el acceso a aplicaciones desde dispositivos que estén registrados con Azure AD y que cumplan ciertas condiciones. El acceso condicional basado en dispositivos protege los recursos de una organización ante los usuarios que intentan acceder a ellos desde:

* Dispositivos desconocidos o no administrados.
* Dispositivos que no cumplan las directivas de seguridad de la organización.

Puede establecer directivas basadas en estos requisitos:

* **Dispositivos unidos a un dominio**. Establezca una directiva para restringir el acceso a dispositivos que estén unidos a un dominio local de Active Directory y que también estén registrados con Azure AD. Esta directiva se aplica a los escritorios, portátiles y tabletas de empresa Windows.
  Para más información sobre cómo configurar el registro automático de dispositivos unidos a un dominio con Azure AD, consulte [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Dispositivos compatibles**. Establezca una directiva para restringir el acceso a los dispositivos que estén marcados como **compatibles** en el directorio del sistema de administración. Esta directiva garantiza que solo se permite el acceso a los dispositivos que cumplen las directivas de seguridad, como exigir el cifrado de archivos en un dispositivo. Puede usar esta directiva para restringir el acceso desde los siguientes dispositivos:
  
  * **Dispositivos unidos a un dominio de Windows**. Administrados mediante System Center Configuration Manager (en la rama actual) en una configuración híbrida.
  * **Dispositivos Windows 10 Mobile para uso personal o profesional**. Administrados con Intune o un sistema de administración de dispositivos móviles de terceros compatible.
  * **Dispositivos iOS y Android**. Administrados con Intune.

Los usuarios que acceden a aplicaciones protegidas con una directiva de una entidad de certificación basada en dispositivos deben acceder a la aplicación desde un dispositivo que cumpla los requisitos de esta directiva. Se deniega el acceso si intentan acceder en un dispositivo que no cumpla los requisitos de la directiva.

Para información sobre cómo configurar una directiva de entidad de certificación basada en dispositivos en Azure AD, consulte [Establecimiento de una directiva de acceso condicional basado en dispositivos para aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md).

## <a name="resources"></a>Recursos
Consulte las categorías de recursos y los artículos siguientes para más información sobre cómo establecer el acceso condicional para su organización.

### <a name="multi-factor-authentication-and-location-policies"></a>Directivas de ubicación y autenticación multifactor
* [Introducción al acceso condicional a aplicaciones conectadas a Azure AD según directivas de grupo, ubicación y autenticación multifactor](active-directory-conditional-access-azuread-connected-apps.md)
* [Aplicaciones y navegadores compatibles](active-directory-conditional-access-supported-apps.md)

### <a name="device-based-conditional-access"></a>Acceso condicional basado en dispositivos
* [Establecimiento de una directiva de acceso condicional basado en dispositivos para aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Solución de problemas de acceso de Azure Active Directory](active-directory-conditional-access-device-remediation.md)
* [Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### <a name="protect-resources-based-on-sign-in-risk"></a>Protección de recursos basada en el riesgo de inicio de sesión
* [Azure AD Identity Protection](active-directory-identityprotection.md)

### <a name="next-steps"></a>Pasos siguientes
* [Preguntas más frecuentes acerca del acceso condicional](active-directory-conditional-faqs.md)
* [Referencia técnica](active-directory-conditional-access-technical-reference.md)

