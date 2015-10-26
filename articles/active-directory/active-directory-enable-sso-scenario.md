<properties 
    pageTitle="Puedo confiar en Azure AD para habilitar el inicio de sesión único (SSO) en todas mis aplicaciones | Microsoft Azure" 
    description="Descubra cómo Azure Active Directory permite ampliar el ámbito de una identidad y cómo realizar la administración." 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>

<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/12/2015" 
    ms.author="markvi" />


# Puedo confiar en Azure AD para habilitar el inicio de sesión único (SSO) en todas mis aplicaciones
Este artículo está destinado a los encargados de tomar decisiones de TI que desean conocer el valor empresarial que Azure Active Directory aporta a las aplicaciones que se usan en la organización.

## Ejecución de aplicaciones en un entorno basado en la nube  

Hoy en día, en el mundo en la nube, se pueden encontrar miles de aplicaciones para realizar cualquier trabajo. A menudo los departamentos de TI ni siquiera conocen todas las aplicaciones de SaaS que se usan en sus organizaciones. En muchos casos se conoce el uso de la aplicación pero resulta caro y lento adoptar cualquier nivel de seguridad y control corporativo. Muchas de las aplicaciones no admiten la integración con los sistemas de identidad de la empresa, en otros casos se evitan las integraciones excepcionales debido al costo y complejidad.

Como consecuencia:

- Muchas organizaciones tienen problemas de acceso no autorizado a los datos corporativos, posible pérdida de datos y otros riesgos de seguridad inherentes a estas aplicaciones no administradas. Dado que ni siquiera se sabe cuántas o qué aplicaciones se usan, la misma idea de comenzar a crear un plan para tratar estos riesgos parece desalentadora.
- Los administradores necesitan administrar individualmente todos los proveedores de servicios IAM que se introducen en el entorno a través de las aplicaciones administradas. Esto incluye tareas tales como la creación y eliminación de usuarios y grupos, así como la concesión o revocación del acceso a estas aplicaciones.
- Los usuarios deben memorizar varias credenciales para tener acceso a las aplicaciones con las que necesitan trabajar. Las contraseñas olvidadas suponen un gran impacto en los costes operativos de muchas organizaciones. Todos estos problemas tienen en común el impacto negativo sobre la productividad de los usuarios y los costos operativos.  
 
## ¿Cómo ayuda Azure Active Directory?
Azure Active Directory le ayuda a solucionar estos problemas ya que le permite ampliar fácilmente la infraestructura de identidad existente a la nube y gracias a ello:

- Ampliar el alcance de los usuarios móviles a cualquier aplicación 
- Ampliar el alcance de la administración de acceso a cualquier aplicación en la nube 
- Detectar las aplicaciones usadas a las que tienen acceso los usuarios


### Ampliación del alcance de la identidad a cualquier aplicación 

Azure Active Directory proporciona por diseño SSO para las aplicaciones en la nube que se hospedan en Azure y otros servicios en línea de Microsoft como Office 365, CRM Online y Intune.

Además, puede actuar como agente de identidad para el inicio de sesión único en aplicaciones de cualquier otra nube pública, como Google, Amazon, IBM y otras. <br> Si una aplicación es ampliamente conocida o popular, ya la integramos previamente en la galería de aplicaciones de Azure Active Directory. Hoy en día, puede encontrar en torno a 2500 aplicaciones ya integradas en la galería y su número aumenta de modo constante. <br> La configuración de SSO para cualquiera de las aplicaciones previamente integradas en la galería de aplicaciones es solo cuestión de unos cuantos clics. ¿Qué ocurre si mi aplicación todavía no aparece en la galería de aplicaciones? Puede integrar cualquier aplicación pública en Azure Active Directory mediante un asistente y así habilitar SSO para ella. Para obtener más detalles, consulte [Implementación de inicio de sesión único con Azure Active Directory para aplicaciones SaaS recién adquiridas](active-directory-single-sign-on-newly-acquired-saas-apps.md) e [Integración del inicio de sesión único de Azure Active Directory con aplicaciones existentes](active-directory-sso-integrate-existing-apps.md)<br> ¿Qué sucede con las aplicaciones desarrolladas por la organización? Le proporcionamos la documentación relacionada que permite a los desarrolladores integrar fácilmente aplicaciones desarrolladas por su organización en Azure Active Directory. Si desea más información al respecto, consulte [Azure AD y aplicaciones: guiar a los desarrolladores](active-directory-applications-guiding-developers-for-lob-applications.md).

Azure Active Directory le permite proporcionar SSO para todas las aplicaciones en la nube gracias la compatibilidad con las aplicaciones previamente integradas, las aplicaciones públicas que no están en la galería de aplicaciones y las aplicaciones que desarrolla su organización.

