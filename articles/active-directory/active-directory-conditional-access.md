<properties
	pageTitle="Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory | Microsoft Azure"  
    description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."  
    services="active-directory" 
	keywords="acceso condicional a aplicaciones, acceso condicional con Azure AD, acceso seguro a recursos de empresa, directivas de acceso condicional" 
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/23/2016"
	ms.author="femila"/>


# Protección del acceso a Office 365 y otras aplicaciones conectadas a Azure Active Directory  
  
La protección del acceso a los recursos de la empresa es importante para todas las organizaciones. Con la llegada de los dispositivos móviles y los servicios en la nube, han cambiado significativamente las formas en que los usuarios acceden a los recursos de la empresa. Esto requiere cambios en la estrategia para proteger los recursos corporativos.
  
## ¿Por qué el acceso condicional  
 Las funcionalidades de control de acceso condicional en Azure Active Directory ofrecen a las empresas varias maneras sencillas de proteger sus recursos en la nube y locales. Si necesita algo como "impedir el acceso a mis recursos mediante una contraseña robada" o "requerir un dispositivo administrado y sin problemas para acceder al contenido de mi empresa", Azure Active Directory satisface sus necesidades.

## ¿Cómo se aplica el control de acceso condicional?  
 Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que elige al autenticar a un usuario, antes de permitirle el acceso a una aplicación. Cuando estos requisitos se cumplen, se autentica al usuario y se permite el acceso a la aplicación.
   
![](./media/active-directory-conditional-access/conditionalaccess-overview.png)

## Acceso basado en usuario a los recursos
  
- **Atributos de usuario**: en el nivel de atributos de usuario, puede aplicar directivas para asegurarse de que solo los usuarios autorizados pueden acceder a los recursos de la empresa.
- **Pertenencia a grupos de un usuario**: también puede controlar el nivel de acceso proporcionado a un usuario basándose en su pertenencia a un grupo o grupos.
- **Autenticación multifactor (MFA)**: también puede aplicar directivas cuando un usuario tenga que autenticar su identidad mediante un sistema de autenticación multifactor. Por ejemplo, puede obligar a un usuario a confirmar un PIN en un teléfono móvil personal para garantizar un nivel de seguridad adicional. La autenticación de MFA protege los recursos impidiendo que acceda a ellos un usuario no autorizado que haya obtenido acceso al nombre de usuario y a la contraseña de un usuario válido.
- **Riesgo de inicio de sesión y usuario**: Azure AD Identity Protection dispone de directivas de riesgo de acceso condicional que proporcionan protección avanzada basada en eventos de riesgo y actividades de inicio de sesión inusuales.
 

## Acceso condicional basado en dispositivos 

**Dispositivos inscritos**: en el nivel de dispositivo, puede configurar directivas que exijan que solo se permita el acceso a los dispositivos inscritos en Mobile Device Management (MDM). Microsoft Intune se utiliza para validar que el dispositivo está inscrito y es compatible. El acceso condicional de nivel de dispositivo garantiza que solo se permite el acceso a los dispositivos que sean compatibles con sus directivas, como exigir el cifrado de archivos en un dispositivo. Además, mediante soluciones MDM, puede asegurarse de que los datos corporativos de un dispositivo perdido o robado se puedan borrar de forma remota.
  

El nivel de acceso que se puede establecer mediante estas directivas puede aplicarse a los recursos en la nube o locales. Los recursos en la nube pueden ser aplicaciones como Office 365 y aplicaciones SaaS de terceros. Además, también pueden ser aplicaciones de línea de negocio que ha hospedado en la nube.
  
## Acceso condicional: mapa de contenido  
El mapa de contenido siguiente enumera los documentos que debe consultar para obtener más información sobre la habilitación de acceso condicional en su implementación actual.


| Escenario | Artículos |
|------------------------------------------------------|----------|
| Protección de los recursos basada en el usuario o la seguridad de la autenticación |[Acceso condicional de Azure en versión de vista previa para aplicaciones SaaS](active-directory-conditional-access-azuread-connected-apps.md)<br><br>[Compatibilidad con el acceso condicional de las aplicaciones](active-directory-conditional-access-supported-apps.md)|
| Proteger los datos corporativos en dispositivos perdidos o robados |[Help protect your data with full or selective wipe using Microsoft Intune](https://docs.microsoft.com/intune/deploy-use/use-remote-wipe-to-help-protect-data-using-microsoft-intune) (Ayuda para proteger los datos con borrado completo o selectivo mediante Microsoft Intune)|
|Protección de recursos basada en el riesgo de inicio de sesión |[Azure AD Identity Protection](active-directory-identityprotection.md) |
| Información adicional |[Preguntas más frecuentes sobre acceso condicional](active-directory-conditional-faqs.md)<br><br>[Referencia técnica](active-directory-conditional-access-technical-reference.md) |

<!---HONumber=AcomDC_0629_2016-->