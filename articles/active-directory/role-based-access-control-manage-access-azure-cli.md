---
title: "Administración del control de acceso basado en rol (RBAC) con la CLI de Azure | Microsoft Docs"
description: "Obtenga información sobre cómo administrar el control de acceso basado en rol (RBAC) con la interfaz de la línea de comandos de Azure a través de la enumeración de roles y acciones de roles y de la asignación de roles a los ámbitos de aplicación y suscripción."
services: active-directory
documentationcenter: 
author: andredm7
manager: mtillman
ms.assetid: 3483ee01-8177-49e7-b337-4d5cb14f5e32
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/12/2017
ms.author: andredm
ms.reviewer: rqureshi
ms.openlocfilehash: b99264eb69f115db6e334b6aceae6ed897202d56
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="manage-role-based-access-control-with-the-azure-command-line-interface"></a>Administración del control de acceso basado en rol con la interfaz de la línea de comandos de Azure
> [!div class="op_single_selector"]
> * [PowerShell](role-based-access-control-manage-access-powershell.md)
> * [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
> * [API DE REST](role-based-access-control-manage-access-rest.md)


Puede usar el control de acceso basado en rol (RBAC) del Portal de Azure y la API de Azure Resource Manager para administrar el acceso a su suscripción y sus recursos en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

Para poder usar la interfaz de la línea de comandos (CLI) de Azure y administrar RBAC, debe cumplir los siguientes requisitos previos:

* CLI de Azure versión 0.8.8 o posterior. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de la interfaz de la línea de comandos de Azure](../cli-install-nodejs.md).
* Azure Resource Manager en la CLI de Azure Para más información, consulte [Uso de la CLI de Azure con Resource Manager](../xplat-cli-azure-resource-manager.md).

## <a name="list-roles"></a>Lista de roles
### <a name="list-all-available-roles"></a>Lista de todos los roles disponibles
Para enumerar todos los roles disponibles, use:

        azure role list

El ejemplo siguiente muestra la lista de *todos los roles disponibles*.

```
azure role list --json | jq '.[] | {"roleName":.properties.roleName, "description":.properties.description}'
```

