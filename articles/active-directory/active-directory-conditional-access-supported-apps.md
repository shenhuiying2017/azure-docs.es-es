
<properties
	pageTitle="Acceso condicional: aplicaciones que se admiten | Microsoft Azure"
	description="Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
    services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="swadhwa"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
	ms.date="06/23/2016"
	ms.author="femila"/>


# Compatibilidad con el acceso condicional de las aplicaciones

Las reglas de acceso condicional pueden utilizarse en las aplicaciones conectadas de Azure AD, las aplicaciones SaaS federadas previamente integradas, las aplicaciones que utilizan inicio de sesión único con contraseña, y las aplicaciones de línea de negocio y el Proxy de la aplicación de Azure AD. Para ver una lista detallada de las aplicaciones en las que puede habilitar el acceso condicional, consulte [Servicios habilitados con acceso condicional](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). El acceso condicional funciona con aplicaciones móviles y de escritorio que usan autenticación moderna. En este tema se explican las compatibilidades con respecto a las versiones de escritorio y móvil de estas aplicaciones.

 Las aplicaciones con autenticación moderna pueden mostrar páginas de inicio de sesión de Azure AD. Esto permite que se solicite a los usuarios en línea autenticación multifactor o que se muestre un mensaje accesible para el usuario final cuando se bloquea el acceso. Es importante comprender cuáles con las aplicaciones que se admiten, así como los pasos que pueden ser necesarios para proteger otros puntos de entrada.

## Aplicaciones que usan autenticación moderna
Las siguientes aplicaciones se han probado con la autenticación multifactor (MFA) y la directiva de ubicación establecida en el servicio de destino.

| Application | Servicio de destino | Plataforma |
|--------------|-----------------|----------------------------------------------------------------|
| Outlook 2016 | Exchange | Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
| Outlook 2013 (es necesario que la autenticación moderna esté habilitada)| Exchange |Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Skype Empresarial (con autenticación moderna)|Exchange (se accede al calendario y al historial de conversaciones de Exchange)| Windows 10, Windows 8.1, Windows 7 |
|Aplicación móvil de Outlook|Exchange| iOS y Android |
|Office 2016; Word, Excel, Sharepoint|SharePoint| Windows 10, Windows Mobile 10, Windows 8.1, Windows 7, Mac |
|Office 2013 (es necesario que la autenticación moderna esté habilitada)|SharePoint|Windows 10, Windows Mobile 10, Windows 8.1, Windows 7|
|Aplicación de Dynamics CRM|Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Aplicación de Yammer|Yammer| Windows Mobile 10, iOS, Android|
|Azure RemoteApp|Servicio Azure Remote App|Windows 10, Windows 8.1, Windows 7,Mac, iOS, Android|

## Aplicaciones que no usan autenticación moderna

Actualmente, es necesario utilizar otros métodos para bloquear el acceso a las aplicaciones que no usan autenticación moderna, dado que no se les exige acceso condicional. Esto es algo que hay que tener en cuenta principalmente para el acceso a Exchange y SharePoint, ya que las versiones de aplicaciones anteriores se han creado con protocolos más antiguos.

## SharePoint
Los protocolos heredados se pueden deshabilitar en SharePoint, mediante el cmdlet Set-SPOTenant. Este cmdlet evitará que los clientes de Office que usan protocolos de autenticación no moderna accedan a recursos de SharePoint Online.

**Comando de ejemplo**: `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`
 
## Exchange

En Exchange, hay dos categorías principales de revisión de protocolo y selección de la directiva adecuada para su organización:

1. Exchange ActiveSync. De forma predeterminada, la directiva de acceso condicional para MFA y la ubicación no se exigen para Exchange ActiveSync. Esto permite la protección del acceso bien mediante la configuración de la directiva de Exchange ActiveSync directamente o el bloqueo de Exchange ActiveSync por medio de reglas de AD FS.
2. Protocolos heredados. En AD FS se pueden bloquear los protocolos heredados. Con ello, se bloquea el acceso para clientes de Office anteriores, como Office 2013 sin autenticación moderna habilitada y posteriores.


### Ejemplo de reglas de AD FS
Las siguientes reglas se pueden usar para bloquear el acceso de los protocolos heredados en AD FS, en dos configuraciones comunes. Opción 1: Permitir que Exchange ActiveSync y permitir solo las aplicaciones heredadas en la intranet.

Al aplicar las tres reglas siguientes a la relación de confianza para usuario autenticado de AD FS para la Plataforma de identidad de Microsoft Office 365, se permitirá el tráfico de Exchange ActiveSync, junto con el tráfico del explorador y la autenticación moderna. Las aplicaciones heredadas se bloquearán desde la extranet.

Regla 1

    `@RuleName = “Allow all intranet traffic”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");`

Regla 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

Regla 3

	@RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### Opción 2: Permitir Exchange ActiveSync y bloquear las aplicaciones heredadas 
Al aplicar las tres reglas siguientes a la relación de confianza para usuario autenticado de AD FS para la Plataforma de identidad de Microsoft Office 365, se permitirá el tráfico de Exchange ActiveSync, junto con el tráfico del explorador y la autenticación moderna. Las aplicaciones heredadas se bloquean desde cualquier ubicación.

Regla 1

    @RuleName = “Allow all intranet traffic only for browser and modern authentication clients”
	c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regla 2

    @RuleName = “Allow EAS”
	c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


Regla 3

    @RuleName = “Allow Extranet browser or browser dialog traffic”
	c1:[Type == " http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] && 
	c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"] 
	=> issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

<!---HONumber=AcomDC_0629_2016-->