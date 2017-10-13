---
title: "Uso de PowerShell para crear y configurar un área de trabajo de Log Analytics | Microsoft Docs"
description: "Log Analytics usa datos de los servidores de la infraestructura local o de nube. Puede recopilar datos de equipo del almacenamiento de Azure cuando son generados por Diagnósticos de Azure."
services: log-analytics
documentationcenter: 
author: richrundmsft
manager: jochan
editor: 
ms.assetid: 3b9b7ade-3374-4596-afb1-51b695f481c2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 11/21/2016
ms.author: richrund
ms.openlocfilehash: 6807ab67e3593da82c147669b29bfdae3b6c967c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-log-analytics-using-powershell"></a>Administración de Log Analytics mediante PowerShell
Puede usar los [cmdlets de PowerShell de Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para realizar una serie de funciones en Log Analytics desde una línea de comandos o como parte de un script.  A continuación se indican algunos ejemplos de las tareas que puede realizar con PowerShell:

* Crear un área de trabajo
* Agregar o quitar una solución
* Importar y exportar búsquedas guardadas
* Crear un grupo de equipos
* Habilitar la recopilación de registros de IIS en equipos con el agente de Windows instalado
* Recopilar contadores de rendimiento en equipos Linux y Windows
* Recopilar eventos de Syslog en equipos Linux 
* Recopilar eventos de registros de eventos de Windows
* Recopilar registros de eventos personalizados
* Agregar al agente de Log Analytics a una máquina virtual de Azure
* Configurar Log Analytics para indizar los datos recopilados mediante Diagnósticos de Azure

Este artículo proporciona dos ejemplos de código que muestran algunas de las funciones que puede realizar desde PowerShell.  Puede consultar la [referencia de cmdlets de PowerShell de Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para otras funciones.

> [!NOTE]
> Log Analytics se llamaba anteriormente Operational Insights, razón por la cual se utiliza este nombre en los cmdlets.
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Estos ejemplos funcionan con la versión 2.3.0 o posteriores del módulo AzureRm.OperationalInsights.


## <a name="create-and-configure-a-log-analytics-workspace"></a>Creación y configuración de un área de trabajo de Log Analytics
El siguiente ejemplo de script muestra cómo:

1. Crear un área de trabajo
2. Enumerar las soluciones disponibles
3. Agregar soluciones al área de trabajo
4. Importar búsquedas guardadas
5. Exportar búsquedas guardadas
6. Crear un grupo de equipos
7. Habilitar la recopilación de registros de IIS en equipos con el agente de Windows instalado
8. Recopilar contadores de rendimiento de discos lógicos de equipos Linux (% de inodos usados; megabytes libres; % de espacio usado; transferencias de disco/seg.; lecturas de disco/seg.; escrituras de disco/seg.)
9. Recopilar eventos de Syslog de equipos Linux
10. Recopilar eventos de error y advertencia del registro de eventos de aplicación de los equipos de Windows
11. Recopilar contadores de rendimiento de MB disponibles de equipos Windows
12. Recopilar registros personalizados 

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
        "Query":  "Type=Perf ObjectName=LogicalDisk InstanceName=\"C:\" CounterName=\"Current Disk Queue Length\"",
        "Version":  1
    }
]
"@ | ConvertFrom-Json

# Custom Log to collect
$CustomLog = @"
{
    "customLogName": "sampleCustomLog1", 
    "description": "Example custom log datasource", 
    "inputs": [
        { 
            "location": { 
            "fileSystemLocations": { 
                "windowsFileTypeLogPaths": [ "e:\\iis5\\*.log" ], 
                "linuxFileTypeLogPaths": [ "/var/logs" ] 
                } 
            }, 
        "recordDelimiter": { 
            "regexDelimiter": { 
                "pattern": "\\n", 
                "matchIndex": 0, 
                "matchIndexSpecified": true, 
                "numberedGroup": null 
                } 
            } 
        }
    ], 
    "extractions": [
        { 
            "extractionName": "TimeGenerated", 
            "extractionType": "DateTime", 
            "extractionProperties": { 
                "dateTimeExtraction": { 
                    "regex": null, 
                    "joinStringRegex": null 
                    } 
                } 
            }
        ] 
    }
