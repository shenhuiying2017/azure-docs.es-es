---
title: "Preparación de unidades de disco duro para un trabajo de importación de Azure Import/Export (versión 1) | Microsoft Docs"
description: "Aprenda a preparar unidades de disco duro mediante la herramienta WAImportExport (versión 1) para crear un trabajo de importación para el servicio Azure Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 3d818245-8b1b-4435-a41f-8e5ec1f194b2
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2017
ms.author: muralikk
ms.openlocfilehash: 361e16262e528c7dea1bab4b9d945a28af8be399
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="preparing-hard-drives-for-an-import-job"></a>Preparación de unidades de disco duro para un trabajo de importación
Para preparar una o varias unidades de disco duro para un trabajo de importación, siga estos pasos:

-   Identifique los datos que se van a importar en Blob service

-   Identifique los directorios virtuales de destino y los blobs de Blob service

-   Determine cuántas unidades necesita

-   Copie los datos en cada uno de los discos duros

 Para ver un flujo de trabajo de ejemplo, consulte [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md).

## <a name="identify-the-data-to-be-imported"></a>Identificación de los datos que se van a importar
 El primer paso para crear un trabajo de importación es determinar los directorios y archivos que se van a importar. Puede tratarse de una lista de directorios, una lista de archivos únicos o una combinación de ambos. Si se incluye un directorio, todos los archivos del directorio y sus subdirectorios formarán parte del trabajo de importación.

> [!NOTE]
>  Como se incluyen subdirectorios de forma recursiva al incluir un directorio primario, especifique solo este directorio. No especifique ninguno de los subdirectorios.
>
>  Actualmente, la herramienta Microsoft Azure Import/Export tiene la siguiente limitación: si un directorio contiene más datos de los que un disco duro puede contener, el directorio deberá dividirse en directorios más pequeños. Por ejemplo, si un directorio contiene 2,5 TB de datos y la capacidad del disco duro es de solo 2 TB, deberá dividir el directorio de 2,5 TB en directorios más pequeños. Esta limitación se solucionará en una versión posterior de la herramienta.

## <a name="identify-the-destination-locations-in-the-blob-service"></a>Identificación de las ubicaciones de destino en Blob service
 Para cada directorio o archivo que se va a importar, debe identificar un directorio virtual de destino o un blob en Azure Blob service. Usará estos destinos como entradas de la herramienta Azure Import/Export. Tenga en cuenta que los directorios deben delimitarse con el carácter de barra diagonal "/".

 En la tabla siguiente se muestran algunos ejemplos de destinos de blob:

|Archivo o directorio de origen|Blob de destino o directorio virtual|
|------------------------------|-------------------------------------------|
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|
|K:\Temp\FavoriteVideo.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO|
|\\\myshare\john\music|https://mystorageaccount.blob.core.windows.net/music|

## <a name="determine-how-many-drives-are-needed"></a>Determinación de cuántas unidades necesita
 A continuación, debe determinar:

-   El número de unidades de disco duro necesarias para almacenar los datos.

-   Determine los directorios o archivos independientes que se copiarán en cada disco duro.

 Asegúrese de que tiene el número de unidades de disco duro que necesita para almacenar los datos que va a transferir.

## <a name="copy-data-to-your-hard-drive"></a>Copiado de los datos en el disco duro
 Esta sección describe cómo llamar a la herramienta Azure Import/Export para copiar los datos en una o varias unidades de disco duro. Cada vez que se llama a la herramienta Azure Import/Export, se crea una nueva *sesión de copia*. Cree al menos una sesión de copia para cada unidad en la que va a copiar los datos; en algunos casos, es posible que necesite más de una sesión de copia para copiar todos los datos en una única unidad. Estas son algunas de las razones por las que puede que necesite varias sesiones de copia:

-   Debe crear una sesión de copia independiente para cada unidad en la que vaya a realizar una copia.

-   Una sesión de copia puede copiar un único directorio o un único blob en la unidad. Si desea copiar varios directorios, varios blobs o una combinación de ambos, deberá crear varias sesiones de copia.

