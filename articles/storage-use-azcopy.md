<properties linkid="storage-use-azcopy" urlDisplayName="AzCopy" pageTitle="How to use AzCopy with Microsoft Azure Storage" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Learn how to use the AzCopy utility to upload, download, and copy blob and file content." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/06/2014" ms.author="tamram" />

# Introducción a la utilidad de línea de comandos AzCopy

AzCopy es una utilidad de línea de comandos diseñada para realizar operaciones de alto rendimiento de carga, descarga y copia de datos a Almacenamiento de blobs y archivos de Azure y desde dicho almacenamiento. Esta guía proporciona información general para usar AzCopy.

> [WACOM.NOTE] Esta guía asume que ha instalado AzCopy 2.5 o posterior. Tenga en cuenta que AzCopy se encuentra actualmente en versión preliminar, por lo que las opciones de la línea de comandos y sus funciones pueden cambiar en versiones futuras.

## Tabla de contenido

-   [Descarga e instalación de AzCopy][Descarga e instalación de AzCopy]
-   [Introducción a la sintaxis de la línea de comandos de AzCopy][Introducción a la sintaxis de la línea de comandos de AzCopy]
-   [Limitación de escrituras concurrentes mientras se copian datos][Limitación de escrituras concurrentes mientras se copian datos]
-   [Copia de blobs de Azure con AzCopy][Copia de blobs de Azure con AzCopy]
-   [Copia de archivos en un recurso compartido de archivos de Azure con AzCopy][Copia de archivos en un recurso compartido de archivos de Azure con AzCopy]
-   [Versiones de AzCopy][Versiones de AzCopy]
-   [Pasos siguientes][Pasos siguientes]

## <span id="install"></span></a> Descarga e instalación de AzCopy

