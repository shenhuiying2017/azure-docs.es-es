<properties 
	pageTitle="Informes de Azure Multi-Factor Authentication" 
	description="Describe cómo cambiar opciones de la configuración del usuario, como por ejemplo forzar a los usuarios realizar nuevamente el proceso proofup." 
	documentationCenter="" 
	services="multi-factor-authentication" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/17/2015" 
	ms.author="billmath"/>

# Administración de la configuración de usuario con Azure Multi-Factor Authentication en la nube

Como administrador, puede gestionar las siguientes opciones de configuración de usuario y de dispositivo.

- [Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto](#require-selected-users-to-provide-contact-methods-again)
- [Eliminar las contraseñas de aplicación existentes de los usuarios](#delete-users-existing-app-passwords)
- [Restaurar MFA en todos los dispositivos suspendidos para un usuario (vista previa pública)](#restore-mfa-on-all-suspended-devices-for-a-user)






Esto resulta útil si un equipo o dispositivo se pierde o es objeto de un robo o si tiene que quitar el acceso de un usuario.


## Solicitud a los usuarios seleccionados para que vuelvan a proporcionar métodos de contacto

Esto obliga a un usuario a volver a realizar el proceso de registro cuando inicia sesión. Tenga en cuenta que las aplicaciones sin explorador continuarán funcionando si el usuario tiene las contraseñas de aplicación para ellas. Puede eliminar las contraseñas de aplicación de los usuarios seleccionando también **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**.

### Cómo requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto

<ol>
<li>Inicie sesión en el Portal de administración de Azure.</li>
<li>En la parte izquierda, haga clic en Active Directory.</li>
<li>En Directorio, haga clic en el directorio para el usuario al que desea requerir que vuelva a proporcionar su método de contacto.</li>
<li>En la parte superior, haga clic en Usuarios.</li>
<li>En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth. Con esto se abrirá la página de Multi-Factor Authentication. <li>Busque al usuario que desea administrar y active la casilla situada junto a su nombre. Puede que necesite cambiar la vista en la parte superior.</li> <li>Se abrirá el vínculo **Administrar configuración de usuario** a la derecha. Haga clic en él.</li> <li>Marque la opción **Requerir a los usuarios seleccionados que vuelvan a proporcionar métodos de contacto**.</li>

![Proporcionar métodos de contacto](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>Haga clic en Guardar.</li>
<li>Haga clic en Cerrar</li>

## Eliminación de las contraseñas de aplicación existentes de los usuarios

Con esto se eliminan todas las contraseñas de aplicación que ha creado un usuario. Las aplicaciones sin explorador asociadas con estas contraseñas de aplicación dejarán de funcionar hasta que se cree una nueva contraseña de aplicación.

### Cómo eliminar las contraseñas de aplicación existentes de los usuarios

<ol>
<li>Inicie sesión en el Portal de administración de Azure.</li>
<li>En la parte izquierda, haga clic en Active Directory.</li>
<li>En Directorio, haga clic en el directorio del usuario para el que desea eliminar las contraseñas de aplicación.</li>
<li>En la parte superior, haga clic en Usuarios.</li>
<li>En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth. Con esto se abrirá la página de Multi-Factor Authentication. <li>Busque al usuario que desea administrar y active la casilla situada junto a su nombre. Puede que necesite cambiar la vista en la parte superior.</li> <li>Se abrirá el vínculo **Administrar configuración de usuario** a la derecha. Haga clic en él.</li> <li>Coloque una marca de verificación en la opción **Eliminar todas las contraseñas de aplicación existentes generadas por los usuarios seleccionados**.</li> ![Eliminar contraseñas de aplicación](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)<li>Haga clic en Guardar.</li> <li>Haga clic en Cerrar.</li>





## Restauración de MFA en todos los dispositivos suspendidos para un usuario

Los administradores tienen la capacidad de restablecer Multi-Factor Authentication en sus dispositivos y exploradores. Esto se hace mediante la restauración de Multi-Factor Authentication para los dispositivos y exploradores de un usuario. Al hacer esto, se quitará la suspensión de todos los dispositivos y exploradores del usuario.

### Cómo restaurar MFA en todos los dispositivos suspendidos para un usuario

<ol>
<li>Inicie sesión en el Portal de administración de Azure.</li>
<li>En la parte izquierda, haga clic en Active Directory.</li>
<li>En Directorio, haga clic en el directorio del usuario para el que desea restaurar MFA.</li>
<li>En la parte superior, haga clic en Usuarios.</li>
<li>En la parte inferior de la página, haga clic en Admin. Multi-Factor Auth. Con esto se abrirá la página de Multi-Factor Authentication. <li>Busque al usuario que desea administrar y active la casilla situada junto a su nombre. Puede que necesite cambiar la vista en la parte superior.</li> <li>Se abrirá el vínculo **Administrar configuración de usuario** a la derecha. Haga clic en él.</li> <li>Coloque una marca de verificación en la opción Restaurar Multi-Factor Authentication en todos los dispositivos suspendidos.</li> ![Eliminar contraseñas de aplicación](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)<li>Haga clic en Guardar.</li> <li>Haga clic en Cerrar.</li>

<!---HONumber=Nov15_HO4-->