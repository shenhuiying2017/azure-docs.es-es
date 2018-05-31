---
title: Recuperación de los resultados de la revisión de acceso de Azure AD | Microsoft Docs
description: Cómo recuperar los resultados de las revisiones de acceso de Azure Active Directory.
services: active-directory
documentationcenter: ''
author: markwahl-msft
manager: mtillman
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/16/2018
ms.author: billmath
ms.openlocfilehash: cdd07fd837863d9a5abced0db8cacaded6288a41
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34192231"
---
# <a name="retrieve-access-review-results"></a>Recuperación de los resultados de la revisión de acceso

Los administradores pueden usar Azure Active Directory (Azure AD) para [crear una revisión de acceso](active-directory-azure-ad-controls-create-access-review.md) para miembros de grupo o usuarios asignados en una aplicación.  Un usuario que se encuentra en el rol **Administrador global**, **Administrador de seguridad** o **Lector de seguridad** también puede leer los resultados de una revisión de acceso.  Para asignar usuarios a uno de estos roles, un administrador de roles con privilegios puede usar Azure Active Directory Privileged Identity Management para hacer que un usuario pueda activar el rol o un administrador global puede [asignar un usuario al rol](active-directory-users-assign-role-azure-portal.md) permanentemente.

## <a name="locating-an-access-review"></a>Búsqueda de una revisión de acceso

Si sabe qué programa contiene la revisión de acceso, vaya a la página de revisiones de acceso, seleccione **Programas** y seleccione el programa que contenga el control de revisión de acceso.  A continuación, seleccione **Controles** y seleccione el control de revisión de acceso. Si hay muchos controles en el programa, puede filtrar para los controles de un tipo específico y ordenar por estado. También puede buscar por el nombre del control de revisión de acceso o el nombre para mostrar del propietario que lo creó. 

Si no sabe qué programa contiene la revisión de acceso, vaya a la página de revisiones de acceso y seleccione **Controles**.  Puede filtrar solo los controles de un tipo específico y ordenar por su estado o también puede buscar por el nombre del control de revisión de acceso o por el nombre para mostrar del propietario que lo creó. 

## <a name="retrieving-the-results-for-a-one-time-access-review"></a>Recuperación de los resultados de una revisión de acceso de una única vez

Si el tipo de periodicidad de la revisión es una única vez, se pueden obtener el progreso de una revisión de acceso activa y los resultados de una revisión de acceso completa en la sección **Resultados**.  Puede escribir el nombre para mostrar o el nombre principal de usuario de un usuario cuyo acceso se está revisando para ver solo el acceso de ese usuario.  Para recuperar todos los resultados de una revisión de acceso completa, haga clic en el botón **Descargar**.

## <a name="retrieving-the-results-for-multiple-instances-of-a-recurring-access-review"></a>Recuperación de los resultados de varias instancias de una revisión de acceso periódica

Para ver el progreso de una revisión de acceso activa que es periódica, haga clic en la sección **Resultados**.  Puede escribir el nombre para mostrar o el nombre principal de usuario de un usuario cuyo acceso se está revisando.

Para ver los resultados de una instancia finalizada de una revisión de acceso que sea recurrente, seleccione **Revisar el historial** y, a continuación, seleccione la instancia específica de la lista de instancias de la revisión de acceso finalizada, en función de las fechas inicial y final de la instancia.   Se pueden obtener los resultados de esta instancia en la sección **Resultados**.  Puede escribir el nombre para mostrar o el nombre principal de usuario de un usuario cuyo acceso se está revisando para ver solo el acceso de ese usuario.  Para recuperar todos los resultados de una instancia finalizada de una revisión de acceso periódica, haga clic en el botón **Descargar**.


## <a name="removing-users-from-an-access-review"></a>Eliminación de usuarios de una revisión de acceso

[!INCLUDE [Privacy](../../includes/gdpr-intro-sentence.md)]

De forma predeterminada, un usuario eliminado permanecerá eliminado en Azure AD durante 30 días, tiempo durante el cual un administrador puede restaurarlo si es necesario.  Después de 30 días, ese usuario se eliminará definitivamente.  Además, mediante el portal de Azure Active Directory, un administrador global puede [eliminar permanentemente un usuario eliminado recientemente](active-directory-users-restore.md) explícitamente antes de que se alcance ese período de tiempo.  Una vez que un usuario se ha eliminado permanentemente, los datos posteriores sobre ese usuario se eliminarán de las revisiones de acceso activas.  La información de auditoría de los usuarios eliminados se conserva en el registro de auditoría.

## <a name="next-steps"></a>Pasos siguientes

- [Administración del acceso de los usuarios con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-user-access-with-access-reviews.md)
- [Administración del acceso de los invitados con las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md)
- [Administración de los programas y los controles para las revisiones de acceso de Azure AD](active-directory-azure-ad-controls-manage-programs-controls.md)
- [Crear una revisión de acceso para los miembros de un grupo o el acceso a una aplicación](active-directory-azure-ad-controls-create-access-review.md)
- [Creación de una revisión de acceso de los usuarios en un rol administrativo de Azure AD](active-directory-privileged-identity-management-how-to-start-security-review.md)


