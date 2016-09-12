<properties
	pageTitle="Acceso condicional de Azure Active Directory | Microsoft Azure"  
    description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."  
    services="active-directory" 
	keywords="acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional" 
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="08/25/2016"
	ms.author="markvi"/>


# Acceso condicional de Azure Active Directory   
  
La protección del acceso a los recursos de la empresa es importante para todas las organizaciones. Con la llegada de los dispositivos móviles y los servicios en la nube, han cambiado significativamente las formas en que los usuarios acceden a los recursos de la empresa. Esto requiere un enfoque nuevo de la seguridad.
  
## ¿Por qué el acceso condicional?  

Las funcionalidades de control del acceso condicional de Azure Active Directory ofrecen a las empresas varias maneras sencillas de proteger sus recursos locales y en la nube. Las directivas de acceso condicional se pueden utilizar como ayuda para la protección contra el riesgo de robo y suplantación de credenciales, ya que requieren la autenticación multifactor, así como ayudarle a mantener a salvo los datos de la compañía, ya que requiere que un dispositivo administrado por Intune conceda el acceso a los servicios confidenciales.



## Requisitos de licencia

El acceso condicional es una característica de [Azure AD Premium](http://www.microsoft.com/identity). Todos los usuarios que tengan acceso a una aplicación a la que se ha aplicado la directiva de acceso condicional, deben tener una licencia de Azure AD Premium. Más información acerca del uso con el [informe de usuarios sin licencia](https://aka.ms/utc5ix).





## ¿Cómo se aplica el control de acceso condicional?  

Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que elige al autenticar a un usuario, antes de permitirle el acceso a una aplicación. Cuando estos requisitos se cumplen, se autentica al usuario y se permite el acceso a la aplicación.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Condiciones
  
- **Pertenencia a un grupo**: El nivel de acceso que se proporciona a un usuario en función de su pertenencia a un grupo.

- **Ubicación**: la ubicación del usuario se puede utilizar para desencadenar MFA y bloquear controles cuando un usuario no está en una red de confianza.

- **Plataforma de dispositivo**: se puede usar el tipo de plataforma del dispositivo, como iOS, Android, Windows Mobile y Windows como condición para aplicar la directiva.

- **Dispositivo habilitado**: el estado de habilitación o deshabilitación del dispositivo se valida durante la evaluación de la directiva. Si se deshabilita un dispositivo perdido o robado en el directorio, no se podrá volver a usar para cumplir los requisitos de la directiva.

- **Inicio de sesión y riesgo del usuario**: las directivas de riesgo del acceso condicional están disponibles en Azure AD Identity Protection y proporcionan protección avanzada basada en eventos de riesgo y actividades de inicio de sesión inusuales.


## Controles
   
- **Multi-Factor Authentication (MFA)**: puede requerir una autenticación segura con MFA. MFA puede proporcionarla Azure MFA o un proveedor de MFA local con ADFS. La autenticación de MFA sirve de ayuda para proteger el acceso a los recursos por parte de un usuario no autorizado que haya obtenido acceso al nombre de usuario y a la contraseña de un usuario válido.

- **Bloqueo**: el acceso se puede aplicar en función de condiciones como la ubicación del usuario. Por ejemplo, se puede bloquear el acceso cuando un usuario no está en una red de confianza.

- **Dispositivos compatibles con**: en el ámbito de dispositivo, puede establecer directivas que exijan que solamente aquellos equipos que estén unidos a un dominio o los dispositivos móviles que estén inscritos en la administración de dispositivos móviles (MDM) y cumplan las normas, se les permita el acceso. Microsoft Intune se utiliza para comprobar el cumplimiento en dispositivos y volver a notificarlo a Azure Active Directory para que se aplique durante el acceso a la aplicación.
 

## Aplicaciones

- El nivel de acceso que se puede establecer mediante estas directivas se puede aplicar tanto servicios como a aplicaciones locales o en la nube. La directiva se aplica directamente al sitio web o servicio. Después, la directiva se exige tanto para al acceso del explorador como a las aplicaciones que acceden al servicio. Aquí se puede encontrar la lista de servicios a los que se pueden aplicar la directiva.


## Acceso condicional basado en dispositivos

También puede restringir el acceso a aplicaciones desde dispositivos que se registran con Azure AD que cumplen ciertas condiciones. Esto resulta útil para proteger recursos de la organización contra usuarios válidos que acceden a estos recursos desde:

- Dispositivos desconocidos o no administrados
- Dispositivos que no cumplen las directivas de seguridad tal como define la organización.

Las directivas se pueden establecer en función de los siguientes requisitos:

- **Dispositivos unidos a un dominio**: Puede establecer una directiva para restringir el acceso a dispositivos que están unidos a un dominio de Active Directory local y también están registrados con Azure AD. Esta directiva se aplica a los escritorios, equipos portátiles o tabletas de empresa con Windows que pertenecen a un dominio de Active Directory local que se han registrado con Azure AD. Para obtener más información sobre cómo configurar el registro automático de dispositivos unidos a dominio con Azure AD, consulte [How to setup automatic Registration of Windows domain joined devices with Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md) (Cómo configurar el registro automático de dispositivos unidos a un dominio de Windows con Azure Active Directory).

- **Dispositivos compatibles**: Puede establecer una directiva para restringir el acceso a los dispositivos que el sistema de administración marca como **compatibles**. Esta directiva garantiza que solo se permite el acceso a los dispositivos que cumplen las directivas de seguridad, como exigir el cifrado de archivos en un dispositivo. Esta directiva puede usarse para restringir el acceso desde los siguientes dispositivos:

    - **Dispositivos unidos a un dominio de Windows** que administra System Center Configuration Manager (rama actual) en una configuración híbrida.

    - **Dispositivos móviles personales o de trabajo con Windows 10** que administra Windows Intune o un sistema compatible de administración de dispositivos móviles (MDM) de terceros.

    - **Dispositivos iOS y Android** que administra Microsoft Intune.


Los usuarios que acceden a aplicaciones que están protegidas por la directiva de CA basado en dispositivo necesitan hacer esto desde dispositivos que cumplen esta directiva. El acceso se deniega cuando se realiza desde un dispositivo que no cumple la directiva.

Para información sobre cómo configurar la directiva de CA basado en dispositivo en Azure AD, consulte [How to configure Device-based Conditional Access policy for access control to Azure Active Directory connected applications](active-directory-conditional-access-policy-connected-applications.md) (Configurar la directiva de acceso condicional basado en dispositivo para control de acceso a aplicaciones conectadas a Azure Active Directory).

## Índice del artículo para acceso condicional de Azure Active Directory
  
El mapa de contenido siguiente enumera los documentos que debe consultar para obtener más información sobre la habilitación de acceso condicional en su implementación actual.


### Directivas de MFA y de ubicación

- [Introducción al acceso condicional a Azure AD](active-directory-conditional-access-azuread-connected-apps.md)

- [Compatibilidad con el acceso condicional de las aplicaciones](active-directory-conditional-access-supported-apps.md)


### Acceso condicional basado en dispositivos

- [Establecer una directiva de acceso condicional basado en dispositivo para control de acceso a aplicaciones conectadas a Azure Active Directory](active-directory-conditional-access-policy-connected-applications.md)

- [Configurar el registro automático de dispositivos unidos a dominio de Windows con Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md)

- [Corrección de usuario al acceder a aplicaciones protegidas con acceso condicional basado en dispositivos de Azure AD](active-directory-conditional-access-device-remediation.md)

- [Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### Protección de recursos basada en el riesgo de inicio de sesión

[Azure AD Identity Protection](active-directory-identityprotection.md)

### Información adicional

- [Preguntas más frecuentes sobre acceso condicional](active-directory-conditional-faqs.md)
- [Referencia técnica](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0831_2016-->