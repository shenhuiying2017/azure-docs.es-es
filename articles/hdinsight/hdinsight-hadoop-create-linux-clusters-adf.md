<properties
   pageTitle="Creación de clústeres de Hadoop basados en Linux bajo demanda en HDInsight usando Data Factory de Azure | Microsoft Azure"
   	description="Aprenda a crear clústeres de HDInsight bajo demanda con Data Factory de Azure."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/10/2016"
   ms.author="jgao"/>

# Creación de clústeres de Hadoop basados en Linux bajo demanda en HDInsight usando Data Factory de Azure

[AZURE.INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) es un servicio de integración de datos basado en la nube que organiza y automatiza el movimiento y la transformación de datos. En este artículo, aprenderá cómo usar Data Factory de Azure para crear un [servicio vinculado de Azure HDInsight bajo demanda](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service), y a usar el clúster para ejecutar un trabajo de Hive. Este es el flujo de nivel alto:

1. Crear un clúster de HDInsight bajo demanda.
2. Ejecutar un trabajo de Hive para leer datos de registro web sin procesar de una cuenta de almacenamiento de blobs de origen, transformar los datos y escribir el resultado en una cuenta de almacenamiento de blobs de destino.
3. Eliminar el clúster en función de la configuración de período de vida.

La actividad de Hive definida en las llamadas de canalización de factoría de datos invoca a un script de HiveQL predefinido. El script crea una tabla externa que hace referencia a los datos de blog sin procesar almacenados en Almacenamiento de blobs de Azure y, después, divide los datos sin procesar por año y mes.

Aquí están las filas de ejemplo para cada mes en el archivo de entrada.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

El script crea tres carpetas de salida basadas en la entrada anterior. Cada carpeta contiene un archivo con las entradas de cada mes.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Para obtener una lista de actividades de transformación de datos de Data Factory además de la actividad de Hive, vea [Transformar y analizar mediante Data Factory de Azure](../data-factory/data-factory-data-transformation-activities.md).

Hay muchas ventajas en usar HDInsight con Data Factory:

- La facturación de los clústeres de HDInsight se prorratea por minuto, tanto si los está usando como si no. Mediante Data Factory, los clústeres se crean bajo demanda. Además, los clústeres se eliminan automáticamente cuando se completan los trabajos. Por lo tanto, solo se paga por el trabajo que ejecuta el tiempo y el tiempo de inactividad breve (período de vida).
- Puede crear un flujo de trabajo mediante la canalización de Data Factory.
- Puede programar trabajos recursivos.

##Requisitos previos:

Antes de empezar las instrucciones de este artículo, debe tener lo siguiente:

- [Suscripción de Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- CLI de Azure o Azure PowerShell.

    [AZURE.INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell-and-cli.md)]

##Preparación de la cuenta de almacenamiento

Puede utilizar hasta tres cuentas de almacenamiento en este escenario:

- la cuenta de almacenamiento predeterminado para el clúster de HDInsight
- la cuenta de almacenamiento para los datos de entrada
- la cuenta de almacenamiento para los datos de salida

Para simplificar el tutorial, utilizará una cuenta de almacenamiento para los 3 objetivos. El script de ejemplo de Azure PowerShell y la CLI de Azure de esta sección realiza lo siguiente:

1. Inicie sesión en Azure.
2. Cree un grupo de recursos de Azure.
3. Cree una cuenta de almacenamiento de Azure.
4. Cree un contenedor de blobs en la cuenta de almacenamiento.
5. Copie los dos archivos siguientes en el contenedor de blobs:

    - Archivo de datos de entrada: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - Script de HiveQL: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    Ambos archivos se almacenan en un contenedor de blobs público.

>[AZURE.IMPORTANT] Anote el nombre del grupo de recursos, el nombre de la cuenta de almacenamiento y la clave de cuenta de almacenamiento utilizados en el script. Los necesitará en la siguiente sección.

**Para preparar el almacenamiento y copiar los archivos mediante la CLI de Azure**

    azure login
    
    azure config mode arm

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

El nombre del contenedor es *adfgetstarted*. Déjelo como está. De lo contrario, tendrá que actualizar la plantilla de Resource Manager.

Si necesita ayuda con este script de la CLI, vea [Uso de la CLI de Azure con Almacenamiento de Azure](../storage/storage-azure-cli.md).

**Para preparar el almacenamiento y copiar los archivos mediante Azure PowerShell**

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

Si necesita ayuda con este script de PowerShell, vea [Uso de Azure PowerShell con Almacenamiento de Azure](../storage/storage-powershell-guide-full.md).

