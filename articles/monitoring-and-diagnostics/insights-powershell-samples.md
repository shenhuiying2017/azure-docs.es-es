---
title: "Ejemplos de inicio rápido de PowerShell de Azure Monitor | Microsoft Docs"
description: "Use PowerShell para acceder a las características de Azure Monitor, como el escalado automático, alertas, webhooks y buscar registros de actividad."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: c0761814-7148-4ab5-8c27-a2c9fa4cfef5
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fea73e15543b2d0284c94118e01415870b5396ff


---
# <a name="azure-monitor-powershell-quick-start-samples"></a>Ejemplos de inicio rápido de PowerShell de Azure Monitor
En este artículo se muestran comandos de PowerShell de ejemplo para ayudarle a acceder a las características de Azure Monitor. Azure Monitor permite escalar automáticamente Cloud Services, Virtual Machines y Web Apps para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados.

> [!NOTE]
> Azure Monitor es el nuevo nombre de lo que se conocía como "Azure Insights" hasta el 25 de septiembre de 2016. Sin embargo, los espacios de nombres y, por tanto, los siguientes comandos aún contienen el término "insights".
> 
> 

## <a name="set-up-powershell"></a>Configurar PowerShell
Si no lo ha hecho ya, configure PowerShell para que se ejecute en el equipo. Para obtener más información, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md) .