-   Puede especificar propiedades y metadatos que se establecerán en los blobs importados como parte de un trabajo de importación. Las propiedades o metadatos que especifique para una sesión de copia se aplicarán a todos los blobs especificados en esa sesión de copia. Si desea especificar propiedades o metadatos diferentes para algunos blobs, deberá crear una sesión de copia independiente. Consulte [Establecimiento de las propiedades y metadatos durante el proceso de importación](storage-import-export-tool-setting-properties-metadata-import-v1.md).

> [!NOTE]
>  Si tiene varias máquinas que cumplan los requisitos descritos en [Configuración de la herramienta Azure Import-Export](storage-import-export-tool-setup-v1.md), puede copiar datos en varias unidades de disco duro en paralelo mediante la ejecución de una instancia de esta herramienta en cada máquina.

 Para cada unidad de disco duro que prepare con la herramienta Azure Import/Export, esta creará un solo archivo de diario. Necesitará los archivos de diario de todas las unidades para crear el trabajo de importación. También se puede utilizar el archivo de diario para reanudar la preparación de la unidad si la herramienta sufre una interrupción.

### <a name="azure-importexport-tool-syntax-for-an-import-job"></a>Sintaxis de la herramienta Azure Import/Export para un trabajo de importación
 Para preparar las unidades para un trabajo de importación, llame a la herramienta Azure Import/Export mediante el comando **PrepImport**. Los parámetros que debe incluir dependen de si se trata de la primera sesión de copia o de una sesión de copia posterior.

 La primera sesión de copia de una unidad requiere algunos parámetros adicionales para especificar la clave de la cuenta de almacenamiento, la letra de la unidad de destino, si la unidad debe estar formateada, si se debe cifrar la unidad y, si es así, la clave de BitLocker y el directorio de registro. Aquí se muestra la sintaxis para que una sesión de copia inicial copie un directorio o un único archivo:

 **Primera sesión de copia para copiar un único directorio**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Primera sesión de copia para copiar un único archivo**

 `WAImportExport PrepImport /sk:<StorageAccountKey> /csas:<ContainerSas> /t: <TargetDriveLetter> [/format] [/silentmode] [/encrypt] [/bk:<BitLockerKey>] [/logdir:<LogDirectory>] /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 En las sesiones de copia posteriores, no es necesario especificar los parámetros iniciales. Aquí se muestra la sintaxis para que una sesión de copia posterior copie un directorio o un único archivo:

 **Sesiones de copia posteriores para copiar un único directorio**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcdir:<SourceDirectory> /dstdir:<DestinationBlobVirtualDirectory> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

 **Sesiones de copia posteriores para copiar un único archivo**

 `WAImportExport PrepImport /j:<JournalFile> /id:<SessionId> /srcfile:<SourceFile> /dstblob:<DestinationBlobPath> [/Disposition:<Disposition>] [/BlobType:<BlockBlob|PageBlob>] [/PropertyFile:<PropertyFile>] [/MetadataFile:<MetadataFile>]`

### <a name="parameters-for-the-first-copy-session-for-a-hard-drive"></a>Parámetros para la primera sesión de copia de una unidad de disco duro
 Cada vez que ejecuta la herramienta Azure Import/Export para copiar archivos en el disco duro la herramienta, crea una sesión de copia. Cada sesión de copia realiza la copia de un único directorio o un único archivo en una unidad de disco duro. El estado de la sesión de copia se escribe en el archivo de diario. Si se interrumpe una sesión de copia (por ejemplo, debido a una pérdida de alimentación del sistema), se puede reanudar ejecutando la herramienta de nuevo y especificando el archivo de diario en la línea de comandos.

> [!WARNING]
>  Si especifica el parámetro **/format** para la primera sesión de copia, se formateará la unidad y se borrarán todos los datos de esta. Se recomienda que use solo unidades vacías para la sesión de copia.

 El comando que se usa para la primera sesión de copia de cada unidad requiere parámetros distintos a los comandos de las sesiones de copia posteriores. En la tabla siguiente se enumeran los parámetros adicionales que están disponibles para la primera sesión de copia:

|Parámetro de línea de comandos|Descripción|
|-----------------------------|-----------------|
|**/sk:**&lt;ClaveCuentaAlmacenamiento\>|`Optional.` La clave de la cuenta de almacenamiento en la que se importarán los datos. Debe incluir **/sk:**<ClaveCuentaAlmacenamiento\> o **/csas:**<SasContenedor\> en el comando.|
|**/csas:**&lt;SasContenedor\>|`Optional`. El contenedor del SAS se utiliza para importar datos a la cuenta de almacenamiento. Debe incluir **/sk:**<ClaveCuentaAlmacenamiento\> o **/csas:**<SasContenedor\> en el comando.<br /><br /> El valor de este parámetro debe comenzar con el nombre del contenedor, seguido de un signo de interrogación (?) y el token de SAS. Por ejemplo:<br /><br /> `mycontainer?sv=2014-02-14&sr=c&si=abcde&sig=LiqEmV%2Fs1LF4loC%2FJs9ZM91%2FkqfqHKhnz0JM6bqIqN0%3D&se=2014-11-20T23%3A54%3A14Z&sp=rwdl`<br /><br /> Los permisos, tanto si se especifican en la dirección URL como en una directiva de acceso almacenada, deben incluir trabajos de lectura, escritura y eliminación para trabajos de importación, y lectura, escritura y enumeración para trabajos de exportación.<br /><br /> Cuando se especifica este parámetro, todos los blobs que desee importar o exportar deben estar dentro del contenedor especificado en la firma de acceso compartido.|
|**/t:**&lt;LetraUnidadDestino\>|`Required.` La letra de unidad del disco duro de destino para la sesión de copia actual, sin los dos puntos finales.|
|**/format**|`Optional.` Especifique este parámetro si hay que formatear la unidad o, de lo contrario, omítalo. Antes de que la herramienta aplique formato a la unidad, se le pedirá una confirmación desde la consola. Para suprimir la confirmación, especifique el parámetro /silentmode.|
|**/silentmode**|`Optional.`Especifique este parámetro para suprimir la confirmación para formatear la unidad de destino.|
|**/encrypt**|`Optional.` Este parámetro se especifica cuando la unidad todavía no se ha cifrado con BitLocker y debe cifrarse mediante la herramienta. Si la unidad ya se ha cifrado con BitLocker, omita este parámetro y especifique el parámetro `/bk`, proporcionando la clave de BitLocker existente.<br /><br /> Si especifica el parámetro `/format`, también debe especificar el parámetro `/encrypt`.|
|**/bk:**&lt;ClaveBitLocker\>|`Optional.`Si se ha especificado `/encrypt`, se omite este parámetro. Si se omite `/encrypt`, ya debe tener cifrada la unidad con BitLocker. Utilice este parámetro para especificar la clave de BitLocker. El cifrado de BitLocker es necesario en todos los discos duros para poder realizar trabajos de importación.|
|**/logdir:**&lt;DirectorioRegistro\>|`Optional.` El directorio de registro especifica el directorio que se usará para almacenar los registros detallados así como los archivos de manifiesto temporales. Si no se especifica, se usará el directorio actual como directorio de registro.|

### <a name="parameters-required-for-all-copy-sessions"></a>Parámetros requeridos para todas las sesiones de copia
 El archivo de diario contiene el estado de todas las sesiones de copia de una unidad de disco duro. También contiene la información necesaria para crear el trabajo de importación. Siempre debe especificar un archivo de diario al ejecutar la herramienta Azure Import/Export, así como un identificador de sesión de copia:

|||
|-|-|
|Parámetro de línea de comandos|Descripción|
|**/j:**&lt;ArchivoDiario\>|`Required.` La ruta de acceso al archivo de diario. Cada unidad debe tener exactamente un archivo de diario. Tenga en cuenta que el archivo de diario no debe residir en la unidad de destino. La extensión del archivo de diario es `.jrn`.|
|**/id:**&lt;IdSesión\>|`Required.` El identificador de sesión identifica una sesión de copia. Se utiliza para asegurar la recuperación precisa de una sesión de copia interrumpida. Los archivos que se copian en una sesión de copia se almacenan en un directorio con el mismo nombre que el identificador de sesión en la unidad de destino.|

### <a name="parameters-for-copying-a-single-directory"></a>Parámetros para copiar un único directorio
 Al copiar un único directorio, se aplican los siguientes parámetros obligatorios y opcionales:

|Parámetro de línea de comandos|Descripción|
|----------------------------|-----------------|
|**/srcdir:**&lt;DirectorioOrigen\>|`Required.` El directorio de origen que contiene los archivos que se copiarán en la unidad de destino. La ruta del directorio debe ser una ruta absoluta (no una ruta de acceso relativa).|
|**/dstdir:**&lt;DirectorioVirtualBlobDestino\>|`Required.` La ruta de acceso al directorio virtual de destino en la cuenta de Windows Azure Storage. El directorio virtual puede existir ya o no.<br /><br /> Puede especificar un contenedor o un prefijo de blob como `music/70s/`. El directorio de destino debe comenzar con el nombre del contenedor, seguido por una barra diagonal "/" y puede incluir opcionalmente un directorio virtual de blobs que termina por "/".<br /><br /> Si el contenedor de destino es el contenedor raíz, debe especificar de forma explícita el contenedor raíz, incluida la barra diagonal, como `$root/`. Puesto que los blobs del contenedor raíz no pueden incluir "/" en sus nombres, los subdirectorios del directorio de origen no se copiarán si el directorio de destino es el contenedor raíz.<br /><br /> Asegúrese de utilizar nombres de contenedor válidos al especificar los directorios virtuales de destino o blobs. Tenga en cuenta que los nombres de contenedor deben estar en minúsculas. Para más información sobre las reglas de nomenclatura de contenedores, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.`Especifica el comportamiento cuando un blob con la dirección especificada ya existe. Los valores válidos para este parámetro son: `rename`, `no-overwrite` y `overwrite`. Tenga en cuenta que estos valores distinguen mayúsculas de minúsculas. Si no se especifica ningún valor, el valor predeterminado será `rename`.<br /><br /> El valor especificado para este parámetro afecta a todos los archivos del directorio especificado mediante el parámetro `/srcdir`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Especifica el tipo de blob para los blobs de destino. Los valores válidos son: `BlockBlob` y `PageBlob`. Tenga en cuenta que estos valores distinguen mayúsculas de minúsculas. Si no se especifica ningún valor, el valor predeterminado será `BlockBlob`.<br /><br /> En la mayoría de los casos, se recomienda `BlockBlob`. Si especifica `PageBlob`, la longitud de cada archivo del directorio debe ser un múltiplo de 512, el tamaño de una página para los blobs en páginas.|
|**/PropertyFile:**&lt;PropertyFile\>|`Optional.` Ruta de acceso al archivo de propiedades para los blobs de destino. Para obtener más información, consulte [Formato de archivo de propiedades y metadatos del servicio Import-Export](../storage-import-export-file-format-metadata-and-properties.md).|
|**/MetadataFile:**&lt;MetadataFile\>|`Optional.` Ruta de acceso al archivo de metadatos para los blobs de destino. Para obtener más información, consulte [Formato de archivo de propiedades y metadatos del servicio Import-Export](../storage-import-export-file-format-metadata-and-properties.md).|

