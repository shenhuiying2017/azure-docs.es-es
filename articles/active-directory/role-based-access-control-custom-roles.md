---
title: "Creación de roles personalizados para Azure RBAC | Microsoft Docs"
description: "Aprenda a definir roles personalizados con Control de acceso basado en roles de Azure para administrar las identidades de manera más precisa en la suscripción de Azure."
services: active-directory
documentationcenter: 
author: andredm7
manager: femila
ms.assetid: e4206ea9-52c3-47ee-af29-f6eef7566fa5
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/11/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8e72f2c8095d13c4b6df3c6576bd58806a3c0f2f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-custom-roles-for-azure-role-based-access-control"></a>Creación de roles personalizados para el control de acceso basado en roles de Azure
Cree un rol personalizado en Control de acceso basado en rol (RBAC) de Azure si ninguno de los roles integrados satisface sus necesidades de acceso específicas. Se pueden crear roles personalizados con [Azure PowerShell](role-based-access-control-manage-access-powershell.md), la [interfaz de la línea de comandos (CLI) de Azure](role-based-access-control-manage-access-azure-cli.md) y la [API de REST](role-based-access-control-manage-access-rest.md). Igual que los roles integrados, puede asignar roles personalizados a usuarios, grupos y aplicaciones en los ámbitos de suscripción, grupo de recursos y recurso. Los roles personalizados se almacenan en un inquilino de Azure AD y se pueden compartir entre suscripciones.

Cada inquilino puede crear hasta 2000 roles personalizados. 

A continuación se muestra un ejemplo de rol personalizado para supervisar y reiniciar máquinas virtuales:

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
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
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
## <a name="actions"></a>Acciones
La propiedad **Actions** de un rol personalizado especifica las operaciones de Azure a las que el rol concede acceso. Se trata de una colección de cadenas de operación que identifican a las operaciones protegibles de proveedores de recursos de Azure. Las cadenas de operaciones usan el formato `Microsoft.<ProviderName>/<ChildResourceType>/<action>`. Las cadenas de la operación que contienen caracteres comodín (\*) conceden acceso a todas las operaciones que coinciden con la cadena de la operación. Por ejemplo:

* `*/read` concede acceso a las operaciones de lectura a todos los tipos de recursos de todos los proveedores de recursos de Azure.
* `Microsoft.Compute/*` concede acceso a todas las operaciones a todos los tipos de recursos del proveedor de recursos Microsoft.Compute.
* `Microsoft.Network/*/read` concede acceso a las operaciones de lectura a todos los tipos de recursos del proveedor de recursos Microsoft.Network de Azure.
* `Microsoft.Compute/virtualMachines/*` concede acceso a todas las operaciones de las máquinas virtuales y a sus tipos de recursos secundarios.
* `Microsoft.Web/sites/restart/Action` concede acceso para reiniciar sitios web.

Use `Get-AzureRmProviderOperation` (en PowerShell) o `azure provider operations show` (en la CLI de Azure) para mostrar las operaciones de proveedores de recursos de Azure. También puede usar estos comandos para comprobar que una cadena de operación es válida y para expandir las cadenas de operación con comodín.

```
Get-AzureRMProviderOperation Microsoft.Compute/virtualMachines/*/action | FT Operation, OperationName

Get-AzureRMProviderOperation Microsoft.Network/*
```

![Captura de pantalla de PowerShell: Get-AzureRMProviderOperation](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

```
azure provider operations show "Microsoft.Compute/virtualMachines/*/action" --js on | jq '.[] | .operation'

azure provider operations show "Microsoft.Network/*"
```

![Captura de pantalla de la CLI de Azure: azure provider operations show "Microsoft.Compute/virtualMachines/\*/action" ](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

## <a name="notactions"></a>NotActions
Use la propiedad **NotActions** si el conjunto de operaciones que quiere permitir se define más fácilmente mediante la exclusión de las operaciones restringidas. El acceso concedido por un rol personalizado se calcula restando las operaciones **NotActions** de las operaciones **Actions**.

> [!NOTE]
> Si un usuario tiene asignado un rol que excluye una operación en **NotActions** y se le asigna un segundo rol que sí concede acceso a esa operación, el usuario puede realizarla. **NotActions** no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de operaciones permitidas cuando es necesario excluir operaciones específicas.
>
>

## <a name="assignablescopes"></a>Ámbitos asignables
La propiedad **AssignableScopes** del rol personalizado especifica los ámbitos (suscripciones, grupos de recursos o recursos) dentro de los que dicho rol personalizado está disponible para su asignación. Puede permitir que el rol personalizado esté disponible para su asignación solamente en las suscripciones o los grupos de recursos que lo requieran, sin necesidad de abarrotar la experiencia de usuario con el resto de las suscripciones o grupos de recursos.

Ejemplos de ámbitos asignables válidos son:

* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624”: permite la disponibilidad del rol para su asignación en dos suscripciones.
* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”: permite la disponibilidad del rol para su asignación en una sola suscripción.
* “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network”: permite la disponibilidad del rol para su asignación solamente en el grupo de recursos de red.

> [!NOTE]
> Tiene que utilizar al menos una suscripción, grupo de recursos o identificador de recurso.
>
>

## <a name="custom-roles-access-control"></a>Control de acceso de roles personalizados
La propiedad **AssignableScopes** del rol personalizado también controla quién puede ver, modificar y eliminar el rol.

* ¿Quién puede crear un rol personalizado?
    Los propietarios (y administradores del acceso de los usuarios) de las suscripciones, los grupos de recursos y los recursos pueden crear roles personalizados para su uso en esos ámbitos.
    El usuario que crea el rol debe ser capaz de realizar la operación `Microsoft.Authorization/roleDefinition/write` en todos los elementos **AssignableScopes** del rol.
* ¿Quién puede modificar un rol personalizado?
    Los propietarios (y administradores del acceso de los usuarios) de las suscripciones, los grupos de recursos y los recursos pueden modificar roles personalizados en esos ámbitos. Los usuarios deben poder realizar la operación `Microsoft.Authorization/roleDefinition/write` en todos los elementos **AssignableScopes** de un rol personalizado.
* ¿Quién puede ver los roles personalizados?
    Todos los roles integrados de RBAC de Azure permiten ver los roles que están disponibles para la asignación. Los usuarios que pueden realizar la operación `Microsoft.Authorization/roleDefinition/read` en un ámbito, pueden ver los roles RBAC que están disponibles para su asignación en ese ámbito.

## <a name="see-also"></a>Otras referencias
* [Control de acceso basado en roles de Azure](role-based-access-control-configure.md): introducción a RBAC en el Portal de Azure.
* Aprenda a administrar el acceso con:
  * [PowerShell](role-based-access-control-manage-access-powershell.md)
  * [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
  * [API DE REST](role-based-access-control-manage-access-rest.md)
* [Roles integrados](role-based-access-built-in-roles.md): obtenga información sobre los roles incluidos de forma predeterminada en RBAC.
