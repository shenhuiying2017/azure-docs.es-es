---
title: 'Privileged Identity Management para Azure Resources: activación de roles| Microsoft Docs'
description: Se describe cómo activar roles en PIM.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/02/2018
ms.author: billmath
ms.custom: pim
ms.openlocfilehash: 3e5456e7a632639cb82d7ba2b2e073938b1798ef
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="privileged-identity-management---resource-roles---activate"></a>Privileged Identity Management: activación de roles de recursos
La activación de roles para los recursos de Azure presenta una nueva experiencia que permite a los miembros del rol aptos programar la activación de fechas y horas futuras, y seleccionar una duración de activación específica dentro de los límites (que configuren los administradores). Aprenda a [activar roles de Azure AD aquí](../active-directory-privileged-identity-management-how-to-activate-role.md).

## <a name="activate-roles"></a>Activación de roles
Vaya a la sección Mis roles en la barra de navegación izquierda. Haga clic en "Activar" para el rol que se va a activar.
![](media/azure-pim-resource-rbac/rbac-roles.png)

En el menú Activaciones, escriba la fecha de inicio y la hora deseadas para activar el rol. Si lo desea, reduzca la duración de la activación (el período de tiempo que el rol permanece activo) y escriba una justificación (si procede); haga clic en Activar.

Si no se modifica la fecha de inicio y la hora, el rol se activa en unos segundos. Verá un rol en cola para el mensaje del banner de activación en la página Mis roles. Haga clic en el botón Actualizar para borrar este mensaje.

![](media/azure-pim-resource-rbac/rbac-activate-notification.png)

Si la activación está programada para una fecha y hora futuras, la solicitud pendiente aparecerá en la pestaña Solicitudes pendientes del menú de navegación izquierdo. En caso de que la activación del rol ya no sea necesaria, el usuario puede cancelar la solicitud; para ello, debe hacer clic en el botón Cancelar del lado derecho de la página.

![](media/azure-pim-resource-rbac/rbac-activate-pending.png)


## <a name="just-enough-administration"></a>Just Enough Administration

PIM para recursos de Azure facilita la aplicación de los procedimientos recomendados de JEA (Just Enough Administration) en las asignaciones de roles para recursos. Los usuarios y los miembros del grupo con asignaciones a suscripciones o grupos de recursos de Azure pueden activar su asignación de rol existente en un ámbito menor. 

En la página de búsqueda, encuentre el recurso subordinado que necesite administrar.

![](media/azure-pim-resource-rbac/azure-resources-02.png)

Seleccione Mis roles en el menú de navegación izquierdo y elija el rol que desee activar. Observe que el tipo de asignación es Heredado, puesto que el rol se asignó a la suscripción, en lugar de al grupo de recursos, tal y como se muestra a continuación.

![](media/azure-pim-resource-rbac/my-roles-02.png)
