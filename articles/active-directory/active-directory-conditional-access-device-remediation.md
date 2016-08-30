<properties
	pageTitle="Solución de problemas: no se puede acceder desde aquí | Microsoft Azure"
	description="Este tema le ayuda a identificar si hay pasos de corrección que pueda seguir para obtener acceso a la aplicación que desea."
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

Aparece una página a la que deniega el acceso al acceder a una aplicación como Office 365 SharePoint Online. ¿Qué hace?

Esta guía le ayuda a identificar si hay pasos de corrección que pueda seguir para obtener acceso a la aplicación que desea.



¿En qué plataforma de dispositivo se ejecuta el dispositivo? La respuesta a esta pregunta determina la sección de este tema más apropiada para cada caso:
 

-	Dispositivo Windows

-	Dispositivo iOS (iPhone o iPad)

-	Dispositivo Android

## Acceso desde un dispositivo Windows

Si el dispositivo tiene cualquiera de los siguientes sistemas operativos: Windows 10, Windows 8.1, Windows 8.0, Windows 7, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2. elija la causa apropiada mediante la identificación de la página que apareció al intentar acceder a la aplicación.

### El dispositivo no está registrado

Si el dispositivo no está registrado en Azure Active Directory y la aplicación está protegida mediante una directiva basada en dispositivo, puede ver una página con el siguiente contenido:

![Escenario](./media/active-directory-conditional-access-device-remediation/01.png "Escenario")

 

Si el dispositivo está unido por dominio a la instancia de Active Directory de su organización, puede intentar lo siguiente:

1.	Asegúrese de que ha iniciado sesión en Windows con su cuenta de trabajo (cuenta de Active Directory).

2.	Conéctese a su red corporativa a través de VPN o acceso directo.

3.	Una vez conectado, bloquee la sesión de Windows mediante la tecla Windows + la tecla 'L'.

4.	Desbloquee la sesión de Windows, para lo que debe especificar las credenciales de su cuenta profesional.

5.	Espere un minuto e vuelva a intente acceder a la aplicación.

6.	Si aparece la misma página, póngase en contacto con el administrador y especifique los detalles después de hacer clic en el vínculo "Más detalles".

Si el dispositivo no está unido a ningún dominio y usa Windows 10, tiene dos opciones:

1. Ejecutar Azure AD Join
2. Agregar su cuenta profesional o educativa a Windows

Para obtener información acerca de las diferencias entre ambas, consulte [Uso de dispositivos de Windows 10 en el área de trabajo](active-directory-azureadjoin-windows10-devices.md).

Para ejecutar Azure AD Join, realice las siguiente acciones (no disponibles en Windows Phone):

**Actualización de aniversario de Windows 10**

1.	Inicie la aplicación de configuración.

2.	Vaya a 'Accounts' (Cuentas) y luego a 'Access work or school' (Acceso profesional o educativo).

3.	Haga clic en 'Connect' (Conectar).

4.	Elija 'Join this device to Azure AD' (Conectar este dispositivo a Azure AD) en la parte inferior de la página.

5.	Autentique con su organización, proporcione una prueba de MFA si fuera necesario y siga los pasos hasta la finalización.

6.	Cierre la sesión y vuelva a iniciarla con su cuenta profesional.

7.	Intente volver a acceder a la aplicación.




**Actualización de Windows de 10 de noviembre de 2015**


1.	Inicie la aplicación de configuración.

2.	Vaya a 'System' (Sistema) y luego a 'About' (Acerca de).
	
3.	Haga clic en 'Join Azure AD' (Conectar a Azure AD).

4.	Autentique con su organización, proporcione una prueba de MFA si fuera necesario y siga los pasos hasta la finalización.

5.	Cierre la sesión y vuelva a iniciarla con su cuenta profesional (cuenta de Azure AD).

6.	Intente volver a acceder a la aplicación.


Para agregar su cuenta profesional o educativa, realice estas acciones:

**Actualización de aniversario de Windows 10**

1.	Inicie la aplicación de configuración.

2.	Vaya a 'Accounts' (Cuentas) y luego a 'Access work or school' (Acceso profesional o educativo).

3.	Haga clic en 'Connect' (Conectar).

4.	Autentique con su organización, proporcione una prueba de MFA si fuera necesario y siga los pasos hasta la finalización.

5.	Intente volver a acceder a la aplicación.


**Actualización de Windows de 10 de noviembre de 2015**
	
1.	Inicie la aplicación de configuración.
2.	Vaya a ‘Accounts’ (Cuentas) y luego a ‘Your accounts’ (Sus cuentas).
3.	Haga clic en ‘Add work or school account’ (Agregar cuenta profesional o educativa).
4.	Autentique con su organización, proporcione una prueba de MFA si fuera necesario y siga los pasos hasta la finalización.
5.	Intente volver a acceder a la aplicación.

Si el dispositivo no está unido a un dominio y ejecute Windows 8.1, puede unirse al área de trabajo e inscribirse a Microsoft Intune, para lo que debe realizar estas acciones:

1.	Inicie Configuración de PC.

2.	Vaya a 'Network' (Red), 'Workplace' (Área de trabajo).

3.	Haga clic en 'Join' (Combinar).

4.	Autentique con su organización, proporcione una prueba de MFA si fuera necesario y siga los pasos hasta la finalización.

5.	Haga clic en 'Turn on' (Activar).

6.	Espere hasta la finalización.

7.	Intente volver a acceder a la aplicación.


## Explorador no compatible

Si accede a la aplicación desde los siguientes exploradores, verá una página similar a la que se muestra a continuación:

1.	Chrome, Firefox o cualquier otro explorador que no sea Microsoft Edge ni Microsoft Internet Explorer en Windows 10 o Windows Server 2016.

2.	Firefox en Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 o Windows Server 2008 R2.
 

![Escenario](./media/active-directory-conditional-access-device-remediation/02.png "Escenario")


La única solución es utilizar un explorador compatible con la plataforma de si dispositivo.

## Acceso desde un dispositivo iOS

Pronto podrá ver instrucciones para iPhone o iPad.

## Acceso desde un dispositivo Android

Pronto podrá ver instrucciones para teléfonos y tabletas con Android.


## Pasos siguientes

[Acceso condicional de Azure Active Directory](active-directory-conditional-access.md)

<!---HONumber=AcomDC_0824_2016-->