<properties
	pageTitle="Vista previa de Servicios de dominio de Azure Active Directory: Guía de solución de problemas | Microsoft Azure"
	description="Guía de solución de problemas Servicios de dominio de Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/16/2015"
	ms.author="maheshu"/>

# Servicios de dominio de Azure AD *(vista previa)*: Guía de solución de problemas
En este artículo se ofrecen sugerencias de solución de problemas para los problemas que puede encontrar al configurar o administrar Servicios de dominio de Azure Active Directory (AD).


### Los usuarios no pueden iniciar sesión en el dominio administrado de los Servicios de dominio de Azure AD
Si encuentra una situación en la que uno o más usuarios de su inquilino de Azure AD no pueden iniciar sesión en el dominio administrado creado recientemente, lleve a cabo los siguientes pasos de solución de problemas:

- Asegúrese de que ha [habilitado la sincronización de contraseñas](active-directory-ds-getting-started-password-sync.md) según los pasos que se describen en la Guía de introducción.

- Asegúrese de que la cuenta de usuario afectada no es una cuenta externa en el inquilino de Azure AD. Entre los ejemplos de las cuentas externas se incluyen las cuentas de Microsoft (por ejemplo, 'joe@live.com') o las cuentas de usuario de un directorio de Azure AD externo. Puesto que los Servicios de dominio de Azure AD no tienen las credenciales de dichas cuentas de usuario, estos usuarios no pueden iniciar sesión el dominio administrado.

- Asegúrese de que el prefijo del UPN de la cuenta de usuario afectada (es decir, la primera parte del UPN) del inquilino de Azure AD tiene menos de 20 caracteres de longitud. Por ejemplo, para el UPN 'joereallylongnameuser@contoso.com', el prefijo ('joereallylongnameuser') supera los 20 caracteres y esta cuenta no estará disponible en el dominio administrado de Servicios de dominio de Azure AD.

- **Sincronizar cuentas:** si las cuentas de usuario afectadas se sincronizan desde un directorio local, asegúrese de que se siguen los siguientes pasos:
    - Ha implementado o actualizado a la versión más reciente recomendada de Azure AD Connect.
    - Ha creado la clave del Registro necesaria para habilitar la sincronización de las credenciales heredadas con Azure AD.
    - Después de crear la clave del Registro mencionada anteriormente en el servidor que ejecuta Azure AD Connect, ha aplicado Azure AD para realizar una sincronización completa como se describe en el documento.
    - En función del tamaño de su directorio, se puede tardar algo en que las cuentas de usuario y los hash de credenciales estén disponibles en Servicios de dominio de Azure AD. Asegúrese de esperar el tiempo suficiente antes de volver a intentar la autenticación (depende del tamaño de su directorio, desde unas horas a un día o dos, para directorios grandes).

- **Cuentas de solo en la nube**: si la cuenta de usuario afectada es una cuenta de usuario de solo en la nube, asegúrese de que el usuario ha cambiado su contraseña después de habilitar Servicios de dominio de Azure AD. Este paso hace que se generen los hash de credenciales necesarios para los Servicios de dominio de Azure AD


### Ponerse en contacto con nosotros
Si tiene problemas con su dominio administrado, compruebe si los pasos descritos en esta guía de solución de problemas resuelven el problema. Si sigue teniendo problemas, no dude en ponerse en contacto con nosotros en:

- El [canal de Voz de usuario de Azure Active Directory](http://feedback.azure.com/forums/169401-azure-active-directory). Asegúrese de escribir delante de su pregunta **'AADDS'** para ponerse en contacto con nosotros.
- También puede enviarnos un mensaje de correo electrónico a [Comentarios de Servicios de dominio de Azure AD](mailto:aaddsfb@microsoft.com).

<!---HONumber=AcomDC_1217_2015-->