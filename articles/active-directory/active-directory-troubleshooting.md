---
title: "Solución de problemas: Elemento \"Active Directory\" falta o no está disponible | Documentos de Microsoft"
description: "Qué hacer cuando el elemento de menú Active Directory no aparece en el Portal de administración de Azure."
services: active-directory
documentationcenter: na
author: bryanla
manager: mbaldwin
editor: 
ms.assetid: 3383020d-6397-43ea-b7aa-c6a9d6a1e3df
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2017
ms.author: bryanla
ms.openlocfilehash: be3a797c4a405fd2f6636e67f4c961dd6d143486
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="troubleshooting-active-directory-item-is-missing-or-not-available"></a>Solución de problemas: El elemento "Active Directory" falta o no está disponible
Muchas de las instrucciones para usar las características y los servicios de Azure Active Directory comienzan con "Vaya al Portal de administración de Azure y haga clic en **Active Directory**". Pero ¿qué hacer si el elemento de menú o la extensión Active Directory no aparecen o están marcados como **No disponible**? Este tema está diseñado para ayudarle en este caso. Describe las condiciones en que **Active Directory** no aparece o no está disponible y explica cómo proceder.

## <a name="active-directory-is-missing"></a>Falta Active Directory
Normalmente, aparece un elemento **Active Directory** en el menú de navegación izquierdo. En las instrucciones de procedimientos de Azure Active Directory, se da por supuesto que este elemento aparece en la vista.

![Captura de pantalla: Active Directory en Azure](./media/active-directory-troubleshooting/typical-view.png)

El elemento Active Directory se muestra en el menú de navegación izquierdo cuando se dé cualquiera de las siguientes condiciones. De lo contrario, el elemento no aparece.

* El usuario actual ha iniciado sesión con una cuenta Microsoft (antes conocida como Windows Live ID).
  
    OR
* El inquilino de Azure tiene un directorio y la cuenta actual es un administrador de directorio.
  
    OR
* El inquilino de Azure tiene al menos un espacio de nombres de Control de acceso de Azure AD (ACS). Para obtener más información, consulte [Espacio de nombres de Control de acceso](https://msdn.microsoft.com/library/azure/gg185908.aspx).
  
    OR
* El inquilino de Azure tiene al menos un proveedor de Multi-Factor Authentication. Para obtener más información, consulte [Administración de proveedores de Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication-get-started-cloud.md).

Para crear un espacio de nombres de Control de acceso o un proveedor de Multi-Factor Authentication, haga clic en **+Nuevo** > **App Services** > **Active Directory**.

Para obtener derechos administrativos a un directorio, pida a un administrador que asigne un rol de administrador a su cuenta. Para obtener detalles, consulte [Asignación de roles de administrador en Azure AD](active-directory-assign-admin-roles.md).

## <a name="active-directory-is-not-available"></a>Active Directory no está disponible
Cuando se hace clic en **+Nuevo** > **App Services**, aparece el elemento **Active Directory**. En concreto, el elemento Active Directory aparece cuando cualquiera de las características de Active Directory, como Directorio, Control de acceso o el proveedor de Multi-Factor Authentication, están disponibles para el usuario actual.

Sin embargo, mientras se carga la página, el elemento aparece atenuado y marcado como **No disponible**. Se trata de un estado temporal. Si espera unos segundos, el elemento estará disponible. Si el retraso se prolonga, el problema se suele resolver cuando se actualiza la página web.

![Captura de pantalla: Active Directory no está disponible](./media/active-directory-troubleshooting/not-available.png)

