---
title: "Referencia de configuración del acceso condicional de Azure Active Directory | Microsoft Docs"
description: "Obtenga una visión general de la configuración admitida en una directiva de acceso condicional de Azure Active Directory."
services: active-directory.
documentationcenter: 
author: MarkusVi
manager: mtillman
ms.assetid: 56a5bade-7dcc-4dcf-8092-a7d4bf5df3c1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/12/2017
ms.author: markvi
ms.reviewer: spunukol
ms.openlocfilehash: 1ce1fc4c03130dfea4e79c89c25cf5a9004e4dc8
ms.sourcegitcommit: 4256ebfe683b08fedd1a63937328931a5d35b157
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/23/2017
---
# <a name="azure-active-directory-conditional-access-settings-reference"></a>Referencia de configuración del acceso condicional de Azure Active Directory

Puede usar el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) para controlar el modo en que los usuarios autorizados pueden acceder a sus recursos.   

En este artículo se proporciona información de soporte técnico para las siguientes opciones de configuración de una directiva de acceso condicional: 

- Asignaciones de aplicaciones de nube

- Condición de plataforma de dispositivos 

- Condición de aplicaciones cliente

- Requisito de aplicación cliente aprobado


Si no es la información que está buscando, deje un comentario al final de este artículo.

## <a name="cloud-apps-assignments"></a>Asignaciones de aplicaciones de nube