**Para examinar la cuenta de almacenamiento y el contenido**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Grupos de recursos** en el panel izquierdo.
3. Haga doble clic en el nombre del grupo de recursos que creó en el script de PowerShell o de la CLI. Utilice el filtro si se muestran demasiados grupos de recursos.
4. En el icono **Recursos**, aparecerá un recurso a menos que comparta el grupo de recursos con otros proyectos. Es la cuenta de almacenamiento con el nombre que especificó anteriormente. Haga clic en el nombre de la cuenta de almacenamiento.
5. Haga clic en los iconos **Blobs**.
6. Haga clic en el contenedor **adfgetstarted**. Verá dos carpetas: **datos de entrada** y **script**.
7. Abra la carpeta y compruebe los archivos en las carpetas.
 
## Creación de Data Factory

Con la cuenta de almacenamiento, los datos de entrada y el script de HiveQL preparados, está listo para crear Data Factory. Existen varios métodos para crear Data Factory. Utilizará el Portal de Azure para llamar a una plantilla de Resource Manager personalizada en este tutorial. También puede llamar a la plantilla de Resource Manager desde la [CLI de Azure](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) y [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell). Para otros métodos de creación de Data Factory, vea [Tutorial: creación de la primera Data Factory](../data-factory/data-factory-build-your-first-pipeline.md).

La plantilla de Resource Manager de nivel superior contiene:

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

Un recurso de Data Factory denominado *hdinsight-hive-on-demand* (el nombre no se muestra en la captura de pantalla). Data Factory solo se admite actualmente en la región Oeste de EE. UU. y de Europa del Norte.

El recurso *hdinsight-hive-on-demand* contiene 4 recursos:

- Un servicio vinculado a la cuenta de almacenamiento que se usará como la cuenta de almacenamiento de HDInsight de forma predeterminada, almacenamiento de datos de entrada y almacenamiento de datos de salida.
- Un servicio vinculado al clúster de HDInsight que se va a crear:

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    Aunque no se especifica, el clúster se crea en la misma región que la cuenta de almacenamiento.
    
    Observe la configuración de *timeToLive*. Data Factory elimina el clúster automáticamente después de que esté inactivo durante 30 minutos.
