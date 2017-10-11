---
title: "Creación de un servidor de Azure Analysis Services mediante PowerShell | Microsoft Docs"
description: Aprenda a crear un servidor de Azure Analysis Services mediante PowerShell.
services: analysis-services
documentationcenter: 
author: minewiskan
manager: erikre
editor: 
ms.assetid: 
ms.service: analysis-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 08/01/2017
ms.author: owend
ms.custom: mvc
ms.openlocfilehash: cb42fd3ed51364cf478848cc51ebbb2f175e96d2
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="create-an-azure-analysis-services-server-by-using-powershell"></a>Creación de un servidor de Azure Analysis Services mediante PowerShell

Este inicio rápido describe el uso de PowerShell desde la línea de comandos para crear un servidor de Azure Analysis Services en un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) de su suscripción.

Esta tarea requiere la versión 4.0 del módulo de Azure PowerShell, o cualquier versión posterior. Para encontrar la versión, ejecute ` Get-Module -ListAvailable AzureRM`. Si necesita instalarla o actualizarla, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/install-azurerm-ps). 

> [!NOTE]
> La creación de un servidor puede dar lugar a un nuevo servicio facturable. Para obtener más información, consulte los [precios de Analysis Services](https://azure.microsoft.com/pricing/details/analysis-services/).

## <a name="prerequisites"></a>Requisitos previos
Para completar este inicio rápido necesita instalar:

* **Suscripción de Azure**: visite [Evaluación gratuita de Azure](https://azure.microsoft.com/offers/ms-azr-0044p/) para crear una cuenta.
* **Azure Active Directory**: la suscripción debe estar asociada a un inquilino de Azure Active Directory y debe tener una cuenta en ese directorio. Para más información, consulte [Permisos de usuario y autenticación](analysis-services-manage-users.md).

## <a name="import-azurermanalysisservices-module"></a>Importación del módulo AzureRm.AnalysisServices
Para crear un servidor en su suscripción, utilice el módulo del componente [AzureRM.AnalysisServices](https://www.powershellgallery.com/packages/AzureRM.AnalysisServices). Cargue el módulo AzureRm.AnalysisServices en la sesión de PowerShell.

```powershell
Import-Module AzureRM.AnalysisServices
```

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en su suscripción de Azure mediante el comando [Add-AzureRmAccount](/powershell/module/azurerm.profile/add-azurermaccount). Siga las instrucciones que aparecen en pantalla.

```powershell
Add-AzureRmAccount
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos
 
Un [grupo de recursos de Azure](../azure-resource-manager/resource-group-overview.md) es un contenedor lógico en el que se implementan y se administran los recursos de Azure como grupo. Cuando cree el servidor, deberá especificar un grupo de recursos de su suscripción. Si aún no dispone de un grupo de recursos, puede crearlo mediante el comando [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). En el siguiente ejemplo, se crea un grupo de recursos denominado `myResourceGroup` en la región del oeste de EE. UU.

```powershell
New-AzureRmResourceGroup -Name "myResourceGroup" -Location "West US"
```

## <a name="create-a-server"></a>Creación de un servidor

Cree un nuevo servidor mediante el comando [New-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). En el ejemplo siguiente se crea un servidor denominado myServer en myResourceGroup, en la región oeste de Estados Unidos, en el nivel de D1, y se especifica philipc@adventureworks.com como administrador del servidor.

```powershell
New-AzureRmAnalysisServicesServer -ResourceGroupName "myResourceGroup" -Name "myServer" -Location West US -Sku D1 -Administrator "philipc@adventure-works.com"
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Puede quitar el servidor de la suscripción mediante el comando [Remove-AzureRmAnalysisServicesServer](/powershell/module/azurerm.analysisservices/new-azurermanalysisservicesserver). Si va a seguir con otros inicios rápidos y tutoriales de esta colección, no quite el servidor. En el ejemplo siguiente se quita el servidor creado en el paso anterior.


```powershell
Remove-AzureRmAnalysisServicesServer -Name "myServer" -ResourceGroupName "myResourceGroup"
```

## <a name="next-steps"></a>Pasos siguientes
[Administración de Azure Analysis Services con PowerShell](analysis-services-powershell.md)   
[Implementación de un modelo desde SSDT](analysis-services-deploy.md)   
[Creación de un modelo en Azure Portal](analysis-services-create-model-portal.md)