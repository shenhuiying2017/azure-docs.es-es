---
title: Administración de recursos compartidos de archivos de Azure mediante Azure Portal
description: Aprenda a usar Azure Portal para administrar Azure Files.
services: storage
documentationcenter: ''
author: wmgries
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/26/2018
ms.author: wgries
ms.openlocfilehash: 9b8a7988be2c3a2622dd45a264abcb674b7d169e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="managing-azure-file-shares-with-the-azure-portal"></a>Administración de recursos compartidos de archivos de Azure mediante Azure Portal 
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. Los recursos compartidos de archivos de Azure se pueden montar en Windows, Linux y macOS. En esta guía se describen los conceptos básicos sobre cómo trabajar con recursos compartidos de archivos de Azure mediante [Azure Portal](https://portal.azure.com/). Obtenga información sobre cómo:

> [!div class="checklist"]
> * Crear un grupo de recursos y una cuenta de almacenamiento
> * Creación de un recurso compartido de archivos de Azure 
> * Creación de directorios
> * Cargar un archivo 
> * Descarga de un archivo
> * Crear y usar una instantánea de recurso compartido

Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="create-a-file-share"></a>Creación de un recurso compartido de archivos
Para crear un recurso compartido de archivos:

1. Seleccione la cuenta de almacenamiento desde el panel.
2. En la página de la cuenta de almacenamiento, en la sección **Services** (Servicios), seleccione **Files** (Archivos).
    ![Captura de pantalla de la sección de servicios de la cuenta de almacenamiento; seleccione el servicio Files](media/storage-how-to-use-files-portal/create-file-share-1.png)

3. En el menú de la parte superior de la página **File service** (Servicio File), haga clic en **+ File Share** (+ Recurso compartido de archivos). Se abre la página **New file share** (Nuevo recurso compartido de archivos).
4. En **Name** (Nombre), escriba *myshare*.
5. Haga clic en **Aceptar** para crear el recurso compartido de archivos de Azure.

## <a name="work-with-the-contents-of-the-azure-file-share"></a>Uso del contenido de un recurso compartido de archivos de Azure
Ahora que ha creado un recurso compartido de archivos de Azure, puede montarlo con SMB en [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). Como alternativa, puede trabajar con el recurso compartido de archivos de Azure en Azure Portal. Todas las solicitudes efectuadas a través de Azure Portal se realizan con la API REST de File, lo que le permite crear, modificar y eliminar archivos y directorios en los clientes sin acceso a SMB.

### <a name="create-a-directory"></a>Creación de directorios
Para crear un nuevo directorio denominado *myDirectory* en la raíz del recurso compartido de archivos de Azure:

1. En la página **File Service** (Servicio File), seleccione el recurso compartido de archivos **myshare**. Se abre la página del recurso compartido de archivos.
2. En el menú que aparece en la parte superior de la página, seleccione **+ Add directory** (Agregar directorio) y su cuenta. Se abre la página **New directory** (Nuevo directorio).
3. Escriba *myDirectory* y, luego, haga clic en **OK** (Aceptar).

### <a name="upload-a-file"></a>Cargar un archivo 
Para demostrar la carga de un archivo, primero debe crear o seleccionar un archivo para cargar. Puede hacerlo por cualquier medio que considere oportuno. Una vez haya seleccionado el archivo que desea cargar:

1. Haga clic en el directorio **myDirectory**. Se abre el panel **myDirectory**.
2. En el menú de la parte superior, haga clic en **Upload** (Cargar). Se abre el panel **Upload files** (Cargar archivos).  
    ![Captura de pantalla del panel de carga de archivos](media/storage-how-to-use-files-portal/upload-file-1.png)

3. Haga clic en el icono de carpeta para abrir una ventana donde examinar los archivos locales. 
4. Seleccione un archivo y haga clic en **Open** (Abrir). 
5. En la página **Upload files** (Cargar archivos), compruebe el nombre de archivo y, a continuación, haga clic en **Upload** (Cargar).
6. Cuando termine, el archivo debe aparecer en la lista en la página **myDirectory**.

### <a name="download-a-file"></a>Descarga de un archivo
Puede descargar una copia del archivo cargado haciendo clic con el botón derecho en él. Después de hacer clic en el botón de descarga, la experiencia exacta dependerá del sistema operativo y el explorador que use.

## <a name="create-and-modify-share-snapshots"></a>Creación y modificación de instantáneas de recurso compartido
Una tarea útil adicional que puede hacer con un recurso compartido de archivos de Azure consiste en crear instantáneas de recurso compartido. Una instantánea conserva un punto en el tiempo para un recurso compartido de archivos de Azure. Las instantáneas de recurso compartido son similares a las tecnologías de sistemas operativos que es posible que conozca, como son:
- [Servicio de instantáneas de volumen (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de archivos Windows como NTFS y ReFS
- Instantáneas del [Administrador de volúmenes lógicos (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantáneas de [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Para crear una instantánea de recurso compartido, siga estos pasos:

1. Abra la cuenta de almacenamiento desde el panel > **Files** > **myshare** para abrir la página del recurso compartido de archivos. 
2. En la página del recurso compartido de archivos, haga clic en el botón **Instantánea** en la parte superior de la página y, a continuación, seleccione **Crear una instantánea**.  
    ![Captura de pantalla que ilustra cómo buscar el botón para crear instantáneas](media/storage-how-to-use-files-portal/create-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Enumeración y exploración de instantáneas de recurso compartido
Una vez creada la instantánea, puede hacer clic en **Instantánea** de nuevo y, a continuación, seleccionar **Ver instantáneas** para enumerar las instantáneas para el recurso compartido. El panel resultante mostrará las instantáneas para este recurso compartido. Haga clic en una instantánea de recurso compartido para examinarla.

### <a name="restore-from-a-share-snapshot"></a>Restauración desde una instantánea de recurso compartido
Para demostrar la restauración de un archivo desde una instantánea de recurso compartido, primero es necesario eliminar un archivo del recurso compartido de archivos de Azure activo. Vaya a la carpeta *myDirectory*, haga clic con el botón derecho en el archivo que cargó y, a continuación, haga clic en **Eliminar**. A continuación, para restaurar ese archivo desde la instantánea de recurso compartido:

1. Haga clic en **Instantáneas** en el menú superior y seleccione **Ver instantáneas**. 
2. Haga clic en la instantánea que creó anteriormente y el contenido se abrirá en una nueva página. 
3. Haga clic en **myDirectory** en la instantánea y se debería ver el archivo que ha eliminado. 
4. Haga clic con el botón derecho en el archivo eliminado y seleccione **Restaurar**.
5. Aparecerá una ventana emergente que le ofrece que elija entre restaurar el archivo como una copia o sobrescribir el archivo original. Como hemos eliminado el archivo original, podemos seleccionar **Sobrescribir el archivo original** para restaurar el archivo tal como estaba antes de ser eliminado. Haga clic en **Aceptar** para restaurar el archivo en el recurso compartido de archivos de Azure.  
    ![Captura de pantalla del cuadro de diálogo de restauración de archivos](media/storage-how-to-use-files-portal/restore-snapshot-1.png)

6. Una vez que se restaure el archivo, cierre la página de la instantánea y vuelva a **myshare** > **myDirectory**. El archivo debería estar en su lugar original.

### <a name="delete-a-share-snapshot"></a>Eliminación de una instantánea de recurso compartido
Para eliminar una instantánea de recurso compartido, [vaya a la lista de instantáneas de recurso compartido](#list-and-browse-a-share-snapshot). Haga clic en la casilla situada junto al nombre de la instantánea de recurso compartido y seleccione el botón **Eliminar**.

![Captura de pantalla de la eliminación de una instantánea de recurso compartido](media/storage-how-to-use-files-portal/delete-snapshot-1.png)

## <a name="clean-up-resources"></a>Limpieza de recursos
[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Pasos siguientes
- [Administración de recursos compartidos de archivos de Azure mediante Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Administración de recursos compartidos de archivos mediante la CLI de Azure](storage-how-to-use-files-cli.md)
- [Administración de recursos compartidos de archivos de Azure mediante el Explorador de Azure Storage](storage-how-to-use-files-storage-explorer.md)
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)