<properties 
	pageTitle="Estados de usuario de Microsoft Azure Multi-Factor Authentication"
	description="Obtenga información sobre los estados de usuario en Azure MFA."
	services="multi-factor-authentication"
	documentationCenter=""
	authors="kgremban"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="multi-factor-authentication"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2016"
	ms.author="kgremban"/>

# Estados de usuario en Azure Multi-Factor Authentication

Las cuentas de usuario de Azure Multi-Factor Authentication tienen los siguientes tres estados:

Estado | Description |Aplicaciones que no son de explorador afectadas| Notas
:-------------: | :-------------: |:-------------: |:-------------: |
Disabled | Estado predeterminado para un nuevo usuario no inscrito en la autenticación multifactor.|No|El usuario no está utilizando la autenticación multifactor.
Enabled |El usuario está inscrito en la autenticación multifactor.|No. Continúa funcionando hasta que se complete el proceso de registro.|El usuario está habilitado pero no ha completado el proceso de registro. Se le pedirá que complete el proceso en el inicio de sesión siguiente.
Aplicado|El usuario se ha inscrito y ha completado el proceso de registro para usar la autenticación multifactor.|Sí. Las aplicaciones requieren contraseñas de aplicación. | El usuario puede haber completado o no el registro. Si ha completado el proceso de registro, está utilizando la autenticación multifactor. De lo contrario, se le pedirá que complete el proceso en el inicio de sesión siguiente.

## Cambio del estado del usuario
El estado del usuario cambia según se haya configurado o no para MFA y si el usuario ha completado el proceso. Al activar MFA para un usuario, el estado de usuario cambiará de deshabilitado a habilitado. Una vez que el usuario, cuyo estado haya cambiado a habilitado, inicie sesión y complete el proceso, el estado cambiará a exigido.

### Para ver el estado de un usuario
--------------------------------------------------------------------------------
1.  Inicie sesión como administrador en el **Portal de Azure clásico**.
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En **Directorio**, haga clic en el directorio para el usuario que desea habilitar. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **Usuarios**.
5.  En la parte inferior de la página, haga clic en **Admin. Multi-Factor Auth**. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  De este modo se abrirá una nueva pestaña del explorador. Podrá ver el estado del usuario. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-user-states/userstate1.png)

###Para cambiar el estado de deshabilitado a habilitado
1.  Inicie sesión como administrador en el **Portal de Azure clásico**.
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En **Directorio**, haga clic en el directorio para el usuario que desea habilitar. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **Usuarios**.
5.  En la parte inferior de la página, haga clic en **Admin. Multi-Factor Auth**. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  De este modo se abrirá una nueva pestaña del explorador. Busque el usuario que desea habilitar para la autenticación multifactor. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **deshabilitado.** ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque una **marca de comprobación** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Habilitar**. ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Haga clic en **Habilitar Multi-Factor Auth**. ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Observe que el estado del usuario ha cambiado de **deshabilitado** a **habilitado**. ![Habilitar usuarios](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Una vez habilitados a los usuarios, se recomienda que se les notifique por correo electrónico. También debe informarles cómo pueden utilizar sus aplicaciones sin explorador para evitar que se les bloquee.

### Para cambiar el estado de habilitado/exigido a deshabilitado
1.  Inicie sesión como administrador en el **Portal de Azure clásico**.
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En **Directorio**, haga clic en el directorio para el usuario que desea habilitar. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **Usuarios**.
5.  En la parte inferior de la página, haga clic en **Admin. Multi-Factor Auth**. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  De este modo se abrirá una nueva pestaña del explorador. Busque el usuario que desee deshabilitar. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **habilitado** o **exigido.**
7.  Coloque una **marca de comprobación** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Deshabilitar**. ![Deshabilitar usuario](./media/multi-factor-authentication-get-started-user-states/userstate2.png)
8.  Se le pedirá que confirme esto. Haga clic en **Sí**. ![Deshabilitar usuario](./media/multi-factor-authentication-get-started-user-states/userstate3.png)
9.  A continuación, verá que la operación se realizó correctamente. Haga clic en **Cerrar.** ![Deshabilitar usuario](./media/multi-factor-authentication-get-started-user-states/userstate4.png)

<!---HONumber=AcomDC_0921_2016-->