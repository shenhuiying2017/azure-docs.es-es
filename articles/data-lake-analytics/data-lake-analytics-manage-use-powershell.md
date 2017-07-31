---
title: "Administración de Azure Data Lake Analytics con Azure PowerShell | Microsoft Docs"
description: "Aprenda a administrar cuentas de Data Lake Analytics, orígenes de datos, trabajos y elementos de catálogo. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: ad14d53c-fed4-478d-ab4b-6d2e14ff2097
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/26/2017
ms.author: mahi
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 86648ebc5e13570050bb142158bf1aa356d466b3
ms.contentlocale: es-es
ms.lasthandoff: 06/28/2017


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

```powershell
Login-AzureRmAccount -SubscriptionId $subId
```

> [!NOTE]
> Para guardar la sesión de inicio para un uso futuro, utilice ``Save-AzureRmProfile``. Para cargar la sesión de inicio de nuevo, utilice ``Select-AzureRmProfile``.

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

### <a name="add-or-remove-firewall-rules"></a>Agregar o eliminar reglas de firewall

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

### <a name="enable-or-disable-firewall-rules"></a>Habilitar o deshabilitar reglas de firewall

Habilitar reglas de firewall.

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Enabled
```

Permitir direcciones IP de Azure.

```powershell
Set-AdlAnalyticsAccount -Name $adla -AllowAzureIpState Enabled
```

Deshabilitar reglas de firewall.

```powershell
Set-AdlAnalyticsAccount -Name $adla -FirewallState Disabled
```

## <a name="managing-data-sources"></a>Administración de orígenes de datos
Actualmente, Azure Data Lake Analytics admite los siguientes orígenes de datos:

* [Almacén de Azure Data Lake](../data-lake-store/data-lake-store-overview.md)
* [Almacenamiento de Azure](../storage/storage-introduction.md)

Cuando se crea una cuenta de Analytics, se debe designar una cuenta de Data Lake Store para que sea el origen de datos predeterminado. La cuenta predeterminada de Almacén de Data Lake sirve para almacenar los registros de auditoría y de metadatos de trabajos. Una vez creada la cuenta de Data Lake Analytics, puede agregar más cuentas de Data Lake Store o cuentas de Storage. 

### <a name="find-the-default-data-lake-store-account"></a>Búsqueda de la cuenta predeterminada de Almacén de Data Lake

```powershell
$dataLakeStoreName = (Get-AdlAnalyticsAccount -Name $adla).DefaultDataLakeAccount
```

### <a name="add-data-sources"></a>Agregar orígenes de datos

Agregar una cuenta adicional de Storage (Blob).

```powershell
$AzureStorageAccountName = "<AzureStorageAccountName>"
$AzureStorageAccountKey = "<AzureStorageAccountKey>"

Add-AdlAnalyticsDataSource -Account $adla -Blob $AzureStorageAccountName -AccessKey $AzureStorageAccountKey
```

Agregar una cuenta adicional de Data Lake Store.

```powershell
$AzureDataLakeStoreName = "<AzureDataLakeStoreAccountName"
Add-AdlAnalyticsDataSource -Account $adla -DataLakeStore $AzureDataLakeStoreName 
```

### <a name="list-data-sources"></a>Enumerar orígenes de datos

Enumerar todos los orígenes de datos asociados.

```powershell
Get-AdlAnalyticsDataSource -Name $adla
```

Enumerar las cuentas de Data Lake Store asociadas.

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "DataLakeStore"
```

Enumerar las cuentas de Storage asociadas.

```powershell
Get-AdlAnalyticsDataSource -Name $adla | where -Property Type -EQ "Blob"
```

## <a name="managing-jobs"></a>Administración de trabajos

### <a name="submit-a-job"></a>Enviar un trabajo

Crear un archivo de texto local con el siguiente script de U-SQL.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Envíe el script.

```powershell
Submit-AdlJob -AccountName $adla –ScriptPath "<LocalPathToScriptFile>"
```

### <a name="list-jobs"></a>Enumeración de trabajos

Muestre todos los trabajos de la cuenta. El resultado incluye los trabajos actualmente en ejecución y aquellos que se han completado recientemente.

```powershell
Get-AdlJob -Account $adla
```

Lista de todos los trabajos con error enviados por "joe@contoso.com" en los últimos siete días.

```powershell
Get-AdlJob -Account $adla `
    -Submitter "joe@contoso.com" `
    -SubmittedAfter (Get-Date).AddDays(-7) `
    -Result Failed
```

### <a name="get-information-about-a-job"></a>Obtención de información sobre un trabajo

Obtenga el estado de un trabajo específico.

```powershell
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

En lugar de repetir `Get-AdlAnalyticsJob` hasta que finalice un trabajo, puede usar el cmdlet `Wait-AdlJob` para esperar a que finalice el trabajo.

```powershell
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Después de finalizar el trabajo, compruebe si existe el archivo de salida; para ello, enumere los archivos de una carpeta.

```powershell
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Compruebe la existencia de un archivo.

```powershell
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

### <a name="cancel-a-job"></a>Cancelación de un trabajo

```powershell
Stop-AdlJob -Account $adls -JobID $jobID
```

## <a name="uploading-and-downloading"></a>Carga y descarga de archivos

Cargar un archivo.

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\data.tsv" `
    -Destination "/data_copy.csv" 
```

Cargar una carpeta completa.

```powershell
Import-AdlStoreItem -AccountName $adls `
    -Path "<LocalPath>\myData\" `
    -Destination "/myData/" `
    -Recurse
```

Descargar un archivo.

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/data.csv" `
    -Destination "<LocalPath>\data.csv"
```

Descargar una carpeta completa.

```powershell
Export-AdlStoreItem -AccountName $adls `
    -Path "/" `
    -Destination "<LocalPath>\myData\" `
    -Recurse
```

> [!NOTE]
> Si se interrumpe el proceso de carga o descarga, puede intentar reanudar el proceso ejecutando el cmdlet de nuevo con la opción ``-Resume``.

## <a name="manage-catalog-items"></a>Administración de elementos de catálogo
El catálogo de U-SQL se usa para estructurar datos y código, para que puedan compartirse mediante scripts de U-SQL. El catálogo permite el mayor rendimiento posible con los datos en Azure Data Lake. Para obtener más información, consulte [Uso del catálogo de U-SQL](data-lake-analytics-use-u-sql-catalog.md).

### <a name="list-items"></a>Enumeración de elementos

Enumerar bases de datos U-SQL.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Database 
```

Enumerar las tablas de una base de datos.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master"
```

Enumerar las tablas de un esquema.

```powershell
Get-AdlCatalogItem -Account $adla -ItemType Table -Path "master.dbo"
```

### <a name="get-information-about-a-catalog-item"></a>Obtención de información de un elemento de catálogo

Obtener detalles de una base de datos U-SQL.

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

Obtener detalles de una tabla de una base de datos U-SQL.

```powershell
Get-AdlCatalogItem  -Account $adla -ItemType Table -Path "master.dbo.mytable"
```

Comprobar la existencia de una base de datos U-SQL.

```powershell
Test-AdlCatalogItem  -Account $adla -ItemType Database -Path "master"
```

### <a name="create-catalog-items"></a>Creación de elementos de catálogo

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
* Administración de Azure Data Lake Analytics mediante [Azure Portal](data-lake-analytics-manage-use-portal.md) | [Azure PowerShell](data-lake-analytics-manage-use-powershell.md) |  [Azure Portal](data-lake-analytics-manage-use-portal.md) | [CLI](data-lake-analytics-manage-use-cli.md) 