### <a name="parameters-for-copying-a-single-file"></a>Parámetros para copiar un único archivo
 Al copiar un único archivo, se aplican los siguientes parámetros obligatorios y opcionales:

|Parámetro de línea de comandos|Descripción|
|----------------------------|-----------------|
|**/srcfile:**&lt;SourceFile\>|`Required.`La ruta de acceso completa al archivo que se va a copiar. La ruta del directorio debe ser una ruta absoluta (no una ruta de acceso relativa).|
|**/dstblob:**&lt;DestinationBlobPath\>|`Required.` La ruta de acceso al blob de destino en la cuenta de Windows Azure Storage. El blob puede existir ya o no.<br /><br /> Especifique el nombre de blob comenzando con el nombre del contenedor. Ni el nombre del blob ni el de la cuenta de almacenamiento pueden comenzar con "/". Para más información sobre las reglas de nomenclatura de blobs, consulte [Asignación de nombres y referencias a contenedores, blobs y metadatos](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata).<br /><br /> Si el contenedor de destino es el contenedor raíz, debe especificar de forma explícita `$root` como contenedor como, por ejemplo, `$root/sample.txt`. Tenga en cuenta que los blobs del contenedor raíz no pueden incluir "/" en sus nombres.|
|**/Disposition:**<rename&#124;no-overwrite&#124;overwrite>|`Optional.`Especifica el comportamiento cuando un blob con la dirección especificada ya existe. Los valores válidos para este parámetro son: `rename`, `no-overwrite` y `overwrite`. Tenga en cuenta que estos valores distinguen mayúsculas de minúsculas. Si no se especifica ningún valor, el valor predeterminado será `rename`.|
|**/BlobType:**<BlockBlob&#124;PageBlob>|`Optional.` Especifica el tipo de blob para los blobs de destino. Los valores válidos son: `BlockBlob` y `PageBlob`. Tenga en cuenta que estos valores distinguen mayúsculas de minúsculas. Si no se especifica ningún valor, el valor predeterminado será `BlockBlob`.<br /><br /> En la mayoría de los casos, se recomienda `BlockBlob`. Si especifica `PageBlob`, la longitud de cada archivo del directorio debe ser un múltiplo de 512, el tamaño de una página para los blobs en páginas.|
|**/PropertyFile:**&lt;PropertyFile\>|`Optional.` Ruta de acceso al archivo de propiedades para los blobs de destino. Para obtener más información, consulte [Formato de archivo de propiedades y metadatos del servicio Import-Export](../storage-import-export-file-format-metadata-and-properties.md).|
|**/MetadataFile:**&lt;MetadataFile\>|`Optional.` Ruta de acceso al archivo de metadatos para los blobs de destino. Para obtener más información, consulte [Formato de archivo de propiedades y metadatos del servicio Import-Export](../storage-import-export-file-format-metadata-and-properties.md).|

