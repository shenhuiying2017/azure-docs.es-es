---
title: Copia o traslado de datos a Azure Storage con AzCopy en Windows| Microsoft Docs
description: "Use la utilidad AzCopy en Windows para mover o copiar datos hacia o desde contenido de archivos, blobs y tablas. Copie datos a Almacenamiento de Azure desde archivos locales o copie datos en o entre cuentas de almacenamiento. Migre fácilmente sus datos a Almacenamiento de Azure."
services: storage
documentationcenter: 
author: seguler
manager: jahogg
editor: tysonn
ms.assetid: aa155738-7c69-4a83-94f8-b97af4461274
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/14/2017
ms.author: seguler
ms.openlocfilehash: 1a4c52babe76e59eacb30e8be91ed934cdbe305b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transfer-data-with-the-azcopy-on-windows"></a>Transferencia de datos con AzCopy en Windows
AzCopy es una utilidad de línea de comandos diseñada para copiar datos a y desde los servicios de Almacenamiento de blobs, Archivos y Almacenamiento de tablas de Microsoft Azure, mediante sencillos comandos con un rendimiento óptimo. También puede copiar datos de un objeto a otro dentro de la cuenta de almacenamiento o entre cuentas de almacenamiento.

Hay dos versiones de AzCopy que puede descargar. AzCopy en Windows está integrado en .NET Framework y ofrece opciones de línea de comandos de estilo Windows. [AzCopy en Linux](storage-use-azcopy-linux.md) está integrado en .NET Core Framework que tiene como destino plataformas Linux y ofrece opciones de línea de comandos de estilo POSIX. Este artículo trata sobre AzCopy en Windows.

## <a name="download-and-install-azcopy-on-windows"></a>Descarga e instalación de AzCopy en Windows

