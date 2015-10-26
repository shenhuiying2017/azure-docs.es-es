<properties
	pageTitle="Administración del control de acceso basado en rol (RBAC) con la CLI de Azure | Microsoft Azure"
	description="Aprenda a administrar el acceso basado en rol (RBAC) con la interfaz de la línea de comandos de Azure a través de la enumeración de roles y acciones de roles, la asignación de roles a los ámbitos de aplicación y suscripción."
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

# Administración del control de acceso basado en rol (RBAC) con la interfaz de la línea de comandos (CLI) de Azure
<!-- Azure Selector -->
> [AZURE.SELECTOR]
- [PowerShell](role-based-access-control-manage-access-powershell.md)
- [Azure CLI](role-based-access-control-manage-access-azure-cli.md)

## Lista de roles RBAC
###	Lista de todos los roles disponibles
Para enumerar todos los roles disponibles, use:

		azure role list

El ejemplo siguiente muestra la lista de *todos los roles disponibles*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-list.png)

###	Lista de las acciones de un rol
Para enumerar las acciones de un rol, use:

    azure role show <role in quotes>

El ejemplo siguiente muestra las acciones del rol *Colaborador* y del rol *Colaborador de la máquina virtual*.

![](./media/role-based-access-control-manage-access-azure-cli/1-azure-role-show.png)

##	Lista de acceso
###	Lista de las asignaciones de rol vigentes en un grupo de recursos
Para enumerar las asignaciones de rol vigentes en un grupo de recursos, use:

    azure role assignment list --resource-group <resource group name>

El ejemplo siguiente muestra las asignaciones de rol vigentes en el grupo *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-1.png)

###	Lista de asignaciones de rol para un usuario, incluidas las asignadas a los grupos de un usuario

El ejemplo siguiente muestra las asignaciones de rol vigentes en el grupo *pharma-sales-projecforcast*.

![](./media/role-based-access-control-manage-access-azure-cli/4-azure-role-assignment-list-2.png)

##	Conceder acceso
Cuando haya identificado el rol que desea asignar, para conceder acceso, use:

    azure role assignment create

###	Asignación de un rol a grupo en el ámbito de la suscripción
Para asignar un rol a un grupo en el ámbito de la suscripción, use:

   azure role assignment create --objId <Id. de objeto del grupo> --role <name of role> --scope <suscripción/Id. de suscripción>

En el ejemplo siguiente se asigna el rol *Lector* al *equipo de Christine Koch* en el ámbito de la *suscripción*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-1.png)

###	Asignación de un rol a aplicación en el ámbito de la suscripción
Para asignar un rol a una aplicación en el ámbito de la suscripción, use:

    azure role assignment create --objId  <applications's object id> --role <name of role> --scope <subscription/subscription id>

En el ejemplo siguiente se concede el rol *Colaborador* a una aplicación de *Azure AD* en la suscripción seleccionada.

 ![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-2.png)

###	Asignación de un rol a usuario en el ámbito del grupo de recursos
Para asignar un rol a un usuario en el ámbito del grupo de recursos, use:

    azure role assignment create --signInName  <user's email address> --roleName <name of role in quotes> --resourceGroup <resource group name>

En el ejemplo siguiente se concede el rol *Colaborador de la máquina virtual* al usuario **samert@aaddemo.com* en el ámbito del grupo de recursos *Pharma-Sales-ProjectForcast*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-3.png)

###	Asignación de un rol a grupo en el ámbito del recurso
Para asignar un rol a un grupo en el ámbito del recurso, use:

    azure role assignment create --objId  <group id> --roleName <name of role in quotes> --resource-name <resource group name> --resource-type <resource group type> --parent <resource group parent> --resource-group <resource group>

El ejemplo siguiente concede el rol *Colaborador de la máquina virtual* a un grupo de *Azure AD* en una *subred*.

![](./media/role-based-access-control-manage-access-azure-cli/2-azure-role-assignment-create-4.png)

##	Quitar acceso
Para quitar una asignación de rol, use:

    azure role assignment delete --objId <object id to from which to remove role> --roleName <role name>

En el ejemplo siguiente se quita la asignación de rol *Colaborador de la máquina virtual* de **sammert@aaddemo.com* en el grupo de recursos *Pharma-Sales-ProjectForcast*. Luego, quita la asignación de rol de un grupo de la suscripción.

![](./media/role-based-access-control-manage-access-azure-cli/3-azure-role-assignment-delete.png)

## Temas de RBAC
[AZURE.INCLUDE [role-based-access-control-toc.md](../../includes/role-based-access-control-toc.md)]

<!---HONumber=Oct15_HO3-->