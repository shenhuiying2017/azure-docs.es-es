---
title: "Autenticación basada en certificados de Azure Active Directory en iOS | Microsoft Docs"
description: "Obtenga información acerca de los escenarios admitidos y los requisitos para configurar la autenticación basada en certificados en las soluciones con dispositivos iOS"
services: active-directory
author: MarkusVi
documentationcenter: na
manager: femila
ms.assetid: 26a6fc54-0153-44fb-b970-9b432c99e9f9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/28/2017
ms.author: markvi
ms.reviewer: nigu
ms.openlocfilehash: af9d0c7ba9c1a3026cc042872e1ab773eb3c4c8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-certificate-based-authentication-on-ios"></a>Autenticación basada en certificados de Azure Active Directory en iOS

La autenticación basada en certificados (CBA) le permite autenticarse mediante Azure Active Directory con un certificado de cliente en un dispositivo Windows, Android o iOS al conectar su cuenta de Exchange Online a: 

* aplicaciones móviles de Office como Microsoft Outlook y Microsoft Word,   
* clientes de Exchange ActiveSync (EAS). 

Al configurar esta función, no tendrá que escribir una combinación de nombre de usuario y contraseña en determinadas aplicaciones de correo electrónico y Microsoft Office de su dispositivo móvil. 

En este tema se proporcionan los requisitos y los escenarios admitidos para configurar CBA en un dispositivo iOS (Android) para usuarios de inquilinos de planes de Office 365 Enterprise, Empresa, Educación, US Government, China y Alemania.

Esta característica se encuentra disponible como versión preliminar en los planes Office 365 US Government Defense y US Government Federal.




## <a name="office-mobile-applications-support"></a>Compatibilidad con aplicaciones móviles de Office

| Aplicaciones | Soporte técnico |
| --- | --- |
| Aplicación Azure Information Protection |![Comprobar][1] |
| Equipos de Microsoft |![Comprobar][1] |
| OneNote |![Comprobar][1] |
| OneDrive |![Comprobar][1] |
| Outlook |![Comprobar][1] |
| Power BI |![Comprobar][1] |
| Skype Empresarial |![Comprobar][1] |
| Word, Excel y PowerPoint |![Comprobar][1] |
| Yammer |![Comprobar][1] |


## <a name="requirements"></a>Requisitos 

La versión del sistema operativo del dispositivo debe ser iOS 9 y posterior. 

Se debe configurar un servidor de federación.  

Se requiere Microsoft Authenticator para las aplicaciones de Office en iOS.  

Para que Azure Active Directory revoque un certificado de cliente, el token de ADFS debe tener las siguientes notificaciones:  

* `http://schemas.microsoft.com/ws/2008/06/identity/claims/<serialnumber>`  
  (el número de serie del certificado de cliente) 
* `http://schemas.microsoft.com/2012/12/certificatecontext/field/<issuer>`  
  (la cadena del emisor del certificado de cliente) 

Azure Active Directory agrega estas notificaciones para el token de actualización, en caso de que estén disponibles en el token de ADFS (o en cualquier otro token SAML). Cuando hay que validar el token de actualización, esta información se utiliza para comprobar la revocación. 

Se recomienda actualizar las páginas de error de ADFS con lo siguiente:

* El requisito de instalar Microsoft Authenticator en iOS
* Instrucciones sobre cómo obtener un certificado de usuario 

Para más información, consulte [Personalizar las páginas de inicio de sesión de AD FS](https://technet.microsoft.com/library/dn280950.aspx).

Algunas aplicaciones de Office (con la autenticación moderna habilitada) envían ‘*prompt=login*’ a Azure AD en su solicitud. De manera predeterminada, Azure AD lo traduce en la solicitud para ADFS a '*wauth = usernamepassworduri*' (pide a ADFS que realice la autenticación de U y P) y '*wfresh = 0*' (pide a ADFS que ignore el estado de SSO y realice una autenticación nueva). Si desea habilitar la autenticación basada en certificados para estas aplicaciones, es preciso que modifique el comportamiento predeterminado de Azure AD. Basta con establecer '*PromptLoginBehavior*' en la configuración del dominio federado como '*Disabled*'. Para realizar esta tarea, puede usar el cmdlet [MSOLDomainFederationSettings](/powershell/module/msonline/set-msoldomainfederationsettings?view=azureadps-1.0):

`Set-MSOLDomainFederationSettings -domainname <domain> -PromptLoginBehavior Disabled`
  

## <a name="exchange-activesync-clients-support"></a>Compatibilidad con clientes de Exchange ActiveSync
En iOS 9 o posterior, se admite el cliente de correo de iOS nativo. Para todas las demás aplicaciones de Exchange ActiveSync, para determinar si se admite esta característica, póngase en contacto con el desarrollador de la aplicación.  


## <a name="next-steps"></a>Pasos siguientes

Si quiere configurar la autenticación basada en certificados en su entorno, consulte las instrucciones de [Introducción a la autenticación basada en certificados en Android](active-directory-certificate-based-authentication-get-started.md).


<!--Image references-->
[1]: ./media/active-directory-certificate-based-authentication-ios/ic195031.png
