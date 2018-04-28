---
title: Administración de recursos compartidos de archivos de Azure mediante el Explorador de Azure Storage
description: Aprenda cómo usar el Explorador de Azure Storage para administrar Azure Files.
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
ms.openlocfilehash: 1953ee18fe878c33a1a0965937f64056278875cf
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2018
---
# <a name="manage-azure-file-shares-with-azure-storage-explorer"></a>Administración de recursos compartidos de archivos de Azure con el Explorador de Azure Storage 
[Azure Files](storage-files-introduction.md) es el sencillo sistema de archivos en la nube de Microsoft. En este artículo se describen los conceptos básicos sobre cómo trabajar con recursos compartidos de archivos de Azure mediante el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/). El Explorador de Storage es una herramienta de cliente popular que está disponible para Windows, macOS y Linux. Puede usar el Explorador de Storage para administrar recursos compartidos de archivos de Azure y otros recursos de almacenamiento.

Esta guía de inicio rápido requiere que esté instalado el Explorador de Storage. Para su descarga e instalación, vaya a [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

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
No se puede usar el Explorador de Storage para crear nuevos recursos. Para los fines de esta demostración, cree la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-create-storage-account-portal](../../../includes/storage-files-create-storage-account-portal.md)]

## <a name="connect-storage-explorer-to-azure-resources"></a>Conexión del Explorador de Storage a los recursos de Azure
Cuando se inicia por primera vez el Explorador de Storage, aparece la ventana **Explorador de Microsoft Azure Storage - Conectar**. El Explorador de Storage proporciona varias maneras de conectar con las cuentas de almacenamiento: 

- **Inicio de sesión con la cuenta de Azure**: puede iniciar sesión con las credenciales de usuario de su organización o su cuenta Microsoft. 
- **Conexión a una cuenta de almacenamiento específica mediante una cadena de conexión o un token de SAS**: una cadena de conexión es una cadena especial que contiene un nombre de cuenta de almacenamiento una clave de cuenta de almacenamiento o un token de SAS. Con el token, el Explorador de Storage accede directamente a la cuenta de almacenamiento (en lugar de simplemente ver todas las cuentas de almacenamiento de una cuenta de Azure). Para más información sobre las cadenas de conexión, consulte [Configuración de las cadenas de conexión de Azure Storage](../common/storage-configure-connection-string.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).
- **Conexión a una cuenta de almacenamiento específica con el nombre y la clave de una cuenta de almacenamiento**: use el nombre y la clave de la cuenta de almacenamiento para conectarse a Azure Storage.

Para los fines de esta guía de inicio rápido, inicie sesión con su cuenta de Azure. Seleccione **Add an Azure Account** (Agregar una cuenta de Azure) y, a continuación, seleccione **Sign In** (Iniciar sesión). Siga las indicaciones para iniciar sesión en su cuenta de Azure.

![Captura de pantalla de la ventana de conexión del Explorador de Microsoft Azure Storage](./media/storage-how-to-use-files-storage-explorer/connect-to-azure-storage-1.png)

### <a name="create-a-file-share"></a>Creación de un recurso compartido de archivos
Para crear su primer recurso compartido de archivos de Azure en la cuenta de almacenamiento *storageacct<random number>*, siga estos pasos:

1. Expanda la cuenta de almacenamiento que creó.
2. Haga clic con el botón derecho en **File Shares** (Recursos compartidos de archivos) y seleccione **Create File Share** (Crear recurso compartido de archivos).  
    ![Captura de pantalla de la carpeta de recursos compartidos de archivos y el menú contextual en contexto](media/storage-how-to-use-files-storage-explorer/create-file-share-1.png)

3. Para el recurso compartido de archivos, escriba *myshare* y, a continuación, presione ENTRAR.

> [!IMPORTANT]  
> Los nombres de los recursos compartidos solo pueden contener letras minúsculas, números y guiones sencillos (pero no pueden empezar con un guion). Para obtener detalles completos sobre cómo asignar un nombre a los recursos compartidos y los archivos, consulte [Asignación de nombres y referencia a recursos compartidos, directorios, archivos y metadatos](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Shares--Directories--Files--and-Metadata).

