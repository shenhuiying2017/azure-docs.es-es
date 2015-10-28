<properties
	pageTitle="Control de acceso basado en roles de Azure Active Directory | Microsoft Azure"
	description="En este artículo se describe el control de acceso basado en roles de Azure."
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

# Control de acceso basado en roles de Azure Active Directory

## Control de acceso basado en roles
El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Gracias a RBAC, puede segregar las tareas entre el equipo de desarrolladores y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajos.

### Aspectos básicos de la administración de acceso en Azure
Cada una de las suscripciones de Azure está asociada a un Azure Active Directory. Solo se puede conceder acceso a los usuarios, los grupos y las aplicaciones de ese directorio para administrar los recursos en la suscripción de Azure, mediante el Portal de administración de Azure, las herramientas de línea de comandos de Azure y las API de administración de Azure.

El acceso se concede mediante la asignación de rol RBAC adecuado a los usuarios, grupos y aplicaciones en el ámbito correcto. Para conceder acceso a toda la suscripción, asigne un rol en el ámbito de la suscripción. Para conceder acceso a un grupo de recursos específico dentro de una suscripción, asigne un rol en el ámbito de grupo de recursos. También puede asignar roles a recursos específicos, como sitios web, máquinas virtuales y subredes para conceder acceso a un único recurso.

![](./media/role-based-access-control-configure/overview.png)

El rol RBAC que asigna a los usuarios, los grupos y las aplicaciones determina qué recursos puede administrar el usuario (o la aplicación) dentro de ese ámbito.

### Roles integrados en Azure RBAC
Azure RBAC cuenta con tres roles básicos que se aplican a todos los tipos de recurso: propietario, colaborador y lector. El propietario tiene acceso completo a todos los recursos y cuenta con el derecho a delegar acceso a otros. El colaborador puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros. El lector solo puede ver los recursos existentes de Azure. El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite crear y administrar máquinas virtuales, pero no permite administrar la red virtual ni la subred a las que se conecta la máquina virtual.

[Roles RBAC integrados](role-based-access-built-in-roles.md) muestra los roles RBAC integrados disponibles en Azure. En cada rol se especifican las operaciones a las que el rol integrado da acceso.

### Jerarquía de recursos de Azure y herencia de acceso
Cada suscripción de Azure pertenece a un único directorio, cada grupo de recursos pertenece a una única suscripción y cada recursos pertenece a un único grupo de recursos. El acceso que se concede en el nivel principal se hereda en los ámbitos secundarios. Si se concede el rol de lector a un grupo de Azure AD en el ámbito de suscripción, los miembros de dicho grupo podrán ver todos los grupos de recursos y todos los recursos de la suscripción. Si se concede el rol de colaborador a una aplicación en el ámbito de grupo de recursos, podrá administrar los recursos de todos los tipos de ese grupo de recursos, pero no de otros grupos de recursos de la suscripción.

### Azure RBAC frente al administrador y los coadministradores de la suscripción clásica
El administrador y los coadministradores de la suscripción clásica tienen acceso competo a la suscripción de Azure. Pueden administrar recursos tanto desde el portal clásico (https://manage.windowsazure.com) y las API del Administrador de servicios de Azure, como desde el nuevo portal de administración (https://portal.azure.com) y las nuevas API del Administrador de recursos de Azure. En el modelo RBAC, a los administradores clásicos se les asigna el rol de propietario en el ámbito de suscripción.

El modelo de autorización detallado (Azure RBAC) solo es compatible con el nuevo portal de administración (https://portal.azure.com) y con las API del Administrador de recursos de Azure. Los usuarios y las aplicaciones a los que se asignan roles RBAC (en el ámbito de suscripción/grupo de recursos/ámbito de recurso) no pueden usar el portal de administración clásico (http://manage.windowsazure.com) ni las API de Administración de servicios de Azure.

### Autorización para administración frente a operaciones de datos
El modelo de autorización detallado (Azure RBAC) solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y las API de Administrador de recursos de Azure. No todas las operaciones de nivel de datos para recursos de Azure se pueden autorizar mediante RBAC. Por ejemplo, la creación, la lectura, la actualización o la eliminación de cuentas de almacenamiento se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación de blobs o tablas de la cuenta de almacenamiento aún no se puede controlar mediante RBAC. Del mismo modo, la creación, la lectura, la actualización o la eliminación de una base de datos de SQL se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación tablas de base de datos de SQL aún no se puede controlar mediante RBAC.

## Administración del acceso con el Portal de administración de Azure
### Consulta de acceso
Seleccione la configuración de acceso en la sección Essentials de la hoja de grupo de recursos. La hoja **usuarios** muestra todos los usuarios, los grupos y las aplicaciones a los que se ha concedido acceso al grupo de recursos. El acceso se asigna en el grupo de recursos o se hereda de una asignación en la suscripción principal.

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE]Los administradores y coadministradores de la suscripción clásica son propietarios de la suscripción en el nuevo modelo RBAC.

### Agregación de acceso
1. Haga clic en el icono **Agregar** de la hoja **Usuarios**. ![](./media/role-based-access-control-configure/grant-access1.png)
2. Seleccione el rol que desea asignar.
3. Busque y seleccione el usuario, el grupo o la aplicación al que desea conceder acceso.
4. Para buscar los usuarios, los grupos y las aplicaciones en el directorio, use los nombres para mostrar, direcciones de correo electrónico e identificadores de objeto.![](./media/role-based-access-control-configure/grant-access2.png)

### Eliminación de acceso
1. En la hoja **Usuarios**, seleccione la asignación de rol que desea quitar.
2. Haga clic en el icono **Quitar** de la hoja de detalles de asignación.
3. Haga clic en **Sí** para confirmar la eliminación.

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE]Las asignaciones heredadas no se pueden quitar de los ámbitos secundarios. Navegue al ámbito principal y quite allí las asignaciones.