## <a name="examples-in-this-article"></a>Ejemplos de este artículo
Los ejemplos de este artículo muestran cómo puede usar cmdlets de Azure Monitor. También puede consultar toda la lista de cmdlets de PowerShell de Azure Monitor en [Azure Monitor Cmdlets](https://msdn.microsoft.com/library/azure/mt282452#40v=azure.200#41.aspx) (Cmdlets de Azure Monitor).

## <a name="sign-in-and-use-subscriptions"></a>Inicio de sesión y uso de suscripciones
En primer lugar, inicie sesión en su suscripción de Azure.

```PowerShell
Login-AzureRmAccount
```

Para ello, es obligatorio iniciar sesión. Una vez hecho, se muestran el id. predeterminado de la suscripción, el id. de inquilino y la cuenta. Todos los cmdlets de Azure funcionan en el contexto de la suscripción predeterminada. Para ver la lista de suscripciones a las que tiene acceso, use el siguiente comando.

```PowerShell
Get-AzureRmSubscription
```

Para cambiar el contexto de trabajo a una suscripción diferente, utilice el siguiente comando.

```PowerShell
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## <a name="retrieve-audit-logs-for-a-subscription"></a>Recuperación de registros de auditoría para una suscripción
Utilice el cmdlet `Get-AzureRmLog` .  Abajo se muestran algunos ejemplos comunes.

Obtención de entradas de registro desde esta fecha y hora hasta la actual:

```PowerShell
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obtención de entradas de registro entre un intervalo de fecha y hora:

```PowerShell
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtención de entradas de registro de un grupo de recursos específico:

```PowerShell
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obtención de entradas de registro de un proveedor de recursos específico entre un intervalo de fecha y hora:

```PowerShell
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtener todas las entradas de registro con un llamador concreto:

```PowerShell
Get-AzureRmLog -Caller 'myname@company.com'
```

El comando siguiente recupera los últimos 1000 eventos desde el registro de actividades:

```PowerShell
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` es compatible con muchos otros parámetros. Consulte la referencia `Get-AzureRmLog` para obtener más información.

> [!NOTE]
> `Get-AzureRmLog` solo proporciona 15 días de historial. El parámetro **-MaxEvents** permite consultar los últimos eventos N más allá de 15 días. Para acceder a eventos de hace más de 15 días, use el SDK o la API de REST (ejemplo de C# usando el SDK). Si no incluye **StartTime**, el valor predeterminado será **EndTime** menos una hora. Si no incluye **EndTime**, el valor predeterminado será la hora actual. Todas las horas se muestran en UTC.
> 
> 

## <a name="retrieve-alerts-history"></a>Recuperación del historial de alertas
Para ver todos los eventos de alerta, puede consultar los registros de Azure Resource Manager con los ejemplos siguientes.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver el historial de una regla de alerta específica, puede utilizar el cmdlet `Get-AzureRmAlertHistory`, con lo que se transmitirá el id. de recurso de la regla de alerta.

```PowerShell
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

El cmdlet `Get-AzureRmAlertHistory` admite varios parámetros. Puede obtener más información en [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).

## <a name="retrieve-information-on-alert-rules"></a>Recuperación de información sobre reglas de alerta
Todos los comandos siguientes se realizan en un grupo de recursos denominado "montest".

Consulta de todas las propiedades de la regla de alerta:

```PowerShell
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperación de todas las alertas de un grupo de recursos:

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperación de todas las reglas de alerta de un recurso de destino. Por ejemplo, todas las reglas de alerta se establecen en una máquina virtual.

```PowerShell
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` es compatible con otros parámetros. Consulte [Get AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obtener más información.

## <a name="create-alert-rules"></a>Creación de reglas de alerta
Puede usar el cmdlet `Add-AlertRule` para crear, actualizar o deshabilitar una regla de alerta.

Puede crear propiedades de correo electrónico y webhook mediante `New-AzureRmAlertRuleEmail` y `New-AzureRmAlertRuleWebhook` respectivamente. En el cmdlet Alert rule, asígnelos como acciones a la propiedad **Actions** de la regla de alerta.

La sección siguiente contiene un ejemplo que muestra cómo crear una regla de alerta con distintos parámetros.

### <a name="alert-rule-on-a-metric"></a>Regla de alerta de una métrica
En la tabla siguiente se describen los parámetros y valores utilizados para crear una alerta con una métrica.

| Parámetro | value |
| --- | --- |
| Nombre |simpletestdiskwrite |
| Ubicación (Location) de esta regla de alerta |Este de EE. UU. |
| ResourceGroup |montest |
| TargetResourceId |/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig |
| Nombre de la métrica (MetricName) de la alerta que se crea |\Disco físico(_Total)\Escrituras de disco/s Consulte el cmdlet `Get-MetricDefinitions` acerca de cómo obtener los nombres exactos de las métricas |
| operator |GreaterThan |
| Valor de umbral (número por segundo para esta métrica) |1 |
| WindowSize (formato hh:mm:ss) |00:05:00 |
| aggregator (estadística de la métrica que, en este caso, usa el recuento medio) |Media |
| mensajes de correo electrónico personalizados (matriz de cadenas) |'foo@example.com','bar@example.com' |
| enviar correo electrónico a los propietarios, colaboradores y lectores |-SendToServiceOwners |

Creación de una acción de correo electrónico

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Crear una acción de Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creación de la regla de alerta de la métrica CPU% en una máquina virtual clásica

```PowerShell
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperación de la regla de alerta

```PowerShell
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

El cmdlet Add alert también actualiza la regla si ya existe una para las propiedades determinadas. Para deshabilitar una regla de alerta, incluya el parámetro **-DisableRule**.

### <a name="alert-on-activity-log-event"></a>Alerta de evento del registro de actividades
> [!NOTE]
> Esta característica aún sigue en la fase de vista previa.
> 
> 

En este escenario, deberá enviar un mensaje de correo electrónico cuando un sitio web se inicie correctamente en mi suscripción del grupo de recursos *abhingrgtest123*.

Configuración de una regla de correo electrónico

```PowerShell
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configuración de una regla de Webhook

```PowerShell
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creación de la regla del evento

```PowerShell
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recuperación de la regla de alerta

```PowerShell
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

El cmdlet `Add-AlertRule` admite otros parámetros diferentes. Para más información, consulte [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## <a name="get-a-list-of-available-metrics-for-alerts"></a>Obtención de una lista de métricas disponibles para las alertas
Puede usar el cmdlet `Get-AzureRmMetricDefinition` para ver la lista de todas las métricas de un recurso específico.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

En el ejemplo siguiente se genera una tabla con el nombre y la unidad de la métrica.

```PowerShell
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Hay disponible una lista completa de opciones para `Get-AzureRmMetricDefinition` en [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).

## <a name="create-and-manage-autoscale-settings"></a>Creación y administración de la configuración de escalado automático
Un recurso, como una aplicación web, una máquina virtual, un servicio en la nube o un conjunto de escalas de máquina virtual, solo puede tener una configuración de escalado automático ajustada.
Sin embargo, cada configuración de escalado automático puede tener varios perfiles. Por ejemplo, uno para un perfil de escala basado en el rendimiento y otro para uno basado en la programación. Cada perfil puede tener varias reglas configuradas. Para obtener más información sobre el escalado automático, consulte [Escalado automático de una aplicación](../cloud-services/cloud-services-how-to-scale.md).

Estos son los pasos que seguirá:

1. Cree reglas.
2. Cree perfiles que asignen las reglas que ha creado anteriormente a los perfiles.
3. Opcional: cree notificaciones de escalado automático configurando las propiedades de Webhook y correo electrónico.
4. Cree una configuración de escalado automático con un nombre en el recurso de destino mediante la asignación de los perfiles y las notificaciones que creó en los pasos anteriores.

En los ejemplos siguientes se muestra cómo puede crear una configuración de escalado automático para un conjunto de escalas de máquina virtual de un sistema operativo de Windows empleando la métrica de uso de CPU.

En primer lugar, cree una regla de escalado horizontal con un aumento del recuento de instancias.

```PowerShell
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```        

Después, cree una regla de reducción de escalado con una disminución del recuento de instancias.

```PowerShell
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Después, cree un perfil para las reglas.

```PowerShell
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Cree una propiedad de Webhook.

```PowerShell
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Cree la propiedad de notificación para la configuración de escalado automático, incluidas las de correo electrónico y Webhook que creó anteriormente.

```PowerShell
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Finalmente, cree la configuración de escalado automático para agregar el perfil que creó antes.

```PowerShell
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obtener más información sobre cómo administrar la configuración de escalado automático, consulte [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## <a name="autoscale-history"></a>Historial de escalado automático
En el ejemplo siguiente se muestra cómo puede ver los eventos de alerta y escalado automático recientes. Utilice la búsqueda de registros de actividades para ver el historial de escalado automático.

```PowerShell
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Puede usar el cmdlet `Get-AzureRmAutoScaleHistory` para recuperar el historial de escalado automático.

```PowerShell
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obtener más información, consulte [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### <a name="view-details-for-an-autoscale-setting"></a>Consulta de los detalles de una configuración de escalado automático
Puede usar el cmdlet `Get-Autoscalesetting` para recuperar más información sobre la configuración de escalado automático.

En el ejemplo siguiente se muestra información sobre todas las configuraciones de escalado automático del grupo de recursos "myrg1".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

En el ejemplo siguiente se muestra información sobre todas las configuraciones de escalado automático del grupo de recursos "myrg1" y, en concreto, la configuración de escalado automático con el nombre "MyScaleVMSSSetting".

```PowerShell
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### <a name="remove-an-autoscale-setting"></a>Eliminación de una configuración de escalado automático
Puede usar el cmdlet `Remove-Autoscalesetting` para eliminar una configuración de escalado automático.

```PowerShell
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## <a name="manage-log-profiles-for-activity-log"></a>Administración de perfiles de registro para el registro de actividades
Puede crear un *perfil de registro* y exportar los datos de su registro de actividades a una cuenta de almacenamiento y configurar la retención de datos. Si lo desea, también puede transmitir los datos al Centro de eventos. Tenga en cuenta que esta característica se encuentra actualmente en la fase de vista previa y solo puede crear un perfil de registro por suscripción. Puede utilizar los siguientes cmdlets con su suscripción actual para crear y administrar perfiles de registro. También puede elegir una suscripción específica. Aunque PowerShell tiene como valor predeterminado la suscripción actual, puede cambiarla cuando quiera con `Set-AzureRmContext`. Puede configurar el registro de actividades para enrutar los datos a cualquier cuenta de almacenamiento o al centro de eventos de dicha suscripción. Los datos se escriben como archivos blob en formato JSON.

### <a name="get-a-log-profile"></a>Obtención de un perfil de registro
Para capturar los perfiles de registro existentes, use el cmdlet `Get-AzureRmLogProfile` .

### <a name="add-a-log-profile-without-data-retention"></a>Adición de un perfil de registro sin retención de datos
```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### <a name="remove-a-log-profile"></a>Eliminación de perfil de registro
```PowerShell
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### <a name="add-a-log-profile-with-data-retention"></a>Adición de un perfil de registro con retención de datos
Puede especificar la propiedad **-RetentionInDays** con el número de días, como un entero positivo, que se conservarán los datos.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### <a name="add-log-profile-with-retention-and-eventhub"></a>Adición de un perfil de registro retención y EventHub
Además de enrutar los datos a la cuenta de almacenamiento, también puede transmitirlos a un Centro de eventos. Tenga en cuenta que es obligatorio en esta versión de vista previa y configuración de la cuenta de almacenamiento; sin embargo, la configuración del Centro de eventos es opcional.

```PowerShell
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## <a name="configure-diagnostics-logs"></a>Configuración de registros de diagnóstico
Muchos servicios de Azure proporcionan otros registros y datos de telemetría, incluidos grupos de seguridad de red de Azure, equilibradores de carga de software, almacenes de claves, servicios de búsqueda de Azure y aplicaciones lógicas. Asimismo, pueden configurarse para guardar los datos en  la cuenta de almacenamiento de Azure. Esta operación solo puede realizarse en un nivel de recursos y la cuenta de almacenamiento debe encontrarse en la misma región que el recurso de destino donde se ha ajustado la configuración de diagnóstico.

### <a name="get-diagnostic-setting"></a>Obtención de la configuración de diagnóstico
```PowerShell
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Deshabilitación de la configuración de diagnóstico

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitación de la configuración de diagnóstico sin retención

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitación la configuración de diagnóstico con retención

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitación de la configuración de diagnóstico con retención para una categoría de registro específica

```PowerShell
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```



<!--HONumber=Nov16_HO3-->