Después de crear el recurso compartido de archivos, se abre una pestaña para el recurso compartido de archivos en el panel derecho. 

## <a name="work-with-the-contents-of-an-azure-file-share"></a>Trabajo con el contenido de un recurso compartido de archivos de Azure
Ahora que ha creado un recurso compartido de archivos de Azure, puede montarlo con SMB en [Windows](storage-how-to-use-files-windows.md), [Linux](storage-how-to-use-files-linux.md) o [macOS](storage-how-to-use-files-mac.md). Como alternativa, puede trabajar con el recurso compartido de archivos de Azure con la CLI de Azure. La ventaja de usar la CLI de Azure en lugar de montar el recurso compartido de archivos mediante SMB es que todas las solicitudes que se realizan con la CLI de Azure se realizan mediante la API REST de Azure Files. Puede utilizar la API REST de Azure Files para crear, modificar y eliminar archivos y directorios en los clientes que no tienen acceso mediante SMB.

### <a name="create-a-directory"></a>Creación de directorios
Al agregar un directorio se proporciona una estructura jerárquica para administrar el recurso compartido de archivos. Puede crear varios niveles en el directorio. Debe asegurarse de que existen los directorios primarios antes de crear un subdirectorio. Por ejemplo, para la ruta de acceso myDirectory/mySubDirectory, primero debe crear el directorio *myDirectory*. A continuación, puede crear *mySubDirectory*. 

1. En la pestaña del recurso compartido de archivos, en el menú superior, seleccione el botón **New Folder** (Nueva carpeta). Se abre el panel **Create New Directory** (Crear nuevo directorio).
    ![Captura de pantalla del botón New Folder (Nueva carpeta) en contexto](media/storage-how-to-use-files-storage-explorer/create-directory-1.png)

2. Para el nombre del directorio, escriba *myDirectory* y, a continuación, seleccione **OK** (Aceptar). 

El directorio *myDirectory* se muestra en la pestaña del recurso compartido de archivos *myshare*.

### <a name="upload-a-file"></a>Cargar un archivo 
Puede cargar un archivo desde la máquina local hasta el nuevo directorio del recurso compartido de archivos. Puede cargar una carpeta completa o un único archivo.

1. En el menú superior, seleccione **Upload** (Cargar). Esta operación le da la opción de cargar un archivo o una carpeta.
2. Seleccione **Upload File** (Cargar archivo) y, a continuación, seleccione un archivo de la máquina local para cargar.
3. En **Cargar en un directorio**, escriba *myDirectory* y, a continuación, seleccione **Cargar**. 

Cuando haya terminado, el archivo aparece en la lista en el panel *myDirectory*.

### <a name="download-a-file"></a>Descarga de un archivo
Para descargar una copia de un archivo desde el recurso compartido de archivos, haga clic con el botón derecho en el archivo y, a continuación, seleccione **Download** (Descargar). Elija dónde desea colocar el archivo en la máquina local y, a continuación, seleccione **Save** (Guardar).

El progreso de la descarga aparece en el panel **Activities** (Actividades) en la parte inferior de la ventana.

