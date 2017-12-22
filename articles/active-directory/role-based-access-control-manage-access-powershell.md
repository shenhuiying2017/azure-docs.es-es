---
title: "Administración del control de acceso basado en roles (RBAC) con Azure PowerShell | Microsoft Docs"
description: "Cómo administrar RBAC con Azure PowerShell, incluida la enumeración y asignación de roles, y la eliminación de asignaciones de roles."
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
ms.assetid: 9e225dba-9044-4b13-b573-2f30d77925a9
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: 631274ec57586a777df8ee07a18b0ad72b905222
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-role-based-access-control-with-azure-powershell"></a>Administración del control de acceso basado en rol con Azure PowerShell
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API DE REST](role-based-access-control-manage-access-rest.md)

Puede usar el control de acceso basado en rol (RBAC) del Portal de Azure y la API de Administración de recursos de Azure para administrar el acceso a su suscripción en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

Para poder usar Windows PowerShell con el fin de administrar RBAC, necesita cumplir los siguientes requisitos previos:

* La versión 0.8.8 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](/powershell/azure/overview).
* Cmdlets de Azure Resource Manager. Instale los [cmdlets de Azure Resource Manager](/powershell/azure/overview) en PowerShell.

## <a name="list-roles"></a>Lista de roles
### <a name="list-all-available-roles"></a>Lista de todos los roles disponibles
Para enumerar los roles de RBAC disponibles para asignación y para inspeccionar las operaciones a las que conceden acceso, use `Get-AzureRmRoleDefinition`.

```
Get-AzureRmRoleDefinition | FT Name, Description
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### <a name="list-actions-of-a-role"></a>Lista de las acciones de un rol
Para enumerar las acciones de un rol específico, use `Get-AzureRmRoleDefinition <role name>`.

```
Get-AzureRmRoleDefinition Contributor | FL Actions, NotActions

(Get-AzureRmRoleDefinition "Virtual Machine Contributor").Actions
```

![RBAC PowerShell - Get-AzureRmRoleDefinition para un rol específico - captura de pantalla](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## <a name="see-who-has-access"></a>Consulta de quién ha accedido
Para mostrar las asignaciones de acceso RBAC, use `Get-AzureRmRoleAssignment`.

### <a name="list-role-assignments-at-a-specific-scope"></a>Lista de asignaciones de roles en un ámbito específico
Puede ver todas las asignaciones de acceso de una suscripción, un grupo de recursos o un recurso determinados. Por ejemplo, para ver todas las asignaciones activas de un grupo de recursos, use `Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>`.

```
Get-AzureRmRoleAssignment -ResourceGroupName Pharma-Sales-ProjectForcast | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment para un grupo de recursos - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### <a name="list-roles-assigned-to-a-user"></a>Lista de roles asignados a un usuario
Para enumerar todos los roles asignados a un usuario especificado y los roles que se asignan a los grupos a los que pertenece el usuario, use `Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups`.

```
Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com | FL DisplayName, RoleDefinitionName, Scope

Get-AzureRmRoleAssignment -SignInName sameert@aaddemo.com -ExpandPrincipalGroups | FL DisplayName, RoleDefinitionName, Scope
```

