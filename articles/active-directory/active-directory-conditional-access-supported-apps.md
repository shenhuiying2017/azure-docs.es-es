
---
title: 'Acceso condicional: aplicaciones que se admiten | Microsoft Docs'
description: Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación.
services: active-directory
documentationcenter: ''
author: markusvi
manager: femila
editor: ''

ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/26/2016
ms.author: markvi

---
# <a name="conditional-access-support-for-applications"></a>Compatibilidad con el acceso condicional de las aplicaciones
Las reglas de acceso condicional pueden utilizarse en las aplicaciones conectadas de Azure Active Directory, las aplicaciones SaaS federadas previamente integradas, las aplicaciones que utilizan inicio de sesión único con contraseña, y las aplicaciones de línea de negocio y el Proxy de la aplicación de Azure AD. Para ver una lista detallada de las aplicaciones en las que puede habilitar el acceso condicional, consulte [Servicios habilitados con acceso condicional](active-directory-conditional-access-technical-reference.md#Services-enabled-with-conditional-access). El acceso condicional funciona con aplicaciones móviles y de escritorio que usan autenticación moderna. En este tema se explican las compatibilidades con respecto a las versiones de escritorio y móvil de estas aplicaciones.

Las aplicaciones con autenticación moderna pueden mostrar páginas de inicio de sesión de Azure AD. Esto permite que se solicite a los usuarios en línea autenticación multifactor o que se muestre un mensaje accesible para el usuario final cuando se bloquea el acceso. La autenticación moderna también resulta necesaria para que el dispositivo pueda autenticarse con Azure AD y, así, se evalúen las directivas de acceso condicional basado en dispositivos.

Es importante comprender cuáles con las aplicaciones que se admiten, así como los pasos que pueden ser necesarios para proteger otros puntos de entrada.

## <a name="applications-using-modern-authentication"></a>Aplicaciones que usan autenticación moderna
Las siguientes aplicaciones admiten el acceso condicional a Office 365 y a otras aplicaciones de servicio conectadas a Azure AD:

| Servicio de destino | Plataforma | Application |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |Aplicación Correo/Calendario/Contactos, Outlook 2016, Outlook 2013 (con la autenticación moderna habilitada) y Skype Empresarial (con la autenticación moderna) |
| Office 365 Exchange Online |Windows 7 y Windows 8.1 |Outlook 2016, Outlook 2013 (con la autenticación moderna habilitada) y Skype Empresarial (con la autenticación moderna) |
| Office 365 Exchange Online |iOS, Android |Aplicación móvil de Outlook |
| Office 365 Exchange Online |Mac OS X |Outlook 2016 solo para MFA/ubicación, compatibilidad futura con directivas basadas en dispositivos y compatibilidad futura con Skype Empresarial |
| Office 365 SharePoint Online |Windows 10 |Aplicaciones de Office 2016, aplicaciones universales de Office, Office 2013 (con la autenticación moderna habilitada), compatibilidad futura con la aplicación de OneDrive para la Empresa (NGSC o cliente de sincronización de nueva generación), compatibilidad futura con grupos de Office y compatibilidad futura con la aplicación de SharePoint |
| Office 365 SharePoint Online |Windows 7 y Windows 8.1 |Aplicaciones de Office 2016, Office 2013 (con la autenticación moderna habilitada) y aplicación de OneDrive para la Empresa (cliente de sincronización de Groove) |
| Office 365 SharePoint Online |iOS, Android |Aplicaciones móviles de Office |
| Office 365 SharePoint Online |Mac OS X |Aplicaciones de Office 2016 solo para MFA/ubicación; compatibilidad futura con directivas basadas en dispositivos |
| Yammer para Office 365 |Windows 10, iOS y Android |Aplicación de Yammer para Office |
| Dynamics CRM |Windows 10, 7 y 8.1, e iOS y Android |Aplicación de Dynamics CRM |
| Servicio de PowerBI |Windows 10, 7 y 8.1, e iOS y Android |Aplicación de PowerBI |
| Servicio Azure Remote App |Windows 10, 7 y 8.1, iOS y Android, y Mac OS X |Azure RemoteApp |
| Cualquier servicio de aplicaciones de Mis aplicaciones |Android e iOS |Cualquier servicio de aplicaciones de Mis aplicaciones |

## <a name="applications-that-do-not-use-modern-authentication"></a>Aplicaciones que no usan autenticación moderna
Actualmente, es necesario utilizar otros métodos para bloquear el acceso a las aplicaciones que no usan autenticación moderna, dado que no se les exige acceso condicional. Esto es algo que hay que tener en cuenta principalmente para el acceso a Exchange y SharePoint, ya que las versiones de aplicaciones anteriores se han creado con protocolos más antiguos.

## <a name="office-365-sharepoint-online"></a>Office 365 SharePoint Online
Los protocolos heredados se pueden deshabilitar en SharePoint, mediante el cmdlet Set-SPOTenant. Este cmdlet evitará que los clientes de Office que usan protocolos de autenticación no moderna accedan a recursos de SharePoint Online. 

**Comando de ejemplo**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

## <a name="office-365-exchange-online"></a>Office 365 Exchange Online
En Exchange, hay dos categorías principales de revisión de protocolo y selección de la directiva adecuada para su organización:

1. Exchange ActiveSync. De forma predeterminada, la directiva de acceso condicional para MFA y la ubicación no se exigen para Exchange ActiveSync. Esto permite la protección del acceso bien mediante la configuración de la directiva de Exchange ActiveSync directamente o el bloqueo de Exchange ActiveSync por medio de reglas de AD FS.
2. Protocolos heredados. En AD FS se pueden bloquear los protocolos heredados. Con ello, se bloquea el acceso para clientes de Office anteriores, como Office 2013 sin autenticación moderna habilitada y posteriores.

### <a name="example-ad-fs-rules"></a>Ejemplo de reglas de AD FS
Las siguientes reglas se pueden usar para bloquear el acceso de los protocolos heredados en AD FS, en dos configuraciones comunes.

### <a name="option-1:-allow-exchange-activesync-and-only-allow-legacy-apps-on-the-intranet"></a>Opción 1: permitir Exchange ActiveSync y solo las aplicaciones heredadas en la intranet.
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

### <a name="option-2:-allow-exchange-activesync-and-block-legacy-apps"></a>Opción 2: Permitir Exchange ActiveSync y bloquear las aplicaciones heredadas
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











<!--HONumber=Oct16_HO2-->


