---
title: "Administración de áreas de trabajo en Azure Log Analytics | Microsoft Docs"
description: "Puede administrar las áreas de trabajo en Azure Log Analytics, puede usar una serie de tareas administrativas en usuarios, cuentas, áreas de trabajo y cuentas de Azure."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/12/2017
ms.author: magoedte
ms.openlocfilehash: 1549408c6885ee556a142ab7de613ebb1629070d
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/13/2017
---
# <a name="manage-workspaces"></a>Administración de áreas de trabajo

Para administrar el acceso a Log Analytics, tendrá que realizar varias tareas administrativas relacionadas con las áreas de trabajo. En este artículo se proporcionan consejos y procedimientos recomendados para administrar áreas de trabajo. En esencia, un área de trabajo es un contenedor donde se incluyen los datos de la cuenta e información básica de la configuración de la cuenta. Tanto usted como otros miembros de la organización pueden usar varias áreas de trabajo para administrar diferentes conjuntos de datos, recopilados a partir de toda la infraestructura de TI o de algunos de sus componentes.

Para crear un área de trabajo, necesitará:

1. Tener una suscripción a Azure.
2. Elegir un nombre de área de trabajo.
3. Asociar el área de trabajo a la suscripción.
4. Elegir una ubicación geográfica.

## <a name="determine-the-number-of-workspaces-you-need"></a>Determinación del número de áreas de trabajo necesarias
Un área de trabajo es un recurso de Azure y un contenedor donde los datos se recopilan, se agregan, se analizan y se presentan en Azure Portal.

Puede tener varias áreas de trabajo por suscripción de Azure y puede tener acceso a más de una área de trabajo. Minimizar el número de áreas de trabajo le permite consultar y correlacionar la mayoría de los datos, ya que no es posible realizar consultas a través de varias áreas de trabajo. En esta sección se describe cuándo puede resultar útil la creación de más de un área de trabajo.

En la actualidad, un área de trabajo proporciona:

* Una ubicación geográfica para el almacenamiento de datos
* Granularidad para la facturación
* Aislamiento de datos
* Ámbito de configuración

Dadas estas características anteriores, quizás quiera crear varias áreas de trabajo en los siguientes casos:

* Una empresa internacional que necesita que los datos estén almacenados en regiones concretas por motivos de soberanía de datos o cumplimiento normativo.
* Un usuario de Azure que desea evitar los gastos de transferencia de datos de salida manteniendo un área de trabajo en la misma región que los recursos de Azure que administra.
* Se desea asignar los gastos a diferentes departamentos o grupos de negocios en función del uso que hagan. Cuando se crea un área de trabajo para cada departamento o grupo de negocios, la factura y la declaración de uso de Azure reflejan los cargos de cada área de trabajo de manera independiente.
* Es un proveedor de servicio administrado que necesita mantener los datos de Log Analytics para cada cliente que administra aislados de los datos de otro cliente.
* Si se administran varios clientes y se desea que cada cliente, departamento o grupo empresarial vea sus propios datos, pero no los datos de los demás.

Cuando se usan agentes para recopilar datos, se puede [configurar cada uno de ellos para que informe a una o varias áreas de trabajo](log-analytics-windows-agent.md).

Si usa System Center Operations Manager, cada grupo de administración de Operations Manager se puede conectar con una sola área de trabajo. Sin embargo, se puede configurar Microsoft Monitoring Agent en el equipo para informar tanto a Operations Manager como a cualquier otra área de trabajo de Log Analytics.  

### <a name="workspace-information"></a>Información del área de trabajo

Puede ver los detalles sobre el área de trabajo en Azure Portal. 

#### <a name="view-workspace-information-in-the-azure-portal"></a>Visualización de la información de un área de trabajo en Azure Portal

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Más servicios**, en la esquina inferior izquierda de Azure Portal.  En la lista de recursos, escriba **Log Analytics**. Cuando comience a escribir, la lista se filtrará en función de la entrada. Haga clic en **Log Analytics**.  
    ![Menú central de Azure](./media/log-analytics-manage-access/hub.png)  
