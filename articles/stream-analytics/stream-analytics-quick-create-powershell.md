---
title: Creación de un trabajo de Stream Analytics mediante Azure PowerShell | Microsoft Docs
description: En esta guía de inicio rápido se detalla cómo usar el módulo de Azure PowerShell para implementar y ejecutar un trabajo de Azure Stream Analytics.
services: stream-analytics
keywords: Stream analytics, Cloud jobs, Azure PowerShell, job input, job output, job transformation
author: SnehaGunda
ms.author: sngun
ms.date: 03/16/2018
ms.topic: quickstart
ms.service: stream-analytics
ms.custom: mvc
manager: kfile
ms.openlocfilehash: 8a1036531ea0e7c1426224bc4d42c83e9049cabf
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
---
# <a name="quickstart-create-a-stream-analytics-job-by-using-azure-powershell"></a>Guía de inicio rápido: Creación de un trabajo de Stream Analytics mediante Azure PowerShell

El módulo de Azure PowerShell se usa para crear y administrar recursos de Azure mediante cmdlets o scripts de PowerShell. En esta guía de inicio rápido se detalla cómo usar el módulo de Azure PowerShell para implementar y ejecutar un trabajo de Azure Stream Analytics.

## <a name="before-you-begin"></a>Antes de empezar

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/).  

* Para realizar los pasos de esta guía, se requiere la versión 3.6 del módulo Azure PowerShell, o cualquier versión posterior. Ejecute `Get-Module -ListAvailable AzureRM` para buscar la versión que está instalada en la máquina local. Si necesita instalarla o actualizarla, consulte el artículo [Instalación y configuración de Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-azurerm-ps). El escenario de este artículo describe la lectura de datos de almacenamiento de blobs, transforma los datos y los escribe de nuevo en un contenedor diferente en el mismo almacenamiento de blobs.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en la suscripción de Azure con el comando `Connect-AzureRmAccount` y especifique las credenciales de Azure en el explorador emergente. Después de iniciar sesión, si tiene varias suscripciones, seleccione la suscripción que desea utilizar para esta guía de inicio rápido mediante la ejecución de los siguientes cmdlets. Asegúrese de reemplazar <your subscription> por el nombre de su suscripción:  

```powershell
# Log in to your Azure account
Connect-AzureRmAccount

# Select the Azure subscription you want to use to create the resource group.
Get-AzureRmSubscription `
  -SubscriptionName “<your subscription>” | Select-AzureRmSubscription
```

## <a name="create-a-resource-group"></a>Crear un grupo de recursos

Cree un grupo de recursos de Azure con [New-AzureRmResourceGroup](https://docs.microsoft.com/powershell/module/azurerm.resources/new-azurermresourcegroup). Un grupo de recursos es un contenedor lógico en el que se implementan y se administran los recursos de Azure.

```powershell
$resourceGroup = "ASAPSRG"
$location = "WestUS2"
New-AzureRMResourceGroup `
  -Name $resourceGroup `
  -Location $location 
```

## <a name="prepare-the-input-data"></a>Preparación de los datos de entrada

Antes de definir el trabajo de Stream Analytics, debe preparar los datos que se configuran como entrada para el trabajo. Ejecute los pasos siguientes para preparar los datos de entrada requeridos por el trabajo: 

1. Descargue los [datos de ejemplo del sensor](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) desde GitHub.  

2. Cree una cuenta de almacenamiento estándar de uso general con replicación de almacenamiento con redundancia local mediante el cmdlet [New-AzureRmStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.storage/New-AzureRmStorageAccount)  

3. Recupere el contexto de la cuenta de almacenamiento que define la cuenta de almacenamiento que se va a usar. Cuando trabaje con cuentas de almacenamiento, haga referencia al contexto en lugar de proporcionar varias veces las credenciales. En este ejemplo va a crear una cuenta de almacenamiento denominada mystorageaccount con el cifrado de blob y el almacenamiento con redundancia local (LRS) (habilitados de forma predeterminada).  

4. A continuación, va a crear un contenedor con [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer), establezca los permisos en 'blob' para permitir el acceso público a los archivos y cargue los [datos de muestra del sensor](https://github.com/Azure/azure-stream-analytics/blob/master/Samples/GettingStarted/HelloWorldASA-InputStream.json) que descargó anteriormente. 

Estos pasos se consiguen con la ejecución del siguiente script de PowerShell:

```powershell
$storageAccountName = "mystorageaccount"
$storageAccount = New-AzureRmStorageAccount `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS `
  -Kind Storage