- Un conjunto de datos para los datos de entrada. Aquí se definen el nombre de archivo y el nombre de carpeta:

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- Un conjunto de datos para los datos de salida y, a continuación, la canalización de procesamiento de datos. Aquí se define la ruta de acceso de salida:
        
        "folderPath": "adfgetstarted/partitioneddata",

    La configuración de [disponibilidad del conjunto de datos](../data-factory/data-factory-create-datasets.md#Availability) es la siguiente:
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    En Data Factory de Azure, la disponibilidad del conjunto de datos de salida activa la canalización. Esto significa que el segmento se genera mensualmente en el último día del mes. Para obtener más información, vea [Programación y ejecución de Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    La definición de canalización es la siguiente:
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    Contiene una actividad. Tanto el *inicio* como el *final* de la actividad tienen una fecha pasada, lo que significa que habrá un único segmento. Si el extremo es una fecha futura, Data Factory creará otro segmento cuando llegue la hora. Para obtener más información, vea [Programación y ejecución de Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    Esta es la definición de actividad:
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasbs://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
    
    Se definen las entradas, salidas y la ruta de acceso del script.
    
**Para crear una Data Factory**

1. Haga clic en la imagen siguiente para iniciar sesión en Azure y abrir la plantilla de Resource Manager en el Portal de Azure. La plantilla se encuentra en https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Escriba **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** y **STORAGEACCOUNTKEY** para la cuenta que creó en la última sección y, a continuación, haga clic en **Aceptar**. El nombre de Data Factory debe ser único globalmente.
3. En **Grupo de recursos**, seleccione el mismo grupo de recursos que usó en la última sección.
4. Haga clic en **Términos legales** y luego en **Crear**.
5. Haga clic en **Crear**. Verá un icono en el Panel denominado **Realización de implementación de la plantilla**. Espere hasta que el texto del icono cambie al nombre de grupo de recursos. Se tarda aproximadamente 20 minutos en crear un clúster de HDInsight.
6. Haga clic en el icono para abrir el grupo de recursos. Ahora verá un recurso de Data Factory más, además del recurso de la cuenta de almacenamiento.
7. Haga clic en **hdinsight-hive-on-demand**.
8. Haga clic en el icono **Diagrama**. El diagrama muestra una actividad con un conjunto de datos de entrada y un conjunto de datos de salida:

    ![Diagrama de la canalización de la actividad de Hive bajo demanda de HDInsight para Data Factory de Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    Los nombres se definen en la plantilla de Resource Manager.
9. Haga doble clic en **AzureBlobOutput**.
10. En los **segmentos actualizados recientemente**, verá un segmento. Si el estado es **En curso**, espere hasta que se cambie a **Listo**.

**Para comprobar la salida de Data Factory**

1. Utilice el mismo procedimiento en la última sesión para comprobar el contenido del contenedor adfgetstarted. Hay dos nuevos contenedores además de **adfgetsarted**:

    - adfhdinsight-Hive-on-Demand-hdinsightondemandlinked-xxxxxxxxxxxxx: este es el contenedor predeterminado del clúster de HDInsight. El nombre del contenedor predeterminado sigue el patrón: "adf >nombredatafactory>-nombreserviciovinculado-marcafechahora".
    - adfjobs: este es el contenedor para los registros de trabajo de ADF.
    
    La salida de Data Factory se almacena en afgetstarted según la configuración de la plantilla de Resource Manager.
2. Haga clic en **adfgetstarted**.
3. Haga doble clic en **partitioneddata**. Verá una carpeta **año=2014** porque todos los registros de web tienen una fecha en el año 2014.

    ![Salida de la canalización de la actividad de Hive bajo demanda de HDInsight para Data Factory de Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Si profundiza en la lista, verá 3 carpetas de enero, febrero y marzo. Además, hay un registro para cada mes.

    ![Salida de la canalización de la actividad de Hive bajo demanda de HDInsight para Data Factory de Azure](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##Limpieza del tutorial

Con el servicio vinculado de HDInsight a petición, se crea un clúster de HDInsight cada vez que un segmento debe procesarse, a menos que haya un clúster existente activo (timeToLive), y el clúster se elimina cuando se realiza el procesamiento. Para cada clúster, Data Factory de Azure crea un almacenamiento de blobs de Azure usado como sistema de archivos predeterminado para el clúster. Aunque se elimine el clúster de HDInsight, el contenedor de almacenamiento de blobs predeterminado y la cuenta de almacenamiento asociada no se eliminan. Esto es así por diseño. A medida que se procesen más segmentos, verá numerosos contenedores en su Almacenamiento de blobs de Azure. Si no los necesita para solucionar problemas de trabajos, puede eliminarlos para reducir el costo de almacenamiento. El nombre de estos contenedores siguen un patrón: "adfnombreDeDataFactory-nombreDeServicioVinculado-marcaDeFechaYHora".

[Azure Resource Manager](../resource-group-overview.md) se usa para implementar, administrar y supervisar la solución como un grupo. Al eliminar un grupo de recursos, se eliminan todos los componentes dentro del grupo.

**Para eliminar el grupo de recursos**

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
2. Haga clic en **Grupos de recursos** en el panel izquierdo.
3. Haga doble clic en el nombre del grupo de recursos que creó en el script de PowerShell o de la CLI. Utilice el filtro si se muestran demasiados grupos de recursos. El grupo de recursos se abre en una nueva hoja.
4. En el icono **Recursos**, aparecerá la cuenta de almacenamiento predeterminada y Data Factory a menos que comparta el grupo de recursos con otros proyectos.
5. Haga clic en **Eliminar** en la parte superior de la hoja. De esta forma, se elimina también la cuenta de almacenamiento y los datos almacenados en la cuenta de almacenamiento.
6. Escriba el nombre del grupo de recursos y, a continuación, haga clic en **Eliminar**.

En caso de que no desee eliminar la cuenta de almacenamiento al eliminar el grupo de recursos, puede considerar el siguiente diseño de arquitectura mediante la separación de los datos empresariales de la cuenta de almacenamiento predeterminada. En este caso, tendrá un grupo de recursos para la cuenta de almacenamiento con los datos empresariales y el otro grupo de recursos para la cuenta de almacenamiento predeterminada y Data Factory. Cuando se elimina el segundo grupo de recursos, no afectará a la cuenta de almacenamiento de datos empresariales. Para ello:

- Agregue lo siguiente al grupo de recursos de nivel superior junto con el recurso Microsoft.DataFactory/datafactories en la plantilla de Resource Manager. Se creará una nueva cuenta de almacenamiento:

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

- Agregue un nuevo punto de servicio vinculado a la nueva cuenta de almacenamiento:

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
    
- Configure el servicio vinculado bajo demanda de HDInsight con un dependsOn y un additionalLinkedServiceNames adicionales:

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
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##Pasos siguientes
En este artículo, ha aprendido cómo utilizar la Data Factory de Azure para crear el clúster de HDInsight bajo demanda para procesar los trabajos de Hive. Para obtener más información:

- [Tutorial de Hadoop: Introducción al uso de Hadoop en HDInsight basado en Linux](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Creación de clústeres de Hadoop basados en Linux en HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [Documentación de HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Documentación de Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0914_2016-->