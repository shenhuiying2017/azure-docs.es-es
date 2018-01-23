---
title: Copia o movimiento de datos a Azure Storage con AzCopy en Linux | Microsoft Docs
description: "Use la utilidad AzCopy en Linux para mover o copiar datos hacia contenido de blobs y archivos o desde ellos. Copie datos a Azure Storage desde archivos locales o copie datos en o entre cuentas de almacenamiento. Migre fácilmente sus datos a Azure Storage."
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
ms.date: 12/11/2017
ms.author: seguler
ms.openlocfilehash: 2fd89684176cd832b656dae8c8f94a6f1ccbbbe8
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/02/2018
---
# <a name="transfer-data-with-azcopy-on-linux"></a>Transferencia de datos con AzCopy en Linux

AzCopy es una utilidad de línea de comandos diseñada para copiar datos a y desde los servicios de Almacenamiento de blobs de Microsoft Azure, File Storage y Table Storage con sencillos comandos diseñados para ofrecer un rendimiento óptimo. Puede copiar datos entre un sistema de archivos y una cuenta de almacenamiento o entre cuentas de almacenamiento.  

Hay dos versiones de AzCopy que puede descargar. AzCopy en Linux se compila con .NET Core Framework, que tiene como destino plataformas Linux que ofrecen opciones de línea de comandos con estilo POSIX. [AzCopy en Windows](../storage-use-azcopy.md) se compila con .NET Framework y ofrece opciones de línea de comandos con estilo Windows. En este artículo se describe AzCopy en Linux.

## <a name="download-and-install-azcopy"></a>Descarga e instalación de AzCopy
### <a name="installation-on-linux"></a>Instalación en Linux

En este artículo se incluyen comandos de varias versiones de Ubuntu.  Use el comando `lsb_release -a` para confirmar el nombre de código y la versión de distribución. 

