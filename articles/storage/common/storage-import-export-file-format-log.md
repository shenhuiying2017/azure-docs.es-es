---
title: Formato del archivo de registro de Azure Import/Export | Microsoft Docs
description: "Obtenga información acerca del formato de los archivos de registro creados cuando se ejecutan los pasos de un trabajo del servicio Import/Export."
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: 
ms.assetid: 38cc16bd-ad55-4625-9a85-e1726c35fd1b
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 16234ccaf13ce1d85cfd207ed4734e683070faa6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-importexport-service-log-file-format"></a>Formato del archivo de registro del servicio Azure Import/Export
Cuando el servicio Microsoft Azure Import/Export realiza una acción en una unidad como parte de un trabajo de importación o de exportación, los registros se escriben en blobs en bloques de la cuenta de almacenamiento asociada a dicho trabajo.  
  
El servicio Import/Export puede escribir dos registros:  
  
-   El registro de errores siempre se genera en caso de error.  
  
-   El registro detallado no está habilitado de forma predeterminada; para habilitarlo, establezca la propiedad `EnableVerboseLog` en una operación [Put Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate) o [Update Job Properties](/rest/api/storageimportexport/jobs#Jobs_Update).  
  
## <a name="log-file-location"></a>Ubicación del archivo de registro  
Los registros se escriben en blobs en bloques en el contenedor o directorio virtual especificados por el valor `ImportExportStatesPath`, que puede establecer en una operación `Put Job`. La ubicación en la que se escriben los registros depende de cómo se especifica la autenticación del trabajo, junto con el valor especificado para `ImportExportStatesPath`. La autenticación del trabajo se puede especificar a través de una clave de la cuenta de almacenamiento o una SAS (firma de acceso compartido) del contenedor.  
  
El nombre del contenedor o directorio virtual puede ser el nombre predeterminado de `waimportexport` u otro nombre de contenedor o directorio virtual que se especifique.  
  
La siguiente tabla muestra las posibles opciones:  
  
|Método de autenticación|Valor de `ImportExportStatesPath`Element|Ubicación de blobs del registro|  
|---------------------------|----------------------------------------------|---------------------------|  
|Clave de cuenta de almacenamiento|Valor predeterminado|Un contenedor llamado `waimportexport`, que es el contenedor predeterminado. Por ejemplo:<br /><br /> `https://myaccount.blob.core.windows.net/waimportexport`|  
|Clave de cuenta de almacenamiento|Valor especificado por el usuario|Un contenedor al que el usuario ha asignado un nombre. Por ejemplo:<br /><br /> `https://myaccount.blob.core.windows.net/mylogcontainer`|  
|SAS de contenedor|Valor predeterminado|Un directorio virtual denominado `waimportexport`, que es el nombre predeterminado, debajo del contenedor especificado en el SAS.<br /><br /> Por ejemplo, si el SAS especificado para el trabajo es `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue`, la ubicación del registro sería `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport`|  
|SAS de contenedor|Valor especificado por el usuario|Un directorio virtual al que el usuario ha asignado un nombre, debajo del contenedor especificado en el SAS.<br /><br /> Por ejemplo, si el SAS especificado para el trabajo es `https://myaccount.blob.core.windows.net/mylogcontainer?sv=2012-02-12&se=2015-05-22T06%3A54%3A55Z&sr=c&sp=wl&sig=sigvalue` y el directorio virtual especificado se llama `mylogblobs`, la ubicación del registro sería `https://myaccount.blob.core.windows.net/mylogcontainer/waimportexport/mylogblobs`.|  
  
Para recuperar la dirección URL de los registros de errores y detallados, llame a la operación [Get Job](/rest/api/storageimportexport/jobs#Jobs_CreateOrUpdate). Los registros están disponibles una vez que haya finalizado el procesamiento de la unidad.  
  
## <a name="log-file-format"></a>Formato de archivo de registro  
El formato de ambos registros es el mismo: un blob que contiene descripciones XML de los eventos que se produjeron al copiar blobs entre el disco duro y la cuenta del cliente.  
  
El registro detallado contiene información completa acerca del estado de la operación de copia de todos los blobs (en un trabajo de importación) o archivos (en un trabajo de exportación), mientras que el registro de errores contiene solo la información de los blobs o archivos que encontraron errores durante el trabajo de importación o exportación.  
  
El formato del registro detallado se muestra a continuación. El registro de errores tiene la misma estructura, pero filtra las operaciones correctas.  

```xml
<DriveLog Version="2014-11-01">  
  <DriveId>drive-id</DriveId>  
  [<Blob Status="blob-status">  
   <BlobPath>blob-path</BlobPath>  
   <FilePath>file-path</FilePath>  
   [<Snapshot>snapshot</Snapshot>]  
   <Length>length</Length>  
   [<LastModified>last-modified</LastModified>]  
   [<ImportDisposition Status="import-disposition-status">import-disposition</ImportDisposition>]  
   [page-range-list-or-block-list]  
   [metadata-status]  
   [properties-status]  
  </Blob>]  
  [<Blob>  
    . . .  
  </Blob>]  
  <Status>drive-status</Status>  
</DriveLog>  
  
page-range-list-or-block-list ::= 
  page-range-list | block-list  
  
page-range-list ::=   
<PageRangeList>  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
      [<PageRange Offset="page-range-offset" Length="page-range-length"   
       [Hash="md5-hash"] Status="page-range-status"/>]  
</PageRangeList>  
  
block-list ::=  
<BlockList>  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]  
       [Hash="md5-hash"] Status="block-status"/>]  
      [<Block Offset="block-offset" Length="block-length" [Id="block-id"]   
       [Hash="md5-hash"] Status="block-status"/>]  
</BlockList>  
  
metadata-status ::=  
<Metadata Status="metadata-status">  
   [<GlobalPath Hash="md5-hash">global-metadata-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">metadata-file-path</Path>]  
</Metadata>  
  
properties-status ::=  
<Properties Status="properties-status">  
   [<GlobalPath Hash="md5-hash">global-properties-file-path</GlobalPath>]  
   [<Path Hash="md5-hash">properties-file-path</Path>]  
</Properties>  
```

En la siguiente tabla se describen los elementos del archivo de registro.  
  
|Elemento XML|Tipo|Descripción|  
|-----------------|----------|-----------------|  
|`DriveLog`|Elemento XML|Representa un registro de la unidad.|  
|`Version`|Attribute, String|La versión del formato del registro.|  
|`DriveId`|string|Número de serie del hardware de la unidad.|  
|`Status`|string|Estado del procesamiento de la unidad. Para más información, consulte la tabla `Drive Status Codes` siguiente.|  
|`Blob`|Elemento XML anidado|Representa un blob.|  
|`Blob/BlobPath`|string|El identificador URI del blob.|  
|`Blob/FilePath`|string|La ruta de acceso relativa al archivo en la unidad.|  
|`Blob/Snapshot`|DateTime|La versión de instantánea del blob, solo para trabajos de exportación.|  
|`Blob/Length`|Entero|La longitud total del blob, en bytes.|  
|`Blob/LastModified`|DateTime|La fecha y hora de la última modificación del blob, solo para trabajos de exportación.|  
|`Blob/ImportDisposition`|string|La disposición de importación del blob, solo para trabajos de importación.|  
|`Blob/ImportDisposition/@Status`|Attribute, String|El estado de la disposición de importación.|  
|`PageRangeList`|Elemento XML anidado|Representa una lista de los intervalos de páginas de un blob en páginas.|  
|`PageRange`|Elemento XML|Representa un intervalo de páginas.|  
|`PageRange/@Offset`|Attribute, Integer|Desplazamiento inicial del intervalo de páginas del blob.|  
|`PageRange/@Length`|Attribute, Integer|Longitud del intervalo de páginas, en bytes.|  
|`PageRange/@Hash`|Attribute, String|Hash MD5 con codificación Base16 del intervalo de páginas.|  
|`PageRange/@Status`|Attribute, String|Estado del procesamiento del intervalo de páginas.|  
|`BlockList`|Elemento XML anidado|Representa una lista de bloques de un blob en bloques.|  
|`Block`|Elemento XML|Representa un bloque.|  
|`Block/@Offset`|Attribute, Integer|Desplazamiento inicial del bloque del blob.|  
|`Block/@Length`|Attribute, Integer|Longitud del bloque, en bytes.|  
|`Block/@Id`|Attribute, String|El identificador del bloque.|  
|`Block/@Hash`|Attribute, String|Hash MD5 con codificación Base16 del bloque.|  
|`Block/@Status`|Attribute, String|Estado de procesamiento del bloque.|  
|`Metadata`|Elemento XML anidado|Representa los metadatos del blob.|  
|`Metadata/@Status`|Attribute, String|Estado de procesamiento de los metadatos del blob.|  
|`Metadata/GlobalPath`|string|Ruta de acceso relativa al archivo de metadatos global.|  
|`Metadata/GlobalPath/@Hash`|Attribute, String|Hash MD5 con codificación Base16 del archivo de metadatos global.|  
|`Metadata/Path`|string|Ruta de acceso relativa al archivo de metadatos.|  
|`Metadata/Path/@Hash`|Attribute, String|Hash MD5 con codificación Base16 del archivo de metadatos.|  
|`Properties`|Elemento XML anidado|Representa las propiedades del blob.|  
|`Properties/@Status`|Attribute, String|Estado de procesamiento de las propiedades del blob, por ejemplo, archivo no encontrado, completado.|  
|`Properties/GlobalPath`|String|Ruta de acceso relativa al archivo de propiedades global.|  
|`Properties/GlobalPath/@Hash`|Attribute, String|Hash MD5 con codificación Base16 del archivo de propiedades global.|  
|`Properties/Path`|string|Ruta de acceso relativa al archivo de propiedades.|  
|`Properties/Path/@Hash`|Attribute, String|Hash MD5 con codificación Base16 del archivo de propiedades.|  
|`Blob/Status`|String|Estado del procesamiento del blob.|  
  
# <a name="drive-status-codes"></a>Códigos de estado de unidad  
En la tabla siguiente se enumera los códigos de estado para procesar una unidad.  
  
|Código de estado|Descripción|  
|-----------------|-----------------|  
|`Completed`|La unidad ha finalizado el procesamiento sin errores.|  
|`CompletedWithWarnings`|La unidad ha finalizado el procesamiento con advertencias en uno o más blobs según las disposiciones de importación especificadas para los blobs.|  
|`CompletedWithErrors`|La unidad ha finalizado con errores en uno o varios blobs o fragmentos.|  
|`DiskNotFound`|No se encuentra ningún disco en la unidad.|  
|`VolumeNotNtfs`|El primer volumen de datos del disco no está en formato NTFS.|  
|`DiskOperationFailed`|Se produjo un error desconocido al realizar operaciones en la unidad.|  
|`BitLockerVolumeNotFound`|No se encuentra ningún volumen cifrable de BitLocker.|  
|`BitLockerNotActivated`|BitLocker no está habilitado en el volumen.|  
|`BitLockerProtectorNotFound`|El protector de la clave de la contraseña numérica no existe en el volumen.|  
|`BitLockerKeyInvalid`|La contraseña numérica especificada no puede desbloquear el volumen.|  
|`BitLockerUnlockVolumeFailed`|Se ha producido un error desconocido al intentar desbloquear el volumen.|  
|`BitLockerFailed`|Se produjo un error desconocido al realizar operaciones de BitLocker.|  
|`ManifestNameInvalid`|El nombre del archivo de manifiesto no es válido.|  
|`ManifestNameTooLong`|El nombre del archivo de manifiesto es demasiado largo.|  
|`ManifestNotFound`|No se encuentra el archivo de manifiesto.|  
|`ManifestAccessDenied`|Se ha denegado el acceso al archivo de manifiesto.|  
|`ManifestCorrupted`|El archivo de manifiesto está dañado (el contenido no coincide con su hash).|  
|`ManifestFormatInvalid`|El contenido del manifiesto no se ajusta al formato requerido.|  
|`ManifestDriveIdMismatch`|El identificador de la unidad en el archivo de manifiesto no coincide con el que se lee de la unidad.|  
|`ReadManifestFailed`|Se produjo un error de E/S de disco al leer del manifiesto.|  
|`BlobListFormatInvalid`|El blob de la lista de blobs de exportación no se ajusta al formato requerido.|  
|`BlobRequestForbidden`|Se prohíbe el acceso a los blobs en la cuenta de almacenamiento. Esto puede deberse a que la clave de la cuenta de almacenamiento o el SAS del contenedor no son válidos.|  
|`InternalError`|Se produjo un error interno al procesar la unidad.|  
  
## <a name="blob-status-codes"></a>Códigos de estado de blob  
En la tabla siguiente se enumera los códigos de estado para procesar un blob.  
  
|Código de estado|Descripción|  
|-----------------|-----------------|  
|`Completed`|El blob ha finalizado el procesamiento sin errores.|  
|`CompletedWithErrors`|El blob ha finalizado el procesamiento con errores en uno o varios intervalos de páginas o bloques, metadatos o propiedades.|  
|`FileNameInvalid`|El nombre de archivo no es válido.|  
|`FileNameTooLong`|El nombre de archivo es demasiado largo.|  
|`FileNotFound`|No se encuentra el archivo.|  
|`FileAccessDenied`|Se ha denegado el acceso al archivo.|  
|`BlobRequestFailed`|Error en la solicitud de Blob service para acceder al blob.|  
|`BlobRequestForbidden`|Se ha prohibido la solicitud de Blob service para acceder al blob. Esto puede deberse a que la clave de la cuenta de almacenamiento o el SAS del contenedor no son válidos.|  
|`RenameFailed`|No se pudo cambiar el nombre del blob (en el caso de un trabajo de importación) o del archivo (en el caso de un trabajo de exportación).|  
|`BlobUnexpectedChange`|Se ha producido un cambio inesperado con el blob (para un trabajo de exportación).|  
|`LeasePresent`|Hay una concesión en el blob.|  
|`IOFailed`|Se produjo un error de E/S de disco o de red durante el procesamiento del blob.|  
|`Failed`|Se produjo un error desconocido durante el procesamiento del blob.|  
  
## <a name="import-disposition-status-codes"></a>Códigos de estado de disposición de importación  
En la tabla siguiente se enumeran los códigos de estado para resolver una disposición de importación.  
  
|Código de estado|Descripción|  
|-----------------|-----------------|  
|`Created`|El blob se ha creado.|  
|`Renamed`|Se ha cambiado el nombre del blob según la disposición de importación del cambio de nombre. El elemento `Blob/BlobPath` contiene el identificador URI del blob cuyo nombre ha cambiado.|  
|`Skipped`|El blob se ha omitido según la disposición de importación `no-overwrite`.|  
|`Overwritten`|El blob ha sobrescrito un blob existente según la disposición de importación `overwrite`.|  
|`Cancelled`|Un error anterior ha detenido el procesamiento posterior de la disposición de importación.|  
  
## <a name="page-rangeblock-status-codes"></a>Códigos de estado de intervalo de páginas o bloque  
En la tabla siguiente se enumeran los códigos de estado para procesar un intervalo de páginas o bloque.  
  
|Código de estado|Descripción|  
|-----------------|-----------------|  
|`Completed`|El intervalo de páginas o bloque han finalizado el procesamiento sin errores.|  
|`Committed`|El bloque se ha confirmado, pero no en la lista completa de bloques completo porque se han producido errores en otros bloques o han aparecido errores al colocar la propia lista de bloques completa.|  
|`Uncommitted`|El bloque está cargado, pero no confirmado.|  
|`Corrupted`|El intervalo de páginas o bloque están dañados (el contenido no coincide con su hash).|  
|`FileUnexpectedEnd`|Se ha encontrado un final de archivo inesperado.|  
|`BlobUnexpectedEnd`|Se ha encontrado un final de blob inesperado.|  
|`BlobRequestFailed`|Error en la solicitud de Blob service para acceder al intervalo de páginas o bloque.|  
|`IOFailed`|Se produjo un error de E/S de disco o de red durante el procesamiento del intervalo de páginas o bloque.|  
|`Failed`|Se produjo un error desconocido durante el procesamiento del intervalo de páginas o bloque.|  
|`Cancelled`|Un error anterior ha detenido el procesamiento posterior del intervalo de páginas o bloque.|  
  
## <a name="metadata-status-codes"></a>Códigos de estado de metadatos  
En la tabla siguiente se enumera los códigos de estado para procesar los metadatos de un blob.  
  
|Código de estado|Descripción|  
|-----------------|-----------------|  
|`Completed`|Los metadatos han finalizado el procesamiento sin errores.|  
|`FileNameInvalid`|El nombre del archivo de metadatos no es válido.|  
|`FileNameTooLong`|El nombre del archivo de metadatos es demasiado largo.|  
|`FileNotFound`|No se encuentra el archivo de metadatos.|  
|`FileAccessDenied`|Se ha denegado el acceso al archivo de metadatos.|  
|`Corrupted`|El archivo de metadatos está dañado (el contenido no coincide con su hash).|  
|`XmlReadFailed`|El contenido de los metadatos no se ajusta al formato requerido.|  
|`XmlWriteFailed`|Error al escribir el XML de metadatos.|  
|`BlobRequestFailed`|Error en la solicitud de Blob service para acceder a los metadatos.|  
|`IOFailed`|Se produjo un error de E/S de disco o de red durante el procesamiento de los metadatos.|  
|`Failed`|Se produjo un error desconocido durante el procesamiento de los metadatos.|  
|`Cancelled`|Un error anterior ha detenido el procesamiento posterior de los metadatos.|  
  
## <a name="properties-status-codes"></a>Códigos de estado de las propiedades  
En la tabla siguiente se enumera los códigos de estado para procesar las propiedades de un blob.  
  
|Código de estado|Descripción|  
|-----------------|-----------------|  
|`Completed`|Las propiedades han finalizado el procesamiento sin errores.|  
|`FileNameInvalid`|El nombre del archivo de propiedades no es válido.|  
|`FileNameTooLong`|El nombre del archivo de propiedades es demasiado largo.|  
|`FileNotFound`|El archivo de propiedades no se encuentra.|  
|`FileAccessDenied`|Se ha denegado el acceso al archivo de propiedades.|  
|`Corrupted`|El archivo de propiedades está dañado (el contenido no coincide con su hash).|  
|`XmlReadFailed`|El contenido de las propiedades no se ajusta al formato requerido.|  
|`XmlWriteFailed`|Error al escribir el XML de propiedades.|  
|`BlobRequestFailed`|Error en la solicitud de Blob service para acceder a las propiedades.|  
|`IOFailed`|Se produjo un error de E/S de disco o de red durante el procesamiento de las propiedades.|  
|`Failed`|Se produjo un error desconocido durante el procesamiento de las propiedades.|  
|`Cancelled`|Un error anterior ha detenido el procesamiento posterior de las propiedades.|  
  
## <a name="sample-logs"></a>Registros de ejemplo  
A continuación se muestra un ejemplo de un registro detallado.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV123456</DriveId>  
    <Blob Status="Completed">  
       <BlobPath>pictures/bob/wild/desert.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\wild\desert.jpg</FilePath>  
       <Length>98304</Length>  
       <ImportDisposition Status="Created">overwrite</ImportDisposition>  
       <BlockList>  
          <Block Offset="0" Length="65536" Id="AAAAAA==" Hash=" 9C8AE14A55241F98533C4D80D85CDC68" Status="Completed"/>  
          <Block Offset="65536" Length="32768" Id="AQAAAA==" Hash=" DF54C531C9B3CA2570FDDDB3BCD0E27D" Status="Completed"/>  
       </BlockList>  
       <Metadata Status="Completed">  
          <GlobalPath Hash=" E34F54B7086BCF4EC1601D056F4C7E37">\Users\bob\Pictures\wild\metadata.xml</GlobalPath>  
       </Metadata>  
    </Blob>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="0" Length="65536" Hash="19701B8877418393CB3CB567F53EE225" Status="Completed"/>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
          <PageRange Offset="131072" Length="4096" Hash="9BA552E1C3EEAFFC91B42B979900A996" Status="Completed"/>  
       </PageRangeList>  
       <Properties Status="Completed">  
          <Path Hash="38D7AE80653F47F63C0222FEE90EC4E7">\Users\bob\Pictures\animals\koala.jpg.properties</Path>  
       </Properties>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
El registro de errores correspondiente se muestra a continuación.  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<DriveLog Version="2014-11-01">  
    <DriveId>WD-WMATV6965824</DriveId>  
    <Blob Status="CompletedWithErrors">  
       <BlobPath>pictures/bob/animals/koala.jpg</BlobPath>  
       <FilePath>\Users\bob\Pictures\animals\koala.jpg</FilePath>  
       <Length>163840</Length>  
       <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
       <PageRangeList>  
          <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted"/>  
       </PageRangeList>  
    </Blob>  
    <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

 El siguiente registro de errores de un trabajo de importación contiene un error sobre un archivo que no se encuentra en la unidad de importación. Tenga en cuenta que el estado de los componentes posteriores es `Cancelled`.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="FileNotFound">  
    <BlobPath>pictures/animals/koala.jpg</BlobPath>  
    <FilePath>\animals\koala.jpg</FilePath>  
    <Length>30310</Length>  
    <ImportDisposition Status="Cancelled">rename</ImportDisposition>  
    <BlockList>  
      <Block Offset="0" Length="6062" Id="MD5/cAzn4h7VVSWXf696qp5Uaw==" Hash="700CE7E21ED55525977FAF7AAA9E546B" Status="Cancelled" />  
      <Block Offset="6062" Length="6062" Id="MD5/PEnGwYOI8LPLNYdfKr7kAg==" Hash="3C49C6C18388F0B3CB35875F2ABEE402" Status="Cancelled" />  
      <Block Offset="12124" Length="6062" Id="MD5/FG4WxqfZKuUWZ2nGTU2qVA==" Hash="146E16C6A7D92AE5166769C64D4DAA54" Status="Cancelled" />  
      <Block Offset="18186" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
      <Block Offset="24248" Length="6062" Id="MD5/ZzibNDzr3IRBQENRyegeXQ==" Hash="67389B343CEBDC8441404351C9E81E5D" Status="Cancelled" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```

El siguiente registro de errores de un trabajo de exportación indica que el contenido del blob se ha escrito correctamente en la unidad, pero que se produjo un error al exportar las propiedades del blob.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog Version="2014-11-01">  
  <DriveId>9WM35C3U</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
    <FilePath>\pictures\wild\canyon.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList />  
    <Properties Status="Failed" />  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
## <a name="next-steps"></a>Pasos siguientes
 
* [API de REST de Storage Import/Export](/rest/api/storageimportexport/)
