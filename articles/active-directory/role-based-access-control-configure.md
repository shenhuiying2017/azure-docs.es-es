<properties
	pageTitle="Control de acceso basado en roles de Azure Active Directory | Microsoft Azure"
	description="Introducción al control de acceso basado en roles de Azure en el Portal de Azure. Use las asignaciones de roles para asignar permisos en el directorio."
	services="active-directory"
	documentationCenter=""
	authors="kgremban"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="identity"
	ms.date="03/22/2016"
	ms.author="kgremban"/>

# Control de acceso basado en roles de Azure

## Control de acceso basado en roles
El control de acceso basado en roles (RBAC) de Azure permite realizar una administración detallada del acceso para Azure. Gracias a RBAC puede dividir las tareas entre el equipo de DevOps, y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. En este artículo se presentan los conceptos básicos de la administración de acceso que le ayudarán a utilizar RBAC en el Portal de Azure.

### Aspectos básicos de la administración de acceso en Azure
Cada suscripción de Azure está asociada a un directorio Azure Active Directory. Los usuarios, grupos y aplicaciones de ese directorio pueden administrar los recursos en la suscripción de Azure. Estos derechos de acceso se conceden con el uso del Portal de Azure, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

El acceso se concede al asignar el rol RBAC adecuado a usuarios, grupos y aplicaciones de un determinado ámbito. El ámbito de una asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso. Un rol asignado en un ámbito principal también concede acceso a los elementos secundarios dentro del mismo. Por ejemplo, un usuario con acceso a un grupo de recursos puede administrar todos los recursos que contiene, como sitios web, máquinas virtuales y subredes.

![Relación entre elementos de Azure Active Directory - diagrama](./media/role-based-access-control-configure/rbac_aad.png)

El rol RBAC que asigna a los usuarios, los grupos y las aplicaciones determina qué recursos se pueden administrar dentro de ese ámbito.

### Roles integrados
RBAC de Azure cuenta con tres roles básicos que se aplican a todos los tipos de recurso:

- El propietario tiene acceso completo a todos los recursos y cuenta con el derecho a delegar acceso a otros.
- El colaborador puede crear y administrar todos los tipos de recursos de Azure pero no puede conceder acceso a otros.
- El lector solo puede ver los recursos existentes de Azure.

El resto de los roles RBAC de Azure permiten la administración de recursos específicos de Azure. Por ejemplo, el rol de colaborador de máquina virtual permite al usuario crear y administrar máquinas virtuales, pero no le da acceso a la red virtual ni la subred a las que se conecta la máquina virtual.

[Roles RBAC integrados](role-based-access-built-in-roles.md) muestra los roles disponibles en Azure. Especifica las operaciones y el ámbito de cada rol integrado que se concede a los usuarios. Si quiere definir sus propios roles para tener un mayor control, consulte [Custom Roles in Azure RBAC](role-based-access-control-custom-roles.md) (Roles personalizados en RBAC de Azure).

### Jerarquía de recursos de Azure y herencia de acceso
- Cada suscripción de Azure pertenece a un único directorio.
- Cada grupo de recursos pertenece a una única suscripción.
- Cada recurso pertenece a un único grupo de recursos.

El acceso que se concede en el nivel principal se hereda en los ámbitos secundarios. Si se asigna el rol de lector a un grupo de Azure AD en el ámbito de suscripción, los miembros de dicho grupo podrán ver todos los grupos de recursos y todos los recursos de la suscripción. Si se asigna el rol de colaborador a una aplicación en el ámbito de grupo de recursos, podrá administrar los recursos de todos los tipos de ese grupo de recursos, pero no de otros grupos de recursos de la suscripción.

