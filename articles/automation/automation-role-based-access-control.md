<properties 
   pageTitle="Control de acceso basado en rol en Automatización de Azure | Microsoft Azure"
   description="El control de acceso basado en rol (RBAC) permite la administración del acceso en los recursos de Azure. En este artículo se describe cómo configurar RBAC en Automatización de Azure."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn"
   keywords="automatización de rbac, control de acceso basado en roles, rbac de azure" />
<tags 
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/12/2016"
   ms.author="magoedte;sngun"/>

# Control de acceso basado en rol en Automatización de Azure

## Control de acceso basado en rol

El control de acceso basado en rol (RBAC) permite la administración del acceso en los recursos de Azure. Con [RBAC](../active-directory/role-based-access-control-configure.md), se puede separar los deberes del equipo y conceder a los usuarios, grupos y aplicaciones únicamente el acceso que necesiten para su trabajo. El acceso basado en rol se puede conceder a los usuarios que utilicen el Portal de Azure, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

## RBAC en cuentas de Automatización de Azure

En Automatización de Azure, el acceso se concede mediante la asignación de rol de RBAC adecuado a los usuarios, grupos y aplicaciones en el ámbito de las cuentas de Automatización. Estos son los roles integrados compatibles que admiten las cuentas de Automatización:

|**Rol** | **Descripción** |
|:--- |:---|
| Propietario | El rol Propietario permite el acceso a todos los recursos y las acciones de una cuenta de Automatización, lo que incluye proporcionar acceso a otros usuarios, grupos y aplicaciones para que administren la cuenta de Automatización. |
| Colaborador | El rol Colaborador permite administrar todo, excepto la modificación de los permisos de acceso de otros usuarios a una cuenta de Automatización. |
| Lector | El rol Lector permite ver todos los recursos de una cuenta de Automatización, pero no realizar cambios.|
| Operador de automatización | El rol Operador de Automatización permite realizar tareas operativas, como iniciar, detener, suspender, reanudar y programar trabajos. Este rol es útil si se desea proteger los recursos de la Cuenta de Automatización, como credenciales, activos y Runbooks, para que no puedan verse ni modificarse, pero permitir a los miembros de la organización ejecutar los Runbooks. |
| Administrador de acceso de usuario | El rol Administrador de acceso de usuario permite administrar el acceso de los usuarios a las cuentas de Automatización de Azure. |

>[AZURE.NOTE] No puede conceder derechos de acceso a uno o más Runbooks específicos, solo a los recursos y las acciones dentro de la cuenta de Automatización.

En este artículo se explicará cómo configurar RBAC en Automatización de Azure. Pero primero, echemos un vistazo a los permisos individuales concedidos a los roles Colaborador, Lector, Operador de Automatización y Administrador de acceso de usuario, para comprenderlos bien antes de conceder a alguien derechos a la cuenta de Automatización. De lo contrario, se podrían producir consecuencias imprevistas o no deseadas.

## Permisos del rol Colaborador

En la siguiente tabla se presentan las acciones específicas que puede realizar el rol Colaborador en Automatización.

| **Tipo de recurso** | **Lectura** | **Escritura** | **Eliminar** | **Otras acciones** |
|:--- |:---|:--- |:---|:--- |
| Cuenta de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | 
| Recurso de certificado de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de conexión de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | 
| Recurso de tipo de conexión de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | 
| Recurso de credencial de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de programación de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Recurso de variable de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Configuración del estado deseado de Automatización | | | | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) |
| Tipo de recurso de Hybrid Runbook Worker | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | 
| Trabajo de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | 
| Transmisión de trabajo de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Programación de trabajos de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Módulo de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | |
| Runbook de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) |
| Borrador de Runbook de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) |
| Trabajo de prueba del borrador de Runbook de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | 
| Webhook de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) |

## Permisos del rol Lector

En la siguiente tabla se presentan las acciones específicas que puede realizar el rol Lector en Automatización.

| **Tipo de recurso** | **Lectura** | **Escritura** | **Eliminar** | **Otras acciones** |
|:--- |:---|:--- |:---|:--- |
| Administrador de suscripciones clásicas | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Bloqueo de administración | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Permiso | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Operaciones del proveedor | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Asignación de roles | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Definición de roles | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | 

## Permisos del rol Operador de Automatización

En la siguiente tabla se presentan las acciones específicas que puede realizar el rol Operador de Automatización en Automatización.

| **Tipo de recurso** | **Lectura** | **Escritura** | **Eliminar** | **Otras acciones** |
|:--- |:---|:--- |:---|:--- |
| Cuenta de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | 
| Recurso de certificado de automatización | | | |
| Recurso de conexión de automatización | | | |
| Recurso de tipo de conexión de Automatización | | | |
| Recurso de credencial de automatización | | | |
| Recurso de programación de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Recurso de variable de automatización | | | |
| Configuración del estado deseado de Automatización | | | | |
| Tipo de recurso de Hybrid Runbook Worker | | | | | 
| Trabajo de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | 
| Transmisión de trabajo de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | |  
| Programación de trabajos de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | |
| Módulo de Automatización | | | |
| Runbook de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Borrador de Runbook de Automatización | | | |
| Trabajo de prueba del borrador de Runbook de Automatización | | | |  
| Webhook de Automatización | | | |