$ctx = $storageAccount.Context
$containerName = "myinputcontainer"

New-AzureStorageContainer `
  -Name $containerName `
  -Context $ctx `
  -Permission blob

Set-AzureStorageBlobContent `
  -File "C:\HelloWorldASA-InputStream.json" `
  -Container $containerName `
  -Context $ctx  

$storageAccountKey = (Get-AzureRmStorageAccountKey `
  -ResourceGroupName $resourceGroup `
  -Name $storageAccountName).Value[0]
```

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

Cree un trabajo de Stream Analytics con el cmdlet [New-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsjob?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre del grupo de recursos y la definición del trabajo. El nombre del trabajo debe ser cualquier nombre descriptivo que identifique el trabajo; el nombre del trabajo de Stream Analytics debe tener una longitud de entre 3 y 63 caracteres, y solo puede incluir caracteres alfanuméricos, guiones y guiones bajos. La definición del trabajo es un archivo JSON que contiene las propiedades necesarias para crear un trabajo. En la máquina local, cree un archivo denominado "JobDefinition.json" y agréguele los siguientes datos JSON:

```json
{    
   "location":"Central US",  
   "properties":{    
      "sku":{    
         "name":"standard"  
      },  
      "eventsOutOfOrderPolicy":"drop",  
      "eventsOutOfOrderMaxDelayInSeconds":10,  
      "compatibilityLevel": 1.1
}
}
```

A continuación, ejecute el cmdlet New-AzureRMStreamAnalyticsJob y asegúrese de reemplazar el valor de la variable jobDefinitionFile por la ruta de acceso donde ha almacenado el archivo JSON de definición del trabajo. 

```powershell
$jobName = "MyStreamingJob"
$jobDefinitionFile = "C:\JobDefinition.json"
New-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  –File $jobDefinitionFile `
  –Name $jobName -Force 
```

## <a name="configure-input-to-the-job"></a>Configuración de la entrada al trabajo

Agregue una entrada al trabajo mediante el cmdlet [New-AzureRMStreamAnalyticsInput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsinput?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre de entrada del trabajo, el nombre del grupo de recursos y la definición de entrada del trabajo. La definición de entrada del trabajo es un archivo JSON que contiene las propiedades necesarias para configurar la entrada del trabajo. En este ejemplo, va a crear un almacenamiento de blobs como entrada. En la máquina local, cree un archivo denominado "JobInputDefinition.json" y agréguele los siguientes datos JSON y asegúrese de reemplazar el valor de **accountKey**  por la clave de acceso de la cuenta de almacenamiento, que es el valor almacenado en el valor $storageAccountKey. 

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
                   "accountKey":"<Your storage account key>"
                }],
                "container": "myinputcontainer",
                "pathPattern": "",
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

A continuación, ejecute el cmdlet New-AzureRMStreamAnalyticsInput y asegúrese de reemplazar el valor de la variable jobDefinitionFile por la ruta de acceso donde ha almacenado el archivo JSON de definición de entrada del trabajo. 

```powershell
$jobInputName = "MyBlobInput"
$jobInputDefinitionFile = "C:\JobInputDefinition.json"
New-AzureRMStreamAnalyticsInput `
  -ResourceGroupName $resourceGroup `
  -JobName $jobName `
  -File $jobInputDefinitionFile `
  -Name $jobInputName 
```

## <a name="configure-output-to-the-job"></a>Configuración de la salida al trabajo