### RBAC de Azure frente a administradores de la suscripción clásica
Los administradores y coadministradores de la suscripción clásica tienen acceso completo a la suscripción de Azure. Pueden administrar los recursos mediante el [Portal de Azure](https://portal.azure.com), las API del Azure Resource Manager, o el [Portal de Azure clásico](https://manage.windowsazure.com) y con las API de la Administración de servicios de Azure. En el modelo RBAC, a los administradores clásicos se les asigna el rol de propietario en el ámbito de suscripción.

RBAC de Azure solo es compatible con el Portal de Azure y las nuevas API de Azure Resource Manager. Los usuarios y las aplicaciones a los que se asignan roles RBAC no pueden usar el portal de administración clásico ni las API de Administración de servicios de Azure.

### Autorización para administración frente a operaciones de datos
RBAC de Azure solo es compatible con operaciones de administración de los recursos de Azure del Portal de Azure y de las API de Azure Resource Manager. No todas las operaciones de nivel de datos para recursos de Azure se pueden autorizar mediante RBAC. Por ejemplo, la creación, la lectura, la actualización o la eliminación de cuentas de almacenamiento se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación de blobs o tablas de la cuenta de almacenamiento aún no se puede controlar mediante RBAC. Del mismo modo, la creación, la lectura, la actualización o la eliminación de una base de datos de SQL se puede controlar mediante RBAC; pero la creación, la lectura, la actualización o la eliminación tablas de base de datos de SQL aún no se puede controlar mediante RBAC.

## Administrar el acceso con el portal de Azure
### Vista de acceso
Puede ver quién tiene acceso a un recurso, a un grupo de recursos o a una suscripción desde la hoja principal del [Portal de Azure](https://portal.azure.com). Por ejemplo, queremos ver quién tiene acceso a uno de nuestros grupos de recursos:

1. Seleccione el icono de **Grupo de recursos** de la barra de navegación de la izquierda.
2. Seleccione el nombre del grupo de recursos que desea examinar en la hoja **Grupos de recursos**.
3. Seleccione el icono **Usuarios** en la parte superior derecha de la hoja del grupo de recursos.
4. La hoja **Usuarios** muestra todos los usuarios, los grupos y las aplicaciones a los que se ha concedido acceso al grupo de recursos.

![Hoja de usuarios: acceso heredado frente a asignado (captura de pantalla)](./media/role-based-access-control-configure/view-access.png)

Tenga en cuenta que algunos usuarios tienen un acceso **Asignado** mientras que otros lo tienen **Heredado**. El acceso se asigna específicamente en el grupo de recursos o se hereda de una asignación en la suscripción principal.

> [AZURE.NOTE] Los administradores y coadministradores de la suscripción clásica se consideran los propietarios de la suscripción en el nuevo modelo RBAC.


### Agregación de acceso
Puede conceder acceso desde el recurso, el grupo de recursos o la suscripción. Ese será el ámbito de la asignación de roles.

1. Seleccione el icono **Agregar** de la hoja **Usuarios**. ![Hoja Agregar acceso: seleccionar un rol (captura de pantalla)](./media/role-based-access-control-configure/grant-access1.png)
2. Seleccione el rol que desea asignar.
3. Seleccione el usuario, el grupo o la aplicación en el directorio al que desea conceder acceso. Puede buscar en el directorio con los nombres para mostrar, direcciones de correo electrónico e identificadores de objeto. ![Hoja Agregar usuarios: búsqueda (captura de pantalla)](./media/role-based-access-control-configure/grant-access2.png)

### Eliminación de acceso

1. En la hoja **Usuarios**, seleccione la asignación de rol que desea quitar.
2. Haga clic en el icono **Quitar** de la hoja de detalles de asignación.
3. Haga clic en **Sí** para confirmar la eliminación.

![Hoja de usuarios: quitar del rol (captura de pantalla)](./media/role-based-access-control-configure/remove-access1.png)

Las asignaciones heredadas no se pueden quitar de los ámbitos secundarios. Tendrá que quitar la asignación de roles del ámbito principal.

![Hoja de usuario: acceso heredado deshabilita el botón de eliminación (captura de pantalla)](./media/role-based-access-control-configure/remove-access2.png)

## Otras herramientas para administrar el acceso
Puede asignar roles y administrar el acceso con los comandos de RBAC de Azure en otras herramientas que no sean el Portal de Azure. Siga los vínculos para obtener más información acerca de los requisitos previos y empezar a trabajar con los comandos de RBAC de Azure.

- [Azure PowerShell](role-based-access-control-manage-access-powershell.md)
- [Interfaz de la línea de comandos de Azure](role-based-access-control-manage-access-azure-cli.md)
- [API DE REST](role-based-access-control-manage-access-rest.md)

## Pasos siguientes
- [Creación de un informe del historial de cambios de acceso](role-based-access-control-access-change-history-report.md)
- Consulte [Roles integrados en Azure RBAC](role-based-access-built-in-roles.md)
- Defina sus propios roles personalizados en [Custom Roles in Azure RBAC](role-based-access-control-custom-roles.md) (Roles personalizados en RBAC de Azure)

<!---HONumber=AcomDC_0330_2016-->