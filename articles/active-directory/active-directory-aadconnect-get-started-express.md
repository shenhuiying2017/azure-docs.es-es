<properties
	pageTitle="Introducción a la configuración rápida de Azure AD Connect | Microsoft Azure"
	description="Obtenga información acerca de cómo descargar, instalar y ejecutar el asistente para instalación de Azure AD Connect."
	services="active-directory"
	documentationCenter=""
	authors="andkjell"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="09/13/2016"
	ms.author="billmath;andkjell"/>

# Introducción a Azure AD Connect mediante la configuración rápida
Se utiliza **Configuración rápida** de Azure AD Connect cuando se dispone de una topología de bosque único y de [sincronización de contraseña](active-directory-aadconnectsync-implement-password-synchronization.md) para la autenticación. **Configuración rápida** es la opción predeterminada y se utiliza en los escenarios implementados más habituales. Ya solo le faltan uno cuantos clics para extender su directorio local a la nube.

Antes de empezar a instalarlo, debe [descargar Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771) y completar los pasos de los requisitos previos en [Requisitos previos de Azure AD Connect](active-directory-aadconnect-prerequisites.md).

Si la configuración rápida no coincide con su topología, consulte la [documentación relacionada](#related-documentation) sobre otros escenarios.

## Instalación rápida de Azure AD Connect
Puede ver estos pasos de acción en la sección [Vídeos](#videos).

1. Inicie sesión como administrador local en el servidor en el que quiere instalar Azure AD Connect. Debe ser el servidor que quiere que sea el servidor de sincronización.
2. Navegue hasta **AzureADConnect.msi** y haga doble clic en él.
3. En la pantalla de bienvenida, active la casilla que acepta los términos de licencia y haga clic en **Continuar**.
4. En la pantalla Configuración rápida, haga clic en **Usar configuración rápida**. ![Bienvenida a Azure AD Connect](./media/active-directory-aadconnect-get-started-express/express.png)
5. En la pantalla Conectar a Azure AD, escriba el nombre de usuario y la contraseña de un administrador global de Azure AD. Haga clic en **Next**. ![Conectarse a Azure](./media/active-directory-aadconnect-get-started-express/connectaad.png) Si aparece un error y tiene problemas de conectividad, consulte [Solución de problemas de conectividad con Azure AD Connect](active-directory-aadconnect-troubleshoot-connectivity.md).
6. En la pantalla Conectar a AD DS, escriba el nombre de usuario y la contraseña de una cuenta de administrador de empresa. Puede escribir la parte del dominio con el formato NetBios o FQDN, es decir, FABRIKAM\\administrator o fabrikam.com\\administrator. Haga clic en **Siguiente**. ![Conectarse a AD DS](./media/active-directory-aadconnect-get-started-express/connectad.png)
7. Solo se mostrará la página [**Configuración de inicio de sesión de Azure AD**](active-directory-aadconnect-user-signin.md#azure-ad-sign-in-configuration) si no completó la [comprobación de dominios](active-directory-add-domain.md) en los [requisitos previos](active-directory-aadconnect-prerequisites.md). ![Dominios sin comprobar](./media/active-directory-aadconnect-get-started-express/unverifieddomain.png) Si ve esta página, revise los dominios marcados como **Not Added** (Sin agregar) y **Not Verified** (Sin comprobar). Asegúrese de que los dominios que usa se han comprobado en Azure AD. Cuando haya comprobado los dominios, haga clic en el símbolo de actualización.
8. En la pantalla Listo para configurar, haga clic en **Instalar**.
	- En la página Listo para configurar, puede desactivar la casilla **Start the synchronization process as soon as configuration completes** (Iniciar el proceso de sincronización en cuanto finalice la configuración). Si quiere realizar alguna configuración adicional, por ejemplo, el [filtrado](active-directory-aadconnectsync-configure-filtering.md), desactive esta casilla. Si desactiva esta opción, el asistente configura la sincronización, pero deja el programador deshabilitado, y no se ejecuta hasta que lo habilite manualmente [volviendo a ejecutar el Asistente para la instalación](active-directory-aadconnectsync-installation-wizard.md).
	- Si tiene Exchange en su entorno de Active Directory local, también dispone de la opción para habilitar una [**implementación híbrida de Exchange**](https://technet.microsoft.com/library/jj200581.aspx). Habilite esta opción si planea tener buzones de Exchange tanto en la nube como locales de manera simultánea. ![Listo para configurar Azure AD Connect](./media/active-directory-aadconnect-get-started-express/readytoconfigure.png)
9. Una vez completada la instalación, haga clic en **Salir**.
10. Una vez completada la instalación, cierre la sesión e inicie de sesión de nuevo antes de utilizar Synchronization Service Manager o el Editor de reglas de sincronización.

## Vídeos

Para ver un vídeo sobre el uso de la instalación rápida, consulte:

>[AZURE.VIDEO azure-active-directory-connect-express-settings]

## Pasos siguientes
Ahora que ha instalado Azure AD Connect, puede [comprobar la instalación y asignar licencias](active-directory-aadconnect-whats-next.md).

Aprenda más sobre estas características, que se habilitaron con la instalación: la [actualización automática](active-directory-aadconnect-feature-automatic-upgrade.md), la característica para [evitar eliminaciones accidentales](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) y [Azure AD Connect Health](active-directory-aadconnect-health-sync.md).

Para más información acerca de estos temas habituales, consulte [Sincronización de Azure AD Connect: Programador](active-directory-aadconnectsync-feature-scheduler.md).

Obtenga más información sobre la [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md).

## Documentación relacionada

Tema. |  
--------- | ---------
Información general de Azure AD Connect | [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)
Instalación mediante configuración personalizada | [Instalación personalizada de Azure AD Connect](active-directory-aadconnect-get-started-custom.md)
Actualización desde DirSync | [Actualización desde la herramienta Sincronización de Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)
Cuentas usadas para la instalación | [Más información sobre permisos y cuentas de Azure AD Connect](active-directory-aadconnect-accounts-permissions.md)

<!---HONumber=AcomDC_0914_2016-->