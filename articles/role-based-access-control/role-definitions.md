---
title: Descripción de definiciones de roles en RBAC de Azure | Microsoft Docs
description: Obtenga información acerca de las definiciones de roles en el control de acceso basado en rol (RBAC) y cómo definir roles personalizados para la administración de acceso específico de recursos en Azure.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
ms.assetid: ''
ms.service: role-based-access-control
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/12/2018
ms.author: rolyon
ms.reviewer: rqureshi
ms.custom: ''
ms.openlocfilehash: 7a9e257d445ff7dadfe27d1c75cde6f58a393397
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2018
ms.locfileid: "34161818"
---
# <a name="understand-role-definitions"></a>Descripción de definiciones de roles

Si quiere comprender el funcionamiento de un rol o si va a crear su propio [rol personalizado](custom-roles.md), resulta útil entender cómo se definen los roles. En este artículo se describen los detalles de las definiciones de roles y se proporcionan algunos ejemplos.

## <a name="role-definition-structure"></a>Estructura de definición de roles

Una *definición de roles* es una recopilación de permisos. A veces, se denomina *rol* simplemente. Una definición de roles enumera las operaciones que se pueden realizar, por ejemplo, de lectura, escritura y eliminación. También puede mostrar las operaciones que no se pueden realizar. Tiene la siguiente estructura:

```
assignableScopes []
description
id
name
permissions []
  actions []
  notActions []
roleName
roleType
type
```

Se especifican las operaciones con cadenas que tienen el formato siguiente:

- `Microsoft.{ProviderName}/{ChildResourceType}/{action}`

La parte `{action}` de una cadena de la operación especifica el tipo de operaciones que puede realizar en un tipo de recurso. Por ejemplo, verá las siguientes subcadenas en `{action}`:

| Subcadena de acción    | DESCRIPCIÓN         |
| ------------------- | ------------------- |
| `*` | El carácter comodín concede acceso a todas las operaciones que coinciden con la cadena. |
| `read` | Permite operaciones de lectura (GET). |
| `write` | Permite operaciones de escritura (PUT, POST y PATCH). |
| `delete` | Permite operaciones de eliminación (DELETE). |