AzCopy en Linux requiere .NET Core Framework (versión 2.0) en la plataforma. Consulte las instrucciones de instalación en la página [.NET Core](https://www.microsoft.com/net/download/linux).

Como ejemplo, instalemos .NET Core en Ubuntu 16.04. Para la guía de instalación más reciente, visite la página de instalación de [.NET Core en Linux](https://www.microsoft.com/net/download/linux).


```bash
curl https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor > microsoft.gpg
sudo mv microsoft.gpg /etc/apt/trusted.gpg.d/microsoft.gpg
sudo sh -c 'echo "deb [arch=amd64] https://packages.microsoft.com/repos/microsoft-ubuntu-xenial-prod xenial main" > /etc/apt/sources.list.d/dotnetdev.list'
sudo apt-get update
sudo apt-get install dotnet-sdk-2.0.2
```

Una vez que instale .NET Core, descargue e instale AzCopy.

```bash
wget -O azcopy.tar.gz https://aka.ms/downloadazcopyprlinux
tar -xf azcopy.tar.gz
sudo ./install.sh
```

Puede quitar los archivos extraídos una vez que se instale AzCopy en Linux. También, si no tiene privilegios de superusuario, también puede ejecutar AzCopy con el script de shell "azcopy" en la carpeta extraída. 


## <a name="writing-your-first-azcopy-command"></a>Escritura del primer comando de AzCopy
La sintaxis básica del comando AzCopy es:

```azcopy
azcopy --source <source> --destination <destination> [Options]
```

Los ejemplos siguientes demuestran diversos escenarios para copiar datos a los blobs y archivos de Microsoft Azure y desde estos. Consulte el menú `azcopy --help` para una explicación detallada de los parámetros que se usan en cada ejemplo.

## <a name="blob-download"></a>Blob: descarga
### <a name="download-single-blob"></a>Descarga de un solo blob

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key> 
```

Si la carpeta `/mnt/myfiles` no existe, AzCopy la crea y descarga `abc.txt ` a la carpeta nueva. 

### <a name="download-single-blob-from-secondary-region"></a>Descarga de un solo blob desde la región secundaria

```azcopy
azcopy \
    --source https://myaccount-secondary.blob.core.windows.net/mynewcontainer/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Tenga en cuenta que debe tener almacenamiento con redundancia geográfica con acceso de lectura habilitado.

### <a name="download-all-blobs"></a>Descarga de todos los blobs

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Supongamos que los siguientes blobs residen en el contenedor especificado:  

```
abc.txt
abc1.txt
abc2.txt
vd1/a.txt
vd1/abcd.txt
```

Después de la operación de descarga, el directorio `/mnt/myfiles` incluye los archivos siguientes:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/vd1/a.txt
/mnt/myfiles/vd1/abcd.txt
```

Si no especifica la opción `--recursive`, no se descargará ningún blob.

### <a name="download-blobs-with-specified-prefix"></a>Descarga de blobs con el prefijo especificado

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --include "a" \
    --recursive
```

Supongamos que los siguientes blobs residen en el contenedor especificado. Se descargarán todos los blobs que comiencen con el prefijo `a`.

```
abc.txt
abc1.txt
abc2.txt
xyz.txt
vd1\a.txt
vd1\abcd.txt
```

Después de la operación de descarga, la carpeta `/mnt/myfiles` incluye los archivos siguientes:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
```

El prefijo se aplica al directorio virtual, que forma la primera parte del nombre del blob. En el ejemplo mostrado anteriormente, el directorio virtual no coincide con el prefijo especificado, por lo que no se descarga ningún blob. Además, si no se especifica la opción `--recursive`, AzCopy no descarga ningún blob.

### <a name="set-the-last-modified-time-of-exported-files-to-be-same-as-the-source-blobs"></a>Establecimiento de la hora de la última modificación de los archivos exportados para que coincida con la de los blobs de origen

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination "/mnt/myfiles" \
    --source-key <key> \
    --preserve-last-modified-time
```

También puede excluir los blobs de la operación de descarga basándose en la hora de su última modificación. Por ejemplo, si desea excluir los blobs cuya hora de la última modificación es igual o más reciente que la del archivo de destino, agregue la opción `--exclude-newer` :

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-newer
```

O, si desea excluir los blobs cuya hora de la última modificación es igual o anterior a la del archivo de destino, agregue la opción `--exclude-older` :

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer \
    --destination /mnt/myfiles \
    --source-key <key> \
    --preserve-last-modified-time \
    --exclude-older
```

## <a name="blob-upload"></a>Blob: carga
### <a name="upload-single-file"></a>Carga de un solo archivo

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/abc.txt \
    --dest-key <key>
```

Si el contenedor de destino especificado no existe, AzCopy lo crea y carga el archivo en él.

### <a name="upload-single-file-to-virtual-directory"></a>Carga de un solo archivo en el directorio virtual

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer/vd/abc.txt \
    --dest-key <key>
```

Si el directorio virtual especificado no existe, AzCopy carga el archivo para incluir el directorio virtual en el nombre del blob (*por ejemplo*, `vd/abc.txt` en el ejemplo anterior).

### <a name="upload-all-files"></a>Carga de todos los archivos

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --recursive
```

Al especificar la opción `--recursive` se carga el contenido del directorio especificado en Blob Storage de forma recursiva, lo que significa que todas las subcarpetas y sus archivos también se cargan. Por ejemplo, supongamos que los siguientes archivos residen en la carpeta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Después de la operación de carga, el contenedor incluye los archivos siguientes:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Cuando la opción `--recursive` no se especifica, solo se cargan los tres archivos siguientes:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="upload-files-matching-specified-pattern"></a>Carga de archivos que coincidan con el patrón especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --include "a*" \
    --recursive
```

Supongamos que los siguientes archivos residen en la carpeta `/mnt/myfiles`:

```
/mnt/myfiles/abc.txt
/mnt/myfiles/abc1.txt
/mnt/myfiles/abc2.txt
/mnt/myfiles/xyz.txt
/mnt/myfiles/subfolder/a.txt
/mnt/myfiles/subfolder/abcd.txt
```

Después de la operación de carga, el contenedor incluye los archivos siguientes:

```
abc.txt
abc1.txt
abc2.txt
subfolder/a.txt
subfolder/abcd.txt
```

Cuando no se especifica la opción `--recursive`, AzCopy omite los archivos que se encuentran en subdirectorios:

```
abc.txt
abc1.txt
abc2.txt
```

### <a name="specify-the-mime-content-type-of-a-destination-blob"></a>Especificación del tipo de contenido MIME de un blob de destino
De forma predeterminada, AzCopy define el tipo de contenido de un blob de destino como `application/octet-stream`. Sin embargo, puede especificar explícitamente el tipo de contenido a través de la opción `--set-content-type [content-type]`. Esta sintaxis establece el tipo de contenido para todos los blobs de una operación de carga.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type "video/mp4"
```

Si la opción `--set-content-type` se especifica sin un valor, AzCopy establece el tipo de contenido de cada blob o archivo según su extensión de archivo.

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/myContainer/ \
    --dest-key <key> \
    --include "ab" \
    --set-content-type
```

## <a name="blob-copy"></a>Blob: copia
### <a name="copy-single-blob-within-storage-account"></a>Copia de un solo blob dentro de una cuenta de almacenamiento

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key> \
    --dest-key <key>
```

Cuando copia un blob sin la opción --syn-copy, se realiza una operación de [copia de lado servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-single-blob-across-storage-accounts"></a>Copia de un solo blob entre cuentas de almacenamiento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Cuando copia un blob sin la opción --syn-copy, se realiza una operación de [copia de lado servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-single-blob-from-secondary-region-to-primary-region"></a>Copia de un solo blob desde la región secundaria a la región primaria

```azcopy
azcopy \
    --source https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1/abc.txt \
    --destination https://myaccount2.blob.core.windows.net/mynewcontainer2/abc.txt \
    --source-key <key1> \
    --dest-key <key2>
```

Tenga en cuenta que debe tener almacenamiento con redundancia geográfica con acceso de lectura habilitado.

### <a name="copy-single-blob-and-its-snapshots-across-storage-accounts"></a>Copia de un solo blob y de sus instantáneas entre cuentas de almacenamiento

```azcopy
azcopy \
    --source https://sourceaccount.blob.core.windows.net/mycontainer1/ \
    --destination https://destaccount.blob.core.windows.net/mycontainer2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "abc.txt" \
    --include-snapshot
```

Después de la operación de copia, el contenedor de destino incluye el blob y sus instantáneas. El contenedor incluye el siguiente blob y sus instantáneas:

```
abc.txt
abc (2013-02-25 080757).txt
abc (2014-02-21 150331).txt
```

### <a name="synchronously-copy-blobs-across-storage-accounts"></a>Copia sincrónica de blobs entre cuentas de almacenamiento.
De forma predeterminada, AzCopy copia datos entre dos extremos de almacenamiento asincrónicamente. Por lo tanto, la operación de copia se ejecuta en segundo plano con una capacidad de ancho de banda de reserva que no tiene contrato de nivel de servicio en términos de la rapidez con que se copia un blob. 

La opción `--sync-copy` garantiza que la operación de copia tiene una velocidad uniforme. Para hacer la copia sincrónica, AzCopy descarga los blobs que se deben copiar desde el origen especificado a la memoria local y, a continuación, los carga en el destino de almacenamiento de blobs.

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/myContainer/ \
    --destination https://myaccount2.blob.core.windows.net/myContainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --include "ab" \
    --sync-copy
```

`--sync-copy` podría generar un costo de salida adicional en comparación con la copia asincrónica. El enfoque recomendado es usar esta opción en una máquina virtual de Azure que se encuentre en la misma región que la cuenta de almacenamiento de origen, para evitar el costo de salida.

## <a name="file-download"></a>Archivo: descarga
### <a name="download-single-file"></a>Descarga de un solo archivo

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/myfolder1/abc.txt \
    --destination /mnt/myfiles/abc.txt \
    --source-key <key>
```

Si el origen especificado es un recurso compartido de archivos de Azure, entonces tiene que especificar el nombre de archivo exacto (*p. ej.* `abc.txt`) para descargar un solo archivo, o especificar la opción `--recursive` para descargar todos los archivos en el recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción `--recursive` en conjunto, se produce un error.

### <a name="download-all-files"></a>Descarga de todos los archivos

```azcopy
azcopy \
    --source https://myaccount.file.core.windows.net/myfileshare/ \
    --destination /mnt/myfiles \
    --source-key <key> \
    --recursive
```

Tenga en cuenta que no se descarga ninguna carpeta vacía.

## <a name="file-upload"></a>Archivo: carga
### <a name="upload-single-file"></a>Carga de un solo archivo

```azcopy
azcopy \
    --source /mnt/myfiles/abc.txt \
    --destination https://myaccount.file.core.windows.net/myfileshare/abc.txt \
    --dest-key <key>
```

### <a name="upload-all-files"></a>Carga de todos los archivos

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --recursive
```

Tenga en cuenta que no se carga ninguna carpeta vacía.

### <a name="upload-files-matching-specified-pattern"></a>Carga de archivos que coincidan con el patrón especificado

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.file.core.windows.net/myfileshare/ \
    --dest-key <key> \
    --include "ab*" \
    --recursive
```

## <a name="file-copy"></a>Archivo: copia
### <a name="copy-across-file-shares"></a>Copia a través de recursos compartidos de archivos

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Al copiar un archivo en distintos recursos compartidos de archivos, se realiza una operación de [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-file-share-to-blob"></a>Copia desde el recurso compartido de archivos al blob

```azcopy
azcopy \ 
    --source https://myaccount1.file.core.windows.net/myfileshare/ \
    --destination https://myaccount2.blob.core.windows.net/mycontainer/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Al copiar un archivo desde un recurso compartido de archivos en un blob, se realiza una [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="copy-from-blob-to-file-share"></a>Copia desde el blob al recurso compartido de archivos

```azcopy
azcopy \
    --source https://myaccount1.blob.core.windows.net/mycontainer/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive
```
Al copiar un archivo desde un blob en un recurso compartido de archivos, se realiza una operación de [copia del lado del servidor](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

### <a name="synchronously-copy-files"></a>Copia sincrónica de archivos
Puede especificar la opción `--sync-copy` para copiar datos de File Storage a File Storage, de File Storage a Blob Storage y de Blob Storage a File Storage de manera sincrónica. AzCopy ejecuta esta operación mediante la descarga de los datos de origen a la memoria local y, luego, su carga en el destino. En este caso, se aplica el costo de salida estándar.

```azcopy
azcopy \
    --source https://myaccount1.file.core.windows.net/myfileshare1/ \
    --destination https://myaccount2.file.core.windows.net/myfileshare2/ \
    --source-key <key1> \
    --dest-key <key2> \
    --recursive \
    --sync-copy
```

Al realizar la copia de File Storage a Blob Storage, el tipo de blob predeterminado es el blob en bloques, por lo tanto, el usuario puede especificar la opción `--blob-type page` para cambiar el tipo de blob de destino. Los tipos disponibles son `page | block | append`.

Tenga en cuenta que `--sync-copy` podría generar un costo de salida adicional en comparación con la copia asincrónica. El enfoque recomendado es usar esta opción en una máquina virtual de Azure que se encuentre en la misma región que la cuenta de almacenamiento de origen, para evitar el costo de salida.

## <a name="other-azcopy-features"></a>Otras características de AzCopy
### <a name="only-copy-data-that-doesnt-exist-in-the-destination"></a>Solo copie los datos que no existan en el destino
Los parámetros `--exclude-older` y `--exclude-newer` le permiten excluir los recursos de origen anteriores o posteriores a la copia, respectivamente. Si solo desea copiar los recursos de origen que no existen en el destino, puede especificar ambos parámetros en el comando de AzCopy:

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --exclude-older --exclude-newer

    --source /mnt/myfiles --destination http://myaccount.file.core.windows.net/myfileshare --dest-key <destkey> --recursive --exclude-older --exclude-newer

    --source http://myaccount.blob.core.windows.net/mycontainer --destination http://myaccount.blob.core.windows.net/mycontainer1 --source-key <sourcekey> --dest-key <destkey> --recursive --exclude-older --exclude-newer

### <a name="use-a-configuration-file-to-specify-command-line-parameters"></a>Uso de un archivo de configuración para especificar parámetros de línea de comandos

```azcopy
azcopy --config-file "azcopy-config.ini"
```

Puede incluir cualquier parámetro de la línea de comandos de AzCopy en un archivo de configuración. AzCopy procesa los parámetros del archivo como si hubieran sido especificados en la línea de comandos, realizando una sustitución directa con el contenido del archivo.

Imaginemos un archivo de configuración denominado `copyoperation`, que contiene las siguientes líneas. Cada parámetro de AzCopy se puede especificar en una sola línea.

    --source http://myaccount.blob.core.windows.net/mycontainer --destination /mnt/myfiles --source-key <sourcekey> --recursive --quiet

o bien, en líneas independientes:

    --source http://myaccount.blob.core.windows.net/mycontainer
    --destination /mnt/myfiles
    --source-key<sourcekey>
    --recursive
    --quiet

AzCopy genera un error si divide el parámetro en dos líneas, tal como se muestra aquí para el parámetro `--source-key`:

    http://myaccount.blob.core.windows.net/mycontainer
    /mnt/myfiles
    --sourcekey
    <sourcekey>
    --recursive
    --quiet

### <a name="specify-a-shared-access-signature-sas"></a>Especificación de una firma de acceso compartido (SAS)

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/abc.txt \
    --destination https://myaccount.blob.core.windows.net/mycontainer2/abc.txt \
    --source-sas <SAS1> \
    --dest-sas <SAS2>
```

También puede especificar una SAS en el identificador URI del contenedor:

```azcopy
azcopy \
    --source https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken \
    --destination /mnt/myfiles \
    --recursive
```

### <a name="journal-file-folder"></a>Carpeta de archivo de diario
Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especificó a través de esta opción. Si el archivo de diario no existe en ningún lugar, AzCopy trata la operación como una nueva y genera un nuevo archivo de diario.

Si el archivo de diario existe, AzCopy comprueba si la línea de comandos escrita coincide con la línea de comandos de dicho archivo. Si las dos líneas de comandos coinciden, AzCopy reanudará la operación incompleta. Si no coinciden, AzCopy le pide al usuario que sobrescriba el archivo de diario para iniciar una operación nueva o que cancele la operación actual.

Si desea usar la ubicación predeterminada para el archivo de diario:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --resume
```

Si omite la opción `--resume` o especifica la opción `--resume` sin la ruta de acceso a la carpeta, tal y como se muestra anteriormente, AzCopy crea el archivo de diario en la ubicación predeterminada, que es `~\Microsoft\Azure\AzCopy`. Si el archivo de diario ya existe, AzCopy reanuda la operación basándose en dicho archivo.

Si desea especificar una ubicación personalizada para el archivo de diario:

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key key \
    --resume "/mnt/myjournal"
```

Este ejemplo crea el archivo de diario si todavía no existe. Si no existe, AzCopy reanuda la operación basándose en el archivo de diario.

Si desea reanudar una operación de AzCopy, repita el mismo comando. AzCopy en Linux le pedirá confirmación:

```azcopy
Incomplete operation with same command line detected at the journal directory "/home/myaccount/Microsoft/Azure/AzCopy", do you want to resume the operation? Choose Yes to resume, choose No to overwrite the journal to start a new operation. (Yes/No)
```

### <a name="output-verbose-logs"></a>Registros detallados de salida

```azcopy
azcopy \
    --source /mnt/myfiles \
    --destination https://myaccount.blob.core.windows.net/mycontainer \
    --dest-key <key> \
    --verbose
```

### <a name="specify-the-number-of-concurrent-operations-to-start"></a>Especificación del número de operaciones simultáneas para iniciar
La opción `--parallel-level` especifica el número de operaciones de copia simultáneas. De manera predeterminada, AzCopy inicia un número determinado de operaciones simultáneas para aumentar el rendimiento de la transferencia de datos. El número de operaciones simultáneas equivale a ocho veces el número de procesadores que posee. Si ejecuta AzCopy en una red de ancho de banda bajo, puede especificar un número inferior para --parallel-level con el fin de evitar errores provocados por la competencia de recursos.

>[!TIP]
>Para ver la lista completa de parámetros de AzCopy, compruebe el menú "azcopy --help".

## <a name="known-issues-and-best-practices"></a>Problemas conocidos y procedimientos recomendados
### <a name="error-net-sdk-20-is-not-found-in-the-system"></a>Error: .NET SDK 2.0 no se encuentra en el sistema.
AzCopy depende de .NET SDK 2.0 a partir de la versión AzCopy 7.0. Antes de esta versión, AzCopy usaba .NET Core 1.1. Si se produce un error que indica que .NET Core 2.0 no está instalado en el sistema, es posible que deba realizar la instalación o la actualización mediante las [instrucciones de instalación de .NET Core](https://www.microsoft.com/net/learn/get-started/linuxredhat).

### <a name="error-installing-azcopy"></a>Error al instalar AzCopy
Si encuentra problemas para instalar AzCopy, puede intentar ejecutar AzCopy con el script de Bash en la carpeta `azcopy` extraída.

```bash
cd azcopy
./azcopy
```

### <a name="limit-concurrent-writes-while-copying-data"></a>Limitación de escrituras concurrentes mientras se copian datos
Cuando copie blobs o archivos con AzCopy, recuerde que otra aplicación puede estar modificando los datos mientras se copian. Si es posible, asegúrese de que los datos no se modifican durante la operación de copia. Por ejemplo, cuando copie un VHD asociado con una máquina virtual de Azure, asegúrese de que ninguna otra aplicación está escribiendo actualmente en el VHD. Una buena manera de hacerlo es mediante la concesión de los recursos que se van a copiar. Alternativamente, puede crear una instantánea del VHD primero y, después, copiar la instantánea.

Si no puede impedir que otras aplicaciones escriban en blobs o archivos mientras se copian, recuerde que para cuando el trabajo finalice, los recursos copiados puede que ya no tengan una paridad total con los recursos de origen.

### <a name="run-one-azcopy-instance-on-one-machine"></a>Ejecutar una instancia de AzCopy en un solo equipo.
AzCopy está diseñado para maximizar el uso de los recursos del equipo para acelerar la transferencia de datos. Se recomienda ejecutar solo una instancia de AzCopy en un equipo y especificar la opción `--parallel-level` si necesita más operaciones simultáneas. Para obtener más detalles, escriba `AzCopy --help parallel-level` en la línea de comandos.

## <a name="next-steps"></a>pasos siguientes
Para más información sobre Azure Storage y AzCopy, consulte los recursos siguientes:

### <a name="azure-storage-documentation"></a>Documentación de Azure Storage:
* [Introducción a Azure Storage](../storage-introduction.md)
* [crear una cuenta de almacenamiento](../storage-create-storage-account.md)
* [Administración de blobs con el Explorador de Storage](https://docs.microsoft.com/azure/vs-azure-tools-storage-explorer-blobs)
* [Uso de la CLI de Azure 2.0 con Azure Storage](../storage-azure-cli.md)
* [Uso del almacenamiento de blobs en C++](../blobs/storage-c-plus-plus-how-to-use-blobs.md)
* [Uso de Blob Storage en Java](../blobs/storage-java-how-to-use-blob-storage.md)
* [Uso de Blob Storage en Node.js](../blobs/storage-nodejs-how-to-use-blob-storage.md)
* [Uso del almacenamiento de blobs de Python](../blobs/storage-python-how-to-use-blob-storage.md)

### <a name="azure-storage-blog-posts"></a>Publicaciones en blobs de Azure Storage
* [Anuncio de AzCopy en Linux (versión preliminar)](https://azure.microsoft.com/en-in/blog/announcing-azcopy-on-linux-preview/)
* [Introducción a la versión de vista previa de la biblioteca de movimiento de datos de Azure Storage](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/)
* [AzCopy: Introducción a la copia sincrónica y el tipo de contenido personalizado](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/01/13/azcopy-introducing-synchronous-copy-and-customized-content-type.aspx)
* [AzCopy: Presentación de la disponibilidad general de AzCopy 3.0 y de la versión de vista previa de AzCopy 4.0 compatible con tablas y archivos](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
* [AzCopy: Optimizada para escenarios de copia a gran escala](http://go.microsoft.com/fwlink/?LinkId=507682)
* [AzCopy: Compatibilidad para almacenamiento con redundancia geográfica de acceso de lectura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
* [AzCopy: Transferencia de datos con modo reiniciable y token SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
* [AzCopy: Uso de copia de blobs entre cuentas](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
* [AzCopy: Carga y descarga de archivos para blobs de Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)