![](./media/role-based-access-control-configure/remove-access2.png)

## Administración del acceso con Azure Powershell
El acceso se puede administrar mediante el uso de comandos de Azure RBAC en las herramientas de Azure PowerShell.

-	Use `Get-AzureRoleDefinition` para mostrar los roles RBAC disponibles para asignación y para inspeccionar las operaciones a las que conceden acceso.

-	Use `Get-AzureRoleAssignment` para mostrar las asignaciones de acceso RBAC vigentes en la suscripción, el grupo de recursos o el recurso especificado. Use el parámetro `ExpandPrincipalGroups` para mostrar las asignaciones de acceso al usuario especificado, así como a los grupos de los que el usuario es miembro. Use el parámetro `IncludeClassicAdministrators` para mostrar también el administrador y los coadministradores de la suscripción clásica.

-	Use `New-AzureRoleAssignment` para conceder acceso a los usuarios, los grupos y las aplicaciones.

-	Use `Remove-AzureRoleAssignment` para quitar el acceso.

Consulte [Administración de acceso con Azure PowerShell](role-based-access-control-manage-access-powershell.md) para ver ejemplos más detallados de la administración de acceso con Azure PowerShell.

## Administración del acceso con la interfaz de la línea de comandos de Azure
El acceso se puede administrar mediante el uso de comandos de Azure RBAC en la interfaz de la línea de comandos de Azure.

-	Use `azure role list` para mostrar los roles RBAC disponibles para asignación. Use azure role show para inspeccionar las operaciones a las que conceden acceso.

-	Use `azure role assignment list` para mostrar las asignaciones de acceso RBAC vigentes en la suscripción, el grupo de recursos o el recurso especificado. Use la opción `expandPrincipalGroups` para mostrar las asignaciones de acceso al usuario especificado, así como a los grupos de los que el usuario es miembro. Use el parámetro `includeClassicAdministrators` para mostrar también el administrador y los coadministradores de la suscripción clásica.

-	Use `azure role assignment create` para conceder acceso a los usuarios, los grupos y las aplicaciones.

-	Use `azure role assignment delete` para quitar el acceso.

Consulte [Administración de acceso con la CLI de Azure](role-based-access-control-manage-access-azure-cli.md) para ver ejemplos más detallados de la administración de acceso con la CLI de Azure.

## Uso del informe de historial de cambios de acceso
Todos los cambios de acceso que tienen lugar en las suscripciones de Azure se registran en eventos de Azure.

### Creación de un informe con Azure PowerShell
Para crear un informe sobre quién concedió/revocó qué tipo de acceso a quién en qué ámbito de las suscripciones de Azure, use el siguiente comando de PowerShell:

    Get-AzureAuthorizationChangeLog

### Creación de un informe con la CLI de Azure
Para crear un informe sobre quién concedió/revocó qué tipo de acceso a quién en qué ámbito de las suscripciones de Azure, use el siguiente comando de la interfaz de la línea de comandos (CLI) de Azure:

    azure authorization changelog

> [AZURE.NOTE]Se pueden consultar los cambios de acceso de los últimos 90 días (en lotes de 15 días).

En el siguiente ejemplo se muestran todos los cambios de acceso de la suscripción que tuvieron lugar en los últimos 7 días.

![](./media/role-based-access-control-configure/access-change-history.png)

### Exportación de cambios de acceso a una hoja de cálculo
Es conveniente exportar los cambios de acceso a una hoja de cálculo para su revisión.

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

<!---HONumber=Oct15_HO3-->