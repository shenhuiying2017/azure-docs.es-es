---
title: "Administración de Azure Data Lake Analytics con Azure PowerShell | Microsoft Docs"
description: "Aprenda a administrar cuentas de Data Lake Analytics, orígenes de datos, trabajos y elementos de catálogo. "
services: data-lake-analytics
documentationcenter: 
author: matt1883
manager: jhubbard
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/23/2017
ms.author: mahi
ms.openlocfilehash: 65bf5928428b21e98c893a9de8ca596329329411
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-data-lake-analytics-using-azure-powershell"></a>Administración de Análisis de Azure Data Lake mediante Azure PowerShell
[!INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Aprenda a administrar cuentas de Azure Data Lake Analytics, orígenes de datos, trabajos y elementos de catálogo mediante Azure PowerShell. 

## <a name="prerequisites"></a>Requisitos previos

Al crear una cuenta de Data Lake Analytics, debe saber:

* **Id. de suscripción**: Id. de la suscripción de Azure en la que reside la cuenta de Data Lake Analytics.
* **Grupo de recursos**: el nombre del grupo de recursos de Azure que contiene la cuenta de Data Lake Analytics.
* **Nombre de la cuenta de Data Lake Analytics**: el nombre de la cuenta solo debe contener letras minúsculas y números.
* **Cuenta predeterminada de Data Lake Store**: cada cuenta de Data Lake Analytics tiene una cuenta de Data Lake Store predeterminada. Estas cuentas deben estar en la misma ubicación.
* **Ubicación**: la ubicación de la cuenta de Data Lake Analytics como "Este de EE. UU. 2" u otras ubicaciones admitidas. Las ubicaciones admitidas se pueden consultar en nuestra [página de precios](https://azure.microsoft.com/pricing/details/data-lake-analytics/).

Los fragmentos de código de PowerShell de este tutorial usan estas variables para almacenar esta información

```powershell
$subId = "<SubscriptionId>"
$rg = "<ResourceGroupName>"
$adla = "<DataLakeAnalyticsAccountName>"
$adls = "<DataLakeStoreAccountName>"
$location = "<Location>"
```

## <a name="log-in"></a>Registro

Inicie sesión con un identificador de suscripción.

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

Inicie sesión con un nombre de suscripción.

```
Login-AzureRmAccount -SubscriptionName $subname 
```

El cmdlet `Login-AzureRmAccount` siempre pide las credenciales. Puede evitar que se le pidan mediante los cmdlets siguientes:

```powershell
# Save login session information
Save-AzureRmProfile -Path D:\profile.json  

# Load login session information
Select-AzureRmProfile -Path D:\profile.json 
```

## <a name="managing-accounts"></a>Administración de cuentas

### <a name="create-a-data-lake-analytics-account"></a>Creación de una cuenta de Análisis de Data Lake

Si aún no tiene un [grupo de recursos](../azure-resource-manager/resource-group-overview.md#resource-groups) para usar, cree uno. 

```powershell
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Cada cuenta de Data Lake Analytics requiere una cuenta predeterminada de Data Lake Store que se usa para almacenar registros. Puede reutilizar una cuenta existente o crear una nueva. 

```powershell
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Una vez que un grupo de recursos y una cuenta de Data Lake Store estén disponibles, cree una cuenta de Data Lake Analytics.

```powershell
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

### <a name="get-information-about-an-account"></a>Obtener información de una cuenta

Obtener detalles acerca de una cuenta.

```powershell
Get-AdlAnalyticsAccount -Name $adla
```

Comprobar la existencia de una cuenta específica de Data Lake Analytics. El cmdlet devuelve `True` o `False`.

```powershell
Test-AdlAnalyticsAccount -Name $adla
```

Compruebe la existencia de una cuenta determinada de Data Lake Store. El cmdlet devuelve `True` o `False`.

```powershell
Test-AdlStoreAccount -Name $adls
```

### <a name="listing-accounts"></a>Lista de cuentas

Enumerar cuentas de Data Lake Analytics dentro de la suscripción actual.

```powershell
Get-AdlAnalyticsAccount
```

Enumerar cuentas de Data Lake Analytics dentro de un grupo de recursos específico.

```powershell
Get-AdlAnalyticsAccount -ResourceGroupName $rg
```

## <a name="managing-firewall-rules"></a>Administración de reglas de firewall

Enumerar reglas de firewall.

```powershell
Get-AdlAnalyticsFirewallRule -Account $adla
```

Agregar una regla de firewall.

```powershell
$ruleName = "Allow access from on-prem server"
$startIpAddress = "<start IP address>"
$endIpAddress = "<end IP address>"

Add-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Cambiar una regla de firewall.

```powershell
Set-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
```

Eliminar una regla de firewall.

```powershell
Remove-AdlAnalyticsFirewallRule -Account $adla -Name $ruleName
```



Permitir direcciones IP de Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Administración de orígenes de datos
Actualmente, Azure Data Lake Analytics admite los siguientes orígenes de datos:

* [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Almacenamiento de Azure](../storage/common/storage-introduction.md)

Cuando se crea una cuenta de Analytics, se debe designar una cuenta de Data Lake Store para que sea el origen de datos predeterminado. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Data Lake Analytics, puede agregar más cuentas de Data Lake Store o cuentas de Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Búsqueda de la cuenta predeterminada de Almacén de Data Lake

```powershell
$adla_acct = Get-AdlAnalyticsAccount -Name $adla
$dataLakeStoreName = $adla_acct.DefaultDataLakeAccount
```

Puede encontrar la cuenta predeterminada de Data Lake Store si filtra la lista de orígenes de datos por la propiedad `IsDefault`:

```powershell
Get-AdlAnalyticsDataSource -Account $adla  | ? { $_.IsDefault } 
```

### <a name="add-a-data-source"></a>Agregar un origen de datos

```powershell

# Add an additional Storage (Blob) account.
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"
Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey

# Add an additional Data Lake Store account.
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Enumerar orígenes de datos

```powershell
# List all the data sources
Get-AdlAnalyticsDataSource -Name $adla

# List attached Data Lake Store accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"

# List attached Storage accounts
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="submit-u-sql-jobs"></a>Envío de trabajos de U-SQL

### <a name="submit-a-string-as-a-u-sql-script"></a>Enviar una cadena como un script U-SQL

```powershell
$script = @"
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
"@

$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 

Submit-AdlJob -AccountName $adla -Script $script -Name "Demo"
```


### <a name="submit-a-file-as-a-u-sql-script"></a>Enviar un archivo como un script U-SQL

```powershell
$scriptpath = "d:\test.usql"
$script | Out-File $scriptpath 
Submit-AdlJob -AccountName $adla –ScriptPath $scriptpath -Name "Demo"
```

## <a name="list-jobs-in-an-account"></a>Enumerar trabajos en una cuenta

### <a name="list-all-the-jobs-in-the-account"></a>Muestre todos los trabajos de la cuenta. 

El resultado incluye los trabajos actualmente en ejecución y aquellos que se han completado recientemente.

```powershell
Get-AdlJob -Account $adla
```


### <a name="list-a-specific-number-of-jobs"></a>Mostrar un número determinado de trabajos

De forma predeterminada, la lista de trabajos se ordena por hora de envío. Así, los trabajos enviados más recientemente aparecen en primer lugar. De forma predeterminada, la cuenta de ADLA recuerda los trabajos durante 180 días, pero el cmdlet Ge-AdlJob devuelve solo los primeros 500. Use el parámetro -Top para mostrar un número específico de trabajos.

```powershell
$jobs = Get-AdlJob -Account $adla -Top 10
```


### <a name="list-jobs-based-on-the-value-of-job-property"></a>Enumerar trabajos según el valor de la propiedad del trabajo

Mediante el parámetro `-State`. Puede combinar cualquiera de estos valores:

* `Accepted`
* `Compiling`
* `Ended`
* `New`
* `Paused`
* `Queued`
* `Running`
* `Scheduling`
* `Start`

```powershell
# List the running jobs
Get-AdlJob -Account $adla -State Running

# List the jobs that have completed
Get-AdlJob -Account $adla -State Ended

# List the jobs that have not started yet
Get-AdlJob -Account $adla -State Accepted,Compiling,New,Paused,Scheduling,Start
```

Use el parámetro `-Result` para detectar si los trabajos finalizados se han realizado correctamente. Tiene estos valores:

* Cancelado
* Con error
* None
* Correcto

``` powershell
# List Successful jobs.
Get-AdlJob -Account $adla -State Ended -Result Succeeded

# List Failed jobs.
Get-AdlJob -Account $adla -State Ended -Result Failed
```


El parámetro `-Submitter` ayuda a identificar quién ha enviado un trabajo.

```powershell
Get-AdlJob -Account $adla -Submitter "joe@contoso.com"
```

`-SubmittedAfter` es útil al filtrar por un intervalo de tiempo.


```powershell
# List  jobs submitted in the last day.
$d = [DateTime]::Now.AddDays(-1)
Get-AdlJob -Account $adla -SubmittedAfter $d

# List  jobs submitted in the last seven day.
$d = [DateTime]::Now.AddDays(-7)
Get-AdlJob -Account $adla -SubmittedAfter $d
```

### <a name="analyzing-job-history"></a>Análisis del historial de trabajo

El uso de Azure PowerShell para analizar el historial de trabajos que se han ejecutado en Data Lake Analytics es una técnica eficaz. Puede usarla para obtener información sobre el uso y el costo. Puede obtener más información examinando el [repositorio de ejemplo del análisis del historial de trabajo](https://github.com/Azure-Samples/data-lake-analytics-powershell-job-history-analysis)  

## <a name="get-information-about-pipelines-and-recurrences"></a>Obtención de información sobre canalizaciones y repeticiones

Use el cmdlet `Get-AdlJobPipeline` para ver la información de canalización de trabajos enviados previamente.

```powershell
$pipelines = Get-AdlJobPipeline -Account $adla

$pipeline = Get-AdlJobPipeline -Account $adla -PipelineId "<pipeline ID>"
```

Use el cmdlet `Get-AdlJobRecurrence` para ver la información de repetición de trabajos enviados previamente.

```powershell
$recurrences = Get-AdlJobRecurrence -Account $adla

$recurrence = Get-AdlJobRecurrence -Account $adla -RecurrenceId "<recurrence ID>"
```

## <a name="get-information-about-a-job"></a>Obtención de información sobre un trabajo

### <a name="get-job-status"></a>Obtención de estado del trabajo

Obtenga el estado de un trabajo específico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

### <a name="examine-the-job-outputs"></a>Examen de las salidas del trabajo

Después de finalizar el trabajo, compruebe si existe el archivo de salida; para ello, enumere los archivos de una carpeta.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

## <a name="manage-running-jobs"></a>Administración de trabajos en ejecución

### <a name="cancel-a-job"></a>Cancelación de un trabajo

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

### <a name="wait-for-a-job-to-finish"></a>Espera de fin de un trabajo

En lugar de repetir `Get-AdlAnalyticsJob` hasta que finalice un trabajo, puede usar el cmdlet `Wait-AdlJob` para esperar a que finalice el trabajo.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

## <a name="manage-compute-policies"></a>Administración de nodos directivas de proceso

### <a name="list-existing-compute-policies"></a>Lista de directivas de proceso existentes

El cmdlet `Get-AdlAnalyticsComputePolicy` recupera información sobre las directivas de proceso de una cuenta de Data Lake Analytics.

```powershell
$policies = Get-AdlAnalyticsComputePolicy -Account $adla
```

### <a name="create-a-compute-policy"></a>Creación de una directiva de proceso

El cmdlet `New-AdlAnalyticsComputePolicy` crea una nueva directiva de proceso para una cuenta de Data Lake Analytics. En este ejemplo se establecen las unidades de asignación (AU) máximas disponibles para el usuario especificado en 50 y la prioridad mínima del trabajo en 250.

```powershell
$userObjectId = (Get-AzureRmAdUser -SearchString "garymcdaniel@contoso.com").Id

New-AdlAnalyticsComputePolicy -Account $adla -Name "GaryMcDaniel" -ObjectId $objectId -ObjectType User -MaxDegreeOfParallelismPerJob 50 -MinPriorityPerJob 250
```

## <a name="check-for-the-existence-of-a-file"></a>Compruebe la existencia de un archivo.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading"></a>Carga y descarga de archivos

Cargar un archivo.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\data.tsv" -Destination "/data_copy.csv" 
```

Cargue una carpeta completa de forma recursiva.

```powershell
Import-AdlStoreItem -AccountName $adls -Path "c:\myData\" -Destination "/myData/" -Recurse
```

Descargar un archivo.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/data.csv" -Destination "c:\data.csv"
```

Descargue una carpeta completa de forma recursiva.

```powershell
Export-AdlStoreItem -AccountName $adls -Path "/" -Destination "c:\myData\" -Recurse
```

> [!NOTE]
> Si se interrumpe el proceso de carga o descarga, puede intentar reanudar el proceso ejecutando el cmdlet de nuevo con la opción ``-Resume``.

## <a name="manage-catalog-items"></a>Administración de elementos de catálogo

El catálogo de U-SQL se usa para estructurar datos y código, para que puedan compartirse mediante scripts de U-SQL. El catálogo permite el mayor rendimiento posible con los datos en Azure Data Lake. Para obtener más información, consulte [Uso del catálogo de U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items-in-the-u-sql-catalog"></a>Enumerar elementos en el catálogo de U-SQL

```powershell
# List U-SQL databases
Get-AdlCatalogItem -Account $adla -ItemType Database 

# List tables within a database
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database"

# List tables within a schema.
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "database.schema"
```

Enumere todos los ensamblados de todas las bases de datos de una cuenta de ADLA.

```powershell
$dbs = Get-AdlCatalogItem -Account $adla -ItemType Database

foreach ($db in $dbs)
{
    $asms = Get-AdlCatalogItem -Account $adla -ItemType Assembly -Path $db.Name

    foreach ($asm in $asms)
    {
        $asmname = "[" + $db.Name + "].[" + $asm.Name + "]"
        Write-Host $asmname
    }
}
```

### <a name="get-details-about-a-catalog-item"></a>Obtener detalles sobre un elemento de catálogo

```powershell
# Get details of a table
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"

# Test existence of a U-SQL database.
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-credentials-in-a-catalog"></a>Crear credenciales en un catálogo

Dentro de una base de datos U-SQL, cree un objeto de credenciales para una base de datos hospedada en Azure. Actualmente, las credenciales U-SQL son el único tipo de elemento de catálogo que se puede crear a través de PowerShell.

```powershell
$dbName = "master"
$credentialName = "ContosoDbCreds"
$dbUri = "https://contoso.database.windows.net:8080"

New-AdlCatalogCredential -AccountName $adla `
          -DatabaseName $db `
          -CredentialName $credentialName `
          -Credential (Get-Credential) `
          -Uri $dbUri
```

### <a name="get-basic-information-about-an-adla-account"></a>Obtener información básica sobre una cuenta de ADLA

Dado un nombre de cuenta, el código siguiente busca información básica sobre la cuenta

```
$adla_acct = Get-AdlAnalyticsAccount -Name "saveenrdemoadla"
$adla_name = $adla_acct.Name
$adla_subid = $adla_acct.Id.Split("/")[2]
$adla_sub = Get-AzureRmSubscription -SubscriptionId $adla_subid
$adla_subname = $adla_sub.Name
$adla_defadls_datasource = Get-AdlAnalyticsDataSource -Account $adla_name  | ? { $_.IsDefault } 
$adla_defadlsname = $adla_defadls_datasource.Name

Write-Host "ADLA Account Name" $adla_name
Write-Host "Subscription Id" $adla_subid
Write-Host "Subscription Name" $adla_subname
Write-Host "Defautl ADLS Store" $adla_defadlsname
Write-Host 

Write-Host '$subname' " = ""$adla_subname"" "
Write-Host '$subid' " = ""$adla_subid"" "
Write-Host '$adla' " = ""$adla_name"" "
Write-Host '$adls' " = ""$adla_defadlsname"" "
```

## <a name="working-with-azure"></a>Trabajo con Azure

### <a name="get-details-of-azurerm-errors"></a>Obtener detalles de errores de AzureRm

```powershell
Resolve-AzureRmError -Last
```

### <a name="verify-if-you-are-running-as-an-administrator"></a>Comprobar si está ejecutando como administrador

```powershell
function Test-Administrator  
{  
    $user = [Security.Principal.WindowsIdentity]::GetCurrent();
    $p = New-Object Security.Principal.WindowsPrincipal $user
    $p.IsInRole([Security.Principal.WindowsBuiltinRole]::Administrator)  
}
```

### <a name="find-a-tenantid"></a>Buscar un elemento TenantID

A partir de un nombre de suscripción:

```powershell
function Get-TenantIdFromSubcriptionName( [string] $subname )
{
    $sub = (Get-AzureRmSubscription -SubscriptionName $subname)
    $sub.TenantId
}

Get-TenantIdFromSubcriptionName "ADLTrainingMS"
```

A partir de un identificador de suscripción:

```powershell
function Get-TenantIdFromSubcriptionId( [string] $subid )
{
    $sub = (Get-AzureRmSubscription -SubscriptionId $subid)
    $sub.TenantId
}

$subid = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
Get-TenantIdFromSubcriptionId $subid
```

A partir de una dirección de dominio como "contoso.com"


```powershell
function Get-TenantIdFromDomain( $domain )
{
    $url = "https://login.windows.net/" + $domain + "/.well-known/openid-configuration"
    return (Invoke-WebRequest $url|ConvertFrom-Json).token_endpoint.Split('/')[3]
}

$domain = "contoso.com"
Get-TenantIdFromDomain $domain
```

### <a name="list-all-your-subscriptions-and-tenant-ids"></a>Enumerar todas las suscripciones e identificadores de inquilino

```powershell
$subs = Get-AzureRmSubscription
foreach ($sub in $subs)
{
    Write-Host $sub.Name "("  $sub.Id ")"
    Write-Host "`tTenant Id" $sub.TenantId
}
```

## <a name="create-a-data-lake-analytics-account-using-a-template"></a>Creación de una cuenta de Data Lake Analytics mediante una plantilla

También puede usar una plantilla de grupo de recursos de Azure mediante el siguiente script de PowerShell:

```powershell
$subId = "<Your Azure Subscription ID>"

$rg = "<New Azure Resource Group Name>"
$location = "<Location (such as East US 2)>"
$adls = "<New Data Lake Store Account Name>"
$adla = "<New Data Lake Analytics Account Name>"

$deploymentName = "MyDataLakeAnalyticsDeployment"
$armTemplateFile = "<LocalFolderPath>\azuredeploy.json"  # update the JSON template path 

# Log in to Azure
Login-AzureRmAccount -SubscriptionId $subId

# Create the resource group
New-AzureRmResourceGroup -Name $rg -Location $location

# Create the Data Lake Analytics account with the default Data Lake Store account.
$parameters = @{"adlAnalyticsName"=$adla; "adlStoreName"=$adls}
New-AzureRmResourceGroupDeployment -Name $deploymentName -ResourceGroupName $rg -TemplateFile $armTemplateFile -TemplateParameterObject $parameters 
```

Para obtener más información , consulte el artículo sobre cómo [implementar una aplicación con la plantilla de Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md) y [crear plantillas de Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md).

**Plantilla de ejemplo**

Guarde el siguiente texto como un archivo `.json` y, a continuación, utilice el script de PowerShell anterior para usar la plantilla. 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "adlAnalyticsName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Analytics account to create."
      }
    },
    "adlStoreName": {
      "type": "string",
      "metadata": {
        "description": "The name of the Data Lake Store account to create."
      }
    }
  },
  "resources": [
    {
      "name": "[parameters('adlStoreName')]",
      "type": "Microsoft.DataLakeStore/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ ],
      "tags": { }
    },
    {
      "name": "[parameters('adlAnalyticsName')]",
      "type": "Microsoft.DataLakeAnalytics/accounts",
      "location": "East US 2",
      "apiVersion": "2015-10-01-preview",
      "dependsOn": [ "[concat('Microsoft.DataLakeStore/accounts/',parameters('adlStoreName'))]" ],
      "tags": { },
      "properties": {
        "defaultDataLakeStoreAccount": "[parameters('adlStoreName')]",
        "dataLakeStoreAccounts": [
          { "name": "[parameters('adlStoreName')]" }
        ]
      }
    }
  ],
  "outputs": {
    "adlAnalyticsAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeAnalytics/accounts',parameters('adlAnalyticsName')))]"
    },
    "adlStoreAccount": {
      "type": "object",
      "value": "[reference(resourceId('Microsoft.DataLakeStore/accounts',parameters('adlStoreName')))]"
    }
  }
}
```

## <a name="next-steps"></a>Pasos siguientes
* [Información general de Análisis de Microsoft Azure Data Lake](data-lake-analytics-overview.md)
* Introducción a Data Lake Analytics mediante [Azure Portal](data-lake-analytics-get-started-portal.md) | [Azure PowerShell](data-lake-analytics-get-started-powershell.md) | [CLI 2.0](data-lake-analytics-get-started-cli2.md)
* Administración de Azure Data Lake Analytics mediante [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) | [CLI](data-lake-analytics-manage-use-cli.md) 
