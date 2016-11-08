---
title: Acceso condicional de Azure Active Directory | Microsoft Docs
description: Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación.
services: active-directory
keywords: acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/21/2016
ms.author: markvi

---
# Acceso condicional de Azure Active Directory
La protección del acceso a los recursos de la empresa es importante para todas las organizaciones. Con la llegada de los dispositivos móviles y los servicios en la nube, han cambiado significativamente las formas en que los usuarios acceden a los recursos de la empresa. La proliferación de dispositivos personales y de empresa requiere un enfoque nuevo respecto al acceso a recursos corporativos y a la seguridad.

## ¿Por qué el acceso condicional?
Las funcionalidades de control del acceso condicional de Azure Active Directory ofrecen a las empresas formas sencillas de proteger sus recursos locales y en la nube. Las directivas de acceso condicional pueden proteger frente al riesgo de que las credenciales sean objeto de robo y suplantación de identidad (phishing) gracias a la autenticación multifactor. Asimismo, es posible aplicar directivas de acceso condicional para proteger los datos de la empresa. Con ello, solo se concedería acceso a servicios con contenido confidencial a los dispositivos inscritos en un sistema de administración de dispositivos móviles, como Microsoft Intune.

## Requisitos previos
El acceso condicional de Azure Active Directory es una característica de [Azure AD Premium](http://www.microsoft.com/identity). Todos los usuarios que tengan acceso a una aplicación a la que se ha aplicado la directiva de acceso condicional, deben tener una licencia de Azure AD Premium. Más información acerca del uso con el [informe de usuarios sin licencia](https://aka.ms/utc5ix).

## ¿Cómo se aplica el control de acceso condicional?
Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que elige al autenticar a un usuario, antes de permitirle el acceso a una aplicación. Cuando estos requisitos se cumplen, se autentica al usuario y se permite el acceso a la aplicación.

![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Condiciones
* **Pertenencia a un grupo**: control del nivel de acceso que se proporciona a un usuario en función de su pertenencia a un grupo.
* **Ubicación**: uso de la ubicación del usuario para desencadenar la autenticación multifactor (MFA) y bloquear controles cuando un usuario no se encuentre en una red de confianza.
* **Plataforma de dispositivo**: uso del tipo de plataforma del dispositivo, como iOS, Android, Windows Mobile y Windows, como condición para aplicar la directiva.
* **Dispositivo habilitado**: validación del estado de habilitación o deshabilitación del dispositivo durante la evaluación de la directiva. Si se deshabilita un dispositivo perdido o robado en el directorio, no se podrá volver a usar para cumplir los requisitos de la directiva.
* **Inicio de sesión y riesgo del usuario**: disponibilidad de directivas de riesgo del acceso condicional con [Azure AD Identity Protection](active-directory-identityprotection.md), que proporcionan protección avanzada en función de eventos de riesgo y actividades de inicio de sesión inusuales.

## Controles
* **Multi-Factor Authentication (MFA)**: puede requerir una autenticación sólida con MFA. La MFA puede proporcionarla Azure MFA o un proveedor de MFA local con Active Directory Federation Server (AD FS). La MFA contribuye a proteger el acceso a los recursos por parte de un usuario no autorizado que haya conseguido el nombre de usuario y la contraseña de un usuario válido.
* **Bloqueo**: posibilidad de aplicar condiciones como la ubicación del usuario para bloquear el acceso de este. Por ejemplo, se puede bloquear el acceso cuando un usuario no está en una red de confianza.
* **Dispositivos conformes**: posibilidad de establecer, en el nivel de dispositivo, directivas que otorguen acceso solo a los equipos unidos a un dominio o a los dispositivos móviles inscritos en una aplicación de administración de dispositivos móviles (MDM) y que, además, cumplan con los requisitos definidos. Por ejemplo, puede utilizarse Microsoft Intune con el fin de comprobar la conformidad de los dispositivos e informar del resultado a Azure Active Directory para que se apliquen las directivas durante el acceso a la aplicación. Para obtener instrucciones detalladas acerca de cómo usar Microsoft Intune para proteger los datos y las aplicaciones, consulte [Proteger aplicaciones y datos con Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/protect-apps-and-data-with-microsoft-intune). También es posible aplicar la protección de datos para dispositivos perdidos o robados mediante Microsoft Intune. Para obtener más información, consulte [Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune).

## Aplicaciones
* El nivel de acceso que se puede establecer mediante estas directivas se puede aplicar tanto servicios como a aplicaciones locales o en la nube. La directiva se aplica directamente al servicio o sitio web. Después, la directiva se exige tanto para al acceso del explorador como a las aplicaciones que acceden al servicio. Aquí se puede encontrar la lista de servicios a los que se pueden aplicar la directiva.

## Acceso condicional basado en dispositivos
También puede restringir el acceso a aplicaciones desde dispositivos que se registran con Azure AD que cumplen ciertas condiciones. El acceso condicional basado en dispositivos protege los recursos de la organización del acceso de usuarios desde los siguientes orígenes:

* Dispositivos desconocidos o no administrados.
* Dispositivos que no cumplen las directivas de seguridad tal como define la organización.

Las directivas se pueden establecer en función de los siguientes requisitos:

* **Dispositivos unidos a un dominio**: Puede establecer una directiva para restringir el acceso a dispositivos que están unidos a un dominio de Active Directory local y también están registrados con Azure AD. Esta directiva se aplica a los escritorios, equipos portátiles o tabletas de empresa con Windows que pertenecen a un dominio de Active Directory local que se han registrado con Azure AD. Para obtener más información sobre cómo configurar el registro automático de dispositivos unidos a dominio con Azure AD, consulte [Configuración del registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).
* **Dispositivos compatibles**: Puede establecer una directiva para restringir el acceso a los dispositivos que el sistema de administración marca como **compatibles**. Esta directiva garantiza que solo se permite el acceso a los dispositivos que cumplen las directivas de seguridad, como exigir el cifrado de archivos en un dispositivo. Esta directiva puede usarse para restringir el acceso desde los siguientes dispositivos:
  
  * **Dispositivos unidos a un dominio de Windows** administrados mediante System Center Configuration Manager (rama actual) en una configuración híbrida.
  * **Dispositivos móviles personales o profesionales con Windows 10** administrados mediante Windows Intune o un sistema compatible de administración de dispositivos móviles (MDM) de terceros.
  * **Dispositivos iOS y Android** administrados mediante Microsoft Intune.

Los usuarios que acceden a aplicaciones que están protegidas por la directiva de CA basado en dispositivo necesitan hacer esto desde dispositivos que cumplen esta directiva. El acceso se deniega cuando se realiza desde un dispositivo que no cumple la directiva.

Para información sobre cómo configurar la directiva de CA basado en dispositivo en Azure AD, consulte [How to configure Device-based Conditional Access policy for access control to Azure Active Directory connected applications](active-directory-conditional-access-policy-connected-applications.md) (Configurar la directiva de acceso condicional basado en dispositivo para control de acceso a aplicaciones conectadas a Azure Active Directory).

## Índice del artículo para acceso condicional de Azure Active Directory
El mapa de contenido siguiente enumera los documentos que debe consultar para obtener más información sobre la habilitación de acceso condicional en su implementación actual.

### Directivas de MFA y de ubicación
* [Introducción al acceso condicional a Azure AD](active-directory-conditional-access-azuread-connected-apps.md)
* [Compatibilidad con el acceso condicional de las aplicaciones](active-directory-conditional-access-supported-apps.md)

### Acceso condicional basado en dispositivos
* [Establecer una directiva de acceso condicional basado en dispositivo para control de acceso a aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)
* [Configurar el registro automático de dispositivos unidos a dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)
* [Corrección de usuario al acceder a aplicaciones protegidas con acceso condicional basado en dispositivos de Azure AD](active-directory-conditional-access-device-remediation.md)
* [Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)

### Protección de recursos basada en el riesgo de inicio de sesión
[Azure AD Identity Protection](active-directory-identityprotection.md)

### Información adicional
* [Preguntas más frecuentes sobre acceso condicional](active-directory-conditional-faqs.md)
* [Referencia técnica](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0928_2016-->