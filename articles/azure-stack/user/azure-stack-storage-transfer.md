---
title: Herramientas de Azure Stack Storage
description: "Obtenga información acerca de las herramientas de transferencia de datos de Azure Stack Storage"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 9799498a11449a9ed496d0fdb40312603eda064e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="tools-for-azure-stack-storage"></a>Herramientas de Azure Stack Storage

*Se aplica a: Sistemas integrados de Azure Stack y Azure Stack Development Kit*

Microsoft Azure Stack proporciona un conjunto de servicios de almacenamiento para discos, blobs, tablas, colas y funcionalidad de administración de cuentas. Puede usar un conjunto de herramientas de Azure Storage si desea administrar o mover datos a Azure Stack Storage, o desde él. En este artículo se proporciona una descripción general de las herramientas disponibles.

La herramienta que mejor se adapte a usted depende de sus requisitos:
* [AzCopy](#azcopy)

    Una utilidad de línea de comandos específica para el almacenamiento que se puede descargar para copiar datos de un objeto a otro dentro de la cuenta de almacenamiento o entre cuentas de almacenamiento.

* [Azure PowerShell](#azure-powershell)

    Un shell de línea de comandos basado en tareas y un lenguaje de scripts diseñado especialmente para la administración del sistema.

* [CLI de Azure](#azure-cli)

    Una herramienta multiplataforma de código abierto que proporciona un conjunto de comandos para trabajar con las plataformas Azure y Azure Stack.

* [Explorador de Microsoft Azure Storage (versión preliminar)](#microsoft-azure-storage-explorer)

    Una aplicación independiente fácil de usar con una interfaz de usuario.

Dadas las diferencias en los servicios de Storage entre Azure y Azure Stack, puede haber requisitos específicos para cada herramienta que se describe en las secciones siguientes. Para ver una comparación entre Azure Stack Storage y Azure Storage, consulte [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md) (Azure Stack Storage: diferencias y consideraciones).


## <a name="azcopy"></a>AzCopy
AzCopy es una utilidad de línea de comandos diseñada para copiar datos hacia y desde los servicios Microsoft Azure Blob Storage y Table Storage mediante sencillos comandos con un rendimiento óptimo. También puede copiar datos de un objeto a otro dentro de la cuenta de almacenamiento o entre cuentas de almacenamiento. Hay dos versiones de AzCopy: AzCopy en Windows y AzCopy en Linux. Azure Stack solo admite la versión de Windows. 
 
### <a name="download-and-install-azcopy"></a>Descarga e instalación de AzCopy 
[Descargue](https://aka.ms/azcopyforazurestack) la versión compatible con Windows de AzCopy para Azure Stack. AzCopy se puede instalar y usar en Azure Stack del mismo modo que Azure. Para más información, consulte [Transferencia de datos con AzCopy en Windows](../../storage/common/storage-use-azcopy.md). 

### <a name="azcopy-command-examples-for-data-transfer"></a>Ejemplos del comando AzCopy para la transferencia de datos
Los ejemplos siguientes muestran varios escenarios habituales para copiar datos a los blobs de Azure Stack y desde ellos. Para más información, consulte [Transferencia de datos con AzCopy en Windows](../../storage/storage-use-azcopy.md). 
#### <a name="download-all-blobs-to-local-disk"></a>Descarga de todos los blobs en el disco local
```azcopy
AzCopy.exe /source:https://myaccount.blob.local.azurestack.external/mycontainer /dest:C:\myfolder /sourcekey:<key> /S
```
#### <a name="upload-single-file-to-virtual-directory"></a>Carga de un solo archivo en el directorio virtual 
```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.local.azurestack.external/mycontainer/vd /DestKey:key /Pattern:abc.txt
```
#### <a name="move-data-between-azure-and-azure-stack-storage"></a>Movimiento de datos entre Azure y Azure Stack Storage 
No se admite la transferencia de datos asincrónica entre Azure Storage y Azure Stack. La transferencia se debe especificar con la opción `/SyncCopy`. 
```azcopy 
Azcopy /Source:https://myaccount.blob.local.azurestack.external/mycontainer /Dest:https://myaccount2.blob.core.windows.net/mycontainer2 /SourceKey:AzSKey /DestKey:Azurekey /S /SyncCopy
```

### <a name="azcopy-known-issues"></a>Problemas conocidos de Azcopy
* Las operaciones de AzCopy en File Storage no están disponible porque File Storage aún no está disponible en Azure Stack.
* No se admite la transferencia de datos asincrónica entre Azure Storage y Azure Stack. Puede especificar la transferencia con la opción `/SyncCopy` para copiar los datos.
* No se admite la versión Linux de Azcopy para Azure Stack Storage. 

## <a name="azure-powershell"></a>Azure PowerShell
Azure PowerShell es un módulo que proporciona cmdlets para administrar servicios tanto en Azure como en Azure Stack. Se trata de un shell de línea de comandos basado en tareas y un lenguaje de scripts especialmente diseñados para administración del sistema.

### <a name="install-and-configure-powershell-for-azure-stack"></a>Instalación y configuración de PowerShell para Azure Stack
Los módulos de Azure PowerShell compatibles con Azure Stack se requieren para trabajar con Azure Stack. Para más información, consulte [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md) y [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md).

### <a name="powershell-sample-script-for-azure-stack"></a>Script de ejemplo de PowerShell para Azure Stack 
En este ejemplo se supone que ha [instalado PowerShell para Azure Stack](azure-stack-powershell-install.md) correctamente. Este script le ayudará a completar la configuración y a pedir las credenciales de su inquilino de Azure Stack para agregar su cuenta al entorno local de PowerShell. A continuación, el script establecerá la suscripción predeterminada de Azure, creará un una nueva cuenta de almacenamiento en Azure, creará un nuevo contenedor en dicha cuenta de almacenamiento y cargará un archivo de imagen existente (blob) en dicho contenedor. Una vez el script ha enumerado todos los blobs en ese contenedor, creará un nuevo directorio de destino en el equipo local y descargará el archivo de imagen.

1. Instale los [módulos de Azure PowerShell compatibles con Azure Stack](azure-stack-powershell-install.md).  
2. Descargue las [herramientas necesarias para trabajar con Azure Stack](azure-stack-powershell-download.md).  
3. Abra **Windows PowerShell ISE** y haga clic en **Ejecutar como administrador**, **Archivo** > **Nuevo** para crear un archivo de script nuevo.
4. Copie el siguiente script y péguelo en el archivo de script nuevo.
5. Actualice las variables del script en función de su configuración. 
6. Nota: Este script tiene que ejecutarse bajo la raíz del **AzureStack_Tools** descargado. 

```PowerShell 
# begin

$ARMEvnName = "AzureStackUser" # set AzureStackUser as your Azure Stack environemnt name
$ARMEndPoint = "https://management.local.azurestack.external" 
$GraphAudiance = "https://graph.windows.net/" 
$AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com" 

$SubscriptionName = "basic" # Update with the name of your subscription.
$ResourceGroupName = "myTestRG" # Give a name to your new resource group.
$StorageAccountName = "azsblobcontainer" # Give a name to your new storage account. It must be lowercase!
$Location = "Local" # Choose "Local" as an example.
$ContainerName = "photo" # Give a name to your new container.
$ImageToUpload = "C:\temp\Hello.jpg" # Prepare an image file and a source directory in your local computer.
$DestinationFolder = "C:\temp\downlaod" # A destination directory in your local computer.

# Import the Connect PowerShell module"
Set-ExecutionPolicy RemoteSigned -Scope CurrentUser -Force
Import-Module .\Connect\AzureStack.Connect.psm1

# Configure the PowerShell environment
# Register an AzureRM environment that targets your Azure Stack instance
Add-AzureRmEnvironment -Name $ARMEvnName -ARMEndpoint $ARMEndPoint 

# Set the GraphEndpointResourceId value
Set-AzureRmEnvironment -Name $ARMEvnName -GraphEndpoint $GraphAudiance

# Login
$TenantID = Get-AzsDirectoryTenantId -AADTenantName $AADTenantName -EnvironmentName $ARMEvnName
Login-AzureRmAccount -EnvironmentName $ARMEvnName -TenantId $TenantID 

# Set a default Azure subscription.
Select-AzureRmSubscription -SubscriptionName $SubscriptionName

# Create a new Resource Group 
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a new storage account.
New-AzureRmStorageAccount -ResourceGroupName $ResourceGroupName -Name $StorageAccountName -Location $Location -Type Standard_LRS

# Set a default storage account.
Set-AzureRmCurrentStorageAccount -StorageAccountName $StorageAccountName -ResourceGroupName $ResourceGroupName 

# Create a new container.
New-AzureStorageContainer -Name $ContainerName -Permission Off

# Upload a blob into a container.
Set-AzureStorageBlobContent -Container $ContainerName -File $ImageToUpload

# List all blobs in a container.
Get-AzureStorageBlob -Container $ContainerName

# Download blobs from the container:
# Get a reference to a list of all blobs in a container.
$blobs = Get-AzureStorageBlob -Container $ContainerName

# Create the destination directory.
New-Item -Path $DestinationFolder -ItemType Directory -Force  

# Download blobs into the local destination directory.
$blobs | Get-AzureStorageBlobContent –Destination $DestinationFolder

# end
```

### <a name="powershell-known-issues"></a>Problemas conocidos de PowerShell 
La versión actual del módulo Azure PowerShell compatible con Azure Stack es 1.2.10. Tiene ciertas diferencias con la versión más reciente de Azure PowerShell. La principal diferencia afecta al funcionamiento de los servicios de almacenamiento:

* El formato del valor de retorno de `Get-AzureRmStorageAccountKey` en la versión 1.2.10 tiene dos propiedades: `Key1` y `Key2`, mientras que la versión actual de Azure devuelve una matriz que contiene todas las claves de la cuenta.
   ```
   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.4, and later versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Value[0]

   # This command gets a specific key for a Storage account, 
   # and works for Azure PowerShell version 1.3.2, and previous versions.
   (Get-AzureRmStorageAccountKey -ResourceGroupName "RG01" `
   -AccountName "MyStorageAccount").Key1

   ```
   Para más información, consulte [Get-AzureRmStorageAccountKey](https://docs.microsoft.com/powershell/module/azurerm.storage/Get-AzureRmStorageAccountKey?view=azurermps-4.1.0).

## <a name="azure-cli"></a>CLI de Azure
La CLI de Azure es la forma de usar la línea de comandos de Azure para administrar los recursos de Azure. Puede instalarla en macOS, Linux y Windows, y ejecutarla desde la línea de comandos. 

La CLI de Azure está optimizada para administrar recursos de Azure desde la línea de comandos y para compilar scripts de automatización que funcionen con Azure Resource Manager. Proporciona muchas de las funciones que se encuentran en Azure Stack Portal, lo que incluye el acceso a datos enriquecidos.

Azure Stack requiere la versión 2.0 de la CLI de Azure. Para más información acerca de cómo instalar y Azure PowerShell con Azure Stack, consulte [Install and configure Azure Stack CLI](azure-stack-connect-cli.md) (Instalación y configuración de la CLI de Azure Stack). Para más información acerca de cómo utilizar la CLI de Azure 2.0 para realizar varias tareas relativas al trabajo con recursos de su cuenta de Azure Stack Storage, consulte [Uso de la CLI de Azure 2.0 con Azure Storage](../../storage/storage-azure-cli.md)

### <a name="azure-cli-sample-script-for-azure-stack"></a>Script de ejemplo de la CLI de Azure para Azure Stack 
Cuando haya completado la instalación y configuración de la CLI, puede probar los siguientes pasos para trabajar con un pequeño script de ejemplo de shell pequeño para interactuar con los recursos de Azure Stack Storage. En primer lugar, el script crea un contenedor nuevo en la cuenta de almacenamiento, después, carga un archivo existente (como un blob) en dicho contenedor, enumera todos los blobs del contenedor y, por último, descarga el archivo en el destino del equipo local que especifique. Antes de ejecutar este script, asegúrese de que se conecta a inicia sesión correctamente con el servicio Azure Stack de destino. 
1. Abra el editor de texto que prefiera; copie y pegue el script anterior en el editor.
2. Actualice las variables del script para incluir sus opciones de configuración. 
3. Después de actualizar las variables necesarias, guarde el script y salga del editor. En los siguientes pasos se da por hecho que ha llamado al script my_storage_sample.sh.
4. Marque el script como archivo ejecutable si es necesario: `chmod +x my_storage_sample.sh`
5. Ejecute el script. Por ejemplo, en Bash: `./my_storage_sample.sh`

```bash
#!/bin/bash
# A simple Azure Stack Storage example script

export AZURESTACK_RESOURCE_GROUP=<resource_group_name>
export AZURESTACK_RG_LOCATION="local"
export AZURESTACK_STORAGE_ACCOUNT_NAME=<storage_account_name>
export AZURESTACK_STORAGE_CONTAINER_NAME=<container_name>
export AZURESTACK_STORAGE_BLOB_NAME=<blob_name>
export FILE_TO_UPLOAD=<file_to_upload>
export DESTINATION_FILE=<destination_file>

echo "Creating the resource group..."
az group create --name $AZURESTACK_RESOURCE_GROUP --location $AZURESTACK_RG_LOCATION

echo "Creating the storage account..."
az storage account create --name $AZURESTACK_STORAGE_ACCOUNT_NAME --resource-group $AZURESTACK_RESOURCE_GROUP --account-type Standard_LRS

echo "Creating the blob container..."
az storage container create --name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Uploading the file..."
az storage blob upload --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --file $FILE_TO_UPLOAD --name $AZURESTACK_STORAGE_BLOB_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME

echo "Listing the blobs..."
az storage blob list --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --output table

echo "Downloading the file..."
az storage blob download --container-name $AZURESTACK_STORAGE_CONTAINER_NAME --account-name $AZURESTACK_STORAGE_ACCOUNT_NAME --name $AZURESTACK_STORAGE_BLOB_NAME --file $DESTINATION_FILE --output table

echo "Done"
```

## <a name="microsoft-azure-storage-explorer"></a>Explorador de almacenamiento de Microsoft Azure

El Explorador de Microsoft Azure Storage es una aplicación independiente de Microsoft que permite trabajar fácilmente con los datos de Azure Storage y Azure Stack Storage en Windows, macOS y Linux. Si desea una manera fácil de administrar los datos de Azure Stack Storage, considere la posibilidad de usar el Explorador de Microsoft Azure Storage.

Para más información acerca de cómo configurar el Explorador de Azure Storage para que funcione con Azure Stack, consulte [Connect Storage Explorer to an Azure Stack subscription](azure-stack-storage-connect-se.md) (Conexión del Explorador de Storage a una suscripción de Azure Stack).

Para más información acerca del Explorador de Microsoft Azure Storage, consulte [Introducción al Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)

## <a name="next-steps"></a>Pasos siguientes
* [Conexión del Explorador de Storage a una suscripción de Azure Stack](azure-stack-storage-connect-se.md)
* [Introducción al Explorador de Storage (versión preliminar)](../../vs-azure-tools-storage-manage-with-storage-explorer.md)
* [Azure Stack Storage: Differences and considerations](azure-stack-acs-differences.md) (Azure Stack Storage: diferencias y consideraciones)
* [Introducción a Almacenamiento de Microsoft Azure](../../storage/common/storage-introduction.md)

