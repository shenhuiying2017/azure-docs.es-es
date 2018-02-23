---
title: "Implementación de plantillas con el portal en Azure Stack | Microsoft Docs"
description: Aprenda a usar el portal de Azure Stack para implementar plantillas.
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: 
ms.openlocfilehash: 30c077747685ad7617148e9130926120420b098b
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="deploy-templates-using-the-azure-stack-portal"></a>Implementación de plantillas con el portal de Azure Stack

*Se aplica a: sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Use el portal para implementar plantillas de Azure Resource Manager en el Kit de desarrollo de Azure Stack.

Las plantillas de Resource Manager implementan y aprovisionan todos los recursos para su aplicación en una única operación coordinada.

1. Inicie sesión en el portal, haga clic en **Nuevo**, haga clic en **Personalizado** y, después, haga clic en **Template Deployment**.
2. Haga clic en **Editar plantilla**, pegue el código de la plantilla JSON en la hoja y, después, haga clic en **Guardar**.
3. Haga clic en **Editar parámetros**, escriba los valores de los parámetros que aparecen y, después, haga clic en **Aceptar**.
4. Haga clic en **Suscripción**, elija la suscripción que quiere usar y, después, haga clic en **Aceptar**.
5. Haga clic en **Grupo de recursos**, elija un grupo de recursos existente o cree uno nuevo y, después, haga clic en **Aceptar**.
6. Haga clic en **Create**(Crear). Un icono nuevo en el panel realiza el seguimiento del progreso de la implementación de la plantilla.

## <a name="next-steps"></a>pasos siguientes
[Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)