Agregue una salida al trabajo mediante el cmdlet [New-AzureRmStreamAnalyticsOutput](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticsoutput?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre de salida del trabajo, el nombre del grupo de recursos y la definición de salida del trabajo. La definición de salida del trabajo es un archivo JSON que contiene las propiedades necesarias para configurar la salida del trabajo. En este ejemplo, va a crear un almacenamiento de blobs como salida. En la máquina local, cree un archivo denominado "JobOutputDefinition.json" y agréguele los siguientes datos JSON y asegúrese de reemplazar el valor de **accountKey**  por la clave de acceso de la cuenta de almacenamiento, que es el valor almacenado en el valor $storageAccountKey. 

```json
{
    "properties": {
        "datasource": {
            "type": "Microsoft.Storage/Blob",
            "properties": {
                "storageAccounts": [
                    {
                      "accountName": "mystorageaccount",
                      "accountKey": "<Your storage account key>"
                    }],
                "container": "myoutputcontainer",
                "pathPattern": "",
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

A continuación, ejecute el cmdlet New-AzureRMStreamAnalyticsOutput y asegúrese de reemplazar el valor de la variable jobOutputDefinitionFile por la ruta de acceso donde ha almacenado el archivo JSON de definición de salida del trabajo. 

```powershell
$jobOutputName = "MyBlobOutput"
$jobOutputDefinitionFile = "C:\JobOutputDefinition.json"
New-AzureRMStreamAnalyticsOutput `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobOutputDefinitionFile `
  –Name $jobOutputName -Force 
```

## <a name="define-the-transformation-query"></a>Definir la consulta de transformación

Agregue una transformación al trabajo mediante el cmdlet [New-AzureRmStreamAnalyticsTransformation](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/new-azurermstreamanalyticstransformation?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre de transformación del trabajo, el nombre del grupo de recursos y la definición de transformación del trabajo. En la máquina local, cree un archivo denominado "JobTransformationDefinition.json" y agregue los siguientes datos JSON a él. El archivo JSON contiene un parámetro de consulta que define la consulta de transformación:

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

A continuación, ejecute el cmdlet New-AzureRMStreamAnalyticsTransformation y asegúrese de reemplazar el valor de la variable jobTransformationDefinitionFile por la ruta de acceso donde ha almacenado el archivo JSON de definición de transformación del trabajo. 

```powershell
$jobTransformationName = "MyJobTransformation"
$jobTransformationDefinitionFile = "C:\JobTransformationDefinition.json"
New-AzureRMStreamAnalyticsTransformation `
  -ResourceGroupName $resourceGroup `
  –JobName $jobName `
  –File $jobTransformationDefinitionFile `
  –Name $jobTransformationName -Force
```

## <a name="start-the-stream-analytics-job-and-check-the-output"></a>Inicio del trabajo de Stream Analytics y consulta de la salida

Inicie el trabajo mediante el cmdlet [Start-AzureRMStreamAnalyticsJob](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0). Este cmdlet toma como parámetros el nombre del trabajo, el nombre del grupo de recursos, el modo de inicio de salida y el tiempo de inicio. OUtpputStartMode acepta dos valores JobStartTime, CustomTime o LastOutputEventTime para saber a qué se refieren cada uno de estos valores, consulte la sección de [parámetros ](https://docs.microsoft.com/powershell/module/azurerm.streamanalytics/start-azurermstreamanalyticsjob?view=azurermps-5.4.0) en la documentación de PowerShell. En este ejemplo, puede especificar el modo como CustomTime y proporcionar un valor para OutputStartTime. 

Para el valor de tiempo, seleccione un día antes a cuando se cargó el archivo en el almacenamiento de blobs, ya que la hora en la que se cargó el archivo es anterior a la hora actual. Después de ejecutar el siguiente cmdlet, devuelve "True" como salida si el trabajo se inicia. Se crea un contenedor denominado "myoutputcontainer" en la cuenta de almacenamiento con los datos transformados. 

```powershell
Start-AzureRMStreamAnalyticsJob `
  -ResourceGroupName $resourceGroup `
  -Name $jobName `
  -OutputStartMode CustomTime `
  -OutputStartTime 2018-03-11T14:45:12Z 
```

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando no los necesite, elimine el grupo de recursos, el trabajo de streaming y todos los recursos relacionados. La eliminación del trabajo evita la facturación de las unidades de streaming utilizadas por el trabajo. Si piensa utilizar el trabajo en el futuro, puede detenerlo y volver a iniciarlo más adelante cuando sea necesario. Si no va a seguir usando este trabajo, siga estos pasos para eliminar todos los recursos creados en esta guía de inicio rápido mediante la ejecución del siguiente cmdlet:

```powershell
Remove-AzureRmResourceGroup `
  -Name $resourceGroup 
```

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha implementado un sencillo trabajo de Stream Analytics. Para aprender a configurar otros orígenes de entrada y realizar la detección en tiempo real, continúe con el siguiente artículo:

> [!div class="nextstepaction"]
> [Detección de fraudes en tiempo real con Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
