---
title: "Migración de datos locales a Azure Storage mediante AzCopy | Microsoft Docs"
description: "Use AzCopy para migrar datos al contenido de archivos, blobs y tablas o copiarlos. Realice la migración de los datos fácilmente desde el almacenamiento local a Azure Storage."
services: storage
author: ruthogunnnaike
manager: jeconnoc
ms.service: storage
ms.tgt_pltfrm: na
ms.devlang: azcopy
ms.topic: tutorial
ms.date: 12/14/2017
ms.author: v-ruogun
ms.openlocfilehash: 3dbfb935ac0b134e127a5dccb7bc76716c688e8a
ms.sourcegitcommit: c4cc4d76932b059f8c2657081577412e8f405478
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
#  <a name="migrate-on-premises-data-to-cloud-storage-by-using-azcopy"></a>Migración de datos locales al almacenamiento en la nube mediante AzCopy

AzCopy es una herramienta de línea de comandos para copiar datos de Azure Blob Storage, Azure Files y Azure Table Storage (o en ellos) mediante comandos simples. Los comandos están diseñados para optimizar el rendimiento. Puede copiar datos entre un sistema de archivos y una cuenta de almacenamiento o entre cuentas de almacenamiento.  

Se pueden descargar dos versiones de AzCopy:

* [AzCopy en Linux](storage-use-azcopy.md) se compila con .NET Core Framework. Tiene como destino plataformas Linux, ya que ofrece opciones de línea de comandos de estilo POSIX. 
* [AzCopy en Windows](../storage-use-azcopy.md) se compila con .NET Framework. Ofrece opciones de línea de comandos de estilo Windows. 
 
