<properties 
	pageTitle="Introducción a Microsoft Azure Multi-Factor Authentication en la nube" 
	description="En esta página de Microsoft Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA en la nube." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="stevenpo" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="08/04/2016" 
	ms.author="billmath"/>

# Introducción a Azure Multi-Factor Authentication en la nube
En este artículo aprenderá a usar Azure Multi-Factor Authentication en la nube.

> [AZURE.NOTE]  La siguiente documentación proporciona información sobre cómo habilitar usuarios mediante el **Portal de Azure clásico**. Si busca información sobre cómo configurar Azure Multi-Factor Authentication para usuarios de Office 365, consulte [Configurar la autenticación multifactor para usuarios de Office 365](https://support.office.com/article/Set-up-multi-factor-authentication-for-Office-365-users-8f0454b2-f51a-4d9c-bcde-2c48e41621c6?ui=es-ES&rs=es-ES&ad=US).

![MFA en la nube](./media/multi-factor-authentication-get-started-cloud/mfa_in_cloud.png)

## Requisitos previos
Los siguientes requisitos previos son necesarios para poder habilitar Azure Multi-Factor Authentication para los usuarios.




- [Registrarse para obtener una suscripción a Azure](https://azure.microsoft.com/pricing/free-trial/): si todavía no dispone de una suscripción de Azure, es necesario que se registre para obtener una. Si simplemente está comenzando a utilizar Azure MFA, puede usar una suscripción de prueba.
2. [Crear un Proveedor de Multi-Factor Authentication](multi-factor-authentication-get-started-auth-provider.md) y asignarlo a su directorio o [asignar licencias a sus usuarios](multi-factor-authentication-get-started-assign-licenses.md).

> [AZURE.NOTE]  Las licencias están disponibles para los usuarios con Azure MFA, Azure AD Premium o Enterprise Mobility Suite (EMS). MFA se incluye en Azure AD Premium y en EMS. Si dispone de suficientes licencias, no necesitará crear un proveedor de autenticación.
		

## Activación de la autenticación multifactor para usuarios
Para activar la autenticación multifactor para un usuario, simplemente cambie el estado del usuario de deshabilitado a habilitado. Para más información sobre los estados de usuario, consulte [User States in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md) (Estados de usuario en Azure Multi-Factor Authentication).

Utilice el procedimiento siguiente para habilitar MFA para los usuarios.

### Activación de Azure Multi-Factor Authentication
--------------------------------------------------------------------------------
1.  Inicie sesión como administrador en el **Portal de Azure clásico**.
2.  En la parte izquierda, haga clic en **Active Directory**.
3.  En **Directorio**, haga clic en el directorio para el usuario que desea habilitar. ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/directory1.png)
4.  En la parte superior, haga clic en **Usuarios**.
5.  En la parte inferior de la página, haga clic en **Admin. Multi-Factor Auth.** ![Hacer clic en Directorio](./media/multi-factor-authentication-get-started-cloud/manage1.png)
6.  De este modo se abrirá una nueva pestaña del explorador. Busque el usuario que desea habilitar para la autenticación multifactor. Puede que necesite cambiar la vista en la parte superior. Asegúrese de que el estado es **deshabilitado.** ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable1.png)
7.  Coloque una **marca de comprobación** en el cuadro junto a su nombre.
7.  A la derecha, haga clic en **Habilitar**. ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/user1.png)
8.  Haga clic en **Habilitar Multi-Factor Auth**. ![Habilitar usuario](./media/multi-factor-authentication-get-started-cloud/enable2.png)
9.  Observe que el estado del usuario ha cambiado de **deshabilitado** a **habilitado**. ![Habilitar usuarios](./media/multi-factor-authentication-get-started-cloud/user.png)
10.  Una vez habilitados a los usuarios, se recomienda que se les notifique por correo electrónico. También debe informarles cómo pueden utilizar sus aplicaciones sin explorador para evitar que se les bloquee.


## Automatización de la activación de la autenticación multifactor con PowerShell

Para cambiar el [estado](multi-factor-authentication-whats-next.md) con [Azure AD PowerShell](../powershell-install-configure.md), puede utilizar lo siguiente. Puede cambiar `$st.State` para que sea igual a uno de los estados siguientes:


- Enabled
- Aplicado
- Disabled

> [AZURE.IMPORTANT]  Tenga en cuenta que si pasa directamente del estado deshabilitado al estado forzado, los clientes de autenticación no moderna dejarán de funcionar porque el usuario no ha pasado por el registro de MFA ni ha obtenido una [contraseña de aplicación](multi-factor-authentication-whats-next.md#app-passwords). Si tiene clientes de autenticación no moderna y requiere contraseñas de aplicación, se recomienda que pase del estado Deshabilitado al Habilitado. Esto permitirá a los usuarios registrar y obtener sus contraseñas de aplicación.
		
		$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
		$st.RelyingParty = "*"
		$st.State = “Enabled”
		$sta = @($st)
		Set-MsolUser -UserPrincipalName bsimon@contoso.com -StrongAuthenticationRequirements $sta

El uso de PowerShell sería una opción para habilitar usuarios de forma masiva. Actualmente no hay ninguna característica de habilitación de forma masiva en el Portal de Azure y debe seleccionar cada usuario individualmente. Si tiene muchos usuarios, esto puede ser bastante trabajo. Con la creación de un script de PowerShell mediante las opciones anteriores, puede recorrer en iteración una lista de usuarios y habilitarlos. Este es un ejemplo:
    
    $users = "bsimon@contoso.com","jsmith@contoso.com","ljacobson@contoso.com"
    foreach ($user in $users)
    {
    	$st = New-Object -TypeName Microsoft.Online.Administration.StrongAuthenticationRequirement
    	$st.RelyingParty = "*"
    	$st.State = “Enabled”
    	$sta = @($st)
    	Set-MsolUser -UserPrincipalName $user -StrongAuthenticationRequirements $sta
    }


Para más información sobre los estados de usuario, consulte [User States in Azure Multi-Factor Authentication](multi-factor-authentication-get-started-user-states.md) (Estados de usuario en Azure Multi-Factor Authentication).

## Pasos siguientes
Ahora que ha configurado la autenticación multifactor en la nube, puede configurar la implementación. Consulte [Configuración de Azure Multi-Factor Authentication].

<!---HONumber=AcomDC_0810_2016-->