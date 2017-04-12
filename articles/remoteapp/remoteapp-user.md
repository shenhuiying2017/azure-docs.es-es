---
title: "Adición de un usuario a la colección de Azure RemoteApp | Microsoft Docs"
description: "Obtenga información acerca de cómo agregar usuarios a la aplicación de Azure RemoteApp."
services: remoteapp
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
ms.assetid: 6b751fd2-2b11-499f-a2eb-12cb47f3129b
ms.service: remoteapp
ms.workload: compute
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2016
ms.author: mbaldwin
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: a84aa7e3ac7de717c2a628abcf6f5f11b05411e8
ms.lasthandoff: 03/31/2017


---
# <a name="how-to-add-a-user-to-your-azure-remoteapp-collection"></a>Agregar usuarios a la aplicación de Azure RemoteApp
> [!IMPORTANT]
> Azure RemoteApp dejará de estar disponible el 31 de agosto de 2017. Para obtener más información, lea el [anuncio](https://go.microsoft.com/fwlink/?linkid=821148) .
> 
> 

Para que los usuarios puedan ver y usar las aplicaciones en Azure RemoteApp, debe concederles acceso a la colección. Esta es la parte fácil: en la pestaña **Acceso de usuario** , escriba la información de cuenta del usuario y, a continuación, haga clic en la marca de verificación.

¿Qué información de cuenta necesita? Depende del tipo de colección que haya creado (en nube o híbrida) y si está usando Office 365 ProPlus en esa colección.

## <a name="supported-user-identities"></a>Identidades de usuario admitido
Los distintos tipos de colección (nube frente a híbrida) admiten el uso de distintas identidades de usuario para el acceso a las aplicaciones.  

Para una colección híbrida de RemoteApp, tendrá que configurar una infraestructura de dominio de Active Directory local y un inquilino de Azure Active Directory con integración de directorios (e inicio de sesión único opcional). Además, deberá crear algunos objetos de Active Directory en el directorio local.  

Para una colección de nube de RemoteApp, a cualquier usuario con Azure Active Directory que admita identidades se le puede conceder acceso de usuario a RemoteApp para incluir cuentas Microsoft.  Consulte la siguiente tabla.

Los usuarios de Office 365 son usuarios de Azure Active Directory. Si tienen cuentas de colección híbrida con sincronización de directorios de Azure Active Directory, se les puede conceder acceso de usuario en una implementación híbrida de RemoteApp.   

Puede usar esta tabla como referencia rápida para saber qué identidad se admite en la colección y cuáles son los requisitos de Active Directory.

| Cuentas de usuario | Nube | Híbrida |
| --- | --- | --- |
| Cuenta Microsoft |Sí |No |
| Azure Active Directory (Azure AD) | | |
| Solo nube de Azure AD |Sí |No |
| ADsync con sincronización de contraseñas |Sí |Sí |
| ADsync sin sincronización de contraseñas |Sí |No |
| ADsync con AD FS |Sí |yes |
| [Proveedores de identidades de terceros compatibles con Azure](https://msdn.microsoft.com/library/azure/jj679342.aspx) (por ejemplo, Ping) |Sí |Sí |
| Multi-Factor Authentication |Sí |Sí |

Consulte [más información](remoteapp-ad.md) sobre cómo configurar Active Directory para RemoteApp.

> [!NOTE]
> Los usuarios de Azure Active Directory deben proceder del inquilino asociado a la suscripción. (Puede ver y modificar su suscripción en la pestaña **Configuración** del portal. Consulte [Cambio del inquilino de Azure Active Directory que usa RemoteApp](remoteapp-changetenant.md) para obtener más información).
> 
> 

## <a name="office-365-proplus-user-account-information"></a>Información de la cuenta de usuario de Office 365 ProPlus
Si usa la imagen de plantilla de Office 365 ProPlus en su colección *o* si creó una imagen personalizada que usa Office 365, solo puede agregar usuarios de Azure Active Directory que tengan suscripciones de Office 365 para el dominio predeterminado de su suscripción. Consulte [Uso de Office 365 con RemoteApp de Azure](remoteapp-o365.md) para obtener más información.


