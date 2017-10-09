---
title: "Referencia técnica del acceso condicional de Azure Active Directory | Microsoft Docs"
description: "Aprenda a usar los controles del acceso condicional en Azure Active Directory. Especifique las condiciones para autenticar a los usuarios y controlar el acceso a la aplicación. Cuando se cumplen las condiciones especificadas, los usuarios se autentican y se concede acceso a la aplicación."
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
ms.sourcegitcommit: 0e862492c9e17d0acb3c57a0d0abd1f77de08b6a
ms.openlocfilehash: 27ace4e9bc4a1626059fba657dce0c629d52f32d
ms.contentlocale: es-es
ms.lasthandoff: 09/27/2017

---
# <a name="azure-active-directory-conditional-access-technical-reference"></a>Referencia técnica del acceso condicional de Azure Active Directory

Puede usar el [acceso condicional de Azure Active Directory (Azure AD)](active-directory-conditional-access-azure-portal.md) para ajustar el modo en que los usuarios autorizados pueden tener acceso a sus recursos.  

Este tema proporciona información de soporte técnico para las siguientes opciones de configuración de una directiva de acceso condicional: 

- Asignaciones de aplicaciones de nube

- Condición de plataforma de dispositivos 

- Condición de aplicaciones cliente

- Requisito de aplicación cliente aprobado



## <a name="cloud-apps-assignments"></a>Asignaciones de aplicaciones de nube

