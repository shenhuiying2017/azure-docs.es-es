---
title: Control de acceso basado en rol en Azure Automation | Microsoft Docs
description: "El control de acceso basado en rol (RBAC) permite la administración del acceso en los recursos de Azure. En este artículo se describe cómo configurar RBAC en Azure Automation."
services: automation
documentationcenter: 
author: georgewallace
manager: jwhit
editor: tysonn
keywords: "automatización de rbac, control de acceso basado en roles, rbac de azure"
ms.assetid: 04b5625e-0ee8-4b5b-85cd-7734c1b3d4a3
ms.service: automation
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/05/2018
ms.author: magoedte;sngun
ms.openlocfilehash: 753c06ec4a20650f779b68e11e1d6d6fd27a0141
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2018
---
# <a name="role-based-access-control-in-azure-automation"></a>Control de acceso basado en rol en Azure Automation

El control de acceso basado en rol (RBAC) permite la administración del acceso en los recursos de Azure. Con [RBAC](../active-directory/role-based-access-control-configure.md), se pueden separar los deberes del equipo y conceder a los usuarios, grupos y aplicaciones únicamente el acceso que necesiten para su trabajo. El acceso basado en rol se puede conceder a los usuarios que utilicen Azure Portal, las herramientas de la línea de comandos de Azure o las API de administración de Azure.

## <a name="roles-in-automation-accounts"></a>Roles en cuentas de Automation
En Azure Automation, el acceso se concede mediante la asignación de rol de RBAC adecuado a los usuarios, grupos y aplicaciones en el ámbito de las cuentas de Automation. Estos son los roles integrados compatibles que admiten las cuentas de Automation:

| **Rol** | **Descripción** |
|:--- |:--- |
| Propietario |El rol Propietario permite el acceso a todos los recursos y las acciones de una cuenta de Automation, lo que incluye proporcionar acceso a otros usuarios, grupos y aplicaciones para que administren la cuenta de Automation. |
| Colaborador |El rol Colaborador permite administrar todo, excepto la modificación de los permisos de acceso de otros usuarios a una cuenta de Automation. |
| Lector |El rol Lector permite ver todos los recursos de una cuenta de Automation, pero no realizar cambios. |
| Operador de Automation |El rol Operador de automatización permite realizar tareas operativas, como iniciar, detener, suspender, reanudar y programar trabajos. Este rol es útil si se desea proteger los recursos de la cuenta de Automation, como credenciales, activos y Runbooks, para que no puedan verse ni modificarse, pero permitir a los miembros de la organización ejecutar los runbooks. |
|Operador de trabajos de Automation|El rol de operador de trabajos de Automation le permite crear y administrar trabajos mediante runbooks de Automation.|
|Operador de runbooks de Automation|El rol de operador de runbooks de Automation permite leer propiedades de runbooks. También es posible crear trabajos del runbook.|
| Colaborador de Log Analytics | El rol de colaborador de Log Analytics permite leer todos los datos de supervisión y editar la configuración de supervisión. La edición de la configuración de supervisión incluye la posibilidad de añadir la extensión de máquina virtual a las máquinas virtuales, leer las claves de las cuentas de almacenamiento para poder configurar la recopilación de registros de Azure Storage, crear y configurar cuentas de Automation, añadir soluciones y configurar Azure Diagnostics en todos los recursos de Azure.|
| Lector de Log Analytics | El rol de lector de Log Analytics permite ver y buscar todos los datos de supervisión, así como ver la configuración de supervisión. Esto incluye la visualización de la configuración de Azure Diagnostics en todos los recursos de Azure. |
| Colaborador de supervisión | El rol de colaborador de supervisión permite leer todos los datos de supervisión y actualizar la configuración de supervisión.|
| Lector de supervisión | El rol de lector de supervisión permite leer todos los datos de supervisión. |
| Administrador de acceso de usuario |El rol Administrador de acceso de usuario permite administrar el acceso de los usuarios a las cuentas de Azure Automation. |

## <a name="role-permissions"></a>Permisos de los roles

