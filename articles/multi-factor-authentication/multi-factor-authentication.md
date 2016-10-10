<properties
	pageTitle="¿Qué es Azure Multi-Factor Authentication? | Microsoft Azure"
	description="En este tema se explica qué es Multi-Factor Authentication (mfa), por qué alguien podría usar MFA, aporta más información sobre el cliente de Multi-Factor Authentication y los distintos métodos y versiones disponibles. Azure Multi-Factor Authentication es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa de seguridad adicional a los inicios de sesión y transacciones de los usuarios."
	keywords="introducción a MFA, introducción general a mfa, qué es mfa"
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtland"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/22/2016"
	ms.author="kgremban"/>

# ¿Qué es Azure Multi-Factor Authentication?
Multi-Factor Authentication (MFA) es un método de autenticación que requiere más de un método de comprobación y agrega un segundo nivel importante de seguridad crítico a las transacciones e inicios de sesión del usuario. Funciona mediante la solicitud de dos o más de los siguientes métodos de verificación:

- Un elemento que conoce (normalmente una contraseña).
- Un elemento del que dispone (un dispositivo de confianza que no se puede duplicar con facilidad, como un teléfono).
- Un elemento físico que le identifica (biométrica).

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>


Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones sencillas de comprobación (como la comprobación mediante llamadas telefónicas, mensajes de texto o aplicaciones móviles).

>[AZURE.VIDEO multi-factor-authentication-overview]

## ¿Por qué usar Azure Multi-Factor Authentication?

Hoy en día, ahora más que nunca, las personas están cada vez más conectadas. Con los smartphones, tabletas, equipos portátiles y equipos de sobremesa, las personas tienen varias opciones para conectarse y permanecer conectados en cualquier momento. Pueden acceder a sus cuentas y las aplicaciones desde cualquier lugar, lo que significa que pueden ser más productivos y atender mejor a sus clientes.