![Línea de comandos de Azure RBAC: lista de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

### <a name="list-actions-of-a-role"></a>Lista de las acciones de un rol
Para enumerar las acciones de un rol, use:

    azure role show "<role name>"

El ejemplo siguiente muestra las acciones del rol *Colaborador* y del rol *Colaborador de la máquina virtual*.

```
azure role show "contributor" --json | jq '.[] | {"Actions":.properties.permissions[0].actions,"NotActions":properties.permissions[0].notActions}'

azure role show "virtual machine contributor" --json | jq '.[] | .properties.permissions[0].actions'
```

![Línea de comandos de Azure RBAC: vista de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

## <a name="list-access"></a>Lista de acceso
### <a name="list-role-assignments-effective-on-a-resource-group"></a>Lista de las asignaciones de rol vigentes en un grupo de recursos
Para mostrar las asignaciones de roles de un grupo de recursos, utilice:

    azure role assignment list --resource-group <resource group name>

En el ejemplo siguiente, se muestran las asignaciones de roles del grupo *pharma-sales-projecforcast* .

```
azure role assignment list --resource-group pharma-sales-projecforcast --json | jq '.[] | {"DisplayName":.properties.aADObject.displayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Línea de comandos de Azure RBAC: lista de asignación de roles de Azure por grupo (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

### <a name="list-role-assignments-for-a-user"></a>Lista de las asignaciones de rol de un usuario
Para enumerar las asignaciones de rol de un usuario específico y las asignaciones de los grupos de un usuario, use:

    azure role assignment list --signInName <user email>

También puede ver las asignaciones de roles que se heredan de grupos modificando el comando:

    azure role assignment list --expandPrincipalGroups --signInName <user email>

En el ejemplo siguiente, se muestran las asignaciones de rol concedidas al usuario *sameert@aaddemo.com* . Entre ellas, los roles asignados directamente al usuario y los roles que se heredan de los grupos.

```
azure role assignment list --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'

azure role assignment list --expandPrincipalGroups --signInName sameert@aaddemo.com --json | jq '.[] | {"DisplayName":.properties.aADObject.DisplayName,"RoleDefinitionName":.properties.roleName,"Scope":.properties.scope}'
```

![Línea de comandos de Azure RBAC: lista de asignación de roles de Azure por usuario (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

## <a name="grant-access"></a>Conceder acceso
Para conceder acceso después de haber identificado el rol que desea asignar, use:

    azure role assignment create

### <a name="assign-a-role-to-group-at-the-subscription-scope"></a>Asignación de un rol a un grupo en el ámbito de la suscripción
Para asignar un rol a un grupo en el ámbito de la suscripción, use:

    azure role assignment create --objectId  <group object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

En el ejemplo siguiente se asigna el rol *Lector* al *equipo de Christine Koch* en el ámbito de la *suscripción*.

![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por grupo (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

### <a name="assign-a-role-to-an-application-at-the-subscription-scope"></a>Asignación de un rol a una aplicación en el ámbito de la suscripción
Para asignar un rol a una aplicación en el ámbito de la suscripción, use:

    azure role assignment create --objectId  <applications object id> --roleName <name of role> --subscription <subscription> --scope <subscription/subscription id>

En el ejemplo siguiente, se concede el rol *Colaborador* a una aplicación de *Azure AD* en la suscripción seleccionada.

 ![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por aplicación (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

### <a name="assign-a-role-to-a-user-at-the-resource-group-scope"></a>Asignación de un rol a un usuario en el ámbito del grupo de recursos
Para asignar un rol a un usuario en el ámbito del grupo de recursos, use:

    azure role assignment create --signInName  <user email address> --roleName "<name of role>" --resourceGroup <resource group name>

En el ejemplo siguiente, se concede el rol *Colaborador de la máquina virtual* al usuario *samert@aaddemo.com* en el ámbito del grupo de recursos *Pharma-Sales-ProjectForcast*.

![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por usuario (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

### <a name="assign-a-role-to-a-group-at-the-resource-scope"></a>Asignación de un rol a un grupo en el ámbito del recurso
Para asignar un rol a un grupo en el ámbito del recurso, use:

    azure role assignment create --objectId <group id> --role "<name of role>" --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

En el ejemplo siguiente, se concede el rol *Colaborador de la máquina virtual* a un grupo de *Azure AD* en una *subred*.

![Línea de comandos de Azure RBAC: asignación de roles de Azure creada por grupo (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

## <a name="remove-access"></a>Quitar acceso
Para quitar una asignación de rol, use:

    azure role assignment delete --objectId <object id to from which to remove role> --roleName "<role name>"

En el ejemplo siguiente, se quita la asignación de rol *Colaborador de la máquina virtual* del usuario *sammert@aaddemo.com* en el grupo de recursos *Pharma-Sales-ProjectForcast*.
Luego, se quita la asignación de rol de un grupo de la suscripción.

![Línea de comandos de Azure RBAC: asignación de roles de Azure eliminada (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## <a name="create-a-custom-role"></a>Crear un rol personalizado
Para crear un rol personalizado, use:

    azure role create --inputfile <file path>

En el ejemplo siguiente, se crea un rol personalizado llamado *Operador de máquina virtual*. El rol personalizado concede acceso a todas las operaciones de lectura de los proveedores de recursos *Microsoft.Compute*, *Microsoft.Storage* y *Microsoft.Network*, y concede acceso para iniciar, reiniciar y supervisar las máquinas virtuales. El rol personalizado se puede usar en dos suscripciones. En este ejemplo, se usa un archivo JSON como entrada.

![JSON: definición de roles personalizados (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-1.png)

![Línea de comandos de Azure RBAC: creación de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-create-2.png)

## <a name="modify-a-custom-role"></a>Modificación de un rol personalizado
Para modificar un rol personalizado, use en primer lugar el comando `azure role list` para recuperar la definición de rol. Después, haga los cambios que desee en el archivo de definición de rol. Por último, use `azure role set` para guardar la definición de rol modificada.

    azure role set --inputfile <file path>

En el ejemplo siguiente, se agrega la operación *Microsoft.Insights/diagnosticSettings/* a **Acciones** y una suscripción de Azure al valor de **AssignableScopes** del rol personalizado de operador de máquina virtual.

![JSON: modificación de definición de roles personalizados (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set-1.png)

![Línea de comandos de Azure RBAC: establecimiento de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-set2.png)

## <a name="delete-a-custom-role"></a>Eliminación de un rol personalizado
Para eliminar un rol personalizado, use primero el comando `azure role list` para determinar el **id.** del rol. Luego, use el comando `azure role delete` para eliminar el rol especificando el **id**.

En el ejemplo siguiente se quita el rol personalizado *Operador de máquina virtual* .

![Línea de comandos de Azure RBAC: eliminación de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-delete.png)

## <a name="list-custom-roles"></a>Lista de roles personalizados
Para enumerar los roles que están disponibles para la asignación en un ámbito, use el comando `azure role list` .

En el comando siguiente se enumeran todos los roles disponibles para la asignación en la suscripción seleccionada.

```
azure role list --json | jq '.[] | {"name":.properties.roleName, type:.properties.type}'
```

![Línea de comandos de Azure RBAC: lista de roles de Azure (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list1.png)

En el ejemplo siguiente, el rol personalizado *Operador de máquina virtual* no está disponible en la suscripción *Production4* debido a que la suscripción no se encuentra entre los valores de **AssignableScopes** del rol.

```
azure role list --json | jq '.[] | if .properties.type == "CustomRole" then .properties.roleName else empty end'
```

![Línea de comandos de Azure RBAC: lista de roles de Azure para roles personalizados (captura de pantalla)](./media/role-based-access-control-manage-access-azure-cli/5-azure-role-list2.png)

## <a name="next-steps"></a>Pasos siguientes
[!INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

