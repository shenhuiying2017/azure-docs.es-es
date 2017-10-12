---
title: "Uso de PowerShell para la administración de Azure Files | Microsoft Docs"
description: Aprenda a usar PowerShell para administrar Azure Files.
services: storage
documentationcenter: 
author: RenaShahMSFT
manager: aungoo
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/19/2017
ms.author: renash
ms.openlocfilehash: f919e1880f709b416867a29de14f1dcc63a165fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-use-powershell-to-manage-azure-files"></a>Uso de PowerShell para administrar Azure Files
Puede usar Azure PowerShell para crear y administrar recursos compartidos de archivos.

## <a name="install-the-powershell-cmdlets-for-azure-storage"></a>Instalación de cmdlets de PowerShell para Almacenamiento de Azure
Para prepararse para usar PowerShell, descargue e instale los cmdlets de Azure PowerShell. Consulte [Instalación y configuración de Azure PowerShell](/powershell/azureps-cmdlets-docs) para obtener instrucciones sobre el punto de instalación y la instalación.

> [!NOTE]
> Es recomendable descargar e instalar el módulo más reciente de Azure PowerShell o actualizar a dicho módulo.
> 
> 

Abra una ventana de Azure PowerShell haciendo clic en **Inicio** y escribiendo **Windows PowerShell**. La ventana de PowerShell cargará el módulo de Azure Powershell.

## <a name="create-a-context-for-your-storage-account-and-key"></a>Creación de un contexto para la cuenta y clave de almacenamiento
Creación del contexto de la cuenta de almacenamiento. El contexto encapsula el nombre y la clave de cuenta de almacenamiento. Para obtener instrucciones acerca de cómo copiar la clave de una cuenta desde [Azure Portal](https://portal.azure.com), consulte [Visualización y copia de las claves de acceso de almacenamiento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#view-and-copy-storage-access-keys).

Sustituya `storage-account-name` y `storage-account-key` por el nombre y clave de su cuenta de almacenamiento  en el siguiente ejemplo.

```powershell
# create a context for account and key
$ctx=New-AzureStorageContext storage-account-name storage-account-key
```

## <a name="create-a-new-file-share"></a>Creación de un nuevo recurso compartido de archivos
Cree el nuevo recurso compartido, denominado `logs`.

```powershell
# create a new share
$s = New-AzureStorageShare logs -Context $ctx
```

Ahora tiene un recurso compartido de archivos en Almacenamiento de archivos. A continuación, agregaremos un directorio y un archivo.

> [!IMPORTANT]
> El nombre del recurso compartido de archivos debe estar en minúsculas. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://msdn.microsoft.com/library/azure/dn167011.aspx).
> 
> 

## <a name="create-a-directory-in-the-file-share"></a>Creación de un directorio en el recurso compartido de archivos
Creación de un directorio en el recurso compartido. En el siguiente ejemplo, el directorio se llama `CustomLogs`.

```powershell
# create a directory in the share
New-AzureStorageDirectory -Share $s -Path CustomLogs
```

## <a name="upload-a-local-file-to-the-directory"></a>Carga de un archivo local al directorio
Ahora cargará un archivo local al directorio. El siguiente ejemplo carga un archivo desde `C:\temp\Log1.txt`. Edite la ruta de acceso al archivo de forma que apunte a un archivo válido situado en su equipo local.

```powershell
# upload a local file to the new directory
Set-AzureStorageFileContent -Share $s -Source C:\temp\Log1.txt -Path CustomLogs
```

## <a name="list-the-files-in-the-directory"></a>Visualización de los archivos del directorio en una lista
Para ver el archivo en el directorio, puede mostrar todos los archivos de este en una lista. Este comando devuelve los archivos y subdirectorios (si hay alguno) del directorio CustomLogs.

```powershell
# list files in the new directory
Get-AzureStorageFile -Share $s -Path CustomLogs | Get-AzureStorageFile
```

Get-AzureStorageFile devuelve una lista de archivos y directorios para cualquier objeto de directorio que se pase. "Get-AzureStorageFile -Share $s" devuelve una lista de archivos y directorios en el directorio raíz. Para obtener una lista de los archivos de un subdirectorio, tiene que pasar el subdirectorio a Get-AzureStorageFile. Esto es lo que hace: la primera parte del comando hasta la barra vertical devuelve una instancia de directorio del subdirectorio CustomLogs. A continuación, esto se pasa a Get-AzureStorageFile, que devuelve los archivos y directorios en CustomLogs.

## <a name="copy-files"></a>Copiar archivos
A partir de la versión 0.9.7 de Azure PowerShell, puede copiar un archivo en otro, un archivo en un blob o un blob en un archivo. A continuación se muestra cómo realizar estas operaciones de copia mediante cmdlets de PowerShell.

```powershell
# copy a file to the new directory
Start-AzureStorageFileCopy -SrcShareName srcshare -SrcFilePath srcdir/hello.txt -DestShareName destshare -DestFilePath destdir/hellocopy.txt -Context $srcCtx -DestContext $destCtx

# copy a blob to a file directory
Start-AzureStorageFileCopy -SrcContainerName srcctn -SrcBlobName hello2.txt -DestShareName hello -DestFilePath hellodir/hello2copy.txt -DestContext $ctx -Context $ctx
```
## <a name="next-steps"></a>Pasos siguientes
Consulte los vínculos siguientes para más información acerca Azure Files.

* [Preguntas más frecuentes](../storage-files-faq.md)
* [Solución de problemas en Windows](storage-troubleshoot-windows-file-connection-problems.md)      
* [Solución de problemas en Linux](storage-troubleshoot-linux-file-connection-problems.md)    