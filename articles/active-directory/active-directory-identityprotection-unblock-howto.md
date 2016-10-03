<properties
	pageTitle="Azure Active Directory Identity Protection: desbloqueo de usuarios | Microsoft Azure"
	description="Aprenda a desbloquear usuarios bloqueados por una directiva de Azure Active Directory Identity Protection."
	services="active-directory"
	keywords="azure active directory identity protection: desbloquear usuario"
	documentationCenter=""
	authors="markusvi"
	manager="femila"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/20/2016"
	ms.author="markvi"/>

#Azure Active Directory Identity Protection: desbloqueo de usuarios

Con Azure Active Directory Identity Protection se pueden configurar directivas para bloquear a los usuarios si se cumplen las condiciones configuradas. Normalmente, los usuarios bloqueados se ponen en contacto con el departamento de soporte técnico para que se desbloqueen. En este tema se explican los pasos que puede dar para desbloquear un usuario bloqueado.


## Determinación del motivo de bloqueo

Como primer paso para desbloquear un usuario, es preciso que determine el tipo de directiva que ha bloqueado al usuario, ya que los siguientes pasos dependen de él. Con Azure Active Directory Identity Protection, se puede bloquear a un usuario con una directiva de riesgo de inicio de sesión o con una directiva de riesgo de usuario.

Puede obtener el tipo de directiva que ha bloqueado un usuario del título del cuadro de diálogo que se presentó al usuario en un intento de inicio de sesión:

|Directiva | Cuadro de diálogo usuario|
|--- | --- |
|Riesgo de inicio de sesión | ![Inicio de sesión bloqueado](./media/active-directory-identityprotection-unblock-howto/02.png) |
|Riesgo de usuario | ![Cuenta bloqueada](./media/active-directory-identityprotection-unblock-howto/104.png) |


Un usuario que está bloqueado por:

- Una directiva de riesgo de inicio de sesión también es conocida como inicio de sesión sospechoso
- Una directiva de riesgo de usuario también es conocida como una cuenta en riesgo

 
## Desbloqueo de inicios de sesión sospechosos

Para desbloquear un inicio de sesión sospechoso, tiene las siguientes opciones:

1. **Iniciar sesión desde una ubicación o dispositivo conocidos**: una razón común para el bloqueo de los inicios de sesión sospechosos son intentos de inicio de sesión desde ubicaciones o dispositivos desconocidos. Los usuarios pueden determinar rápidamente si esta es la razón del bloqueo. Para ello, deben intentar iniciar sesión desde una ubicación o dispositivo conocidos.


3. **Excluir de la directiva**: si piensa que la configuración actual de la directiva de inicio de sesión provoca problemas a usuarios concretos, puede excluir los usuarios de ella. Para más detalles, consulte la [directiva de riesgo de inicio de sesión](active-directory-identityprotection.md#sign-in-risk-policy).
 
4. **Deshabilitar directiva**: si piensa que la configuración de la directiva provoca problemas a todos los usuarios, puede deshabilitar la directiva. Para más detalles, consulte la [directiva de riesgo de inicio de sesión](active-directory-identityprotection.md#sign-in-risk-policy).


## Desbloqueo de cuentas en riesgo

Para desbloquear una cuenta en riesgo, tiene las siguientes opciones:

1. **Restablecer contraseña**: puede restablecer la contraseña del usuario. Para más información, consulte [Restablecimiento manual de una contraseña segura](active-directory-identityprotection.md#manual-secure-password-reset).

2. **Descartar todos los eventos de riesgo**: la directiva de riesgo de usuario bloquea un usuario si se ha alcanzado el nivel de riesgo del usuario configurado para bloquear el acceso. Para reducir el nivel de riesgo de un usuario, cierre manualmente los eventos de riesgo notificados. Para más información, consulte [Cierre manual de eventos de riesgo](active-directory-identityprotection.md#closing-risk-events-manually).

3. **Excluir de la directiva**: si piensa que la configuración actual de la directiva de inicio de sesión provoca problemas a usuarios concretos, puede excluir los usuarios de ella. Para más detalles, consulte la [directiva de riesgo de inicio de usuario](active-directory-identityprotection.md#user-risk-policy).
 
4. **Deshabilitar directiva**: si piensa que la configuración de la directiva provoca problemas a todos los usuarios, puede deshabilitar la directiva. Para más detalles, consulte la [directiva de riesgo de inicio de usuario](active-directory-identityprotection.md#user-risk-policy).




## Pasos siguientes

 ¿Desea más información acerca de Azure AD Identity Protection? Consulte [Azure Active Directory Identity Protection](active-directory-identityprotection.md).
 

<!---HONumber=AcomDC_0921_2016-->