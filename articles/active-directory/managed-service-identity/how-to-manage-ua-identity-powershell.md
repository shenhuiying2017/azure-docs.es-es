---
title: Creación, enumeración y eliminación de una identidad asignada por el usuario (MSI) mediante Azure PowerShell
description: Instrucciones paso a paso sobre cómo crear, enumerar y eliminar una instancia de Managed Service Identity asignada por el usuario mediante Azure PowerShell.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: cc9b1b002c882a847d0ba2359caf4a193ea8d648
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2018
---
# <a name="create-list-or-delete-a-user-assigned-identity-using-azure-powershell"></a>Creación, enumeración o eliminación de una identidad asignada por el usuario mediante Azure PowerShell

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

La identidad de servicio administrada proporciona a los servicios de Azure una identidad administrada en Azure Active Directory. Puede usar esta identidad para autenticar a cualquier servicio que admita la autenticación de Azure AD, sin necesidad de tener credenciales en el código. 

En este artículo, aprenderá a crear, enumerar y eliminar una identidad asignada por el usuario mediante Azure PowerShell.

## <a name="prerequisites"></a>requisitos previos

- Si no está familiarizado con Managed Service Identity, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad asignada por el sistema y una asignada por el usuario](overview.md#how-does-it-work)**.
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.
- Instale [la versión más reciente de Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM) si aún no lo ha hecho.
- Si decide instalar y usar PowerShell de forma local, en este tutorial necesitará la versión 5.7.0 del módulo de Azure PowerShell o cualquier versión posterior. Ejecute ` Get-Module -ListAvailable AzureRM` para encontrar la versión. Si necesita actualizarla, consulte [Instalación del módulo de Azure PowerShell](/powershell/azure/install-azurerm-ps). Si PowerShell se ejecuta localmente, también debe ejecutar `Login-AzureRmAccount` para crear una conexión con Azure.

## <a name="create-a-user-assigned-identity"></a>Creación de una identidad asignada por el usuario

Para crear una identidad asignada por el usuario, use el comando [New-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/new-azurermuserassignedidentity). El parámetro `ResourceGroupName` especifica el grupo de recursos donde se creará la identidad asignada por el usuario, mientras que el parámetro `-Name` especifica su nombre. Reemplace los valores de los parámetros `<RESOURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` con sus propios valores:

> [!IMPORTANT]
> La creación de identidades asignadas por el usuario solo admite caracteres alfanuméricos y guiones (0-9, a-z, A-z, -). Además, el nombre debe limitarse a una longitud de 24 caracteres para que la asignación a VM/VMSS funcione correctamente. Compruebe si hay actualizaciones. Para más información, consulte [Preguntas más frecuentes y problemas conocidos](known-issues.md).

 ```azurepowershell-interactive
New-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-identities"></a>Enumeración de identidades asignadas por el usuario

Para enumerar las identidades asignadas por el usuario, use el comando [Get-AzureRmUserAssigned](/powershell/module/azurerm.managedserviceidentity/get-azurermuserassignedidentity).  El parámetro `-ResourceGroupName` especifica el grupo de recursos donde se creó la identidad asignada por el usuario.  Reemplace `<RESOURCE GROUP>` por su propio valor:

```azurepowershell-interactive
Get-AzureRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```
En la respuesta, a las identidades de usuario se devuelve el valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` para la clave `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-identity"></a>Eliminación de una identidad asignada por el usuario

Para eliminar una identidad asignada por el usuario, use el comando [Remove-AzureRmUserAssignedIdentity](/powershell/module/azurerm.managedserviceidentity/remove-azurermuserassignedidentity).  El parámetro `-ResourceGroupName` especifica el grupo de recursos donde se creó la identidad asignada por el usuario, mientras que el parámetro `-Name` especifica su nombre.  Reemplace los valores de los parámetros `<RESOURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` con sus propios valores:

 ```azurecli-interactive
Remove-AzurRmUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```
> [!NOTE]
> La eliminación de una identidad asignada por el usuario no quitará la referencia de ningún recurso al que se haya asignado. Las asignaciones de identidad deben quitarse por separado.

## <a name="related-content"></a>Contenido relacionado

Para obtener una lista completa y más detalles sobre los comandos de MSI de Azure PowerShell, consulte [AzureRM.ManagedServiceIdentity](/powershell/module/azurerm.managedserviceidentity#managed_service_identity).


 