Azure Multi-Factor Authentication es una solución fácil de usar, escalable y confiable que proporciona un segundo método de autenticación para que los usuarios siempre estén protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalable](./media/multi-factor-authentication/scalable.png)| ![Siempre protegido](./media/multi-factor-authentication/protected.png)|![Confiable](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalable**|**Siempre protegido**|**Confiable**

- **Fácil de usar**: Azure Multi-Factor Authenticaton es fácil de configurar y utilizar. La protección adicional que se incluye con Azure Multi-Factor Authentication permite a los usuarios administrar sus propios dispositivos. Y lo mejor de todo es que en muchos casos se puede configurar con unos pocos clics.
- **Escalable**: Azure Multi-Factor Authenticaton utiliza la potencia de la nube y se integra con la instancia local de Active Directory y aplicaciones personalizadas. Esta protección se extiende incluso a los escenarios críticos de gran volumen.
- **Siempre protegido** Azure Multi-Factor Authenticaton ofrece autenticación segura mediante los más elevados estándares del sector.
- **Confiable**: se garantiza la disponibilidad del 99,9% de Azure Multi-Factor Authenticaton. Se considera que el servicio no está disponible cuando no puede recibir ni procesar las solicitudes de autenticación para la autenticación multifactor.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## Cómo funciona Azure Multi-Factor Authentication

La seguridad de la autenticación mediante varias fases se basa en el enfoque por niveles. El uso de varias fases de autenticación supone un reto importante para los atacantes. Aunque un atacante consiga descifrar su contraseña, no le servirá de nada si no dispone también del dispositivo de confianza. Si pierde el dispositivo, quien lo encuentre no podrá usarlo, salvo que conozca la contraseña.

![Página de proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Proporciona seguridad adicional al requerir una segunda forma de autenticación y ofrece autenticación segura a través de una gama de opciones de comprobación sencillas:

- llamada de teléfono
- mensaje de texto
- notificación de aplicación móvil, que permite a los usuarios elegir el método que prefieran
- código de comprobación de la aplicación móvil
- tokens OATH de terceros

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## Métodos disponibles para la autenticación multifactor
Cuando un usuario inicia sesión, se envía una comprobación adicional al usuario. Lo siguiente es una lista de métodos que pueden usarse para esta segunda comprobación.

Método de comprobación | Description
------------- | ------------- |
Llamada de teléfono | Se realiza una llamada al teléfono del usuario para solicitarle que compruebe si ha iniciado sesión. Presione la tecla # para completar el proceso de comprobación. Esta opción es configurable y puede cambiarse por un código que especifique.
Mensaje de texto | Se envía un mensaje de texto al smartphone del usuario con un código de seis dígitos. Escriba este código para completar el proceso de comprobación.
Notificación en aplicación móvil | Se envía al smartphone del usuario una solicitud de comprobación en la que se le solicita que complete la comprobación mediante la selección de **Verify** (Comprobar) en la aplicación móvil. Esto se realiza si la notificación en la aplicación es el método de comprobación principal. Si el usuario recibe dicha notificación sin estar conectado, pueden notificar que se ha producido un fraude.</li><br><p> La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).
Código de comprobación con la aplicación móvil | Se envía un código de comprobación a la aplicación móvil que se ejecuta en el smartphone del usuario. Esto sucede si ha seleccionado un código de verificación como método de comprobación principal.</li><br><p> La aplicación Microsoft Authenticator está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [iOS](http://go.microsoft.com/fwlink/?Linkid=825073).


## Versiones disponibles de Azure Multi-Factor Authentication
Azure Multi-Factor Authentication está disponible en tres versiones diferentes. En la tabla siguiente se describe cada una de ellas con más detalle.

Versión | Description
------------- | ------------- |
Multi-Factor Authentication para Office 365 | Esta versión funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. De este modo, los administradores pueden proteger ahora sus recursos de Office 365 con la autenticación multifactor. Esta versión incluye una suscripción a Office 365.
Multi-Factor Authentication para administradores de Azure | El mismo subconjunto de funcionalidades de Multi-Factor Authentication para Office 365 está disponible para los administradores de Azure de forma gratuita. Ahora, las cuentas administrativas de la suscripción de Azure pueden obtener mayor protección mediante la habilitación de esta funcionalidad de Multi-Factor Authentication en el núcleo. Los administradores que deseen acceder a Azure Portal para crear una máquina virtual o un sitio web, administrar el almacenamiento o usar cualquier otro servicio de Azure, pueden agregar MFA a su cuenta de administrador.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication ofrece el mayor conjunto de capacidades. Proporciona opciones de configuración adicionales a través del [Portal de Azure clásico](http://manage.windowsazure.com), capacidades de generación de informes avanzadas y soporte técnico para una amplia variedad de aplicaciones locales y en la nube. Azure Multi-Factor Authentication se incluye como parte de Azure Active Directory Premium y Enterprise Mobility Suite.

## Comparación de características de las versiones
En la tabla siguiente se proporciona una lista de las características que están disponibles en las distintas versiones de Azure Multi-Factor Authentication.


Característica | Multi-Factor Authentication para Office 365 (incluido en la SKU de Office 365)|Multi-Factor Authentication para administradores de Azure (incluido con la suscripción a Azure) | Azure Multi-Factor Authentication (incluido en Azure AD Premium y Enterprise Mobility Suite)
------------- | :-------------: |:-------------: |:-------------: |
Los administradores pueden proteger cuentas con MFA.| ● | ● (Disponible solo para las cuentas de administrador de Azure)|●
Aplicación móvil como segundo factor|● | ● | ●
Llamada de teléfono como segundo factor|● | ● | ●
SMS como segundo factor|● | ● | ●
Contraseñas de aplicación para clientes que no admiten MFA|● | ● | ●
Control de administración sobre los métodos de autenticación| ● | ● | ●
Modo de PIN| | | ●
Alerta de fraude| | | ●
Informes de MFA| | | ●
Omisión por única vez| | | ●
Saludos personalizados para las llamadas de teléfono| | | ●
Personalización del identificador de llamada para llamadas telefónicas| | | ●
Confirmación de evento| | | ●
IP de confianza| | | ●
Recordar MFA para dispositivos de confianza |● | ● | ●
SDK de MFA | | | ● requiere un proveedor de Multi-Factor Authentication y una suscripción completa a Azure
MFA para aplicaciones locales mediante servidor MFA| | | ●

## Cómo conseguir Azure Multi-Factor Authentication

Si desea disponer de todas las funcionalidades que ofrece Azure Multi-Factor Authentication, además de las características para los usuarios de Office 365 y administradores de Azure, hay varias opciones:

1.	Comprar licencias de Azure Multi-Factor Authentication y asignarlas a los usuarios.
2.	Comprar licencias que incluyen Azure Multi-Factor Authentication, como Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite, y asignarlas a los usuarios.
3.	Crear un proveedor de Azure Multi-Factor Authentication dentro de una suscripción a Azure. Si todavía no dispone de una suscripción a Azure, puede registrarse para obtener una evaluación gratuita de Azure. Las suscripciones de prueba tienen que convertirse en suscripciones normales antes de que expire el periodo de prueba.

Si se utiliza un proveedor de Azure Multi-Factor Authentication, hay dos modelos de uso disponibles que se facturan a través de la suscripción a Azure:


- **Por usuario**. Destinado a empresas que quieren habilitar la autenticación multifactor para un número fijo de empleados que con frecuencia necesitan autenticación.
- **Por autenticación**. Destinado a empresas que quieren habilitar la autenticación multifactor para un número mayor de usuarios externos que no suelen necesitar frecuentemente autenticación.

Azure Multi-Factor Authentication proporciona métodos de comprobación seleccionables tanto para la nube y como para servidor. Esto significa que puede elegir qué métodos hay disponibles para que los usuarios los utilicen con Multi-Factor Authentication. Esta característica es actualmente una versión preliminar pública para la versión en la nube de Multi-Factor Authentication. Para más información, consulte [Métodos de verificación seleccionables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Para obtener información detallada sobre los precios, consulte [Precios de Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Elija el modelo por puesto o según el consumo que mejor funcione para su organización. Para comenzar, consulte [Introducción](multi-factor-authentication-get-started.md).

## Pasos siguientes

Para empezar a usar Azure Multi-Factor Authentication, el primer paso es [elegir entre MFA en la nube o local](multi-factor-authentication-get-started.md)

<!---HONumber=AcomDC_0928_2016-->