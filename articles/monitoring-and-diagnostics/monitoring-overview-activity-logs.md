---
title: "Información general sobre el registro de actividades de Azure | Microsoft Docs"
description: "Aprenda qué es el registro de actividad de Azure y cómo puede usarlo para comprender los eventos que se producen en su suscripción de Azure."
author: johnkemnetz
manager: orenr
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c274782f-039d-4c28-9ddb-f89ce21052c7
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/02/2017
ms.author: johnkem
ms.openlocfilehash: 8ff9f73fc0732cd2227b7e0cc1091e04d69014eb
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="monitor-subscription-activity-with-the-azure-activity-log"></a>Supervise la actividad de suscripción con Azure Activity Log
**Azure Activity Log** es un registro de suscripción que proporciona información sobre los eventos de nivel de suscripción que se han producido en Azure. Esta incluye una serie de datos, desde datos operativos de Azure Resource Manager hasta actualizaciones en eventos de Estado del servicio. El Registro de actividad se conocía anteriormente como "Registros de auditoría" o "Registros operativos", ya que la categoría Administrativo notifica eventos del plano de control de las suscripciones. Con el Registro de actividades, se pueden determinar los interrogantes “qué, quién y cuándo” de las operaciones de escritura (PUT, POST, DELETE) en los recursos de la suscripción. También puede conocer el estado de la operación y otras propiedades relevantes. El registro de actividad no incluye las operaciones de lectura (GET) ni las operaciones de los recursos que usan el modelo Clásico/"RDFE".

![Comparación de los registros de actividad y otros tipos de registros ](./media/monitoring-overview-activity-logs/Activity_Log_vs_other_logs_v5.png)

Ilustración 1: Comparación de los registros de actividad y otros tipos de registros

El registro de actividad es distinto de los [registros de diagnóstico](monitoring-overview-of-diagnostic-logs.md). El Registro de actividad proporciona datos sobre las operaciones en un recurso desde el exterior (el "plano de control"). Los registros de diagnóstico son emitidos por un recurso y proporcionan información sobre el funcionamiento de dicho recurso (el "plano de datos").

Puede recuperar los eventos del registro de actividad mediante Azure Portal, la CLI, los cmdlets de PowerShell y la API de REST de Azure Monitor.


> [!WARNING]
> El registro de actividad de Azure sirve principalmente para las actividades que se producen en Azure Resource Manager. No hace seguimiento de los recursos que usan el modelo Clásico/RDFE. Algunos tipos de recursos clásicos tienen un proveedor de recursos de servidor proxy en Azure Resource Manager (por ejemplo, Microsoft.ClassicCompute). Si interactúa con un tipo de recurso clásico a través de Azure Resource Manager con estos proveedores de recursos de servidor proxy, las operaciones aparecen en el registro de actividad. Si interactúa con un tipo de recurso clásico en el portal clásico o fuera de los servidores proxy de Azure Resource Manager, sus acciones solo se registran en el registro de operaciones. Solo se puede tener acceso al registro de operaciones desde el portal clásico.
>
>

