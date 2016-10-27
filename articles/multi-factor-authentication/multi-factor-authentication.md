<properties
    pageTitle="Información general de MFA de Azure | Microsoft Azure"
    description="En este tema se explica qué es Azure Multi-factor Authentication, por qué usar MFA, aporta más información sobre el cliente de Multi-Factor Authentication y los distintos métodos y versiones disponibles. "
    keywords="introducción a MFA, introducción general a mfa, qué es mfa"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>


# <a name="what-is-azure-multi-factor-authentication?"></a>¿Qué es Azure Multi-Factor Authentication?
La verificación en dos pasos es un método de autenticación que requiere más de un método de comprobación y agrega un segundo nivel importante de seguridad crítico a las transacciones e inicios de sesión del usuario. Funciona mediante la solicitud de dos o más de los siguientes métodos de verificación:

- Un elemento que conoce (normalmente una contraseña).
- Un elemento del que dispone (un dispositivo de confianza que no se puede duplicar con facilidad, como un teléfono).
- Un elemento físico que le identifica (biométrica).

<center>![Nombre de usuario y contraseña](./media/multi-factor-authentication/pword.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Certificados](./media/multi-factor-authentication/phone.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Smartphone](./media/multi-factor-authentication/hware.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Tarjeta inteligente](./media/multi-factor-authentication/smart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Tarjeta inteligente virtual](./media/multi-factor-authentication/vsmart.png) &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;![Nombre de usuario y contraseña](./media/multi-factor-authentication/cert.png)</center>

Azure Multi-Factor Authentication (MFA) es la solución de Microsoft de comprobación de dos pasos. Azure MFA ayuda a proteger el acceso a los datos y las aplicaciones, al tiempo que satisface la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de métodos de comprobación (como la comprobación mediante llamadas telefónicas, mensajes de texto o aplicaciones móviles).

>[AZURE.VIDEO multi-factor-authentication-overview]

## <a name="why-use-azure-multi-factor-authentication?"></a>¿Por qué usar Azure Multi-Factor Authentication?

Hoy en día, ahora más que nunca, las personas están cada vez más conectadas. Con los smartphones, tabletas, equipos portátiles y equipos de sobremesa, las personas tienen varias opciones para conectarse y permanecer conectados en cualquier momento. Pueden acceder a sus cuentas y las aplicaciones desde cualquier lugar, lo que significa que pueden ser más productivos y atender mejor a sus clientes.

Azure Multi-Factor Authentication es una solución fácil de usar, escalable y confiable que proporciona un segundo método de autenticación para que los usuarios siempre estén protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalable](./media/multi-factor-authentication/scalable.png)| ![Siempre protegido](./media/multi-factor-authentication/protected.png)|![Confiable](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalable**|**Siempre protegido**|**Confiable**

- **Fácil de usar** : Azure Multi-Factor Authenticaton es fácil de configurar y utilizar. La protección adicional que se incluye con Azure Multi-Factor Authentication permite a los usuarios administrar sus propios dispositivos. Y lo mejor de todo es que en muchos casos se puede configurar con unos pocos clics.
- **Escalable** : Azure Multi-Factor Authenticaton utiliza la potencia de la nube y se integra con la instancia local de Active Directory y aplicaciones personalizadas. Esta protección se extiende incluso a los escenarios críticos de gran volumen.
- **Siempre protegido** Azure Multi-Factor Authenticaton ofrece autenticación segura mediante los más elevados estándares del sector.
- **Confiable** : se garantiza la disponibilidad del 99,9% de Azure Multi-Factor Authenticaton. Se considera que el servicio no está disponible cuando no puede recibir ni procesar las solicitudes de comprobación para la comprobación en dos pasos.

>[AZURE.VIDEO windows-azure-multi-factor-authentication]


## <a name="how-azure-multi-factor-authentication-works"></a>Cómo funciona Azure Multi-Factor Authentication

La seguridad de la comprobación en pos pasos se basa en el enfoque por niveles. Poner en peligro los distintos métodos de comprobación supone un reto importante para los atacantes. Aunque un atacante consiga descifrar su contraseña, no le servirá de nada si no dispone también del dispositivo de confianza. Si pierde el dispositivo, quien lo encuentre no podrá usarlo, salvo que conozca la contraseña.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

## <a name="methods-available-for-multi-factor-authentication"></a>Métodos disponibles para Multi-Factor Authentication
Cuando un usuario inicia sesión, se envía una solicitud de comprobación adicional al usuario. Lo siguiente es una lista de métodos que pueden usarse para esta segunda comprobación.

Método de comprobación | Description
------------- | ------------- |
llamada de teléfono | Se realiza una llamada al teléfono del usuario para solicitarle que compruebe si ha iniciado sesión. Presione la tecla # para completar el proceso de comprobación. Esta opción es configurable y puede cambiarse por un código que especifique.
mensaje de texto | Se envía un mensaje de texto al smartphone del usuario con un código de seis dígitos. Escriba este código para completar el proceso de comprobación.
Notificación en aplicación móvil | Se envía al smartphone del usuario una solicitud de comprobación en la que se le solicita que complete la comprobación mediante la selección de **Verify** (Comprobar) en la aplicación móvil. Esto se realiza si la notificación en la aplicación es el método de comprobación principal. Si el usuario recibe dicha notificación sin estar conectado, pueden notificar que se ha producido un fraude.
Código de comprobación con la aplicación móvil | La aplicación móvil en un dispositivo de usuario genera un código de comprobación. Esto sucede si ha seleccionado un código de verificación como método de comprobación principal.

Para los métodos de verificación de la aplicación móvil, Azure Multi-Factor Authentication funciona con aplicaciones de autenticación de terceros para smartphones. Sin embargo, recomendamos la aplicación Microsoft Authenticator, que está disponible para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072) e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