## <a name="create-and-modify-share-snapshots"></a>Creación y modificación de instantáneas de recurso compartido
Una instantánea conserva una copia de un momento dado de un recurso compartido de archivos de Azure. Las instantáneas de recursos compartidos de archivos son similares a otras tecnologías con las que es posible que ya esté familiarizado:
- [Servicio de instantáneas de volumen (VSS)](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/ee923636) para sistemas de archivos Windows como NTFS y ReFS
- Instantáneas del [Administrador de volúmenes lógicos (LVM)](https://en.wikipedia.org/wiki/Logical_Volume_Manager_(Linux)#Basic_functionality) para sistemas Linux
- Instantáneas de [Apple File System (APFS)](https://developer.apple.com/library/content/documentation/FileManagement/Conceptual/APFS_Guide/Features/Features.html) para macOS

Para crear una instantánea de recurso compartido, siga estos pasos:

1. Seleccione la pestaña del recurso compartido de archivos *myshare*.
2. En el menú superior, seleccione **Create Snapshot** (Crear instantánea). (Podría tener que seleccionar en primer lugar **More** (Más) para ver esta opción, dependiendo de las dimensiones de la ventana del Explorador de Storage).  
    ![Captura de pantalla del botón Create Snapshot (Crear instantánea) en contexto](media/storage-how-to-use-files-storage-explorer/create-share-snapshot-1.png)

### <a name="list-and-browse-share-snapshots"></a>Enumeración y exploración de instantáneas de recurso compartido
Después de crear la instantánea, para obtener una lista de las instantáneas del recurso compartido, seleccione **View Snapshots for File Share** (Ver instantáneas del recurso compartido de archivos). (Podría tener que seleccionar en primer lugar **More** (Más) para ver esta opción, dependiendo de las dimensiones de la ventana del Explorador de Storage). Para examinar una instantánea del recurso compartido, haga doble clic en la instantánea.

![Captura de pantalla de la ventana de exploración de instantáneas](media/storage-how-to-use-files-storage-explorer/list-browse-snapshots-1.png)

### <a name="restore-from-a-share-snapshot"></a>Restauración desde una instantánea de recurso compartido
Para demostrar cómo restaurar un archivo desde una instantánea del recurso compartido, primero es necesario eliminar un archivo del recurso compartido de archivos de Azure activo. Vaya a la carpeta *myDirectory*, haga clic con el botón derecho en el archivo que cargó y, a continuación, seleccione **Delete** (Eliminar). Para restaurar ese archivo desde la instantánea del recurso compartido:

1. Seleccione **View Snapshots for File Share** (Ver instantáneas del recurso compartido de archivos). (Podría tener que seleccionar en primer lugar **More** (Más) para ver esta opción, dependiendo de las dimensiones de la ventana del Explorador de Storage).
2. En la lista de instantáneas del recurso compartido, haga doble clic en la instantánea del recurso compartido.
3. Examine la instantánea hasta que encuentre el archivo que ha eliminado. Seleccione el recurso compartido de archivos y, a continuación, seleccione **Restore Snapshot** (Restaurar instantánea). (Podría tener que seleccionar en primer lugar **More** (Más) para ver esta opción, dependiendo de las dimensiones de la ventana del Explorador de Storage). Se abre una ventana y muestra una advertencia de que al restaurar el archivo se sobrescribirá el contenido del recurso compartido de archivos y esta acción no se puede deshacer. Seleccione **Aceptar**.
4. El archivo debe estar ahora en su lugar original en el recurso compartido de archivos de Azure activo.

### <a name="delete-a-share-snapshot"></a>Eliminación de una instantánea de recurso compartido
Para eliminar una instantánea del recurso compartido, vaya a la [lista de instantáneas del recurso compartido](#list-and-browse-share-snapshots). Haga clic con el botón derecho en la instantánea del recurso compartido que desea eliminar y, a continuación, seleccione **Delete** (Eliminar).

## <a name="clean-up-resources"></a>Limpieza de recursos
No se puede usar el Explorador de Storage para eliminar recursos. Para realizar la limpieza de esta guía de inicio rápido, puede usar [Azure Portal](https://portal.azure.com/). 

[!INCLUDE [storage-files-clean-up-portal](../../../includes/storage-files-clean-up-portal.md)]

## <a name="next-steps"></a>Pasos siguientes
- [Administración de recursos compartidos de archivos con Azure Portal](storage-how-to-use-files-portal.md)
- [Administración de recursos compartidos de archivos con Azure PowerShell](storage-how-to-use-files-powershell.md)
- [Administración de recursos compartidos de archivos con la CLI de Azure](storage-how-to-use-files-cli.md)
- [Planeamiento de una implementación de Azure Files](storage-files-planning.md)