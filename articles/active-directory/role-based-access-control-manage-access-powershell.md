<properties
	pageTitle="Administración del control de acceso basado en rol (RBAC) con Azure PowerShell | Microsoft Azure"
	description="Cómo administrar RBAC con Azure PowerShell, incluida la enumeración y asignación de roles y la eliminación de asignaciones de roles."
	services="active-directory"
	documentationCenter=""
	authors="IHenkel"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="10/12/2015"
	ms.author="inhenk"/>

# Administración del control de acceso basado en rol (RBAC) con Azure PowerShell
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Lista de roles RBAC
### Lista de todos los roles disponibles
Para enumerar los roles RBAC disponibles para asignación y para inspeccionar las operaciones a las que conceden acceso, use:

		Get-AzureRoleDefinition

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition1.png)

### Lista de las acciones de un rol
Para enumerar las acciones de un rol específico, use:

    Get-AzureRoleDefinition <role name>

![](./media/role-based-access-control-manage-access-powershell/1-get-azure-rm-role-definition2.png)

## Lista de acceso
### Lista de todas las asignaciones de rol en la suscripción seleccionada
Para enumerar las asignaciones de acceso RBAC vigentes en la suscripción, el recurso o el grupo de recursos especificado, use:

    Get-AzureRoleAssignment

###	Lista de las asignaciones de rol vigentes en un grupo de recursos
Para enumerar las asignaciones de acceso para un grupo de recursos, use:

    Get-AzureRoleAssignment -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment1.png)

### Lista de asignaciones de rol para un usuario, incluidas las asignadas a grupos de usuarios
Para enumerar las asignaciones de acceso para el usuario especificado, así como para los grupos de los que el usuario es miembro, use:

    Get-AzureRoleAssignment -ExpandPrincipalGroups

![](./media/role-based-access-control-manage-access-powershell/4-get-azure-rm-role-assignment2.png)

### Lista de asignaciones de roles de administrador y coadministrador de servicio clásico
Para enumerar las asignaciones de acceso para el administrador y los coadministradores de suscripción clásica, use:

    Get-AzureRoleAssignment -IncludeClassicAdministrators

## Conceder acceso
### Búsqueda de Id. de objetos
Para usar las siguientes secuencias de comandos, primero debe encontrar los Id. de los objetos. Se presume que ya conoce el Id. de la suscripción con la que trabaja; si no es así, consulte [Get-AzureSubscription](https://msdn.microsoft.com/library/dn495302.aspx) en MSDN.

#### Encuentre el Id. del objeto de un grupo de Azure AD
Para encontrar el Id. del objeto de un grupo de Azure AD, use:

    Get-AzureADGroup -SearchString <group name in quotes>

#### Encuentre el Id. del objeto de una entidad de servicio de Azure AD
Para obtener el Id. del objeto de una entidad de servicio de Azure AD, use: Get-AzureADServicePrincipal -SearchString <service name in quotes>

### Asignación de un rol a grupo en el ámbito de la suscripción
Para conceder acceso a un grupo en el ámbito de la suscripción, use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment1.png)

### Asignación de un rol a aplicación en el ámbito de la suscripción
Para conceder acceso a una aplicación en el ámbito de la suscripción, use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment2.png)

### Asignación de un rol a usuario en el ámbito del grupo de recursos
Para conceder acceso a un usuario en el ámbito del grupo de recursos, use:

    New-AzureRoleAssignment -SignInName <email of user> -RoleDefinitionName <role name in quotes> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment3.png)

### Asignación de un rol a grupo en el ámbito del recurso
Para conceder acceso a un grupo en el ámbito del recurso, use:

    New-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name in quotes> -ResourceName <resource name> -ResourceType <resource type> -ParentResource <parent resource> -ResourceGroupName <resource group name>

![](./media/role-based-access-control-manage-access-powershell/2-new-azure-rm-role-assignment4.png)

## Quitar acceso
Para quitar el acceso de usuarios, grupos y aplicaciones, use:

    Remove-AzureRoleAssignment -ObjId <object id> -RoleDefinitionName <role name> -Scope <scope such as subscription/subscription id>

![](./media/role-based-access-control-manage-access-powershell/3-remove-azure-rm-role-assignment.png)

## Temas de RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->