1.  Descrgue la [versión más reciente de AzCopy][versión más reciente de AzCopy].
2.  Ejecución de la instalación. De forma predeterminada, la instalación de AzCopy crea una carpeta llamada `AzCopy` bajo `%ProgramFiles(x86)%\Microsoft SDKs\Azure\` (en una máquina con Windows de 64 bits) o `%ProgramFiles%\Microsoft SDKs\Azure\` (en una máquina con Windows de 32 bits). Sin embargo, puede cambiar la ruta de acceso de instalación desde el asistente para la instalación.
3.  Si lo desea, puede agregar la ubicación de instalación de AzCopy a la ruta de acceso del sistema.

## <span id="syntax"></span></a> Introducción a la sintaxis de la línea de comandos de AzCopy

A continuación, abra una ventana de comandos y navegue al directorio de instalación de AzCopy en el equipo, donde se encuentra el ejecutable `AzCopy.exe`. La sintaxis básica del comando AzCopy es:

    AzCopy <source> <destination> [filepattern] [options]

-   El parámetro `<source>` especifica los datos de origen desde los que copiar. El origen puede ser un directorio del sistema de archivos, un contenedor de blobs, un directorio virtual de blobs o un recurso compartido de archivos de almacenamiento.

-   El parámetro `<destination>` especifica el destino de la copia. El destino puede ser un directorio del sistema de archivos, un contenedor de blobs, un directorio virtual de blobs o un recurso compartido de archivos de almacenamiento.

-   El comportamiento del parámetro `filepattern` opcional viene determinado por la ubicación de los datos de origen y la presencia de la opción del modo recursivo. El modo recursivo viene especificado por la opción `/S`.

    Si el origen especificado es un directorio del sistema de archivos, se aplican los caracteres comodín estándar y el patrón de archivos proporcionado coincidirá con los archivos del directorio. Si se especifica la opción `/S`, entonces AzCopy también hará coincidir el patrón especificado con todos los archivos de cualquier subcarpeta que se encuentre bajo el directorio.

    Si el origen especificado es un contenedor de blobs o un directorio virtual, entonces los caracteres comodín no se aplican. Si se especifica la opción `/S`, entonces AzCopy interpreta el patrón de archivos especificado como un prefijo de blobs. Si la opción `/S` no se especifica, entonces AzCopy hará coincidir el patrón de archivos con los nombres de blob exactos.

    Si el origen especificado es un recurso compartido de archivos de Azure, entonces debe especificar el nombre de archivo exacto (*p. ej.* `abc.txt`) para copiar un solo archivo, o especificar la opción `/S` para copiar todos los archivos en el recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción `/S` conjuntamente, se producirá un error.

    El patrón de archivos predeterminado usado cuando no se especifica un patrón de archivos es `*.*` para un directorio del sistema de archivos, o un prefijo vacío para un recurso de blob o Almacenamiento de archivos de Azure.

    > [WACOM.NOTE] Debido a factores de rendimiento, la versión 2.5 de AzCopy ya no admite varios patrones de archivos en un solo comando. Ahora, debe emitir varios comandos, cada uno con un solo patrón de archivos, para hacer frente al escenario de varios patrones de archivos.

-   En la tabla siguiente se describen las opciones disponibles para el parámetro `options`. También puede escribir `AzCopy /?` desde la línea de comandos para obtener ayuda con las opciones.

<table>
  <tr>
    <th>Nombre de opci&oacute;n</th>
    <th>Descripci&oacute;n</th>
    <th>Aplicable a Almacenamiento de blobs (S/N)</th>
    <th>Aplicable al almacenamiento de archivos (S/N)</th>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;clave de almacenamiento&gt;</b></td>
    <td>Especifica la clave de cuenta de almacenamiento para el recurso de destino.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/DestSAS:&lt;SAS del contenedor&gt;</b></td>
    <td>Especifica una firma de acceso compartido (SAS) para el contenedor de destino (si se aplica). Encierre la SAS con comillas dobles, ya que puede contener caracteres de l&iacute;nea de comandos especiales.<br />
        Si el recurso de destino es un blob, puede especificar esta opci&oacute;n seguida por el token SAS, o bien puede especificar la SAS como parte del URI del blob de destino, sin esta opci&oacute;n.<br />
        Esta opci&oacute;n solo se aplica para Almacenamiento de blobs y solamente es v&aacute;lida cuando se cargan o copian blobs dentro de la misma cuenta de almacenamiento.</td>
    <td>S</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;clave de almacenamiento&gt;</b></td>
    <td>Especifica la clave de cuenta de almacenamiento para el recurso de origen.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;SAS del contenedor&gt;</b></td>
    <td>Especifica una firma de acceso compartido para el contenedor de origen (si se aplica). Encierre la SAS con comillas dobles, ya que puede contener caracteres de l&iacute;nea de comandos especiales. 
        <br />
        Si no se proporciona una clave ni una SAS, entonces el contenedor se leer&aacute; a trav&eacute;s de acceso an&oacute;nimo. 
        <br />
        Esta opci&oacute;n solamente se aplica para Almacenamiento de blobs.</td>
    <td>S</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Especifica el modo recursivo para operaciones de copia. En el modo recursivo, AzCopy copiar&aacute; todos los blobs o archivos que coincidan con el patr&oacute;n de archivos especificado, incluidos los que se encuentren en las subcarpetas.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>Especifica que el destino es un blob en bloques o un blob en p&aacute;ginas. Esta opci&oacute;n solamente se aplica cuando el destino es un blob; de lo contrario, se genera un error. Si el destino es un blob y esta opci&oacute;n no se especifica, AzCopy, de forma predeterminada, asumir&aacute; que se desea un blob en bloques.</td>
    <td>S</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMd5</b></td>
    <td>Calcula un hash MD5 para datos descargados y comprueba que el hash MD5 almacenado en la propiedad Content-MD5 del blob o archivo coincide con el hash calculado. La comprobaci&oacute;n MD5 se desactiva de forma predeterminada, por lo que debe especificar esta opci&oacute;n para realizar dicha comprobaci&oacute;n cuando se descargan datos.
    <br />
    Tenga en cuenta que Almacenamiento de Azure no garantiza que el hash MD5 almacenado para el blob o archivo est&eacute; actualizado. El cliente ser&aacute; el responsable de actualizar el MD5 siempre que el blob o el archivo se modifique.
    <br />
    AzCopy siempre establece la propiedad Content-MD5 para un blob o archivo de Azure despu&eacute;s de cargarlo en el servicio.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indica si se desean transferir instant&aacute;neas. Esta opci&oacute;n solo es v&aacute;lida cuando el origen es un blob. 
        <br />
        El nombre de las instant&aacute;neas del blob transferidas se cambia seg&uacute;n este formato: [nombre del blob] (tiempo de la instant&aacute;nea)[extensi&oacute;n]. 
        <br />
        De forma predeterminada, las instant&aacute;neas no se copian.</td>
    <td>S</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[Archivo de registro detallado]</b></td>
    <td>Env&iacute;a mensajes con el estado detallado a un archivo de registro. El nombre predeterminado del archivo de registro detallado es <code data-inline="1">AzCopyVerbose.log</code> en <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code>. Si especifica una ubicaci&oacute;n existente para el archivo en esta opci&oacute;n, el registro detallado se anexar&aacute; a dicho archivo.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/Z:[carpeta de archivos de diario]</b></td>
    <td>Especifica una carpeta de archivos de diario para reanudar una operaci&oacute;n.<br />
        AzCopy siempre admite la reanudaci&oacute;n si una operaci&oacute;n se ha interrumpido.<br />
        Si esta opci&oacute;n no se especifica, o si se especifica sin una ruta de acceso a la carpeta, AzCopy crear&aacute; el archivo de diario en la ubicaci&oacute;n predeterminada, que es <code data-inline="1">%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />
        Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especific&oacute; a trav&eacute;s de esta opci&oacute;n. Si el archivo de diario no existe en ning&uacute;n lugar, AzCopy trata la operaci&oacute;n como una nueva y genera un nuevo archivo de diario.
        <br />
        Si el archivo de diario existe, AzCopy comprobar&aacute; si la l&iacute;nea de comandos escrita coincide con la de dicho archivo. Si las dos l&iacute;neas de comandos coinciden, AzCopy reanudar&aacute; la operaci&oacute;n incompleta. Si no coinciden, se le preguntar&aacute; si desea sobrescribir el archivo de diario, iniciar una nueva operaci&oacute;n o cancelar la operaci&oacute;n actual. 
        <br />
        El archivo de diario se elimina cuando la operaci&oacute;n se completa correctamente.
        <br />
        Tenga en cuenta que la reanudaci&oacute;n de una operaci&oacute;n a partir de un archivo de diario creado por una versi&oacute;n anterior de AzCopy no se admite.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/@:archivo-de-respuesta</b></td>
    <td>Especifica un archivo que contiene par&aacute;metros. AzCopy procesa los par&aacute;metros del archivo como si hubieran sido especificados en la l&iacute;nea de comandos.<br /> 
        En un archivo de respuesta, puede especificar varios par&aacute;metros en una sola l&iacute;nea o especificar cada par&aacute;metro en su propia l&iacute;nea. Tenga en cuenta que un par&aacute;metro individual no puede ocupar varias l&iacute;neas. 
        <br />
        Los archivos de respuesta pueden incluir l&iacute;neas de comentario que comiencen con el s&iacute;mbolo <code data-inline="1">#</code>. 
        <br />
        Puede especificar varios archivos de respuesta. Sin embargo, tenga en cuenta que AzCopy no admite archivos de respuesta anidados.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Suprime todos los mensajes de confirmaci&oacute;n de AzCopy.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Especifica solamente una operaci&oacute;n de descripci&oacute;n; no se copian datos.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Establece la hora de la &uacute;ltima modificaci&oacute;n del archivo descargado para que coincida con la del blob o archivo de origen.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Excluye un recurso origen m&aacute;s nuevo. El recurso no se copiar&aacute; si el origen es m&aacute;s nuevo que el destino.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Excluye un recurso de origen m&aacute;s antiguo. El recurso no se copiar&aacute; si el recurso de origen es m&aacute;s antiguo que el destino.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Carga solamente archivos que tienen el atributo de archivado establecido.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Carga solamente archivos que tienen cualquiera de los atributos especificados establecido.<br />
        Los atributos disponibles son los siguientes:  
        <br />
        R&nbsp;&nbsp;&nbsp;Archivos de solo lectura
        <br />
        A&nbsp;&nbsp;&nbsp;Archivos listos para archivarse
        <br />
        S&nbsp;&nbsp;&nbsp;Archivos del sistema
        <br />
        H&nbsp;&nbsp;&nbsp;Archivos ocultos
        <br />
        C&nbsp;&nbsp;&nbsp;Archivos comprimidos
        <br />
        N&nbsp;&nbsp;&nbsp;Archivos normales
        <br />
        E&nbsp;&nbsp;&nbsp;Archivos cifrados
        <br />
        T&nbsp;&nbsp;&nbsp;Archivos temporales
        <br />
        O&nbsp;&nbsp;&nbsp;Archivos fuera de l&iacute;nea
        <br />
        I&nbsp;&nbsp;&nbsp;Archivos no indizados</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Excluye archivos que tienen cualquiera de los atributos especificados establecido.<br />
        Los atributos disponibles son los siguientes:  
        <br />
        R&nbsp;&nbsp;&nbsp;Archivos de solo lectura  
        <br />
        A&nbsp;&nbsp;&nbsp;Archivos listos para archivarse  
        <br />
        S&nbsp;&nbsp;&nbsp;Archivos del sistema  
        <br />
        H&nbsp;&nbsp;&nbsp;Archivos ocultos  
        <br />
        C&nbsp;&nbsp;&nbsp;Archivos comprimidos  
        <br />
        N&nbsp;&nbsp;&nbsp;Archivos normales  
        <br />
        E&nbsp;&nbsp;&nbsp;Archivos cifrados  
        <br />
        T&nbsp;&nbsp;&nbsp;Archivos temporales  
        <br />
        O&nbsp;&nbsp;&nbsp;Archivos fuera de l&iacute;nea  
        <br />
        I&nbsp;&nbsp;&nbsp;Archivos no indizados</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimitador&gt;</b></td>
    <td>Indica el car&aacute;cter delimitador usado para delimitar directorios virtuales en un nombre de blob.<br />
        De forma predeterminada, AzCopy usa / como car&aacute;cter delimitador. Sin embargo, AzCopy admite el uso de cualquier car&aacute;cter convencional (como @, # o %) como delimitador. Si necesita incluir uno de estos caracteres especiales en la l&iacute;nea de comandos, encierre el nombre de archivo entre comillas dobles. 
        <br />
        Esta opci&oacute;n solamente se aplica para descarga de blobs.</td>
    <td>S</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC</b></td>
    <td>Especifica el n&uacute;mero de operaciones simult&aacute;neas. 
        <br />
        El n&uacute;mero predeterminado es 8 veces el n&uacute;mero de procesadores de n&uacute;cleo que est&aacute;n en funcionamiento. Por tanto, si su PC tiene 8 n&uacute;cleos, de forma predeterminada AzCopy iniciar&aacute; 64 operaciones simult&aacute;neas.<br />
        Tenga en cuenta que los n&uacute;meros grandes de operaciones simult&aacute;neas en un entorno con poco ancho de banda pueden saturar la conexi&oacute;n de red, impidiendo que las operaciones se completen. Reduzca el n&uacute;mero de operaciones simult&aacute;neas bas&aacute;ndose en el ancho de banda de red disponible real.</td>
    <td>S</td>
    <td>S</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob</b></td>
    <td>Especifica que el recurso <code data-inline="1">source</code> es un blob disponible en el entorno de desarrollo local, que se ejecuta en el emulador de almacenamiento.</td>
    <td>S</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob</b></td>
    <td>Especifica que el recurso <code data-inline="1">destination</code> es un blob disponible en el entorno de desarrollo local, que se ejecuta en el emulador de almacenamiento.</td>
    <td>Y</td>
    <td>N</td>
  </tr>
</table>

## <span id="limit-writes"></span></a> Limitación de escrituras concurrentes mientras se copian datos

Cuando copie blobs o archivos con AzCopy, recuerde que otra aplicación puede estar modificando los datos mientras se copian. Si es posible, asegúrese de que los datos no se modifican durante la operación de copia. Por ejemplo, cuando copie un VHD asociado con una máquina virtual de Azure, asegúrese de que ninguna otra aplicación está escribiendo actualmente en el VHD. Alternativamente, puede crear una instantánea del VHD primero y, después, copiar la instantánea.

Si no puede impedir que otras aplicaciones escriban en blobs o archivos mientras se copian, recuerde que para cuando el trabajo finalice, los recursos copiados puede que ya no tengan una paridad total con los recursos de origen.

## <span id="copy-blobs"></span></a> Copia de blobs de Azure con AzCopy

Los ejemplos siguientes muestran diversos escenarios para copiar blobs con AzCopy.

### Copia de un solo blob

**Cargar un archivo desde el sistema de archivos a Almacenamiento de blobs:**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key abc.txt

**Descargar un blob desde Almacenamiento de blobs en el sistema de archivos:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

### Copia de un blob a través de la copia del lado del servidor

Cuando copia un blob dentro de una cuenta de almacenamiento o a través de cuentas de almacenamiento, se realiza una operación de copia del lado del servidor. Para obtener más información acerca de las operaciones de copia del lado del servidor, consulte [Introducción a la copia asincrónica de blobs entre cuentas][Introducción a la copia asincrónica de blobs entre cuentas].

**Copia de un blob dentro de una cuenta de almacenamiento:**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourcekey:key /destkey:key abc.txt 

**Copia de un blob entre cuentas de almacenamiento:**

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt

### Copia de un blob desde la región secundaria

Si la cuenta de almacenamiento tiene el almacenamiento con redundancia geográfica de acceso de lectura habilitado, puede copiar datos desde la región secundaria.

**Copia de un blob en la cuenta principal desde la secundaria:**

    AzCopy https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 https://myaccount2.blob.core.windows.net/mynewcontainer2 /sourcekey:key1 /destkey:key2 abc.txt

**Descarga de un blob de la secundaria en un archivo del sistema de archivos:**

    AzCopy https://myaccount-secondary.blob.core.windows.net/mynewcontainer C:\myfolder /sourcekey:key abc.txt

### Carga de un archivo en un nuevo contenedor de blobs o directorio virtual

**Carga de un archivo en un nuevo contenedor de blobs**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mynewcontainer /destkey:key abc.txt

Tenga en cuenta que si el contenedor de destino especificado no existe, AzCopy lo creará y cargará el archivo en él.

**Carga de un archivo en un nuevo directorio virtual de blobs**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer/vd /destkey:key abc.txt

Tenga en cuenta que si el directorio virtual especificado no existe, AzCopy cargará el archivo para incluir el directorio virtual en su nombre (*p. ej.*, `vd/abc.txt` en el ejemplo anterior).

### Descarga de un blob en una nueva carpeta

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key abc.txt

Si la carpeta `C:\myfolder` todavía no existe, AzCopy la creará en el sistema de archivos y descargará `abc.txt` en la nueva carpeta.

### Carga de archivos y subcarpetas de un directorio en un contenedor de forma recursiva

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /S

La especificación de la opción `/S` copiará el contenido del directorio especificado en Almacenamiento de blobs de forma recursiva, lo que significa que todas las subcarpetas y sus archivos también se copiarán. Por ejemplo, supongamos que los siguientes archivos residen en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de copia, el contenedor incluirá los siguientes archivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Carga de archivos desde un directorio a un contenedor de forma no recursiva

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key

Si no especifica la opción `/S` en la línea de comandos, AzCopy no realizará la copia de forma recursiva. Solamente se copiarán los archivos del directorio especificado; NO se copiará ninguna subcarpeta ni sus archivos. Por ejemplo, supongamos que los siguientes archivos residen en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de copia, el contenedor incluirá los siguientes archivos:

    abc.txt
    abc1.txt
    abc2.txt

### Descarga de todos los blobs de un contenedor a un directorio del sistema de archivos de forma recursiva

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /S

Supongamos que los siguientes blobs residen en el contenedor especificado:

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de copia, el directorio `C:\myfolder` incluirá los siguientes archivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\vd1\a.txt
    C:\myfolder\vd1\abcd.txt

### Descarga de blobs de un directorio de blobs virtual a un directorio del sistema de archivos de forma recursiva

    AzCopy https://myaccount.blob.core.windows.net/mycontainer/vd1/ C:\myfolder /sourcekey:key /S

Supongamos que los siguientes blobs residen en el contenedor especificado:

    abc.txt
    abc1.txt
    abc2.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de copia, el directorio `C:\myfolder` incluirá los siguientes archivos. Tenga en cuenta que solamente se copiarán los blobs del directorio virtual:

    C:\myfolder\a.txt
    C:\myfolder\abcd.txt

### Carga de archivos que coincidan con el patrón de archivos especificado en un contenedor de forma recursiva

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key a* /S

Supongamos que los siguientes archivos residen en la carpeta `C:\myfolder`:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt
    C:\myfolder\xyz.txt
    C:\myfolder\subfolder\a.txt
    C:\myfolder\subfolder\abcd.txt

Después de la operación de copia, el contenedor incluirá los siguientes archivos:

    abc.txt
    abc1.txt
    abc2.txt
    subfolder\a.txt
    subfolder\abcd.txt

### Descarga de blobs con el prefijo especificado al sistema de archivos de forma recursiva

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key a /S

Supongamos que los siguientes blobs residen en el contenedor especificado. Se copiarán todos los blobs que comiencen con el prefijo `a`:

    abc.txt
    abc1.txt
    abc2.txt
    xyz.txt
    vd1\a.txt
    vd1\abcd.txt

Después de la operación de copia, la carpeta `C:\myfolder` incluirá los siguientes archivos:

    C:\myfolder\abc.txt
    C:\myfolder\abc1.txt
    C:\myfolder\abc2.txt

Tenga en cuenta que el prefijo se aplica al directorio virtual, que forma la primera parte del nombre del blob. En el ejemplo mostrado anteriormente, el directorio virtual no coincide con el prefijo especificado, por lo que no se copia.

### Copia de un blob y sus instantáneas en otra cuenta de almacenamiento

    AzCopy https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 https://destaccount.blob.core.windows.net/mycontainer2 /sourcekey:key1 /destkey:key2 abc.txt /snapshot

Después de la operación de copia, el contenedor de destino incluirá el blob y sus instantáneas. Suponiendo que el blob del ejemplo anterior tiene dos instantáneas, el contenedor incluirá el siguiente blob e instantáneas:

    abc.txt
    abc (2013-02-25 080757).txt
    abc (2014-02-21 150331).txt

### Uso de un archivo de respuesta para especificar parámetros de línea de comandos

    AzCopy /@:"C:\myfolder\abc.txt"

Puede incluir cualquier parámetro de la línea de comandos de AzCopy en un archivo de respuesta. AzCopy procesa los parámetros del archivo como si hubieran sido especificados en la línea de comandos, realizando una sustitución directa con el contenido del archivo.

**Especificación de uno o varios archivos de respuesta de una sola línea**

Imaginemos un archivo de respuesta llamado `source.txt` que especifica un contenedor de origen:

    http://myaccount.blob.core.windows.net/mycontainer

Y un archivo de respuesta llamado `dest.txt` que especifica una carpeta de destino en el sistema de archivos:

    C:\myfolder

Y un archivo de respuesta llamado `options.txt` que especifica opciones para AzCopy:

    /S /Y

Para llamar a AzCopy con estos archivos de respuesta, todos los cuales residen en un directorio `C:\responsefiles`, use este comando:

    AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /sourcekey:[sourcekey] /@:"C:\responsefiles\options.txt"   

AzCopy procesa este comando como si se incluyeran todos los parámetros individuales en la línea de comandos:

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

**Especificación de un archivo de respuesta de varias líneas**

Imaginemos un archivo de respuesta llamado `copyoperation.txt`, que contiene las siguientes líneas. Cada parámetro de AzCopy se especifica en su propia línea:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:[sourcekey]
    /S 
    /Y

Para llamar a AzCopy con este archivo de respuesta, use este comando:

    AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy procesa este comando como si se incluyeran todos los parámetros individuales en la línea de comandos:

    AzCopy http://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:[sourcekey] /S /Y

Tenga en cuenta que cada parámetro de AzCopy se debe especificar completamente en una línea. Por ejemplo, AzCopy no se procesará si divide el parámetro en dos líneas, tal y como se muestra aquí para el parámetro `/sourcekey`:

    http://myaccount.blob.core.windows.net/mycontainer
    C:\myfolder
    /sourcekey:
    [sourcekey]
    /S 
    /Y

### Especificación de una firma de acceso compartido (SAS)

**Especificación de una SAS para el contenedor de origen usando la opción /sourceSAS**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 C:\myfolder /sourceSAS:SAS /S

**Especificación de una SAS para el contenedor de origen en el URI de dicho contenedor**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken C:\myfolder /S

**Especificación de una SAS para el contenedor de destino usando la opción /destSAS**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer1 /destSAS:SAS abc.txt

**Especificación de una SAS para los contenedores de origen y destino**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer1 https://myaccount.blob.core.windows.net/mycontainer2 /sourceSAS:SAS1 /destSAS:SAS2 abc.txt

### Especificación de una carpeta de archivo de diario

Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especificó a través de esta opción. Si el archivo de diario no existe en ningún lugar, AzCopy trata la operación como una nueva y genera un nuevo archivo de diario.

Si el archivo de diario existe, AzCopy comprobará si la línea de comandos escrita coincide con la de dicho archivo. Si las dos líneas de comandos coinciden, AzCopy reanudará la operación incompleta. Si no coinciden, se le preguntará si desea sobrescribir el archivo de diario, iniciar una nueva operación o cancelar la operación actual.

**Utilización de la ubicación predeterminada para el archivo de diario**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z

Si omite la opción `/Z` o especifica la opción `/Z` sin la ruta de acceso a la carpeta, tal y como se muestra anteriormente, AzCopy crea el archivo de diario en la ubicación predeterminada, que es `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`. Si el archivo de diario ya existe, AzCopy reanuda la operación basándose en dicho archivo.

**Especificación de una ubicación personalizada para el archivo de diario**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /Z:C:\journalfolder\

Este ejemplo crea el archivo de diario si todavía no existe. Si no existe, AzCopy reanuda la operación basándose en el archivo de diario.

**Reanudación de una operación de AzCopy**

    AzCopy /Z:C:\journalfolder\

Este ejemplo reanuda la última operación que es posible que no terminara.

### Generación de un archivo de registro

**Escritura del archivo de registro detallado en la ubicación predeterminada**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V

Si especifica la opción `/V` sin proporcionar una ruta de acceso de archivo al registro detallado, AzCopy crea el archivo de registro en la ubicación predeterminada, que es `%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy`.

**Escritura del archivo de registro detallado en una ubicación predeterminada**

    AzCopy C:\myfolder https://myaccount.blob.core.windows.net/mycontainer /destkey:key /V:C:\myfolder\azcopy1.log

Tenga en cuenta que si especifica una ruta de acceso relativa después de la opción `/V`, como por ejemplo `/V:test/azcopy1.log`, el registro detallado se creará en el directorio de trabajo actual dentro de una subcarpeta llamada `test`.

### Establecimiento de la hora de la última modificación de los archivos descargados para que coincida con la de los blobs de origen

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT

### Exclusión de los blobs de la operación de copia basándose en la hora de su última modificación

Especifique la opción `/MT` para comparar la hora de la última modificación del blob de origen y el archivo de destino.

**Exclusión de blobs que son más nuevos que el archivo de destino**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XN

**Exclusión de blobs que son más antiguos que el archivo de destino**

    AzCopy https://myaccount.blob.core.windows.net/mycontainer C:\myfolder /sourcekey:key /MT /XO

### Especificación del número de operaciones simultáneas para iniciar

La opción `/NC` especifica el número de operaciones de copia simultáneas. De forma predeterminada, AzCopy iniciará operaciones simultáneas a ocho veces el número de procesadores de núcleo que tiene. Si ejecuta AzCopy en una red con un ancho de banda bajo, puede especificar un número bajo para esta opción para evitar errores provocados por la competencia de recursos.

### Ejecución de AzCopy en recursos de blob en el emulador de almacenamiento

    AzCopy https://127.0.0.1:10004/myaccount/myfileshare/ C:\myfolder /SourceKey:key /SourceType:Blob /S

## <span id="copy-files"></span></a> Copia de archivos en un recurso compartido de archivos de Azure con AzCopy

Los ejemplos siguientes muestran diversos escenarios para copiar archivos de Azure con AzCopy.

### Descarga de un archivo desde un recurso compartido de archivos de Azure en el sistema de archivos

    AzCopy https://myaccount.file.core.windows.net/myfileshare/myfolder1/ C:\myfolder /SourceKey:key abc.txt

Tenga en cuenta que si el origen especificado es un recurso compartido de archivos de Azure, entonces debe especificar el nombre de archivo exacto (*p. ej.* `abc.txt`) para copiar un solo archivo, o especificar la opción `/S` para copiar todos los archivos en el recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción `/S` conjuntamente, se producirá un error.

### Descarga de archivos y carpetas de un recurso compartido de archivos de Azure en el sistema de archivos de forma recursiva

    AzCopy https://myaccount.file.core.windows.net/myfileshare/ C:\myfolder /SourceKey:key /S

Tenga en cuenta que ninguna carpeta vacía se copiará.

### Carga de archivos y carpetas desde el sistema de archivos en un recurso compartido de archivos de Azure

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Tenga en cuenta que ninguna carpeta vacía se copiará.

### Carga de archivos que coincidan con el patrón de archivos especificado en un recurso compartido de Azure de forma recursiva

    AzCopy C:\myfolder https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key ab* /S

## <span id="versions"></span></a> Versiones de AzCopy

| Versión    | Novedades                                                                                                                                                    |
|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **V2.5.0** | **Versión actual. Optimiza el rendimiento de escenarios de copia a gran escala e introduce varias mejoras de facilidad de uso importantes.**                 |
| V2.4.1     | Admite la especificación de la carpeta de destino en el asistente para la instalación.                                                                       |
| V2.4.0     | Admite la carga y descarga de archivos para Almacenamiento de archivos de Azure.                                                                             |
| V2.3.0     | Admite cuentas de almacenamiento con redundancia geográfica de acceso de lectura.                                                                            |
| V2.2.2     | Actualizada para usar la versión 3.0.3 de la biblioteca del cliente de almacenamiento de Azure.                                                              |
| V2.2.1     | Problema de rendimiento solucionado cuando se copian grandes cantidades de archivos dentro de la misma cuenta de almacenamiento.                             |
| V2.2       | Admite el establecimiento del delimitador de directorios virtuales para nombres de blob. Admite la especificación de la ruta de acceso al archivo de diario. |
| V2.1       | Proporciona más de 20 opciones para admitir operaciones de carga, descarga y copia de blobs de una forma diferente.                                          |

## <span id="next-steps"></span></a>Pasos siguientes

Para obtener más información acerca de Almacenamiento de Azure y AzCopy, consulte los recursos siguientes.

### Documentación de Almacenamiento de Azure:

-   [Introducción a Almacenamiento de Azure][Introducción a Almacenamiento de Azure]
-   [Almacenamiento de archivos en Almacenamiento de blobs][Almacenamiento de archivos en Almacenamiento de blobs]
-   [Creación de un recurso compartido de archivos SMB en Azure con Almacenamiento de archivos][Creación de un recurso compartido de archivos SMB en Azure con Almacenamiento de archivos]

### Publicaciones en blobs de Almacenamiento de Azure

-   [Introducción al servicio de archivos de Microsoft Azure][Introducción al servicio de archivos de Microsoft Azure]
-   [AzCopy 2.5: Optimizada para escenarios de copia a gran escala][AzCopy 2.5: Optimizada para escenarios de copia a gran escala]
-   [AzCopy: Compatibilidad para almacenamiento con redundancia geográfica de acceso de lectura][AzCopy: Compatibilidad para almacenamiento con redundancia geográfica de acceso de lectura]
-   [AzCopy: Transferencia de datos con modo reiniciable y token SAS][AzCopy: Transferencia de datos con modo reiniciable y token SAS]
-   [AzCopy: Uso de copia de blobs entre cuentas][AzCopy: Uso de copia de blobs entre cuentas]
-   [AzCopy: Carga y descarga de archivos para blobs de Windows Azure][AzCopy: Carga y descarga de archivos para blobs de Windows Azure]

  [Descarga e instalación de AzCopy]: #install
  [Introducción a la sintaxis de la línea de comandos de AzCopy]: #syntax
  [Limitación de escrituras concurrentes mientras se copian datos]: #limit-writes
  [Copia de blobs de Azure con AzCopy]: #copy-blobs
  [Copia de archivos en un recurso compartido de archivos de Azure con AzCopy]: #copy-files
  [Versiones de AzCopy]: #versions
  [Pasos siguientes]: #next-steps
  [versión más reciente de AzCopy]: http://aka.ms/downloadazcopy
  [Introducción a la copia asincrónica de blobs entre cuentas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx
  [Introducción a Almacenamiento de Azure]: http://azure.microsoft.com/es-es/documentation/articles/storage-introduction/
  [Almacenamiento de archivos en Almacenamiento de blobs]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-blobs/
  [Creación de un recurso compartido de archivos SMB en Azure con Almacenamiento de archivos]: http://azure.microsoft.com/es-es/documentation/articles/storage-dotnet-how-to-use-files/
  [Introducción al servicio de archivos de Microsoft Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx
  [AzCopy 2.5: Optimizada para escenarios de copia a gran escala]: http://go.microsoft.com/fwlink/?LinkId=507682
  [AzCopy: Compatibilidad para almacenamiento con redundancia geográfica de acceso de lectura]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx
  [AzCopy: Transferencia de datos con modo reiniciable y token SAS]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx
  [AzCopy: Uso de copia de blobs entre cuentas]: http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx
  [AzCopy: Carga y descarga de archivos para blobs de Windows Azure]: http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx
