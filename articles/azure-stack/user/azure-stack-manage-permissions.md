---
title: "Administración de permisos para recursos por usuario en Azure Stack | Microsoft Docs"
description: Si es administrador de servicios o inquilino, aprenda a administrar los permisos RBAC.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: dfec5648ff383fd98965c1918cdab6472bb3c17f
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control"></a>Administrar el control de acceso basado en roles

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En Azure Stack, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio. Por ejemplo, el Usuario A puede tener permisos de lector en la Suscripción 1, pero tener permisos de propietario en la Máquina virtual 7.

* Lector: el usuario puede ver todo el contenido, pero no puede realizar cambios.
* Colaborador: el usuario pueden administrar todo el contenido, excepto el acceso a los recursos.
* Propietario: el usuario puede administrar todo el contenido, incluido el acceso a los recursos.

## <a name="set-access-permissions-for-a-user"></a>Establecimiento de los permisos de acceso de un usuario
1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En la hoja para el recurso, haga clic en el icono **Acceso**![](media/azure-stack-manage-permissions/image1.png).
3. En la hoja **Usuarios**, haga clic en **Roles**.
4. En la hoja **Roles**, haga clic en **Agregar** para agregar permisos para el usuario.

## <a name="next-steps"></a>pasos siguientes


