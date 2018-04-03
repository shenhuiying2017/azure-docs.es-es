---
title: Administración de recursos compartidos de archivos de Azure mediante el Explorador de Azure Storage
description: Aprenda a usar el Explorador de Azure Storage para administrar Azure Files.
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
ms.date: 02/27/2018
ms.author: wgries
ms.openlocfilehash: 0c16d3b0ef0b178f99eaafecd74eabb00cca5af9
ms.sourcegitcommit: c3d53d8901622f93efcd13a31863161019325216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/29/2018
---
# <a name="managing-azure-file-shares-with-azure-storage-explorer"></a>Administración de recursos compartidos de archivos de Azure mediante el Explorador de Azure Storage 
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. En esta guía se describen los conceptos básicos sobre cómo trabajar con recursos compartidos de archivos de Azure mediante el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). El Explorador de Azure Storage es una conocida herramienta de cliente disponible para Windows, macOS y Linux, que permite administrar recursos compartidos de archivos de Azure y otros recursos de almacenamiento.

Esta guía de inicio rápido requiere que esté instalado el Explorador de Microsoft Azure Storage. Si necesita instalarlo, visite el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) para descargarlo.

En este artículo, aprenderá a:

> [!div class="checklist"]
> * Crear un grupo de recursos y una cuenta de almacenamiento
> * Creación de un recurso compartido de archivos de Azure 
> * Creación de directorios
> * Cargar un archivo
> * Descarga de un archivo
> * Crear y usar una instantánea de recurso compartido

