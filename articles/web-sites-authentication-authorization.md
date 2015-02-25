<properties 
	pageTitle="Autenticación y autorización de aplicaciones de línea de negocio en sitios web Azure" 
	description="Conozca las distintas opciones de autenticación y autorización de las aplicaciones de línea de negocio que se implementan en sitios web Azure" 
	services="web-sites" 
	documentationCenter="" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="12/23/2014" 
	ms.author="cephalin"/>

# Autenticación y autorización de los usuarios de aplicaciones de línea de negocio en sitios web Azure #

[Sitios web Azure](http://azure.microsoft.com/es-es/services/websites/) habilita escenarios de aplicaciones de línea de negocio empresariales al ser compatible con el inicio de sesión único (SSO) de los usuarios, ya tengan acceso a la aplicación desde el entorno local o desde la internet pública. Se puede integrar con [Azure Active Directory](http://azure.microsoft.com/es-es/services/active-directory/) (AAD) o con el servicio de token seguro local (STS), como los servicios de federación de Active Directory (AD FS), para autenticar a los usuarios de Active Directory (AD) interno y autorizarles correctamente.

## Autorización y autenticación sin fricción ##

Con solo unos clics del botón, puede habilitar la autenticación y autorización para su sitio web. La configuración de estilo de la casilla de verificación en cada sitio web de Azure proporciona el control de acceso básico de su sitio web LOB. Para ello, impone HTTPS y la autenticación en un inquilino de Azure AD de su elección antes de conceder a los usuarios acceso al contenido del sitio web. Para obtener más información, consulte [Autenticación y autorización de Sitios web Azure](http://azure.microsoft.com/blog/2014/11/13/azure-websites-authentication-authorization/).

>[AZURE.NOTE] Esta característica está actualmente en vista previa.

## Implementación manual de la autenticación y autorización ##

En muchos escenarios, deseará personalizar el comportamiento de la autenticación y autorización de la aplicación, como una página de inicio de sesión y cierre de sesión, lógica de autorización personalizada, comportamiento de la aplicación multinquilino, etc. En estos casos, puede ser mejor configurar la autenticación y autorización manualmente para conseguir una mayor flexibilidad de sus características. A continuación se muestran dos opciones principales  

-	[Azure AD](../web-sites-dotnet-lob-application-azure-ad/) : puede implementar autenticación y autorización en su sitio web con Azure AD. El uso de Azure AD como proveedor de identidades tiene las siguientes características:
	-	Admite los protocolos de autenticación populares, como [OAuth 2.0](http://oauth.net/2/), [OpenID Connect](http://openid.net/connect/) y [SAML 2.0](http://en.wikipedia.org/wiki/SAML_2.0). Para obtener una lista completa de los protocolos admitidos, consulte [Protocolos de autenticación de Azure Active Directory](http://msdn.microsoft.com/es-es/library/azure/dn151124.aspx).
	-	Puede usar un proveedor de identidad solo de Azure sin ninguna infraestructura local.
	-	También se puede configurar la sincronización de directorios con un AD local (administrado local).
	-	Azure AD con sincronización de directorios de su dominio de AD local permite una experiencia SSO sin problemas en su sitio web cuando los usuarios de AD acceden desde la intranet y desde internet. Desde la intranet, los usuarios de AD pueden acceder automáticamente al sitio web mediante la autenticación integrada. Desde internet, los usuarios de AD pueden iniciar sesión en el sitio web con sus credenciales de Windows.
	-	Proporciona SSO para [todas las aplicaciones compatibles con Azure AD](http://azure.microsoft.com/es-es/marketplace/active-directory/), incluidas Azure, Office 365, Dynamics CRM Online, Windows InTune y miles de aplicaciones de la nube que no son de Microsoft. 
	-	Azure AD delega la administración de las aplicaciones [de usuario de confianza](http://en.wikipedia.org/wiki/Relying_party) a los roles no de administrador, aunque el acceso de las aplicaciones a los datos de directorios confidenciales de deben seguir configurándolo los administradores globales.
	-	Envía un conjunto de uso general de los tipos de notificaciones de todas las aplicaciones de usuario de confianza. Para obtener la lista de tipos de notificaciones, consulte [Tokens y tipos de notificaciones admitidos](http://msdn.microsoft.com/es-es/library/azure/dn195587.aspx). Las notificaciones no son personalizables.
	-	[La API de Azure AD Graph](http://msdn.microsoft.com/es-es/library/azure/hh974476.aspx) permite el acceso de la aplicación a datos de directorios en Azure AD.
-	[Servicio de token seguro (STS) local, como AD FS](../web-sites-dotnet-lob-application-adfs/): puede implementar autenticación y autorización en su sitio web con un STS local como AD FS. El uso de AD FS local tiene las siguientes características:
	-	La topología de AD FS debe implementarse de forma local, con sobrecarga de administración y coste.
	-	Es la mejor opción cuando la directiva de la compañía requiere que los datos de AD se almacenen de forma local.
	-	Solo los administradores de AD FS pueden configurar [relaciones de confianza para usuario autenticado y reglas de notificaciones](http://technet.microsoft.com/es-es/library/dd807108.aspx).
	-	Puede administrar [notificaciones](http://technet.microsoft.com/es-es/library/ee913571.aspx) según la aplicación.
	-	Debe tener una solución independiente para tener acceso a datos de AD locales a través del firewall corporativo.




<!--HONumber=42-->
