---
title: "Implementación de plantillas con el portal en Azure Stack | Microsoft Docs"
description: Aprenda a usar el portal de Azure Stack para implementar plantillas.
services: azure-stack
documentationcenter: 
author: HeathL17
manager: byronr
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: helaw
ms.openlocfilehash: 630d86ed7e3558ae2c8a62f4e1a94f10ca11812d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implementación de plantillas con el portal de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Use el portal para implementar plantillas de Azure Resource Manager en el kit de desarrollo de Azure Stack.

Las plantillas de Resource Manager implementan y aprovisionan todos los recursos para su aplicación en una única operación coordinada.

1. Inicie sesión en el portal, haga clic en **Nuevo**, haga clic en **Personalizado** y, después, haga clic en **Template Deployment**.
2. Haga clic en **Editar plantilla**, pegue el código de la plantilla JSON en la hoja y, después, haga clic en **Guardar**.
3. Haga clic en **Editar parámetros**, escriba los valores de los parámetros que aparecen y, después, haga clic en **Aceptar**.
4. Haga clic en **Suscripción**, elija la suscripción que quiere usar y, después, haga clic en **Aceptar**.
5. Haga clic en **Grupo de recursos**, elija un grupo de recursos existente o cree uno nuevo y, después, haga clic en **Aceptar**.
6. Haga clic en **Crear**. Un icono nuevo en el panel realiza el seguimiento del progreso de la implementación de la plantilla.

## <a name="next-steps"></a>Pasos siguientes
[Implementación de plantillas en Azure Stack con PowerShell](azure-stack-deploy-template-powershell.md)