Cuando se configura una directiva de acceso condicional, debe [seleccionar las aplicaciones de nube a las que se aplica la directiva](active-directory-conditional-access-azure-portal.md#who). 

![Seleccione las aplicaciones de nube de la directiva](./media/active-directory-conditional-access-technical-reference/09.png)


### <a name="microsoft-cloud-applications"></a>Aplicaciones de nube de Microsoft

Puede asignar una directiva de acceso condicional a las siguientes aplicaciones de nube de Microsoft:

- Azure RemoteApp

- Microsoft Dynamics 365

- Yammer de Microsoft Office 365

- Microsoft Office 365 Exchange Online

- Microsoft Office 365 SharePoint Online (incluye OneDrive para la Empresa)

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

En una directiva de acceso condicional, puede configurar la condición de la plataforma de dispositivos para asociar la directiva al sistema operativo que se está ejecutando en un cliente.

![Asociar la directiva de acceso al sistema operativo del cliente](./media/active-directory-conditional-access-technical-reference/41.png)

El acceso condicional de Azure AD admite las siguientes plataformas de dispositivo:

- Android

- iOS

- Windows Phone

- Windows

- macOS (versión preliminar)



## <a name="client-apps-condition"></a>Condición de aplicaciones cliente 

Cuando se configura una directiva de acceso condicional, puede [seleccionar las aplicaciones cliente ](active-directory-conditional-access-azure-portal.md#client-apps) de la condición de la aplicación cliente. Si establece la condición de aplicaciones cliente, podrá conceder o bloquear el acceso cuando se realiza un intento de acceso desde estos tipos de aplicaciones cliente:

- Browser
- Aplicaciones de escritorio y aplicaciones móviles

![Control de acceso de las aplicaciones cliente](./media/active-directory-conditional-access-technical-reference/03.png)

### <a name="supported-browsers"></a>Exploradores compatibles 

Controle el acceso del explorador mediante la opción **Explorador** que encontrará en la directiva de acceso condicional. Solo se concede el acceso cuando el intento de acceso se realiza mediante un explorador compatible. Cuando se realiza un intento de acceso mediante un explorador que no es compatible, se bloquea el intento.

![Controlar el acceso de los exploradores admitidos](./media/active-directory-conditional-access-technical-reference/05.png)

En la directiva de acceso condicional, se admiten los siguientes exploradores: 


| SO                     | Exploradores                    | Soporte técnico     |
| :--                    | :--                         | :-:         |
| Windows 10             | Internet Explorer, Edge     | ![Comprobar][1] |
| Windows 10             | Chrome                      | ![Comprobar][1] |
| Windows 8 / 8.1        | Internet Explorer, Chrome   | ![Comprobar][1] |
| Windows 7              | Internet Explorer, Chrome   | ![Comprobar][1] |
| iOS                    | Safari, Intune Managed Browser                      | ![Comprobar][1] |
| Android                | Safari, Intune Managed Browser                      | ![Comprobar][1] |
| Windows Phone          | Internet Explorer, Edge     | ![Comprobar][1] |
| Windows Server 2016    | Internet Explorer, Edge     | ![Comprobar][1] |
| Windows Server 2016    | Chrome                      | Próximamente |
| Windows Server 2012 R2 | Internet Explorer, Chrome   | ![Comprobar][1] |
| Windows Server 2008 R2 | Internet Explorer, Chrome   | ![Comprobar][1] |
| macOS                  | Safari                      | ![Comprobar][1] |
| macOS                  | Chrome                      | Próximamente |

> [!NOTE]
> Para obtener soporte de Chrome, debe usar la versión 1703 de Windows 10 Creators Update o una versión posterior.<br>
> Puede instalar [esta extensión](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji).

### <a name="supported-mobile-applications-and-desktop-clients"></a>Clientes de escritorio y aplicaciones móviles compatibles

Si selecciona la opción **clientes de escritorio y aplicaciones móviles** en la directiva de acceso condicional, podrá controlar el acceso de la aplicación y el cliente. El acceso se concede solo cuando el intento de acceso se realiza usando los clientes de escritorio y las aplicaciones móviles compatibles. Cuando se realiza un intento de acceso mediante una aplicación o un cliente no compatible, se bloquea el intento.

![Controlar el acceso de aplicaciones móviles o clientes de escritorio compatibles](./media/active-directory-conditional-access-technical-reference/06.png)

Los siguientes clientes de escritorio y aplicaciones móviles admiten el acceso condicional para Office 365 y otras aplicaciones de servicio conectadas a Azure AD:


| Aplicaciones cliente| Servicio de destino| Plataforma |
| --- | --- | --- |
| Directiva de Azure Multi-Factor Authentication y de ubicación de las aplicaciones (no se admiten las directivas basadas en dispositivos)| Cualquier servicio de aplicaciones de Mis aplicaciones| Android, iOS|
| Azure RemoteApp| Servicio Azure RemoteApp| Windows 10, Windows 8.1, Windows 7, iOS, Android, macOS|
| Aplicación de Dynamics 365| Dynamics 365| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Microsoft Office 365 Teams (controla todos los servicios que admiten Microsoft Teams y todas sus aplicaciones cliente: escritorio de Windows, iOS, Android, Windows Phone y cliente web)| Equipos de Microsoft| Windows 10, Windows 8.1, Windows 7, iOS, Android|
| Aplicación Correo/Calendario/Contactos, Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna)| Office 365 Exchange Online| Windows 10|
| Outlook 2016, Outlook 2013 (con autenticación moderna) y Skype Empresarial (con autenticación moderna)| Office 365 Exchange Online| Windows 8.1, Windows 7, Windows 7|
| Aplicación móvil de Outlook| Office 365 Exchange Online| iOS|
| Outlook 2016 (Office para macOS)| Office 365 Exchange Online| macOS|
| Aplicaciones de Office 2016, aplicaciones universales de Office, Office 2013 (con autenticación moderna), cliente de sincronización de [OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e), compatibilidad futura con Grupos de Office y la aplicación SharePoint| Office 365 SharePoint Online| Windows 10|
| Aplicaciones de Office 2016, Office 2013 (con autenticación moderna), cliente de sincronización de [OneDrive](https://support.office.com/en-US/article/Azure-Active-Directory-conditional-access-with-the-OneDrive-sync-client-on-Windows-028d73d7-4b86-4ee0-8fb7-9a209434b04e)| Office 365 SharePoint Online| Windows 8.1, Windows 7, Windows 7|
| Aplicaciones móviles de Office| Office 365 SharePoint Online| iOS, Android|
| Office 2016 para macOS (solo Word, Excel, PowerPoint y OneNote), futuro soporte técnico de OneDrive para la Empresa| Office 365 SharePoint Online| macOS|
| Aplicación de Yammer para Office| Yammer para Office 365| Windows 10, iOS y Android|
| Aplicación de Power BI (actualmente no es compatible con Android)| Servicio de PowerBI| Windows 10, Windows 8.1, Windows 7 e iOS|
| Aplicación de Visual Studio Team Services| Visual Studio Team Services| Windows 10, Windows 8.1, Windows 7, iOS, Android|



## <a name="approved-client-app-requirement"></a>Requisito de aplicación cliente aprobado 

Puede usar la opción **Solicitar aplicación cliente aprobada** en la directiva de acceso condicional para controlar las conexiones de cliente. Se concede acceso solo cuando una aplicación cliente aprobada realiza un intento de conexión.

![Control de acceso de las aplicaciones cliente aprobadas](./media/active-directory-conditional-access-technical-reference/21.png)

Puede usar las siguientes aplicaciones cliente con el requisito de la aplicación cliente aprobada:

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


**Comentarios**

- Las aplicaciones cliente aprobadas admiten la característica de administración de aplicaciones móviles de Intune.

- Requisito de la opción **Solicitar aplicación cliente aprobada**:

    - Solo admite iOS y Android como [condición de plataformas de dispositivo](#device-platforms-condition).

    - No admite la opción **Explorador** como [condición de aplicaciones cliente](#supported-browsers).
    
    - Sustituye la opción **Aplicaciones móviles y clientes de escritorio** de la [condición de aplicaciones cliente](#supported-mobile-apps-and-desktop-clients) si está seleccionada.


## <a name="next-steps"></a>Pasos siguientes

- Para ver una introducción al acceso condicional, consulte [Acceso condicional en Azure Active Directory](active-directory-conditional-access-azure-portal.md).
- Si está listo para configurar las directivas de acceso condicional de su entorno, consulte [Procedimientos recomendados para el acceso condicional en Azure Active Directory](active-directory-conditional-access-best-practices.md).



<!--Image references-->
[1]: ./media/active-directory-conditional-access-technical-reference/01.png



