<properties
	pageTitle="Azure Insights: ejemplos de inicio rápido de PowerShell de Azure Insights | Microsoft Azure"
	description="Los comandos de ejemplo de PowerShell de inicio rápido de Azure Insights pueden ayudarle a acceder rápidamente a las características de supervisión de Azure Insights."
	authors="kamathashwin"
	manager=""
	editor=""
	services="azure-portal"
	documentationCenter="na"/>

<tags
	ms.service="azure-portal"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="ashwink"/>

# Ejemplos de inicio rápido de PowerShell de Azure Insights

En este artículo se muestran comandos de PowerShell de ejemplo para ayudarle a acceder rápidamente a las características de supervisión de Azure Insights. Azure Insights permite escalar automáticamente aplicaciones web, máquinas virtuales y servicios en la nube para enviar notificaciones de alerta o llamar a direcciones URL web en función de los valores de datos de telemetría configurados.

## Configurar PowerShell
Si no lo ha hecho ya, configure PowerShell para que se ejecute en el equipo. Para obtener más información, consulte [Cómo instalar y configurar Azure PowerShell](../powershell-install-configure.md).

## Ejemplos de este artículo

Los ejemplos de este artículo muestran cómo puede usar cmdlets de Azure Insights. También puede consultar toda la lista de cmdlets de Azure Insights (supervisión) en [Cmdlets de Azure Insights](https://msdn.microsoft.com/library/mt282452.aspx).


## Inicio de sesión y uso de suscripciones

En primer lugar, inicie sesión en su suscripción de Azure.

```
Login-AzureRmAccount
```

Para ello, es obligatorio iniciar sesión. Una vez hecho, se muestran el id. predeterminado de la suscripción, el id. de inquilino y la cuenta. Todos los cmdlets de Azure funcionan en el contexto de la suscripción predeterminada. Para ver la lista de suscripciones a las que tiene acceso, use el siguiente comando.

```
Get-AzureRmSubscription
```

Para cambiar el contexto de trabajo a una suscripción diferente, utilice el siguiente comando.

```
Set-AzureRmContext -SubscriptionId <subscriptionid>
```


## Recuperación de registros de auditoría para una suscripción
Utilice el cmdlet `Get-AzureRmLog`. Abajo se muestran algunos ejemplos comunes.

Obtención de entradas de registro desde esta fecha y hora hasta la actual:

```
Get-AzureRmLog -StartTime 2016-03-01T10:30
```

Obtención de entradas de registro entre un intervalo de fecha y hora:

```
Get-AzureRmLog -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtención de entradas de registro de un grupo de recursos específico:

```
Get-AzureRmLog -ResourceGroup 'myrg1'
```

Obtención de entradas de registro de un proveedor de recursos específico entre un intervalo de fecha y hora:

```
Get-AzureRmLog -ResourceProvider 'Microsoft.Web' -StartTime 2015-01-01T10:30 -EndTime 2015-01-01T11:30
```

Obtener todas las entradas de registro con un llamador concreto:

```
Get-AzureRmLog -Caller 'myname@company.com'
```

El comando siguiente recupera los últimos 1000 eventos desde el registro de auditoría:

```
Get-AzureRmLog -MaxEvents 1000
```

`Get-AzureRmLog` es compatible con muchos otros parámetros. Consulte la referencia `Get-AzureRmLog` para obtener más información.

>[AZURE.NOTE] `Get-AzureRmLog` solo proporciona 15 días de historial. El parámetro **-MaxEvents** permite consultar los últimos eventos N más allá de 15 días. Para acceder a eventos de hace más de 15 días, use el SDK o la API de REST (ejemplo de C# usando el SDK). Si no incluye **StartTime**, el valor predeterminado será **EndTime** menos una hora. Si no incluye **EndTime**, el valor predeterminado será la hora actual. Todas las horas se muestran en UTC.

## Recuperación del historial de alertas
Para ver todos los eventos de alerta, puede consultar los registros de Azure Resource Manager (ARM) con los ejemplos siguientes.

```
Get-AzureRmLog -Caller "Microsoft.Insights/alertRules" -DetailedOutput -StartTime 2015-03-01
```

Para ver el historial de una regla de alerta específica, puede utilizar el cmdlet `Get-AzureRmAlertHistory`, con lo que se transmitirá el id. de recurso de la regla de alerta.

```
Get-AzureRmAlertHistory -ResourceId /subscriptions/s1/resourceGroups/rg1/providers/microsoft.insights/alertrules/myalert -StartTime 2016-03-1 -Status Activated
```

El cmdlet `Get-AzureRmAlertHistory` admite varios parámetros. Puede obtener más información en [Get-AlertHistory](https://msdn.microsoft.com/library/mt282453.aspx).


## Recuperación de información sobre reglas de alerta
Todos los comandos siguientes se realizan en un grupo de recursos denominado "montest".

Consulta de todas las propiedades de la regla de alerta:

```
Get-AzureRmAlertRule -Name simpletestCPU -ResourceGroup montest -DetailedOutput
```

Recuperación de todas las alertas de un grupo de recursos:

```
Get-AzureRmAlertRule -ResourceGroup montest
```

Recuperación de todas las reglas de alerta de un recurso de destino. Por ejemplo, todas las reglas de alerta se establecen en una máquina virtual.

```
Get-AzureRmAlertRule -ResourceGroup montest -TargetResourceId /subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig
```

`Get-AzureRmAlertRule` es compatible con otros parámetros. Consulte [Get AlertRule](https://msdn.microsoft.com/library/mt282459.aspx) para obtener más información.

## Creación de reglas de alerta
Puede usar el cmdlet `Add-AlertRule` para crear, actualizar o deshabilitar una regla de alerta.

Puede crear propiedades de correo electrónico y Webhook mediante `New-AzureRmAlertRuleEmail` y `New-AzureRmAlertRuleWebhook` respectivamente. En el cmdlet Alert rule, asígnelos como acciones a la propiedad **Actions** de la regla de alerta.

La sección siguiente contiene un ejemplo que muestra cómo crear una regla de alerta con distintos parámetros.

### Regla de alerta de una métrica
En la tabla siguiente se describen los parámetros y valores utilizados para crear una alerta con una métrica.


|Parámetro|value|
|---|---|
|Nombre|	simpletestdiskwrite|
|Ubicación (Location) de esta regla de alerta|	Este de EE. UU.|
|ResourceGroup|	montest|
|TargetResourceId|	/subscriptions/s1/resourceGroups/montest/providers/Microsoft.Compute/virtualMachines/testconfig|
|Nombre de la métrica (MetricName) de la alerta que se crea|	\\PhysicalDisk(\_Total)\\Disk Writes/sec. Consulte el cmdlet `Get-MetricDefinitions` siguiente sobre cómo recuperar los nombres de métrica exactos| |Operador (Operator)| GreaterThan| |Valor de umbral (Threshold) (recuento o segundos de esta métrica)| 1| |WindowSize (formato hh:mm:ss)| 00:05:00| |Agregador (estadísticas de la métrica que, en este caso, usa el recuento Average)| Average| |Correos electrónicos personalizadas (matriz de cadena)|'foo@example.com','bar@example.com'| |Envío de mensajes de correo electrónico a propietarios, colaboradores y lectores| -SendToServiceOwners|

Creación de una acción de correo electrónico

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Crear una acción de Webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creación de la regla de alerta de la métrica CPU% en una máquina virtual clásica

```
Add-AzureRmMetricAlertRule -Name vmcpu_gt_1 -Location "East US" -ResourceGroup myrg1 -TargetResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.ClassicCompute/virtualMachines/my_vm1 -MetricName "Percentage CPU" -Operator GreaterThan -Threshold 1 -WindowSize 00:05:00 -TimeAggregationOperator Average -Actions $actionEmail, $actionWebhook -Description "alert on CPU > 1%"
```

Recuperación de la regla de alerta

```
Get-AzureRmAlertRule -Name vmcpu_gt_1 -ResourceGroup myrg1 -DetailedOutput
```

El cmdlet Add alert también actualiza la regla si ya existe una para las propiedades determinadas. Para deshabilitar una regla de alerta, incluya el parámetro **-DisableRule**.

### Alerta de eventos de registro de auditoría

>[AZURE.NOTE] Esta característica aún sigue en la fase de vista previa.

En este escenario, deberá enviar correo electrónico cuando un sitio web se haya iniciado correctamente en mi suscripción del grupo de recursos *abhingrgtest123*.

Configuración de una regla de correo electrónico

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com
```

Configuración de una regla de Webhook

```
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri https://example.com?token=mytoken
```

Creación de la regla del evento

```
Add-AzureRmLogAlertRule -Name superalert1 -Location "East US" -ResourceGroup myrg1 -OperationName microsoft.web/sites/start/action -Status Succeeded -TargetResourceGroup abhingrgtest123 -Actions $actionEmail, $actionWebhook
```

Recuperación de la regla de alerta

```
Get-AzureRmAlertRule -Name superalert1 -ResourceGroup myrg1 -DetailedOutput
```

El cmdlet `Add-AlertRule` admite otros parámetros diferentes. Puede obtener más información en la sección sobre el cmdlet [Add-AlertRule](https://msdn.microsoft.com/library/mt282468.aspx).

## Obtención de una lista de métricas disponibles para las alertas
Puede usar el cmdlet `Get-AzureRmMetricDefinition` para ver la lista de todas las métricas de un recurso específico.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id>
```

En el ejemplo siguiente se genera una tabla con el nombre y la unidad de la métrica.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Hay disponible una lista completa de opciones para `Get-AzureRmMetricDefinition` en [Get-MetricDefinitions](https://msdn.microsoft.com/library/mt282458.aspx).


## Creación y administración de la configuración de escalado automático
Un recurso, como una aplicación web, una máquina virtual, un servicio en la nube o un conjunto de escalas de máquina virtual, solo puede tener una configuración de escalado automático ajustada. Sin embargo, cada configuración de escalado automático puede tener varios perfiles. Por ejemplo, uno para un perfil de escala basado en el rendimiento y otro para uno basado en la programación. Cada perfil puede tener varias reglas configuradas. Para obtener más información sobre el escalado automático, consulte [Escalado automático de una aplicación](../cloud-services/cloud-services-how-to-scale.md).

Estos son los pasos que seguirá:

1. Cree reglas.
2. Cree perfiles que asignen las reglas que ha creado anteriormente a los perfiles.
3. Opcional: cree notificaciones de escalado automático configurando las propiedades de Webhook y correo electrónico.
4. Cree una configuración de escalado automático con un nombre en el recurso de destino mediante la asignación de los perfiles y las notificaciones que creó en los pasos anteriores.

En los ejemplos siguientes se muestra cómo puede crear una configuración de escalado automático para un conjunto de escalas de máquina virtual de un sistema operativo de Windows empleando la métrica de uso de CPU.

En primer lugar, cree una regla de escalado horizontal con un aumento del recuento de instancias.

```
$rule1 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 0.01 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Increase -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```		

Después, cree una regla de reducción de escalado con una disminución del recuento de instancias.

```
$rule2 = New-AzureRmAutoscaleRule -MetricName "\Processor(_Total)\% Processor Time" -MetricResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -Operator GreaterThan -MetricStatistic Average -Threshold 2 -TimeGrain 00:01:00 -TimeWindow 00:10:00 -ScaleActionCooldown 00:10:00 -ScaleActionDirection Decrease -ScaleActionScaleType ChangeCount -ScaleActionValue 1
```

Después, cree un perfil para las reglas.

```
$profile1 = New-AzureRmAutoscaleProfile -DefaultCapacity 2 -MaximumCapacity 10 -MinimumCapacity 2 -Rules $rule1,$rule2 -Name "My_Profile"
```

Cree una propiedad de Webhook.

```
$webhook_scale = New-AzureRmAutoscaleWebhook -ServiceUri "https://example.com?mytoken=mytokenvalue"
```

Cree la propiedad de notificación para la configuración de escalado automático, incluidas las de correo electrónico y Webhook que creó anteriormente.

```
$notification1= New-AzureRmAutoscaleNotification -CustomEmails ashwink@microsoft.com -SendEmailToSubscriptionAdministrators SendEmailToSubscriptionCoAdministrators -Webhooks $webhook_scale
```

Finalmente, cree la configuración de escalado automático para agregar el perfil que creó antes.

```
Add-AzureRmAutoscaleSetting -Location "East US" -Name "MyScaleVMSSSetting" -ResourceGroup big2 -TargetResourceId /subscriptions/s1/resourceGroups/big2/providers/Microsoft.Compute/virtualMachineScaleSets/big2 -AutoscaleProfiles $profile1 -Notifications $notification1
```

Para obtener más información sobre cómo administrar la configuración de escalado automático, consulte [Get-AutoscaleSetting](https://msdn.microsoft.com/library/mt282461.aspx).

## Historial de escalado automático
En el ejemplo siguiente se muestra cómo puede ver los eventos de alerta y escalado automático recientes. Utilice la búsqueda de registros de auditoría para ver el historial de escalado automático.

```
Get-AzureRmLog -Caller "Microsoft.Insights/autoscaleSettings" -DetailedOutput -StartTime 2015-03-01
```

Puede usar el cmdlet `Get-AzureRmAutoScaleHistory` para recuperar el historial de escalado automático.

```
Get-AzureRmAutoScaleHistory -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/microsoft.insights/autoscalesettings/myScaleSetting -StartTime 2016-03-15 -DetailedOutput
```

Para obtener más información, consulte [Get-AutoscaleHistory](https://msdn.microsoft.com/library/mt282464.aspx).

### Consulta de los detalles de una configuración de escalado automático
Puede usar el cmdlet `Get-Autoscalesetting` para recuperar más información sobre la configuración de escalado automático.

En el ejemplo siguiente se muestra información sobre todas las configuraciones de escalado automático del grupo de recursos "myrg1".

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -DetailedOutput
```

En el ejemplo siguiente se muestra información sobre todas las configuraciones de escalado automático del grupo de recursos "myrg1" y, en concreto, la configuración de escalado automático con el nombre "MyScaleVMSSSetting".

```
Get-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting -DetailedOutput
```

### Eliminación de una configuración de escalado automático
Puede usar el cmdlet `Remove-Autoscalesetting` para eliminar una configuración de escalado automático.

```
Remove-AzureRmAutoscalesetting -ResourceGroup myrg1 -Name MyScaleVMSSSetting
```

## Administración de perfiles de registro para registros de auditoría

Puede crear un *perfil de registro* y exportar los datos de sus registros de auditoría a una cuenta de almacenamiento y configurar la retención de datos. Si lo desea, también puede transmitir los datos al Centro de eventos. Tenga en cuenta que esta característica se encuentra actualmente en la fase de vista previa y solo puede crear un perfil de registro por suscripción. Puede utilizar los siguientes cmdlets con su suscripción actual para crear y administrar perfiles de registro. También puede elegir una suscripción específica. Aunque PowerShell tiene como valor predeterminado la suscripción actual, puede cambiarla cuando quiera con `Set-AzureRmContext`. Puede configurar los registros de auditoría para enrutar los datos a cualquier cuenta de almacenamiento o al Centro de eventos de dicha suscripción. Los datos se escriben como archivos blob en formato JSON.

### Obtención de un perfil de registro
Para capturar los perfiles de registro existentes, utilice el cmdlet `Get-AzureRmLogProfile`.

### Adición de un perfil de registro sin retención de datos

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia
```

### Eliminación de perfil de registro

```
Remove-AzureRmLogProfile -name my_log_profile_s1
```

### Adición de un perfil de registro con retención de datos

Puede especificar la propiedad **-RetentionInDays** con el número de días, por ejemplo, un entero positivo, que será donde se conservarán los datos.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

### Adición de un perfil de registro retención y EventHub
Además de enrutar los datos a la cuenta de almacenamiento, también puede transmitirlos a un Centro de eventos. Tenga en cuenta que es obligatorio en esta versión de vista previa y configuración de la cuenta de almacenamiento; sin embargo, la configuración del Centro de eventos es opcional.

```
Add-AzureRmLogProfile -Name my_log_profile_s1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -serviceBusRuleId /subscriptions/s1/resourceGroups/Default-ServiceBus-EastUS/providers/Microsoft.ServiceBus/namespaces/mytestSB/authorizationrules/RootManageSharedAccessKey -Locations global,westus,eastus,northeurope,westeurope,eastasia,southeastasia,japaneast,japanwest,northcentralus,southcentralus,eastus2,centralus,australiaeast,australiasoutheast,brazilsouth,centralindia,southindia,westindia -RetentionInDays 90
```

## Configuración de registros de diagnóstico
Muchos servicios de Azure proporcionan otros registros y datos de telemetría, incluidos grupos de seguridad de red de Azure, equilibradores de carga de software, almacenes de claves, servicios de búsqueda de Azure y aplicaciones lógicas. Asimismo, pueden configurarse para guardar los datos en la cuenta de almacenamiento de Azure. Esta operación solo puede realizarse en un nivel de recursos y la cuenta de almacenamiento debe encontrarse en la misma región que el recurso de destino donde se ha ajustado la configuración de diagnóstico.

### Obtención de la configuración de diagnóstico

```
Get-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp
```

Deshabilitación de la configuración de diagnóstico

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $false
```

Habilitación de la configuración de diagnóstico sin retención

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true
```

Habilitación la configuración de diagnóstico con retención

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Logic/workflows/andy0315logicapp -StorageAccountId /subscriptions/s1/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

Habilitación de la configuración de diagnóstico con retención para una categoría de registro específica

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1/resourceGroups/insights-integration/providers/Microsoft.Network/networkSecurityGroups/viruela1 -StorageAccountId /subscriptions/s1/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/sakteststorage -Categories NetworkSecurityGroupEvent -Enable $true -RetentionEnabled $true -RetentionInDays 90
```

<!---HONumber=AcomDC_0406_2016-->