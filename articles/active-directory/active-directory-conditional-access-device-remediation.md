<properties
	pageTitle="Solución de problemas: no se puede acceder desde aquí | Microsoft Azure"
	description="Este tema le ayuda a identificar los pasos de corrección que puede seguir para obtener acceso a la aplicación."
	services="active-directory"
	keywords="acceso condicional basado en dispositivo, registro de dispositivo, habilitar registro de dispositivo, registro de dispositivo y MDM"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="08/23/2016"
	ms.author="markvi"/>


# Solución de problemas: no se puede acceder desde aquí

Aparece una página a la que deniega el acceso al acceder a una aplicación como SharePoint Online. ¿Qué hace?

Esta guía le ayuda a identificar los pasos de corrección disponibles que pueda seguir para obtener acceso a la aplicación.



¿En qué plataforma de dispositivo se ejecuta el dispositivo? La respuesta a esta pregunta determina la sección de este tema más apropiada para cada caso:


-	Dispositivo Windows
-	Dispositivo iOS (iPhone o iPad)
-	Dispositivo Android

## Acceso desde un dispositivo Windows

Si el dispositivo ejecuta Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2, elija la causa apropiada mediante la identificación de la página que apareció al intentar acceder a la aplicación.

### El dispositivo no está registrado

Si el dispositivo no está registrado en Azure Active Directory (Azure AD) y la aplicación está protegida mediante una directiva basada en dispositivo, puede ver una página con el siguiente contenido:

![Mensajes de tipo "No se puede llegar allí desde aquí" para dispositivos no registrados](./media/active-directory-conditional-access-device-remediation/01.png "Escenario")



Si el dispositivo está unido al dominio a la instancia de Active Directory de su organización, puede intentar lo siguiente:

1.	Asegúrese de que ha iniciado sesión en Windows con su cuenta de trabajo (cuenta de Active Directory).
2.	Conéctese a su red corporativa a través de VPN o DirectAccess.
3.	Una vez conectado, bloquee la sesión de Windows presionando a la vez la tecla Windows y la tecla L.
4.	Desbloquee la sesión de Windows, para lo que debe especificar las credenciales de su cuenta profesional.
5.	Espere un minuto e intente de nuevo acceder a la aplicación.
6.	Si aparece la misma página, póngase en contacto con el administrador, haga clic en **Más detalles** y después especifique los detalles.

Si el dispositivo no está unido al dominio y ejecuta Windows 10, tiene dos opciones:

- Ejecute Azure AD Join.
- Agregar su cuenta profesional o educativa a Windows

Para obtener información acerca de las diferencias entre ambas, consulte [Uso de dispositivos de Windows 10 en el área de trabajo](active-directory-azureadjoin-windows10-devices.md).

Para ejecutar Azure AD Join, realice las siguiente acciones (no disponibles en Windows Phone):

**Actualización de aniversario de Windows 10**

1.	Abra la aplicación de **configuración**.
2.	Haga clic en **Cuentas** > **Access work or school** (Acceder a la cuenta profesional o educativa).
3.	Haga clic en **Conectar**.
4.	Haga clic en **Unir este dispositivo a Azure Active Directory** en la parte inferior de la página.
5.	Autentique su organización, proporcione una prueba de Multi-Factor Authentication, si fuera necesario, y siga los pasos hasta la finalización.
6.	Cierre la sesión y vuelva a iniciarla con su cuenta profesional.
7.	Intente volver a acceder a la aplicación.




**Actualización de Windows de 10 de noviembre de 2015**


1.	Abra la aplicación de **configuración**.
2.	Haga clic en **Sistema** > **About** (Acerca de).
3.	Haga clic en **Unirse a Azure AD**.
4.	Autentique su organización, proporcione una prueba de Multi-Factor Authentication, si fuera necesario, y siga los pasos hasta la finalización.
5.	Cierre la sesión y vuelva a iniciarla con su cuenta profesional (cuenta de Azure AD).
6.	Intente volver a acceder a la aplicación.

Para agregar su cuenta profesional o educativa, realice estas acciones:

**Actualización de aniversario de Windows 10**

1.	Abra la aplicación de **configuración**.
2.	Haga clic en **Cuentas** > **Access work or school** (Acceder a la cuenta profesional o educativa).
3.	Haga clic en **Conectar**.
4.	Autentique su organización, proporcione una prueba de Multi-Factor Authentication, si fuera necesario, y siga los pasos hasta la finalización.
5.	Intente volver a acceder a la aplicación.


**Actualización de Windows de 10 de noviembre de 2015**

1.	Abra la aplicación de **configuración**.
2.	Haga clic en **Cuentas** > **Your accounts** (Sus cuentas).
3.	Haga clic en **Add work or school account** (Agregar cuenta profesional o educativa).
4.	Autentique su organización, proporcione una prueba de Multi-Factor Authentication, si fuera necesario, y siga los pasos hasta la finalización.
5.	Intente volver a acceder a la aplicación.

Si el dispositivo no está unido a un dominio y ejecuta Windows 8.1, puede unirse al lugar de trabajo e inscribirse a Microsoft Intune, para lo que debe realizar estas acciones:

1.	Abra **Configuración de PC**.
2.	Haga clic en **Red** > **Área de trabajo**.
3.	Haga clic en **Unir**.
4.	Autentique su organización, proporcione una prueba de Multi-Factor Authentication, si fuera necesario, y siga los pasos hasta la finalización.
5.	Haga clic en **Activar**.
6.	Espere hasta la finalización.
7.	Intente volver a acceder a la aplicación.


## Explorador no compatible

Si accede a la aplicación desde los siguientes exploradores, verá una página similar a la que se ha mostrado anteriormente:

- Chrome, Firefox o cualquier otro explorador que no sea Microsoft Edge ni Microsoft Internet Explorer en Windows 10 o Windows Server 2016.
- Firefox en Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.

![Mensaje "No se puede llegar allí desde aquí" para exploradores no admitidos](./media/active-directory-conditional-access-device-remediation/02.png "Escenario")


La única solución es utilizar un explorador compatible con la aplicación de la plataforma del dispositivo.

## Acceso desde un dispositivo iOS
Pronto podrá ver instrucciones para iPhone o iPad.

## Acceso desde un dispositivo Android
Pronto podrá ver instrucciones para teléfonos y tabletas con Android.

## Pasos siguientes

[Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)

<!----HONumber=AcomDC_0831_2016-->