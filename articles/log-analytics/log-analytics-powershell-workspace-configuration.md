<properties
	pageTitle="Uso de PowerShell para crear y configurar un área de trabajo de Log Analytics | Microsoft Azure"
	description="Log Analytics usa datos de los servidores de la infraestructura local o de nube. Puede recopilar datos de equipo del almacenamiento de Azure cuando son generados por Diagnósticos de Azure."
	services="log-analytics"
	documentationCenter=""
	authors="richrundmsft"
	manager="jochan"
	editor=""/>

<tags
	ms.service="log-analytics"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="powershell"
	ms.topic="article"
	ms.date="05/16/2016"
	ms.author="richrund"/>

# Administración de Log Analytics mediante PowerShell

Puede usar los [ cmdlets de PowerShell de Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) para realizar una serie de funciones en Log Analytics desde una línea de comandos o como parte de un script. A continuación se indican algunos ejemplos de las tareas que puede realizar con PowerShell:

+ Crear un área de trabajo
+ Agregar o quitar una solución
+ Importar y exportar búsquedas guardadas
+ Agregar al agente de Log Analytics a una máquina virtual de Azure
+ Configurar Log Analytics para indizar los datos recopilados mediante Diagnósticos de Azure

Este artículo proporciona dos ejemplos de código que muestran algunas de las funciones que puede realizar desde PowerShell. Puede consultar la [referencia de cmdlets de PowerShell de Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) para otras funciones.

> [AZURE.NOTE] Log Analytics se llamaba anteriormente Visión operativa, razón por la cual se utiliza este nombre en los cmdlets.

## Requisitos previos

Para usar PowerShell con el área de trabajo de Log Analytics debe tener:

+ Una suscripción de Azure, y 
+ El área de trabajo de Log Analytics de Azure vinculada a su suscripción de Azure.

Si ha creado un área de trabajo de OMS, pero aún no se ha vinculado a una suscripción de Azure, puede crear el vínculo en el portal de Azure, en el portal de OMS o con los cmdlets Get-AzureRMOperationalInsightsLinkTargets y New-AzureRMOperationalInsightsWorkspace cmdlets.

## Creación de un área de trabajo de Log Analytics, incorporación de soluciones y búsquedas guardadas

El siguiente ejemplo de script muestra cómo:

1.	Crear un área de trabajo
2.	Enumerar las soluciones disponibles
3.	Agregar soluciones al área de trabajo
4.	Importar búsquedas guardadas
5.	Exportar búsquedas guardadas

```

$ResourceGroup = "oms-example"
$WorkspaceName = "log-analytics-" + (Get-Random -Maximum 99999) # workspace names need to be unique - Get-Random helps with this for the example code
$Location = "westeurope"

# List of solutions to enable
$Solutions = "Security", "Updates", "SQLAssessment"

# Saved Searches to import
$ExportedSearches = @"
[
    {
        "Category":  "My Saved Searches",
        "DisplayName":  "WAD Events (All)",
        "Query":  "Type=Event SourceSystem:AzureStorage ",
        "Version":  1
    },
    {        
        "Category":  "My Saved Searches",
        "DisplayName":  "Current Disk Queue Length",
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName="C:" CounterName="Current Disk Queue Length"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Create the resource group if needed
try {
    Get-AzureRmResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzureRmResourceGroup -Name $ResourceGroup -Location $Location
}

# Create the workspace
New-AzureRmOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup

# List all solutions and their installation status
Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Add solutions
foreach ($soln in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $soln -Enabled $true
}

#List enabled solutions
(Get-AzureRmOperationalInsightsIntelligencePacks -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Where({($_.enabled -eq $true)})

# Import Saved Searches
foreach ($search in $ExportedSearches) {
    $id = $search.Category + "|" + $search.DisplayName
    New-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId $id -DisplayName $search.DisplayName -Category $search.Category -Query $search.Query -Version $search.Version
}

# Export Saved Searches
(Get-AzureRmOperationalInsightsSavedSearch -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName).Value.Properties | ConvertTo-Json 

```

## Configuración de Log Analytics para indizar Diagnósticos de Azure 

Para supervisar recursos de Azure sin agente, lo que incluye roles web y de trabajo, clústeres de Service Fabric, grupos de seguridad de red, almacenes de claves y puertas de enlace de aplicaciones, los recursos primero deben tener habilitado Diagnósticos de Azure para escribir en una cuenta de almacenamiento y luego Log Analytics se puede configurar para recopilar los registros de la cuenta de almacenamiento.

También puede usar PowerShell para configurar un área de trabajo de Log Analytics en una suscripción de Azure para recopilar registros de distintas suscripciones de Azure.

El ejemplo siguiente muestra cómo:

1.	Enumerar las cuentas de almacenamiento existentes y las ubicaciones desde las que Log Analytics indizará datos
2.	Crear una nueva configuración para leer desde una cuenta de almacenamiento
3.	Actualizar la configuración recién creada para indizar datos desde ubicaciones adicionales
4.	Eliminar la configuración recién creada

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two with the storage account resource id and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles", "insights-logs-networksecuritygroupevent/resourceId=/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

## Pasos siguientes

- [Revise los cmdlets de PowerShell de Log Analytics](http://msdn.microsoft.com/library/mt188224.aspx) para obtener información adicional sobre cómo usar PowerShell para la configuración de Log Analytics.

<!---HONumber=AcomDC_0518_2016-->