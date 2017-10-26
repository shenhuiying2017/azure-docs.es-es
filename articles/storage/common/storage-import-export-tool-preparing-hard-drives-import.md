---
title: "Preparación de unidades de disco duro para un trabajo de importación de Azure Import/Export | Microsoft Docs"
description: "Aprenda a preparar unidades de disco duro mediante la herramienta WAImportExport para crear un trabajo de importación para el servicio Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: muralikk
ms.openlocfilehash: 2854822907e818297c8d2f74cab48b0afa0d646c
ms.sourcegitcommit: b723436807176e17e54f226fe00e7e977aba36d5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2017
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparación de unidades de disco duro para un trabajo de importación

La herramienta WAImportExport es la herramienta de preparación y reparación de unidades de disco duro que se puede usar con el [servicio Microsoft Azure Import/Export](../storage-import-export-service.md). Puede usar esta herramienta para copiar los datos en las unidades de disco duro que se van a enviar a un centro de datos de Azure. Cuando haya finalizado un trabajo de importación, puede usar esta herramienta para reparar los blobs que se hayan dañado, no se hayan encontrado o hayan entrado en conflicto con otros blobs. Después de recibir las unidades de un trabajo de exportación completado, puede utilizar esta herramienta para reparar los archivos dañados o no encontrados de las unidades. En este artículo, se explica el uso de esta herramienta.

## <a name="prerequisites"></a>Requisitos previos

### <a name="requirements-for-waimportexportexe"></a>Requisitos para WAImportExport.exe