"@

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
foreach ($solution in $Solutions) {
    Set-AzureRmOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true
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

# Create Computer Group based on a query
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Web Servers" -DisplayName "Web Servers" -Category "My Saved Searches" -Query "Computer=""web*"" | distinct Computer" -Version 1

# Create a computer group based on names (up to 5000)
$computerGroup = """servername1.contoso.com"",""servername2.contoso.com"",""servername3.contoso.com"",""servername4.contoso.com"""
New-AzureRmOperationalInsightsComputerGroup -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -SavedSearchId "My Named Servers" -DisplayName "Named Servers" -Category "My Saved Searches" -Query $computerGroup -Version 1

# Enable IIS Log Collection using agent
Enable-AzureRmOperationalInsightsIISLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Perf
New-AzureRmOperationalInsightsLinuxPerformanceObjectDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Logical Disk" -InstanceName "*"  -CounterNames @("% Used Inodes", "Free Megabytes", "% Used Space", "Disk Transfers/sec", "Disk Reads/sec", "Disk Reads/sec", "Disk Writes/sec") -IntervalSeconds 20  -Name "Example Linux Disk Performance Counters"
Enable-AzureRmOperationalInsightsLinuxCustomLogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Linux Syslog
New-AzureRmOperationalInsightsLinuxSyslogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -Facility "kern" -CollectEmergency -CollectAlert -CollectCritical -CollectError -CollectWarning -Name "Example kernal syslog collection"
Enable-AzureRmOperationalInsightsLinuxSyslogCollection -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName

# Windows Event
New-AzureRmOperationalInsightsWindowsEventDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -EventLogName "Application" -CollectErrors -CollectWarnings -Name "Example Application Event Log"

# Windows Perf
New-AzureRmOperationalInsightsWindowsPerformanceCounterDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -ObjectName "Memory" -InstanceName "*" -CounterName "Available MBytes" -IntervalSeconds 20 -Name "Example Windows Performance Counter"

# Custom Logs
New-AzureRmOperationalInsightsCustomLogDataSource -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -CustomLogRawJson "$CustomLog" -Name "Example Custom Log Collection"

```

## <a name="configuring-log-analytics-to-index-azure-diagnostics"></a>Configuración de Log Analytics para indizar Diagnósticos de Azure
Para la supervisión de recursos de Azure sin agente, los recursos necesitan tener Diagnósticos de Azure habilitado y configurado para escribir en un área de trabajo de Log Analytics. Este método envía los datos directamente a Log Analytics y no requiere que los datos se escriban en una cuenta de almacenamiento. Los recursos admitidos son los siguientes:

| Tipo de recurso | Registros | Métricas |
| --- | --- | --- |
| Puertas de enlace de aplicaciones    | Sí | Sí |
| Cuentas de automatización     | Sí | |
| Cuentas de Batch          | Sí | Sí |
| Data Lake Analytics     | yes | | 
| Data Lake Store         | Sí | |
| Elastic SQL Pool        |     | Sí |
| Espacio de nombres del Centro de eventos     |     | Sí |
| IoT Hubs                |     | Sí |
| Key Vault               | Sí | |
| Equilibradores de carga          | Sí | |
| Logic Apps              | yes | Sí |
| Grupos de seguridad de red | Sí | |
| Caché en Redis             |     | yes |
| Servicios de búsqueda         | Sí | Sí |
| Espacio de nombres de Bus de servicio   |     | Sí |
| SQL (v12)               |     | Sí |
| Sitios web               |     | yes |
| Granjas de servidores web        |     | yes |

Para obtener información detallada sobre las métricas disponibles, consulte las [métricas admitidas con Azure Monitor](../monitoring-and-diagnostics/monitoring-supported-metrics.md).

Para obtener información detallada sobre los registros disponibles, consulte los [servicios admitidos y el esquema de los registros de diagnóstico](../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md).

```
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$resourceId = "/SUBSCRIPTIONS/ec11ca60-1234-491e-5678-0ea07feae25c/RESOURCEGROUPS/DEMO/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/DEMO" 

Set-AzureRmDiagnosticSetting -ResourceId $resourceId -WorkspaceId $workspaceId -Enabled $true
```

También puede usar el cmdlet anterior para recopilar registros de recursos que se encuentran en distintas suscripciones. El cmdlet funciona en suscripciones distintas porque se proporciona tanto el identificador del recurso que crea los registros y como el del área de trabajo al que se envían los registros.


## <a name="configuring-log-analytics-to-index-azure-diagnostics-from-storage"></a>Configuración de Log Analytics para indizar diagnósticos de Azure desde el almacenamiento
Para recopilar datos de registro desde una instancia en ejecución de un servicio en la nube clásico o un clúster de Service Fabric, tiene que escribir primero los datos en Azure Storage. Después, Log Analytics se puede configurar para recopilar los registros de la cuenta de almacenamiento. Los recursos admitidos son los siguientes:

* Servicios en la nube clásicos (roles web y de trabajo)
* Clústeres de Service Fabric

El ejemplo siguiente muestra cómo:

1. Enumerar las cuentas de almacenamiento existentes y las ubicaciones desde las que Log Analytics indizará datos
2. Crear una configuración para leer desde una cuenta de almacenamiento
3. Actualizar la configuración recién creada para indizar datos desde ubicaciones adicionales
4. Eliminar la configuración recién creada

```
# validTables = "WADWindowsEventLogsTable", "LinuxsyslogVer2v0", "WADServiceFabric*EventTable", "WADETWEventTable" 
$workspace = (Get-AzureRmOperationalInsightsWorkspace).Where({$_.Name -eq "your workspace name"})

# Update these two lines with the storage account resource ID and the storage account key for the storage account you want to Log Analytics to  
$storageId = "/subscriptions/ec11ca60-1234-491e-5678-0ea07feae25c/resourceGroups/demo/providers/Microsoft.Storage/storageAccounts/wadv2storage"
$key = "abcd=="

# List existing insights
Get-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name

# Create a new insight
New-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -StorageAccountResourceId $storageId -StorageAccountKey $key -Tables @("WADWindowsEventLogsTable") -Containers @("wad-iis-logfiles")

# Update existing insight
Set-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" -Tables @("WADWindowsEventLogsTable", "WADETWEventTable") -Containers @("wad-iis-logfiles")

# Remove the insight
Remove-AzureRmOperationalInsightsStorageInsight -ResourceGroupName $workspace.ResourceGroupName -WorkspaceName $workspace.Name -Name "newinsight" 

```

También puede usar el script anterior para recopilar registros de cuentas de almacenamiento que se encuentran en distintas suscripciones. El script funciona en suscripciones distintas porque se proporciona tanto el identificador del recurso de cuenta de almacenamiento como la clave de acceso correspondiente. Cuando se cambia la clave de acceso, hay que actualizar los datos del almacenamiento para que tenga la nueva clave.


## <a name="next-steps"></a>Pasos siguientes
* [Revise los cmdlets de PowerShell de Log Analytics](https://msdn.microsoft.com/library/mt188224\(v=azure.300\).aspx) para obtener información adicional sobre cómo usar PowerShell para la configuración de Log Analytics.