Las tablas siguientes describen los permisos específicos concedidos a cada rol. En ellas, se incluyen elementos correspondientes a las categorías Actions (para conceder permisos) y NotActions (para restringirlos).

### <a name="owner"></a>Propietario

Un propietario puede administrar todo, incluido el acceso. La siguiente tabla muestra los permisos concedidos para el rol:

|Acciones|DESCRIPCIÓN|
|---|---|
|Microsoft.Automation/automationAccounts/|Crear y administrar recursos de todos los tipos.|

### <a name="contributor"></a>Colaborador

Un colaborador puede administrar todo, excepto el acceso. La siguiente tabla muestra los permisos concedidos y denegados para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Crear y administrar recursos de todos los tipos|
|**No acciones**||
|Microsoft.Authorization/*/Delete| Eliminar roles y asignaciones de roles.       |
|Microsoft.Authorization/*/Write     |  Crear roles y asignaciones de roles.       |
|Microsoft.Authorization/elevateAccess/Action    | Deniega la capacidad de crear un administrador de acceso de usuario.       |

### <a name="reader"></a>Lector

Un lector puede ver todos los recursos de una cuenta de Automation, pero no realizar cambios.

|**Acciones**  |**Descripción**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Ver todos los recursos en una cuenta de Automation. |

### <a name="automation-job-operator"></a>Operador de trabajos de Automation

El rol de operador de trabajos de Automation se asigna en el ámbito de la cuenta de Automation. Esto permite que los permisos de operador administren trabajos en la cuenta.

