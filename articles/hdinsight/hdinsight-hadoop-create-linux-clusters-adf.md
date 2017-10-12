---
title: "Creación de clústeres de Hadoop a petición mediante Data Factory - Azure HDInsight | Microsoft Docs"
description: "Aprenda a crear clústeres de Hadoop en HDInsight mediante Azure Data Factory."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: c1061811d205494969047fa3f91cbf449a25d8ab
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Creación de clústeres de Hadoop en HDInsight mediante Azure Data Factory
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. Puede crear un clúster Hadoop de HDInsight Just-In-Time para procesar un segmento de datos de entrada y eliminar el clúster cuando el procesamiento está completo. Algunas de las ventajas de usar un clúster de Hadoop de HDInsight a petición son:

- Solo se paga por el tiempo en que el trabajo se ejecuta en el clúster de Hadoop de HDInsight (más un breve período de inactividad configurable). La facturación de los clústeres de HDInsight se prorratea por minuto, tanto si se usan como si no. Cuando se usa un servicio vinculado de HDInsight de petición en Data Factory, los clústeres se crean a petición. Además, los clústeres se eliminan automáticamente cuando se completan los trabajos. Por lo tanto, solo se paga por tiempo durante el que se ejecuta el trabajo y el breve tiempo de inactividad (configuración del período de vida).
- Puede crear un flujo de trabajo mediante una canalización de Data Factory. Por ejemplo, puede hacer que la canalización copie datos de un servidor de SQL Server local a una instancia de Azure Blob Storage, procesar los datos mediante la ejecución de un script de Hive y un script de Pig en un clúster de Hadoop de HDInsight a petición. Después, copie los datos de resultado a una instancia de Azure SQL Data Warehouse para que las aplicaciones BI los consuman.
- Puede programar que el flujo de trabajo se ejecute periódicamente (cada hora, diariamente, semanalmente, mensualmente, etcétera).

En Azure Data Factory, una factoría de datos puede tener una o varias canalizaciones de datos. Una canalización de datos consta de una o varias actividades. Hay dos tipos de actividades: [actividades de movimiento de datos](../data-factory/copy-activity-overview.md) y [actividades de transformación de datos](../data-factory/transform-data.md). Las actividades de movimiento de datos (en la actualidad, solo actividad de copia) se usan para mover datos de un almacén de datos de origen a un almacén de datos de destino. Los actividades de transformación de datos se usan para procesar o transformar datos. La actividad de Hive de HDInsight es una de las actividades de transformación de datos compatibles con Data Factory. En este tutorial se usa la actividad de transformación de Hive.

Puede configurar una actividad de Hive para usar su propio clúster de Hadoop de HDInsight o un clúster de Hadoop de HDInsight a petición. En este tutorial, la actividad de Hive de la canalización de la factoría de datos está configurada para utilizar un clúster de HDInsight a petición. Por lo tanto, cuando se ejecuta la actividad para procesar un segmento de datos, esto es lo que ocurre:

1. Se crea automáticamente un clúster de Hadoop de HDInsight para que just-in-time procese el segmento.  
2. Los datos de entrada se procesan mediante la ejecución de un script de HiveQL en el clúster.
3. El clúster de Hadoop de HDInsight se elimina cuando se ha completado el procesamiento y el clúster está inactivo durante el período configurado (valor timeToLive). Si el siguiente segmento de datos está disponible para su procesamiento con en este tiempo de inactividad timeToLive, el mismo clúster se usa para procesar el segmento.  

En este tutorial, el script de HiveQL asociado con la actividad de Hive realiza las siguientes acciones:

1. Crea primero una tabla externa que hace referencia a los datos de registro web sin procesar que se almacenan en Azure Blob Storage.
2. Separa los datos sin procesar por año y mes.
3. Almacena los datos separados en Azure Blob Storage.

En este tutorial, el script de HiveQL asociado con la actividad de Hive crea una tabla externa que hace referencia a los datos de registro web sin procesar almacenados en Azure Blob Storage. Aquí están las filas de ejemplo para cada mes en el archivo de entrada.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