Descargue la [versión más reciente de AzCopy en Windows](http://aka.ms/downloadazcopy).

Después de instalar AzCopy en Windows con el programa de instalación, abra una ventana de comandos y desplácese hasta el directorio de instalación de AzCopy en el equipo, donde se encuentra el ejecutable `AzCopy.exe`. Si lo desea, puede agregar la ubicación de instalación de AzCopy a la ruta de acceso del sistema. De forma predeterminada, AzCopy se instala en `%ProgramFiles(x86)%\Microsoft SDKs\Azure\AzCopy` o `%ProgramFiles%\Microsoft SDKs\Azure\AzCopy`.

## <a name="writing-your-first-azcopy-command"></a>Escritura del primer comando de AzCopy

La sintaxis básica del comando AzCopy es:

```azcopy
AzCopy /Source:<source> /Dest:<destination> [Options]
```

Los ejemplos siguientes muestran diversos escenarios para copiar datos a y desde los blobs, archivos y tablas de Microsoft Azure. Consulte la sección [Parámetros de AzCopy](#azcopy-parameters) para obtener una explicación detallada de los parámetros utilizados en cada ejemplo.

## <a name="download-blobs-from-blob-storage"></a>Descarga de blobs desde Blob Storage

Echemos un vistazo a varias maneras de descargar blobs mediante AzCopy.

### <a name="download-a-single-blob"></a>Descarga de un solo blob

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:"abc.txt"
```

Tenga en cuenta que si la carpeta `C:\myfolder` no existe, AzCopy la crea y descarga `abc.txt ` en la nueva carpeta.

### <a name="download-a-single-blob-from-the-secondary-region"></a>Descarga de un solo blob desde la región secundaria

```azcopy
AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Tenga en cuenta que debe tener almacenamiento con redundancia geográfica con acceso de lectura habilitado para acceder a la región secundaria.

### <a name="download-all-blobs-in-a-container"></a>Descarga de todos los blobs de un contenedor

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S
```

Supongamos que los siguientes blobs residen en el contenedor especificado:  

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de descarga, el directorio `C:\myfolder` incluye los archivos siguientes:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

Si no especifica la opción `/S`, no se descarga ningún blob.

### <a name="download-blobs-with-a-specific-prefix"></a>Descarga de blobs con un prefijo específico

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S
```

Supongamos que los siguientes blobs residen en el contenedor especificado. Se descargan todos los blobs que comiencen con el prefijo `a`:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de descarga, la carpeta `C:\myfolder` incluye los archivos siguientes:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

El prefijo se aplica al directorio virtual, que forma la primera parte del nombre del blob. En el ejemplo mostrado anteriormente, el directorio virtual no coincide con el prefijo especificado, por lo que no se descarga. Además, si no se especifica la opción `\S`, AzCopy no descarga ningún blob.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Establecimiento de la hora de la última modificación de los archivos exportados para que coincida con la de los blobs de origen

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT
```

También puede excluir los blobs de la operación de descarga basándose en la hora de su última modificación. Por ejemplo, si desea excluir los blobs cuya hora de la última modificación es igual o más reciente que la del archivo de destino, agregue la opción `/XN` :

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN
```

Si desea excluir los blobs cuya hora de la última modificación es igual o anterior a la del archivo de destino, agregue la opción `/XO`:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO
```

## <a name="upload-blobs-to-blob-storage"></a>Carga de blobs a Blob Storage

Veamos varias maneras de cargar blobs mediante AzCopy.

### <a name="upload-a-single-blob"></a>Carga de un solo blob

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:"abc.txt"
```

Si el contenedor de destino especificado no existe, AzCopy lo crea y carga el archivo en él.

### <a name="upload-a-single-blob-to-a-virtual-directory"></a>Carga de un solo blob en un directorio virtual

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt
```

Si el directorio virtual especificado no existe, AzCopy carga el archivo para incluir el directorio virtual en su nombre (*por ejemplo*, `vd/abc.txt` en el ejemplo anterior).

### <a name="upload-all-blobs-in-a-folder"></a>Carga de todos los blobs de una carpeta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S
```

Al especificar la opción `/S` se carga el contenido del directorio especificado en Blob Storage de forma recursiva, lo que significa que todas las subcarpetas y sus archivos también se cargan. Por ejemplo, supongamos que los siguientes archivos residen en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de carga, el contenedor incluye los archivos siguientes:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si no especifica la opción `/S`, AzCopy no realiza la carga de forma recursiva. Después de la operación de carga, el contenedor incluye los archivos siguientes:

    abc.txt
    abc1.txt
    abc2.txt

### <a name="upload-blobs-matching-a-specific-pattern"></a>Carga de blobs que coinciden con un patrón específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S
```

Supongamos que los siguientes archivos residen en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de carga, el contenedor incluye los archivos siguientes:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

Si no especifica la opción `/S`, AzCopy solo carga los blobs que no residan en un directorio virtual:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especificación del tipo de contenido MIME de un blob de destino

De forma predeterminada, AzCopy define el tipo de contenido de un blob de destino como `application/octet-stream`. A partir de la versión 3.1.0, puede especificar explícitamente el tipo de contenido a través de la opción `/SetContentType:[content-type]`. Esta sintaxis establece el tipo de contenido para todos los blobs de una operación de carga.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType:video/mp4
```

Si especifica `/SetContentType` sin un valor, AzCopy establece cada blob o tipo de contenido del archivo según su extensión de archivo.

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.blob.core.windows.net/myContainer/ /DestKey:key /Pattern:ab /SetContentType
```

## <a name="copy-blobs-in-blob-storage"></a>Copia de blobs en Blob Storage

Veamos varias maneras de copiar blobs de una ubicación a otra mediante AzCopy.

### <a name="copy-a-single-blob-from-one-container-to-another-within-the-same-storage-account"></a>Copia de un solo blob desde un contenedor a otro dentro de la misma cuenta de almacenamiento 

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt
```

Cuando copia un blob dentro de una cuenta de almacenamiento, se realiza una operación de [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-a-single-blob-from-one-storage-account-to-another"></a>Copia de un solo blob de una cuenta de almacenamiento a otra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Cuando copia un blob entre cuentas de almacenamiento, se realiza una operación de [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx) .

### <a name="copy-a-single-blob-from-the-secondary-region-to-the-primary-region"></a>Copia de un solo blob de la región secundaria a la región primaria

```azcopy
AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
```

Tenga en cuenta que debe tener almacenamiento con redundancia geográfica con acceso de lectura habilitado para acceder al almacenamiento secundario.

### <a name="copy-a-single-blob-and-its-snapshots-from-one-storage-account-to-another"></a>Copia de un solo blob y sus instantáneas de una cuenta de almacenamiento a otra

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot
```

Después de la operación de copia, el contenedor de destino incluye el blob y sus instantáneas. Suponiendo que el blob del ejemplo anterior tiene dos instantáneas, el contenedor incluye el siguiente blob e instantáneas:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### <a name="copy-all-blobs-in-a-container-to-another-storage-account"></a>Copia de todos los blobs de un contenedor a otra cuenta de almacenamiento 

```azcopy
AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer1 
/Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /S
```

Especificar la opción /S carga el contenido del contenedor especificado de forma recursiva. Consulte [Carga de todos los blobs de una carpeta](#upload-all-blobs-in-a-folder) para más información y un ejemplo.

### <a name="synchronously-copy-blobs-from-one-storage-account-to-another"></a>Copia sincrónica de blobs de una cuenta de almacenamiento a otra

De forma predeterminada, AzCopy copia datos entre dos extremos de almacenamiento asincrónicamente. Por lo tanto, la operación de copia se ejecuta en segundo plano con la capacidad de ancho de banda de reserva sin SLA en relación con la velocidad con que se copia un blob y AzCopy comprueba periódicamente el estado de la copia hasta que se complete o se devuelva un error.

La opción `/SyncCopy` garantiza que la operación de copia tiene una velocidad uniforme. Para hacer la copia sincrónica, AzCopy descarga los blobs que se deben copiar desde el origen especificado a la memoria local y, a continuación, los carga en el destino de almacenamiento de blobs.

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/myContainer/ /Dest:https://myaccount2.blob.core.windows.net/myContainer/ /SourceKey:key1 /DestKey:key2 /Pattern:ab /SyncCopy
```

`/SyncCopy` podría generar un costo de salida adicional en comparación con la copia asincrónica. Es recomendable usar esta opción en la máquina virtual de Azure que se encuentra en la misma región que la cuenta de almacenamiento de origen, para evitar el costo de salida.

## <a name="download-files-from-file-storage"></a>Descarga de archivos desde File Storage

Veamos varias maneras de descargar archivos mediante AzCopy.

### <a name="download-a-single-file"></a>Descarga de un solo archivo

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt
```

Si el origen especificado es un recurso compartido de archivos de Azure, entonces tiene que especificar el nombre de archivo exacto (*p. ej.* `abc.txt`) para descargar un solo archivo, o especificar la opción `/S` para descargar todos los archivos en el recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción `/S` en conjunto, se produce un error.

### <a name="download-all-files-in-a-directory"></a>Descarga de todos los archivos de un directorio

```azcopy
AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S
```

Tenga en cuenta que no se descargan las carpetas vacías.

## <a name="upload-files-to-an-azure-file-share"></a>Carga de archivos en un recurso compartido de archivos de Azure

Veamos varias maneras de cargar archivos mediante AzCopy.

### <a name="upload-a-single-file"></a>Carga de un solo archivo

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:abc.txt
```

### <a name="upload-all-files-in-a-folder"></a>Carga de todos los archivos de una carpeta

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S
```

Tenga en cuenta que no se cargan las carpetas vacías.

### <a name="upload-files-matching-a-specific-pattern"></a>Carga de archivos que coinciden con un patrón específico

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S
```

## <a name="copy-files-in-file-storage"></a>Copia de archivos en File Storage

Veamos varias maneras de copiar archivos en un recurso compartido de archivos de Azure mediante AzCopy.

### <a name="copy-from-one-file-share-to-another"></a>Copia de un recurso compartido de archivos a otro

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S
```
Al copiar un archivo en distintos recursos compartidos de archivos, se realiza una operación de [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-an-azure-file-share-to-blob-storage"></a>Copia de un recurso compartido de archivos de Azure a Blob Storage

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare/ /Dest:https://myaccount2.blob.core.windows.net/mycontainer/ /SourceKey:key1 /DestKey:key2 /S
```
Al copiar un archivo desde un recurso compartido de archivos en un blob, se realiza una [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-a-blob-from-blob-storage-to-an-azure-file-share"></a>Copia de un blob de Blob Storage a un recurso compartido de archivos de Azure

```azcopy
AzCopy /Source:https://myaccount1.blob.core.windows.net/mycontainer/ /Dest:https://myaccount2.file.core.windows.net/myfileshare/ /SourceKey:key1 /DestKey:key2 /S
```
Cuando copia un archivo de un blob a un recurso compartido de archivos, se realiza una operación de [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="synchronously-copy-files"></a>Copia sincrónica de archivos

Puede especificar la opción `/SyncCopy` para copiar datos de forma sincrónica del Almacenamiento de archivos al Almacenamiento de archivos, del Almacenamiento de archivos al Almacenamiento de blobs y del Almacenamiento de blobs al Almacenamiento de archivos; para ello, AzCopy descarga los datos de origen en la memoria local y los carga de nuevo al destino. Se aplica el costo de salida estándar.

```azcopy
AzCopy /Source:https://myaccount1.file.core.windows.net/myfileshare1/ /Dest:https://myaccount2.file.core.windows.net/myfileshare2/ /SourceKey:key1 /DestKey:key2 /S /SyncCopy
```

Cuando hace una copia de File Storage a Blob Storage, el tipo de blob predeterminado es blob en bloques. El usuario puede especificar la opción `/BlobType:page` para cambiar el tipo de blob de destino.

Tenga en cuenta que `/SyncCopy` podría generar un costo de salida adicional en comparación con la copia asincrónica. El enfoque recomendado es usar esta opción en la máquina virtual de Azure que se encuentra en la misma región que la cuenta de almacenamiento de origen, para evitar el costo de salida.

## <a name="export-data-from-table-storage"></a>Exportación de datos desde Table Storage

Examinemos la exportación de datos desde Azure Table Storage mediante AzCopy.

### <a name="export-a-table"></a>Exportación de una tabla

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key
```

AzCopy escribe un archivo de manifiesto en la carpeta de destino especificada. El archivo de manifiesto se utiliza por parte del proceso de importación para ubicar los archivos de datos necesarios y realizar la validación de datos. El archivo de manifiesto utiliza la siguiente convención de nombres de forma predeterminada:

    <account name>_<table name>_<timestamp>.manifest

El usuario también puede especificar la opción `/Manifest:<manifest file name>` para establecer el nombre del archivo de manifiesto.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /Manifest:abc.manifest
```

### <a name="split-an-export-from-table-storage-into-multiple-files"></a>División de una exportación de Table Storage en varios archivos

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/mytable/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100
```

AzCopy usa un *índice de volumen* en los nombres de los archivos de datos divididos para distinguir los diversos archivos. El índice de volumen consta de dos partes, un *índice de rango de clave de partición* y un *índice de archivo dividido*. Ambos índices se basan en 0.

El rango de clave de partición es 0, si un usuario no especifica la opción `/PKRS`.

Por ejemplo, imagine que AzCopy genera dos archivos de datos después de que el usuario especifique la opción `/SplitSize`. Los nombres de archivos de datos resultantes serían:

    myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
    myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenga en cuenta que el mínimo valor posible para la opción `/SplitSize` es 32 MB. Si el destino especificado es Blob Storage, AzCopy divide el archivo de datos una vez que su tamaño alcanza el límite especificado (200 GB), independientemente de si el usuario ha especificado o no la opción `/SplitSize`.

### <a name="export-a-table-to-json-or-csv-data-file-format"></a>Exportación de una tabla al formato de archivo de datos JSON o CSV

De manera predeterminada, AzCopy exporta las tablas a archivos de datos JSON. Puede especificar la opción `/PayloadFormat:JSON|CSV` para exportar las tablas como JSON o CSV.

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PayloadFormat:CSV
```

Al especificar el formato de carga CSV, AzCopy también genera un archivo de esquema con la extensión `.schema.csv` para cada archivo de datos.

### <a name="export-table-entities-concurrently"></a>Exportación de entidades de tabla simultáneamente

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:C:\myfolder\ /SourceKey:key /PKRS:"aa#bb"
```

AzCopy inicia operaciones simultáneas para exportar entidades cuando el usuario especifica la opción `/PKRS`. Cada operación exporta un rango de claves de partición.

Tenga en cuenta que el número de operaciones simultáneas está controlado también por la opción `/NC`. AzCopy usa el número de procesadores de núcleo como valor predeterminado de `/NC` al copiar entidades de tabla, incluso si no se especificó `/NC`. Si el usuario especifica la opción `/PKRS`, AzCopy usa el valor más pequeño de los dos (rangos de claves de partición u operaciones simultáneas especificadas de manera implícita o explícita) para determinar el número de operaciones simultáneas que van a dar inicio. Para obtener más detalles, escriba `AzCopy /?:NC` en la línea de comandos.

### <a name="export-a-table-to-blob-storage"></a>Exportación de una tabla a Blob Storage

```azcopy
AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2
```

AzCopy genera un archivo de datos JSON en el contenedor de blobs local con la siguiente convención de nomenclatura:

    <account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

El archivo de datos JSON generado sigue el formato de carga para metadatos mínimos. Para obtener más información sobre el formato de carga, consulte [Formato de carga para las operaciones del servicio Tabla](http://msdn.microsoft.com/library/azure/dn535600.aspx).

Tenga en cuenta que, al exportar tablas a blobs, AzCopy descarga las entidades de tabla en archivos de datos temporales locales y, a continuación, carga tales entidades en el blob. Estos archivos de datos temporales se colocan en la carpeta de archivos de diario con la ruta de acceso predeterminada "<code>%LocalAppData%\Microsoft\Azure\AzCopy</code>". Puede especificar la opción /Z:[carpeta-de-archivos-de-diario] para cambiar la ubicación de esta carpeta y, así, modificar también la de los archivos de datos temporales. El tamaño de los archivos de datos temporales se decide según el de las entidades de tabla y el especificado con la opción /SplitSize, aunque el archivo de datos temporales en el disco local se elimina inmediatamente después de cargarse en el blob. Asegúrese de que tiene suficiente espacio en el disco local para almacenar estos archivos de datos temporales antes de que se eliminen.

## <a name="import-data-into-table-storage"></a>Importación de datos a Table Storage

Examinemos la importación de datos a Azure Table Storage mediante AzCopy.

### <a name="import-a-table"></a>Importación de una tabla

```azcopy
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

La opción `/EntityOperation` indica cómo insertar entidades en la tabla. Los valores posibles son:

* `InsertOrSkip`omite una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
* `InsertOrMerge`combina una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
* `InsertOrReplace`reemplaza una entidad existente o inserta una nueva entidad si esta no existe en la tabla.

Tenga en cuenta que no puede especificar la opción `/PKRS` en el escenario de importación. A diferencia del escenario de exportación, en el que debe especificar la opción `/PKRS` para iniciar las operaciones simultáneas, AzCopy inicia de manera predeterminada operaciones simultáneas cuando se importa una tabla. El número predeterminado de operaciones simultáneas que se inician equivale al número de procesadores de núcleo; sin embargo, puede especificar un número diferente para la simultaneidad con la opción `/NC`. Para obtener más detalles, escriba `AzCopy /?:NC` en la línea de comandos.

Tenga en cuenta que AzCopy solo admite la importación de archivos con el formato JSON, no CSV. AzCopy no permite importar tablas de archivos de manifiesto y archivos JSON creados por los usuarios. Estos archivos deben proceder de una exportación de tablas de AzCopy. Para evitar errores, no modifique el archivo JSON exportado ni el archivo de manifiesto.

### <a name="import-entities-into-a-table-from-blob-storage"></a>Importación de entidades a una tabla de Blob Storage

Suponga que un contenedor de blobs contiene lo siguiente: un archivo JSON que representa una tabla de Azure y el archivo de manifiesto que lo acompaña.

    myaccount_mytable_20140103T112020.manifest
    myaccount_mytable_20140103T112020_0_0_0AF395F1DC42E952.json

Puede ejecutar el comando siguiente para importar las entidades en una tabla mediante el archivo de manifiesto de ese contenedor de blobs:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:https://myaccount.table.core.windows.net/mytable /SourceKey:key1 /DestKey:key2 /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:"InsertOrReplace"
```

## <a name="other-azcopy-features"></a>Otras características de AzCopy

Veamos algunas otras características de AzCopy.

### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Solo copie los datos que no existan en el destino

Los parámetros `/XO` y `/XN` le permiten excluir los recursos de origen anteriores o posteriores a la copia, respectivamente. Si solo desea copiar los recursos de origen que no existen en el destino, puede especificar ambos parámetros en el comando de AzCopy:

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /XO /XN

    /Source:C:\myfolder /Dest:http://myaccount.file.core.windows.net/myfileshare /DestKey:<destkey> /S /XO /XN

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:http://myaccount.blob.core.windows.net/mycontainer1 /SourceKey:<sourcekey> /DestKey:<destkey> /S /XO /XN

Tenga en cuenta que esto no se admite cuando el origen o el destino es una tabla.

### <a name="use-a-response-file-to-specify-command-line-parameters"></a>Uso de un archivo de respuesta para especificar parámetros de línea de comandos

```azcopy
AzCopy /@:"C:\responsefiles\copyoperation.txt"
```

Puede incluir cualquier parámetro de la línea de comandos de AzCopy en un archivo de respuesta. AzCopy procesa los parámetros del archivo como si hubieran sido especificados en la línea de comandos, realizando una sustitución directa con el contenido del archivo.

Imaginemos un archivo de respuesta llamado `copyoperation.txt`, que contiene las siguientes líneas. Cada parámetro de AzCopy se puede especificar en una sola línea

    /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

o bien, en líneas independientes:

    /Source:http://myaccount.blob.core.windows.net/mycontainer
    /Dest:C:\myfolder
    /SourceKey:<sourcekey>
    /S
    /Y

AzCopy genera un error si divide el parámetro en dos líneas, tal como se muestra aquí para el parámetro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
     C:\myfolder
    /sourcekey:
    <sourcekey>
    /S
    /Y

### <a name="use-multiple-response-files-to-specify-command-line-parameters"></a>Uso de varios archivos de respuesta para especificar parámetros de línea de comandos

Imaginemos un archivo de respuesta llamado `source.txt` que especifica un contenedor de origen:

    /Source:http://myaccount.blob.core.windows.net/mycontainer

Y un archivo de respuesta llamado `dest.txt` que especifica una carpeta de destino en el sistema de archivos:

    /Dest:C:\myfolder

Y un archivo de respuesta llamado `options.txt` que especifica opciones para AzCopy:

    /S /Y

Para llamar a AzCopy con estos archivos de respuesta, todos los cuales residen en un directorio `C:\responsefiles`, use este comando:

```azcopy
AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   
```

AzCopy procesa este comando como si se incluyeran todos los parámetros individuales en la línea de comandos:

```azcopy
AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y
```

### <a name="specify-a-shared-access-signature-sas"></a>Especificación de una firma de acceso compartido (SAS)

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt
```

También puede especificar una SAS en el identificador URI del contenedor:

```azcopy
AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S
```

### <a name="journal-file-folder"></a>Carpeta de archivo de diario

Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especificó a través de esta opción. Si el archivo de diario no existe en ningún lugar, AzCopy trata la operación como una nueva y genera un nuevo archivo de diario.

Si el archivo de diario existe, AzCopy comprueba si la línea de comandos escrita coincide con la línea de comandos de dicho archivo. Si las dos líneas de comandos coinciden, AzCopy reanudará la operación incompleta. Si no coinciden, se le pregunta si desea sobrescribir el archivo de diario, iniciar una nueva operación o cancelar la operación actual.

Si desea usar la ubicación predeterminada para el archivo de diario:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z
```

Si omite la opción `/Z` o especifica la opción `/Z` sin la ruta de acceso a la carpeta, tal y como se muestra anteriormente, AzCopy crea el archivo de diario en la ubicación predeterminada, que es `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si el archivo de diario ya existe, AzCopy reanuda la operación basándose en dicho archivo.

Si desea especificar una ubicación personalizada para el archivo de diario:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\
```

Este ejemplo crea el archivo de diario si todavía no existe. Si no existe, AzCopy reanuda la operación basándose en el archivo de diario.

Si desea reanudar una operación de AzCopy:

```azcopy
AzCopy /Z:C:\journalfolder\
```

Este ejemplo reanuda la última operación que es posible que no terminara.

### <a name="generate-a-log-file"></a>Generación de un archivo de registro

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V
```

Si especifica la opción `/V` sin proporcionar una ruta de acceso de archivo al registro detallado, AzCopy crea el archivo de registro en la ubicación predeterminada, que es `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

De lo contrario, puede crear un archivo de registro en una ubicación personalizada:

```azcopy
AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log
```

Tenga en cuenta que si especifica una ruta de acceso relativa después de la opción `/V`, como por ejemplo `/V:test/azcopy1.log`, el registro detallado se creará en el directorio de trabajo actual dentro de una subcarpeta llamada `test`.

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificación del número de operaciones simultáneas para iniciar

La opción `/NC` especifica el número de operaciones de copia simultáneas. De manera predeterminada, AzCopy inicia un número determinado de operaciones simultáneas para aumentar el rendimiento de la transferencia de datos. Para las operaciones de tablas, el número de operaciones simultáneas es igual al número de procesadores que se tiene. Para las operaciones de blobs y archivos, el número de operaciones simultáneas es igual a ocho veces el número de procesadores que se tiene. Si ejecuta AzCopy en una red con un ancho de banda bajo, puede especificar un número bajo para /NC con el fin de evitar errores provocados por la competencia de recursos.

### <a name="run-azcopy-against-the-azure-storage-emulator"></a>Ejecución de AzCopy en el emulador de Azure Storage

Puede ejecutar AzCopy en el [emulador de Azure Storage](storage-use-emulator.md) para blobs:

```azcopy
AzCopy /Source:https://127.0.0.1:10000/myaccount/mycontainer/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S
```

También puede ejecutarlo para tablas:

```azcopy
AzCopy /Source:https://127.0.0.1:10002/myaccount/mytable/ /Dest:C:\myfolder /SourceKey:key /SourceType:Table
```

## <a name="azcopy-parameters"></a>Parámetros de AzCopy

A continuación se describen los parámetros para AzCopy. También puede escribir uno de los siguientes comandos desde la línea de comandos para obtener ayuda en el uso de AzCopy:

* Para obtener ayuda detallada sobre la línea de comandos de AzCopy: `AzCopy /?`
* Para obtener ayuda detallada con algún parámetro de AzCopy: `AzCopy /?:SourceKey`
* Para obtener ejemplos de línea de comandos: `AzCopy /?:Samples`

### <a name="sourcesource"></a>/Source:"source"

Especifica los datos de origen desde los que copiar. El origen puede ser un directorio del sistema de archivos, un contenedor de blobs, un directorio virtual de blobs, un recurso compartido de archivos de almacenamiento, un directorio de archivos de almacenamiento o una tabla de Azure.

**Aplicable a:** blobs, archivos, tablas

### <a name="destdestination"></a>/Dest:"destination"

Especifica el destino de la copia. El destino puede ser un directorio del sistema de archivos, un contenedor de blobs, un directorio virtual de blobs, un recurso compartido de archivos de almacenamiento, un directorio de archivos de almacenamiento o una tabla de Azure.

**Aplicable a:** blobs, archivos, tablas

### <a name="patternfile-pattern"></a>/Pattern:"file-pattern"

Especifica un patrón de archivos que indica qué archivos se van a copiar. El comportamiento del parámetro /Pattern viene determinado por la ubicación de los datos de origen y la presencia de la opción del modo recursivo. El modo recursivo viene especificado por la opción /S.

Si el origen especificado es un directorio del sistema de archivos, se aplican los caracteres comodín estándar y el patrón de archivos proporcionado coincidirá con los archivos del directorio. Si se especifica la opción /S, AzCopy también hará coincidir el patrón especificado con todos los archivos de cualquier subcarpeta que se encuentre bajo el directorio.

Si el origen especificado es un contenedor de blobs o un directorio virtual, entonces los caracteres comodín no se aplican. Si se especifica la opción /S, AzCopy interpreta el patrón de archivos especificado como un prefijo de blobs. Si la opción /S no se especifica, AzCopy hará coincidir el patrón de archivos con los nombres de blob exactos.

Si el origen especificado es un recurso compartido de archivos de Azure, debe especificar el nombre de archivo exacto (p. ej. abc.txt) para copiar un solo archivo, o especificar la opción /S para copiar todos los archivos en el recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción /S en conjunto, se produce un error.

AzCopy utiliza la coincidencia entre mayúsculas y minúsculas cuando /Soure es un contenedor de blob o el directorio virtual de blob y utiliza la falta de coincidencia entre mayúsculas y minúsculas en todos los demás casos.

El patrón de archivo predeterminado que se usa cuando no se especifica ninguno es *.* para una ubicación del sistema de archivos, o un prefijo vacío para una ubicación de Almacenamiento de Azure. No se admite la especificación de varios patrones de archivos.

**Aplicable a:** blobs, archivos

### <a name="destkeystorage-key"></a>/DestKey:"storage-key"

Especifica la clave de cuenta de almacenamiento para el recurso de destino.

**Aplicable a:** blobs, archivos, tablas

### <a name="destsassas-token"></a>/DestSAS:"sas-token"

Especifica una firma de acceso compartido (SAS) para los permisos de LECTURA y ESCRITURA del destino (si corresponde). Encierre la SAS con comillas dobles, ya que puede contener caracteres de línea de comandos especiales.

Si el recurso de destino es un contenedor de blobs, un recurso compartido de archivos o tabla, puede especificar esta opción seguida por el token SAS, o bien puede especificar la SAS como parte del URI del contenedor de blob, recurso compartido de archivos o tabla, sin esta opción.

Si el origen y el destino son blobs en ambos casos, el blob de destino debe residir dentro de la misma cuenta de almacenamiento que el blob de origen.

**Aplicable a:** blobs, archivos, tablas

### <a name="sourcekeystorage-key"></a>/SourceKey:"storage-key"

Especifica la clave de cuenta de almacenamiento para el recurso de origen.

**Aplicable a:** blobs, archivos, tablas

### <a name="sourcesassas-token"></a>/SourceSAS:"sas-token"

Especifica una firma de acceso compartido (SAS) para los permisos de LECTURA y LISTA del origen (si corresponde). Encierre la SAS con comillas dobles, ya que puede contener caracteres de línea de comandos especiales.

Si el recurso de origen es un contenedor de blobs y no se proporciona una clave ni una SAS, el contenedor de blobs se lee con acceso anónimo.

Si el origen es un recurso compartido de archivos o una tabla, es necesario proporcionar una clave o una SAS.

**Aplicable a:** blobs, archivos, tablas

### <a name="s"></a>/S

Especifica el modo recursivo para operaciones de copia. En el modo recursivo, AzCopy copia todos los blobs o archivos que coinciden con el patrón de archivos especificado, incluidos los que se encuentran en las subcarpetas.

**Aplicable a:** blobs, archivos

### <a name="blobtypeblock--page--append"></a>/BlobType:"block" | "page" | "append"

Especifica si el blob de destino es un blob en bloques, un blob en páginas o un blob de anexo. Esta opción solo es aplicable cuando se carga un blob. De lo contrario, se producirá un error. Si el destino es un blob y esta opción no se especifica, AzCopy, de forma predeterminada, creará un blob en bloques.

**Aplicable a:** blobs

### <a name="checkmd5"></a>/CheckMD5

Calcula un hash MD5 para datos descargados y comprueba que el hash MD5 almacenado en la propiedad Content-MD5 del blob o archivo coincide con el hash calculado. La comprobación MD5 se desactiva de forma predeterminada, por lo que debe especificar esta opción para realizar dicha comprobación cuando se descargan datos.

Tenga en cuenta que Almacenamiento de Azure no garantiza que el hash MD5 almacenado para el blob o archivo esté actualizado. El cliente será el responsable de actualizar el MD5 siempre que el blob o el archivo se modifique.

AzCopy siempre establece la propiedad Content-MD5 para un blob o archivo de Azure después de cargarlo en el servicio.  

**Aplicable a:** blobs, archivos

### <a name="snapshot"></a>/Snapshot

Indica si se desean transferir instantáneas. Esta opción solo es válida cuando el origen es un blob.

El nombre de las instantáneas del blob transferidas se cambia según este formato: nombre-blog (hora de la instantánea).extensión.

De forma predeterminada, las instantáneas no se copian.

**Aplicable a:** blobs

### <a name="vverbose-log-file"></a>/V:[verbose-log-file]

Envía mensajes con el estado detallado a un archivo de registro.

De forma predeterminada, el nombre del archivo de registro detallado es AzCopyVerbose.log en `%LocalAppData%\Microsoft\Azure\AzCopy`. Si especifica una ubicación existente para el archivo en esta opción, el registro detallado se anexa a dicho archivo.  

**Aplicable a:** blobs, archivos, tablas

### <a name="zjournal-file-folder"></a>/Z:[carpeta de archivos de diario]

Especifica una carpeta de archivos de diario para reanudar una operación.

AzCopy siempre admite la reanudación si una operación se ha interrumpido.

Si esta opción no se especifica o si se especifica sin una ruta de acceso a la carpeta, AzCopy crea el archivo de diario en la ubicación predeterminada, que es %LocalAppData%\Microsoft\Azure\AzCopy.

Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especificó a través de esta opción. Si el archivo de diario no existe en ningún lugar, AzCopy trata la operación como una nueva y genera un nuevo archivo de diario.

Si el archivo de diario existe, AzCopy comprueba si la línea de comandos escrita coincide con la línea de comandos de dicho archivo. Si las dos líneas de comandos coinciden, AzCopy reanudará la operación incompleta. Si no coinciden, se le pregunta si desea sobrescribir el archivo de diario, iniciar una nueva operación o cancelar la operación actual.

El archivo de diario se elimina cuando la operación se completa correctamente.

Tenga en cuenta que la reanudación de una operación a partir de un archivo de diario creado por una versión anterior de AzCopy no se admite.

**Aplicable a:** blobs, archivos, tablas

### <a name="parameter-file"></a>/@:"parameter-file"

Especifica un archivo que contiene parámetros. AzCopy procesa los parámetros del archivo como si hubieran sido especificados en la línea de comandos.

En un archivo de respuesta, puede especificar varios parámetros en una sola línea o especificar cada parámetro en su propia línea. Tenga en cuenta que un parámetro individual no puede ocupar varias líneas.

Los archivos de respuesta pueden incluir líneas de comentario que comiencen con el símbolo #.

Puede especificar varios archivos de respuesta. Sin embargo, tenga en cuenta que AzCopy no admite archivos de respuesta anidados.

**Aplicable a:** blobs, archivos, tablas

### <a name="y"></a>/Y

Suprime todos los mensajes de confirmación de AzCopy.

**Aplicable a:** blobs, archivos, tablas

### <a name="l"></a>/L

Especifica solamente una operación de descripción; no se copian datos.

AzCopy interpreta el uso de esta opción como una simulación para ejecutar la línea de comandos sin esta opción /L y cuenta el número de objetos que se va a copiar; puede especificar la opción /V al mismo tiempo para comprobar qué objetos se copian en el registro detallado.

El comportamiento de esta opción también está determinado por la ubicación de los datos de origen y la presencia de la opción /S del modo recursivo y la opción /Pattern del patrón de archivos.

AzCopy requiere el permiso LISTA y LECTURA de esta ubicación de origen cuando se usa esta opción.

**Aplicable a:** blobs, archivos

### <a name="mt"></a>/MT

Establece la hora de la última modificación del archivo descargado para que coincida con la del blob o archivo de origen.

**Aplicable a:** blobs, archivos

### <a name="xn"></a>/XN

Excluye un recurso origen más nuevo. El recurso no se copia si la hora de la última modificación del origen es la misma o más reciente que el destino.

**Aplicable a:** blobs, archivos

### <a name="xo"></a>/XO
Excluye un recurso de origen más antiguo. El recurso no se copia si la hora de la última modificación del origen es la misma o anterior que el destino.

**Aplicable a:** blobs, archivos

### <a name="a"></a>/A

Carga solamente archivos que tienen el atributo de archivado establecido.

**Aplicable a:** blobs, archivos

### <a name="iarashcnetoi"></a>/IA:[RASHCNETOI]

Carga solamente archivos que tienen cualquiera de los atributos especificados establecido.

Los atributos disponibles son los siguientes:

* R = Archivos de solo lectura
* A = Archivos listos para archivarse
* S = Archivos del sistema
* H = Archivos ocultos
* C = Archivos comprimidos
* N = Archivos normales
* E = Archivos cifrados
* T = Archivos temporales
* O = Archivos fuera de línea
* I = Archivos no indexados

**Aplicable a:** blobs, archivos

### <a name="xarashcnetoi"></a>/XA:[RASHCNETOI]

Excluye archivos que tienen cualquiera de los atributos especificados establecido.

Los atributos disponibles son los siguientes:

* R = Archivos de solo lectura
* A = Archivos listos para archivarse
* S = Archivos del sistema
* H = Archivos ocultos
* C = Archivos comprimidos
* N = Archivos normales
* E = Archivos cifrados
* T = Archivos temporales
* O = Archivos fuera de línea
* I = Archivos no indexados

**Aplicable a:** blobs, archivos

### <a name="delimiterdelimiter"></a>/Delimiter:"delimiter"

Indica el carácter delimitador usado para delimitar directorios virtuales en un nombre de blob.

De forma predeterminada, AzCopy usa / como carácter delimitador. Sin embargo, AzCopy admite el uso de cualquier carácter convencional (como @, # o %) como delimitador. Si necesita incluir uno de estos caracteres especiales en la línea de comandos, encierre el nombre de archivo entre comillas dobles.

Esta opción solamente se aplica para descarga de blobs.

**Aplicable a:** blobs

### <a name="ncnumber-of-concurrent-operations"></a>/NC:"number-of-concurrent-operations"

Especifica el número de operaciones simultáneas.

AzCopy inicia de manera predeterminada un número determinado de operaciones simultáneas para aumentar el rendimiento de la transferencia de datos. Tenga en cuenta que un número elevado de operaciones simultáneas en un entorno con poco ancho de banda puede saturar la conexión de red e impedir que las operaciones se completen. Reduzca el número de operaciones simultáneas basándose en el ancho de banda de red disponible real.

El límite máximo de operaciones simultáneas es de 512.

**Aplicable a:** blobs, archivos, tablas

### <a name="sourcetypeblob--table"></a>/SourceType:"Blob" | "Table"

Especifica que el recurso `source` es un blob disponible en el entorno de desarrollo local, que se ejecuta en el emulador de almacenamiento.

**Aplicable a:** blobs, tablas

### <a name="desttypeblob--table"></a>/DestType:"Blob" | "Table"

Especifica que el recurso `destination` es un blob disponible en el entorno de desarrollo local, que se ejecuta en el emulador de almacenamiento.

**Aplicable a:** blobs, tablas

### <a name="pkrskey1key2key3"></a>/PKRS:"key1#key2#key3#..."

Divide el rango de claves de partición para habilitar la exportación de datos de tablas en paralelo, lo que aumenta la velocidad de la operación de exportación.

Si esta opción no se especifica, AzCopy utiliza un solo subproceso para exportar las entidades de tabla. Por ejemplo, si el usuario especifica /PKRS:"aa#bb", AzCopy inicia tres operaciones simultáneas.

Cada operación exporta uno de los tres rangos de claves de partición, como se muestra a continuación:

  [first-partition-key, aa)

  [aa, bb)

  [bb, last-partition-key]

**Aplicable a:** tablas

### <a name="splitsizefile-size"></a>/SplitSize:"file-size"

Especifica el tamaño dividido del archivo exportado en MB; el valor mínimo permitido es 32.

Si esta opción no se especifica, AzCopy exporta los datos de la tabla a un único archivo.

Si los datos de la tabla se exportan a un blob y el tamaño del archivo exportado alcanza el límite de 200 GB establecido para el tamaño de un blob, AzCopy divide el archivo exportado, incluso si no se ha especificado esta opción.

**Aplicable a:** tablas

### <a name="entityoperationinsertorskip--insertormerge--insertorreplace"></a>/EntityOperation:"InsertOrSkip" | "InsertOrMerge" | "InsertOrReplace"

Especifica el comportamiento de la importación de los datos de la tabla.

* InsertOrSkip: omite una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
* InsertOrMerge: combina una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
* InsertOrReplace: reemplaza una entidad existente o inserta una nueva entidad si esta no existe en la tabla.

**Aplicable a:** tablas

### <a name="manifestmanifest-file"></a>/Manifest:"manifest-file"

Especifica el archivo de manifiesto para la operación de exportación e importación de tablas.

Esta opción es opcional durante la operación de exportación, AzCopy genera un archivo de manifiesto con nombre predefinido si esta opción no se especifica.

Esta opción es necesaria durante la operación de importación para localizar los archivos de datos.

**Aplicable a:** tablas

### <a name="synccopy"></a>/SyncCopy

Indica si se van a copiar archivos o blobs entre dos extremos de Almacenamiento de Azure de forma sincrónica.

De forma predeterminada, AzCopy usa la copia asincrónica del servidor. Especifique esta opción para hacer una copia sincrónica, que se descarga blobs o archivos en la memoria local y, a continuación, los carga en el Almacenamiento de Azure.

Puede usar esta opción al copiar archivos dentro del almacenamiento de blobs o el almacenamiento de archivos o del almacenamiento de blobs al almacenamiento de archivos o viceversa.

**Aplicable a:** blobs, archivos

### <a name="setcontenttypecontent-type"></a>/SetContentType:"content-type"

Especifica el tipo de contenido MIME de los blobs o archivos de destino.

AzCopy establece el tipo de contenido de un blob o archivo en application/octet-stream de forma predeterminada. Puede establecer el tipo de contenido para todos los blobs o archivos especificando explícitamente un valor para esta opción.

Si especifica sin un valor, AzCopy establece cada tipo de contenido de blob o archivo según su extensión de archivo.

**Aplicable a:** blobs, archivos

### <a name="payloadformatjson--csv"></a>/PayloadFormat:"JSON" | "CSV"

Especifica el formato del archivo de datos exportados de tabla.

Si no se especifica esta opción, de forma predeterminada, AzCopy exporta el archivo de datos de tabla en formato JSON.

**Aplicable a:** tablas

## <a name="known-issues-and-best-practices"></a>Problemas conocidos y prácticas recomendadas

Veamos algunos de los problemas conocidos y los procedimientos recomendados.

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitación de escrituras concurrentes mientras se copian datos

Cuando copie blobs o archivos con AzCopy, recuerde que otra aplicación puede estar modificando los datos mientras se copian. Si es posible, asegúrese de que los datos no se modifican durante la operación de copia. Por ejemplo, cuando copie un VHD asociado con una máquina virtual de Azure, asegúrese de que ninguna otra aplicación está escribiendo actualmente en el VHD. Una buena manera de hacerlo es mediante la concesión de los recursos que se van a copiar. Alternativamente, puede crear una instantánea del VHD primero y, después, copiar la instantánea.

Si no puede impedir que otras aplicaciones escriban en blobs o archivos mientras se copian, recuerde que para cuando el trabajo finalice, los recursos copiados puede que ya no tengan una paridad total con los recursos de origen.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Ejecutar una instancia de AzCopy en un solo equipo.

AzCopy está diseñado para maximizar el uso de los recursos del equipo para acelerar la transferencia de datos. Se recomienda ejecutar solo una instancia de AzCopy en un equipo y especificar la opción `/NC` si necesita más operaciones simultáneas. Para obtener más detalles, escriba `AzCopy /?:NC` en la línea de comandos.

### <a name="enable-fips-compliant-md5-algorithms-for-azcopy-when-you-use-fips-compliant-algorithms-for-encryption-hashing-and-signing"></a>Habilitar algoritmos MD5 que cumplan con FIPS para AzCopy cuando "Use algoritmos que cumplen con FIPS para el cifrado, firma y operaciones hash".

AzCopy usa de forma predeterminada la implementación de .NET MD5 para calcular el MD5 al copiar los objetos, pero hay algunos requisitos de seguridad que necesitan AzCopy para habilitar la configuración de MD5 compatible con FIPS.

Puede crear un archivo app.config `AzCopy.exe.config` con la propiedad `AzureStorageUseV1MD5` y separarlo con AzCopy.exe.

    <?xml version="1.0" encoding="utf-8" ?>
    <configuration>
      <appSettings>
        <add key="AzureStorageUseV1MD5" value="false"/>
      </appSettings>
    </configuration>

Respecto de la propiedad "AzureStorageUseV1MD5":

* True (valor predeterminado): AzCopy usa la implementación de MD5 .NET.
* False: AzCopy usa el algoritmo MD5 compatible con FIPS.

Los algoritmos compatibles con FIPS están deshabilitados de manera predeterminada en Windows. Puede cambiar esta configuración de directiva en su máquina. En la ventana Ejecutar (Windows+R), escriba secpol.msc para abrir la ventana **Directiva de seguridad local**. En la ventana **Configuración de seguridad**, vaya a **Configuración de seguridad** > **Directivas locales** > **Opciones de seguridad**. Ubique la directiva **System cryptography: Use FIPS-compliant algorithms for encryption, hashing and signing** (Criptografía de sistema: usar algoritmos compatibles con FIPS para cifrado, hash y firma). Haga doble clic en la directiva para ver el valor en la columna **Configuración de seguridad**.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Azure Storage y AzCopy, consulte los recursos siguientes:

### <a name="azure-storage-documentation"></a>Documentación de Almacenamiento de Azure:
* [Introducción a Almacenamiento de Azure](../storage-introduction.md)
* [Uso del almacenamiento de blobs de .NET](../blobs/storage-dotnet-how-to-use-blobs.md)
* [Uso del almacenamiento de archivos de .NET](../storage-dotnet-how-to-use-files.md)
* [Uso del almacenamiento de tablas de .NET](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [Creación, administración o eliminación de una cuenta de almacenamiento](../storage-create-storage-account.md)
* [Transferencia de datos con AzCopy en Linux](storage-use-azcopy-linux.md)

### <a name="azure-storage-blog-posts"></a>Publicaciones en blobs de Almacenamiento de Azure
* [Introducción a la versión de vista previa de la biblioteca de movimiento de datos de Almacenamiento de Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introducción a la copia sincrónica y el tipo de contenido personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Presentación de la disponibilidad general de AzCopy 3.0 y de la versión de vista previa de AzCopy 4.0 compatible con tablas y archivos](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimizada para escenarios de copia a gran escala](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Compatibilidad para almacenamiento con redundancia geográfica de acceso de lectura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferencia de datos con modo reiniciable y token SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Uso de copia de blobs entre cuentas](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Carga y descarga de archivos para blobs de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)