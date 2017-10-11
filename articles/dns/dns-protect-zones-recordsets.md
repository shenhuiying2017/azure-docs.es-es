---
title: "Protección de registros y zonas DNS | Microsoft Docs"
description: "Cómo proteger conjuntos de registros y zonas DNS en DNS de Microsoft Azure."
services: dns
documentationcenter: na
author: jtuliani
manager: carmonm
ms.assetid: 190e69eb-e820-4fc8-8e9a-baaf0b3fb74a
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/20/2016
ms.author: jonatul
ms.openlocfilehash: 0b7040d6273b3a6b85cd55850d596807226b87fc
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-protect-dns-zones-and-records"></a>Cómo proteger registros y zonas DNS

Los registros y las zonas DNS son recursos críticos. Si se elimina una zona DNS o incluso tan solo un registro DNS puede provocar una interrupción total del servicio.  Por consiguiente, es importante proteger las zonas y los registros DNS críticos contra cambios accidentales o no autorizados.

En este artículo se explica cómo DNS de Azure le permite proteger sus zonas y registros DNS de dichos cambios.  Aplicamos dos eficaces características de seguridad que proporciona Azure Resource Manager: [control de acceso basado en rol](../active-directory/role-based-access-control-what-is.md) y [bloqueos de recursos](../azure-resource-manager/resource-group-lock-resources.md).

## <a name="role-based-access-control"></a>Control de acceso basado en rol

El control de acceso basado en rol (RBAC) de Azure permite realizar una administración detallada del acceso de usuarios, grupos y recursos de Azure. Con RBAC, puede conceder de forma precisa el grado de acceso que los usuarios necesiten para realizar sus trabajos. Para más información sobre cómo RBAC ayuda a administrar el acceso, vea [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).

### <a name="the-dns-zone-contributor-role"></a>Rol "DNS Zone Contributor" (Colaborador de zona DNS)

Se trata de un rol integrado que ofrece Azure para administrar recursos DNS.  Al asignar permisos de colaborador de zona DNS a un usuario o grupo permite que ese grupo administre recursos DNS, pero no recursos de ningún otro tipo.

Por ejemplo, supongamos que el grupo de recursos "myzones" contiene cinco zonas de Contoso Corporation. Cuando se conceden permisos "DNS Zone Contributor" (Colaborador de zona DNS) de administrador de DNS a ese grupo de recursos, se permite el control total sobre esas zonas DNS. También se evita la concesión de permisos innecesarios, por ejemplo el administrador de DNS no puede crear ni detener máquinas virtuales.

La manera más sencilla de asignar permisos RBAC es [a través de Azure Portal](../active-directory/role-based-access-control-configure.md).  Abra la hoja "Control de acceso (IAM)" del grupo de recursos, haga clic en "Agregar" y luego seleccione el rol "DNS Zone Contributor" (Colaborador de zona DNS) y los usuarios o grupos a los que necesita concederle permisos.

![RBAC de nivel de grupo de recursos a través de Azure Portal](./media/dns-protect-zones-recordsets/rbac1.png)