## <a name="available-versions-of-azure-multi-factor-authentication"></a>Versiones disponibles de Azure Multi-Factor Authentication
Azure Multi-Factor Authentication está disponible en tres versiones diferentes. 

Versión | Description
------------- | ------------- |
Multi-Factor Authentication para Office 365 | Esta versión funciona exclusivamente con aplicaciones de Office 365 y se administra desde el portal de Office 365. De este modo, los administradores pueden proteger ahora sus recursos de Office 365 con la comprobación en dos fases. Esta versión incluye una suscripción a Office 365.
Multi-Factor Authentication para administradores de Azure | Todos los administradores de Azure tienen a su disposición, sin costo adicional, el mismo subconjunto de funciones de comprobación en dos pasos que para Office 365. Cada cuenta administrativa de una suscripción de Azure puede habilitar esta funcionalidad para obtener una protección adicional. Los administradores que deseen acceder a Azure Portal para crear una máquina virtual o un sitio web, administrar el almacenamiento o usar cualquier otro servicio de Azure, pueden agregar MFA a su cuenta de administrador.
Azure Multi-Factor Authentication | Azure Multi-Factor Authentication ofrece el mayor conjunto de capacidades. Proporciona opciones de configuración adicionales a través del [Portal de Azure clásico](http://manage.windowsazure.com), capacidades de generación de informes avanzadas y soporte técnico para una amplia variedad de aplicaciones locales y en la nube. Azure Multi-Factor Authentication se incluye como parte de Azure Active Directory Premium y Enterprise Mobility Suite, y puede implementarse en la nube o localmente.

## <a name="feature-comparison-of-versions"></a>Comparación de características de las versiones
En la tabla siguiente se proporciona una lista de las características que están disponibles en las distintas versiones de Azure Multi-Factor Authentication.

>[AZURE.NOTE] Esta tabla de comparación describe las características que forman parte de cada suscripción. Si tiene Azure AD Premium o Enterprise Mobility Suite, algunas características pueden no estar disponibles dependiendo de si utiliza [MFA en la nube o MFA local](multi-factor-authentication-get-started.md).

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

## <a name="how-to-get-azure-multi-factor-authentication"></a>Cómo conseguir Azure Multi-Factor Authentication

Si desea todas las funcionalidades que ofrece Azure Multi-Factor Authentication, hay varias opciones:

1.  Comprar licencias de Azure Multi-Factor Authentication y asignarlas a los usuarios.
2.  Comprar licencias que incluyen Azure Multi-Factor Authentication, como Azure Active Directory Premium, Enterprise Mobility Suite o Enterprise Cloud Suite, y asignarlas a los usuarios.
3.  Crear un proveedor de Azure Multi-Factor Authentication dentro de una suscripción a Azure. Si se utiliza un proveedor de Azure Multi-Factor Authentication, hay dos modelos de uso disponibles que se facturan a través de la suscripción a Azure:  
    - **Por usuario**. Destinado a empresas que quieren habilitar la comprobación en dos pasos para un número fijo de empleados que con frecuencia necesitan autenticación.  
    - **Por autenticación**. Destinado a empresas que quieren habilitar la comprobación en dos pasos para un número mayor de usuarios externos que no suelen necesitar frecuentemente autenticación.  

Azure Multi-Factor Authentication proporciona métodos de comprobación seleccionables tanto para la nube y como para servidor. Esto significa que puede elegir qué métodos hay disponibles para los usuarios. Esta característica es actualmente una versión preliminar pública para la versión en la nube de Multi-Factor Authentication. Para más información, vea [Métodos de comprobación seleccionables](multi-factor-authentication-whats-next.md#selectable-verification-methods).

Para obtener información detallada sobre los precios, vea [Precios de Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

## <a name="next-steps"></a>Pasos siguientes

Para empezar a usar Azure Multi-Factor Authentication, el primer paso es [elegir entre MFA en la nube o local](multi-factor-authentication-get-started.md)



<!--HONumber=Oct16_HO2-->


