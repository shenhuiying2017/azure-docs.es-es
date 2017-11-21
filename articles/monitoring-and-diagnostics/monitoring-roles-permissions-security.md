---
title: "Introducción a roles, permisos y seguridad con Azure Monitor | Microsoft Docs"
description: "Obtenga información sobre cómo utilizar los permisos y los roles integrados de Azure Monitor para restringir el acceso a los recursos de supervisión."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 2686e53b-72f0-4312-bcd3-3dc1b4a9b912
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: johnkem
ms.openlocfilehash: f8767073bb7a6723088bb2727346d23ec8872cd1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="get-started-with-roles-permissions-and-security-with-azure-monitor"></a>Introducción a roles, permisos y seguridad con Azure Monitor
Muchos equipos necesitan regular estrictamente el acceso a los datos y la configuración de supervisión. Por ejemplo, si tiene miembros del equipo que trabajan exclusivamente en la supervisión (ingenieros de soporte técnico o ingenieros de operaciones de desarrollo) o si usa un proveedor de servicios administrados, puede concederles acceso solo a datos de supervisión, mientras restringe su capacidad para crear, modificar o eliminar recursos. En este artículo se explica cómo aplicar rápidamente un rol RBAC de supervisión integrado a un usuario en Azure o crear un rol personalizado propio para un usuario que necesita permisos de supervisión limitados. Después se describen las consideraciones de seguridad para los recursos relacionados con Azure Monitor y cómo puede limitar el acceso a los datos que contienen.

## <a name="built-in-monitoring-roles"></a>Roles de supervisión integrados
Los roles integrados en Azure Monitor están diseñados para ayudar a limitar el acceso a recursos de una suscripción, sin impedir que los responsables de la infraestructura de supervisión obtengan y configuren los datos que necesitan. Azure Monitor proporciona dos roles de fábrica: un lector de supervisión y un colaborador de supervisión.

### <a name="monitoring-reader"></a>Lector de supervisión
Las personas asignadas al rol Lector de supervisión pueden ver todos los datos de supervisión en una suscripción, pero no pueden modificar cualquier recurso o editar cualquier configuración relacionada con la supervisión de recursos. Este rol es adecuado para los usuarios de una organización, como los ingenieros de soporte técnico u operaciones que necesitan tener la capacidad de:

* Ver paneles de supervisión en el portal y crear sus propios paneles de supervisión privados.
* Consultar métricas con la [API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931930.aspx), los [cmdlets de PowerShell](insights-powershell-samples.md) o la [CLI multiplataforma](insights-cli-samples.md).
* Consultar el registro de actividades a través del portal, la API de REST de Azure Monitor, los cmdlets de PowerShell o la CLI multiplataforma.
* Ver la [configuración de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) de un recurso.
* Ver el [perfil de registro](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) de una suscripción.
* Consultar la configuración de escalado automático.
* Consultar la configuración y actividad de alertas.
* Acceder a datos de Application Insights y ver los datos en AI Analytics.
* Buscar datos del área de trabajo de Log Analytics, incluidos los datos de uso del área de trabajo.
* Ver grupos de administración de Log Analytics.
* Recuperar el esquema de búsqueda de Log Analytics.
* Enumerar los Intelligence Pack de Log Analytics.
* Recuperar y ejecutar las búsquedas guardadas en Log Analytics.
* Recuperar la configuración de almacenamiento de Log Analytics.

