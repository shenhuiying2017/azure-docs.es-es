---
title: Aplicaciones y navegadores que usan reglas de acceso condicional en Azure Active Directory | Microsoft Docs
description: "Con el control de acceso condicional, Azure Active Directory comprueba la existencia de condiciones específicas cuando autentica al usuario y para permitir el acceso de la aplicación."
services: active-directory
documentationcenter: 
author: MarkusVi
manager: femila
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 03/28/2017
ms.author: markvi
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 7dc79fad1a87f015475cd2643c8bec55192b2a15
ms.lasthandoff: 03/28/2017


---
# <a name="applications-and-browsers-that-use-conditional-access-rules-in-azure-active-directory"></a>Aplicaciones y navegadores que usan reglas de acceso condicional en Azure Active Directory

Las reglas de acceso condicional con compatibles con aplicaciones conectadas a Azure Active Directory (Azure AD), aplicaciones de software como servicio (SaaS) federadas previamente integradas, aplicaciones que usan el inicio de sesión único (SSO) con contraseña, aplicaciones de línea de negocio y aplicaciones que usan el proxy de la aplicación de Azure AD. Para ver una lista detallada de las aplicaciones para las que puede usar el acceso condicional, consulte [Servicios habilitados con acceso condicional](active-directory-conditional-access-technical-reference.md). El acceso condicional funciona con aplicaciones móviles y de escritorio que usen la autenticación moderna. En este artículo, se explica cómo el acceso condicional funciona en aplicaciones móviles y de escritorio.

Puede usar páginas de inicio de sesión de Azure AD en aplicaciones que usen la autenticación moderna. Con una página de inicio de sesión, se solicita a un usuario que realice la autenticación multifactor. Aparece un mensaje si se bloquea el acceso del usuario. La autenticación moderna resulta necesaria para que el dispositivo se autentique con Azure AD y para que se evalúen las directivas de acceso condicional basado en dispositivos.

Es importante saber qué aplicaciones pueden usar las reglas de acceso condicional y los pasos que debería tomar para proteger otros puntos de entrada de la aplicación.

## <a name="applications-that-use-modern-authentication"></a>Aplicaciones que usan la autenticación moderna
> [!NOTE]
> Si tiene una directiva de acceso condicional en Azure AD con una equivalente en Office 365, configure ambas directivas. Por ejemplo, este sería el caso con las directivas de acceso condicional para Exchange Online o SharePoint Online.
>
>

Las siguientes aplicaciones admiten el acceso condicional para Office 365 y otras aplicaciones de servicio conectadas a Azure AD:

