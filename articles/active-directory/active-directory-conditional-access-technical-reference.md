---
title: "Referencia técnica del acceso condicional de Azure Active Directory | Microsoft Docs"
description: "Con el control de acceso condicional, Azure Active Directory comprueba las condiciones específicas que se eligen al autenticar al usuario y antes de permitirle acceso a la aplicación. Si se cumplen las condiciones, el usuario queda autenticado y se le permite el acceso a la aplicación."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: femila
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/01/2017
ms.author: markvi
ms.reviewer: spunukol
ms.translationtype: HT
ms.sourcegitcommit: a16daa1f320516a771f32cf30fca6f823076aa96
ms.openlocfilehash: f3d8bdbfc29ca1008006837512c0e6ae8cb8f6fe
ms.contentlocale: es-es
ms.lasthandoff: 09/02/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referencia técnica del acceso condicional de Azure Active Directory

Con el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md), puede ajustar el modo en que los usuarios autorizados pueden tener acceso a sus recursos.  
Este tema proporciona información de soporte técnico para los siguientes elementos de una directiva de acceso condicional: 

- Asignaciones de aplicaciones de nube

- Condición de plataforma de dispositivos 

- Condición de aplicaciones cliente

- Requisito de aplicación cliente aprobado 



## <a name="cloud-apps-assignments"></a>Asignaciones de aplicaciones de nube