3. En la hoja de suscripciones de Log Analytics, seleccione un área de trabajo.
4. La hoja del área de trabajo muestra información detallada sobre el área de trabajo y vínculos a información adicional.  
    ![detalles de área de trabajo](./media/log-analytics-manage-access/workspace-details.png)  


## <a name="manage-accounts-and-users"></a>Administración de cuentas y usuarios
Cada área de trabajo puede tener varias cuentas asociadas, y cada cuenta (ya sea una cuenta Microsoft o una cuenta profesional) puede tener acceso a varias áreas de trabajo.

De forma predeterminada, la cuenta de Microsoft o la cuenta profesional que crea el área de trabajo se convierte en el administrador del área de trabajo.

Hay dos modelos de permisos que controlan el acceso a un área de trabajo de Log Analytics:

1. Roles de usuario heredados de Log Analytics
2. [Acceso basado en roles de Azure](../active-directory/role-based-access-control-configure.md)

En la tabla siguiente se resume el acceso que se puede establecer con cada modelo de permisos:

|                          | Portal de Log Analytics | Azure Portal | API (incluido PowerShell) |
|--------------------------|----------------------|--------------|----------------------------|
| Roles de usuario de Log Analytics | Sí                  | No           | No                         |
| Acceso basado en roles de Azure  | Sí                  | Sí          | Sí                        |

> [!NOTE]
> Log Analytics va a pasar al uso del acceso basado en roles de Azure como modelo de permisos, para sustituir a los roles de usuario de Log Analytics.
>
>