### <a name="resuming-an-interrupted-copy-session"></a>Reanudación de una sesión de copia interrumpida
 Si se interrumpe una sesión de copia por alguna razón, puede reanudarla mediante la ejecución de la herramienta especificando únicamente el archivo de diario:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /ResumeSession
```

 Solo se puede reanudar la sesión de copia más reciente, si esta finalizó de forma anómala.

> [!IMPORTANT]
>  Al reanudar una sesión de copia, no modifique los archivos de datos de origen ni los directorios mediante la adición o eliminación de archivos.

### <a name="aborting-an-interrupted-copy-session"></a>Anulación de una sesión de copia interrumpida
 Si se interrumpe una sesión de copia y no es posible reanudarla (por ejemplo, si un directorio de origen resultó inaccesible), debe anular la sesión actual para que se pueda revertir y se puedan iniciar nuevas sesiones de copia:

```
WAImportExport.exe PrepImport /j:<JournalFile> /id:<SessionId> /AbortSession
```

 Solo se puede anular la última sesión de copia, si esta finalizó de forma anómala. Tenga en cuenta que no se puede anular la primera sesión de copia de una unidad. En su lugar, debe reiniciar la sesión de copia con un nuevo archivo de diario.

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de la herramienta Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Establecimiento de las propiedades y metadatos durante el proceso de importación](storage-import-export-tool-setting-properties-metadata-import-v1.md)
* [Flujo de trabajo de ejemplo para preparar las unidades de disco duro para un trabajo de importación](storage-import-export-tool-sample-preparing-hard-drives-import-job-workflow-v1.md)
* [Quick reference for frequently used commands for import jobs](storage-import-export-tool-quick-reference-v1.md) (Referencia rápida para comandos usados con frecuencia) 
* [Revisión del estado del trabajo con archivos de registro de copia](storage-import-export-tool-reviewing-job-status-v1.md)
* [Reparación de un trabajo de importación](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Reparación de un trabajo de exportación](storage-import-export-tool-repairing-an-export-job-v1.md)
* [Solución de problemas de la herramienta Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