Vea el vídeo siguiente sobre la introducción del registro de actividad.
> [!VIDEO https://channel9.msdn.com/Blogs/Seth-Juarez/Logs-John-Kemnetz/player]
> 
>

## <a name="categories-in-the-activity-log"></a>Categorías del Registro de actividad
El Registro de actividad contiene varias categorías de datos. Para obtener todos los detalles sobre el esquema de datos de estas categorías, [vea este artículo](monitoring-activity-log-schema.md). Entre ellos se incluyen los siguientes:
* **Administrativo**: esta categoría contiene el registro de todas las operaciones de creación, actualización, eliminación y acción realizadas a través de Resource Manager. Los ejemplos de los tipos de eventos que aparecen en esta categoría incluyen "crear máquina virtual" y "eliminar grupo de seguridad de red". Cada acción realizada por un usuario o una aplicación mediante Resource Manager se modela como una operación en un tipo de recurso determinado. Si el tipo de operación es Write, Delete o Action, los registros de inicio y corrección o error de esa operación se registran en la categoría Administrativo. La categoría Administrativo también incluye los cambios realizados en el control de acceso basado en roles de una suscripción.
* **Estado del servicio**: esta categoría contiene el registro de los incidentes de estado del servicio que se han producido en Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "SQL Azure en el este de EE. UU. está experimentando un tiempo de inactividad". Los eventos de estado del servicio son de cinco variedades: Acción requerida, Recuperación asistida, Incidente, Mantenimiento, Información o Seguridad, y solo aparecen si tiene un recurso en la suscripción que se vaya a ver afectado por el evento.
* **Alerta**: esta categoría contiene el registro de todas las activaciones de alertas de Azure. Un ejemplo del tipo de evento que aparece en esta categoría es "el % de CPU en myVM ha estado por encima de 80 durante los últimos 5 minutos". Varios sistemas de Azure tienen un concepto de alerta: puede definir una regla de algún tipo y recibir una notificación cuando las condiciones coincidan con esa regla. Cada vez que un tipo de alerta de Azure compatible "se activa" o se cumplen las condiciones para generar una notificación, también se inserta un registro de la activación en esta categoría del Registro de actividad.
* **Escalado automático**: esta categoría contiene el registro de los eventos relacionados con el funcionamiento del motor de escalado automático en función de cualquier configuración de escalado automático que haya definido en la suscripción. Un ejemplo del tipo de evento que aparece en esta categoría es "Error de acción de escalado automático". Con el escalado automático puede escalar horizontalmente o reducir horizontalmente de forma automática el número de instancias de un tipo de recurso compatible en función de la hora del día o cargar datos (métricas) mediante una configuración de escalado automático. Cuando se cumplen las condiciones para escalar o reducir verticalmente, se registran los eventos de inicio y corrección o error en esta categoría.
* **Recomendación**: esta categoría contiene eventos de recomendación de determinados tipos de recursos, como sitios web y servidores SQL Server. Estos eventos ofrecen recomendaciones para usar mejor los recursos. Solo recibirá eventos de este tipo si dispone de recursos que emitan recomendaciones.
* **Directiva, Seguridad y Estado de los recursos**: estas categorías no contienen ningún evento; están reservadas para un uso futuro.

## <a name="event-schema-per-category"></a>Esquema de eventos por categoría
[Vea este artículo para comprender el esquema de eventos de Activity Log por categoría.](monitoring-activity-log-schema.md)

## <a name="what-you-can-do-with-the-activity-log"></a>Qué se puede hacer con el registro de actividad
Estas son algunas de las cosas que puede hacer con el registro de actividad:

![Registro de actividad de Azure](./media/monitoring-overview-activity-logs/Activity_Log_Overview_v3.png)


* Verlo y realizar consultas en él en **Azure Portal**.
* [Crear una alerta basada en un evento de Activity Log.](monitoring-activity-log-alerts.md)
* [Transmitirlo al **Centro de eventos**](monitoring-stream-activity-logs-event-hubs.md) para la ingestión en un servicio de terceros o una solución de análisis personalizado como PowerBI.
* Analizarlo en PowerBI con el [**paquete de contenido de PowerBI**](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs/).
* [Guardarlo en una **cuenta de almacenamiento** para archivarlo o inspeccionarlo manualmente](monitoring-archive-activity-log.md). Puede especificar el tiempo de retención (en días) mediante el **perfil de registro**.
* Consultarlo mediante un cmdlet de PowerShell, la CLI o la API de REST.

## <a name="query-the-activity-log-in-the-azure-portal"></a>Consulta de Activity Log en Azure Portal
En Azure Portal puede ver el registro de actividad en varios lugares:
* La **hoja de Activity Log**, a la que se puede acceder buscando Activity Log en "More Services" (Más servicios) en el panel de navegación izquierdo.
* La **hoja Monitor**, que aparece de forma predeterminada en el panel de navegación izquierdo. Activity Log es una de las secciones de esta hoja Azure Monitor.
* La **hoja de recursos** de cualquier recurso, por ejemplo, la hoja de configuración de una máquina virtual. Activity Log es una de las secciones en la mayoría de estas hojas de recursos y al hacer clic en ella automáticamente filtra los eventos relacionados con ese recurso concreto.

En Azure Portal, puede filtrar Activity Log por estos campos:
* Intervalo de tiempo: la hora de inicio y finalización para los eventos.
* Categoría: la categoría de los eventos, como se describió anteriormente.
* Suscripción: uno o más nombres de suscripción de Azure.
* Grupo de recursos: uno o más grupos de recursos dentro de esas suscripciones.
* Recurso (nombre): el nombre de un recurso específico.
* Tipo de recurso: el tipo de recurso, por ejemplo, Microsoft.Compute/virtualmachines.
* Nombre de la operación: el nombre de una operación de Azure Resource Manager, por ejemplo, Microsoft.SQL/servers/Write.
* Gravedad: el nivel de gravedad del evento (informativo, advertencia, error, crítico).
* Evento iniciado por: el 'llamador' o el usuario que realizó la operación.
* Abrir búsqueda: se trata de un cuadro de búsqueda de texto abierto que busca esa cadena en todos los campos de todos los eventos.

Una vez que haya definido un conjunto de filtros, puede guardarlo como una consulta que se conserva entre sesiones por si necesita volver a realizar la misma consulta con esos filtros aplicados de nuevo en el futuro. También puede anclar una consulta en el panel de Azure para estar siempre al tanto de eventos específicos.

Al hacer clic en "Aplicar" se ejecuta la consulta y se muestran todos los eventos que coinciden. Al hacer clic en cualquier evento en la lista, se muestra el resumen de ese evento, así como la notación de objetos JavaScript (JSON) completa y sin procesar de ese evento.

Para ampliar aún más las posibilidades, puede hacer clic en el icono de **Búsqueda de registros**, que muestra los datos de Activity Log en el [análisis de registros de la solución Activity Log Analytics](../log-analytics/log-analytics-activity.md). La hoja Log Analytics ofrece una experiencia básica de exploración/filtro en registros, pero Log Analytics le permite dinamizar, consultar y visualizar sus datos de otras formas más eficaces.

## <a name="export-the-activity-log-with-a-log-profile"></a>Exportación del registro de actividad con perfiles de registro
Un **perfil de registro** controla cómo se exporta el registro de actividad. Con un perfil de registro, puede configurar:

* Dónde se debería enviar el registro de actividad (cuenta de almacenamiento o Event Hubs)
* Qué categorías de eventos (Escritura, Eliminación, Acción) se deberían enviar. *El significado de "categoría" en los eventos de registro de actividad y los perfiles de registro es distinto. En el perfil de registro, "Categoría" representa el tipo de operación (escritura, eliminación, acción). En un evento de registro de actividad, la propiedad de "categoría" representa el origen o el tipo de un evento (por ejemplo, Administration, ServiceHealth, Alert, etc.).*
* Qué regiones (ubicaciones) se deben exportar. Asegúrese de que incluye "global", ya que muchos eventos en Activity Log son eventos globales.
* El tiempo durante el que se debe conservar un registro de actividad en una cuenta de almacenamiento.
    - Una retención de cero días significa que los registros se conservan de forma indefinida. De lo contrario, el valor puede ser cualquier número de días comprendido entre 1 y 2147483647.
    - Si se establecen directivas de retención, pero el almacenamiento de registros en una cuenta de almacenamiento está deshabilitado (por ejemplo, si solo se han seleccionado las opciones de Event Hubs u OMS), las directivas de retención no surten ningún efecto.
    - Las directivas de retención se aplican a diario, por lo que al final de un día (UTC) se eliminan los registros del día que quede fuera de la directiva de retención. Por ejemplo, si tuviera una directiva de retención de un día, se eliminarían los registros de anteayer al principio del día de hoy.

Puede usar una cuenta de almacenamiento o un espacio de nombres de centro de eventos que no esté en la misma suscripción que el que emite los registros. El usuario que configura los ajustes debe tener el acceso de RBAC adecuado a ambas suscripciones.

Estas opciones se pueden configurar a través de la opción de exportación en la hoja de registro de actividad en el portal. También pueden configurarse mediante programación [con la API de REST de Azure Monitor](https://msdn.microsoft.com/library/azure/dn931927.aspx), los cmdlets de PowerShell o con la CLI. Una suscripción solo puede tener un perfil de registro.

### <a name="configure-log-profiles-using-the-azure-portal"></a>Configuración de perfiles de registro mediante Azure Portal
Puede transmitir el registro de actividad a un centro de eventos o almacenarlo en una cuenta de almacenamiento mediante la opción "Exportar" en Azure Portal.

1. Vaya a la hoja de **registro de actividad** mediante el menú en el lado izquierdo del portal.

    ![Ir al registro de actividad en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-navigate.png)
2. Haga clic en el botón **Exportar** en la parte superior de la hoja.

    ![Botón Exportar en el portal](./media/monitoring-overview-activity-logs/activity-logs-portal-export.png)
3. En la hoja que aparece, puede seleccionar:  
  * Las regiones para las que le gustaría exportar eventos
  * La cuenta de almacenamiento en la que desea guardar los eventos
  * El número de días que desea conservar estos eventos en el almacenamiento. Un valor de 0 días conserva los registros para siempre.
  * El espacio de nombres de Service Bus en el que quiere que se cree un centro de eventos para transmitir estos eventos.

     ![Exportar en hoja de registro de actividad](./media/monitoring-overview-activity-logs/activity-logs-portal-export-blade.png)
4. Haga clic en **Guardar** para guardar la configuración. La configuración se aplica inmediatamente a la suscripción.

### <a name="configure-log-profiles-using-the-azure-powershell-cmdlets"></a>Configuración de perfiles de registro mediante cmdlets de Azure PowerShell
#### <a name="get-existing-log-profile"></a>Obtención del perfil de registro existente
```
Get-AzureRmLogProfile
```

#### <a name="add-a-log-profile"></a>Incorporación de un perfil de registro
```
Add-AzureRmLogProfile -Name my_log_profile -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus -RetentionInDays 90 -Categories Write,Delete,Action
```

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| Nombre |Sí |Nombre de su perfil de registro. |
| StorageAccountId |No |Identificador de recurso de la cuenta de almacenamiento donde se debe guardar el registro de actividad. |
| serviceBusRuleId |No |Identificador de regla de Service Bus para el espacio de nombres de Service Bus donde desea que se creen los centros de eventos. Es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Ubicaciones |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. |
| RetentionInDays |Sí |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |
| Categorías |No |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |

#### <a name="remove-a-log-profile"></a>Eliminación de perfil de registro
```
Remove-AzureRmLogProfile -name my_log_profile
```

### <a name="configure-log-profiles-using-the-azure-cross-platform-cli"></a>Configuración de perfiles de registro mediante la CLI multiplataforma de Azure
#### <a name="get-existing-log-profile"></a>Obtención del perfil de registro existente
```
azure insights logprofile list
```
```
azure insights logprofile get --name my_log_profile
```
La propiedad `name` debe ser el nombre del perfil de registro.

#### <a name="add-a-log-profile"></a>Incorporación de un perfil de registro
```
azure insights logprofile add --name my_log_profile --storageId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Storage/storageAccounts/my_storage --serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey --locations global,westus,eastus,northeurope --retentionInDays 90 –categories Write,Delete,Action
```

| Propiedad | Obligatorio | Description |
| --- | --- | --- |
| name |Sí |Nombre de su perfil de registro. |
| storageId |No |Identificador de recurso de la cuenta de almacenamiento donde se debe guardar el registro de actividad. |
| serviceBusRuleId |No |Identificador de regla de Service Bus para el espacio de nombres de Service Bus donde desea que se creen los centros de eventos. Es una cadena con este formato: `{service bus resource ID}/authorizationrules/{key name}`. |
| Ubicaciones |Sí |Lista separada por comas de las regiones para las que desea recopilar eventos del registro de actividad. |
| RetentionInDays |Sí |Número de días que deben retenerse los eventos, entre 1 y 2147483647. Con el valor cero, se almacenan los registros indefinidamente. |
| Categorías |No |Lista separada por comas de las categorías de eventos que deben recopilarse. Los valores posibles son Write, Delete y Action. |

#### <a name="remove-a-log-profile"></a>Eliminación de perfil de registro
```
azure insights logprofile delete --name my_log_profile
```

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre el registro de actividad (antes, Registros de auditoría)](../azure-resource-manager/resource-group-audit.md)
* [Transmisión del registro de actividad de Azure a Event Hubs](monitoring-stream-activity-logs-event-hubs.md)
