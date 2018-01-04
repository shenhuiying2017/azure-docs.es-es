---
title: "Asignación de un acceso de MSI a un recurso de Azure mediante PowerShell"
description: Instrucciones paso a paso para asignar una identidad de servicio administrada (MSI) en un recurso y acceso a otro recurso, mediante PowerShell.
services: active-directory
documentationcenter: 
author: BryanLa
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 8b3b000b4dc708e50c7e96fe04646837c96a555a
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2017
---
# <a name="assign-a-managed-service-identity-msi-access-to-a-resource-using-powershell"></a>Asignación de un acceso de Managed Service Identity (MSI) a un recurso mediante PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Una vez haya configurado un recurso de Azure con una identidad de servicio administrada, puede dar el acceso de MSI a otro recurso, al igual que cualquier entidad de seguridad. En este ejemplo se muestra cómo otorgar acceso de MSI a una máquina virtual de Azure para una cuenta de almacenamiento de Azure, mediante PowerShell.

## <a name="prerequisites"></a>requisitos previos

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

A continuación, instale [Azure PowerShell versión 4.3.1](https://www.powershellgallery.com/packages/AzureRM/4.3.1), si todavía no lo ha hecho.

## <a name="use-rbac-to-assign-the-msi-access-to-another-resource"></a>Uso de RBAC para asignar el acceso de MSI a otro recurso

Después de habilitar MSI en un recurso de Azure, [como una máquina virtual de Azure](msi-qs-configure-powershell-windows-vm.md):

1. Inicie sesión en Azure mediante el cmdlet `Login-AzureRmAccount`. Use una cuenta asociada a la suscripción de Azure en la que haya configurado la identidad de servicio administrada:

   ```powershell
   Login-AzureRmAccount
   ```
2. En este ejemplo, vamos a dar a una máquina virtual de Azure acceso a una cuenta de almacenamiento. En primer lugar, se usa [Get- AzureRMVM](/powershell/module/azurerm.compute/get-azurermvm) para obtener la entidad de servicio para la máquina virtual denominada "myVM", que se creó cuando se habilitó MSI. A continuación, usamos [New-AzureRmRoleAssignment](/powershell/module/AzureRM.Resources/New-AzureRmRoleAssignment) para conceder a la máquina virtual acceso de "Lectura" a una cuenta de almacenamiento denominada "myStorageAcct":

    ```powershell
    $spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
    New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
    ```

## <a name="troubleshooting"></a>solución de problemas

Si la identidad de servicio administrada para el recurso no aparece en la lista de identidades disponibles, compruebe que la identidad se haya habilitado correctamente. En nuestro caso, podemos volver a la máquina virtual de Azure en [Azure Portal](https://portal.azure.com) y:

- Buscar en la página "Configuración" y asegurarnos de que MSI habilitado = "Sí".
- Buscar en la página "Extensiones" y asegurarnos de que la extensión MSI se ha implementado correctamente.

Si alguna de las opciones no es correcta, puede que tenga que volver a implementar la identidad de servicio administrada en el recurso nuevo o solucionar el error de implementación.

## <a name="related-content"></a>Contenido relacionado

- Para obtener información general sobre MSI, consulte [Managed Service Identity overview](msi-overview.md) (Introducción a Managed Service Identity).
- Para habilitar MSI en una máquina virtual de Azure, consulte [Configuración de Managed Service Identity (MSI) de una máquina virtual de Azure con PowerShell](msi-qs-configure-powershell-windows-vm.md).

Use la siguiente sección de comentarios para proporcionar sus opiniones y ayudarnos a afinar y remodelar el contenido.