Cuando se configura una directiva de acceso condicional, se necesita [seleccionar las aplicaciones de nube a las que se aplica la directiva](active-directory-conditional-access-azure-portal.md#who). 

![Control](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-apps"></a>Aplicaciones de nube de Microsoft

Puede asignar una directiva de acceso condicional a las siguientes aplicaciones de nube de Microsoft:

- Azure RemoteApp

- Dynamics CRM

- Yammer de Microsoft Office 365

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (incluye OneDrive para la Empresa)

- Microsoft Power BI 

- Visual Studio Team Services

- Equipos de Microsoft


### <a name="other-apps"></a>Otras aplicaciones 

Además de las aplicaciones de nube de Microsoft, puede asignar una directiva de acceso condicional a los siguientes tipos de aplicaciones de nube:

- Azure Active Directory (Azure AD): aplicaciones conectadas

- Aplicaciones de software como servicio (SaaS) federadas y preintegradas

- Aplicaciones que utilizan contraseña de inicio de sesión único (SSO)

- Aplicaciones de línea de negocio

- Aplicaciones que usan el proxy de aplicación de Azure AD. 


## <a name="device-platforms-condition"></a>Condición de plataformas de dispositivo

En una directiva de acceso condicional, puede configurar la condición de la plataforma de dispositivos para asociar la directiva al sistema operativo que se está ejecutando en un cliente.

![Control](./media/active-directory-conditional-access-technical-reference/41.png)

El acceso condicional de Azure AD admite las siguientes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

- Windows

- macOS (versión preliminar)



## <a name="client-apps-condition"></a>Condición de aplicaciones cliente 

Cuando se configura una directiva de acceso condicional, puede establecer una [condición de aplicaciones de cliente](active-directory-conditional-access-azure-portal.md#client-apps). La condición de aplicaciones de cliente le permite conceder o bloquear el acceso cuando se realiza un intento de acceso desde estos tipos de aplicaciones de cliente:

- Browser
- Aplicaciones de escritorio y aplicaciones móviles

![Control](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Exploradores compatibles 

Si selecciona *Exploradores* en la directiva de acceso condicional para conceder acceso a los recursos, el acceso se concede únicamente cuando el intento de acceso se realiza usando un explorador compatible. Cuando se realiza un intento de acceso mediante un explorador no compatible, se bloquea el intento.

![Exploradores compatibles](./media/active-directory-conditional-access-technical-reference/05.png)

En la directiva de acceso condicional, se admiten los siguientes exploradores: 


| SO                     | Exploradores                 | Soporte técnico     |
| :--                    | :--                      | :-:         |
| Windows 10                 | IE, Edge                 | ![Comprobar][1] |
| Windows 10                 | Chrome                   | Vista previa     |
| Windows 8/8.1            | IE, Chrome               | ![Comprobar][1] |
| Windows 7                  | IE, Chrome               | ![Comprobar][1] |
| iOS                    | Safari                   | ![Comprobar][1] |
| Android                | Chrome                   | ![Comprobar][1] |
| Windows Phone          | IE, Edge                 | ![Comprobar][1] |
| Windows Server 2016    | IE, Edge                 | ![Comprobar][1] |
| Windows Server 2016    | Chrome                   | Próximamente |
| Windows Server 2012 R2 | IE, Chrome               | ![Comprobar][1] |
| Windows Server 2008 R2 | IE, Chrome               | ![Comprobar][1] |
| Mac OS                 | Safari                   | ![Comprobar][1] |
| Mac OS                 | Chrome                   | Próximamente |

> [!NOTE]
> Para obtener soporte para Chrome, debe usar Windows 10 Creators Update e instalar la extensión que encontrará [aquí](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).


### <a name="supported-mobile-apps-and-desktop-clients"></a>Clientes de escritorio y aplicaciones móviles compatibles

Si selecciona **Aplicaciones móviles y aplicaciones de escritorio** en la directiva de acceso condicional para conceder acceso a los recursos, este se concede solo cuando el intento de acceso se realiza usando los clientes de escritorio y las aplicaciones móviles compatibles. Cuando se realiza un intento de acceso mediante una aplicación móvil o un cliente de escritorio no compatible, se bloquea el intento.

![Control](./media/active-directory-conditional-access-technical-reference/06.png)

Los siguientes clientes de escritorio y aplicaciones móviles admiten el acceso condicional para Office 365 y otras aplicaciones de servicio conectadas a Azure AD:


| Aplicaciones cliente| Servicio de destino| Plataforma |
| --- | --- | --- |
| Directiva de MFA y de ubicación para las aplicaciones. No se admiten las directivas basadas en dispositivos.| Cualquier servicio de aplicaciones de Mis aplicaciones| Android e iOS|
| Azure RemoteApp| Servicio Azure Remote App| Windows 10, Windows 8.1, Windows 7, iOS, Android y Mac OS X|
| Aplicación de Dynamics CRM| Dynamics CRM| Windows 10, Windows 8.1, Windows 7, iOS y Android|
| Microsoft Teams Services: controla todos los servicios que admiten Microsoft Teams y todas sus aplicaciones cliente: escritorio de Windows, iOS, Android, WP y cliente web| Equipos de Microsoft| Windows 10, Windows 8.1, Windows 7, iOS y Android|
| Aplicación Correo/Calendario/Contactos, Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7, Windows 7|
| Aplicación móvil de Outlook| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| Mac OS X|
| Aplicaciones de Office 2016, aplicaciones universales de Office, Office 2013 (con autenticación moderna), cliente de sincronización de OneDrive (ver [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), la compatibilidad con Grupos de Office está prevista para el futuro, la compatibilidad con la aplicación SharePoint está prevista para el futuro| Office 365 SharePoint Online| Windows 10|
| Aplicaciones de Office 2016, Office 2013 (con autenticación moderna), cliente de sincronización de OneDrive (ver [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))| Office 365 SharePoint Online| Windows 8.1, Windows 7, Windows 7|
| Aplicaciones móviles de Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 para macOS (solo Word, Excel, PowerPoint y OneNote). Soporte técnico de OneDrive para la Empresa previsto para el futuro| Office 365 SharePoint Online| Mac OS X|
| Aplicación de Yammer para Office| Yammer para Office 365| Windows 10, iOS y Android|
| Aplicación de PowerBI. La aplicación de Power BI para Android no admite actualmente el acceso condicional basado en dispositivos.| Servicio de PowerBI| Windows 10, Windows 8.1, Windows 7 e iOS|
| Aplicación de Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS y Android|



## <a name="approved-client-app-requirement"></a>Requisito de aplicación cliente aprobado 

Cuando configura una directiva de acceso condicional, puede seleccionar el requisito para conceder acceso solo si una aplicación cliente aprobada realizó un intento de conexión. 

![Control](./media/active-directory-conditional-access-technical-reference/21.png)

Las aplicaciones cliente aprobadas para esta configuración son:

- Microsoft Excel

- Microsoft OneDrive

- Microsoft Outlook

- Microsoft OneNote

- Microsoft PowerPoint

- Microsoft SharePoint

- Microsoft Skype Empresarial

- Equipos de Microsoft

- Microsoft Visio

- Microsoft Word


**Comentarios:**

- Estas aplicaciones admiten la administración de aplicaciones móviles (MAM) de Microsoft Intune.

- Este requisito:

    - Solo admite IOS y Android como [condición de plataformas de dispositivo](#device-platforms-condition) seleccionada 

    - No admite **Explorador** como [condición de aplicación cliente](#supported-browsers) seleccionada 
    
    - Sustituye la [condición de aplicación cliente](#supported-mobile-apps-and-desktop-clients) denominada **Aplicaciones móviles y aplicaciones de escritorio** si está seleccionada  


## <a name="next-steps"></a>Pasos siguientes

- Para ver una introducción al acceso condicional, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md)
- Si está listo para configurar directivas de acceso condicional para su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md)



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