En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Cree una cuenta de almacenamiento. 
> * Use AzCopy para cargar todos los datos.
> * Modifique los datos para fines de prueba.
> * Cree una tarea programada o trabajo cron para identificar los nuevos archivos que cargar.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, descargue la versión más reciente de AzCopy en [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#download-and-install-azcopy) o [Windows](http://aka.ms/downloadazcopy). 

[!INCLUDE [storage-quickstart-tutorial-create-account-portal](../../../includes/storage-quickstart-tutorial-create-account-portal.md)]

>[!NOTE]
>Si desea descargar blobs de una región secundaria del almacenamiento local y cargarlos, establezca **Replicación** en  **	Almacenamiento con redundancia geográfica con acceso de lectura**. Al seleccionar esta opción se crea una cuenta de [almacenamiento con redundancia geográfica](https://docs.microsoft.com/azure/storage/common/storage-redundancy#geo-redundant-storage). 
>
>

## <a name="create-a-container"></a>Crear un contenedor

Los blobs siempre se cargan en un contenedor. Puede usar contenedores para organizar grupos de blobs de la misma forma que organiza archivos en carpetas en el equipo. 

Siga estos pasos para crear un contenedor:

1. Seleccione el botón **Cuentas de almacenamiento** de la página principal y la cuenta de almacenamiento que ha creado.
2. Seleccione **Blobs** en **Servicios** y **Contenedor**. 

   ![Crear un contenedor](media/storage-azcopy-migrate-on-premises-data/CreateContainer.png)
 
El nombre de contenedor debe empezar por una letra o un número. Solo puede contener letras, números y el carácter de guion (-). Para conocer más reglas sobre la nomenclatura de los contenedores de almacenamiento, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).

## <a name="upload-all-files-in-a-folder-to-blob-storage"></a>Carga de todos los archivos de una carpeta en Blob Storage

Puede usar AzCopy para cargar todos los archivos en una carpeta en Blob Storage en [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#upload-blobs-to-blob-storage) o [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux#blob-download). Para cargar todos los blobs en una carpeta, escriba el siguiente comando de AzCopy:

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
        --source /mnt/myfolder \
        --destination https://myaccount.blob.core.windows.net/mycontainer \
        --dest-key <key> \
        --recursive

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S
---

Reemplace `<key>` y `key` por el la clave de cuenta. En Azure Portal, la clave de cuenta se recupera al seleccionar **Claves de acceso** en **Configuración** en la cuenta de almacenamiento. Seleccione una clave y péguela en el comando de AzCopy. Si el contenedor de destino especificado no existe, AzCopy lo crea y carga el archivo en él. Actualice la ruta de acceso de origen al directorio de datos y reemplace **myaccount** en la dirección URL de destino con el nombre de la cuenta de almacenamiento.

Para cargar el contenido del directorio especificado repetidamente en Blob Storage, especifique la opción `--recursive` (Linux) o `/S` (Windows). Al ejecutar AzCopy con una de estas opciones, se cargan también todas las subcarpetas y sus archivos.

## <a name="upload-modified-files-to-blob-storage"></a>Carga de archivos modificados en Blob Storage
Puede utilizar AzCopy para [cargar archivos](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy#other-azcopy-features) según la hora de la última modificación. Para probar esto, modifique o cree archivos en el directorio de origen para fines de prueba. Para cargar solo los archivos nuevos o actualizados, agregue el parámetro `--exclude-older` (Linux) o `/XO` (Windows) al comando AzCopy.

Si solo desea copiar los recursos de origen que no existen en el destino, especifique ambos parámetros `--exclude-older` y `--exclude-newer` (Linux) o `/XO` y `/XN` (Windows) en el comando de AzCopy. AzCopy carga solo los datos actualizados, en función de su marca de tiempo.
 
# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy \
    --source /mnt/myfolder \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive \
    --exclude-older

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /S /XO
---

## <a name="create-a-scheduled-task-or-cron-job"></a>Creación de una tarea programada o trabajo cron 
Puede crear una tarea programada o un trabajo cron que ejecute un script de AzCopy. El script identifica y carga los datos locales nuevos en el almacenamiento en la nube en un intervalo de tiempo específico. 

Copie el comando de AzCopy en un editor de texto. Actualice los valores de parámetro del comando de AzCopy con los valores adecuados. Guarde el archivo como `script.sh` (Linux) o `script.bat` (Windows) para AzCopy. 

# <a name="linuxtablinux"></a>[Linux](#tab/linux)
    azcopy --source /mnt/myfiles --destination https://myaccount.blob.core.windows.net/mycontainer --dest-key <key> --recursive --exclude-older --exclude-newer --verbose >> Path/to/logfolder/`date +\%Y\%m\%d\%H\%M\%S`-cron.log

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
    cd C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy
    AzCopy /Source: C:\myfolder  /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey: key /V /XO /XN >C:\Path\to\logfolder\azcopy%date:~-4,4%%date:~-7,2%%date:~-10,2%%time:~-11,2%%time:~-8,2%%time:~-5,2%.log
---

AzCopy se ejecuta con la opción detallada `--verbose` (Linux) o `/V` (Windows). La salida se redirige a un archivo de registro. 

En este tutorial, se utiliza [Schtasks](https://msdn.microsoft.com/library/windows/desktop/bb736357(v=vs.85).aspx) para crear una tarea programada en Windows. El comando [Crontab](http://crontab.org/) se utiliza para crear un trabajo cron en Linux. 
 **Schtasks** permite a un administrador crear, eliminar, consultar, cambiar, ejecutar y finalizar tareas programadas en un equipo local o remoto. **Cron** permite a los usuarios de Unix y Linux ejecutar comandos o scripts en una fecha y hora especificadas mediante [expresiones cron](https://en.wikipedia.org/wiki/Cron#CRON_expression).


# <a name="linuxtablinux"></a>[Linux](#tab/linux)
Para crear un trabajo cron en Linux, escriba el siguiente comando en un terminal: 

```bash
crontab -e 
*/5 * * * * sh /path/to/script.sh 
```

Especificar la expresión cron `*/5 * * * * ` en el comando indica que el script de shell `script.sh` debe ejecutarse cada cinco minutos. Puede programar que el script se ejecute en un momento determinado todos los días, meses o años. Para más información sobre cómo establecer la fecha y la hora de ejecución de un trabajo, consulte las [expresiones cron](https://en.wikipedia.org/wiki/Cron#CRON_expression). 

# <a name="windowstabwindows"></a>[Windows](#tab/windows)
Para crear una tarea programada en Windows, escriba el siguiente comando en un símbolo del sistema o en PowerShell:

```cmd 
schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\Users\username\Documents\script.bat
```

El comando utiliza:
- El parámetro `/SC` para especificar una programación por minuto.
- El parámetro `/MO` para especificar un intervalo de cinco minutos.
- El parámetro `/TN` para especificar el nombre de la tarea.
- El parámetro `/TR` para especificar la ruta de acceso al archivo `script.bat`. 

Para más información sobre cómo crear una tarea programada en Windows, consulte [Schtasks](https://technet.microsoft.com/library/cc772785(v=ws.10).aspx#BKMK_minutes).

---
 
Para asegurarse de que la tarea programada o el trabajo cron se ejecutan correctamente, cree nuevos archivos en el directorio `myfolder`. Espere cinco minutos para confirmar que los nuevos archivos se han cargado en la cuenta de almacenamiento. Vaya al directorio de registro para ver los registros de salida de la tarea programada o el trabajo cron. 

Para más información sobre las formas de mover datos locales a Azure Storage y viceversa, consulte [Movimiento de datos hacia y desde Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-moving-data?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).  

## <a name="next-steps"></a>pasos siguientes
Para más información sobre Azure Storage y AzCopy, consulte los recursos siguientes:

* [Introducción a Azure Storage](../storage-introduction.md)
* [Transferencia de datos con AzCopy en Windows](storage-use-azcopy.md) 
* [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md) 
* [crear una cuenta de almacenamiento](../storage-create-storage-account.md)
* [Administración de blobs con el Explorador de Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)  






 
 