Esta es la definición de roles [Colaborador](built-in-roles.md#contributor) en formato JSON. La operación de carácter comodín (`*`) en `actions` indica que la entidad de seguridad asignada a este rol puede realizar todas las acciones o, en otras palabras, administrar todo el contenido. Esto incluye las acciones definidas en el futuro, a medida que Azure agregue nuevos tipos de recursos. Las operaciones en `notActions` se restan de `actions`. En el caso del rol [Colaborador](built-in-roles.md#contributor), `notActions` le quita la capacidad para administrar y asignar el acceso a los recursos.

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you manage everything except access to resources.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/b24988ac-6180-42a0-ab88-20f7382dd24c",
    "name": "b24988ac-6180-42a0-ab88-20f7382dd24c",
    "permissions": [
      {
        "actions": [
          "*"
        ],
        "additionalProperties": {},
        "notActions": [
          "Microsoft.Authorization/*/Delete",
          "Microsoft.Authorization/*/Write",
          "Microsoft.Authorization/elevateAccess/Action"
        ],
      }
    ],
    "roleName": "Contributor",
    "roleType": "BuiltInRole",
    "type": "Microsoft.Authorization/roleDefinitions"
  }
]
```

## <a name="management-operations"></a>Operaciones de administración

El control de acceso basado en rol para las operaciones de administración se especifica en las secciones `actions` y `notActions` de una definición de roles. A continuación, se incluyen algunos ejemplos de operaciones de administración de Azure:

- Administrar el acceso a una cuenta de almacenamiento
- Crear, actualizar o eliminar un contenedor de blobs
- Eliminar un grupo de recursos y todos sus recursos

El acceso a la administración no se hereda con los datos. Esta separación impide que los roles con caracteres comodín (`*`) tengan acceso no restringido a los datos. Por ejemplo, si un usuario tiene el rol [Lector](built-in-roles.md#reader) en una suscripción, podrá ver la cuenta de almacenamiento, pero no los datos subyacentes.

## <a name="actions"></a>actions

El permiso `actions` especifica las operaciones de administración a las que concede acceso el rol. Se trata de una colección de cadenas de operación que identifican a las operaciones protegibles de proveedores de recursos de Azure. A continuación, se incluyen algunos ejemplos de operaciones de administración que se pueden usar en `actions`.

| Cadena de la operación    | DESCRIPCIÓN         |
| ------------------- | ------------------- |
| `*/read` | Concede acceso a las operaciones de lectura a todos los tipos de recursos de todos los proveedores de recursos de Azure.|
| `Microsoft.Compute/*` | Concede acceso a todas las operaciones a todos los tipos de recursos del proveedor de recursos Microsoft.Compute.|
| `Microsoft.Network/*/read` | Concede acceso a las operaciones de lectura a todos los tipos de recursos del proveedor de recursos Microsoft.Network.|
| `Microsoft.Compute/virtualMachines/*` | Concede acceso a todas las operaciones de las máquinas virtuales y a sus tipos de recursos secundarios.|
| `microsoft.web/sites/restart/Action` | Concede acceso para reiniciar una aplicación web.|

## <a name="notactions"></a>notActions

El permiso `notActions` especifica las operaciones de administración que se excluyen de los `actions` permitidos. Use el permiso `notActions` si el conjunto de operaciones que quiere permitir se define más fácilmente mediante la exclusión de las operaciones restringidas. El acceso concedido por un rol (permisos vigentes) se calcula restando las operaciones de `notActions` de las de `actions`.

> [!NOTE]
> Si un usuario tiene asignado un rol que excluye una operación en `notActions` y se le asigna un segundo rol que sí que concede acceso a esa operación, el usuario puede realizar dicha operación. `notActions` no es una regla de denegación, es simplemente una manera cómoda de crear un conjunto de operaciones permitidas cuando es necesario excluir operaciones específicas.
>

## <a name="assignablescopes"></a>assignableScopes

La sección `assignableScopes` especifica los ámbitos (grupos de administración [actualmente en versión preliminar], suscripciones, grupos de recursos o recursos) en los que el rol está disponible para la asignación. Puede permitir que el rol esté disponible para su asignación solamente en las suscripciones o los grupos de recursos que lo requieran, sin necesidad de abarrotar la experiencia de usuario con el resto de las suscripciones o grupos de recursos. Tiene que utilizar al menos un grupo de administración, una suscripción, un grupo de recursos o un identificador de recurso.

Los roles integrados tienen `assignableScopes` establecido en el ámbito raíz (`"/"`). El ámbito raíz indica que el rol está disponible para la asignación en todos los ámbitos. No puede usar el ámbito raíz en sus propios roles personalizados. Si lo intenta, se producirá un error de autorización.

Ejemplos de ámbitos asignables válidos son:

| Escenario | Ejemplo |
|----------|---------|
| El rol está disponible para la asignación en una única suscripción. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e"` |
| El rol está disponible para la asignación en dos suscripciones. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e", "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624"` |
| El rol está disponible para la asignación solo en el grupo de recursos de red. | `"/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network"` |
| El rol está disponible para la asignación en todos los ámbitos. | `"/"` |

## <a name="assignablescopes-and-custom-roles"></a>assignableScopes y roles personalizados

La sección `assignableScopes` de un rol personalizado también controla quién puede crear, eliminar, modificar o ver dicho rol.

| Task | Operación | DESCRIPCIÓN |
| --- | --- | --- |
| Creación o eliminación de un rol personalizado | `Microsoft.Authorization/ roleDefinition/write` | Los usuarios que tienen acceso a esta operación en todos los ámbitos `assignableScopes` del rol personalizado pueden crear (o eliminar) roles personalizados para usar en esos ámbitos. Por ejemplo, los [propietarios](built-in-roles.md#owner) y [administradores del acceso de los usuarios](built-in-roles.md#user-access-administrator) de las suscripciones, los grupos de recursos y los recursos. |
| Modificación de un rol personalizado | `Microsoft.Authorization/ roleDefinition/write` | Los usuarios que tienen acceso a esta operación en todos los ámbitos `assignableScopes` del rol personalizado pueden modificar roles personalizados en esos ámbitos. Por ejemplo, los [propietarios](built-in-roles.md#owner) y [administradores del acceso de los usuarios](built-in-roles.md#user-access-administrator) de las suscripciones, los grupos de recursos y los recursos. |
| Visualización de un rol personalizado | `Microsoft.Authorization/ roleDefinition/read` | Los usuarios que tienen acceso a esta operación en un ámbito pueden ver los roles personalizados que están disponibles para su asignación en ese ámbito. Todos los roles integrados permiten que los roles personalizados estén disponibles para la asignación. |

## <a name="role-definition-examples"></a>Ejemplos de definición de roles

En el siguiente ejemplo, se muestra la definición de roles [Lector](built-in-roles.md#reader) tal como se muestra mediante la CLI de Azure:

```json
[
  {
    "additionalProperties": {},
    "assignableScopes": [
      "/"
    ],
    "description": "Lets you view everything, but not make any changes.",
    "id": "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/acdd72a7-3385-48ef-bd42-f606fba81ae7",
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

A continuación, se muestra un ejemplo de rol personalizado para supervisar y reiniciar máquinas virtuales, tal como se muestra mediante Azure PowerShell:

```json
{
  "Name":  "Virtual Machine Operator",
  "Id":  "88888888-8888-8888-8888-888888888888",
  "IsCustom":  true,
  "Description":  "Can monitor and restart virtual machines.",
  "Actions":  [
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
  "NotActions":  [

                 ],
  "AssignableScopes":  [
                           "/subscriptions/{subscriptionId1}",
                           "/subscriptions/{subscriptionId2}",
                           "/subscriptions/{subscriptionId3}"
                       ]
}
```

## <a name="see-also"></a>Otras referencias

* [Roles integrados](built-in-roles.md)
* [Roles personalizados](custom-roles.md)
* [Operaciones del proveedor de recursos de Azure Resource Manager](resource-provider-operations.md)
