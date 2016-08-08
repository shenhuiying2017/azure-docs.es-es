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
	ms.date="07/26/2016"
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

- **Dispositivos inscritos o compatibles**: a nivel de dispositivo se pueden configurar directivas que exijan que solo se permita el acceso a los dispositivos compatibles e inscritos en Administración de dispositivos móviles (MDM). Microsoft Intune se utiliza para validar que el dispositivo está inscrito y es compatible. El acceso condicional a nivel de dispositivo garantiza que solo se permite el acceso a los dispositivos que sean compatibles con la directiva de MDM.
 

## Aplicaciones

- El nivel de acceso que se puede establecer mediante estas directivas se puede aplicar tanto servicios como a aplicaciones locales o en la nube. La directiva se aplica directamente al sitio web o servicio. Después, la directiva se exige tanto para al acceso del explorador como a las aplicaciones que acceden al servicio. Aquí se puede encontrar la lista de servicios a los que se pueden aplicar la directiva.


  
## Acceso condicional: mapa de contenido  
El mapa de contenido siguiente enumera los documentos que debe consultar para obtener más información sobre la habilitación de acceso condicional en su implementación actual.


### Directivas de MFA y de ubicación

- [Introducción al acceso condicional a Azure AD](active-directory-conditional-access-azuread-connected-apps.md)
- [Compatibilidad con el acceso condicional de las aplicaciones](active-directory-conditional-access-supported-apps.md)


### Directivas de dispositivo

[Ayudar a proteger los datos con el borrado selectivo o completo mediante Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune)


### Protección de recursos basada en el riesgo de inicio de sesión

[Azure AD Identity Protection](active-directory-identityprotection.md)

### Información adicional

- [Preguntas más frecuentes sobre acceso condicional](active-directory-conditional-faqs.md)
- [Referencia técnica](active-directory-conditional-access-technical-reference.md)

<!---HONumber=AcomDC_0727_2016-->