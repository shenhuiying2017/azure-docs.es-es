<properties 
	pageTitle="Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS" 
	description="En esta página de Azure Multi-Factor Authentication se describe cómo empezar a trabajar con Azure MFA y AD FS 2.0 en la nube." 
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

# Protección de recursos en la nube con Azure Multi-Factor Authentication y AD FS

Si su organización está federada con Azure Active Directory y dispone de los recursos a los que tiene acceso Azure AD, puede usar Azure Multi-Factor Authentication o Servicios de federación de Active Directory para proteger esos recursos. Utilice los siguientes procedimientos para proteger recursos de Azure Active Directory mediante Azure Multi-Factor Authentication o Servicios de federación de Active Directory.

## Para proteger recursos de Azure AD mediante AD FS, realice lo siguiente: 



1. Utilice los pasos descritos en [Activación de autenticación multifactor para usuarios](multi-factor-authentication-get-started-cloud/#turn-on-multi-factor-authentication-for-users) para habilitar una cuenta.
2. Utilice el siguiente procedimiento para configurar una regla de notificación:

<center>![Cloud](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)</center>

- 	Inicie la consola de administración de AD FS.
- 	Vaya a Relaciones de confianza para usuario autenticado y haga clic con el botón derecho en la relaciones de confianza para usuario autenticado. Seleccione Editar reglas de notificación...
- 	Haga clic en Agregar regla…
- 	En la lista desplegable, seleccione Enviar notificaciones con una regla personalizada y haga clic en Siguiente.
- 	Escriba un nombre para la regla de notificación.
- 	En Regla personalizada: agregue lo siguiente:


		=> issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

	Notificación correspondiente.

		<saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
		<saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
		</saml:Attribute>
- Haga clic en Aceptar. Haga clic en Finish. Cierre la consola de administración de AD FS.

Los usuarios ya pueden completar el inicio de sesión mediante el método local (como una tarjeta inteligente).


 

<!---HONumber=July15_HO2-->