![RBAC PowerShell - Get-AzureRmRoleAssignment para un usuario - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### <a name="list-classic-service-administrator-and-coadmin-role-assignments"></a>Lista de asignaciones de roles de administrador y coadministrador de servicio básico
Para enumerar las asignaciones de acceso para el administrador y los coadministradores de suscripción clásica, use:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## <a name="grant-access"></a>Conceder acceso
### <a name="search-for-object-ids"></a>Búsqueda de identificadores de objetos
Para asignar un rol, debe identificar el objeto (usuario, grupo o aplicación) y el ámbito.

Si no conoce el id. de suscripción, lo encontrará en la hoja **Suscripciones** de Azure Portal. Para obtener información sobre cómo obtener el id. de suscripción, vea [Get-AzureSubscription](/powershell/module/azure/get-azuresubscription?view=azuresmps-3.7.0) en MSDN.

Para obtener el id. del objeto de un grupo de Azure AD, use:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Para obtener el id. de objeto de una entidad principal de servicio de Azure AD o aplicación, use lo siguiente:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Asignación de un rol a una aplicación en el ámbito de la suscripción
Para conceder acceso a una aplicación en el ámbito de la suscripción, use:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Asignación de un rol a un usuario en el ámbito del grupo de recursos
Para conceder acceso a un usuario en el ámbito del grupo de recursos, use:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Asignación de un rol a un grupo en el ámbito del recurso
Para conceder acceso a un grupo en el ámbito del recurso, use:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## <a name="remove-access"></a>Quitar acceso
Para quitar el acceso de usuarios, grupos y aplicaciones, use:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## <a name="create-a-custom-role"></a>Crear un rol personalizado
Para crear un rol personalizado, use el comando ```New-AzureRmRoleDefinition``` . Existen dos métodos para estructurar el rol: por medio de PSRoleDefinitionObject o una plantilla JSON. 

## <a name="get-actions-for-a-resource-provider"></a>Acciones Get para un proveedor de recursos
A la hora de crear roles personalizados desde el principio, es importante conocer todas las operaciones posibles de los proveedores de recursos.
Use el comando ```Get-AzureRMProviderOperation``` para obtener esta información.
Por ejemplo, si quiere comprobar todas las operaciones disponibles para la máquina virtual, use este comando:

```
Get-AzureRMProviderOperation "Microsoft.Compute/virtualMachines/*" | FT OperationName, Operation , Description -AutoSize
```

### <a name="create-role-with-psroledefinitionobject"></a>Creación de rol con PSRoleDefinitionObject
Al usar PowerShell para crear un rol personalizado, puede empezar desde cero o usar uno de los [roles integrados](role-based-access-built-in-roles.md) como punto de partida. En el ejemplo de esta sección se empieza con un rol integrado y, luego, se personaliza con más privilegios. Edite los atributos para agregar cualquier elemento *Actions*, *notActions* o *scopes* que quiera y guarde los cambios como un nuevo rol.

El ejemplo siguiente se inicia con el rol *Colaborador de la máquina virtual* y lo usa para crear un rol personalizado denominado *Operador de máquina virtual*. El nuevo rol concede acceso a todas las operaciones de lectura de los proveedores de recursos *Microsoft.Compute*, *Microsoft.Storage* y *Microsoft.Network*, y concede acceso para iniciar, reiniciar y supervisar las máquinas virtuales. El rol personalizado se puede usar en dos suscripciones.

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Contributor"
$role.Id = $null
$role.Name = "Virtual Machine Operator"
$role.Description = "Can monitor and restart virtual machines."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/*/read")
$role.Actions.Add("Microsoft.Network/*/read")
$role.Actions.Add("Microsoft.Compute/*/read")
$role.Actions.Add("Microsoft.Compute/virtualMachines/start/action")
$role.Actions.Add("Microsoft.Compute/virtualMachines/restart/action")
$role.Actions.Add("Microsoft.Authorization/*/read")
$role.Actions.Add("Microsoft.Resources/subscriptions/resourceGroups/read")
$role.Actions.Add("Microsoft.Insights/alertRules/*")
$role.Actions.Add("Microsoft.Support/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e")
$role.AssignableScopes.Add("/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624")
New-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

### <a name="create-role-with-json-template"></a>Creación de rol con plantilla JSON
Se puede usar una plantilla JSON como definición de origen para el rol personalizado. En el ejemplo siguiente se crea un rol personalizado que permite el acceso de lectura a recursos de almacenamiento y proceso y el acceso a la asistencia técnica, y además agrega ese rol a dos suscripciones. Cree un archivo `C:\CustomRoles\customrole1.json` con el siguiente ejemplo. El identificador se debe establecer en `null` durante la creación de rol inicial ya que se generará un nuevo identificador automáticamente. 

```
{
  "Name": "Custom Role 1",
  "Id": null,
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```
Para agregar el rol a las suscripciones, ejecute el siguiente comando de PowerShell:
```
New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="modify-a-custom-role"></a>Modificación de un rol personalizado
De modo similar a la creación de un rol personalizado, puede modificar un rol personalizado existente por medio de PSRoleDefinitionObject o una plantilla JSON.

### <a name="modify-role-with-psroledefinitionobject"></a>Modificación de rol con PSRoleDefinitionObject
Para modificar un rol personalizado, use en primer lugar el comando `Get-AzureRmRoleDefinition` para recuperar la definición de rol. Después, haga los cambios que desee en la definición de rol. Por último, use el comando `Set-AzureRmRoleDefinition` para guardar la definición de rol modificada.

En el ejemplo siguiente se agrega la operación `Microsoft.Insights/diagnosticSettings/*` al rol personalizado *Operador de máquina virtual* .

```
$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.Actions.Add("Microsoft.Insights/diagnosticSettings/*")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

En el ejemplo siguiente se agrega una suscripción de Azure a los ámbitos asignables del rol personalizado *Operador de máquina virtual* .

```
Get-AzureRmSubscription - SubscriptionName Production3

$role = Get-AzureRmRoleDefinition "Virtual Machine Operator"
$role.AssignableScopes.Add("/subscriptions/34370e90-ac4a-4bf9-821f-85eeedead1a2")
Set-AzureRmRoleDefinition -Role $role
```

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

### <a name="modify-role-with-json-template"></a>Modificación de rol con plantilla JSON
Con la anterior plantilla JSON, puede modificar fácilmente un rol personalizado existente para agregar o quitar acciones. Actualice la plantilla JSON y agregue la acción de lectura para las redes tal y como se muestra en el siguiente ejemplo. Las definiciones que aparecen en la plantilla no se aplican de manera acumulativa a una definición existente, lo que significa que el rol aparecerá exactamente como lo especifique en la plantilla. También que necesita actualizar el identificador con el identificador del rol. Si no está seguro de cuál es este valor, puede usar el cmdlet `Get-AzureRmRoleDefinition` para obtener esta información.

```
{
  "Name": "Custom Role 1",
  "Id": "acce7ded-2559-449d-bcd5-e9604e50bad1",
  "IsCustom": true,
  "Description": "Allows for read access to Azure storage and compute resources and access to support",
  "Actions": [
    "Microsoft.Compute/*/read",
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Support/*"
  ],
  "NotActions": [
  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"
  ]
}
```

Para actualizar el rol existente, ejecute el siguiente comando de PowerShell:
```
Set-AzureRmRoleDefinition -InputFile "C:\CustomRoles\customrole1.json"
```

## <a name="delete-a-custom-role"></a>Eliminación de un rol personalizado
Para eliminar un rol personalizado, use el comando `Remove-AzureRmRoleDefinition` .

En el ejemplo siguiente se quita el rol personalizado *Operador de máquina virtual* .

```
Get-AzureRmRoleDefinition "Virtual Machine Operator"

Get-AzureRmRoleDefinition "Virtual Machine Operator" | Remove-AzureRmRoleDefinition
```

![RBAC PowerShell - Remove-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## <a name="list-custom-roles"></a>Lista de roles personalizados
Para enumerar los roles que están disponibles para la asignación en un ámbito, use el comando `Get-AzureRmRoleDefinition` .

En el ejemplo siguiente se enumeran todos los roles disponibles para la asignación en la suscripción seleccionada.

```
Get-AzureRmRoleDefinition | FT Name, IsCustom
```

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

En el ejemplo siguiente, el rol personalizado *Operador de máquina virtual* no está disponible en la suscripción *Production4*, ya que la suscripción no se encuentra entre los elementos **AssignableScopes** del rol.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## <a name="see-also"></a>Consulte también
* [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md)
  [!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

