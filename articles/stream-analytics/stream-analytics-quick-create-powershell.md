---
title: Creación de un trabajo de Stream Analytics mediante Azure PowerShell
description: En esta guía de inicio rápido se detalla cómo usar el módulo de Azure PowerShell para implementar y ejecutar un trabajo de Azure Stream Analytics.
services: stream-analytics
author: SnehaGunda
ms.author: sngun
ms.date: 05/14/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 2b5d8bfd6dbe36637a0c6873e941118e7ee71b80
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2018
ms.locfileid: "34212439"
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Guía de inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure PowerShell

El módulo de Azure PowerShell se usa para crear y administrar recursos de Azure mediante cmdlets o scripts de PowerShell. En esta guía de inicio rápido se detalla cómo usar el módulo de Azure PowerShell para implementar y ejecutar un trabajo de Azure Stream Analytics. 

El trabajo de ejemplo lee los datos de streaming de un blob de Azure Blob Storage. El archivo de datos de entrada usado en esta guía de inicio rápido contiene datos estáticos solo con fines ilustrativos. En un escenario real, se usan datos de entrada de streaming para los trabajos de Stream Analytics. Después, el trabajo transforma los datos mediante el lenguaje de consultas de Stream Analytics para calcular el promedio de temperatura cuando supere los 100 °. Por último, escribe los eventos de salida resultantes en otro archivo. 

## <a name="before-you-begin"></a>Antes de empezar

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/).  

* Para realizar los pasos de esta guía, se requiere la versión 3.6 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión que está instalada en la máquina local. Si necesita instalarla o actualizarla, consulte el artículo [Instalación y configuración de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). 


## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando `Connect-AzureRmAccount` y escriba las credenciales de Azure en el explorador emergente:

```powershell
# Log in to your Azure account
Connect-AzureRmAccount
```

Después de iniciar sesión, si tiene varias suscripciones, seleccione la suscripción que desea utilizar para esta guía de inicio rápido mediante la ejecución de los siguientes cmdlets. Asegúrese de reemplazar <your subscription name> por el nombre de su suscripción:  

```powershell
# List all available subscriptions.
Get-AzureRmSubscription

# Select the Azure subscription you want to use to create the resource group and resources.
Get-AzureRmSubscription -SubscriptionName "<your subscription name>" | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```powershell
$resourceGroup = "StreamAnalyticsRG"
$location = "WestUS2"
New-AzureRmResourceGroup `
   -Name $resourceGroup `
   -Location $location 
```

## <a name="prepare-the-input-data"></a>Preparación de los datos de entrada

Antes de definir el trabajo de Stream Analytics, prepare los datos que se configuran como entrada del mismo.

1. Descargue los [datos de ejemplo del sensor](https://raw.githubusercontent.com/Azure/azure-stream-analytics/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) desde GitHub. Haga clic con el botón derecho en el vínculo y, después, en **Save Link As...** (Guardar vínculo cómo) o **Save target as** (Guardar destino como).

2. El siguiente bloque de código de PowerShell realiza varios comandos para preparar los datos de entrada que requiere el trabajo. Examine las secciones para comprender el código. 

   1. Cree una cuenta de almacenamiento de uso general mediante el cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount).  En este ejemplo va a crear una cuenta de almacenamiento denominada mystorageaccount con el cifrado de blob y el almacenamiento con redundancia local (LRS) (habilitados de forma predeterminada).  

   2. Recupere el contexto `$storageAccount.Context` de la cuenta de almacenamiento que define la cuenta que se va a usar. Cuando trabaje con cuentas de almacenamiento, haga referencia al contexto en lugar de proporcionar varias veces las credenciales. 

   3. Cree un contenedor de almacenamiento mediante [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer)y cargue los [datos de ejemplo del sensor](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) que descargó anteriormente. 

   4. Copie la clave de almacenamiento que genera el código y péguela en los archivos JSON para poder crear posteriormente la entrada y las salidas del trabajo de streaming.

   ```powershell
   $storageAccountName = "mystorageaccount"
   $storageAccount = New-AzureRmStorageAccount `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName `
     -Location $location `
     -SkuName Standard_LRS `
     -Kind Storage
   
   $ctx = $storageAccount.Context
   $containerName = "streamanalytics"
   
   New-AzureStorageContainer `
     -Name $containerName `
     -Context $ctx
   
   Set-AzureStorageBlobContent `
     -File "c:\HelloWorldASA-InputStream.json" `
     -Blob "input/HelloWorldASA-InputStream.json" `
     -Container $containerName `
     -Context $ctx  
   
   $storageAccountKey = (Get-AzureRmStorageAccountKey `
     -ResourceGroupName $resourceGroup `
     -Name $storageAccountName).Value[0]
   
   Write-Host "The <storage account key> placeholder needs to be replaced in your input and output json files with this key value:" 
   Write-Host $storageAccountKey -ForegroundColor Cyan
   ```

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

Cree un trabajo de Stream Analytics con el cmdlet [New-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre del grupo de recursos y la definición del trabajo. El nombre del trabajo puede ser cualquier nombre descriptivo que lo identifique. Solo puede contener caracteres alfanuméricos, guiones y guiones bajos, y debe tener una longitud de entre 3 y 63 caracteres. La definición del trabajo es un archivo JSON que contiene las propiedades necesarias para crear un trabajo. En su máquina local, cree un archivo denominado `JobDefinition.json` y agréguele los siguientes datos JSON:

```json
{    
   "location":"WestUS2",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"adjust",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
   }
}
```

Después, ejecute el cmdlet `New-AzureRmStreamAnalyticsJob`. Asegúrese de reemplazar el valor de la variable `jobDefinitionFile` por la ruta de acceso a la ubicación en la que haya almacenado el archivo JSON de definición del trabajo. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -File $jobDefinitionFile `
  -Name $jobName `
  -Force 
```

