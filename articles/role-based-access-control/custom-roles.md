---
title: Creación de roles personalizados para Azure RBAC | Microsoft Docs
description: Aprenda a definir roles personalizados con Control de acceso basado en roles de Azure para administrar las identidades de manera más precisa en la suscripción de Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9e2ea46ea1a6b5bd3f50d4d4c15492c16c5241c0
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161063"
---
# <a name="create-custom-roles-in-azure"></a>Creación de roles personalizados en Azure

Si los [roles integrados](built-in-roles.md) no satisfacen sus necesidades de acceso específicas, puede crear sus propios roles personalizados. Igual que los roles integrados, puede asignar roles personalizados a usuarios, grupos y entidades de servicio en los ámbitos de suscripción, grupo de recursos y recurso. Los roles personalizados se almacenan en un inquilino de Azure Active Directory (Azure AD) y se pueden compartir entre suscripciones. Se pueden crear roles personalizados con Azure PowerShell, la CLI de Azure o la API REST. En este artículo se describe un ejemplo de cómo empezar a crear roles personalizados con PowerShell y la CLI de Azure.

## <a name="create-a-custom-role-to-open-support-requests-using-powershell"></a>Creación de un rol personalizado para abrir solicitudes de soporte técnico con PowerShell

Para crear un rol personalizado, puede empezar con un rol integrado, editarlo y, a continuación, crear un nuevo rol. En este ejemplo, el rol integrado [Lector](built-in-roles.md#reader) se personaliza para crear un rol personalizado denominado "Nivel de acceso de incidencias de soporte técnico de lector". Permite al usuario ver todo el contenido de la suscripción y también abrir las solicitudes de soporte técnico.

> [!NOTE]
> Los únicos dos roles integrados que permiten a un usuario abrir solicitudes de soporte técnico son [Propietario](built-in-roles.md#owner) y [Colaborador](built-in-roles.md#contributor). Para que un usuario sea capaz de abrir solicitudes de soporte técnico, se le debe asignar un rol en el ámbito de la suscripción, ya que todas las solicitudes de soporte técnico se crean en función de una suscripción de Azure.

En PowerShell, use el comando [Get-AzureRmRoleDefinition](/powershell/module/azurerm.resources/get-azurermroledefinition) para exportar el rol [Lector](built-in-roles.md#reader) en formato JSON.

```azurepowershell
Get-AzureRmRoleDefinition -Name "Reader" | ConvertTo-Json | Out-File C:\rbacrole2.json
```

A continuación, se muestra la salida JSON para el rol [Lector](built-in-roles.md#reader). Un rol típico se compone de tres secciones principales: `Actions`, `NotActions` y `AssignableScopes`. En la sección `Actions`, se enumeran todas las operaciones permitidas para el rol. Para excluir las operaciones de `Actions`, debe agregarlas a `NotActions`. Los permisos vigentes se calculan restando las operaciones de `NotActions` de las de `Actions`.

```json
{
    "Name":  "Reader",
    "Id":  "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "IsCustom":  false,
    "Description":  "Lets you view everything, but not make any changes.",
    "Actions":  [
                    "*/read"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/"
                         ]
}
```

A continuación, puede editar la salida JSON para crear el rol personalizado. En este caso, para crear incidencias de soporte técnico, se debe agregar la operación `Microsoft.Support/*`. Cada operación está disponible desde un proveedor de recursos. Para obtener una lista de las operaciones para un proveedor de recursos, puede usar el comando [Get-AzureRmProviderOperation](/powershell/module/azurerm.resources/get-azurermprovideroperation) o consultar [Operaciones del proveedor de recursos de Azure Resource Manager](resource-provider-operations.md).

Es obligatorio que el rol contenga los identificadores explícitos de las suscripciones en las que se utiliza. Los identificadores de suscripción aparecen en la sección `AssignableScopes`; en caso contrario, no podrá importar el rol en su suscripción.

Por último, debe establecer la propiedad `IsCustom` en `true` para especificar que se trata de un rol personalizado.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes":  [
                             "/subscriptions/11111111-1111-1111-1111-111111111111"
                         ]
}
```

Para crear el nuevo rol personalizado, puede usar el comando [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition) y proporcionar el archivo de definición de roles JSON actualizado.

```azurepowershell
New-AzureRmRoleDefinition -InputFile "C:\rbacrole2.json"
```

Tras ejecutar [New-AzureRmRoleDefinition](/powershell/module/azurerm.resources/new-azurermroledefinition), el nuevo rol personalizado ya está disponible en Azure Portal y se puede asignar a los usuarios.

![Captura de pantalla de un rol importado en Azure Portal](./media/custom-roles/18.png)

![Captura de pantalla de la asignación de un rol importado personalizado a un usuario del mismo directorio](./media/custom-roles/19.png)

![Captura de pantalla de los permisos de un rol importado personalizado](./media/custom-roles/20.png)

Los usuarios con este rol personalizado pueden crear nuevas solicitudes de soporte técnico.

![Captura de pantalla de un rol personalizado que puede crear solicitudes de soporte técnico](./media/custom-roles/21.png)

Los usuarios con este rol personalizado no pueden realizar otras acciones como crear máquinas virtuales o grupos de recursos.

![Captura de pantalla de un rol personalizado que no puede crear máquinas virtuales](./media/custom-roles/22.png)

![Captura de pantalla de un rol personalizado que no puede crear nuevos grupos de recursos](./media/custom-roles/23.png)

## <a name="create-a-custom-role-to-open-support-requests-using-azure-cli"></a>Creación de un rol personalizado para abrir solicitudes de soporte técnico con la CLI de Azure

Los pasos para crear un rol personalizado mediante la CLI de Azure son similares a los que se usan con PowerShell, excepto en que la salida JSON es diferente.

En este ejemplo, puede empezar con el rol integrado [Lector](built-in-roles.md#reader). Para enumerar las acciones del rol [Lector](built-in-roles.md#reader), use el comando [az role definition list](/cli/azure/role/definition#az_role_definition_list).

```azurecli
az role definition list --name "Reader" --output json
```

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "name": "acdd72a7-3385-48ef-bd42-f606fba81ae7",
    "permissions": [
      {
        "actions": [
          "*/read"
        ],
        "additionalProperties": {},
        "notActions": [],
      }
    ],
    "roleName": "Reader",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

Cree un archivo JSON con el formato siguiente. Se ha agregado la operación `Microsoft.Support/*` a las secciones `Actions` para que este usuario pueda abrir solicitudes de soporte técnico mientras continúa siendo un lector. Debe agregar el identificador de suscripción en el que se usará este rol en la sección `AssignableScopes`.

```json
{
    "Name":  "Reader support tickets access level",
    "IsCustom":  true,
    "Description":  "View everything in the subscription and also open support requests.",
    "Actions":  [
                    "*/read",
                    "Microsoft.Support/*"
                ],
    "NotActions":  [

                   ],
    "AssignableScopes": [
                            "/subscriptions/11111111-1111-1111-1111-111111111111"
                        ]
}
```

Para crear un nuevo rol personalizado, use el comando [az role definition create](/cli/azure/role/definition#az_role_definition_create).

```azurecli
az role definition create --role-definition ~/roles/rbacrole1.json
```

El nuevo rol ahora está disponible en Azure Portal y el proceso para usarlo es el mismo que en la sección anterior de PowerShell.

![Captura de pantalla de Azure Portal de un rol personalizado creado con la CLI 1.0](./media/custom-roles/26.png)


## <a name="see-also"></a>Otras referencias
- [Descripción de definiciones de roles](role-definitions.md)
- [Administración del control de acceso basado en rol con Azure PowerShell](role-assignments-powershell.md)
- [Administración del control de acceso basado en rol con la CLI de Azure](role-assignments-cli.md)
- [Administración del control de acceso basado en rol con la API REST](role-assignments-rest.md)
