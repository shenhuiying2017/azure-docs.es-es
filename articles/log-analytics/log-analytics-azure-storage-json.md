---
title: Análisis de los registros de diagnóstico de Azure mediante Log Analytics | Microsoft Docs
description: Log Analytics lee los registros de servicios de Azure que escriben registros de diagnóstico de Azure en formato JSON en Blob Storage.
services: log-analytics
documentationcenter: ''
author: bandersmsft
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2016
ms.author: banders

---
# <a name="analyze-azure-diagnostic-logs-using-log-analytics"></a>Análisis de los registros de diagnóstico de Azure mediante Log Analytics
Log Analytics recopila registros para los siguientes servicios de Azure, que escriben [registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) en formato JSON en Blob Storage:

* Automatización (vista previa)
* Almacén de claves (vista previa)
* Puerta de enlace de aplicaciones (vista previa)
* Grupo de seguridad de red (vista previa)

Las siguientes secciones le guiarán en el uso de PowerShell para:

* Configurar Log Analytics para recopilar los registros del almacenamiento para cada recurso  
* Habilitar la solución de Log Analytics para el servicio de Azure

Antes de que Log Analytics pueda recopilar los datos de estos recursos, es necesario habilitar Diagnósticos de Azure. Utilice el cmdlet `Set-AzureRmDiagnosticSetting` para habilitar el registro.

Para más información acerca de cómo habilitar el registro de diagnóstico, consulte los artículos siguientes:

* [Almacén de claves](../key-vault/key-vault-logging.md)
* [Puerta de enlace de aplicaciones](../application-gateway/application-gateway-diagnostics.md)
* [Grupo de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md)

Esta documentación también incluye detalles sobre:

* La solución de problemas de recopilación de datos
* La interrupción de la recopilación de datos

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configuración de Log Analytics para recopilar registros de diagnóstico de Azure
La recopilación de registros de estos servicios y la habilitación de la solución para visualizarlos se realiza mediante scripts de PowerShell.

En el ejemplo siguiente se habilita el registro de todos los recursos admitidos

```
# update to be the storage account that logs will be written to. Storage account must be in the same region as the resource to monitor
# format is similar to "/subscriptions/ec11ca60-ab12-345e-678d-0ea07bbae25c/resourceGroups/Default-Storage-WestUS/providers/Microsoft.Storage/storageAccounts/mystorageaccount"
$storageAccountId = ""

$supportedResourceTypes = ("Microsoft.Automation/AutomationAccounts", "Microsoft.KeyVault/Vaults", "Microsoft.Network/NetworkSecurityGroups", "Microsoft.Network/ApplicationGateways")

# update location to match your storage account location
$resources = Get-AzureRmResource | where { $_.ResourceType -in $supportedResourceTypes -and $_.Location -eq "westus" }

foreach ($resource in $resources) {
    Set-AzureRmDiagnosticSetting -ResourceId $resource.ResourceId -StorageAccountId $storageAccountId -Enabled $true -RetentionEnabled $true -RetentionInDays 1
}
```


Para algunos recursos, es posible llevar a cabo la configuración anterior de Azure Portal si se siguen los pasos que se describen en [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-diagnostic-logs).

## <a name="configure-log-analytics-to-collect-azure-diagnostic-logs"></a>Configuración de Log Analytics para recopilar registros de diagnóstico de Azure
Hemos proporcionado un módulo de script de PowerShell que exporta dos cmdlets para ayudar a configurar Log Analytics:

1. `Add-AzureDiagnosticsToLogAnalyticsUI` le pide que especifique distintos valores y realiza configuraciones sencillas
2. `Add-AzureDiagnosticsToLogAnalytics` toma como valores los recursos que se deben supervisar y después configura Log Analytics  

### <a name="pre-requisites"></a>Requisitos previos
1. Azure PowerShell con la versión 1.0.8 o posterior de los cmdlets de Visión operativa.
   * [Instalación y configuración de Azure PowerShell](../powershell-install-configure.md)
   * Comprobar la versión de los cmdlets: `Import-Module AzureRM.OperationalInsights -MinimumVersion 1.0.8 `
