---
title: "Administración del control de acceso basado en rol (RBAC) con la CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo administrar el control de acceso basado en rol (RBAC) con la interfaz de la línea de comandos de Azure a través de la enumeración de roles y acciones de roles y de la asignación de roles a los ámbitos de aplicación y suscripción."
services: active-directory
documentationcenter: 
author: rolyon
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.openlocfilehash: 5c099a7fd8848c2934603ec9b2db8947885226f9
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/22/2018
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Administración del control de acceso basado en rol con la interfaz de la línea de comandos de Azure

> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API DE REST](role-based-access-control-manage-access-rest.md)


Con el control de acceso basado en rol (RBAC), puede definir el acceso para usuarios, grupos y entidades de servicio mediante la asignación de roles en un ámbito determinado. En este artículo se explica cómo administrar el acceso mediante la interfaz de la línea de comandos (CLI) de Azure.

## <a name="prerequisites"></a>requisitos previos

Para usar la CLI de Azure para administrar RBAC, necesita los siguientes requisitos previos:

* [CLI de Azure 2.0](/cli/azure/overview). Puede usarla en el explorador con [Azure Cloud Shell](../cloud-shell/overview.md) o puede [instalarla](/cli/azure/install-azure-cli) en macOS, Linux y Windows y ejecutarla desde la línea de comandos.

## <a name="list-roles"></a>Lista de roles

### <a name="list-role-definitions"></a>Lista de definiciones de roles

Para enumerar todas las definiciones de roles disponibles, utilice [az role definition list](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list
```

En el ejemplo siguiente se muestra el nombre y la descripción de todas las definiciones de roles disponibles:

```azurecli
az role definition list --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs"
}

...
```

En el ejemplo siguiente se muestran todas las definiciones de roles integradas:

```azurecli
az role definition list --custom-role-only false --output json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description, "type":.properties.type}'
```

```Output
{
  "roleName": "API Management Service Contributor",
  "description": "Can manage service and the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Operator Role",
  "description": "Can manage service but not the APIs",
  "type": "BuiltInRole"
}
{
  "roleName": "API Management Service Reader Role",
  "description": "Read-only access to service and APIs",
  "type": "BuiltInRole"
}

...
```

### <a name="list-actions-of-a-role-definition"></a>Lista de las acciones de una definición de roles

Para enumerar las acciones de una definición de roles, use [az role definition list](/cli/azure/role/definition#az_role_definition_list):

```azurecli
az role definition list --name <role_name>
```

En el ejemplo siguiente se muestra la definición de roles de *Colaborador*:

```azurecli
az role definition list --name "Contributor"
```

```Output
[
  {
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "properties": {
      "additionalProperties": {
        "createdBy": null,
        "createdOn": "0001-01-01T08:00:00.0000000Z",
        "updatedBy": null,
        "updatedOn": "2016-12-14T02:04:45.1393855Z"
      },
      "assignableScopes": [
        "/"
      ],
      "description": "Lets you manage everything except access to resources.",
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": [
            "Microsoft.Authorization/*/Delete",
            "Microsoft.Authorization/*/Write",
            "Microsoft.Authorization/elevateAccess/Action"
          ]
        }
      ],
      "roleName": "Contributor",
      "type": "BuiltInRole"
    },
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

En el ejemplo siguiente se muestran los valores de *actions* y *notActions* del rol *Colaborador*:

```azurecli
az role definition list --name "Contributor" --output json | jq '.[] | {"actions":.properties.permissions[0].actions, "notActions":.properties.permissions[0].notActions}'
```

```Output
{
  "actions": [
    "*"
  ],
  "notActions": [
    "Microsoft.Authorization/*/Delete",
    "Microsoft.Authorization/*/Write",
    "Microsoft.Authorization/elevateAccess/Action"
  ]
}
```

En el ejemplo siguiente se muestran los valores de actions del rol *Colaborador de máquina virtual*:

```azurecli
az role definition list --name "Virtual Machine Contributor" --output json | jq '.[] | .properties.permissions[0].actions'
```

```Output
[
  "Microsoft.Authorization/*/read",
  "Microsoft.Compute/availabilitySets/*",
  "Microsoft.Compute/locations/*",
  "Microsoft.Compute/virtualMachines/*",
  "Microsoft.Compute/virtualMachineScaleSets/*",
  "Microsoft.Insights/alertRules/*",
  "Microsoft.Network/applicationGateways/backendAddressPools/join/action",
  "Microsoft.Network/loadBalancers/backendAddressPools/join/action",

  ...

  "Microsoft.Storage/storageAccounts/listKeys/action",
  "Microsoft.Storage/storageAccounts/read"
]
```

