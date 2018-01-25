---
title: "Configuración de SharePoint Online y Exchange Online para el acceso condicional de Azure Active Directory | Microsoft Docs"
description: Aprenda a configurar SharePoint Online y Exchange Online para el acceso condicional de Azure Active Directory.
services: active-directory
documentationcenter: 
author: MarkusVi
manager: mtillman
editor: 
ms.assetid: 62349fba-3cc0-4ab5-babe-372b3389eff6
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: markvi
ms.reviewer: calebb
ms.openlocfilehash: c527d4511ab2dd3bb349c10c1defb24723fef59a
ms.sourcegitcommit: 384d2ec82214e8af0fc4891f9f840fb7cf89ef59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2018
---
# <a name="set-up-sharepoint-online-and-exchange-online-for-azure-active-directory-conditional-access"></a>Configuración de SharePoint Online y Exchange Online para el acceso condicional de Azure Active Directory 

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede controlar el modo en que los usuarios acceden a las aplicaciones en la nube. Si desea usar acceso condicional para controlar el acceso a Exchange y SharePoint en línea, debe:

- Revisar si se admite el escenario de acceso condicional
- Evitar que las aplicaciones cliente omitan el cumplimiento de las directivas de acceso condicional.   

En este artículo se explica cómo puede abordar ambos casos.


## <a name="what-you-need-to-know"></a>Lo que necesita saber

Puede usar el acceso condicional de Azure AD para proteger las aplicaciones en la nube cuando un intento de autenticación procede de:

- Un explorador web

- Una aplicación de cliente que use [autenticación moderna](https://support.office.com/article/Using-Office-365-modern-authentication-with-Office-clients-776c0036-66fd-41cb-8928-5495c0f9168a)

- Exchange ActiveSync 

Algunas aplicaciones en la nube también admiten protocolos de autenticación heredados. Esto se aplica, por ejemplo, a SharePoint Online y Exchange Online. Cuando una aplicación cliente puede utilizar un protocolo de autenticación heredado para tener acceso a una aplicación en la nube, Azure AD no puede aplicar una directiva de acceso condicional en este intento de acceso. Para evitar que una aplicación cliente omita el cumplimiento de directivas, debe comprobar si es posible habilitar solo la autenticación moderna en las aplicaciones en la nube afectadas. 

Algunos ejemplos de aplicaciones cliente a las que el acceso condicional no se aplica son:

- Office 2010 y versiones anteriores

- Office 2013, si no está habilitada la autenticación moderna



 
## <a name="control-access-to-sharepoint-online"></a>Control de acceso a SharePoint Online

Además de la autenticación moderna, SharePoint Online también admite protocolos de autenticación heredados. Si se habilitan protocolos de autenticación heredados, no se aplican las directivas de acceso condicional para SharePoint para los clientes que no usen la autenticación moderna.

Puede deshabilitar los protocolos de autenticación heredados para el acceso de SharePoint mediante el cmdlet **[Set-SPOTenant](https://technet.microsoft.com/library/fp161390.aspx)**: 

    Set-SPOTenant -LegacyAuthProtocolsEnabled $false

## <a name="control-access-to-exchange-online"></a>Control del acceso a Exchange Online

Al configurar directivas de acceso condicional para Exchange Online, debe revisar la siguiente:

- Exchange ActiveSync

- Protocolos de autenticación heredados



### <a name="exchange-activesync"></a>Exchange ActiveSync

Aunque Exchange Active Sync es compatible con la autenticación moderna, hay algunas limitaciones relativas a la compatibilidad en escenarios de acceso condicional:

- Solo puede configurar la condición de plataformas de dispositivo  

    ![Plataformas de dispositivo](./media/active-directory-conditional-access-no-modern-authentication/05.png)

- No se permite establecer el requisito de la autenticación multifactor  

    ![Acceso condicional](./media/active-directory-conditional-access-no-modern-authentication/01.png)

Para proteger eficazmente el acceso a Exchange Online desde Exchange ActiveSync, puede:

- Configurar una directiva de acceso condicional admitida siguiendo estos pasos:

    a. Seleccione solamente **Office 365 Exchange Online** como aplicación en la nube.  

    ![Acceso condicional](./media/active-directory-conditional-access-no-modern-authentication/04.png)

    b. Seleccione **Exchange Active Sync** como **aplicación cliente** y, a continuación, seleccione **Aplicar directiva solo en las plataformas compatibles**.  

    ![Plataformas de dispositivo](./media/active-directory-conditional-access-no-modern-authentication/03.png)

- Bloquear Exchange ActiveSync con reglas de los Servicios de federación de Active Directory (AD FS).

        @RuleName = "Block Exchange ActiveSync"
        c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
        => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "false");




### <a name="legacy-authentication-protocols"></a>Protocolos de autenticación heredados

Además de la autenticación moderna, Exchange Online también admite protocolos de autenticación heredados. Si se habilitan protocolos de autenticación heredados, no se aplican las directivas de acceso condicional para Exchange Online para los clientes que no usen la autenticación moderna.

Puede deshabilitar los protocolos de autenticación heredados para Exchange Online estableciendo reglas de AD FS. Así se bloquea el acceso desde:

- Clientes anteriores de Office, como Office 2013, que no tienen habilitada la autenticación moderna 

- Versiones anteriores de Office


## <a name="set-up-ad-fs-rules"></a>Configuración de reglas de AD FS

Puede usar las siguientes reglas de autorización de emisión para habilitar o bloquear el tráfico en el nivel de AD FS. 

### <a name="block-legacy-traffic-from-the-extranet"></a>Bloqueo del tráfico heredado desde la extranet

Mediante la aplicación de las tres reglas siguientes: 

- Habilita el acceso para:
    - Tráfico de Exchange ActiveSync
    - Tráfico del explorador
    - Tráfico de autenticación moderna
- Bloquea el acceso para: 
    - Aplicaciones cliente heredadas de la extranet

**Regla 1:**

    @RuleName = "Allow all intranet traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "true"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regla 2:**

    @RuleName = "Allow Exchange ActiveSync"
    c1:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-client-application", Value == "Microsoft.Exchange.ActiveSync"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

**Regla 3:**

    @RuleName = "Allow extranet browser and browser dialog traffic"
    c1:[Type == "http://schemas.microsoft.com/ws/2012/01/insidecorporatenetwork", Value == "false"] &&
    c2:[Type == "http://schemas.microsoft.com/2012/01/requestcontext/claims/x-ms-endpoint-absolute-path", Value =~ "(/adfs/ls)|(/adfs/oauth2)"]
    => issue(Type = "http://schemas.microsoft.com/authorization/claims/permit", Value = "true");

### <a name="block-legacy-traffic-from-anywhere"></a>Bloqueo del tráfico heredado desde cualquier lugar

Mediante la aplicación de las tres reglas siguientes:

- Habilita el acceso para: 
    - Tráfico de Exchange ActiveSync
    - Tráfico del explorador
    - Tráfico de autenticación moderna
- Bloquea el acceso para: 
    - Las aplicaciones heredadas desde cualquier ubicación

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

## <a name="next-steps"></a>pasos siguientes

Para más información, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)