Si no tiene una suscripción a Azure, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="create-a-storage-account"></a>Crear una cuenta de almacenamiento
El Explorador de Azure Storage no ofrece la posibilidad de crear nuevos recursos, así que en esta demostración, creará la cuenta de almacenamiento con [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connecting-azure-storage-explorer-to-azure-resources"></a>Conexión del Explorador de Azure Storage a recursos de Azure
La primera vez, se muestra la ventana **Explorador de Microsoft Azure Storage: Conectar**. El Explorador de Azure Storage proporciona varias maneras de conectar con las cuentas de almacenamiento: 

- **Inicio de sesión mediante su cuenta de Azure**: le permite iniciar sesión con sus credenciales de usuario de la organización o con su cuenta de Microsoft. 
- **Conexión a una cuenta de almacenamiento específica con una cadena de conexión o un token de SAS**: una cadena de conexión es una cadena especial que contiene el nombre y la clave o el token de SAS de una cuenta de almacenamiento, que permite al Explorador de Azure Storage acceder directamente a la cuenta de almacenamiento (en lugar de ver simplemente todas las cuentas de almacenamiento que contiene una cuenta de Azure). Para más información sobre las cadenas de conexión, consulte [Configuración de las cadenas de conexión de Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Conexión a una cuenta de almacenamiento específica con el nombre y la clave de una cuenta de almacenamiento**: use el nombre y la clave de su cuenta de almacenamiento para conectarse a Azure Storage.

Para los fines de esta guía de inicio rápido, inicie sesión con su cuenta de Azure. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y haga clic en **Iniciar sesión**. Siga las indicaciones de la pantalla para registrarse en su cuenta de Azure.

![Explorador de Microsoft Azure Storage: ventana Conectar](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Creación de un recurso compartido de archivos
Para crear su primer recurso compartido de archivos de Azure en la cuenta de almacenamiento *storageacct<random number>*, siga estos pasos:

1. Expanda la cuenta de almacenamiento que creó.
2. Haga clic con el botón derecho en **Recursos compartidos de archivos** y seleccione **Create File Share** (Crear recurso compartido de archivos).  
    ![Captura de pantalla de la carpeta de recursos compartidos de archivos y el menú contextual en contexto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Escriba *myshare* para el recurso compartido de archivos y presione **Entrar**.

> [!Important]  
> Los nombres de recursos compartidos deben estar formados por letras minúsculas, números y guiones sencillos, pero no pueden empezar con un guion. Para obtener detalles completos sobre cómo asignar un nombre a recursos compartidos y archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Después de crear el recurso compartido de archivos, se abrirá una pestaña para él en el panel derecho. 

## <a name="manipulating-the-contents-of-the-azure-file-share"></a>Manipulación del contenido del recurso compartido de archivos de Azure
Ahora que ha creado un recurso compartido de archivos de Azure, puede montarlo con SMB en [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). Como alternativa, puede manipular el recurso compartido de archivos de Azure con Azure Portal. Todas las solicitudes efectuadas a través de Azure Portal se realizan con la API REST de File, lo que le permite crear, modificar y eliminar archivos y directorios en los clientes sin acceso a SMB.

### <a name="create-a-directory"></a>Creación de directorios
Al agregar un directorio se proporciona una estructura jerárquica para administrar el recurso compartido de archivos. Aunque puede crear varios niveles, debe asegurarse de que todos los directorios principales existan antes de crear un subdirectorio. Por ejemplo, para la ruta de acceso myDirectory/mySubDirectory, primero debe crear el directorio *myDirectory* y, luego, *mySubDirectory*. 

1. En la pestaña del recurso compartido de archivos, en el menú superior, haga clic en el botón **+ Nueva carpeta**. Se abre la página **Crear nuevo directorio**.
    ![Captura de pantalla del botón Nueva carpeta en contexto](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Escriba *myDirectory* como nombre y, luego, haga clic en **Aceptar**. 

El directorio *myDirectory* se muestra en la pestaña del recurso compartido de archivos *myshare*.

### <a name="upload-a-file"></a>Cargar un archivo 
Cargue un archivo desde la máquina local hasta el nuevo directorio del recurso compartido de archivos. Puede cargar una carpeta completa o un único archivo.

1. En el menú en la parte superior, seleccione **Cargar**. Esta operación le da la opción de cargar un archivo o una carpeta.

2. Seleccione **Cargar archivo** y, a continuación, elija un archivo de la máquina local para cargar.

3. En **Upload to a directory** (Cargar en un directorio), escriba *myDirectory* y, luego, haga clic en **Cargar**. 

Cuando termine, el archivo debe aparecer en la lista en la página **myDirectory**.

### <a name="download-a-file"></a>Descarga de un archivo
Puede descargar una copia de un archivo en el recurso compartido de archivos; para ello, haga clic con el botón derecho en el archivo y seleccione **Descargar**. Elija dónde desea colocar el archivo en la máquina local y, a continuación, haga clic en **Guardar**.

Verá el progreso de la descarga en el panel **Actividades** en la parte inferior de la ventana.

## <a name="create-and-modify-share-snapshots"></a>Creación y modificación de instantáneas de recurso compartido
Una instantánea conserva una copia de un momento dado de un recurso compartido de archivos de Azure. Las instantáneas de recurso compartido de archivos son similares a otras tecnologías con las que es posible que ya esté familiarizado:
- [Servicio de instantáneas de volumen (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de archivos Windows como NTFS y ReFS.
- Instantáneas del [Administrador de volúmenes lógicos (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantáneas de [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS. 

Para crear una instantánea de recurso compartido, siga estos pasos:

1. Abra la pestaña del recurso compartido de archivos *myshare*.
2. En el menú de la parte superior de la pestaña, haga clic en **Crear instantánea** (este elemento podría estar oculto detrás un elemento **... Más** según las dimensiones de la ventana del Explorador de Azure Storage).  
    ![Captura de pantalla del botón Crear instantánea en contexto](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Enumeración y exploración de instantáneas de recurso compartido
Una vez creada la instantánea, puede hacer clic en **View Snapshots for File Share** (Ver instantáneas de recurso compartido de archivos) (este elemento podría estar oculto detrás un elemento **... Más** según las dimensiones de la ventana del Explorador de Azure Storage) para enumerar las instantáneas del recurso compartido. Haga doble clic en una instantánea de recurso compartido para examinarla.

![Captura de pantalla de la pantalla de exploración de instantáneas](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Restauración desde una instantánea de recurso compartido
Para demostrar la restauración de un archivo desde una instantánea de recurso compartido, primero es necesario eliminar un archivo del recurso compartido de archivos de Azure activo. Vaya a la carpeta *myDirectory*, haga clic con el botón derecho en el archivo que cargó y, a continuación, haga clic en **Eliminar**. A continuación, para restaurar ese archivo desde la instantánea de recurso compartido:

1. Haga clic en **View Snapshots for File Share** (Ver instantáneas de recurso compartido de archivos) (este elemento podría estar oculto detrás de un elemento **... Más** según las dimensiones de la ventana del Explorador de Azure Storage).
2. Seleccione una instantánea de recurso compartido de la lista y haga doble clic para navegar.
3. Desplácese por la instantánea hasta que encuentre el archivo eliminado, selecciónelo y haga clic **Restaurar instantánea** (este elemento podría estar oculto detrás un elemento **... Más** según las dimensiones de la ventana del Explorador de Azure Storage). Recibirá una advertencia de que al restaurar el archivo se sobrescribirá el contenido del recurso compartido de archivos y esta acción no se puede deshacer. Seleccione **Aceptar**.
4. El archivo debe estar ahora en su lugar original en el recurso compartido de archivos de Azure activo.

### <a name="delete-a-share-snapshot"></a>Eliminación de una instantánea de recurso compartido
Para eliminar una instantánea de recurso compartido, [vaya a la lista de instantáneas de recurso compartido](#list-and-browse-share-snapshots). Haga clic con el botón derecho en la instantánea de recurso compartido que quiere eliminar y seleccione Eliminar.

## <a name="clean-up-resources"></a>Limpieza de recursos
El Explorador de Azure Storage no ofrece la posibilidad de quitar recursos; puede realizar una limpieza desde esta guía de inicio rápido con [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Pasos siguientes
- [Administración de recursos compartidos de archivos con Azure Portal](storage-how-to-use-files-portal.md)
- [Administración de recursos compartidos de archivos con Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Administración de recursos compartidos de archivos con la CLI de Azure](storage-how-to-use-files-cli.md)
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)