Para más detalles, en la tabla de [acciones del Operador de Automatización](../active-directory/role-based-access-built-in-roles.md#automation-operator), se enumeran las acciones que admite el rol Operador de automatización en la cuenta de Automatización y sus recursos.

## Permisos del rol Administrador de acceso de usuario

En la siguiente tabla se presentan las acciones específicas que puede realizar el rol Administrador de acceso de usuario en Automatización.

| **Tipo de recurso** | **Lectura** | **Escritura** | **Eliminar** | **Otras acciones** |
|:--- |:---|:--- |:---|:--- |
| Cuenta de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de certificado de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de conexión de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de tipo de conexión de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de credencial de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de programación de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Recurso de variable de automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Configuración del estado deseado de Automatización | | | | |
| Tipo de recurso de Hybrid Runbook Worker | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Trabajo de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Transmisión de trabajo de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Programación de trabajos de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Módulo de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Runbook de Automatización de Azure | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Borrador de Runbook de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | |
| Trabajo de prueba del borrador de Runbook de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | | | 
| Webhook de Automatización | ![Estado verde](media/automation-role-based-access-control/green-checkmark.png) | | |

## Configuración de RBAC para una Cuenta de Automatización mediante el Portal de Azure

1.	Inicie sesión en el [Portal de Azure](https://portal.azure.com/) y abra su cuenta de Automatización en la hoja Cuentas de Automatización.

2.	Haga clic en el control **Acceso** de la esquina superior derecha. Se abre la hoja **Usuarios**, donde puede agregar nuevos usuarios, grupos y aplicaciones para administrar la cuenta de Automatización y ver los roles existentes que se pueden configurar para la cuenta de Automatización.

    ![Botón de acceso](media/automation-role-based-access-control/automation-01-access-button.png)

>[AZURE.NOTE] Los **administradores de suscripciones** ya existen como usuario predeterminado. El grupo de administradores de suscripciones de Active Directory incluye los administradores y coadministradores de servicios de su suscripción de Azure. El administrador del servicio es el propietario de la suscripción de Azure y sus recursos, y también heredará el rol de propietario de las cuentas de Automation. Esto significa que el acceso es **Heredado** en el caso de los **administradores y coadministradores de servicios** de una suscripción, mientras que es **Asignado** en el caso de los demás usuarios. Haga clic en **Administradores de suscripciones** para ver más detalles sobre sus permisos.

### Adición de usuarios nuevos y asignación de roles

1.	En la hoja Usuarios, haga clic en **Agregar** para abrir la **hoja Agregar acceso**, donde puede agregar un usuario, un grupo o una aplicación y asignarles un rol.

    ![Agregar usuario](media/automation-role-based-access-control/automation-02-add-user.png)

2.	Seleccione en rol en la lista de roles disponibles. Aquí se elegirá el rol **Lector**, pero es posible elegir cualquiera de los roles integrados disponibles que admitan las cuentas de Automatización o cualquier rol personalizado que haya definido.

    ![Seleccionar rol](media/automation-role-based-access-control/automation-03-select-role.png)

3.	Haga clic en **Agregar usuarios** para abrir la hoja **Agregar usuarios**. Si ha agregado los usuarios, grupos o aplicaciones para administrar su suscripción, se enumerarán dichos usuarios y podrá seleccionarlos para agregar acceso. Si no hay usuarios en la lista o si el usuario que quiere agregar no aparece en ella, haga clic en **Invitar** para abrir la hoja **Invitar a un invitado**, donde puede invitar a un usuario con una dirección de correo electrónico válida de la cuenta Microsoft, como Outlook.com, OneDrive o Xbox Live ID. Una vez que haya escrito la dirección de correo electrónico del usuario, haga clic en **Seleccionar** para agregarlo y en **Aceptar**.

    ![Agregar usuarios](media/automation-role-based-access-control/automation-04-add-users.png)
 
    El usuario debería aparecer agregado en la hoja **Usuarios** con el rol **Lector** asignado.

    ![Enumerar usuarios](media/automation-role-based-access-control/automation-05-list-users.png)

    También se puede asignar un rol al usuario en la hoja **Roles**.

1. Haga clic en **Roles** en la hoja Usuarios para abrir la **hoja Roles**. En dicha hoja, puede ver el nombre del rol, el número de usuarios y los grupos asignados a dicho rol.

    ![Asignar rol en la hoja de usuarios](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)
   
    >[AZURE.NOTE] El control de acceso basado en roles solo se puede establecer en el nivel de Cuenta de Automatización, no en los recursos que estén debajo de Cuenta de Automatización.

    Es posible asignar más de un rol a un usuario, grupo o aplicación. Por ejemplo, si se agrega el rol **Operador de Automatización** junto con el **rol Lector** al usuario, este podrá ver todos los recursos de Automatización y ejecutar los trabajos de Runbook. Puede expandir la lista desplegable para ver una lista de los roles asignados al usuario.

    ![Ver varios roles](media/automation-role-based-access-control/automation-07-view-multiple-roles.png)
 
### Eliminación de un usuario

Puede quitar el permiso de usuario de cualquier usuario que no administre la Cuenta de Automatización o que haya dejado de trabajar en la organización. Estos son los pasos que deben seguirse para eliminar un usuario:

1.	En la hoja **Usuarios**, seleccione la asignación de roles que quiera quitar.

2.	Haga clic en el botón **Quitar** de la hoja de detalles de asignación.

3.	Haga clic en **Sí** para confirmar la eliminación.

    ![Quitar usuarios](media/automation-role-based-access-control/automation-08-remove-users.png)

## Usuario con rol asignado

Cuando un usuario asignado a un rol inicia sesión en su cuenta de Automatización, verá la cuenta del propietario en la lista de **directorios predeterminados**. Para ver la Cuenta de Automatización al que se ha agregado, es preciso que cambie el directorio predeterminado al directorio predeterminado del propietario.

![Directorio predeterminado](media/automation-role-based-access-control/automation-09-default-directory-in-role-assigned-user.png)

### Experiencia del usuario en el rol Operador de automatización

Cuando un usuario asignado al rol Operador de Automatización vea la cuenta de Automatización a la que está asignado, solo verá la lista de Runbooks, los trabajos de Runbook y las programaciones creados en la cuenta de Automatización, pero no su definición. Puede iniciar, detener, suspender, reanudar o programar el trabajo de Runbook. El usuario no tendrá acceso a otros recursos de Automatización como configuraciones, grupos de Hybrid Worker o nodos de DSC.

![Sin acceso a los recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

Cuando el usuario hace clic en el Runbook, los comandos para ver el origen o editar el Runbook no se proporcionan, ya que el rol de Operador de Automatización no permite acceder a ellos.

![Sin acceso para editar Runbook](media/automation-role-based-access-control/automation-11-no-access-to-edit-runbook.png)

El usuario tendrá acceso para ver y crear programaciones, pero no tendrá acceso a otros tipos de activos.

![Sin acceso a los activos](media/automation-role-based-access-control/automation-12-no-access-to-assets.png)

Este usuario tampoco tiene acceso para ver los Webhooks asociados a un Runbook

![Sin acceso a Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)

## Configuración de RBAC para una Cuenta de Automatización mediante Azure PowerShell

El acceso basado en rol también se puede configurar en una cuenta de Automatización mediante los siguientes [cmdlets de Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).

• [Get AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) enumera todos los roles RBAC disponibles en Azure Active Directory. Este comando se puede usar con la propiedad **Name** para enumerar todas las acciones que un rol concreto puede llevar a cabo. **Ejemplo:** ![Obtener definición de rol](media/automation-role-based-access-control/automation-14-get-azurerm-role-definition.png)

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) enumera las asignaciones de roles RBAC de Azure AD en el ámbito especificado. Sin parámetros, este comando devuelve todas las asignaciones de roles realizadas en la suscripción. Use el parámetro **ExpandPrincipalGroups** para enumerar las asignaciones de acceso del usuario especificado, así como los grupos a los que pertenezca. **Ejemplo:** use el siguiente comando para enumerar todos los usuarios de una cuenta de Automatización y sus roles.

    Get-AzureRMRoleAssignment -scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>” 

![Obtener asignación de rol](media/automation-role-based-access-control/automation-15-get-azurerm-role-assignment.png)

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para asignar acceso a usuarios, grupos y aplicaciones en un ámbito determinado. **Ejemplo:** use el siguiente comando para asignar el rol "Operador de Automatización" para un usuario en el ámbito de la cuenta de Automatización.

    New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName "Automation operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”  

![Nueva asignación de rol](media/automation-role-based-access-control/automation-16-new-azurerm-role-assignment.png)

• Use [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para quitar el acceso al usuario, grupo o aplicación concretos de un ámbito determinado. **Ejemplo:** use el siguiente comando para quitar el usuario del rol "Operador de Automatización" en el ámbito de la cuenta de Automatización.

    Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName "Automation Operator" -Scope “/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation Account Name>”

En los ejemplos anteriores, reemplace el **nombre de inicio de sesión**, el **identificador de suscripción**, el **nombre del grupo de recursos** y el **nombre de la cuenta de Automation** por los detalles de su cuenta. Elija **Sí** cuando se le pida que confirme antes de procederse a quitar la asignación de roles de usuario.


## Pasos siguientes
-  Para más información sobre las diferentes formas de configurar RBAC para Automatización de Azure, consulte [Administración del control de acceso basado en rol con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
- Para ver detalles sobre las distintas maneras de iniciar un Runbook, consulte [Inicio de un Runbook en Automatización de Azure](automation-starting-a-runbook.md).
- Para más información acerca de distintos tipos de Runbook, consulte [Tipos de Runbooks de Automatización de Azure](automation-runbook-types.md).

<!---HONumber=AcomDC_0914_2016-->