Los permisos también se pueden [conceder mediante Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>"
```

El comando equivalente también está [disponible a través de la CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to all zones in a resource group
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --resourceGroup "<resource group name>"
```

### <a name="zone-level-rbac"></a>RBAC de nivel de zona

Las reglas de RBAC de Azure pueden aplicarse a una suscripción, a un grupo de recursos o a un recurso individual. En el caso de DNS de Azure, ese recurso puede ser una zona DNS individual o incluso un conjunto de registros individual.

Por ejemplo, supongamos que el grupo de recursos "myzones" contiene la zona "contoso.com" y una subzona "customers.contoso.com" en la que se crean registros CNAME para cada cuenta de cliente.  La cuenta que se use para administrar estos registros CNAME solo debe tener permisos para crear registros en la zona "customers.contoso.com", no debe tener acceso a otras zonas.

Los permisos RBAC de nivel de zona se pueden conceder a través de Azure Portal.  Abra la hoja "Control de acceso (IAM)" de la zona, haga clic en "Agregar" y luego seleccione el rol "DNS Zone Contributor" (Colaborador de zona DNS) y los usuarios o grupos a los que necesita concederle permisos.

![RBAC de nivel de zona DNS a través de Azure Portal](./media/dns-protect-zones-recordsets/rbac2.png)

Los permisos también se pueden [conceder mediante Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant 'DNS Zone Contributor' permissions to a specific zone
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -ResourceGroupName "<resource group name>" -ResourceName "<zone name>" -ResourceType Microsoft.Network/DNSZones
```

El comando equivalente también está [disponible a través de la CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant 'DNS Zone Contributor' permissions to a specific zone
azure role assignment create --signInName <user email address> --roleName "DNS Zone Contributor" --resource-name <zone name> --resource-type Microsoft.Network/DNSZones --resource-group <resource group name>
```

### <a name="record-set-level-rbac"></a>RBAC de nivel de conjunto de registros

Podemos ir más lejos. Tenga en cuenta que el administrador de correo de Contoso Corporation debe tener acceso a los registros MX y TXT que se encuentran en la cúspide de la zona "contoso.com".  No necesita acceder a ningún otro registro MX ni TXT ni a registros de ningún otro tipo.  DNS de Azure le permite asignar permisos en el nivel de conjunto de registros exactamente a los registros que el administrador de correo tenga que acceder.  Al administrador de correo se le concede exactamente el control que necesita, no puede realizar otros cambios.

Los permisos RBAC de nivel de conjunto de registros pueden configurarse a través de Azure Portal, mediante el botón "Usuarios" de la hoja del conjunto de registros:

![RBAC de nivel de conjunto de registros a través de Azure Portal](./media/dns-protect-zones-recordsets/rbac3.png)

Los permisos RBAC de nivel de conjunto de registros también se pueden [conceder mediante Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

```powershell
# Grant permissions to a specific record set
New-AzureRmRoleAssignment -SignInName "<user email address>" -RoleDefinitionName "DNS Zone Contributor" -Scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

El comando equivalente también está [disponible a través de la CLI de Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md):

```azurecli
# Grant permissions to a specific record set
azure role assignment create --signInName "<user email address>" --roleName "DNS Zone Contributor" --scope "/subscriptions/<subscription id>/resourceGroups/<resource group name>/providers/Microsoft.Network/dnszones/<zone name>/<record type>/<record name>"
```

### <a name="custom-roles"></a>Roles personalizados

El rol "DNS Zone Contributor" (Colaborador de zona DNS) integrado permite el control total sobre un recurso DNS. También es posible crear roles de Azure de cliente propios, para proporcionar un control incluso más detallado.

Retomemos el ejemplo en el que se crea un registro CNAME en la zona "customers.contoso.com" para cada cuenta de cliente de Contoso Corporation.  La cuenta que se use para administrar estos registros CNAME solo debe tener permiso para administrar registros CNAME.  Es pues imposible modificar registros de otros tipos (como cambiar registros MX) o realizar operaciones de nivel de zona, como la eliminación de zonas.

En el ejemplo siguiente se muestra una definición de rol personalizado para administrar únicamente registros CNAME:

```json
{
    "Name": "DNS CNAME Contributor",
    "Id": "",
    "IsCustom": true,
    "Description": "Can manage DNS CNAME records only.",
    "Actions": [
        "Microsoft.Network/dnsZones/CNAME/*",
        "Microsoft.Network/dnsZones/read",
        "Microsoft.Authorization/*/read",
        "Microsoft.Insights/alertRules/*",
        "Microsoft.ResourceHealth/availabilityStatuses/read",
        "Microsoft.Resources/deployments/*",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.Support/*"
    ],
    "NotActions": [
    ],
    "AssignableScopes": [
        "/subscriptions/ c276fc76-9cd4-44c9-99a7-4fd71546436e"
    ]
}
```

La propiedad Actions define los siguientes permisos específicos de DNS:

* `Microsoft.Network/dnsZones/CNAME/*` concede control total sobre registros CNAME
* `Microsoft.Network/dnsZones/read` concede permiso para leer zonas DNS, pero no para modificarlas, lo que permite ver la zona en la que se crea el registro CNAME.

Las acciones restantes se copian desde el [Rol de colaborador de zona DNS integrado](../active-directory/role-based-access-built-in-roles.md#dns-zone-contributor).

> [!NOTE]
> El uso de un rol RBAC personalizado para evitar que se eliminen conjuntos de registros mientras se permite modificarlos no es un control eficaz. Evita que los conjuntos de registros se eliminen, pero no que se modifiquen.  Entre las modificaciones permitidas figuran la adición y eliminación de registros desde el conjunto de registros, incluida la eliminación de todos los registros para dejar un conjunto de registros "empty". Esto tiene el mismo efecto que eliminar el conjunto de registros desde un punto de vista de la resolución DNS.

Actualmente no pueden realizarse definiciones de roles personalizados a través de Azure Portal. Puede crearse un rol personalizado basado en esta definición de rol mediante Azure PowerShell:

```powershell
# Create new role definition based on input file
New-AzureRmRoleDefinition -InputFile <file path>
```

También puede crearse a través de la CLI de Azure:

```azurecli
# Create new role definition based on input file
azure role create -inputfile <file path>
```

El rol se puede asignar después del mismo modo que los roles integrados, tal cual se describe anteriormente en este artículo.

Para obtener más información sobre cómo crear, administrar y asignar roles personalizados, vea [Roles personalizados en RBAC de Azure](../active-directory/role-based-access-control-custom-roles.md).

## <a name="resource-locks"></a>Bloqueos de recursos

Además de RBAC, Azure Resource Manager admite otro tipo de control de seguridad, concretamente, la posibilidad de "bloquear" recursos. Mientras que las reglas de RBAC permiten controlar las acciones de usuarios y grupos específicos, los bloqueos de recursos se aplican a los recursos y son eficaces en todos los usuarios y roles. Para obtener más información, consulte [Bloqueo de recursos con el Administrador de recursos de Azure](../azure-resource-manager/resource-group-lock-resources.md).

Hay dos tipos de bloqueo de recurso: **DoNotDelete** y **ReadOnly**. Pueden aplicarse a una zona DNS o a un conjunto de registros individual.  En las secciones siguientes se describen varios escenarios comunes y cómo mantenerlos con bloqueos de recursos.

### <a name="protecting-against-all-changes"></a>Protección contra todos los cambios

Para evitar que se realicen cambios, aplique un bloqueo ReadOnly a la zona.  Esto impide que se creen otros conjuntos de registros y que se modifiquen o eliminen conjuntos de registros existentes.

Pueden crearse bloqueos de recursos de nivel de zona a través de Azure Portal.  En la hoja de zona DNS, haga clic en "Bloqueos" y luego en "Agregar":

![Bloqueos de recursos de nivel de zona a través de Azure Portal](./media/dns-protect-zones-recordsets/locks1.png)

También pueden crearse bloqueos de recursos de nivel de zona a través de Azure PowerShell:

```powershell
# Lock a DNS zone
New-AzureRmResourceLock -LockLevel <lock level> -LockName <lock name> -ResourceName <zone name> -ResourceType Microsoft.Network/DNSZones -ResourceGroupName <resource group name>
```

Actualmente no se admite la configuración de bloqueos de recursos de Azure a través de la CLI de Azure.

### <a name="protecting-individual-records"></a>Protección de registros individuales

Para evitar que se modifique un conjunto de registros de DNS existente, aplique un bloqueo ReadOnly al conjunto de registros.

> [!NOTE]
> La aplicación de un bloqueo DoNotDelete a un conjunto de registros no es un control eficaz. Evita que el conjunto de registros se elimine, pero no que se modifique.  Entre las modificaciones permitidas figuran la adición y eliminación de registros desde el conjunto de registros, incluida la eliminación de todos los registros para dejar un conjunto de registros "empty". Esto tiene el mismo efecto que eliminar el conjunto de registros desde un punto de vista de la resolución DNS.

Actualmente, los bloqueos de recursos de nivel de conjunto de recursos solo pueden configurarse mediante Azure PowerShell.  No se admiten en Azure Portal ni en la CLI de Azure.

```powershell
# Lock a DNS record set
New-AzureRmResourceLock -LockLevel <lock level> -LockName "<lock name>" -ResourceName "<zone name>/<record set name>" -ResourceType "Microsoft.Network/DNSZones/<record type>" -ResourceGroupName "<resource group name>"
```

### <a name="protecting-against-zone-deletion"></a>Protección contra la eliminación de zonas

Cuando se elimina una zona en DNS de Azure, se eliminan también todos los conjuntos de registros de la zona.  Esta operación no se puede deshacer.  La eliminación accidental de una zona crítica tiene la posibilidad de tener un significativo impacto de negocio.  Por consiguiente, es muy importante que se proteja contra la eliminación accidental de zonas.

La aplicación de un bloqueo DoNotDelete a una zona impide que se elimine la zona.  Pero, como los recursos secundarios heredan los bloqueos, también impide que se eliminen los conjuntos de registros de la zona, lo que puede no ser deseable.  Además, tal como se describe en la nota anterior, tampoco resulta eficaz puesto que todavía se pueden quitar registros de los conjuntos de registros existentes.

Como alternativa, considere la posibilidad de aplicar un bloqueo DoNotDelete a un conjunto de registros de la zona, como el conjunto de registros SOA.  Puesto que la zona no se puede eliminar sin eliminar también los conjuntos de registros, esto protege contra la eliminación de la zona, mientras sigue permitiendo que se modifiquen libremente los conjuntos de registros contenidos en la zona. Si intenta eliminar la zona, Azure Resource Manager detecta que esto también eliminaría el conjunto de registros SOA y bloquea la llamada porque la SOA está bloqueada.  No se elimina ningún conjunto de registros.

El comando de PowerShell siguiente crea un bloqueo DoNotDelete contra el registro SOA de la zona especificada:

```powershell
# Protect against zone delete with DoNotDelete lock on the record set
New-AzureRmResourceLock -LockLevel DoNotDelete -LockName "<lock name>" -ResourceName "<zone name>/@" -ResourceType" Microsoft.Network/DNSZones/SOA" -ResourceGroupName "<resource group name>"
```

Otra manera de evitar la eliminación accidental de zonas consiste en usar un rol personalizado para asegurarse de que las cuentas de operador y de servicio que se utilizan para administrar las zonas no dispongan de permisos de eliminación de zonas. Cuando tenga que eliminar una zona, puede aplicar una eliminación en dos pasos; de esta forma, se conceden permisos primero para eliminar la zona (en el ámbito de la zona, para evitar la eliminación de una zona incorrecta) y, a continuación, se elimina la zona.

Este segundo método tiene la ventaja de que funciona para todas las zonas a las que acceden esas cuentas y no obliga a acordarse de crear bloqueos. Tiene el inconveniente de que las cuentas con permisos de eliminación de zona, como la del propietario de la suscripción, pueden eliminar por error una zona crítica.

Es posible utilizar ambos enfoques (el bloqueo de recursos y los roles personalizados) al mismo tiempo, para abordar en profundidad la protección de zonas DNS.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo trabajar con RBAC, vea [Introducción a la administración de acceso en Azure Portal](../active-directory/role-based-access-control-what-is.md).
* Para más información sobre cómo trabajar con bloqueos de recursos, vea [Bloqueo de recursos con Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

