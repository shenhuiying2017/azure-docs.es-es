---
title: "Cómo cambiar, eliminar o administrar los grupos de administración en Azure | Microsoft Docs"
description: "Aprenda a mantener y actualizar la jerarquía de grupos de administración."
author: rthorn17
manager: rithorn
editor: 
ms.assetid: 
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 3/1/2018
ms.author: rithorn
ms.openlocfilehash: 33797ddcd2a6ff083c5fb4b2fa7ddb8f9d6bd76c
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2018
---
# <a name="manage-your-resources-with-management-groups"></a>Administración de los recursos con grupos de administración 
Los grupos de administración son contenedores que ayudan a administran el acceso, las directivas y el cumplimiento de varias suscripciones. Puede cambiar, eliminar y administrar estos contenedores para que las jerarquías puedan usarse con [Azure Policy](../azure-policy/azure-policy-introduction.md) y los [controles de acceso basado en roles de Azure (RBAC)](../active-directory/role-based-access-control-what-is.md). Para más información sobre los grupos de administración, consulte [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md).

La característica de grupo de administración está disponible en versión preliminar pública. Para empezar a usar grupos de administración, inicie sesión en [Azure Portal](https://portal.azure.com); o puede usar [Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview), la [CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available) o la [API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview) para administrar grupos de administración.

Para realizar cambios en un grupo de administración, debe tener un rol de propietario o colaborador en el grupo de administración. Para ver qué permisos tiene, seleccione el grupo de administración y, a continuación, seleccione **IAM**. Para más información sobre los roles de RBAC, consulte [Administración del acceso y los permisos con RBAC](../active-directory/role-based-access-control-what-is.md).

## <a name="change-the-name-of-a-management-group"></a>Cambio del nombre de un grupo de administración 
Puede cambiar el nombre del grupo de administración mediante el portal, PowerShell o la CLI de Azure.

### <a name="change-the-name-in-the-portal"></a>Cambiar el nombre en el portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**.  
3. Seleccione el grupo de administración cuyo nombre quiere cambiar. 
4. Seleccione la opción **Cambiar nombre de grupo** en la parte superior de la página. 

    ![Cambiar nombre de grupo](media/management-groups/detail_action_small.png)
5. Cuando se abra el menú, escriba el nuevo nombre que desea mostrar.

    ![Cambiar nombre de grupo](media/management-groups/rename_context.png) 
4. Seleccione **Guardar**. 

### <a name="change-the-name-in-powershell"></a>Cambiar el nombre en PowerShell

Para actualizar el nombre para mostrar, use **Update-AzureRmManagementGroup**. Por ejemplo, para cambiar el nombre de un grupo de administración de "Contoso TI" a "Grupo de Contoso", ejecute el siguiente comando: 

```azurepowershell-inetractive
C:\> Update-AzureRmManagementGroup -GroupName ContosoIt -DisplayName "Contoso Group"  
``` 

### <a name="change-the-name-in-azure-cli"></a>Cambiar el nombre en la CLI de Azure

En la CLI de Azure, use el comando de actualización. 

```azure-cli
C:\> az account management-group update --group-name Contoso --display-name "Contoso Group" 
```

---
 
## <a name="delete-a-management-group"></a>Eliminación de un grupo de administración
Para eliminar un grupo de administración, deben cumplirse los siguientes requisitos:
1. No deben existir grupos de administración secundarios ni suscripciones en el grupo de administración. 
    - Para mover una suscripción fuera de un grupo de administración, consulte [Mover la suscripción a otro grupo de administración](#Move-subscriptions-in-the-hierarchy). 
    - Para mover un grupo de administración a otro grupo de administración, consulte [Mover grupos de administración en la jerarquía](#Move-management-groups-in-the-hierarchy). 
2. Tiene permisos de escritura sobre el rol de propietario o colaborador para grupos de administración en el grupo de administración. Para ver qué permisos tiene, seleccione el grupo de administración y, a continuación, seleccione **IAM**. Para más información sobre los roles de RBAC, consulte [Administración del acceso y los permisos con RBAC](../active-directory/role-based-access-control-what-is.md).  

### <a name="delete-in-the-portal"></a>Eliminar en el portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**.  
3. Seleccione el grupo de administración que desea eliminar. 
    
    ![Eliminar grupo](media/management-groups/delete.png)
4. Seleccione **Eliminar**. 
    - Si el icono está deshabilitado, al mantener el selector del mouse sobre el icono se muestra el motivo. 
5. Se abre una ventana para que confirme si quiere eliminar el grupo de administración. 

    ![Eliminar grupo](media/management-groups/delete_confirm.png) 
6. Seleccione **Sí**. 


### <a name="delete-in-powershell"></a>Eliminar en PowerShell

Use el comando **Remove-AzureRmManagementGroup** de PowerShell para eliminar grupos de administración. 

```azurepowershell-interactive
Remove-AzureRmManagementGroup -GroupName Contoso
```

### <a name="delete-in-azure-cli"></a>Eliminación en la CLI de Azure
Con la CLI de Azure, use el comando az account management-group delete. 

```azure-cli
C:\> az account management-group delete --group-name Contoso
```
---

## <a name="view-management-groups"></a>Visualización de los grupos de administración
Puede ver cualquier grupo de administración sobre el que tenga un rol de RBAC directo o heredado.  

### <a name="view-in-the-portal"></a>Ver en el portal
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**. 
3. Se carga la página de jerarquía de grupos de administración, donde se muestran todos los grupos a los que tiene acceso. 
    ![Primario](media/management-groups/main.png)
4. Seleccione un grupo de administración individual para ver los detalles.  

### <a name="view-in-powershell"></a>Ver en PowerShell
Use el comando Get-AzureRmManagementGroup para recuperar todos los grupos.  

```azurepowershell-interactive
Get-AzureRmManagementGroup
```
Para obtener información de un único grupo de administración, use el parámetro -GroupName.

```azurepowershell-interactive
Get-AzureRmManagementGroup -GroupName Contoso
```

### <a name="view-in-azure-cli"></a>Ver en la CLI de Azure
Use el comando list para recuperar todos los grupos.  

```azure-cli
az account management-group list
```
Para obtener información de un único grupo de administración, use el comando show.

```azurepowershell-interactive
az account management-group show --group-name Contoso
```
---

## <a name="move-subscriptions-in-the-hierarchy"></a>Movimiento de las suscripciones en la jerarquía
Uno de los motivos de crear un grupo de administración es agrupar las suscripciones. Solo los grupos de administración y las suscripciones pueden convertirse en secundarios de otro grupo de administración. Una suscripción que se mueve a un grupo de administración hereda todas las directivas y accesos de usuario del grupo de administración primario. 

Para mover la suscripción, hay dos permisos que debe tener: 
- Rol de "propietario" en la suscripción secundaria.
- Rol de "propietario" o "colaborador" en el nuevo grupo de administración primario. 
- Rol de "propietario" o "colaborador" en el grupo de administración primario anterior.
Para ver qué permisos tiene, seleccione el grupo de administración y, a continuación, seleccione **IAM**. Para más información sobre los roles de RBAC, consulte [Administración del acceso y los permisos con RBAC](../active-directory/role-based-access-control-what-is.md). 

### <a name="move-subscriptions-in-the-portal"></a>Mover las suscripciones en el portal

**Agregar una suscripción existente a un grupo de administración**
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**. 
3. Seleccione el grupo de administración que tiene previsto que sea el primario.      
5. En la parte superior de la página, seleccione **Agregar existente**. 
6. En el menú que se abre, seleccione el valor de **Tipo de recurso** del elemento que intenta mover, que es **Suscripción**.
7. Seleccione la suscripción de la lista con el identificador correcto. 

    ![Secundario](media/management-groups/add_context_2.png)
8. Seleccione "Guardar".

**Quitar una suscripción de un grupo de administración**
1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**. 
3. Seleccione el grupo de administración que tiene previsto que sea el primario actual.  
4. Seleccione los puntos suspensivos al final de la fila correspondiente a la suscripción de la lista que quiere mover.

    ![Move](media/management-groups/move_small.png)
5. Seleccione **Mover**.
6. En el menú que se abre, seleccione el **grupo de administración primario**.

    ![Move](media/management-groups/move_small_context.png)
7. Seleccione **Guardar**.

### <a name="move-subscriptions-in-powershell"></a>Mover las suscripciones en PowerShell
Para mover una suscripción en PowerShell, use el comando Add-AzureRmManagementGroupSubscription.  

```azurepowershell-interactive
New-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

Para quitar el vínculo entre la suscripción y el grupo de administración, use el comando Remove-AzureRmManagementGroupSubscription.

```azurepowershell-interactive
Remove-AzureRmManagementGroupSubscription -GroupName Contoso -SubscriptionId 12345678-1234-1234-1234-123456789012
```

### <a name="move-subscriptions-in-azure-cli"></a>Mover las suscripciones en la CLI de Azure
Para mover una suscripción en la CLI, utilice el comando add. 

```azure-cli
C:\> az account management-group add --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

Para quitar la suscripción del grupo de administración, use el comando subscription remove.  

```azure-cli
C:\> az account management-group remove --group-name Contoso --subscription 12345678-1234-1234-1234-123456789012
```

---

## <a name="move-management-groups-in-the-hierarchy"></a>Movimiento de grupos de administración en la jerarquía  
Al mover un grupo de administración primario, todos los recursos secundarios que incluyen grupos de administración, suscripciones, grupos de recursos y recursos se mueven con el elemento primario.   

### <a name="move-management-groups-in-the-portal"></a>Mover grupos de administración en el portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Seleccione **Todos los servicios** > **Grupos de administración**. 
3. Seleccione el grupo de administración que tiene previsto que sea el primario.      
5. En la parte superior de la página, seleccione **Agregar existente**.
6. En el menú que se abre, seleccione el valor de **Tipo de recurso** del elemento que intenta mover, que es **Grupo de administración**.
7. Seleccione el grupo de administración con el identificador y el nombre correctos.

    ![mover](media/management-groups/add_context.png)
8. Seleccione **Guardar**.

### <a name="move-management-groups-in-powershell"></a>Mover grupos de administración en PowerShell
Use el comando Update-AzureRmManagementGroup de PowerShell para mover un grupo de administración a un grupo diferente.  

```powershell
C:\> Update-AzureRmManagementGroup -GroupName Contoso  -ParentName ContosoIT
```  
### <a name="move-management-groups-in-azure-cli"></a>Mover grupos de administración en la CLI de Azure
Use el comando update para mover un grupo de administración con CLI de Azure. 

```azure-cli
C:/> az account management-group udpate --group-name Contoso --parent-id "Contoso Tenant" 
``` 

---

## <a name="next-steps"></a>Pasos siguientes 
Para más información sobre los grupos de administración, consulte: 
- [Organización de los recursos con grupos de administración de Azure](management-groups-overview.md)
- [Creación de grupos de administración para organizar los recursos de Azure](management-groups-create.md)
- [Instalación del módulo de Azure Powershell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups/0.0.1-preview)
- [Revisión de las especificaciones de API REST](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview/2018-01-01-preview)
- [Instalación de la extensión de la CLI de Azure](https://docs.microsoft.com/en-us/cli/azure/extension?view=azure-cli-latest#az_extension_list_available)