El script de HiveQL crea particiones de los datos sin procesar por año y mes. Crea tres carpetas de salida basadas en la entrada anterior. Cada carpeta contiene un archivo con las entradas de cada mes.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Para obtener una lista de actividades de transformación de datos de Data Factory además de la actividad de Hive, vea [Transformar y analizar mediante Data Factory de Azure](../data-factory/transform-data.md).

> [!NOTE]
> Actualmente, solo se puede crear un clúster de HDInsight versión 3.2 desde Azure Data Factory.

## <a name="prerequisites"></a>Requisitos previos
Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

* [Suscripción de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Preparación de la cuenta de almacenamiento
Puede utilizar hasta tres cuentas de almacenamiento en este escenario:

- la cuenta de almacenamiento predeterminado para el clúster de HDInsight
- la cuenta de almacenamiento para los datos de entrada
- la cuenta de almacenamiento para los datos de salida

Para simplificar el tutorial, utilizará una cuenta de almacenamiento para los 3 objetivos. El script de ejemplo de Azure PowerShell de esta sección realiza las siguientes tareas:

1. Inicie sesión en Azure.
2. Cree un grupo de recursos de Azure.
3. Cree una cuenta de almacenamiento de Azure.
4. Cree un contenedor de blobs en la cuenta de almacenamiento.
5. Copie los dos archivos siguientes en el contenedor de blobs:

   * Archivo de datos de entrada: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Script de HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Ambos archivos se almacenan en un contenedor de blobs público.


**Para preparar el almacenamiento y copiar los archivos mediante Azure PowerShell:**
> [!IMPORTANT]
> Especifique los nombres del grupo de recursos de Azure y la cuenta de Azure Storage que creará el script.
> Anote el **nombre del grupo de recursos**, el **nombre de la cuenta de almacenamiento** y la **clave de la cuenta de almacenamiento** que genere el script. Los necesitará en la siguiente sección.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Si necesita ayuda con el script de PowerShell, consulte [Uso de Azure PowerShell con Azure Storage](../storage/common/storage-powershell-guide-full.md). Si desea usar la CLI de Azure en su lugar, consulte la sección [Apéndice](#appendix) del script de la CLI de Azure.

**Para examinar la cuenta de almacenamiento y el contenido**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Grupos de recursos** en el panel izquierdo.
3. Haga doble clic en el nombre del grupo de recursos que creó en el script de PowerShell. Utilice el filtro si se muestran demasiados grupos de recursos.
4. En el icono **Recursos** , aparecerá un recurso a menos que comparta el grupo de recursos con otros proyectos. Ese recurso es la cuenta de almacenamiento con el nombre que especificó anteriormente. Haga clic en el nombre de la cuenta de almacenamiento.
5. Haga clic en los iconos **Blobs** .
6. Haga clic en el contenedor **adfgetstarted** . Verá dos carpetas: **inputdata** y **script**.
7. Abra la carpeta y compruebe los archivos en las carpetas. La carpeta inputdata contiene el archivo input.log con datos de entrada, mientras que la carpeta script contiene el archivo de script de HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Creación de una factoría de datos mediante una plantilla de Resource Manager
Con la cuenta de almacenamiento, los datos de entrada y el script de HiveQL preparados, está listo para crear Data Factory. Existen varios métodos para crear Data Factory. En este tutorial, creará una factoría de datos mediante la implementación de una plantilla de Azure Resource Manager mediante Azure Portal. También puede implementar una plantilla de Resource Manager mediante la [CLI de Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) y [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Para otros métodos de creación de Data Factory, vea [Tutorial: creación de la  primera Data Factory](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en Azure Portal. La plantilla se encuentra en https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Consulte la sección [Entidades de Data Factory en la plantilla](#data-factory-entities-in-the-template) para obtener información detallada acerca de las entidades definidas en la plantilla. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Seleccione la opción **Usar existente** de la configuración de **Grupo de recursos** y seleccione el nombre del grupo de recursos que creó en el paso anterior (mediante el script de PowerShell).
3. Escriba el nombre de la factoría de datos (**Nombre de factoría de datos**). Este nombre debe ser único globalmente.
4. Escriba el **nombre de la cuenta de almacenamiento** y la **clave de la cuenta de almacenamiento** que anotó en el paso anterior.
5. Tras leer los **términos y condiciones** seleccione **Acepto los términos y condiciones indicados anteriormente**.
6. Seleccione la opción **Anclar al panel**.
6. Haga clic en **Comprar o crear**. Verá un icono en el Panel denominado **Realización de implementación de la plantilla**. Espere a que se abra la hoja **Grupo de recursos** de su grupo de recursos. También puede hacer clic en el icono con el mismo nombre que el grupo de recursos para abrir la hoja del grupo de recursos.
6. Si la hoja del grupo de recursos no está abierta, haga clic en el icono para abrir el grupo de recursos. Ahora verá un recurso de Data Factory más, además del recurso de la cuenta de almacenamiento.
7. Haga clic en el nombre de la factoría de datos (el valor que especificó en el parámetro **Nombre de factoría de datos**).
8. En la hoja Data Factory, haga clic en el icono **Diagrama**. El diagrama muestra una actividad con un conjunto de datos de entrada y un conjunto de datos de salida:

    ![Diagrama de la canalización de la actividad de Hive bajo demanda de HDInsight para Data Factory de Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Los nombres se definen en la plantilla de Resource Manager.
9. Haga doble clic en **AzureBlobOutput**.
10. En los **segmentos actualizados recientemente**, verá un segmento. Si el estado es **En curso**, espere hasta que se cambie a **Listo**. Se suele tarda aproximadamente **20 minutos** en crear un clúster de HDInsight.

### <a name="check-the-data-factory-output"></a>Comprobación de la salida de Data Factory

1. Utilice el mismo procedimiento en la última sesión para comprobar el contenido de los contenedores adfgetstarted. Hay dos nuevos contenedores además de **adfgetsarted**:

   * Un contenedor con nombre que sigue el patrón: `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Este es el contenedor predeterminado del clúster de HDInsight.
   * adfjobs: este es el contenedor de los registros de trabajo de ADF.

     La salida de Data Factory se almacena en **afgetstarted**, tal lo configuró en la plantilla de Resource Manager.
2. Haga clic en **adfgetstarted**.
3. Haga doble clic en **partitioneddata**. Se ve una carpeta **year=2014**, ya que todos los registros de web son del año 2014.

    ![Salida de la canalización de la actividad de Hive bajo demanda de HDInsight para Data Factory de Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Si profundiza en la lista, verá 3 carpetas de enero, febrero y marzo. Además, hay un registro para cada mes.

    ![Salida de la canalización de la actividad de Hive bajo demanda de HDInsight para Data Factory de Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Entidades de Data Factory en la plantilla
Este es el aspecto de la plantilla de Resource Manager de nivel superior para una factoría de datos:

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Definición de factoría de datos
Puede definir una factoría de datos en la plantilla de Resource Manager como se muestra en el ejemplo siguiente:  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
dataFactoryName es el nombre de la factoría de datos que se especifica al implementar la plantilla. Data Factory solo se admite actualmente en las zonas del este de EE. UU., oeste de EE. UU. y Europa del Norte.

### <a name="defining-entities-within-the-data-factory"></a>Definición de entidades en la factoría de datos
Las siguientes entidades de Data Factory se definen en la plantilla JSON:

* [Servicio vinculado de Almacenamiento de Azure](#azure-storage-linked-service)
* [Servicio vinculado a petición de HDInsight](#hdinsight-on-demand-linked-service)
* [Conjunto de datos de entrada de blob de Azure:](#azure-blob-input-dataset)
* [Conjunto de datos de salida de blob de Azure](#azure-blob-output-dataset)
* [Canalización de datos con una actividad de copia](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Servicio vinculado de Almacenamiento de Azure
El servicio vinculado Azure Storage vincula una cuenta de Azure Storage a la factoría de datos. En este tutorial, la misma cuenta de almacenamiento se usa como la cuenta de almacenamiento para HDInsight predeterminada, el almacenamiento de datos de entrada y el almacenamiento de datos de salida. Por consiguiente, se define solo un servicio vinculado Azure Storage. En la definición del servicio vinculado, especifique el nombre y la clave de su cuenta de Azure Storage. Consulte [Servicio vinculado de Azure Storage](../data-factory/connector-azure-blob-storage.md) para más información sobre las propiedades JSON usadas para definir un servicio vinculado de Azure Storage.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
**connectionString** usa los parámetros storageAccountName y storageAccountKey. Especifique los valores de estos parámetros al implementar la plantilla.  

#### <a name="hdinsight-on-demand-linked-service"></a>Servicio vinculado a petición de HDInsight
En la definición del servicio vinculado de HDInsight a petición, especifique los valores de los parámetros de configuración que usa el servicio Data Factory para crear un clúster de Hadoop de HDInsight en runtime. Consulte el artículo [Servicios vinculados de proceso](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) para más información sobre las propiedades JSON que se usan para definir un servicio vinculado a petición de HDInsight.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Tenga en cuenta los siguientes puntos:

* Data Factory crea automáticamente un cluster de HDInsight **basado en Linux**.
* El clúster de Hadoop de HDInsight se crea en la misma región que la cuenta de almacenamiento.
* Observe la configuración de *timeToLive* . Data Factory elimina el clúster automáticamente después de que esté inactivo durante 30 minutos.
* El clúster de HDInsight crea un **contenedor predeterminado** en el almacenamiento de blobs que especificó en JSON (**linkedServiceName**). HDInsight no elimina este contenedor cuando se elimina el clúster. Este comportamiento es así por diseño. Con el servicio vinculado de HDInsight a petición se crea un clúster de HDInsight cada vez tenga que procesarse un segmento, a menos que haya un clúster existente activo (**timeToLive**), que se elimina cuando finaliza el procesamiento.

Para más información, consulte la sección [Servicio vinculado a petición de HDInsight de Azure](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

> [!IMPORTANT]
> A medida que se procesen más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. Los nombres de estos contenedores siguen este patrón: "adf**nombredefactoría dedatos**-**nombredelserviciovinculado**-marcadefechayhora". Use herramientas como el [Explorador de almacenamiento de Microsoft](http://storageexplorer.com/) para eliminar contenedores de Almacenamiento de blobs de Azure.

#### <a name="azure-blob-input-dataset"></a>Conjunto de datos de entrada de blob de Azure
En la definición del conjunto de datos de entrada, especifique los nombres del contenedor de blobs, la carpeta y el archivo que contiene los datos de entrada. Consulte las [propiedades del conjunto de datos de Azure Blob](../data-factory/connector-azure-blob-storage.md) para más información sobre las propiedades JSON usadas para definir un conjunto de datos de Azure Blob.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Observe la siguiente configuración concreta en la definición de JSON:

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Conjunto de datos de salida de blob de Azure
En la definición del conjunto de datos de salida, especifique los nombres del contenedor de blobs y de la carpeta que contiene los datos de salida. Consulte las [propiedades del conjunto de datos de Azure Blob](../data-factory/connector-azure-blob-storage.md) para más información sobre las propiedades JSON usadas para definir un conjunto de datos de Azure Blob.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

folderPath especifica la ruta de acceso a la carpeta que contiene los datos de salida:

```json
"folderPath": "adfgetstarted/partitioneddata",
```

La configuración de [disponibilidad del conjunto de datos](../data-factory/concepts-datasets-linked-services.md) es la siguiente:

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

En Data Factory de Azure, la disponibilidad del conjunto de datos de salida activa la canalización. En este ejemplo, el segmento se genera el último día de cada mes (EndOfInterval). 

#### <a name="data-pipeline"></a>Canalización de datos
Defina una canalización que transforme los datos mediante la ejecución de un script de Hive en un clúster de Azure HDInsight a petición. Consulte [JSON de canalización](../data-factory/concepts-pipelines-activities.md) para obtener descripciones de elementos JSON que se usan para definir una canalización en este ejemplo.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

La canalización contiene una actividad, la actividad de HDInsightHive. Como las fechas de inicio y finalización están en enero de 2016, se procesan los datos de un solo mes (un segmento). Las fechas de *inicio* y *finalización* de la actividad tienen una fecha pasada, por lo que Data Factory procesa los datos del mes de inmediato. Si el extremo es una fecha futura, Data Factory creará otro segmento cuando llegue la hora. Para obtener más información, vea [Programación y ejecución de Data Factory](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Limpieza del tutorial

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Elimine los contenedores de blobs creados por el clúster de HDInsight a petición
Con el servicio vinculado de HDInsight a petición, se crea un clúster de HDInsight cada vez que un segmento debe procesarse, a menos que haya un clúster existente activo (timeToLive), y el clúster se elimina cuando se realiza el procesamiento. Para cada clúster, Azure Data Factory crea un contenedor de blobs en Azure Blob Storage que se usa como cuenta de almacenamiento predeterminada del clúster. Aunque se elimine el clúster de HDInsight, el contenedor de almacenamiento de blobs predeterminado y la cuenta de almacenamiento asociada no se eliminan. Este comportamiento es así por diseño. A medida que se procesen más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. Los nombres de estos contenedores siguen un patrón: `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Elimine las carpetas **adfjobs** y **adfyourdatafactoryname-linkedservicename-datetimestamp**. El contenedor adfjobs contiene registros de trabajo de Azure Data Factory.

### <a name="delete-the-resource-group"></a>Eliminar el grupo de recursos
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) se usa para implementar, administrar y supervisar una solución como un grupo.  Al eliminar un grupo de recursos, se eliminan todos sus componentes.  

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Grupos de recursos** en el panel izquierdo.
3. Haga clic en el nombre del grupo de recursos que creó en el script de PowerShell. Utilice el filtro si se muestran demasiados grupos de recursos. El grupo de recursos se abre en una nueva hoja.
4. En el icono **Recursos** , aparecerá la cuenta de almacenamiento predeterminada y Data Factory a menos que comparta el grupo de recursos con otros proyectos.
5. Haga clic en **Eliminar** en la parte superior de la hoja. Así se eliminan también la cuenta de almacenamiento y los datos almacenados en ella.
6. Escriba el nombre del grupo de recursos para confirmar la eliminación y, después, haga clic en **Eliminar**.

Si no desea eliminar la cuenta de almacenamiento al eliminar el grupo de recursos, considere la siguiente arquitectura mediante la separación de los datos empresariales de la cuenta de almacenamiento predeterminada. En este caso, tiene un grupo de recursos para la cuenta de almacenamiento con los datos empresariales y el otro grupo de recursos para la cuenta de almacenamiento predeterminada del servicio vinculado de HDInsight y Data Factory. Cuando se elimina el segundo grupo de recursos, no afecta a la cuenta de almacenamiento de datos empresariales. Para ello:

* Agregue lo siguiente al grupo de recursos de nivel superior junto con el recurso Microsoft.DataFactory/datafactories en la plantilla de Resource Manager. Crea una cuenta de almacenamiento:

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Agregue un nuevo punto de servicio vinculado a la nueva cuenta de almacenamiento:

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Configure el servicio vinculado bajo demanda de HDInsight con un dependsOn y un additionalLinkedServiceNames adicionales:

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Pasos siguientes
En este artículo, ha aprendido cómo utilizar la Data Factory de Azure para crear el clúster de HDInsight bajo demanda para procesar los trabajos de Hive. Para obtener más información:

* [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
* [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Documentación de Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Anexo

### <a name="azure-cli-script"></a>Script de la CLI de Azure
Para realizar el tutorial se puede utilizar la CLI de Azure, en lugar de Azure PowerShell. Para usar la CLI de Azure, instálela siguiendo estas instrucciones:

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Use la CLI de Azure para preparar el almacenamiento y copiar los archivos

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

El nombre del contenedor es *adfgetstarted*. Déjelo como está. De lo contrario, tendrá que actualizar la plantilla de Resource Manager. Si necesita ayuda con este script de la CLI, vea [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/common/storage-azure-cli.md).