Los roles de usuario heredados de Log Analytics solo controlan el acceso a las actividades realizadas en el [portal de Log Analytics](https://mms.microsoft.com).

Las siguientes actividades también requieren permisos de Azure:

| Acción                                                          | Permisos de Azure necesarios | Notas |
|-----------------------------------------------------------------|--------------------------|-------|
| Agregar y quitar soluciones de administración                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | |
| Cambiar el plan de tarifa                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Ver los datos en los iconos de soluciones *Backup* y *Site Recovery* | Administrador o coadministrador | Accede a los recursos implementados mediante el modelo de implementación clásica |
| Creación de un área de trabajo en Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Administración del acceso a Log Analytics mediante permisos de Azure
Para conceder acceso al área de trabajo de Log Analytics mediante permisos de Azure, siga los pasos que se describen en [Uso de asignaciones de roles para administrar el acceso a los recursos de la suscripción de Azure](../active-directory/role-based-access-control-configure.md).

Azure tiene dos roles de usuario integrados para Log Analytics:
- Lector de Log Analytics
- Colaborador de Log Analytics

Los miembros del rol *Lector de Log Analytics* pueden:
- Ver y buscar todos los datos de supervisión 
- Ver la configuración de supervisión, incluida la visualización de la configuración de Azure Diagnostics en todos los recursos de Azure.

| Tipo    | Permiso | Descripción |
| ------- | ---------- | ----------- |
| Acción | `*/read`   | Capacidad para ver todos los recursos y la configuración de los recursos. Incluye la visualización de: <br> Estado de la extensión de la máquina virtual <br> Configuración de Azure Diagnostics en los recursos <br> Todas las propiedades y opciones de configuración de todos los recursos |
| Acción | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Capacidad de realizar consultas de búsqueda de registros v2 |
| Acción | `Microsoft.OperationalInsights/workspaces/search/action` | Capacidad de realizar consultas de búsqueda de registros v1 |
| Acción | `Microsoft.Support/*` | Capacidad de abrir casos de soporte técnico |
|No acción | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Evita la lectura de la clave del área de trabajo necesaria para usar la API de recopilación de datos e instalar agentes |


Los miembros del rol *Colaborador de Log Analytics* pueden:
- Leer todos los datos de supervisión 
- Crear y configurar cuentas de Automation
- Agregar y quitar soluciones de administración
- Leer las claves de las cuentas de almacenamiento 
- Configurar la recopilación de registros de Azure Storage
- Editar la configuración de supervisión de los recursos de Azure:
  - Agregar la extensión de máquina virtual a las máquinas virtuales
  - Configurar Azure Diagnostics en todos los recursos de Azure

> [!NOTE] 
> Puede usar la capacidad para agregar una extensión de máquina virtual a una máquina virtual para obtener el control total sobre una máquina virtual.

| Permiso | Descripción |
| ---------- | ----------- |
| `*/read`     | Capacidad para ver todos los recursos y la configuración de los recursos. Incluye la visualización de: <br> Estado de la extensión de la máquina virtual <br> Configuración de Azure Diagnostics en los recursos <br> Todas las propiedades y opciones de configuración de todos los recursos |
| `Microsoft.Automation/automationAccounts/*` | Capacidad para crear y configurar cuentas de Azure Automation, incluido agregar y editar runbooks |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Agregar, actualizar y eliminar extensiones de máquina virtual, incluida la extensión de Microsoft Monitoring Agent y el agente de OMS para la extensión de Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Ver la clave de la cuenta de almacenamiento. Necesaria para configurar Log Analytics para leer los registros de cuentas de Azure Storage |
| `Microsoft.Insights/alertRules/*` | Agregar, actualizar y eliminar reglas de alertas |
| `Microsoft.Insights/diagnosticSettings/*` | Agregar, actualizar y eliminar la configuración de diagnósticos en los recursos de Azure |
| `Microsoft.OperationalInsights/*` | Agregar, actualizar y eliminar la configuración de áreas de trabajo de Log Analytics |
| `Microsoft.OperationsManagement/*` | Agregar y eliminar soluciones de administración |
| `Microsoft.Resources/deployments/*` | Crear y eliminar implementaciones. Necesario para agregar y eliminar soluciones, áreas de trabajo y cuentas de Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Crear y eliminar implementaciones. Necesario para agregar y eliminar soluciones, áreas de trabajo y cuentas de Automation |

Para agregar y quitar usuarios de un rol de usuario, es necesario tener permisos `Microsoft.Authorization/*/Delete` y `Microsoft.Authorization/*/Write`.

Use estos roles para conceder a los usuarios acceso en distintos ámbitos:
- Suscripción: acceso a todas las áreas de trabajo de la suscripción
- Grupo de recursos: acceso a todas las áreas de trabajo del grupo de recursos
- Recurso: acceso solo al área de trabajo especificada

Use [roles personalizados](../active-directory/role-based-access-control-custom-roles.md) para crear roles con los permisos específicos necesarios.

### <a name="azure-user-roles-and-log-analytics-portal-user-roles"></a>Roles de usuario de Azure y roles de usuario del portal de Log Analytics
Si tiene al menos permiso de lectura de Azure en el área de trabajo de Log Analytics, puede abrir el portal de OMS haciendo clic en la tarea **Portal de OMS** al visualizar el área de trabajo de Log Analytics.

Al abrir el portal de OMS, puede cambiar al uso de los roles de usuario heredados de Log Analytics. Si no tiene una asignación de roles en el portal de Log Analytics, el servicio [comprueba los permisos de Azure que tiene en el área de trabajo](https://docs.microsoft.com/rest/api/authorization/permissions#Permissions_ListForResource).
La asignación de roles en el portal de OMS se determina de la manera siguiente:

| Condiciones                                                   | Rol de usuario asignado de Log Analytics | Notas |
|--------------------------------------------------------------|----------------------------------|-------|
| La cuenta pertenece a un rol de usuario heredado de Log Analytics     | El rol de usuario especificado de Log Analytics | |
| La cuenta no pertenece a un rol de usuario heredado de Log Analytics <br> Permisos de Azure completos para el área de trabajo (`*` permiso <sup>1</sup>) | Administrador ||
| La cuenta no pertenece a un rol de usuario heredado de Log Analytics <br> Permisos de Azure completos para el área de trabajo (`*` permiso <sup>1</sup>) <br> *sin acciones* de `Microsoft.Authorization/*/Delete` y `Microsoft.Authorization/*/Write` | Colaborador ||
| La cuenta no pertenece a un rol de usuario heredado de Log Analytics <br> Permiso de lectura de Azure | Solo lectura ||
| La cuenta no pertenece a un rol de usuario heredado de Log Analytics <br> Los permisos de Azure no se entienden | Solo lectura ||
| Para suscripciones administradas por el Proveedor de soluciones en la nube (CSP) <br> La cuenta con la que inicia sesión se encuentra en la instancia de Azure Active Directory vinculada al área de trabajo | Administrador | Normalmente, el cliente de un CSP |
| Para suscripciones administradas por el Proveedor de soluciones en la nube (CSP) <br> La cuenta con la que inicia sesión no se encuentra en la instancia de Azure Active Directory vinculada al área de trabajo | Colaborador | Normalmente el CSP |

Para más información sobre las definiciones de roles, <sup>1</sup> consulte los [permisos de Azure](../active-directory/role-based-access-control-custom-roles.md). Al evaluar funciones, una acción de `*` no es equivalente a `Microsoft.OperationalInsights/workspaces/*`.

Algunos aspectos relativos a Azure Portal que deben tenerse en cuenta:

* Si inicia sesión en el portal de OMS mediante http://mms.microsoft.com, verá la lista **Seleccione un área de trabajo**. Esta lista solo contiene áreas de trabajo en las que tiene un rol de usuario de Log Analytics. Para ver las áreas de trabajo a las que tiene acceso con las suscripciones de Azure, será preciso que especifique un inquilino como parte de la dirección URL. Por ejemplo: `mms.microsoft.com/?tenant=contoso.com`. A menudo, el identificador del inquilino es la última parte de la dirección de correo electrónico con la que inicia sesión.
* Si desea navegar directamente a un portal al que tenga acceso a través de los permisos de Azure, será preciso que especifique el recurso como parte de la dirección URL. Esta dirección URL se puede obtener mediante PowerShell.

  Por ejemplo: `(Get-AzureRmOperationalInsightsWorkspace).PortalUrl`.

  La dirección URL tiene el siguiente aspecto: `https://eus.mms.microsoft.com/?tenant=contoso.com&resource=%2fsubscriptions%2faaa5159e-dcf6-890a-a702-2d2fee51c102%2fresourcegroups%2fdb-resgroup%2fproviders%2fmicrosoft.operationalinsights%2fworkspaces%2fmydemo12`

### <a name="managing-users-in-the-oms-portal"></a>Administración de usuarios en el portal de OMS
Los usuarios y grupos se administran en la pestaña **Administrar usuarios** de la pestaña **Cuentas** de la página Configuración.   

![administrar usuarios](./media/log-analytics-manage-access/setup-workspace-manage-users.png)


#### <a name="add-a-user-to-an-existing-workspace"></a>Agregar un usuario a un área de trabajo existente
Siga estos pasos para agregar un usuario o un grupo a un área de trabajo.

1. En el portal de OMS, haga clic en el icono **Configuración**.
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Administrar usuarios**.
3. En la sección **Administrar usuarios**, elija el tipo de cuenta que desea agregar: **Cuenta de organización**, **Cuenta Microsoft** o **Soporte técnico de Microsoft**.

   * Si elige Cuenta de Microsoft, escriba la dirección de correo electrónico del usuario asociado a la cuenta de Microsoft.
   * Si elige Cuenta profesional, escriba parte del nombre del grupo o usuario o el alias de correo electrónico, y aparecerá una lista de usuarios y grupos que coinciden con esos datos en un cuadro desplegable. Seleccione un usuario o grupo.
   * Use el servicio de soporte técnico de Microsoft para dar a un ingeniero, o a otro empleado de Microsoft de este servicio, acceso temporal a su área de trabajo para ayudar en la solución de problemas.

     > [!NOTE]
     > Para optimizar el rendimiento, limite a tres el número de grupos de Active Directory asociados a una única cuenta de OMS: uno para los administradores, otro para los colaboradores y otro para los usuarios de solo lectura. Si utiliza más grupos, el rendimiento de Log Analytics podría verse afectado.
     >
     >
4. Elija el tipo de usuario o grupo que desea agregar: **Administrador**, **Colaborador** o **Usuario de solo lectura**.  
5. Haga clic en **Agregar**.

   Si va a agregar una cuenta de Microsoft, se envía una invitación para unir el área de trabajo al correo electrónico que proporcionó. Después de que el usuario siga las instrucciones de la invitación para unirse a OMS, podrá acceder a este área de trabajo.
   Si agrega una cuenta de organización, el usuario podrá acceder a Log Analytics inmediatamente.  

#### <a name="edit-an-existing-user-type"></a>Edición de un tipo de usuario existente
Puede cambiar el rol de cuenta de un usuario asociado a su cuenta de OMS. Tiene las siguientes opciones de rol:

* *Administrador*: puede administrar usuarios, ver y actuar en todas las alertas y agregar y quitar servidores
* *Colaborador*: puede ver y actuar en todas las alertas y agregar y quitar servidores
* *Usuario de solo lectura*: los usuarios con este rol no podrán hacer lo siguiente:

  1. Agregar o quitar soluciones. Se oculta la galería de soluciones.
  2. Agregar, modificar o eliminar iconos en **Mi panel**.
  3. Vea las páginas de **Configuración**. Las páginas están ocultas.
  4. En la vista de búsqueda, la configuración de PowerBI, las búsquedas guardadas y las tareas de alertas están ocultas.

#### <a name="to-edit-an-account"></a>Para editar una cuenta
1. En el portal de OMS, haga clic en el icono **Configuración**.
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Administrar usuarios**.
3. Seleccione el rol del usuario que desea cambiar.
4. En el cuadro de diálogo de confirmación, haga clic en **Sí**.

### <a name="remove-a-user-from-a-workspace"></a>Eliminación de un usuario de un área de trabajo
Siga estos pasos para quitar un usuario de un área de trabajo. La eliminación del usuario no implica el cierre del área de trabajo. En su lugar, elimina la asociación entre ese usuario y el área de trabajo. Si un usuario tiene asociadas varias áreas de trabajo, podrá continuar iniciando sesión en OMS y ver el resto de áreas de trabajo.

1. En el portal de OMS, haga clic en el icono **Configuración**.
2. Haga clic en la pestaña **Cuentas** y, después, en la pestaña **Administrar usuarios**.
3. Haga clic en **Quitar** junto al nombre de usuario que desea eliminar.
4. En el cuadro de diálogo de confirmación, haga clic en **Sí**.

### <a name="add-a-group-to-an-existing-workspace"></a>Incorporación de un grupo a un área de trabajo existente
1. En la sección anterior "Agregar un usuario a un área de trabajo existente", siga los pasos del 1 al 4.
2. En **Elegir usuario/grupo**, seleccione **Grupo**.  
   ![add a group to an existing workspace](./media/log-analytics-manage-access/add-group.png)
3. Especifique el nombre para mostrar o la dirección de correo electrónico del grupo que quiere agregar.
4. Seleccione el grupo en los resultados de la lista y después haga clic en **Agregar**.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Vincular un área de trabajo existente a una suscripción de Azure
Todas las áreas de trabajo creadas después del 26 de septiembre de 2016 deben estar vinculadas a una suscripción de Azure en el momento de la creación. Las áreas de trabajo creadas antes de esta fecha deben estar vinculadas a un área de trabajo la próxima vez que inicie sesión. Al crear el área de trabajo desde Azure Portal, o al vincular el área de trabajo a una suscripción de Azure, su instancia de Azure Active Directory quedará vinculada como su cuenta de organización.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-oms-portal"></a>Para vincular un área de trabajo a una suscripción de Azure en el portal OMS

- Cuando inicie sesión en el portal de OMS, se le pedirá que seleccione una suscripción de Azure. Seleccione la suscripción que desea vincular al área de trabajo y haga clic en **Vínculo**.  
    ![Vínculo a la suscripción de Azure](./media/log-analytics-manage-access/required-link.png)

    > [!IMPORTANT]
    > Para vincular un área de trabajo, su cuenta de Azure debe tener acceso al área de trabajo que quiere vincular.  En otras palabras, la cuenta que use para acceder a Azure Portal deberá ser **la misma** cuenta que usa para acceder al área de trabajo. Si no lo es, consulte [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Para vincular un área de trabajo a una suscripción de Azure en Azure Portal
1. Inicie sesión en [Azure Portal](http://portal.azure.com).
2. Busque **Log Analytics** y selecciónelo.
3. Aparecerá una lista con las áreas de trabajo existentes. Haga clic en **Agregar**.  
   ![lista de áreas de trabajo](./media/log-analytics-manage-access/manage-access-link-azure01.png)
4. En **OMS Workspace** (Área de trabajo de OMS), haga clic en **Or link existing** (O vincular existente).  
   ![vincular existente](./media/log-analytics-manage-access/manage-access-link-azure02.png)
5. Haga clic en **Configurar los valores obligatorios**.  
   ![configure required settings](./media/log-analytics-manage-access/manage-access-link-azure03.png)
6. Aparecerá una lista con las áreas de trabajo que aún no están vinculadas con su cuenta de Azure. Seleccione un área de trabajo.  
   ![seleccionar áreas de trabajo](./media/log-analytics-manage-access/manage-access-link-azure04.png)
7. Si es necesario, puede cambiar los valores de los elementos siguientes:
   * La suscripción
   * Grupos de recursos
   * Ubicación
   * Plan de tarifa   
     ![cambiar valores](./media/log-analytics-manage-access/manage-access-link-azure05.png)
8. Haga clic en **Aceptar**. Ahora, el área de trabajo está vinculada a su cuenta de Azure.

> [!NOTE]
> Si no ve el área de trabajo que quiere vincular, significa que su suscripción de Azure no tiene acceso al área de trabajo que creó mediante el portal de OMS.  Para conceder acceso a esta cuenta desde el portal OMS, consulte [Agregar un usuario a un área de trabajo existente](#add-a-user-to-an-existing-workspace).
>
>

## <a name="change-an-azure-active-directory-organization-for-a-workspace"></a>Cambio de la organización de Azure Active Directory para un área de trabajo

Puede cambiar la organización de Azure Active Directory para un área de trabajo. Cambiar la organización de Azure Active Directory le permite agregar usuarios y grupos desde ese directorio al área de trabajo.

### <a name="to-change-the-azure-active-directory-organization-for-a-workspace"></a>Para cambiar la organización de Azure Active Directory para un área de trabajo

1. En la página Configuración del portal de OMS, haga clic en **Cuentas** y, luego, haga clic en la pestaña **Administrar usuarios**.  
2. Revise la información de las cuentas de organización y haga clic en **Cambiar organización**.  
    ![cambiar organización](./media/log-analytics-manage-access/manage-access-add-adorg01.png)
3. Especifique los datos de identidad del administrador del dominio de Azure Active Directory. A continuación, verá un mensaje de confirmación en el que se indica que el área de trabajo se ha vinculado al dominio de Azure Active Directory.  
    ![confirmación de área de trabajo vinculada](./media/log-analytics-manage-access/manage-access-add-adorg02.png)

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Entender la utilización de datos](log-analytics-usage.md) para saber cómo analizar el volumen de datos recopilados por las soluciones y enviados desde los equipos.
* [Agregue soluciones de administración de Log Analytics desde Azure Marketplace](log-analytics-add-solutions.md) para agregar funcionalidad y recopilar datos.