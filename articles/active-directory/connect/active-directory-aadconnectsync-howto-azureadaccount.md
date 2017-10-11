---
title: "Sincronización de Azure AD Connect: administración de la cuenta de servicio de Azure AD | Microsoft Docs"
description: "En este tema se describe cómo restaurar la cuenta de servicio de Azure AD."
services: active-directory
keywords: "AADSTS70002, AADSTS50054, Procedimiento para restablecer la contraseña de la cuenta de servicio del conector de Azure AD Connect Sync"
documentationcenter: 
author: andkjell
manager: femila
editor: 
ms.assetid: 6077043a-27f1-4304-a44b-81dc46620f24
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2017
ms.author: billmath
ms.openlocfilehash: 8e9e8192ee4fcb636b5be91d2616acbc9120c8c0
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/03/2017
---
# <a name="azure-ad-connect-sync-how-to-manage-the-azure-ad-service-account"></a>Sincronización de Azure AD Connect: administración de la cuenta de servicio de Azure AD
Se supone que la cuenta de servicio utilizada por el conector de Azure AD está libre de servicio. Si necesita restablecer sus credenciales, este tema le interesa. Por ejemplo, si un administrador global ha restablecido la contraseña por error en la cuenta de servicio mediante PowerShell.

## <a name="reset-the-credentials"></a>Restablecimiento de las credenciales
Si la cuenta de servicio definida en el conector de Azure AD no puede ponerse en contacto con Azure AD debido a problemas de autenticación, se puede restablecer la contraseña.

1. Inicie sesión en el servidor de Azure AD Connect Sync e inicie PowerShell.
2. Ejecute `Add-ADSyncAADServiceAccount`.  
   ![Cmdlet addadsyncaadserviceaccount de PowerShell](./media/active-directory-aadconnectsync-howto-azureadaccount/addadsyncaadserviceaccount.png)
3. Proporcione las credenciales de administrador global de Azure AD.

Este cmdlet restablecerá la contraseña de la cuenta de servicio y la actualizará en Azure AD y en el motor de sincronización.

## <a name="known-issues-these-steps-can-solve"></a>Problemas conocidos que pueden solucionarse siguiendo estos pasos
Esta sección es una lista de los errores notificados por clientes que se solucionaron mediante un restablecimiento de las credenciales de la cuenta de servicio de Azure AD.

- - -
Evento 6900  
El servidor encontró un error inesperado al procesar una notificación de cambio de contraseña:  
AADSTS70002: error al validar las credenciales. AADSTS50054: se utilizó una contraseña antigua para realizar la autenticación.

- - -
Evento 659  
Error al recuperar la configuración de sincronización de directivas de contraseña. Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException:  
AADSTS70002: error al validar las credenciales. AADSTS50054: se utilizó una contraseña antigua para realizar la autenticación.

## <a name="next-steps"></a>Pasos siguientes
**Temas de introducción**

* [Sincronización de Azure AD Connect: comprender y personalizar la sincronización](active-directory-aadconnectsync-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](active-directory-aadconnect.md)

