---
title: 'Script de PowerShell: copia de los datos de local a Azure mediante el uso de Azure Data Factory| Microsoft Docs'
description: El script de PowerShell copia datos de la base de datos SQL Server local en otra instancia de Azure Blob Storage.
services: data-factory
author: spelluru
manager: jhubbard
editor: 
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2017
ms.author: spelluru
ms.openlocfilehash: 7f062a58482ad72e3dd3844431205502b4c44786
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/01/2017
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Use PowerShell para crear una canalización de factoría de datos para copiar datos de local a Azure

Este script de PowerShell de ejemplo crea una canalización en Azure Data Factory que copia los datos de una base de datos SQL Server local a una instancia de Azure Blob Storage.

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh.md)]

## <a name="prerequisites"></a>Requisitos previos

- **SQL Server**. Use una base de datos de SQL Server local como almacén de datos de **origen** en este ejemplo.
- **Cuenta de Almacenamiento de Azure**. Azure Blob Storage se usará como un almacén de datos de **destino o receptor** en este ejemplo. Si no tiene una cuenta de almacenamiento de Azure, consulte la sección [Crear una cuenta de almacenamiento](../../storage/common/storage-create-storage-account.md#create-a-storage-account) para ver los pasos para su creación.
- **Integration Runtime autohospedado**. Descargue el archivo MSI desde el [centro de descarga](https://www.microsoft.com/download/details.aspx?id=39717) y ejecútelo para instalar un Integration Runtime autohospedado en la máquina.  

### <a name="create-sample-database-in-sql-server"></a>Creación de una base de datos de ejemplo en SQL Server
1. En la base de datos de SQL Server local, cree una tabla con el nombre **emp** usando el siguiente script SQL: 

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Inserte algún dato de ejemplo en la tabla:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Script de ejemplo

> [!IMPORTANT]
> Este script crea archivos JSON que definen entidades de Data Factory (servicio vinculado, conjunto de datos y canalización) en la carpeta c:\ del disco duro.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


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
| [Set-AzureRmDataFactoryV2](/powershell/module/azurerm.datafactoryv2/set-azurermdatafactoryv2) | Creación de una factoría de datos. |
| [New-AzureRmDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/azurerm.datafactoryv2/new-azurermdatafactoryv2linkedserviceencryptedcredential) | Cifra las credenciales en un servicio vinculado y genera una nueva definición de servicio vinculado con la credencial cifrada. 
| [Set-AzureRmDataFactoryV2LinkedService](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2linkedservice) | Crea un servicio vinculado en la factoría de datos. Un servicio vinculado enlaza un almacén de datos o proceso a una factoría de datos. |
| [Set-AzureRmDataFactoryV2Dataset](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactoryv2dataset) | Crea un conjunto de datos en la factoría de datos. Un conjunto de datos representa la entrada/salida para una actividad en una canalización. | 
| [Set-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Set-azurermdatafactorv2ypipeline) | Crea una canalización en la factoría de datos. Una canalización contiene una o varias actividades que realizan una operación determinada. En esta canalización, una actividad de copia realiza una copia de los datos de una ubicación en otra ubicación en una instancia de Azure Blob Storage. |
| [Invoke-AzureRmDataFactoryV2Pipeline](/powershell/module/azurerm.datafactoryv2/Invoke-azurermdatafactoryv2pipelinerun) | Crea una ejecución para la canalización. En otras palabras, ejecuta la canalización. |
| [Get-AzureRmDataFactoryV2ActivityRun](/powershell/module/azurerm.datafactoryv2/get-azurermdatafactoryv2activityrun) | Obtiene información detallada sobre la ejecución de la actividad (actividad ejecutar) en la canalización. 
| [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) | Elimina un grupo de recursos, incluidos todos los recursos anidados. |
|||

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure PowerShell, consulte la [documentación de Azure PowerShell](https://docs.microsoft.com/powershell/).

Encontrará más ejemplos de scripts de PowerShell de Azure Data Factory en los [ejemplos de PowerShell de Azure Data Factory](../samples-powershell.md).