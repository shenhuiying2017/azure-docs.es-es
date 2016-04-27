<properties
	pageTitle="Administración del control de acceso basado en rol (RBAC) con Azure PowerShell | Microsoft Azure"
	description="Cómo administrar RBAC con Azure PowerShell, incluida la enumeración y asignación de roles, y la eliminación de asignaciones de roles."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="04/12/2016"
	ms.author="kgremban"/>

# Administración del control de acceso basado en rol con Azure PowerShell

> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [CLI de Azure](role-based-access-control-manage-access-azure-cli.md)
- [API DE REST](role-based-access-control-manage-access-rest.md)


El control de acceso basado en roles (RBAC) del Portal de Azure y la API del Administrador de recursos de Azure le permite administrar el acceso a su suscripción en un nivel específico. Con esta característica, puede conceder acceso a usuarios, grupos o entidades de seguridad de servicio de Active Directory asignándoles roles en un ámbito determinado.

Para poder usar Windows PowerShell con el fin de administrar RBAC, necesita lo siguiente:

- La versión 0.8.8 o posterior de Azure PowerShell. Para instalar la última versión y asociarla a la suscripción de Azure, consulte [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md).

- Cmdlets de Azure Resource Manager. Instale los [cmdlets de Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx) en PowerShell.

## Lista de roles

### Lista de todos los roles disponibles
Para enumerar los roles RBAC disponibles para asignación y para inspeccionar las operaciones a las que conceden acceso, use:

		Get-AzureRmRoleDefinition

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Lista de las acciones de un rol
Para enumerar las acciones de un rol específico, use:

    Get-AzureRmRoleDefinition <role name>

![RBAC PowerShell - Get-AzureRmRoleDefinition para un rol específico - captura de pantalla](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Consulta de quién ha accedido
Para mostrar las asignaciones de acceso RBAC, use lo siguiente:

    Get-AzureRmRoleAssignment

###	Lista de asignaciones de roles en un ámbito específico
Puede ver todas las asignaciones de acceso de una suscripción, un grupo de recursos o un recurso determinados. Por ejemplo, para ver todas las asignaciones activas de un grupo de recursos, use lo siguiente:

    Get-AzureRmRoleAssignment -ResourceGroupName <resource group name>

![RBAC PowerShell - Get-AzureRmRoleAssignment para un grupo de recursos - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Lista de roles asignados a un usuario
Para obtener una lista de todos los roles asignados a un usuario determinado, incluidos los roles asignados a los grupos que pertenece, use lo siguiente:

    Get-AzureRmRoleAssignment -SignInName <User email> -ExpandPrincipalGroups

![RBAC PowerShell - Get-AzureRmRoleAssignment para un usuario - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Lista de asignaciones de roles de administrador y coadministrador de servicio clásico
Para enumerar las asignaciones de acceso para el administrador y los coadministradores de suscripción clásica, use:

    Get-AzureRmRoleAssignment -IncludeClassicAdministrators

## Conceder acceso
### Búsqueda de identificadores de objetos
Para asignar un rol, debe identificar el objeto (usuario, grupo o aplicación) y el ámbito.

Si no conoce el id. de suscripción, puede encontrarlo en la hoja **Suscripciones** del Portal de Azure. También puede saber cómo obtenerlo mediante una consulta con [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) en MSDN.

Para obtener el identificador del objeto de un grupo de Azure AD, use:

    Get-AzureRmADGroup -SearchString <group name in quotes>

Para obtener el id. de objeto de una entidad principal de servicio de Azure AD, o aplicación, use lo siguiente:

    Get-AzureRmADServicePrincipal -SearchString <service name in quotes>

### Asignación de un rol a aplicación en el ámbito de la suscripción
Para conceder acceso a una aplicación en el ámbito de la suscripción, use:

    New-AzureRmRoleAssignment -ObjectId <application id> -RoleDefinitionName <role name in quotes> -Scope <subscription id>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Asignación de un rol a usuario en el ámbito del grupo de recursos
Para conceder acceso a un usuario en el ámbito del grupo de recursos, use:

    New-AzureRmRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Asignación de un rol a grupo en el ámbito del recurso
Para conceder acceso a un grupo en el ámbito del recurso, use:

    New-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![RBAC PowerShell - New-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Quitar acceso
Para quitar el acceso de usuarios, grupos y aplicaciones, use:

    Remove-AzureRmRoleAssignment -ObjectId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription id>

![RBAC PowerShell - Remove-AzureRmRoleAssignment - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Creación de un rol personalizado
Para crear un rol personalizado, use el comando `New-AzureRmRoleDefinition`.

En el ejemplo siguiente se crea un rol personalizado denominado *Operador de máquina virtual* que concede acceso a todas las operaciones de lectura de los proveedores de recursos *Microsoft.Compute*, *Microsoft.Storage* y *Microsoft.Network* y concede acceso para iniciar, reiniciar y supervisar las máquinas virtuales. El rol personalizado se puede usar en dos suscripciones.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/2-new-azurermroledefinition.png)

## Modificación de un rol personalizado
Para modificar un rol personalizado, use en primer lugar el comando `Get-AzureRmRoleDefinition` para recuperar la definición de rol. Seguidamente, realice los cambios que quiera en la definición del rol. Por último, use el comando `Set-AzureRmRoleDefinition` para guardar la definición de rol modificada.

En el ejemplo siguiente se agrega la operación `Microsoft.Insights/diagnosticSettings/*` al rol personalizado *Operador de máquina virtual*.

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-1.png)

En el ejemplo siguiente se agrega una suscripción de Azure a los ámbitos asignables del rol personalizado Operador de máquina virtual.

![RBAC PowerShell - Set-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/3-set-azurermroledefinition-2.png)

## Eliminación de un rol personalizado

Para eliminar un rol personalizado, use el comando `Remove-AzureRmRoleDefinition`.

En el ejemplo siguiente se quita el rol personalizado *Operador de máquina virtual*.

![RBAC PowerShell - Remove-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/4-remove-azurermroledefinition.png)

## Lista de roles personalizados
Para enumerar los roles que están disponibles para la asignación en un ámbito, use el comando `Get-AzureRmRoleDefinition`.

En el ejemplo siguiente se enumeran todos los roles disponibles para la asignación en la suscripción seleccionada.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition-1.png)

En el ejemplo siguiente, el rol personalizado *Operador de máquina virtual* no está disponible en la suscripción *Production4* debido a que la suscripción no se encuentra entre los **AssignableScopes** del rol.

![RBAC PowerShell - Get-AzureRmRoleDefinition - captura de pantalla](./media/role-based-access-control-manage-access-powershell/5-get-azurermroledefinition2.png)

## Consulte también
- [Uso de Azure PowerShell con Azure Resource Manager](../powershell-azure-resource-manager.md)[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=AcomDC_0413_2016-->