> [!NOTE]
> Este rol no otorga acceso de lectura a los datos de registro que se han transmitido a un centro de eventos o que están almacenados en una cuenta de almacenamiento. [Vea la información a continuación](#security-considerations-for-monitoring-data) para saber cómo configurar el acceso a estos recursos.
> 
> 

### <a name="monitoring-contributor"></a>Colaborador de supervisión
Las personas asignadas al rol Colaborador de supervisión pueden ver todos los datos de supervisión en una suscripción y crear o modificar la configuración de supervisión, pero no pueden modificar los demás recursos. Este rol es un superconjunto del rol Lector de supervisión y es adecuado para los miembros del equipo de supervisión de una administración o los proveedores de servicios administrados que, además de los permisos anteriores, también necesitan tener la capacidad de:

* Publicar paneles de supervisión como un panel compartido.
* Determinar la [configuración de diagnóstico](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings) de un recurso.*
* Establecer el [perfil de registro](monitoring-overview-activity-logs.md#export-the-activity-log-with-a-log-profile) de una suscripción.*
* Definir la configuración y actividad de alertas.
* Crear componentes y pruebas web de Application Insights.
* Mostrar las claves compartidas del área de trabajo de Log Analytics.
* Habilitar o deshabilitar Intelligence Pack de Log Analytics.
* Crear y eliminar y ejecutar las búsquedas guardadas en Log Analytics.
* Crear y eliminar la configuración de almacenamiento de Log Analytics.

*Al usuario también se le debe conceder el permiso ListKeys por separado en el recurso de destino (cuenta de almacenamiento o espacio de nombres del centro de eventos) para definir la configuración del diagnóstico o el perfil de registros.

> [!NOTE]
> Este rol no otorga acceso de lectura a los datos de registro que se han transmitido a un centro de eventos o que están almacenados en una cuenta de almacenamiento. [Vea la información a continuación](#security-considerations-for-monitoring-data) para saber cómo configurar el acceso a estos recursos.
> 
> 

## <a name="monitoring-permissions-and-custom-rbac-roles"></a>Roles RBAC personalizados y permisos de supervisión
Si los roles integrados anteriores no satisfacen las necesidades exactas de su equipo, puede [crear un rol RBAC personalizado](../active-directory/role-based-access-control-custom-roles.md) con permisos más granulares. A continuación se muestran las operaciones comunes de RBAC de Azure Monitor con sus descripciones.

| Operación | Descripción |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Leer, escribir o eliminar grupos de acción. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Leer, escribir o eliminar alertas de registro de actividad. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Leer, escribir o eliminar reglas de alertas (alertas de métricas). |
| Microsoft.Insights/AlertRules/Incidents/Read |Enumerar los incidentes (historial de la regla de alerta desencadenada) de reglas de alerta. Solo se aplica en el portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Configuración de escalado automático de lectura, escritura y eliminación. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Configuración de diagnóstico de lectura, escritura y eliminación. |
| Microsoft.Insights/EventCategories/Read |Enumerar todas las categorías posibles en el registro de actividad. Lo utiliza Azure Portal. |
| Microsoft.Insights/eventtypes/digestevents/Read |Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. |
| Microsoft.Insights/eventtypes/values/Read |Enumerar eventos del registro de actividades (eventos de administración) de una suscripción. Este permiso es aplicable para el acceso mediante programación y mediante el portal al registro de actividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Leer, escribir o eliminar configuración de diagnóstico para registros de flujo de red. |
| Microsoft.Insights/LogDefinitions/Read |Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Leer, escribir o eliminar perfiles de registro (registro de actividad de streaming para la cuenta de almacenamiento o centro de eventos). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Leer, escribir o eliminar alertas de métricas casi en tiempo real (versión preliminar pública). |
| Microsoft.Insights/MetricDefinitions/Read |Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
| Microsoft.Insights/Metrics/Read |Leer las métricas de un recurso. |
| Microsoft.Insights/Register/Action |Registrar el proveedor de recursos de Azure Monitor. |


> [!NOTE]
> El acceso a las alertas, la configuración de diagnóstico y las métricas de un recurso requiere que el usuario tenga acceso de lectura al tipo de recurso y el ámbito de ese recurso. La definición (“escritura”) de una configuración de diagnóstico o un perfil de registro que se archiva en una cuenta de almacenamiento o se transmite a centros de eventos requiere que el usuario tenga también el permiso ListKeys en el recurso de destino.
> 
> 

Por ejemplo, mediante la tabla anterior se podría crear un rol RBAC personalizado para un "lector de registro de actividades" similar al siguiente:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzureRmRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Consideraciones de seguridad para datos de supervisión
Los datos de supervisión, en particular los archivos de registro, pueden obtener información confidencial, como los nombres de usuario o las direcciones IP. Los datos de supervisión de Azure se presentan en tres formatos básicos:

1. El registro de actividades, que describe todas las acciones de plano de control en su suscripción de Azure.
2. Los registros de diagnóstico, que son registros emitidos por un recurso.
3. Métricas, que emiten los recursos.

Estos tres tipos de datos pueden almacenarse en una cuenta de almacenamiento o transmitirse a un centro de eventos, y ambos son recursos de Azure de uso general. Dado que son recursos de uso general, su creación, eliminación o el acceso a ellos constituyen una operación privilegiada que suele estar reservada a un administrador. Se recomienda utilizar los procedimientos siguientes para recursos relacionados con la supervisión para impedir el uso indebido:

* Utilizar una cuenta de almacenamiento dedicada a datos de supervisión. Si necesita separar los datos de supervisión en varias cuentas de almacenamiento, nunca comparta el uso de una cuenta de almacenamiento entre los datos de supervisión y de otro tipo, ya que se podría conceder acceso accidentalmente a datos no relacionados con la supervisión a aquellos que solo necesitan tener acceso a datos que no son de supervisión (por ejemplo, un SIEM de terceros).
* Utilizar un espacio de nombres exclusivo y dedicado de Service Bus o Event Hubs en toda la configuración de diagnóstico, por la misma razón anterior.
* Limitar el acceso a las cuentas de almacenamiento o a centros de eventos relacionados con la supervisión, manteniéndolos en un grupo de recursos independiente y [utilizar el ámbito](../active-directory/role-based-access-control-what-is.md#basics-of-access-management-in-azure) en los roles de supervisión para limitar el acceso a ese grupo de recursos exclusivamente.
* No conceder nunca el permiso ListKeys para las cuentas de almacenamiento o los centros de eventos en el ámbito de la suscripción cuando un usuario solo necesita acceso a los datos de supervisión. En su lugar, conceder estos permisos al usuario en un ámbito de recurso o grupo de recursos (si tiene un grupo de recursos de supervisión dedicado).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Restricción del acceso a cuentas de almacenamiento relacionadas con la supervisión
Cuando un usuario o aplicación necesita acceso a datos de supervisión en una cuenta de almacenamiento, debe [generar una SAS de la cuenta](https://msdn.microsoft.com/library/azure/mt584140.aspx) en la cuenta de almacenamiento que contenga los datos de supervisión con acceso de solo lectura de nivel de servicio a Blob Storage. En PowerShell, podría quedar de manera similar a:

```powershell
$context = New-AzureStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzureStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

Puede proporcionar el token a la entidad que necesita leer desde la cuenta de almacenamiento, y esta puede enumerar y leer desde todos los blobs de dicha cuenta de almacenamiento.

O bien, si necesita controlar este permiso con RBAC, puede conceder a dicha entidad el permiso Microsoft.Storage/storageAccounts/listkeys/action para esa cuenta de almacenamiento determinada. Esto es necesario para los usuarios que necesitan tener la capacidad de definir una configuración de diagnóstico o un perfil de registro a fin de archivar en una cuenta de almacenamiento. Por ejemplo, podría crear el siguiente rol RBAC personalizado para un usuario o una aplicación que solo necesita leer desde una cuenta de almacenamiento:

```powershell
$role = Get-AzureRmRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzureRmRoleDefinition -Role $role 
```

> [!WARNING]
> El permiso ListKeys permite al usuario enumerar las claves de la cuenta de almacenamiento principal y secundaria. Estas claves conceden al usuario todos los permisos firmados (leer, escribir, crear blobs, eliminar blobs, etc.) en todos los servicios suscritos (blob, cola, tabla y archivo) en esa cuenta de almacenamiento. Se recomienda usar una SAS de cuenta, como se ha descrito anteriormente, cuando sea posible.
> 
> 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Restricción del acceso a centros de eventos relacionados con la supervisión
Se puede seguir un patrón similar con los centros de eventos, pero primero debe crear una regla de autorización de escucha dedicada. Si desea conceder acceso a una aplicación que solo necesita escuchar a centros de eventos relacionados con la supervisión, haga lo siguiente:

1. Cree una directiva de acceso compartido en los centros de eventos que se crearon para streaming de datos de supervisión con notificaciones de escucha exclusivamente. Esto se puede hacer en el portal. Por ejemplo, podría llamarlo "monitoringReadOnly." Si es posible, debe proporcionar la clave directamente al consumidor y omitir el paso siguiente.
2. Si el consumidor debe tener la capacidad de obtener la clave ad hoc, conceda al usuario la acción ListKeys para ese centro de eventos. Esto es necesario para los usuarios que necesitan tener la capacidad de definir una configuración de diagnóstico o un perfil de registro para realizar transmisiones a los centros de eventos. Por ejemplo, podría crear una regla de RBAC:
   
   ```powershell
   $role = Get-AzureRmRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.ServiceBus/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzureRmRoleDefinition -Role $role 
   ```

## <a name="next-steps"></a>Pasos siguientes
* [Consulte información sobre RBAC y permisos en Resource Manager](../active-directory/role-based-access-control-what-is.md)
* [Lea la información general sobre supervisión en Azure](monitoring-overview.md)