Con las directivas de acceso condicional, puede controlar cómo los usuarios acceden a las [aplicaciones en la nube](active-directory-conditional-access-azure-portal.md#who). Al configurar una directiva de acceso condicional, debe seleccionar al menos una aplicación en la nube. 

![Seleccione las aplicaciones de nube de la directiva](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplicaciones de nube de Microsoft

Puede asignar una directiva de acceso condicional a las siguientes aplicaciones de nube de Microsoft:

- Azure Information Protection: [Más información](https://docs.microsoft.com/information-protection/get-started/faqs#i-see-azure-information-protection-is-listed-as-an-available-cloud-app-for-conditional-accesshow-does-this-work)

- Azure RemoteApp

- Microsoft Dynamics 365

- Yammer de Microsoft Office 365

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (incluye OneDrive para la Empresa y Project Online)

- Microsoft Power BI 

- Microsoft Visual Studio Team Services

- Equipos de Microsoft


### <a name="other-applications"></a>Otras aplicaciones 

Además de las aplicaciones de nube de Microsoft, puede asignar una directiva de acceso condicional a los siguientes tipos de aplicaciones de nube:

- Aplicaciones conectadas a Azure AD

- Aplicaciones de software como servicio (SaaS) federadas y preintegradas

- Aplicaciones que utilizan contraseña de inicio de sesión único (SSO)

- Aplicaciones de línea de negocio

- Aplicaciones que usan el proxy de aplicación de Azure AD


## <a name="device-platform-condition"></a>Condición de plataforma de dispositivos

En una directiva de acceso condicional, puede configurar la condición de la plataforma de dispositivos para asociar la directiva al sistema operativo que se está ejecutando en un cliente. El acceso condicional de Azure AD admite las siguientes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

- Windows

- macOS


![Asociar la directiva de acceso al sistema operativo del cliente](./media/active-directory-conditional-access-technical-reference/41.png)





## <a name="client-apps-condition"></a>Condición de aplicaciones cliente 

En la directiva de acceso condicional, puede configurar la condición de las [aplicaciones cliente](active-directory-conditional-access-azure-portal.md#client-apps) para asociar la directiva con la aplicación cliente que ha iniciado un intento de acceso. Si establece la condición de aplicaciones cliente, podrá conceder o bloquear el acceso cuando se realiza un intento de acceso desde estos tipos de aplicaciones cliente:

- Browser
- Aplicaciones de escritorio y aplicaciones móviles

![Control de acceso de las aplicaciones cliente](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Exploradores compatibles 

En la directiva de acceso condicional, puede seleccionar **Exploradores** como aplicación cliente.

![Controlar el acceso de los exploradores admitidos](./media/active-directory-conditional-access-technical-reference/05.png)

Esta configuración funciona con todos los exploradores. Sin embargo, para satisfacer una directiva de dispositivo, como un requisito de dispositivo compatible, se admiten los sistemas operativos y exploradores siguientes:


| SO                     | Exploradores                            | Soporte técnico     |
| :--                    | :--                                 | :-:         |
| Windows 10             | Internet Explorer, Edge, Chrome     | ![Comprobar][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome           | ![Comprobar][1] |
| Windows 7              | Internet Explorer, Chrome           | ![Comprobar][1] |
| iOS                    | Safari, Intune Managed Browser      | ![Comprobar][1] |
| Android                | Safari, Intune Managed Browser      | ![Comprobar][1] |
| Windows Phone          | Internet Explorer, Edge             | ![Comprobar][1] |
| Windows Server 2016    | Internet Explorer, Edge             | ![Comprobar][1] |
| Windows Server 2016    | Chrome                              | Próximamente |
| Windows Server 2012 R2 | Internet Explorer, Chrome           | ![Comprobar][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome           | ![Comprobar][1] |
| macOS                  | Chrome, Safari                      | ![Comprobar][1] |


> [!NOTE]
> Para obtener soporte de Chrome, debe usar la versión 1703 de Windows 10 Creators Update o una versión posterior.<br>
> Puede instalar [esta extensión](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

Estos exploradores admiten la autenticación de dispositivo, lo que permite identificar y validar el dispositivo con respecto a una directiva. Se produce un error en la comprobación del dispositivo si el explorador se ejecuta en modo privado. 


### <a name="supported-mobile-applications-and-desktop-clients"></a>Clientes de escritorio y aplicaciones móviles compatibles

En la directiva de acceso condicional, puede seleccionar **Aplicaciones móviles y aplicaciones de escritorio** como aplicación cliente.


![Controlar el acceso de aplicaciones móviles o clientes de escritorio compatibles](./media/active-directory-conditional-access-technical-reference/06.png)


Esta configuración afecta a los intentos de acceso realizados desde las siguientes aplicaciones móviles y aplicaciones de escritorio: 


|Aplicaciones cliente|Servicio de destino|Plataforma|
|---|---|---|
|Azure RemoteApp|Servicio Azure Remote App|Windows 10, Windows 8.1, Windows 7, iOS, Android y Mac OS X|
|Aplicación de Dynamics CRM|Dynamics CRM|Windows 10, Windows 8.1, Windows 7, iOS y Android|
|Aplicación de Correo electrónico/Calendario/People, Outlook 2016, Outlook 2013 (con la autenticación moderna)|Office 365 Exchange Online|Windows 10|
|Directiva de MFA y de ubicación para las aplicaciones. No se admiten las directivas basadas en dispositivos. |Cualquier servicio de aplicaciones de Mis aplicaciones|Android e iOS|
|Microsoft Teams Services: controla todos los servicios que admiten Microsoft Teams y todas sus aplicaciones cliente: escritorio de Windows, iOS, Android, WP y cliente web|Equipos de Microsoft|Windows 10, Windows 8.1, Windows 7, iOS, Android y macOS |
|Aplicaciones de Office 2016, Office 2013 (con autenticación moderna), cliente de sincronización de OneDrive (ver [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e))|Office 365 SharePoint Online|Windows 8.1, Windows 7, Windows 7|
|Aplicaciones de Office 2016, aplicaciones universales de Office, Office 2013 (con autenticación moderna), cliente de sincronización de OneDrive (ver [notas](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)), la compatibilidad con Grupos de Office está prevista para el futuro, la compatibilidad con la aplicación SharePoint está prevista para el futuro|Office 365 SharePoint Online|Windows 10|
|Office 2016 para macOS (solo Word, Excel, PowerPoint y OneNote). Soporte técnico de OneDrive para la Empresa previsto para el futuro|Office 365 SharePoint Online|Mac OS X|
|Aplicaciones móviles de Office|Office 365 SharePoint Online|Android, iOS|
|Aplicación de Yammer para Office|Yammer para Office 365|Windows 10, iOS y Android|
|Outlook 2016 (Office para macOS)|Office 365 Exchange Online|Mac OS X|
|Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna)|Office 365 Exchange Online|Windows 8.1, Windows 7, Windows 7|
|Aplicación móvil de Outlook|Office 365 Exchange Online|Android, iOS|
|Aplicación de PowerBI. La aplicación de Power BI para Android no admite actualmente el acceso condicional basado en dispositivos.|Servicio de PowerBI|Windows 10, Windows 8.1, Windows 7 e iOS|
|Skype Empresarial|Office 365 Exchange Online|Android, IOS |
|Aplicación de Visual Studio Team Services|Visual Studio Team Services|Windows 10, Windows 8.1, Windows 7, iOS y Android|



## <a name="approved-client-app-requirement"></a>Requisito de aplicación cliente aprobado 

En la directiva de acceso condicional, puede requerir que un intento de acceso a las aplicaciones en la nube seleccionadas se realice desde una aplicación cliente aprobada. 

![Control de acceso de las aplicaciones cliente aprobadas](./media/active-directory-conditional-access-technical-reference/21.png)

Esta configuración se aplica a las aplicaciones cliente siguientes:


- Microsoft Azure Information Protection
- Microsoft Excel
- Microsoft OneDrive
- Microsoft OneNote
- Microsoft Outlook
- Microsoft Planner
- Microsoft PowerPoint
- Microsoft SharePoint
- Microsoft Skype Empresarial
- Equipos de Microsoft
- Microsoft Visio
- Microsoft Word



**Comentarios:**

- Las aplicaciones cliente aprobadas admiten la característica de administración de aplicaciones móviles de Intune.

- Requisito de la opción **Solicitar aplicación cliente aprobada**:

    - Solo admite iOS y Android como [condición de plataformas de dispositivo](#device-platforms-condition).

    - No admite la opción **Explorador** como [condición de aplicaciones cliente](#supported-browsers).
    
    - Sustituye la opción **Aplicaciones móviles y clientes de escritorio** de la [condición de aplicaciones cliente](#supported-mobile-apps-and-desktop-clients) si está seleccionada.


## <a name="next-steps"></a>pasos siguientes

- Para ver una introducción al acceso condicional, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Si está listo para configurar las directivas de acceso condicional de su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png


