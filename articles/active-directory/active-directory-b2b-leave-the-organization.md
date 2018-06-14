---
title: 'Salir de una organización como usuario invitado: Azure Active Directory | Microsoft Docs'
description: Muestra cómo un usuario invitado de Azure AD B2B puede salir de una organización a través del Panel de acceso.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 05/11/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 942439da3e116415c77a28950df69d44744b2dd8
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2018
ms.locfileid: "34077710"
---
# <a name="leave-an-organization-as-a-guest-user"></a>Salir de una organización como usuario invitado

Es posible que un usuario invitado de Azure Active Directory (Azure AD) B2B decida salir de una organización en cualquier momento si ya no necesita usar las aplicaciones de dicha organización ni mantener ninguna asociación. Un usuario puede salir de una organización por su cuenta, sin ponerse en contacto con un administrador.

## <a name="leave-an-organization"></a>Salir de una organización

Para salir de una organización, tras haber iniciado sesión como usuario en el [Panel de acceso](https://myapps.microsoft.com), siga estos pasos:

1. Si aún no ha iniciado sesión en la organización que quiere abandonar, seleccione su nombre en la esquina superior derecha y haga clic en dicha organización.
2. En la esquina superior derecha, seleccione su nombre.
3. Junto a **Organizaciones**, seleccione el icono de configuración (engranaje).
 
   ![Captura de pantalla que muestra la configuración de usuario en el panel de acceso](media/active-directory-b2b-leave-the-organization/UserSettings.png) 

3. En **Organizaciones**, busque la organización que quiere abandonar y seleccione **Salir de la organización**.

   ![Captura de pantalla que muestra la opción Salir de la organización en la interfaz de usuario](media/active-directory-b2b-leave-the-organization/LeaveOrg.png)

4. Cuando se le pida que confirme, seleccione **Salir**. 

## <a name="account-removal"></a>Eliminar cuenta

Cuando un usuario sale de una organización, la cuenta de usuario se "elimina temporalmente" en el directorio. De forma predeterminada, el objeto de usuario se mueve al área **Usuarios eliminados** en Azure AD, pero no se elimina permanentemente durante 30 días. Esta eliminación temporal permite que el administrador restaure la cuenta de usuario (incluidos los grupos y permisos) si el usuario lo solicita en el plazo de 30 días.

Si lo desea, un administrador de inquilinos puede eliminar permanentemente la cuenta en cualquier momento durante el período de 30 días. Para ello, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com), seleccione **Azure Active Directory**.
2. En **Administrar**, seleccione **Usuarios**.
3. Seleccione **Usuarios eliminados**.
4. Active la casilla de verificación situada junto a un usuario eliminado y, luego, seleccione **Eliminar de forma permanente**.

Si elimina permanentemente un usuario, esta acción es irrevocable.

[!INCLUDE [GDPR-related guidance](../../includes/gdpr-dsr-and-stp-note.md)]

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre B2B de Azure AD, consulte [¿Qué es la colaboración B2B de Azure AD?](active-directory-b2b-what-is-azure-ad-b2b.md)