2. El registro de diagnósticos está configurado para el recurso de Azure que desea supervisar. Use `Set-AzureRmDiagnosticSetting` o consulte el artículo sobre el [uso de Log Analytics para recopilar datos de las cuentas de Azure Storage](log-analytics-azure-storage.md) para más información sobre cómo se habilita el diagnóstico.
3. Un área de trabajo de [Log Analytics](https://portal.azure.com/#create/Microsoft.LogAnalyticsOMS)  
4. El módulo AzureDiagnosticsAndLogAnalytics de PowerShell
   * Descargue el módulo [AzureDiagnosticsAndLogAnalytics](https://www.powershellgallery.com/packages/AzureDiagnosticsAndLogAnalytics/) de la Galería de PowerShell

### <a name="option-1:-run-the-interactive-configuration-scripts"></a>Opción 1: Ejecución de los scripts de configuración interactivos
Abra PowerShell y ejecute:

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Run the UI configuration script
Add-AzureDiagnosticsToLogAnalyticsUI

```

Se mostrará una lista de opciones disponibles y un aviso para que haga sus selecciones.
Se le pedirá que elija sus opciones para:

* Tipos de recursos (servicios de Azure) de los que recopilar registros
* Instancias de recursos de los que recopilar registros
* Área de trabajo de Log Analytics que recopilará los datos

Tras ejecutar este script, deberá ver registros en Log Analytics unos 30 minutos después de que se escriban nuevos datos de diagnóstico en el almacenamiento. Si los registros no están disponibles después de ese tiempo, consulte la sección de solución de problemas más abajo.

### <a name="option-2:-build-a-list-of-resources-and-pass-them-to-the-configuration-cmdlet"></a>Opción 2: Creación de una lista de recursos para transferirlos al cmdlet de configuración
Puede crear una lista de recursos que tengan el diagnóstico de Azure habilitado para transferirlos después al cmdlet de configuración.

Para ver más información sobre el cmdlet, puede ejecutar `Get-Help Add-AzureDiagnosticsToLogAnalytics`.

Para más información sobre OMS, consulte los [cmdlets de PowerShell para Log Analytics](https://msdn.microsoft.com/library/mt188224.aspx)

> [!NOTE]
> Si el área de trabajo y los recursos están en diferentes suscripciones de Azure, vaya de una a otra con `Select-AzureRmSubscription -SubscriptionId <Subscription the resource is in>`, según sea necesario
> 
> 

```
# Connect to Azure
Login-AzureRmAccount
# If you have diagnostics logs being written to classic storage you will also need to run
Add-AzureAccount

# Import the module
Install-Module -Name AzureDiagnosticsAndLogAnalytics

# Update the values below with the name of the resource that has Azure diagnostics enabled and the name of your Log Analytics workspace
$resourceName = "***example-resource***"
$workspaceName = "***log-analytics-workspace***"

# Find the resource to monitor:
$resource = Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" -ResourceNameContains $resourceName

# Find the Log Analytics workspace to configure, for example:
$workspace = Find-AzureRmResource -ResourceType "Microsoft.OperationalInsights/workspaces" -ResourceNameContains $workspaceName

# Perform configuration
Add-AzureDiagnosticsToLogAnalytics $resource $workspace

# Enable the Log Analytics solution
Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -intelligencepackname KeyVault -Enabled $true

```
Tras ejecutar este script, deberá ver registros en Log Analytics unos 30 minutos después de que se escriban nuevos datos de diagnóstico en el almacenamiento. Si los registros no están disponibles después de ese tiempo, consulte la sección de solución de problemas más abajo.  

> [!NOTE]
> Esta configuración no se ve en Azure Portal. Puede comprobar la configuración mediante el cmdlet `Get-AzureRmOperationalInsightsStorageInsight` .  
> 
> 

## <a name="stopping-log-analytics-from-collecting-azure-diagnostic-logs"></a>Detención de la recopilación de registros de diagnóstico de Azure de Log Analytics
Para eliminar la configuración de Log Analytics de un recurso, use el cmdlet `Remove-AzureRmOperationalInsightsStorageInsight`.

## <a name="troubleshooting-configuration-for-azure-diagnostic-logs"></a>Solución de problemas de configuración de registros de diagnóstico de Azure
*Problema*

Al configurar un recurso que realiza el registro en almacenamiento clásico, se muestra el siguiente error:

```
Select-AzureSubscription : The subscription id 7691b0d1-e786-4757-857c-7360e61896c3 doesn't exist.

Parameter name: id
```

*Resolución*

Inicie sesión en la API para el modelo de implementación clásica con `Add-AzureAccount`

## <a name="troubleshooting-data-collection-for-azure-diagnostic-logs"></a>Solución de problemas de recopilación de datos para los registros de diagnóstico de Azure
Si no se muestran datos del recurso de Azure en Log Analytics, puede seguir estos pasos de solución de problemas:

* Comprobar el flujo de datos a la cuenta de almacenamiento
* Comprobar que la solución de Log Analytics para el servicio está habilitada
* Comprobar que Log Analytics está configurado para leer del almacenamiento
* Actualizar la clave de la cuenta de almacenamiento

### <a name="verify-data-is-flowing-to-the-storage-account"></a>Comprobación de que los datos fluyen a la cuenta de almacenamiento
Compruebe si hay flujo de datos a la cuenta de almacenamiento con una herramienta que permita explorar Azure Storage (por ejemplo, Visual Studio) o con PowerShell.

Para buscar la cuenta de almacenamiento en la que se registrará el recurso de acuerdo con su configuración, use el siguiente comando de PowerShell:

`Find-AzureRmResource -ResourceType "Microsoft.KeyVault/Vaults" | select ResourceId | Get-AzureRmDiagnosticSetting `

El contenedor de almacenamiento utilizado por Diagnósticos de Azure tiene un nombre con el formato:  

`insights-logs-<Category>`

Consulte el artículo sobre el [uso de cmdlets de almacenamiento para comprobar datos recientes en un contenedor](../storage/storage-powershell-guide-full.md) para más información acerca de cómo ver el contenido de la cuenta de almacenamiento.

### <a name="verify-the-log-analytics-solution-for-the-service-is-enabled"></a>Comprobar que la solución de Log Analytics para el servicio está habilitada
Si usa `Add-AzureDiagnosticsToLogAnalyticsUI`, se habilitará automáticamente la solución de Log Analytics correcta.

Para comprobar si una solución está habilitada, ejecute el siguiente comando de PowerShell:

`Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName`

Si la solución no está habilitada, puede habilitarla con el siguiente comando de PowerShell:

`Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $logAnalyticsResourceGroup -WorkspaceName $logAnalyticsWorkspaceName -IntelligencePackName $solution -Enabled $true`

Para buscar el nombre de la solución que habilitar para cada tipo de recurso, utilice el siguiente comando de PowerShell (esta variable está disponible después de importar el módulo):

`$MonitorableResourcesToOMSSolutions`

### <a name="verify-that-log-analytics-is-configured-to-read-from-storage"></a>Comprobar que Log Analytics está configurado para leer del almacenamiento
Si agrega recursos adicionales de Azure, debe habilitar el registro de diagnóstico y configurar Log Analytics para esos recursos.
Si desea comprobar para qué recursos y cuentas de almacenamiento está configurado Log Analytics para recopilar registros, use el siguiente comando de PowerShell:

```
# Find the Workspace ResourceGroup and Name
$logAnalyticsWorkspace = Get-AzureRmOperationalInsightsWorkspace

#Get the configuration for all resources:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name

#Get the just the resources configured:
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name | select Containers
```

### <a name="update-the-storage-key"></a>Actualización de la clave de almacenamiento
Si cambia la clave de la cuenta de almacenamiento, también será necesario actualizar la clave en la configuración de Log Analytics.
Puede actualizar la clave en la configuración de Log Analytics mediante el siguiente comando de PowerShell:

`Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $logAnalyticsWorkspace.ResourceGroupName -WorkspaceName $logAnalyticsWorkspace.Name –Name <Storage Insight Name> -StorageAccountKey $newKey `

Para buscar el nombre de visión de almacenamiento, use el cmdlet `Get-AzureRmOperationalInsightsStorageInsight` , tal y como se muestra en ejemplos anteriores.

## <a name="next-steps"></a>Pasos siguientes
* Consulte [Use blob storage for IIS and table storage for events](log-analytics-azure-storage-iis-table.md) (Uso de Blob Storage para IIS y de Table Storage para eventos) para aprender a leer los registros para servicios de Azure que escriben diagnósticos en Table Storage o registros ISS en Blob Storage.
* [Incorporación de soluciones de Log Analytics desde la galería de soluciones](log-analytics-add-solutions.md) para más información sobre los datos.
* [Búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para analizar los datos.

<!--HONumber=Oct16_HO2-->


