<properties urlDisplayName="" pageTitle="¿Qué es Azure Multi-Factor Authentication?" metaKeywords="" description="" metaCanonical="" services="" documentationCenter="" title="What is Azure Multi-Factor Authentication?" authors="billmath" solutions="" manager="terrylan" editor="lisatoft" />

<tags ms.service="multi-factor-authentication" ms.workload="identity" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/17/2014" ms.author="billmath" />





<h1 id="whatiswamfa">¿Qué es Azure Multi-Factor Authentication?</h1>

La autenticación mediante varias fases o de dos fases es un método de autenticación que requiere el uso de más de un método de verificación y agregar un segundo nivel importante de seguridad a las transacciones e inicios de sesión del usuario. Funciona mediante la solicitud de dos o más de los siguientes métodos de verificación: 

* Un elemento que conoce (normalmente una contraseña).
* Un elemento del que dispone (un dispositivo de confianza que no se puede duplicar con facilidad, como un teléfono).
* Un elemento físico que le identifica (biométrica).

La seguridad de la autenticación mediante varias fases se basa en el enfoque por niveles. El uso de varias fases de autenticación supone un reto importante para los atacantes. Incluso si un atacante consigue descifrar la contraseña de usuario, no sirve de nada si no dispone también del dispositivo de confianza. Por otra parte, si el usuario pierde el dispositivo, la persona que encuentre el dispositivo no podrá usarlo a no ser que conozca la contraseña de usuario.
Azure Multi-Factor Authentication es el servicio de autenticación mediante varias fases que requiere que el usuario también compruebe los inicios de sesión mediante una aplicación móvil, una llamada de teléfono o un mensaje de texto. Está disponible para usarse con Azure Active Directory, para garantizar los recursos locales con Servidor Azure Multi-Factor Authentication y con los directorios y aplicaciones personalizados mediante el SDK. 

![Azure Multi-Factor Authentication](./media/multi-factor-authentication/whatismfa.png)

<h3>Protección de Azure Active Directory en la nube</h3>

Habilite Multi-Factor Authentication para configurar identidades de Azure AD y se le solicitará a los usuarios que configuren la comprobación adicional la próxima vez que inicien sesión. Use Multi-Factor Authentication para obtener acceso a Azure, Microsoft Online Services como Office 365 y Dynamics CRM Online, y a servicios en la nube de terceros que integren Azure AD sin una configuración adicional. La autenticación mediante varias fases se puede habilitar rápidamente para una gran cantidad de aplicaciones y usuarios globales.  [Más información](http://msdn.microsoft.com/es-es/library/azure/dn249466.aspx)

<h3>Protección de los recursos locales y Active Directory</h3>

Habilite Multi-Factor Authentication para los recursos locales como IIS y Active Directory mediante Servidor Multi-Factor Authentication de Azure.  Servidor Multi-Factor Authentication de Azure permite al administrador la integración con la autenticación IIS para asegurar las aplicaciones web de Microsoft IIS, la autenticación de RADIUS, la autenticación de LDAP y la autenticación de Windows.  [Más información](http://msdn.microsoft.com/es-es/library/azure/dn249467.aspx)
<h3>Protección de aplicaciones personalizadas</h3>

Un SDK habilita la integración directa con los servicios en la nube. Realice la comprobación mediante mensaje de texto o llamada telefónica de la autenticación activa en los procesos de transacción o inicio de sesión de la aplicación y aproveche la base de datos de usuarios existente de la aplicación. [Más información](http://msdn.microsoft.com/es-es/library/azure/dn249464.aspx)

<h3>Multi-Factor Authentication para Office 365</h3>

Multi-Factor Authentication para Office 365, con tecnología de Azure Multi-Factor Authentication, funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. De este modo, los administradores pueden proteger ahora sus recursos de Office 365 con la autenticación mediante varias fases. [Más información](http://msdn.microsoft.com/es-es/library/azure/dn383636.aspx)

<h3>Multi-Factor Authentication para administradores de Azure</h3>

La misma subred de capacidades Multi-Factor Authentication para Office 365 estará disponible sin que suponga ningún coste para los administradores de Azure. Las cuentas administrativas de la suscripción de Azure pueden obtener ahora una protección adicional mediante la habilitación de esta funcionalidad de autenticación a través de varias fases. Por lo tanto si un administrador desea acceder al portal Azure para crear una VM, un sitio web, un almacenamiento de administración, servicios móviles o cualquier otro Servicio Azure, puede agregar la autenticación multifactor a su cuenta de administrador.  [Más información](http://msdn.microsoft.com/es-es/library/azure/dn249471.aspx)

<h3>Comparación de la característica Multi-Factor Authentication</h3>

A continuación se muestran las versiones de la autenticación mediante varias fases que están disponibles y un breve resumen de las características que proporcionan. Puede usar la información para determinar qué versión de la autenticación mediante varias fases es la apropiada para usted. [Más información](http://msdn.microsoft.com/es-es/library/azure/dn249471.aspx)

![Azure Multi-Factor Authentication Feature Comparison](./media/multi-factor-authentication/mfacomparison2.png)


**Recursos adicionales**

* [Inicio de sesión en Azure como una organización](/es-es/manage/services/identity/organizational-account/)
* [Identidad de Azure](/es-es/manage/windows/fundamentals/identity/)
* [Biblioteca de Azure Multi-Factor Authentication](http://technet.microsoft.com/es-es/library/dn249471.aspx)


<!--HONumber=35.2-->
