<properties 
	pageTitle="¿Qué es Azure Multi-Factor Authentication?" 
	description="Azure Multi-Factor Authentication es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una capa adicional de seguridad a los inicios de sesión y transacciones de los usuarios." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="terrylan" 
	editor="bryanla"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/02/2015" 
	ms.author="billmath"/>

# Seguridad adicional en un mundo conectado permanentemente

Multi-factor Authentication (MFA) es un método de autenticación que requiere el uso de más de un método de verificación y agrega un segundo nivel importante de seguridad a las transacciones e inicios de sesión del usuario. Funciona mediante la solicitud de dos o más de los siguientes métodos de verificación:

- Un elemento que conoce (normalmente una contraseña).
- Un elemento del que dispone (un dispositivo de confianza que no se puede duplicar con facilidad, como un teléfono).
- Un elemento físico que le identifica (biométrica).

<center>![Username and Password](./media/multi-factor-authentication/pword.png) &#160;&#160;&#160;&#160;&#160;![Certificates](./media/multi-factor-authentication/phone.png) &#160;&#160;&#160;&#160;&#160;![Smart Phone](./media/multi-factor-authentication/hware.png) &#160;&#160;&#160;&#160;&#160;![Smart Card](./media/multi-factor-authentication/smart.png) &#160;&#160;&#160;&#160;&#160;![Virtual Smart Card](./media/multi-factor-authentication/vsmart.png) &#160;&#160;&#160;&#160;&#160;![Username and Password](./media/multi-factor-authentication/cert.png)</center>

## ¿Qué es Azure Multi-Factor Authentication?

Azure Multi-Factor Authentication es un método para comprobar quién es el que requiere usar más de un nombre de usuario y contraseña. Proporciona una segunda capa de seguridad a los inicios de sesión y transacciones de los usuarios.

Azure Multi-Factor Authentication ayuda a proteger el acceso a los datos y las aplicaciones, además de satisfacer la demanda de los usuarios de un proceso de inicio de sesión simple. Ofrece una autenticación segura a través de una gran variedad de opciones sencillas, como llamadas telefónicas, mensajes de texto o notificaciones de aplicaciones móviles, lo que permite a los usuarios elegir el mecanismo que prefieran.

Para obtener información general de cómo funciona Multi-Factor Authentication, vea el siguiente vídeo.


<center>[AZURE.VIDEO multi-factor-authentication-overview]</center>

##¿Por qué usar Azure Multi-Factor Authentication?

Hoy en día, ahora más que nunca, las personas están cada vez más conectadas. Con los smartphones, tabletas, equipos portátiles y equipos de sobremesa, las personas tienen varias opciones para conectarse y permanecer conectados en cualquier momento. Las personas pueden acceder a sus cuentas y las aplicaciones desde cualquier lugar y esto significa que pueden ser más productivos y atender mejor a sus clientes.

Azure Multi-Factor Authentication es una solución fácil de usar, escalable y confiable que proporciona un segundo método de autenticación para que los usuarios siempre estén protegidos.

![Fácil de usar](./media/multi-factor-authentication/simple.png)| ![Escalabilidad](./media/multi-factor-authentication/scalable.png)| ![Siempre protegido](./media/multi-factor-authentication/protected.png)|![Confiable](./media/multi-factor-authentication/reliable.png)
:-------------: | :-------------: | :-------------: | :-------------: |
**Fácil de usar**|**Escalable**|**Siempre protegido**|**Confiable**

- **Fácil de usar**: Azure Multi-Factor Authenticaton es fácil de configurar y utilizar. La protección adicional que se incluye con Azure Multi-Factor Authenticaton permite a los usuarios utilizar y administrar sus propios dispositivos y, en muchos casos, puede instalarse con unos pocos clics.
- **Escalable**: Azure Multi-Factor Authenticaton utiliza la eficacia de la nube y se integra con Active Directory local y aplicaciones personalizadas. Esta protección se extiende incluso a los escenarios críticos de gran volumen.
- **Siempre protegido** Azure Multi-Factor Authenticaton ofrece autenticación segura mediante los más elevados estándares del sector.
- **Confiable**: se garantiza la disponibilidad del 99,9% de Azure Multi-Factor Authenticaton. Se considera que el servicio no está disponible cuando no puede recibir ni procesar las solicitudes de autenticación para la autenticación multifactor.  

Para obtener más información sobre por qué usar Azure Multi-Factor Authenticaton, vea el vídeo siguiente.

<center>[AZURE.VIDEO windows-azure-multi-factor-authentication]</center>

**Recursos adicionales**

* [Para los usuarios](multi-factor-authentication-end-user.md)
* [Azure Multi-Factor Authenticaton en MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx) 

<!---HONumber=July15_HO1-->