- **Configuración de la máquina**
  - Windows 7, Windows Server 2008 R2 o un sistema operativo de Windows más reciente
  - Se debe instalar .NET framework 4. Consulte las [preguntas más frecuentes](#faq) sobre cómo comprobar si .Net Framework está instalado en la máquina.
- **Clave de la cuenta de almacenamiento**: necesita al menos una de las claves de cuenta para la cuenta de almacenamiento.

### <a name="preparing-disk-for-import-job"></a>Preparación del disco para el trabajo de importación

- **BitLocker:** se debe habilitar BitLocker en la máquina donde se ejecuta la herramienta WAImportExport. Consulte las [preguntas más frecuentes](#faq) sobre cómo habilitar BitLocker.
- **Discos** accesibles desde la máquina en la que se ejecuta la herramienta WAImportExport. Consulte las [preguntas más frecuentes](#faq) sobre la especificación del disco.
- **Archivos de origen**: se debe poder acceder a los archivos que se van a importar desde la máquina de copia, independientemente de si están en un recurso compartido de red o en una unidad de disco duro local.

### <a name="repairing-a-partially-failed-import-job"></a>Reparación de un trabajo de importación con errores

- **Copie el archivo de registro** que se genera cuando el servicio Azure Import/Export copia los datos entre la cuenta de almacenamiento y el disco. Se encuentra en la cuenta de almacenamiento de destino.

### <a name="repairing-a-partially-failed-export-job"></a>Reparación de un trabajo de exportación con errores

- **Copie el archivo de registro** que se genera cuando el servicio Azure Import/Export copia los datos entre la cuenta de almacenamiento y el disco. Se encuentra en la cuenta de almacenamiento de origen.
- **Archivo de manifiesto**: [opcional] se encuentra en la unidad exportada que devolvió Microsoft.

## <a name="download-and-install-waimportexport"></a>Descarga e instalación de WAImportExport

Descargue la [versión más reciente de WAImportExport.exe](https://www.microsoft.com/download/details.aspx?id=55280). Extraiga el contenido comprimido en un directorio del equipo.

La siguiente tarea consiste en crear archivos CSV.

## <a name="prepare-the-dataset-csv-file"></a>Preparación del archivo CSV de conjunto de datos

### <a name="what-is-dataset-csv"></a>¿Qué es un archivo CSV de conjunto de datos?

Un archivo CSV de conjunto de datos es el valor de la marca /dataset, un archivo CSV que contiene la lista de directorios o archivos que se van a copiar en las unidades de destino. El primer paso para crear un trabajo de importación es determinar los directorios y archivos que se van a importar. Puede tratarse de una lista de directorios, una lista de archivos únicos o una combinación de ambos. Si se incluye un directorio, todos los archivos del directorio y sus subdirectorios formarán parte del trabajo de importación.

Para cada directorio o archivo que se va a importar, debe identificar un directorio virtual de destino o un blob en Azure Blob service. Usará estos destinos como entradas de la herramienta WAImportExport. Los directorios deben delimitarse con el carácter de barra diagonal "/".

En la tabla siguiente se muestran algunos ejemplos de destinos de blob:

| Archivo o directorio de origen | Blob de destino o directorio virtual |
| --- | --- |
| H:\Video | https://mystorageaccount.blob.core.windows.net/video |
| H:\Photo | https://mystorageaccount.blob.core.windows.net/photo |
| K:\Temp\FavoriteVideo.ISO | https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO |
| \\myshare\john\music | https://mystorageaccount.blob.core.windows.net/music |

### <a name="sample-datasetcsv"></a>Dataset.csv de ejemplo

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
"F:\50M_original\100M_1.csv.txt","containername/100M_1.csv.txt",BlockBlob,rename,"None",None
"F:\50M_original\","containername/",BlockBlob,rename,"None",None
```

### <a name="dataset-csv-file-fields"></a>Campos del archivo CSV de conjunto de datos

| Campo | Descripción |
| --- | --- |
| BasePath | **[Obligatorio]**<br/>El valor de este parámetro representa el origen donde se encuentran los datos que desea importar. La herramienta copiará de forma recursiva todos los datos que se encuentran en esta ruta de acceso.<br><br/>**Valores permitidos**: estos deben constituir una ruta de acceso válida en el equipo local o una ruta de acceso a un recurso compartido válida, que sean accesibles para el usuario. La ruta del directorio debe ser una ruta absoluta (no una ruta de acceso relativa). Si la ruta de acceso finaliza en "\\", representa un directorio; por otro lado, una ruta de acceso que no termine en "\\" representa un archivo.<br/>No se permite ninguna expresión regular en este campo. Si la ruta de acceso contiene espacios, póngala entre "".<br><br/>**Ejemplo**: "c:\Directory\c\Directory\File.txt"<br>"\\\\FBaseFilesharePath.domain.net\sharename\directory\"  |
| DstBlobPathOrPrefix | **[Obligatorio]**<br/> La ruta de acceso al directorio virtual de destino en la cuenta de Windows Azure Storage. El directorio virtual puede existir ya o no. Si no existe, el servicio Import/Export creará uno.<br/><br/>Asegúrese de utilizar nombres de contenedor válidos al especificar los directorios virtuales de destino o blobs. Tenga en cuenta que los nombres de contenedor deben estar en minúsculas. Para más información sobre las reglas de nomenclatura de contenedores, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata). Si solo se especifica la raíz, la estructura de directorios del origen se replica en el contenedor de blob de destino. Si se desea una estructura de directorios diferente que la del origen, se necesitan varias filas de asignación en el archivo CSV.<br/><br/>Puede especificar un contenedor o un prefijo de blob como music/70s/. El directorio de destino debe comenzar con el nombre del contenedor, seguido por una barra diagonal "/" y puede incluir opcionalmente un directorio virtual de blobs que termina por "/".<br/><br/>Si el contenedor de destino es el contenedor raíz, debe especificar de forma explícita el contenedor raíz, incluida la barra diagonal, como $root/. Puesto que los blobs del contenedor raíz no pueden incluir "/" en sus nombres, los subdirectorios del directorio de origen no se copiarán si el directorio de destino es el contenedor raíz.<br/><br/>**Ejemplo**<br/>Si la ruta de acceso del blob de destino es https://mystorageaccount.blob.core.windows.net/video, el valor de este campo puede ser video/  |
| BlobType | **[Opcional]** block &amp;#124; page<br/>Actualmente el servicio Import/Export admite 2 tipos de blobs. Blobs en páginas y blobs en bloques. De forma predeterminada, todos los archivos se importarán como blobs en bloque. Y \*.vhd y \*.vhdx se importarán como blobs en páginas. Hay un límite para el tamaño permitido de los blobs en bloque y los blobs en página. Consulte [Objetivos de escalabilidad de Storage](storage-scalability-targets.md) para más información.  |
| Disposition | **[Opcional]**  rename | no-overwrite | overwrite <br/> Este campo especifica el comportamiento de copia solo durante la importación, es decir , cuando se están cargando datos en la cuenta de almacenamiento desde el disco. Las opciones disponibles son: rename&#124;overwite&#124;no-overwrite. El valor predeterminado es "rename" si no se especifica nada. <br/><br/>**Rename**: si hay un objeto con el mismo nombre, crea una copia en el destino.<br/>Overwrite: sobrescribe el archivo con el archivo más reciente. El archivo con la modificación más reciente gana.<br/>**No-overwrite**: omite la sobrescritura del archivo si ya está presente.|
| MetadataFile | **[Opcional]** <br/>El valor de este campo es el archivo de metadatos que se puede proporcionar si es necesario mantener los metadatos de los objetos o proporcionar metadatos personalizados. Ruta de acceso al archivo de metadatos para los blobs de destino. Para obtener más información, consulte [Formato de archivo de propiedades y metadatos del servicio Import/Export](../storage-import-export-file-format-metadata-and-properties.md). |
| PropertiesFile | **[Opcional]** <br/>Ruta de acceso al archivo de propiedades para los blobs de destino. Para obtener más información, consulte [Formato de archivo de propiedades y metadatos del servicio Import/Export](../storage-import-export-file-format-metadata-and-properties.md). |

## <a name="prepare-initialdriveset-or-additionaldriveset-csv-file"></a>Preparación del archivo CSV InitialDriveSet o AdditionalDriveSet

### <a name="what-is-driveset-csv"></a>¿Qué es el archivo CSV de conjunto de unidades?

El valor de la marca /InitialDriveSet o /AdditionalDriveSet es un archivo CSV que contiene la lista de discos a los que se asignan letras de unidad para que la herramienta pueda elegir correctamente la lista de discos que se van a preparar. Si el tamaño de los datos es mayor que el de un solo disco, la herramienta WAImportExport distribuirá los datos entre varios discos de los que aparecen en este archivo CSV de forma optimizada.

No hay ningún límite en el número de discos en los que se pueden escribir los datos en una sola sesión. La herramienta distribuirá los datos basándose en el tamaño del disco y de la carpeta. Seleccionará el disco más optimizado para el tamaño del objeto. Al cargar los datos en la cuenta de almacenamiento, estos convergen de nuevo en la estructura de directorios que se especificó en el archivo de conjunto de datos. Para crear un archivo CSV de conjunto de unidades, siga los pasos descritos a continuación.

### <a name="create-basic-volume-and-assign-drive-letter"></a>Creación de volumen básico y asignación de letras de unidad

Cree un volumen básico y asigne una letra de unidad siguiendo las instrucciones descritas en "Creación de particiones más sencilla" en [Overview of Disk Management](https://technet.microsoft.com/library/cc754936.aspx) (Información general sobre administración de discos).

### <a name="sample-initialdriveset-and-additionaldriveset-csv-file"></a>Archivos CSV InitialDriveSet o AdditionalDriveSet de ejemplo

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
G,AlreadyFormatted,SilentMode,AlreadyEncrypted,060456-014509-132033-080300-252615-584177-672089-411631
H,Format,SilentMode,Encrypt,
```

### <a name="driveset-csv-file-fields"></a>Campos del archivo CSV de conjunto de unidades

| Fields | Valor |
| --- | --- |
| DriveLetter | **[Obligatorio]**<br/> Cada unidad que se proporciona a la herramienta como destino debe tener un volumen NTFS simple en ella y una letra de unidad asignada.<br/> <br/>**Ejemplo**: R o r |
| FormatOption | **[Obligatorio]**  Format | AlreadyFormatted<br/><br/> **Format**: si se especifica esto formateará todos los datos del disco. <br/>**AlreadyFormatted**: la herramienta omitirá el formateo si se especifica este valor. |
| SilentOrPromptOnFormat | **[Obligatorio]**  SilentMode | PromptOnFormat<br/><br/>**SilentMode**: si proporciona este valor permitirá al usuario ejecutar la herramienta en modo silencioso. <br/>**PromptOnFormat**: la herramienta pedirá al usuario que confirme si la acción es realmente intencionada en cada formato.<br/><br/>Si no se establece, el comando se anulará y mostrará el mensaje de error: "Incorrect value for SilentOrPromptOnFormat: none" (Valor incorrecto para SilentOrPromptOnFormat: ninguno) |
| Cifrado | **[Obligatorio]**  Encrypt | AlreadyEncrypted<br/> El valor de este campo decide qué disco cifrar y cuáles no. <br/><br/>**Encrypt**: la herramienta formateará la unidad. Si el valor del campo "FormatOption" es "Format", este valor debe ser obligatoriamente "Encrypt". Si se especifica "AlreadyEncrypted" en este caso, se producirá un error "When Format is specified, Encrypt must also be specified" (Si se especifica Format, también se debe especificar Encrypt).<br/>**AlreadyEncrypted**: la herramienta descifrará la unidad con la clave BitLockerKey proporcionada en el campo "ExistingBitLockerKey". Si el valor del campo "FormatOption" es "AlreadyFormatted", este valor podrá ser "Encrypt" o "AlreadyEncrypted" |
| ExistingBitLockerKey | **[Obligatorio]**  Si el valor del campo "Encryption" es "AlreadyEncrypted"<br/> El valor de este campo es la clave de BitLocker que está asociada al disco concreto. <br/><br/>Este campo debe dejarse en blanco si el valor del campo "Encryption" es "Encrypt".  Si se especifica la clave de BitLocker en este caso, se producirá el error "Bitlocker Key should not be specified" (No se debe especificar la clave de Bitlocker).<br/>  **Ejemplo**: 060456-014509-132033-080300-252615-584177-672089-411631|

##  <a name="preparing-disk-for-import-job"></a>Preparación del disco para el trabajo de importación

Para preparar las unidades para un trabajo de importación, llame a la herramienta WAImportExport mediante el comando **PrepImport**. Los parámetros que debe incluir dependen de si se trata de la primera sesión de copia o de una sesión de copia posterior.

### <a name="first-session"></a>Primera sesión

En primer lugar realice una sesión de copia para copiar un directorio único o varios directorios en un único disco o en varios discos (según lo que se haya especificado en el archivo CSV) mediante el comando PrepImport de la herramienta WAImportExport de la primera sesión de copia para copiar los directorios o los archivos mediante una nueva sesión de copia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> [/logdir:<LogDirectory>] [/sk:<StorageAccountKey>] [/silentmode] [/InitialDriveSet:<driveset.csv>] DataSet:<dataset.csv>
```

**Ejemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:\*\*\*\*\*\*\*\*\*\*\*\*\* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

### <a name="add-data-in-subsequent-session"></a>Adición de datos en una sesión posterior

En las sesiones de copia posteriores, no es necesario especificar los parámetros iniciales. Debe utilizar el mismo archivo de diario para que la herramienta recuerde en qué punto lo dejó en la sesión anterior. El estado de la sesión de copia se escribe en el archivo de diario. Aquí se muestra la sintaxis para que una sesión de copia posterior copie directorios o archivos adicionales:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<DifferentSessionId>  [DataSet:<differentdataset.csv>]
```

**Ejemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

### <a name="add-drives-to-latest-session"></a>Adición de unidades a la última sesión

Si los datos no cabían en las unidades especificadas en InitialDriveset, se puede usar la herramienta para agregar unidades adicionales a la misma sesión de copia. 

>[!NOTE] 
>El identificador de sesión debe coincidir con el identificador de la sesión anterior. El archivo de diario debe coincidir con el especificado en la sesión anterior.
>
```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AdditionalDriveSet:<newdriveset.csv>
```

**Ejemplo:**

```
WAImportExport.exe PrepImport /j:SameJournalTest.jrn /id:session#2  /AdditionalDriveSet:driveset-2.csv
```

### <a name="abort-the-latest-session"></a>Anulación de la última sesión:

Si se interrumpe una sesión de copia y no es posible reanudarla (por ejemplo, si un directorio de origen resultó inaccesible), debe anular la sesión actual para que se pueda revertir y se puedan iniciar nuevas sesiones de copia:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /AbortSession
```

**Ejemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /AbortSession
```

Solo se puede anular la última sesión de copia, si esta finalizó de forma anómala. Tenga en cuenta que no se puede anular la primera sesión de copia de una unidad. En su lugar, debe reiniciar la sesión de copia con un nuevo archivo de diario.

### <a name="resume-a-latest-interrupted-session"></a>Reanudación de la última sesión interrumpida

Si se interrumpe una sesión de copia por alguna razón, puede reanudarla mediante la ejecución de la herramienta especificando únicamente el archivo de diario:

```
WAImportExport.exe PrepImport /j:<SameJournalFile> /id:<SameSessionId> /ResumeSession
```

**Ejemplo:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2 /ResumeSession
```

> [!IMPORTANT] 
> Al reanudar una sesión de copia, no modifique los archivos de datos de origen ni los directorios mediante la adición o eliminación de archivos.

## <a name="waimportexport-parameters"></a>Parámetros de WAImportExport

| Parámetros | Descripción |
| --- | --- |
|     /j:&lt;JournalFile&gt;  | **Obligatorio**<br/> Ruta de acceso al archivo de diario. Un archivo de diario realiza un seguimiento de un conjunto de unidades y registra el progreso de la preparación de estas. Siempre se debe especificar el archivo de diario.  |
|     /logdir:&lt;LogDirectory&gt;  | **Opcional**. El directorio de registro.<br/> En este directorio se escribirán los archivos de registro detallados, así como algunos archivos temporales. Si no se especifica, se usará el directorio actual como directorio de registro. El directorio de registro se puede especificar solo una vez para el mismo archivo de diario.  |
|     /id:&lt;SessionId&gt;  | **Obligatorio**<br/> El identificador de sesión se usa para identificar una sesión de copia. Se utiliza para asegurar la recuperación precisa de una sesión de copia interrumpida.  |
|     /ResumeSession  | Opcional. Si la última sesión de copia finalizó de forma anómala, se puede especificar este parámetro para reanudar la sesión.   |
|     /AbortSession  | Opcional. Si la última sesión de copia finalizó de forma anómala, se puede especificar este parámetro para anular la sesión.  |
|     /sn:&lt;StorageAccountName&gt;  | **Obligatorio**<br/> Solo es aplicable para RepairImport y RepairExport. El nombre de la cuenta de almacenamiento.  |
|     /sk:&lt;StorageAccountKey&gt;  | **Obligatorio**<br/> La clave de la cuenta de almacenamiento. |
|     /InitialDriveSet:&lt;driveset.csv&gt;  | **Obligatorio** cuando se ejecuta la primera sesión de copia<br/> Un archivo CSV que contiene una lista de unidades de disco que se van a preparar.  |
|     /AdditionalDriveSet:&lt;driveset.csv&gt; | **Obligatoria**. Al agregar unidades de disco a la sesión de copia actual. <br/> Un archivo CSV que contiene una lista de unidades de disco adicionales que se van a agregar.  |
|      /r:&lt;RepairFile&gt; | **Obligatorio** Solo es aplicable para RepairImport y RepairExport.<br/> Ruta de acceso al archivo de seguimiento del progreso de la reparación. Cada unidad debe tener un solo archivo de reparación.  |
|     /d:&lt;TargetDirectories&gt; | **Obligatoria**. Solo es aplicable para RepairImport y RepairExport. Para RepairImport, uno o varios directorios separados por punto y coma que se van a reparar; Para RepairExport, un directorio que se va a reparar, p. ej., el directorio raíz de la unidad.  |
|     /CopyLogFile:&lt;DriveCopyLogFile&gt; | **Obligatorio** Solo es aplicable para RepairImport y RepairExport. Ruta de acceso al archivo de registro de copia de la unidad (detallado o de errores).  |
|     /ManifestFile:&lt;DriveManifestFile&gt; | **Obligatorio** Solo es aplicable para RepairExport.<br/> Ruta de acceso al archivo de manifiesto de la unidad.  |
|     /PathMapFile:&lt;DrivePathMapFile&gt; | **Opcional**. Solo es aplicable para RepairImport.<br/> Ruta de acceso al archivo que contiene las asignaciones de las rutas de acceso de archivo relativas a la raíz de la unidad de las ubicaciones de archivos reales (delimitado por tabulaciones). Cuando se especifica en primer lugar, se rellenará con las rutas de acceso de archivo con destinos vacíos, lo que significa que no se encuentran en TargetDirectories, se les ha denegado el acceso, tienen nombres no válidos o se encuentran en varios directorios. El archivo de asignación de rutas de acceso puede editarse manualmente para incluir las rutas de acceso de destino correctas y se especifica de nuevo para que la herramienta resuelva las rutas de acceso del archivo correctamente.  |
|     /ExportBlobListFile:&lt;ExportBlobListFile&gt; | **Obligatoria**. Solo se aplica para PreviewExport.<br/> Ruta de acceso al archivo XML que contiene una lista de rutas de acceso de blob o prefijos de ruta de acceso para los blobs que se van a exportar. El formato de archivo es el mismo que el formato de blob de la lista de blobs de la operación Put Job de la API de REST del servicio Import/Export.  |
|     /DriveSize:&lt;DriveSize&gt; | **Obligatoria**. Solo se aplica para PreviewExport.<br/>  Tamaño de las unidades que se utilizan para la exportación. Por ejemplo, 500 GB; 1,5 TB. Nota: 1 GB = 1 000 000 000 bytes 1 TB = 1 000 000 000 000 bytes  |
|     /DataSet:&lt;dataset.csv&gt; | **Obligatorio**<br/> Un archivo CSV que contiene la lista de directorios o archivos que se van a copiar en las unidades de destino.  |
|     /silentmode  | **Opcional**.<br/> Si no se especifica, le recordará el requisito de las unidades y necesitará su confirmación para continuar.  |

## <a name="tool-output"></a>Salida de la herramienta

### <a name="sample-drive-manifest-file"></a>Archivo de manifiesto de la unidad de ejemplo

```xml
<?xml version="1.0" encoding="UTF-8"?>
<DriveManifest Version="2011-MM-DD">
   <Drive>
      <DriveId>drive-id</DriveId>
      <StorageAccountKey>storage-account-key</StorageAccountKey>
      <ClientCreator>client-creator</ClientCreator>
      <!-- First Blob List -->
      <BlobList Id="session#1-0">
         <!-- Global properties and metadata that applies to all blobs -->
         <MetadataPath Hash="md5-hash">global-metadata-file-path</MetadataPath>
         <PropertiesPath Hash="md5-hash">global-properties-file-path</PropertiesPath>
         <!-- First Blob -->
         <Blob>
            <BlobPath>blob-path-relative-to-account</BlobPath>
            <FilePath>file-path-relative-to-transfer-disk</FilePath>
            <ClientData>client-data</ClientData>
            <Length>content-length</Length>
            <ImportDisposition>import-disposition</ImportDisposition>
            <!-- page-range-list-or-block-list -->
            <!-- page-range-list -->
            <PageRangeList>
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
               <PageRange Offset="1073741824" Length="512" Hash="md5-hash" />
            </PageRangeList>
            <!-- block-list -->
            <BlockList>
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
               <Block Offset="1073741824" Length="4194304" Id="block-id" Hash="md5-hash" />
            </BlockList>
            <MetadataPath Hash="md5-hash">metadata-file-path</MetadataPath>
            <PropertiesPath Hash="md5-hash">properties-file-path</PropertiesPath>
         </Blob>
      </BlobList>
   </Drive>
</DriveManifest>
```

### <a name="sample-journal-file-xml-for-each-drive"></a>Archivo de diario de ejemplo (XML) para cada unidad

```xml
[BeginUpdateRecord][2016/11/01 21:22:25.379][Type:ActivityRecord]
ActivityId: DriveInfo
DriveState: [BeginValue]
<?xml version="1.0" encoding="UTF-8"?>
<Drive>
   <DriveId>drive-id</DriveId>
   <BitLockerKey>*******</BitLockerKey>
   <ManifestFile>\DriveManifest.xml</ManifestFile>
   <ManifestHash>D863FE44F861AE0DA4DCEAEEFFCCCE68</ManifestHash> </Drive>
[EndValue]
SaveCommandOutput: Completed
[EndUpdateRecord]
```

### <a name="sample-journal-file-jrn-for-session-that-records-the-trail-of-sessions"></a>Ejemplo de archivo de diario (JRN) de la sesión, que registra el rastro de sesiones

```
[BeginUpdateRecord][2016/11/02 18:24:14.735][Type:NewJournalFile]
VocabularyVersion: 2013-02-01
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.749][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
LogDirectory: F:\logs
[EndUpdateRecord]
[BeginUpdateRecord][2016/11/02 18:24:14.754][Type:ActivityRecord]
ActivityId: PrepImportDriveCommandContext
StorageAccountKey: *******
[EndUpdateRecord]
```

## <a name="faq"></a>P+F

### <a name="general"></a>General

#### <a name="what-is-waimportexport-tool"></a>¿Qué es la herramienta WAImportExport?

La herramienta WAImportExport es la herramienta de preparación y reparación de unidades de disco duro que se puede usar con el servicio Microsoft Azure Import/Export. Puede usar esta herramienta para copiar los datos en las unidades de disco duro que se van a enviar a un centro de datos de Azure. Cuando haya finalizado un trabajo de importación, puede usar esta herramienta para reparar los blobs que se hayan dañado, no se hayan encontrado o hayan entrado en conflicto con otros blobs. Después de recibir las unidades de un trabajo de exportación completado, puede utilizar esta herramienta para reparar los archivos dañados o no encontrados de las unidades.

#### <a name="how-does-the-waimportexport-tool-work-on-multiple-source-dir-and-disks"></a>¿Cómo funciona la herramienta WAImportExport en varios directorios y discos de origen?

Si el tamaño de los datos es mayor que el del disco, la herramienta WAImportExport distribuirá los datos entre los discos de forma optimizada. La copia de datos en varios discos se puede hacer en paralelo o secuencialmente. No hay ningún límite en el número de discos en los que se pueden escribir los datos de forma simultánea. La herramienta distribuirá los datos basándose en el tamaño del disco y de la carpeta. Seleccionará el disco más optimizado para el tamaño del objeto. Al cargar los datos en la cuenta de almacenamiento, estos convergen de nuevo en la estructura de directorios que se especificó.

#### <a name="where-can-i-find-previous-version-of-waimportexport-tool"></a>¿Dónde puedo encontrar la versión anterior de la herramienta WAImportExport?

La herramienta WAImportExport tiene todas las funcionalidades que tenía la herramienta WAImportExport V1. La herramienta WAImportExport permite a los usuarios especificar varios orígenes y escribir en varias unidades. Además, uno puede administrar fácilmente varias ubicaciones de origen desde las que se deben copiar los datos en un solo archivo CSV. Sin embargo, en caso de que necesite soporte de SAS o si desea copiar un único origen en un único disco, puede [descargar la herramienta WAImportExport V1] (http://go.microsoft.com/fwlink/?LinkID=301900&amp;clcid=0x409) y consultar la [referencia de WAImportExport V1](storage-import-export-tool-how-to-v1.md) para obtener ayuda con el uso de WAImportExport V1.

#### <a name="what-is-a-session-id"></a>¿Qué es un identificador de sesión?

La herramienta espera que el parámetro de sesión de copia (/id) sea el mismo si el objetivo es distribuir los datos entre varios discos. Mantener el mismo nombre de la sesión de copia permitirá al usuario copiar datos de una o varias ubicaciones de origen en uno o varios discos o directorios de destino. Mantener el mismo identificador de sesión permite a la herramienta seleccionar la copia de archivos en la que se quedó la última vez.

Sin embargo, no se puede utilizar la misma sesión de copia para importar datos a diferentes cuentas de almacenamiento.

Si el nombre de la sesión de copia es el mismo a través de varias ejecuciones de la herramienta, también es previsible que el archivo de registro (/logdir) y la clave de la cuenta de almacenamiento (/sk) sean los mismos.

SessionId puede estar formado por letras, números 0 a 9, guion bajo (\_), guion (-) o hash (#) y su longitud debe estar comprendida entre 3 y 30 caracteres.

Por ejemplo, session-1 o session#1 o session\_1

#### <a name="what-is-a-journal-file"></a>¿Qué es un archivo de diario?

Cada vez que ejecuta la herramienta WAImportExport para copiar archivos en el disco duro la herramienta crea una sesión de copia. El estado de la sesión de copia se escribe en el archivo de diario. Si se interrumpe una sesión de copia (por ejemplo, debido a una pérdida de alimentación del sistema), se puede reanudar ejecutando la herramienta de nuevo y especificando el archivo de diario en la línea de comandos.

Para cada unidad de disco duro que prepare con la herramienta Azure Import/Export, esta creará un único archivo de diario con el nombre "&lt;DriveID&gt;.xml", donde el identificador de la unidad es el número de serie asociado a la unidad que la herramienta lee desde el disco. Necesitará los archivos de diario de todas las unidades para crear el trabajo de importación. También se puede utilizar el archivo de diario para reanudar la preparación de la unidad si la herramienta sufre una interrupción.

#### <a name="what-is-a-log-directory"></a>¿Qué es un directorio de registro?

El directorio de registro especifica el directorio que se usará para almacenar los registros detallados así como los archivos de manifiesto temporales. Si no se especifica, se usará el directorio actual como directorio de registro. Los registros son detallados.

### <a name="prerequisites"></a>Requisitos previos

#### <a name="what-are-the-specifications-of-my-disk"></a>¿Cuáles son las especificaciones de mi disco?

Una o más unidades de disco duro SATAII o III, o SSD, de 2,5 o 3,5 pulgadas conectadas a la máquina de copia.

#### <a name="how-can-i-enable-bitlocker-on-my-machine"></a>¿Cómo se puede habilitar BitLocker en mi máquina?

Una manera sencilla de comprobarlo es hacer clic con el botón derecho en la unidad del sistema. Le mostrará las opciones de Bitlocker si la funcionalidad está activada. Si está desactivada, no las verá.

![Comprobación de BitLocker](./media/storage-import-export-tool-preparing-hard-drives-import/BitLocker.png)

Este es un artículo sobre [cómo habilitar BitLocker](https://technet.microsoft.com/library/cc766295.aspx)

Es posible que la máquina no tenga el chip TPM. Si no obtiene una salida mediante tpm.msc, mire la sección de preguntas más frecuentes que aparece a continuación.

#### <a name="how-to-disable-trusted-platform-module-tpm-in-bitlocker"></a>¿Cómo deshabilitar el Módulo de plataforma segura (TPM) en BitLocker?
> [!NOTE]
> Solo si no hay ningún TPM en sus servidores, debe deshabilitar la directiva TPM. No es necesario deshabilitar TPM si hay un TPM de confianza en el servidor del usuario. 
> 

Para deshabilitar el TPM de BitLocker, siga los pasos siguientes:<br/>
1. Inicie el **Editor de directivas de grupo** escribiendo gpedit.msc en un símbolo del sistema. Si el **Editor de directivas de grupo** no está disponible, habilite primero BitLocker. Consulte las preguntas más frecuentes descritas anteriormente.
2. Abra **Directiva de equipo Local &gt; Configuración del equipo &gt; Plantillas administrativas &gt; Componentes de Windows&gt; Cifrado de unidad BitLocker &gt; Unidades del sistema operativo**.
3. Edite la directiva **Requerir autenticación adicional al iniciar**.
4. Establezca la directiva en **Habilitado** y asegúrese de que la opción **Permitir BitLocker sin un TPM compatible** está activada.

####  <a name="how-to-check-if-net-4-or-higher-version-is-installed-on-my-machine"></a>¿Cómo se comprueba si .Net 4 o una versión posterior están instaladas en mi máquina?

Todas las versiones de Microsoft .NET Framework se instalan en el directorio siguiente: %windir%\Microsoft.NET\Framework\

Vaya a este directorio de la máquina de destino donde se debe ejecutar la herramienta. Busque un nombre de carpeta que empieza por "v4". La ausencia de un directorio de este tipo significa que .NET 4 no está instalado en la máquina. Puede descargar .Net 4 en la máquina con [Microsoft .NET Framework 4 (Instalador web)](https://www.microsoft.com/download/details.aspx?id=17851).

### <a name="limits"></a>límites

#### <a name="how-many-drives-can-i-preparesend-at-the-same-time"></a>¿Cuántas unidades se pueden preparar o enviar al mismo tiempo?

No hay ningún límite en el número de discos que puede preparar la herramienta. Sin embargo, la herramienta solo admite letras de unidad como entradas. Eso limita la preparación a 25 discos de forma simultánea. Un solo trabajo puede controlar un máximo de 10 discos a la vez. Si prepara más de 10 discos con destino en la misma cuenta de almacenamiento, estos se pueden distribuir en varios trabajos.

#### <a name="can-i-target-more-than-one-storage-account"></a>¿Se puede usar como destino más de una cuenta de almacenamiento?

Solo se puede enviar una cuenta de almacenamiento por trabajo y en una sola sesión de copia.

### <a name="capabilities"></a>Capacidades

#### <a name="does-waimportexportexe-encrypt-my-data"></a>¿Cifra WAImportExport.exe mis datos?

Sí. El cifrado de BitLocker está habilitado y es obligatorio para este proceso.

#### <a name="what-will-be-the-hierarchy-of-my-data-when-it-appears-in-the-storage-account"></a>¿Cuál será la jerarquía de mis datos cuando aparezcan en la cuenta de almacenamiento?

Aunque los datos se distribuyen entre los discos, al cargar los datos en la cuenta de almacenamiento, estos convergen de nuevo en la estructura de directorios que se especificó en el archivo CSV de conjunto de datos.

#### <a name="how-many-of-the-input-disks-will-have-active-io-in-parallel-when-copy-is-in-progress"></a>¿Cuántos discos de entrada tendrán E/S activo en paralelo cuando la copia esté en curso?

La herramienta distribuye los datos entre los discos de entrada en función del tamaño de los archivos de entrada. Es decir, el número de discos activos en paralelo depende completamente de la naturaleza de los datos de entrada. Según el tamaño de los archivos individuales en el conjunto de datos de entrada, uno o varios discos pueden mostrar E/S activo en paralelo. Consulte más detalles en la siguiente pregunta.

#### <a name="how-does-the-tool-distribute-the-files-across-the-disks"></a>¿Cómo distribuye la herramienta los archivos entre los discos?

La herramienta WAImportExport lee y escribe archivos lote por lote, un lote contiene un número máximo de 100 000 archivos. Esto significa que se pueden escribir 100 000 archivos como máximo en paralelo. Se puede escribir en varios discos de forma simultánea si estos 100 000 archivos están distribuidos en varias unidades. Sin embargo, el que la herramienta escriba en varios discos simultáneamente o en un único disco depende del tamaño acumulado del lote. Por ejemplo, en el caso de archivos pequeños, si los 100 000 archivos caben en una sola unidad, la herramienta escribirá en un solo disco durante el procesamiento de este lote.

### <a name="waimportexport-output"></a>Salida de WAImportExport

#### <a name="there-are-two-journal-files-which-one-should-i-upload-to-azure-portal"></a>Hay dos archivos de diario, ¿cuál debería cargar en Azure Portal?

**.xml**: para cada unidad de disco duro que prepare con la herramienta Azure Import/Export, esta creará un único archivo de diario con el nombre `<DriveID>.xml`, donde el identificador de la unidad es el número de serie asociado a la unidad que la herramienta lee desde el disco. Necesitará los archivos de diario de todas las unidades para crear el trabajo de importación en Azure Portal. También se puede utilizar el archivo de diario para reanudar la preparación de la unidad si la herramienta sufre una interrupción.

**.jrn**: el archivo de diario con el sufijo `.jrn` contiene el estado de todas las sesiones de copia de una unidad de disco duro. También contiene la información necesaria para crear el trabajo de importación. Siempre debe especificar un archivo de diario al ejecutar la herramienta WAImportExport, así como un identificador de sesión de copia.

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de la herramienta Azure Import/Export](storage-import-export-tool-setup.md)
* [Establecimiento de las propiedades y metadatos durante el proceso de importación](storage-import-export-tool-setting-properties-metadata-import.md)
* [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow.md)
* [Quick reference for frequently used commands for import jobs](storage-import-export-tool-quick-reference.md) (Referencia rápida para comandos usados con frecuencia) 
* [Revisión del estado del trabajo con archivos de registro de copia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparación de un trabajo de importación](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparación de un trabajo de exportación](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Solución de problemas de la herramienta Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