|**Acciones**  |**Descripción**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorización de lectura.|
|Microsoft.Automation/automationAccounts/jobs/read|Enumerar trabajos del runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Reanudar un trabajo que está en pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancelar un trabajo en curso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leer los flujos de trabajo y los resultados.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausar un trabajo en curso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crear trabajos.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Roles de lectura y asignaciones de roles.       |
|Microsoft.Resources/deployments/*      |Crear y administrar implementaciones de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Crear y administrar reglas de alertas.        |
|Microsoft.Support/* |Crear y administrar incidencias de soporte técnico.|

### <a name="automation-runbook-operator"></a>Operador de runbooks de Automation

El rol de operador de runbooks de Automation se concede en el ámbito del runbook. El operador de runbooks de Automation puede ver el nombre del runbook. Este permiso, en combinación con "Operador de trabajos de Automation" en el ámbito de la cuenta de Automation, permite al operador realizar las acciones de operador de Automation para un runbook determinado. La siguiente tabla muestra los permisos concedidos para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Enumerar los runbooks.        |
|Microsoft.Authorization/*/read      | Autorización de lectura.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Roles de lectura y asignaciones de roles.         |
|Microsoft.Resources/deployments/*      | Crear y administrar implementaciones de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Crear y administrar reglas de alertas.        |
|Microsoft.Support/*      | Crear y administrar incidencias de soporte técnico.        |

### <a name="automation-operator"></a>Operador de Automation

Un operador de Automation puede iniciar, detener, suspender y reanudar trabajos. La siguiente tabla muestra los permisos concedidos para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|Microsoft.Authorization/*/read|Autorización de lectura.|
|Microsoft.Automation/automationAccounts/jobs/read|Enumerar trabajos del runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Reanudar un trabajo que está en pausa.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Cancelar un trabajo en curso.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Leer los flujos de trabajo y los resultados.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pausar un trabajo en curso.|
|Microsoft.Automation/automationAccounts/jobs/write|Crear trabajos.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Roles de lectura y asignaciones de roles.         |
|Microsoft.Resources/deployments/*      |Crear y administrar implementaciones de grupos de recursos.         |
|Microsoft.Insights/alertRules/*      | Crear y administrar reglas de alertas.        |
|Microsoft.Support/* |Crear y administrar incidencias de soporte técnico.|

### <a name="log-analytics-contributor"></a>Colaborador de Log Analytics

Un colaborador de Log Analytics puede leer todos los datos de supervisión y editar la configuración de supervisión. La edición de la configuración de supervisión incluye la posibilidad de añadir la extensión de máquina virtual a las máquinas virtuales, leer las claves de las cuentas de almacenamiento para poder configurar la recopilación de registros de Azure Storage, crear y configurar cuentas de Automation, añadir soluciones y configurar Azure Diagnostics en todos los recursos de Azure. La siguiente tabla muestra los permisos concedidos para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|*/read|Leer recursos de todos los tipos, excepto secretos.|
|Microsoft.Automation/automationAccounts/*|Administrar cuentas de Automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Crear y administrar extensiones de máquinas virtuales.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Enumerar claves de cuentas de almacenamiento clásicas.|
|Microsoft.Compute/virtualMachines/extensions/*|Crear y administrar extensiones de máquinas virtuales clásicas.|
|Microsoft.Insights/alertRules/*|Reglas de alerta de lectura, escritura y eliminación.|
|Microsoft.Insights/diagnosticSettings/*|Configuración de diagnóstico de lectura, escritura y eliminación.|
|Microsoft.OperationalInsights/*|Administrar Log Analytics.|
|Microsoft.OperationsManagement/*|Administrar soluciones en áreas de trabajo.|
|Microsoft.Resources/deployments/*|Crear y administrar implementaciones de grupos de recursos.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Crear y administrar implementaciones de grupos de recursos.|
|Microsoft.Storage/storageAccounts/listKeys/action|Enumerar claves de cuentas de almacenamiento.|
|Microsoft.Support/*|Crear y administrar incidencias de soporte técnico.|


### <a name="log-analytics-reader"></a>Lector de Log Analytics

Un lector de Log Analytics puede ver y buscar los datos de supervisión, así como consultar la configuración, incluida la de Azure Diagnostics en todos los recursos de Azure. La siguiente tabla muestra los permisos concedidos o denegados para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|*/read|Leer recursos de todos los tipos, excepto secretos.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Administrar las consultas en Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Buscar datos de Log Analytics.|
|Microsoft.Support/*|Crear y administrar incidencias de soporte técnico.|
|**No acciones**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|No se pueden leer las claves de acceso compartido.|

### <a name="monitoring-contributor"></a>Colaborador de supervisión

Un colaborador de supervisión puede leer todos los datos de supervisión y actualizar la configuración de supervisión. La siguiente tabla muestra los permisos concedidos para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|*/read|Leer recursos de todos los tipos, excepto secretos.|
|Microsoft.AlertsManagement/alerts/*|Administrar alertas.|
|Microsoft.AlertsManagement/alertsSummary/*|Administrar el panel de alertas.|
|Microsoft.Insights/AlertRules/*|Administrar reglas de alertas.|
|Microsoft.Insights/components/*|Administrar componentes de Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Administrar la configuración de diagnóstico.|
|Microsoft.Insights/eventtypes/*|Enumerar eventos del registro de actividades (eventos de administración) de una suscripción. Este permiso es aplicable para el acceso mediante programación y mediante el portal al registro de actividades.|
|Microsoft.Insights/LogDefinitions/*|Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. Enumere las categorías de registro del registro de actividad.|
|Microsoft.Insights/MetricDefinitions/*|Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso).|
|Microsoft.Insights/Metrics/*|Leer las métricas de un recurso.|
|Microsoft.Insights/Register/Action|Registrar el proveedor de Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Administrar pruebas web de Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Administrar paquetes de soluciones de Log Analytics.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Administrar búsquedas guardadas de Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Buscar áreas de trabajo de Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Enumerar las claves de un área de trabajo de Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Administrar configuraciones de visión de almacenamiento de Log Analytics.|
|Microsoft.Support/*|Crear y administrar incidencias de soporte técnico.|
|Microsoft.WorkloadMonitor/workloads/*|Administrar cargas de trabajo.|

### <a name="monitoring-reader"></a>Lector de supervisión

Un lector de supervisión puede leer todos los datos de supervisión. La siguiente tabla muestra los permisos concedidos para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|*/read|Leer recursos de todos los tipos, excepto secretos.|
|Microsoft.OperationalInsights/workspaces/search/action|Buscar áreas de trabajo de Log Analytics.|
|Microsoft.Support/*|Crear y administrar incidencias de soporte técnico|

### <a name="user-access-administrator"></a>Administrador de acceso de usuario

Un administrador de acceso de usuario puede administrar el acceso de los usuarios a los recursos de Azure. La siguiente tabla muestra los permisos concedidos para el rol:

|**Acciones**  |**Descripción**  |
|---------|---------|
|*/read|Leer todos los recursos.|
|Microsoft.Authorization/*|Administrar la autorización|
|Microsoft.Support/*|Crear y administrar incidencias de soporte técnico|

## <a name="onboarding"></a>Incorporación

Las siguientes tablas muestran los permisos mínimos necesarios para incorporar máquinas virtuales para las soluciones de seguimiento de cambios y administración de actualizaciones.

### <a name="onboarding-from-a-virtual-machine"></a>Incorporación desde una máquina virtual

|**Acción**  |**Permiso**  |**Ámbito mínimo**  |
|---------|---------|---------|
|Escribir nueva implementación      | Microsoft.Resources/deployments/*          |La suscripción          |
|Escribir nuevo grupo de recursos      | Microsoft.Resources/subscriptions/resourceGroups/write        | La suscripción          |
|Crear nueva área de trabajo predeterminada      | Microsoft.OperationalInsights/workspaces/write         | Grupos de recursos         |
|Crear nueva cuenta      |  Microsoft.Automation/automationAccounts/write        |Grupos de recursos         |
|Vincular área de trabajo y cuenta      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Área de trabajo</br>Cuenta de Automation
|Crear solución      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Grupos de recursos          |
|Crear extensión MMA      | Microsoft.Compute/virtualMachines/write         | Máquina virtual         |
|Crear búsqueda guardada      | Microsoft.OperationalInsights/workspaces/write          | Área de trabajo         |
|Crear configuración de ámbito      | Microsoft.OperationalInsights/workspaces/write          | Área de trabajo         |
|Vincular solución a la configuración de ámbito      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solución         |
|Comprobación de estado de incorporación: leer área trabajo      | Microsoft.OperationalInsights/workspaces/read         | Área de trabajo         |
|Comprobación de estado de incorporación: leer propiedad de área de trabajo vinculada de la cuenta     | Microsoft.Automation/automationAccounts/read      | Cuenta de Automation        |
|Comprobación de estado de incorporación: leer solución      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Solución         |
|Comprobación de estado de incorporación: leer máquina virtual      | Microsoft.Compute/virtualMachines/read         | Máquina virtual         |
|Comprobación de estado de incorporación: leer cuenta      | Microsoft.Automation/automationAccounts/read  |  Cuenta de Automation   |

### <a name="onboarding-from-automation-account"></a>Incorporación desde cuenta de Automation

|**Acción**  |**Permiso** |**Ámbito mínimo**  |
|---------|---------|---------|
|Crear una nueva implementación     | Microsoft.Resources/deployments/*        | La suscripción         |
|Crear un grupo de recursos     | Microsoft.Resources/subscriptions/resourceGroups/write         | La suscripción        |
|Hoja AutomationOnboarding: crear nueva área de trabajo     |Microsoft.OperationalInsights/workspaces/write           | Grupos de recursos        |
|Hoja AutomationOnboarding: leer área de trabajo vinculada     | Microsoft.Automation/automationAccounts/read        | Cuenta de Automation       |
|Hoja AutomationOnboarding: leer solución     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Solución        |
|Hoja AutomationOnboarding: leer área de trabajo     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Área de trabajo        |
|Crear vínculo para área de trabajo y cuenta     | Microsoft.OperationalInsights/workspaces/write        | Área de trabajo        |
|Escribir cuenta para Shoebox      | Microsoft.Automation/automationAccounts/write        | Cuenta        |
|Crear solución      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Grupo de recursos         |
|Crear o editar búsqueda guardada     | Microsoft.OperationalInsights/workspaces/write        | Área de trabajo        |
|Crear o editar la configuración de ámbito     | Microsoft.OperationalInsights/workspaces/write        | Área de trabajo        |
|Vincular solución a la configuración de ámbito      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Solución         |
|**Paso 2: Incorporación de varias máquinas virtuales**     |         |         |
|Hoja VMOnboarding: crear extensión MMA     | Microsoft.Compute/virtualMachines/write           | Máquina virtual        |
|Crear o editar búsqueda guardada     | Microsoft.OperationalInsights/workspaces/write           | Área de trabajo        |
|Crear o editar la configuración de ámbito  | Microsoft.OperationalInsights/workspaces/write   | Área de trabajo|

## <a name="update-management"></a>Administración de actualizaciones

La administración de actualizaciones cubre varios servicios para proporcionar su servicio. La siguiente tabla muestra los permisos necesarios para administrar las implementaciones de administración de actualizaciones:

|**Recurso**  |**Rol**  |**Ámbito**  |
|---------|---------|---------|
|Cuenta de Automation     | Colaborador de Log Analytics       | Cuenta de Automation        |
|Cuenta de Automation    | Colaborador de la máquina virtual        | Grupo de recursos para la cuenta        |
|Área de trabajo de Log Analytics     | Colaborador de Log Analytics| Área de trabajo de Log Analytics        |
|Área de trabajo de Log Analytics |Lector de Log Analytics| La suscripción|
|Solución     |Colaborador de Log Analytics         | Solución|
|Máquina virtual     | Colaborador de la máquina virtual        | Máquina virtual        |

## <a name="configure-rbac-for-your-automation-account-using-azure-portal"></a>Configuración de RBAC para una cuenta de Automation mediante Azure Portal
1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y abra su cuenta de Automation en la página Cuentas de Automation.  
2. Haga clic en el control **Control de acceso (IAM)** de la esquina superior izquierda. Se abre la página **Control de acceso (IAM)**, donde puede añadir nuevos usuarios, grupos y aplicaciones para administrar la cuenta de Automation y ver los roles existentes que se pueden configurar para la cuenta de Automation.
   
   ![Botón de acceso](media/automation-role-based-access-control/automation-01-access-button.png)  

### <a name="add-a-new-user-and-assign-a-role"></a>Adición de usuarios nuevos y asignación de roles
1. En la página **Control de acceso (IAM)**, haga clic en **+ Agregar** para abrir la página **Agregar permisos**, donde puede agregar un usuario, un grupo o una aplicación y asignarles un rol.  

2. Seleccione en rol en la lista de roles disponibles. Puede elegir cualquiera de los roles integrados disponibles que admitan las cuentas de Automation o cualquier rol personalizado que haya definido.

3. Escriba el nombre del usuario al que desee conceder permisos en el campo **Seleccionar**. Seleccione el usuario de la lista y haga clic en **Guardar**.
   
   ![Agregar usuarios](media/automation-role-based-access-control/automation-04-add-users.png)  
   
   El usuario debería aparecer añadido en la página **Usuarios** con el rol seleccionado asignado.  
   
   ![Enumerar usuarios](media/automation-role-based-access-control/automation-05-list-users.png)  
   
   También se puede asignar un rol al usuario en la página **Roles** . 
4. Haga clic en **Roles** en la página **Control de acceso (IAM)** para abrir la página **Roles**. Aquí puede ver el nombre del rol, el número de usuarios y los grupos asignados a dicho rol.
   
    ![Asignar rol en la página de usuarios](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)  
   
   > [!NOTE]
   > El control de acceso basado en roles solo se puede establecer en el ámbito de cuenta de Automation, no en los recursos que estén debajo de la cuenta de Automation.

### <a name="remove-a-user"></a>Eliminación de un usuario
Puede quitar el permiso de usuario de cualquier usuario que no administre la cuenta de Automation o que haya dejado de trabajar en la organización. Estos son los pasos que deben seguirse para eliminar un usuario: 

1. En la página **Control de acceso (IAM)**, seleccione el usuario que desea quitar y haga clic en **Quitar**.
2. Haga clic en el botón **Quitar** del panel de detalles de asignación.
3. Haga clic en **Sí** para confirmar la eliminación.

   ![Quitar usuarios](media/automation-role-based-access-control/automation-08-remove-users.png)

## <a name="role-assigned-user"></a>Usuario con rol asignado

Cuando un usuario asignado a un rol inicia sesión en Azure y selecciona su cuenta de Automation, verá la cuenta del propietario en la lista de **Directorios**. Para ver la Cuenta de Automation al que se ha agregado, es preciso que cambie el directorio predeterminado al directorio predeterminado del propietario.

### <a name="user-experience-for-automation-operator-role"></a>Experiencia del usuario en el rol Operador de Automation
Cuando un usuario asignado al rol Operador de Automation vea la cuenta de Automation a la que está asignado, solo verá la lista de runbooks, los trabajos de runbook y las programaciones creados en la cuenta de Automation, pero no su definición. Puede iniciar, detener, suspender, reanudar o programar el trabajo de Runbook. El usuario no tiene acceso a otros recursos de Automation como configuraciones, grupos de Hybrid Worker o nodos de DSC.

![Sin acceso a los recursos](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)  

El usuario tiene acceso para ver y crear programaciones, pero no tiene acceso a otros tipos de recursos.

Este usuario tampoco tiene acceso para ver los Webhooks asociados a un Runbook

![Sin acceso a Webhooks](media/automation-role-based-access-control/automation-13-no-access-to-webhooks.png)  

## <a name="configure-rbac-for-your-automation-account-using-azure-powershell"></a>Configuración de RBAC para una cuenta de Automation mediante Azure PowerShell
El acceso basado en rol también se puede configurar en una cuenta de Automation mediante los siguientes [cmdlets de Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md):

• [Get AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) enumera todos los roles RBAC disponibles en Azure Active Directory. Este comando se puede usar con la propiedad **Name** para enumerar todas las acciones que un rol concreto puede llevar a cabo.

```powershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

A continuación se incluye el resultado de ejemplo:

```powershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action, 
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
``` 

• [Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) enumera las asignaciones de roles RBAC de Azure AD en el ámbito especificado. Sin parámetros, este comando devuelve todas las asignaciones de roles realizadas en la suscripción. Use el parámetro **ExpandPrincipalGroups** para enumerar las asignaciones de acceso del usuario especificado, así como los grupos a los que pertenezca.  
    **Ejemplo:** use el siguiente comando para enumerar todos los usuarios de una cuenta de Automatización y sus roles.

```powershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A continuación se incluye el resultado de ejemplo:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

• [New-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) para asignar acceso a usuarios, grupos y aplicaciones en un ámbito determinado.  
    **Ejemplo:** use el siguiente comando para asignar el rol "Operador de Automation" para un usuario en el ámbito de la cuenta de Automation.

```powershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

A continuación se incluye el resultado de ejemplo:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

• Use [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) para quitar el acceso a un usuario, grupo o aplicación concretos de un ámbito determinado.  
    **Ejemplo:** use el siguiente comando para quitar el usuario del rol "Operador de Automation" en el ámbito de la cuenta de Automation.

```powershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

En los ejemplos anteriores, reemplace el **nombre de inicio de sesión**, el **identificador de suscripción**, el **nombre del grupo de recursos** y el **nombre de la cuenta de Automation** por los detalles de su cuenta. Elija **Sí** cuando se le pida que confirme antes de procederse a quitar la asignación de roles de usuario.   

## <a name="next-steps"></a>pasos siguientes
* Para más información sobre las diferentes formas de configurar RBAC para Azure Automation, consulte [Administración del control de acceso basado en rol con Azure PowerShell](../active-directory/role-based-access-control-manage-access-powershell.md).
* Para ver detalles sobre las distintas maneras de iniciar un Runbook, consulte [Inicio de un Runbook en Automatización de Azure](automation-starting-a-runbook.md)
* Para más información acerca de distintos tipos de Runbook, consulte [Tipos de Runbooks de Azure Automation](automation-runbook-types.md)