## <a name="configure-input-to-the-job"></a>Configuración de la entrada al trabajo

Agregue una entrada al trabajo mediante el cmdlet [New-AzureRmStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre de entrada del trabajo, el nombre del grupo de recursos y la definición de entrada del trabajo. La definición de la entrada del trabajo es un archivo JSON que contiene las propiedades necesarias para configurar la entrada del trabajo. En este ejemplo, creará un almacenamiento de blobs como entrada. 

En su máquina local, cree un archivo denominado `JobInputDefinition.json` y agréguele los siguientes datos JSON. Asegúrese de reemplazar el valor de `accountKey` por la clave de acceso de su cuenta de almacenamiento que sea el valor almacenado en $storageAccountKey. 

```json
{
    "properties": {
        "type": "Stream",
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                {
                   "accountName": "mystorageaccount",
                   "accountKey":"<storage account key>"
                }],
                "container": "streamanalytics",
                "pathPattern": "input/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8"
            }
        }
    },
    "name": "MyBlobInput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/inputs"
}
```

Después, ejecute el cmdlet `New-AzureRmStreamAnalyticsInput` y asegúrese de reemplazar el valor de la variable `jobDefinitionFile` por la ruta de acceso a la ubicación en la que haya almacenado el archivo JSON de definición de la entrada del trabajo. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRmStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configuración de la salida al trabajo

Agregue una salida al trabajo mediante el cmdlet [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre de salida del trabajo, el nombre del grupo de recursos y la definición de salida del trabajo. La definición de la salida del trabajo es un archivo JSON que contiene las propiedades necesarias para configurar la salida del trabajo. En este ejemplo se usa el almacenamiento de blobs como salida. 

En su máquina local, cree un archivo denominado `JobOutputDefinition.json` y agréguele los siguientes datos JSON. Asegúrese de reemplazar el valor de `accountKey` por la clave de acceso de su cuenta de almacenamiento que sea el valor almacenado en $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<storage account key>"
                    }],
                "container": "streamanalytics",
                "pathPattern": "output/",
                "dateFormat": "yyyy/MM/dd",
                "timeFormat": "HH"
            }
        },
        "serialization": {
            "type": "Json",
            "properties": {
                "encoding": "UTF8",
                "format": "LineSeparated"
            }
        }
    },
    "name": "MyBlobOutput",
    "type": "Microsoft.StreamAnalytics/streamingjobs/outputs"
}
```

Después, ejecute el cmdlet `New-AzureRmStreamAnalyticsOutput`. Asegúrese de reemplazar el valor de la variable `jobOutputDefinitionFile` por la ruta de acceso a la ubicación en la que haya almacenado el archivo JSON de definición de la salida del trabajo. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRmStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobOutputDefinitionFile `
  -Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definir la consulta de transformación

Agregue una transformación al trabajo mediante el cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre de transformación del trabajo, el nombre del grupo de recursos y la definición de transformación del trabajo. En su máquina local, cree un archivo denominado `JobTransformationDefinition.json` y agréguele los siguientes datos JSON. El archivo JSON contiene un parámetro de consulta que define la consulta de transformación:

```json
{     
   "name":"MyTransformation",  
   "type":"Microsoft.StreamAnalytics/streamingjobs/transformations",  
   "properties":{    
      "streamingUnits":1,  
      "script":null,  
      "query":" SELECT System.Timestamp AS OutputTime, dspl AS SensorName, Avg(temp) AS AvgTemperature INTO MyBlobOutput FROM MyBlobInput TIMESTAMP BY time GROUP BY TumblingWindow(second,30),dspl HAVING Avg(temp)>100"  
   }  
}
```

Después, ejecute el cmdlet `New-AzureRmStreamAnalyticsTransformation`. Asegúrese de reemplazar el valor de la variable `jobTransformationDefinitionFile` por la ruta de acceso a la ubicación en la que haya almacenado el archivo JSON de definición de la transformación del trabajo. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRmStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobTransformationDefinitionFile `
  -Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Inicio del trabajo de Stream Analytics y consulta de la salida

Inicie el trabajo mediante el cmdlet [Start-AzureRmStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre del grupo de recursos, el modo de inicio de salida y el tiempo de inicio. `OutputStartMode` acepta valores de `JobStartTime`, `CustomTime` o `LastOutputEventTime`. Para más información acerca de los elementos a los que hacen referencia cada uno de estos valores, consulte la sección de [parámetros](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) de la documentación de PowerShell. En este ejemplo, especifique el modo como `CustomTime` e indique el valor de `OutputStartTime`. 

Para el valor de tiempo, seleccione `2018-01-01`. Esta fecha de inicio se elige porque es anterior a la marca de tiempo del evento de los datos del ejemplo. Después de ejecutar el siguiente cmdlet, devuelve `True` como salida si el trabajo se inicia. En el contenedor de almacenamiento, se crea una carpeta de salida con los datos transformados. 

```powershell
Start-AzureRmStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-01-01T00:00:00Z 
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el trabajo de streaming y todos los recursos relacionados. La eliminación del trabajo evita la facturación de las unidades de streaming utilizadas por el trabajo. Si planea utilizar el trabajo en el futuro, puede omitir su eliminación y simplemente detenerlo por el momento. Si no va a seguir usando este trabajo, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido mediante la ejecución del siguiente cmdlet:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un sencillo trabajo de Stream Analytics. Para aprender a configurar otros orígenes de entrada y realizar la detección en tiempo real, continúe con el siguiente artículo:

> [!div class="nextstepaction"]
> [Detección de fraudes en tiempo real con Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
