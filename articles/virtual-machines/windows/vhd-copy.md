---
title: "Creación de una copia de una máquina virtual especializada en Azure | Microsoft Docs"
description: "Sepa cómo crear una copia de una máquina virtual Windows especializada que se ejecute en Azure con el modelo de implementación de Resource Manager."
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: ce7e6cd3-6a4a-4fab-bf66-52f699b1398a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/22/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: aaf97d26c982c1592230096588e0b0c3ee516a73
ms.openlocfilehash: 1b2b013fb29c66817aab2ea5e63917b59726b4e8
ms.lasthandoff: 04/27/2017


---
# <a name="create-a-copy-of-a-specialized-windows-vm-running-in-azure"></a>Creación de una copia de una máquina virtual Windows especializadas que se ejecute en Azure
En este artículo se muestra cómo utilizar la herramienta AZCopy para crear una copia del VHD a partir de una máquina virtual Windows especializada que se ejecute en Azure. Después, puede usar la copia del VHD para crear una nueva máquina virtual. 

* Si desea copiar una máquina virtual generalizada, consulte [Cómo crear una imagen de máquina virtual desde una máquina virtual de Azure existente](capture-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Si quiere cargar un VHD de una máquina virtual local, como el que creó con Hyper-V, consulte [Carga de una imagen de máquina virtual de Windows en Azure para implementaciones de Resource Manager](upload-image.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

## <a name="before-you-begin"></a>Antes de empezar
Asegúrese de todo esto:

* Tener información sobre las **cuentas de almacenamiento de origen y de destino**. Para la máquina virtual de origen, necesitará los nombres del contenedor y la cuenta de almacenamiento. Normalmente, el nombre del contenedor será **vhds**. También debe tener una cuenta de almacenamiento de destino. Si todavía no tiene una, puede crearla con el portal (**Más servicios** > Cuentas de almacenamiento > Agregar) o el cmdlet [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/new-azurermstorageaccount). 
* Tener instalado Azure [Microsoft Azure PowerShell 1.0](/powershell/azure/overview) o versiones posteriores.
* Tener descargada e instalada la [herramienta AzCopy](../../storage/storage-use-azcopy.md). 

## <a name="deallocate-the-vm"></a>Desasignación de la máquina virtual
Desasigne la máquina virtual para liberar espacio en el VHD que se va a copiar. 

* **Portal**: haga clic en **Máquinas virtuales** > **myVM** > Detener
* **PowerShell**: `Stop-AzureRmVM -ResourceGroupName myResourceGroup -Name myVM` desasigne la máquina virtual denominada "**myVM**" del grupo de recursos **myResourceGroup**.

El **estado** de la máquina virtual en Azure Portal cambia de **Detenido** a **Detenido (desasignado)**.

## <a name="get-the-storage-account-urls"></a>Obtención de las URL de las cuentas de almacenamiento
Necesita las URL de las cuentas de almacenamiento de origen y de destino. Las direcciones URL se verán así: `https://<storageaccount>.blob.core.windows.net/<containerName>/`. Si ya conoce el nombre de la cuenta de almacenamiento y el contenedor, puede reemplazar simplemente la información entre corchetes para crear la dirección URL. 

Puede usar Azure Portal o Azure PowerShell para obtener la URL:

* **Portal**: haga clic en **Más servicios** > **Cuentas de almacenamiento** > <storage account> **Blobs**; el archivo VHD de origen estará probablemente en el contenedor **vhds**. Haga clic en la opción **Propiedades** del contenedor y copie el texto etiquetado como **Dirección URL**. Necesitará las direcciones URL de los contenedores de origen y de destino. 
* **PowerShell**: `Get-AzureRmVM -ResourceGroupName "myResourceGroup" -Name "myVM"` obtiene la información de la máquina virtual denominada "**myVM**" del grupo de recursos **myResourceGroup**. En los resultados, busque la sección **Perfil de almacenamiento** del **URI del VHD**. La primera parte del URI es la dirección URL al contenedor, y la última, el nombre del VHD del sistema operativo de la máquina virtual.

## <a name="get-the-storage-access-keys"></a>Obtención de las claves de acceso de almacenamiento
Busque las claves de acceso de las cuentas de almacenamiento de origen y de destino. Para más información acerca de las claves de acceso, consulte [Acerca de las cuentas de almacenamiento de Azure](../../storage/storage-create-storage-account.md).

* **Portal**: haga clic en **Más servicios** > **Cuentas de almacenamiento** > <storage account> **Toda la configuración** > **Claves de acceso**. Copie la clave etiquetada como **clave1**.
* **PowerShell**: `Get-AzureRmStorageAccountKey -Name mystorageaccount -ResourceGroupName myResourceGroup` obtiene la clave de almacenamiento de la cuenta de almacenamiento **mystorageaccount** del grupo de recursos **myResourceGroup**. Copie la clave etiquetada como **clave1**.

## <a name="copy-the-vhd"></a>Copia del VHD
Puede copiar archivos entre cuentas de almacenamiento con AzCopy. Para el contenedor de destino, si el contenedor especificado no existe, se creará automáticamente. 

Para usar AzCopy, abra una ventana de comandos en el equipo local y navegue hasta la carpeta donde está instalada dicha herramienta. Será similar a *C:\Archivos de programa (x86)\Microsoft SDKs\Azure\AzCopy*. 

Para copiar todos los archivos dentro de un contenedor, use el modificador **/S**. Esto puede utilizarse para copiar el VHD del sistema operativo y todos los discos de datos si están en el mismo contenedor. En este ejemplo se muestra cómo copiar todos los archivos del contenedor **mysourcecontainer** de la cuenta de almacenamiento **mysourcestorageaccount** en el contenedor **mydestinationcontainer** de la cuenta de almacenamiento **mydestinationstorageaccount**. Reemplace los nombres de las cuentas de almacenamiento y los contenedores por los suyos. Reemplace `<sourceStorageAccountKey1>` y `<destinationStorageAccountKey1>` por sus propias claves.

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
    /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
    /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> /S
```

Si solo quiere copiar un VHD específico en un contenedor con varios archivos, también puede especificar el nombre de archivo con el modificador /Pattern. En este ejemplo, solo se copiará el archivo denominado "**myFileName.vhd**".

```
AzCopy /Source:https://mysourcestorageaccount.blob.core.windows.net/mysourcecontainer `
  /Dest:https://mydestinationatorageaccount.blob.core.windows.net/mydestinationcontainer `
  /SourceKey:<sourceStorageAccountKey1> /DestKey:<destinationStorageAccountKey1> `
  /Pattern:myFileName.vhd
```


Cuando haya finalizado, obtendrá un mensaje parecido a este:

```
Finished 2 of total 2 file(s).
[2016/10/07 17:37:41] Transfer summary:
-----------------
Total files transferred: 2
Transfer successfully:   2
Transfer skipped:        0
Transfer failed:         0
Elapsed time:            00.00:13:07
```

## <a name="troubleshooting"></a>Solución de problemas
* Cuando use AZCopy, si ve el error El servidor no pudo autenticar la solicitud, asegúrese de que el valor del encabezado de autenticación tenga el formato correcto, incluida la firma, y, si usa la clave 2 o la clave de almacenamiento secundaria, pruebe a utilizar la clave de almacenamiento principal o 1.

## <a name="next-steps"></a>Pasos siguientes
* Puede crear una nueva máquina virtual [asociando la copia del VHD a una máquina virtual como un disco del SO](create-vm-specialized.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).


