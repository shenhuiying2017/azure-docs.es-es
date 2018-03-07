---
title: "Creación de grupos de administración para organizar los recursos de Azure | Microsoft Docs"
description: "Aprenda a crear grupos de administración de Azure para administrar varios recursos."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/22/2018
ms.author: rithorn
ms.openlocfilehash: 7c5aeca5afe8921ab39040e9afc2921b1711c447
ms.sourcegitcommit: 12fa5f8018d4f34077d5bab323ce7c919e51ce47
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/23/2018
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Creación de grupos de administración para la organización y la administración de recursos
Los grupos de administración son contenedores que ayudan a administran el acceso, las directivas y el cumplimiento de varias suscripciones. Cree estos contenedores para construir una jerarquía eficaz y eficiente que pueda usarse con [Azure Policy](../azure-policy/azure-policy-introduction.md) y los [controles de acceso basados en roles de Azure](../active-directory/role-based-access-control-what-is.md). Para más información sobre los grupos de administración, consulte [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md). 

La característica de grupo de administración está disponible en versión preliminar pública. Para empezar a usar grupos de administración, inicie sesión en [Azure Portal](https://portal.azure.com); o puede usar [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), la [CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) o la [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) para crear grupos de administración.   

El primer grupo de administración creado en el directorio puede tardar hasta 15 minutos en completarse. Hay procesos que se ejecutan la primera vez para configurar el servicio de grupos de administración dentro de Azure para su directorio. Recibirá una notificación cuando finalice el proceso.  

## <a name="how-to-create-a-management-group"></a>Creación de un grupo de administración
Puede crear el grupo de administración mediante el portal, PowerShell o la CLI de Azure.

### <a name="create-in-portal"></a>Crear en el portal

1. Inicie sesión en [Azure Portal](http://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**.
3. En la página principal, seleccione **Nuevo grupo de administración**. 

    ![Crear grupo](media/management-groups/create_main.png) 
4.  Rellene el campo de identificador de grupo de administración. 
    - El **identificador de grupo de administración** es el identificador único de directorio que se usa para enviar comandos en este grupo de administración. Este identificador no es editable una vez creado el grupo, dado que se usa en todo el sistema de Azure para identificar ese grupo. 
    - El campo de nombre para mostrar es el nombre que se muestra en Azure Portal. Un nombre para mostrar independiente es un campo opcional al crear el grupo de administración y se puede cambiar en cualquier momento.  

    ![Crear](media/management-groups/create_context_menu.png)  
5.  Seleccione **Guardar**.


### <a name="create-in-powershell"></a>Crear en PowerShell
En PowerShell, use los cmdlets Add-AzureRmManagementGroups.   

```azurepowershell-interactive
C:\> Add-AzureRmManagementGroup -GroupName Contoso 
```
**GroupName** es un identificador único que se crea. Este identificador lo usan otros comandos para hacer referencia a este grupo y no se puede cambiar más adelante.

Si quisiera que el grupo de administración mostrara un nombre diferente en Azure Portal, agregaría el parámetro **DisplayName** con la cadena. Por ejemplo, si quisiera crear un grupo de administración con el nombre de grupo de Contoso y el nombre para mostrar de "Grupo de Contoso", usaría el siguiente cmdlet: 

```azurepowershell-interactive
C:\> Add-AzureRmManagementGroup -GroupName Contoso -DisplayName "Contoso Group" -ParentId ContosoTenant
``` 
Use el parámetro **ParentId** para que este grupo de administración se cree en una administración diferente.  

### <a name="create-in-azure-cli"></a>Crear en la CLI de Azure
En al CLI de Azure, usará el comando az account management-group create. 

```azure-cli
C:\ az account management-group create --group-name <YourGroupName>
``` 

---

## <a name="next-steps"></a>pasos siguientes 
Para más información sobre los grupos de administración, consulte: 
- [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md)
- [Cómo cambiar, eliminar o administrar los grupos de administración](management-groups-manage.md)
- [Instalación del módulo de Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Revisión de las especificaciones de API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Instalación de la extensión de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)
