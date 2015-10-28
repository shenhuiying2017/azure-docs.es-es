<properties
	pageTitle="Habilitación del acceso híbrido con el proxy de aplicación| Microsoft Azure"
	description="Habilite el acceso a aplicaciones que se ejecutan dentro de su red privada desde fuera de la red mediante Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/06/2015"
	ms.author="femila"/>

#Habilitación del acceso híbrido con el proxy de aplicación
Con el proxy de aplicación de Microsoft Azure AD, puede publicar aplicaciones que se ejecutan dentro de la red privada, de forma que se pueda acceder a ellas de forma segura, en cualquier dispositivo, desde fuera de la red. El proxy de aplicación de Azure AD sirve para publicar aplicaciones dentro de su red privada local o en la nube. Se puede configurar como un servicio incluido en Azure AD y solo requiere que se instale un conector de proxy de aplicación en un servidor dentro de su entorno.

##Cómo funciona

En los pasos siguientes, se indica cómo las aplicaciones se pueden publicar en un entorno híbrido.
 
1.	Los conectores se implementan en la red corporativa. (Se pueden implementar varios conectores para mejorar la redundancia y escalabilidad).
2.	El conector se conecta automáticamente al servicio en la nube.
3.	El usuario se conecta al servicio en la nube que enruta el tráfico a los recursos a través de los conectores.

En el diagrama siguiente, se proporciona información detallada sobre cómo funciona el proxy de aplicación:

 ![Cómo funciona el proxy de aplicación de Azure AD](./media/active-directory-appssoaccess-whatis/azureappproxxy.png)

 
1.	El usuario accede a la aplicación a través del proxy de aplicación y se le dirige a la página de inicio de sesión de Azure AD para que se autentique.
2.	Después de iniciar sesión correctamente, se genera un token y se envía al usuario.
3.	Ahora, el usuario enviará el token al proxy de aplicación, que recuperará el nombre principal de usuario (UPN) y el nombre de entidad de seguridad (SPN) del token y dirigirá la solicitud al conector.
4.	El conector suplanta al usuario para solicitar un vale Kerberos que se puede usar para la autenticación interna (Windows). (Delegación limitada de Kerberos)
5.	 Se recupera un vale Kerberos de Active Directory.
6.	El vale recuperado se envía al servidor de aplicaciones donde se comprueba.
7.	 La respuesta se enviará a través del proxy de aplicación al usuario final. Solo existe un requisito para publicar una aplicación web: la aplicación web en su red privada debe ser accesible para el servidor donde esté instalado el conector. Es decir, puede instalar el conector en los servidores de aplicaciones en sí o en cualquier otro servidor del entorno; lo importante es que el conector pueda acceder a la aplicación web.

##Integración de aplicaciones locales a través del proxy de aplicación de Azure AD
El panel de acceso en Azure AD permite publicar aplicaciones locales en Azure AD. Se deben seguir estos pasos:

1. Habilite el proxy de aplicación en Azure AD e instale y registre el conector. Para obtener instrucciones detalladas, consulte la página sobre cómo [habilitar el proxy de aplicación de Azure AD](active-directory-application-proxy-enable/#step-1-enable-application-proxy-in-azure-ad.md).
2. Publique las aplicaciones mediante el proxy de aplicación de Azure AD. Para obtener instrucciones detalladas, consulte [Publicación de aplicaciones mediante el proxy de aplicación de Azure AD](active-directory-application-proxy-publish.md).

<!---HONumber=Oct15_HO3-->