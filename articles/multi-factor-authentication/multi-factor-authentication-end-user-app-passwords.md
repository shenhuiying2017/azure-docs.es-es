<properties 
	pageTitle="¿Qué son las contraseñas de aplicación en Azure MFA?" 
	description="Esta página ayudará a los usuarios a entender qué son las contraseñas de aplicación y para qué se utilizan con respecto a Azure MFA." 
	services="multi-factor-authentication" 
	documentationCenter="" 
	authors="billmath" 
	manager="swadhwa" 
	editor="curtand"/>

<tags 
	ms.service="multi-factor-authentication" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/02/2015" 
	ms.author="billmath"/>




# ¿Qué son las contraseñas de aplicación en Azure Multi-Factor Authentication?

Determinadas aplicaciones sin explorador, como el cliente de correo electrónico nativo de Apple que usa Exchange Active Sync, actualmente no admiten la autenticación multifactor. La autenticación multifactor se habilita por usuario. Esto significa que si un usuario se ha habilitado para la autenticación multifactor y está intentando utilizar aplicaciones sin explorador, no podrá hacerlo. Una contraseña de aplicación permite que pueda hacerlo.

>[AZURE.NOTE]Autenticación moderna para los clientes de Office 2013
>
> Los clientes de Office 2013 (incluye Outlook) ahora admiten nuevos protocolos de autenticación que pueden habilitarse para admitir Multi-Factor Authentication. Esto significa que una vez habilitada, las contraseñas de aplicación no son necesarias para los clientes de Office 2013. Para obtener más información, consulte [Anuncio de la vista previa pública de la autenticación moderna de Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).
 
## Cómo utilizar las contraseñas de aplicación

Las siguientes son algunas cosas que es necesario recordar sobre el uso de contraseñas de aplicación.

- La contraseña real se genera automáticamente y no la proporciona el usuario. Esto es así porque una contraseña generada automáticamente es más difícil de adivinar y por tanto es más segura.
- Actualmente hay un límite de 40 contraseñas por usuario. Si intenta crear más una vez que haya alcanzado el límite, se le pedirá que elimine una de las contraseñas de aplicación existentes para poder crear una nueva.
- Se recomienda crear contraseñas de aplicación para cada dispositivo y no para cada aplicación. Por ejemplo, puede crear una contraseña de aplicación para su equipo portátil y utilizar esa contraseña de aplicación para todas las aplicaciones de ese equipo portátil.
- Lla primera vez que inicie sesión se le da una contraseña de aplicación. Si necesita otras adicionales, puede crearlas.
 
<center>![Setup](./media/multi-factor-authentication-end-user-app-passwords/app.png)</center>

Una vez que tenga una contraseña de aplicación, utilícela en lugar de la contraseña original para estas aplicaciones sin explorador. Por ejemplo, si está utilizando la autenticación multifactor y el cliente de correo electrónico nativo de Apple en su teléfono. Utilice la contraseña de aplicación para omitir la autenticación multifactor y poder seguir trabajando.

### Creación de contraseñas de aplicación
Durante el inicio de sesión primero, se proporciona una contraseña de aplicación que puede utilizar. Además también puede crear contraseñas de aplicación más adelante. La forma de hacerlo depende de cómo use la autenticación multifactor. Elija la opción que le sea más aplicable.

Cómo usa la autenticación multifactor|Descripción
:------------- | :------------- | 
[La uso con Office 365](multi-factor-authentication-end-user-manage-o365.md)| Esto significa que tendrá que crear las contraseñas de aplicación a través del portal de Office 365.
[La uso con Microsoft Azure](multi-factor-authentication-end-user-manage-azure.md)| Esto significa que tendrá que crear las contraseñas de aplicación a través del Portal de Azure.
[No lo sé](multi-factor-authentication-end-user-manage-myapps.md)|Esto significa que tendrá que crear las contraseñas de aplicación a través de [https://myapps.microsoft.com](https://myapps.microsoft.com)




 

<!---HONumber=August15_HO6-->