---
title: "Script de PowerShell: transformación de datos en la nube con Data Factory | Microsoft Docs"
description: "Este script de PowerShell transforma los datos en la nube mediante la ejecución del programa de Spark en un clúster de Spark de Azure HDInsight."
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/12/2017
ms.author: spelluru
ms.openlocfilehash: 195b7276346827479fbbe10dfaaaa9ed1d754967
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="powershell-script---transform-data-in-cloud-using-azure-data-factory"></a>Script de PowerShell: transformación de datos en la nube con Azure Data Factory

Este script de PowerShell de ejemplo crea una canalización que transforma los datos en la nube mediante la ejecución del programa de Spark en un clúster de Spark de Azure HDInsight. 

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Requisitos previos
* **Cuenta de Almacenamiento de Azure**. Cree un script de Python y un archivo de entrada y cárguelos en Azure Storage. La salida del programa Spark se almacena en esta cuenta de almacenamiento. El clúster de Spark a petición usa la misma cuenta de almacenamiento que el almacenamiento principal.  

### <a name="upload-python-script-to-your-blob-storage-account"></a>Carga del script de Python en la cuenta de Blob Storage
1. Cree un archivo de Python denominado **WordCount_Spark.py** con el siguiente contenido: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
2. Reemplace **&lt;storageaccountname&gt;** por el nombre de la cuenta de Azure Storage. A continuación, guarde el archivo. 
3. En Azure Blob Storage, cree un contenedor denominado **adftutorial** si no existe. 
4. Cree una carpeta llamada **spark**.
5. Cree una subcarpeta denominada **script** en la carpeta **spark**. 
6. Cargue el archivo **WordCount_Spark.py** a la subcarpeta **script**. 


### <a name="upload-the-input-file"></a>Carga del archivo de entrada
1. Cree un archivo denominado **minecraftstory.txt** con algo de texto. El programa Spark contará el número de palabras de este texto. 
2. Cree una subcarpeta denominada `inputfiles` en la carpeta `spark` del contenedor de blobs. 
3. Cargue `minecraftstory.txt` a la subcarpeta `inputfiles`. 

## <a name="sample-script"></a>Script de ejemplo
> [!IMPORTANT]
> Este script crea archivos JSON que definen entidades de Data Factory (servicio vinculado, conjunto de datos y canalización) en la carpeta c:\ del disco duro.

[!code-powershell[main](../../../powershell_scripts/data-factory/transform-data-using-spark/transform-data-using-spark.ps1 "Transform data using Spark")]

## <a name="clean-up-deployment"></a>Limpieza de la implementación

Después de ejecutar el script de ejemplo, puede usar el comando siguiente para quitar el grupo de recursos y todos los recursos asociados a él:

```powershell
Remove-AzureRmResourceGroup -ResourceGroupName $resourceGroupName
```
Para eliminar la factoría de datos del grupo de recursos, ejecute el siguiente comando: 

```powershell
Remove-AzureRmDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Explicación del script

Este script usa los siguientes comandos:

| Comando | Notas |
|---|---|
| [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Crea un grupo de recursos en el que se almacenan todos los recursos. |
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2) | Creación de una factoría de datos. |
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedservice) | Crea un servicio vinculado en la factoría de datos. Un servicio vinculado enlaza un almacén de datos o equipo a una factoría de datos. |
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactorv2ypipeline) | Crea una canalización en la factoría de datos. Una canalización contiene una o varias actividades que realizan una operación determinada. En esta canalización, una actividad de Spark transforma los datos mediante la ejecución de un programa en un clúster de Spark de Azure HDInsight. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2pipelinerun) | Crea una ejecución para la canalización. En otras palabras, ejecuta la canalización. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtiene información detallada sobre la ejecución de la actividad (actividad ejecutar) en la canalización. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de scripts de PowerShell para Azure Data Factory en el artículo [Ejemplos de Azure PowerShell para Azure Data Factory](../samples-powershell.md).