## <a name="list-access"></a>Lista de acceso

### <a name="list-role-assignments-for-a-user"></a>Lista de las asignaciones de rol de un usuario

Para mostrar las asignaciones de roles de un usuario específico use [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --assignee <assignee>
```

De forma predeterminada, se mostrarán únicamente las asignaciones que se limitan a la suscripción. Para ver las asignaciones limitadas por grupo o recurso, use `--all`.

En el ejemplo siguiente, se muestran las asignaciones de roles asignadas directamente al usuario *patlong@contoso.com*:

```azurecli
az role assignment list --all --assignee patlong@contoso.com --output json | jq '.[] | {"principalName":.properties.principalName, "roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "principalName": "patlong@contoso.com",
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
```

### <a name="list-role-assignments-for-a-resource-group"></a>Lista de las asignaciones de roles de un grupo de recursos

Para mostrar las asignaciones de roles de un grupo de recursos, utilice [az role assignment list](/cli/azure/role/assignment#az_role_assignment_list):

```azurecli
az role assignment list --resource-group <resource_group>
```

En el ejemplo siguiente se muestran las asignaciones de roles del grupo de recursos *pharma-sales-projectforecast*:

```azurecli
az role assignment list --resource-group pharma-sales-projectforecast --output json | jq '.[] | {"roleDefinitionName":.properties.roleDefinitionName, "scope":.properties.scope}'
```

```Output
{
  "roleDefinitionName": "Backup Operator",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}
{
  "roleDefinitionName": "Virtual Machine Contributor",
  "scope": "/subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/pharma-sales-projectforecast"
}

...
```

## <a name="assign-access"></a>Asignación de acceso

### <a name="assign-a-role-to-a-user"></a>Asignar de un rol a un usuario

Para asignar un rol a un usuario en el ámbito del grupo de recursos, use [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee <assignee> --resource-group <resource_group>
```

En el ejemplo siguiente, se asigna el rol *Colaborador de la máquina virtual* al usuario *patlong@contoso.com* en el ámbito del grupo de recursos *Pharma-Sales-ProjectForcast*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee patlong@contoso.com --resource-group pharma-sales-projectforecast
```

### <a name="assign-a-role-to-a-group"></a>Asignación de un rol a un grupo

Para asignar un rol a un grupo, use [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

En el ejemplo siguiente se asigna el rol *Lector* al grupo *equipo de Ann Mack* con el identificador 22222222-2222-2222-2222-222222222222 en el ámbito de la suscripción. Para obtener el identificador del grupo, puede usar [az ad group list](/cli/azure/ad/group#az_ad_group_list) o [az ad group show](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment create --role Reader --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

En el ejemplo siguiente se asigna el rol *Colaborador de la máquina virtual* al grupo *equipo de Ann Mack* con el identificador 22222222-2222-2222-2222-222222222222 en un ámbito de recursos de una red virtual denominada *pharma-sales-project-network*:

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 22222222-2222-2222-2222-222222222222 --scope /subscriptions/11111111-1111-1111-1111-111111111111/resourcegroups/pharma-sales-projectforecast/providers/Microsoft.Network/virtualNetworks/pharma-sales-project-network
```

### <a name="assign-a-role-to-an-application"></a>Asignación de un rol a una aplicación

Para asignar un rol a una asignación, use [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create):

```azurecli
az role assignment create --role <role> --assignee-object-id <assignee_object_id> --resource-group <resource_group> --scope </subscriptions/subscription_id>
```

En el ejemplo siguiente, se asigna el rol *Colaborador de la máquina virtual* a una aplicación con el identificador de objeto 44444444-4444-4444-4444-444444444444 en el ámbito del grupo de recursos *pharma-sales-projectforecast*. Para obtener el identificador de objeto de la aplicación, puede usar [az ad app list](/cli/azure/ad/app#az_ad_app_list) o [az ad app show](/cli/azure/ad/app#az_ad_app_show).

```azurecli
az role assignment create --role "Virtual Machine Contributor" --assignee-object-id 44444444-4444-4444-4444-444444444444 --resource-group pharma-sales-projectforecast
```

## <a name="remove-access"></a>Quitar acceso

### <a name="remove-a-role-assignment"></a>Eliminación de una asignación de rol

Para quitar una asignación de roles, use [az role assignment delete](/cli/azure/role/assignment#az_role_assignment_delete):

```azurecli
az role assignment delete --assignee <assignee> --role <role> --resource-group <resource_group>
```

En el ejemplo siguiente, se quita la asignación de rol *Colaborador de la máquina virtual* del usuario *patlong@contoso.com* en el grupo de recursos *pharma-sales-projectforecast*:

```azurecli
az role assignment delete --assignee patlong@contoso.com --role "Virtual Machine Contributor" --resource-group pharma-sales-projectforecast
```

En el ejemplo siguiente se quita el rol *Lector* al grupo *equipo de Ann Mack* con el identificador 22222222-2222-2222-2222-222222222222 en el ámbito de la suscripción. Para obtener el identificador del grupo, puede usar [az ad group list](/cli/azure/ad/group#az_ad_group_list) o [az ad group show](/cli/azure/ad/group#az_ad_group_show).

```azurecli
az role assignment delete --assignee 22222222-2222-2222-2222-222222222222 --role "Reader" --scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="custom-roles"></a>Roles personalizados

### <a name="list-custom-roles"></a>Lista de roles personalizados

Para enumerar los roles que están disponibles para la asignación en un ámbito, use [az role definition list](/cli/azure/role/definition#az_role_definition_list).

Los siguientes ejemplos muestran todos los roles personalizados de la suscripción actual:

```azurecli
az role definition list --custom-role-only true --output json | jq '.[] | {"roleName":.properties.roleName, "type":.properties.type}'
```

```azurecli
az role definition list --output json | jq '.[] | if .properties.type == "CustomRole" then {"roleName":.properties.roleName, "type":.properties.type} else empty end'
```

```Output
{
  "roleName": "My Management Contributor",
  "type": "CustomRole"
}
{
  "roleName": "My Service Operator Role",
  "type": "CustomRole"
}
{
  "roleName": "My Service Reader Role",
  "type": "CustomRole"
}

...
```

### <a name="create-a-custom-role"></a>Crear un rol personalizado

Para crear un rol personalizado, use [az role definition create](/cli/azure/role/definition#az_role_definition_create). La definición de roles puede ser una descripción JSON o una ruta de acceso a un archivo que contenga esta descripción.

```azurecli
az role definition create --role-definition <role_definition>
```

En el ejemplo siguiente, se crea un rol personalizado llamado *Operador de máquina virtual*. El rol personalizado asigna acceso a todas las operaciones de lectura de los proveedores de recursos *Microsoft.Compute*, *Microsoft.Storage* y *Microsoft.Network*, y asigna acceso para iniciar, reiniciar y supervisar las máquinas virtuales. El rol personalizado se puede usar en dos suscripciones. En este ejemplo, se usa un archivo JSON como entrada.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition create --role-definition ~/roles/vmoperator.json
```

### <a name="update-a-custom-role"></a>Actualización de un rol personalizado

Para actualizar un rol personalizado, primero use [az role definition list](/cli/azure/role/definition#az_role_definition_list) para recuperar la definición de roles. Después, haga los cambios que desee en la definición de rol. Por último, use [az role definition update](/cli/azure/role/definition#az_role_definition_update) para guardar la definición de roles actualizada.

```azurecli
az role definition update --role-definition <role_definition>
```

En el ejemplo siguiente, se agrega la operación *Microsoft.Insights/diagnosticSettings/* a *Acciones* del rol personalizado de *Operador de máquina virtual*.

vmoperator.json

```json
{
  "Name": "Virtual Machine Operator",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/11111111-1111-1111-1111-111111111111",
    "/subscriptions/33333333-3333-3333-3333-333333333333"
  ]
}
```

```azurecli
az role definition update --role-definition ~/roles/vmoperator.json
```

### <a name="delete-a-custom-role"></a>Eliminación de un rol personalizado

Para eliminar un rol personalizado, use [az role definition delete](/cli/azure/role/definition#az_role_definition_delete). Para especificar el rol que desea eliminar, use el nombre del rol o el identificador. Para determinar el identificador del rol, use [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition delete --name <role_name or role_id>
```

En el ejemplo siguiente se elimina el rol personalizado *Operador de máquina virtual*:

```azurecli
az role definition delete --name "Virtual Machine Operator"
```

## <a name="next-steps"></a>pasos siguientes

[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

