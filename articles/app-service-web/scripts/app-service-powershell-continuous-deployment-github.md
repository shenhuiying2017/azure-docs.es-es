---
title: "Ejemplo de script de Azure PowerShell: creación de una aplicación web con implementación continua desde GitHub | Microsoft Docs"
description: "Ejemplo de script de Azure PowerShell: creación de una aplicación web con implementación continua desde GitHub"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 42f901f8-02f7-4869-b22d-d99ef59f874c
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 9b6429027a0d9f95ddced8b6616693ac2959a014
ms.lasthandoff: 03/10/2017

---

# <a name="create-a-web-app-with-continuous-deployment-from-github"></a>Creación de una aplicación web con implementación continua desde GitHub

Este script de ejemplo crea una aplicación web en App Service con sus recursos relacionados y, después, configura la implementación continua desde un repositorio de GitHub. Para la implementación de GitHub sin una implementación continua, consulte [Creación de una aplicación web e implementación de código desde GitHub](app-service-powershell-deploy-github.md).

Antes de ejecutar este script, asegúrese de lo siguiente:

- Se ha creado una conexión con Azure mediante el cmdlet `Login-AzureRmAccount`.
- El código de la aplicación está en un repositorio de GitHub público o privado del que usted es el propietario.
- Ha [creado un token de acceso en su cuenta de GitHub](https://help.github.com/articles/creating-an-access-token-for-command-line-use/).

## <a name="sample-script"></a>Script de ejemplo

[!code-powershell[main](../../../powershell_scripts/app-service/deploy-github-continuous/deploy-github-continuous.ps1?highlight=1-2 "Creación de una aplicación web con implementación continua desde GitHub")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación 

Después de ejecutar el script de ejemplo, se puede usar el comando siguiente para quitar el grupo de recursos, la aplicación web y todos los recursos relacionados.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos. Cada comando de la tabla crea un vínculo a documentación específica del comando.

| Comando | Notas |
|---|---|
| [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/resourcemanager/AzureRM.Resources/v3.5.0/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [New-AzureRmAppServicePlan](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermappserviceplan) | Crea un plan de App Service, |
| [New-AzureRmWebApp](https://docs.microsoft.com/powershell/resourcemanager/azurerm.websites/v2.5.0/new-azurermwebapp) | Crea una aplicación web. |
| [Set-AzureRmResource](https://docs.microsoft.com/powershell/resourcemanager/azurerm.resources/v3.5.0/set-azurermresource) | Modifica un recurso en un grupo de recursos. |

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el módulo de Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/azureps-cmdlets-docs/).

Puede encontrar ejemplos de Azure PowerShell para Azure App Service Web Apps en los [ejemplos de PowerShell](../app-service-powershell-samples.md).