| Servicio de destino | Plataforma | Application |
| --- | --- | --- |
| Office 365 Exchange Online |Windows 10 |Aplicación Correo/Calendario/Contactos, Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna) |
| Office 365 Exchange Online |Windows 8.1, Windows 7, Windows 7 |Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna) |
| Office 365 Exchange Online |iOS, Android |Aplicación móvil de Outlook |
| Office 365 Exchange Online |Mac OS X |Outlook 2016 para la autenticación multifactor y la ubicación únicamente; compatibilidad con directivas basadas en dispositivos prevista para el futuro, compatibilidad con Skype Empresarial prevista para el futuro |
| Office 365 SharePoint Online |Windows 10 |Aplicaciones de Office 2016, aplicaciones universales de Office, Office 2013 (con autenticación moderna), cliente de sincronización de OneDrive (ver [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), compatibilidad con Grupos de Office prevista para el futuro, compatibilidad con la aplicación SharePoint prevista para el futuro |
| Office 365 SharePoint Online |Windows 8.1, Windows 7, Windows 7 |Aplicaciones de Office 2016, Office 2013 (con autenticación moderna), cliente de sincronización de OneDrive (ver [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)) |
| Office 365 SharePoint Online |iOS, Android |Aplicaciones móviles de Office |
| Office 365 SharePoint Online |Mac OS X |Aplicaciones de Office 2016 para la autenticación multifactor y la ubicación únicamente; compatibilidad con directivas basadas en dispositivos prevista para el futuro |
| Yammer para Office 365 |Windows 10, iOS; Android support planned for the future |Aplicación de Yammer para Office |
| Dynamics CRM |Windows 10, Windows 8.1, Windows 7, iOS y Android |Aplicación de Dynamics CRM |
| Servicio de PowerBI |Windows 10, Windows 8.1, Windows 7, iOS y Android |Aplicación de PowerBI |
| Servicio Azure Remote App |Windows 10, Windows 8.1, Windows 7, iOS, Android y Mac OS X |Azure RemoteApp |
| Cualquier servicio de aplicaciones de Mis aplicaciones |Android e iOS |Cualquier servicio de aplicaciones de Mis aplicaciones |

## <a name="applications-that-do-not-use-modern-authentication"></a>Aplicaciones que no usan autenticación moderna
Actualmente, debe usar otros métodos para bloquear el acceso a aplicaciones que no usan la autenticación moderna. El acceso condicional no aplica las reglas de acceso para las aplicaciones que no usan la autenticación moderna. Esta consideración se refiere principalmente al acceso a Exchange y SharePoint. La mayoría de las versiones anteriores de las aplicaciones usan protocolos de control de acceso antiguos.

### <a name="control-access-in-office-365-sharepoint-online"></a>Control de acceso en Office 365 SharePoint Online
Puede deshabilitar los protocolos heredados para el acceso de SharePoint mediante el cmdlet Set-SPOTenant. Use este cmdlet para evitar que los clientes de Office que usan protocolos de autenticación no moderna accedan a recursos de SharePoint Online.

**Comando de ejemplo**:    `Set-SPOTenant -LegacyAuthProtocolsEnabled $false`

### <a name="control-access-in-office-365-exchange-online"></a>Control de acceso en Office 365 Exchange Online
Exchange ofrece dos categorías principales de protocolos. Revise las opciones siguientes y seleccione la directiva que sea adecuada para su organización.

* **Exchange ActiveSync**. De forma predeterminada, no se aplican las directivas de acceso condicional para la autenticación multifactor y la ubicación para Exchange ActiveSync. Debe proteger el acceso a estos servicios mediante la configuración directa de la directiva de Exchange ActiveSync o mediante el bloqueo de Exchange ActiveSync con reglas de Servicios de federación de Active Directory (AD FS).
* **Protocolos heredados**. Puede bloquear los protocolos heredados con AD FS. Con ello, se bloquea el acceso a clientes de Office anteriores, como Office 2013 sin la autenticación moderna habilitada, y versiones anteriores de Office.

### <a name="use-ad-fs-to-block-legacy-protocol"></a>Uso de AD FS para bloquear el protocolo heredado
Puede usar las siguientes reglas de ejemplo para bloquear el acceso de protocolos heredados en el nivel de AD FS. Elija una de las dos configuraciones habituales.

#### <a name="option-1-allow-exchange-activesync-and-allow-legacy-apps-but-only-on-the-intranet"></a>Opción 1: Permitir Exchange ActiveSync y aplicaciones heredadas, pero solo en la intranet
Al aplicar las tres reglas siguientes a la relación de confianza para usuario autenticado de AD FS para la plataforma de identidad de Microsoft Office 365, el tráfico de Exchange ActiveSync, así como el del explorador y la autenticación moderna, tendrán acceso. Las aplicaciones heredadas se bloquean desde la extranet.

##### <a name="rule-1"></a>Regla 1
    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-2"></a>Regla 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

##### <a name="rule-3"></a>Regla 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

#### <a name="option-2-allow-exchange-activesync-and-block-legacy-apps"></a>Opción 2: Permitir Exchange ActiveSync y bloquear las aplicaciones heredadas
Al aplicar las tres reglas siguientes a la relación de confianza para usuario autenticado de AD FS para la plataforma de identidad de Microsoft Office 365, el tráfico de Exchange ActiveSync, así como el del explorador y la autenticación moderna, tendrán acceso. Las aplicaciones heredadas se bloquean desde cualquier ubicación.

##### <a name="rule-1"></a>Regla 1
    @RuleName = "Allow all intranet traffic only for browser and modern authentication clients"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-2"></a>Regla 2
    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


##### <a name="rule-3"></a>Regla 3
    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");


## <a name="supported-browsers"></a>Exploradores compatibles


| SO                     | Exploradores                 | Soporte técnico |
| :--                    | :--                      | :-:     |
| Windows 10                 | IE, Edge                 | ![Comprobar][1] |
| Windows 10                 | Chrome                   | Próximamente |
| Windows 8/8.1            | IE                       | ![Comprobar][1] |
| Windows 7                  | IE                       | ![Comprobar][1] |
| iOS                     | Safari                   | ![Comprobar][1] |
| Android                | Chrome                   | ![Comprobar][1] |
| Windows Phone               | IE, Edge                 | ![Comprobar][1] |
| Windows Server 2016    | IE, Edge                 | ![Comprobar][1] |
| Windows Server 2012 R2 | IE                       | ![Comprobar][1] |
| Windows Server 2008 R2     | IE                       | ![Comprobar][1] |
| Mac OS                 | Safari                   | ![Comprobar][1] |
| Mac OS                 | Chrome                   | Próximamente |


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-supported-apps/ic195031.png