El valor de Azure Active Directory no solo se limita a las aplicaciones en la nube. Gracias a Azure Active Directory también puede ampliar el alcance de las identidades de Azure, ya que proporciona acceso remoto seguro a aplicaciones locales mediante SSO. Esto significa que ya no se necesitan tecnologías como las redes privadas virtuales u otras infraestructuras tradicionales de acceso remoto para tener acceso a aplicaciones web locales. Azure Active Directory ofrece esta funcionalidad a través de funciones de proxy de aplicación.

El modelo tradicional de SSO se basa en la asignación de dos cuentas individuales en dos repositorios de identidades. Con Azure Active Directory, puede incluso asignar una cuenta individual con una cuenta corporativa compartida, como la cuenta de Twitter corporativa. Mediante la implementación de SSO para las cuentas corporativas compartidas, ya no existe realmente necesidad de compartir las credenciales de cuenta con los usuarios, lo que mejora considerablemente la protección de estas cuentas. Si desea obtener más información al respecto, consulte [Uso compartido de cuentas con Azure AD](active-directory-sharing-accounts.md).

Al ampliar el alcance de las identidades, una única contraseña proporciona acceso a miles de aplicaciones.



### Ampliación del alcance de la administración de acceso a cualquier aplicación en la nube

La administración del acceso a las aplicaciones en la nube resulta cara si se debe realizar manualmente para cada aplicación. Con Azure Active Directory, puede administrar el acceso a las aplicaciones en la nube basándose en grupos de un punto central: el portal de Azure. Puede asignar acceso a usuarios individuales o incluso a grupos. Para obtener más detalles, consulte [Administración del acceso a las aplicaciones](active-directory-managing-access-to-apps.md).

Algunas aplicaciones como Salesforce, Box, Google Apps y Concur proporcionan interfaces de automatización para la creación y eliminación (o desactivación) de cuentas. Si un proveedor ofrece esta interfaz, se aprovecha en Azure Active Directory. En otras palabras, Azure Active Directory proporciona compatibilidad para el aprovisionamiento automático de usuarios a aplicaciones que ofrecen una interfaz de automatización relacionada.

El aprovisionamiento automático de usuarios permite:

- Si a un usuario se le ha concedido acceso a una aplicación relacionada, Azure Active Directory proporciona automáticamente la cuenta SSO necesaria a la aplicación.
- Si se elimina una cuenta de usuario en Active Directory de Azure, la cuenta de la aplicación se desactiva o se elimina. La configuración automatizada de proveedores de aprovisionamiento de usuario proporciona las siguientes ventajas:
- 	Reduce los costos operativos ya que automatiza las tareas administrativas que tendría que realizar el personal de TI.
- Mejora la seguridad del entorno porque reduce la posibilidad de que exista un acceso que no es necesario a las aplicaciones.

Para obtener más detalles sobre el aprovisionamiento automático de usuarios, consulte [Automatización del aprovisionamiento y desaprovisionamiento de usuarios para aplicaciones SaaS con Azure Active Directory](active-directory-saas-app-provisioning.md).


### Detección de las aplicaciones usadas a las que tienen acceso los usuarios

Gracias al SSO y el aprovisionamiento de cuentas, Azure Active Directory proporciona mecanismos eficaces para mejorar la forma en que los usuarios y los administradores pueden trabajar con las aplicaciones en el entorno de las que es consciente. En las empresas modernas, sin embargo, a menudo los departamentos de TI no son conscientes de todas las aplicaciones en la nube que se usan. Con Cloud App Discovery, Azure Active Directory proporciona también una solución para detectar estas aplicaciones. Con Cloud App Discovery, puede:

- Detectar aplicaciones en uso y medir el uso por número de usuarios, volumen de tráfico o número de solicitudes web a la aplicación.
- Identificar a los usuarios que están usando una aplicación.
- Exportar datos para análisis sin conexión de adición.
- Dar prioridad a las aplicaciones para poner bajo control de TI e integrar aplicaciones fácilmente para habilitar el inicio de sesión único y la administración de usuarios.

Para obtener más detalles acerca de Cloud App Discovery, consulte [¿Cómo puedo detectar aplicaciones en la nube no sancionadas que se usan dentro de mi organización?](active-directory-cloudappdiscovery-whatis.md)


## ¿Cómo puedo comenzar?

En primer lugar, si aún no usa Azure AD y es un administrador de TI:

- [¡Pruébelo!](https://azure.microsoft.com/trial/get-started-active-directory) Puede suscribirse a una evaluación gratuita de 30 días hoy e implementar la primera solución en la nube en menos de 5 minutos desde este vínculo

<!---HONumber=Oct15_HO3-->