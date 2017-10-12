---
title: Administrar permisos para los recursos por usuario en Azure Stack (administrador de servicios e inquilino) | Microsoft Docs
description: Si es administrador de servicios o inquilino, aprenda a administrar los permisos RBAC.
services: azure-stack
documentationcenter: 
author: Heathl17
manager: byronr
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: e558f9de9bc3182bbe20ceb9d8f3f96e47fa542c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-role-based-access-control"></a>Administrar el control de acceso basado en roles

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

En Azure Stack, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio. Por ejemplo, el Usuario A puede tener permisos de lector en la Suscripción 1, pero tener permisos de propietario en la Máquina virtual 7.

* Lector: el usuario puede ver todo el contenido, pero no puede realizar cambios.
* Colaborador: el usuario pueden administrar todo el contenido, excepto el acceso a los recursos.
* Propietario: el usuario puede administrar todo el contenido, incluido el acceso a los recursos.

## <a name="set-access-permissions-for-a-user"></a>Establecimiento de los permisos de acceso de un usuario
1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En la hoja para el recurso, haga clic en el icono **Acceso**![](media/azure-stack-manage-permissions/image1.png).
3. En la hoja **Usuarios**, haga clic en **Roles**.
4. En la hoja **Roles**, haga clic en **Agregar** para agregar permisos para el usuario.

## <a name="next-steps"></a>Pasos siguientes
[Add a new Azure Stack tenant account in Azure Active Directory](azure-stack-add-new-user-aad.md)

