<properties urlDisplayName="AzCopy" pageTitle="Uso de AzCopy con Almacenamiento de Microsoft Azure" metaKeywords="Get started Azure AzCopy   Azure unstructured data   Azure unstructured storage   Azure blob   Azure blob storage   Azure file   Azure file storage   Azure file share   AzCopy" description="Aprenda a utilizar la herramienta AzCopy para cargar, descargar y copiar contenidos de blobs y archivos." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="storage" documentationCenter="" title="How to use AzCopy with Microsoft Azure Storage" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/17/2014" ms.author="tamram" />

# Introducción a la utilidad de línea de comandos AzCopy

AzCopy es una utilidad de línea de comandos diseñada para realizar operaciones de alto rendimiento de carga, descarga y copia de datos a y desde Almacenamiento de blobs, archivos y tablas de Microsoft Azure. Esta guía proporciona información general para usar AzCopy.

> [WACOM.NOTE] Esta guía asume que ha instalado AzCopy 3.0.0 o posterior. AzCopy 3.x ahora se encuentra diponible a nivel general.<br /> 
> En esta guía también se aborda la utilización de AzCopy 4.0.0, que es una versión en vista previa de AzCopy. Asimismo, en la guía, las funciones proporcionadas solo en la versión de vista previa se designan como *vista previa*.<br />
> Tenga en cuenta que, para AzCopy 4.x, las opciones de la línea de comandos y la funcionalidad pueden cambiar en versiones futuras.

## Tabla de contenido

- [Descarga e instalación de AzCopy](#install)
- [Introducción a la sintaxis de la línea de comandos de AzCopy](#syntax)
- [Limitación de escrituras concurrentes mientras se copian datos](#limit-writes)
- [Copia de blobs de Azure con AzCopy](#copy-blobs)
- [Copia de archivos en un recurso compartido de archivos de Azure con AzCopy &#40;solo versión de vista previa&#41;](#copy-files)
- [Copia de entidades de tabla de Azure con AzCopy &#40;solo versión de vista previa&#41;](#copy-entities)
- [Versiones de AzCopy](#versions)
- [Pasos siguientes](#next-steps)

## <a id="install"></a> Descarga e instalación de AzCopy

1. Descrgue la [versión más reciente de AzCopy](http://aka.ms/downloadazcopy) o la [última versión de vista previa](http://aka.ms/downloadazcopypr).
2. Ejecución de la instalación. De forma predeterminada, la instalación de AzCopy crea una carpeta denominada "AzCopy" en "%Archivos de programa(x86)%\Microsoft SDKs\Azure\" (en un equipo con Windows de 64 bits) o "%Archivos de programa%\Microsoft SDKs\Azure\" (en un equipo con Windows de 32 bits). Sin embargo, puede cambiar la ruta de acceso de instalación desde el asistente para la instalación.
3. Si lo desea, puede agregar la ubicación de instalación de AzCopy a la ruta de acceso del sistema.

## <a id="syntax"></a> Introducción a la sintaxis de la línea de comandos de AzCopy

A continuación, abra una ventana de comandos y navegue al directorio de instalación de AzCopy en el equipo, donde se encuentra el ejecutable "AzCopy.exe". La sintaxis básica del comando AzCopy es:

	AzCopy /Source:<source> /Dest:<destination> /Pattern:<filepattern> [Options]

> [WACOM.NOTE] A partir de la versión 3.0.0 de AzCopy, la sintaxis de línea de comandos de AzCopy requiere que se especifiquen todos los parámetros para incluir su nombre; *por ejemplo*, "/ParameterName:ParameterValue".

En la tabla siguiente se describen los parámetros para AzCopy. También puede escribir uno de los siguientes comandos desde la línea de comandos para obtener ayuda en el uso de AzCopy:

- Para obtener ayuda detallada sobre la línea de comandos para AzCopy: "AzCopy /?"
- Para obtener ayuda detallada con algún parámetro de AzCopy: "AzCopy /?:SourceKey"
- Para obtener ejemplos de línea de comandos: "AzCopy /?:Samples" 


<table>
  <tr>
    <th>Nombre de opción</th>
    <th>Descripción</th>
    <th>Aplicable a Almacenamiento de blobs (S/N)</th>
    <th>Aplicable al almacenamiento de archivos (S/N) (solo versión de vista previa)</th>
    <th>Aplicable al almacenamiento de tablas (S/N) (solo versión de vista previa)</th>
  </tr>
  <tr>
    <td><b>/Source:&lt;source&gt;</b></td>
    <td>Especifica los datos de origen desde los que copiar. El origen puede ser un directorio del sistema de archivos, un contenedor de blobs, un directorio virtual de blobs, un recurso compartido de archivos de almacenamiento, un directorio de archivos de almacenamiento o una tabla de Azure.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/Dest:&lt;destination&gt;</b></td>
    <td>Especifica el destino de la copia. El destino puede ser un directorio del sistema de archivos, un contenedor de blobs, un directorio virtual de blobs, un recurso compartido de archivos de almacenamiento, un directorio de archivos de almacenamiento o una tabla de Azure.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/Pattern:&lt;file-pattern&gt;</b></td>
      <td>
          Especifica un patrón de archivos que indica qué archivos se van a copiar. El comportamiento del parámetro /Pattern viene determinado por la ubicación de los datos de origen y la presencia de la opción del modo recursivo. El modo recursivo viene especificado por la opción /S.
          <br />
          Si el origen especificado es un directorio del sistema de archivos, se aplican los caracteres comodín estándar y el patrón de archivos proporcionado coincidirá con los archivos del directorio. Si se especifica la opción /S, AzCopy también hará coincidir el patrón especificado con todos los archivos de cualquier subcarpeta que se encuentre bajo el directorio.
          <br />
          Si el origen especificado es un contenedor de blobs o un directorio virtual, entonces los caracteres comodín no se aplican. Si se especifica la opción&nbsp;/S&nbsp;, AzCopy interpreta el patrón de archivos especificado como un prefijo de blobs. Si la opción&nbsp;/S&nbsp;no se especifica, AzCopy hará coincidir el patrón de archivos con los nombres de blob exactos.
          <br />
          Si el origen especificado es un recurso compartido de archivos de Azure, debe especificar el nombre de archivo exacto (p. ej. &nbsp;abc.txt) para copiar un solo archivo, o especificar la opción&nbsp;/S&nbsp;para copiar todos los archivos en el recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción /S&nbsp;conjuntamente, se producirá un error.
          <br />
          El patrón de archivos predeterminado usado cuando no se especifica un patrón de archivos es *.* para una ubicación del sistema de archivos, o un prefijo vacío para una ubicación de Almacenamiento de Azure. No se admite la especificación de varios patrones de archivos.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/DestKey:&lt;storage-key&gt;</b></td>
    <td>Especifica la clave de cuenta de almacenamiento para el recurso de destino.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td class="auto-style1"><b>/DestSAS:&lt;sas-token&gt;</b></td>
    <td class="auto-style1">Especifica una firma de acceso compartido (SAS) para el contenedor de destino (si se aplica). Encierre la SAS con comillas dobles, ya que puede contener caracteres de línea de comandos especiales.<br />
        Si el recurso de destino es una tabla o un contenedor de blobs, puede especificar esta opción seguida por el token SAS, o bien puede especificar la SAS como parte del URI del blob de destino, sin esta opción.<br />
        Si el origen y el destino son blobs en ambos casos, el blob de destino debe residir dentro de la misma cuenta de almacenamiento que el blob de origen.</td>
    <td class="auto-style1">Y</td>
    <td class="auto-style1">N</td>
    <td class="auto-style1">Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/SourceKey:&lt;storage-key&gt;</b></td>
    <td>Especifica la clave de cuenta de almacenamiento para el recurso de origen.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/SourceSAS:&lt;sas-token&gt;</b></td>
    <td>Especifica una firma de acceso compartido para el contenedor de origen (si se aplica). Encierre la SAS con comillas dobles, ya que puede contener caracteres de línea de comandos especiales.
        <br />
        Si el recurso de origen es un contenedor de blobs, y no se proporciona una clave ni una SAS, el contenedor de blobs se leerá a través de acceso anónimo.
        <br />
        Si el origen es una tabla, es necesario proporcionar una clave o una SAS.</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/S</b></td>
    <td>Especifica el modo recursivo para operaciones de copia. En el modo recursivo, AzCopy copiará todos los blobs o archivos que coincidan con el patrón de archivos especificado, incluidos los que se encuentren en las subcarpetas.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/BlobType:&lt;block | page&gt;</b></td>
    <td>Especifica si el blob de destino es un blob en bloques o un blob en páginas. Esta opción solamente es aplicable cuando se carga un blob; de lo contrario, se genera un error. Si el destino es un blob y esta opción no se especifica, AzCopy, de forma predeterminada, creará un blob en bloques.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/CheckMD5</b></td>
    <td>Calcula un hash MD5 para datos descargados y comprueba que el hash MD5 almacenado en la propiedad Content-MD5 del blob o archivo coincide con el hash calculado. La comprobación MD5 se desactiva de forma predeterminada, por lo que debe especificar esta opción para realizar dicha comprobación cuando se descargan datos.
	<br />
    Tenga en cuenta que Almacenamiento de Azure no garantiza que el hash MD5 almacenado para el blob o archivo esté actualizado. El cliente será el responsable de actualizar el MD5 siempre que el blob o el archivo se modifique.
	<br />
    AzCopy siempre establece la propiedad Content-MD5 para un blob o archivo de Azure después de cargarlo en el servicio.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Snapshot</b></td>
    <td>Indica si se desean transferir instantáneas. Esta opción solo es válida cuando el origen es un blob. 
        <br />
        El nombre de las instantáneas del blob transferidas se cambia según este formato: [nombre del blob] (tiempo de la instantánea)[extensión]. 
        <br />
        De forma predeterminada, las instantáneas no se copian.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/V:[Archivo de registro detallado]</b></td>
    <td>Envía mensajes con el estado detallado a un archivo de registro. El nombre predeterminado del archivo de registro detallado es <code>AzCopyVerbose.log</code> en <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>. Si especifica una ubicación existente para el archivo en esta opción, el registro detallado se anexará a dicho archivo.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/Z:[carpeta de archivos de diario]</b></td>
    <td>Especifica una carpeta de archivos de diario para reanudar una operación.<br />
        AzCopy siempre admite la reanudación si una operación se ha interrumpido.<br />
        Si esta opción no se especifica, o si se especifica sin una ruta de acceso a la carpeta, AzCopy creará el archivo de diario en la ubicación predeterminada, que es <code>%LocalAppData%\Microsoft\Azure\AzCopy</code>.<br />
        Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especificó a través de esta opción. Si el archivo de diario no existe en ningún lugar, AzCopy trata la operación como una nueva y genera un nuevo archivo de diario.
        <br />
		Si el archivo de diario existe, AzCopy comprobará si la línea de comandos escrita coincide con la de dicho archivo. Si las dos líneas de comandos coinciden, AzCopy reanudará la operación incompleta. Si no coinciden, se le preguntará si desea sobrescribir el archivo de diario, iniciar una nueva operación o cancelar la operación actual. 
        <br />
        El archivo de diario se elimina cuando la operación se completa correctamente.
		<br />
		Tenga en cuenta que la reanudación de una operación a partir de un archivo de diario creado por una versión anterior de AzCopy no se admite.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/@:parameter-file</b></td>
    <td>Especifica un archivo que contiene parámetros. AzCopy procesa los parámetros del archivo como si hubieran sido especificados en la línea de comandos.<br /> 
		En un archivo de respuesta, puede especificar varios parámetros en una sola línea o especificar cada parámetro en su propia línea. Tenga en cuenta que un parámetro individual no puede ocupar varias líneas. 
        <br />
		Los archivos de respuesta pueden incluir líneas de comentario que comiencen con el símbolo <code>#</code> . 
        <br />
        Puede especificar varios archivos de respuesta. Sin embargo, tenga en cuenta que AzCopy no admite archivos de respuesta anidados.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/Y</b></td>
    <td>Suprime todos los mensajes de confirmación de AzCopy.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/L</b></td>
    <td>Especifica solamente una operación de descripción; no se copian datos.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/MT</b></td>
    <td>Establece la hora de la última modificación del archivo descargado para que coincida con la del blob o archivo de origen.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XN</b></td>
    <td>Excluye un recurso origen más nuevo. El recurso no se copiará si el origen es más nuevo que el destino.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XO</b></td>
    <td>Excluye un recurso de origen más antiguo. El recurso no se copiará si el recurso de origen es más antiguo que el destino.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/A</b></td>
    <td>Carga solamente archivos que tienen el atributo de archivado establecido.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/IA:[RASHCNETOI]</b></td>
    <td>Carga solamente archivos que tienen cualquiera de los atributos especificados establecido.<br />
        Los atributos disponibles son los siguientes:  
        <br />
        R&nbsp;&nbsp;&nbsp;Archivos de solo lectura
        <br />
        A&nbsp;&nbsp;&nbsp;Archivos preparados para almacenamiento
        <br />
        S&nbsp;&nbsp;&nbsp;Archivos del sistema
        <br />
        H&nbsp;&nbsp;&nbsp;Archivos ocultos
        <br />
        C&nbsp;&nbsp;&nbsp;Archivo comprimido
        <br />
        N&nbsp;&nbsp;&nbsp;Archivos normales
        <br />
        E&nbsp;&nbsp;&nbsp;Archivos cifrados
        <br />
        T&nbsp;&nbsp;&nbsp;Archivos temporales
        <br />
        O&nbsp;&nbsp;&nbsp;Archivos sin conexión
        <br />
        I&nbsp;&nbsp;&nbsp;Archivos no indizados</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/XA:[RASHCNETOI]</b></td>
    <td>Excluye archivos que tienen cualquiera de los atributos especificados establecido.<br />
        Los atributos disponibles son los siguientes:  
        <br />
        R&nbsp;&nbsp;&nbsp;Archivos de solo lectura  
        <br />
        A&nbsp;&nbsp;&nbsp;Archivos preparados para almacenamiento  
        <br />
        S&nbsp;&nbsp;&nbsp;Archivos del sistema  
        <br />
        H&nbsp;&nbsp;&nbsp;Archivos ocultos  
        <br />
        C&nbsp;&nbsp;&nbsp;Archivo comprimido  
        <br />
        N&nbsp;&nbsp;&nbsp;Archivos normales  
        <br />
        E&nbsp;&nbsp;&nbsp;Archivos cifrados  
        <br />
        T&nbsp;&nbsp;&nbsp;Archivos temporales  
        <br />
        O&nbsp;&nbsp;&nbsp;Archivos sin conexión  
        <br />
        I&nbsp;&nbsp;&nbsp;Archivos no indizados</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/Delimiter:&lt;delimiter&gt;</b></td>
    <td>Indica el carácter delimitador usado para delimitar directorios virtuales en un nombre de blob.<br />
        De forma predeterminada, AzCopy usa / como carácter delimitador. Sin embargo, AzCopy admite el uso de cualquier carácter convencional (como @, # o %) como delimitador. Si necesita incluir uno de estos caracteres especiales en la línea de comandos, encierre el nombre de archivo entre comillas dobles. 
        <br />
        Esta opción solamente se aplica para descarga de blobs.</td>
    <td>Y</td>
    <td>N</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/NC:&lt;number-of-concurrents&gt;</b></td>
    <td>Especifica el número de operaciones simultáneas.
        <br />
        AzCopy inicia de manera predeterminada un número determinado de operaciones simultáneas para aumentar el rendimiento de la transferencia de datos. Tenga en cuenta que un número elevado de operaciones simultáneas en un entorno con poco ancho de banda puede saturar la conexión de red e impedir que las operaciones se completen. Reduzca el número de operaciones simultáneas basándose en el ancho de banda de red disponible real.
        <br />
		El límite máximo de operaciones simultáneas es de 512.</td>
    <td>Y</td>
    <td>Y<br /> (solo vista previa)</td>
    <td>N</td>
  </tr>
  <tr>
    <td><b>/SourceType:Blob|Table</b></td>
    <td>Especifica que el recurso <code>de origen</code> es un blob disponible en el entorno de desarrollo local, que se ejecuta en el emulador de almacenamiento.</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/DestType:Blob|Table</b></td>
    <td>Especifica que el recurso <code>de destino</code> es un blob disponible en el entorno de desarrollo local, que se ejecuta en el emulador de almacenamiento.</td>
    <td>Y</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><strong>/PKRS:&lt;&quot;key1#key2#key3#...&quot;&gt;</strong></td>
    <td>Divide el rango de claves de partición para habilitar la exportación de datos de tablas en paralelo, lo que aumenta la velocidad de la operación de exportación.
        <br />
        Si esta opción no se especifica, AzCopy utiliza un solo subproceso para exportar las entidades de tabla. Por ejemplo, si el usuario especifica /PKRS:&quot;aa#bb&quot;, AzCopy inicia tres operaciones simultáneas.
        <br />
        Cada operación exporta uno de los tres rangos de claves de partición, como se muestra a continuación: 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;&lt;primera clave de partición&gt;, aa&#41; 
        <br />
        &nbsp;&nbsp;&nbsp;&#91;aa, bb&#41;
        <br />
        &nbsp;&nbsp;&nbsp;&#91;bb, &lt;última clave de partición&gt;&#93; </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><strong>/SplitSize:</strong><file-size><strong>&lt;file-size&gt;</strong></td>
    <td>Especifica el tamaño de la división del archivo exportado en MB.
        <br />
        Si esta opción no se especifica, AzCopy exportará los datos de la tabla a un único archivo.
        <br />
        Si los datos de la tabla se exportan a un blob, y el tamaño del archivo exportado alcanza el límite de 200 GB establecido para el tamaño de un blob, AzCopy dividirá el archivo exportado, incluso si no se ha especificado esta opción. </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/EntityOperation:&lt;InsertOrSkip | InsertOrMerge | InsertOrReplace&gt;
</b>
</td>
    <td>Especifica el comportamiento de la importación de los datos de la tabla.
        <br />
        InsertOrSkip: omite una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
        <br />
        InsertOrMerge: combina una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
        <br />
        InsertOrReplace: reemplaza una entidad existente o inserta una nueva entidad si esta no existe en la tabla. </td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
  <tr>
    <td><b>/Manifest:&lt;manifest-file&gt;</b></td>
    <td>Especifica el archivo de manifiesto para la operación de importación. <br />
        El archivo de manifiesto se genera durante la operación de exportación.</td>
    <td>N</td>
    <td>N</td>
    <td>Y<br /> (solo vista previa)</td>
  </tr>
</table>

<br/>

## <a id="limit-writes"></a> Limitación de escrituras concurrentes mientras se copian datos

Cuando copie blobs o archivos con AzCopy, recuerde que otra aplicación puede estar modificando los datos mientras se copian. Si es posible, asegúrese de que los datos no se modifican durante la operación de copia. Por ejemplo, cuando copie un VHD asociado con una máquina virtual de Azure, asegúrese de que ninguna otra aplicación está escribiendo actualmente en el VHD. Alternativamente, puede crear una instantánea del VHD primero y, después, copiar la instantánea.

Si no puede impedir que otras aplicaciones escriban en blobs o archivos mientras se copian, recuerde que para cuando el trabajo finalice, los recursos copiados puede que ya no tengan una paridad total con los recursos de origen.

## <a id="copy-blobs"></a> Copia de blobs de Azure con AzCopy

Los ejemplos siguientes muestran diversos escenarios para copiar blobs con AzCopy.

### Copia de un solo blob

**Cargar un archivo desde el sistema de archivos a Almacenamiento de blobs:**
	
	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:abc.txt

**Descargar un blob desde Almacenamiento de blobs en el sistema de archivos:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Copia de un blob a través de la copia del lado del servidor

Cuando copia un blob dentro de una cuenta de almacenamiento o a través de cuentas de almacenamiento, se realiza una operación de copia del lado del servidor. Para obtener más información acerca de las operaciones de copia del lado del servidor, consulte [Introducción a la copia asincrónica de blobs entre cuentas](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/06/12/introducing-asynchronous-cross-account-copy-blob.aspx).

**Copia de un blob dentro de una cuenta de almacenamiento:**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceKey:key /DestKey:key /Pattern:abc.txt 

**Copia de un blob entre cuentas de almacenamiento:**

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt
 
### Copia de un blob desde la región secundaria 

Si la cuenta de almacenamiento tiene el almacenamiento con redundancia geográfica de acceso de lectura habilitado, puede copiar datos desde la región secundaria. 

**Copia de un blob en la cuenta principal desde la secundaria:**

	AzCopy /Source:https://myaccount1-secondary.blob.core.windows.net/mynewcontainer1 /Dest:https://myaccount2.blob.core.windows.net/mynewcontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt

**Descarga de un blob de la secundaria en un archivo del sistema de archivos:**

	AzCopy /Source:https://myaccount-secondary.blob.core.windows.net/mynewcontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

### Carga de un archivo en un nuevo contenedor de blobs o directorio virtual

**Carga de un archivo en un nuevo contenedor de blobs**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mynewcontainer /DestKey:key /Pattern:abc.txt

Tenga en cuenta que si el contenedor de destino especificado no existe, AzCopy lo creará y cargará el archivo en él.

**Carga de un archivo en un nuevo directorio virtual de blobs**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer/vd /DestKey:key /Pattern:abc.txt

Tenga en cuenta que si el directorio virtual especificado no existe, AzCopy cargará el archivo para incluir el directorio virtual en su nombre (*p. ej.*, "vd/abc.txt" en el ejemplo anterior).

### Descarga de un blob en una nueva carpeta

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Si la carpeta "C:\myfolder" todavía no existe, AzCopy la creará en el sistema de archivos y descargará "abc.txt" en la nueva carpeta.

### Carga de archivos y subcarpetas de un directorio en un contenedor de forma recursiva

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /S

La especificación de la opción "/S" copiará el contenido del directorio especificado en Almacenamiento de blobs de forma recursiva, lo que significa que todas las subcarpetas y sus archivos también se copiarán. Por ejemplo, supongamos que los siguientes archivos residen en la carpeta "C:\myfolder":

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

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key

Si no especifica la opción "/S" en la línea de comandos, AzCopy no realizará la copia de forma recursiva. Solamente se copiarán los archivos del directorio especificado; NO se copiará ninguna subcarpeta ni sus archivos. Por ejemplo, supongamos que los siguientes archivos residen en la carpeta "C:\myfolder":

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /S

Supongamos que los siguientes blobs residen en el contenedor especificado:  

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Después de la operación de copia, el directorio "C:\myfolder" incluirá los siguientes archivos:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt
	C:\myfolder\vd1\a.txt
	C:\myfolder\vd1\abcd.txt

### Descarga de blobs de un directorio de blobs virtual a un directorio del sistema de archivos de forma recursiva

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer/vd1/ /Dest:C:\myfolder /SourceKey:key /S

Supongamos que los siguientes blobs residen en el contenedor especificado:

	abc.txt
	abc1.txt
	abc2.txt
	vd1\a.txt
	vd1\abcd.txt

Después de la operación de copia, el directorio "C:\myfolder" incluirá los siguientes archivos. Tenga en cuenta que solamente se copiarán los blobs del directorio virtual:

	C:\myfolder\a.txt
	C:\myfolder\abcd.txt

### Carga de archivos que coincidan con el patrón de archivos especificado en un contenedor de forma recursiva 

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Pattern:a* /S

Supongamos que los siguientes archivos residen en la carpeta "C:\myfolder":

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

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /Pattern:a /S

Supongamos que los siguientes blobs residen en el contenedor especificado. Se copiarán todos los blobs que comiencen con el prefijo "a":

	abc.txt
	abc1.txt
	abc2.txt
	xyz.txt
	vd1\a.txt
	vd1\abcd.txt

Después de la operación de copia, la carpeta "C:\myfolder" incluirá los siguientes archivos:

	C:\myfolder\abc.txt
	C:\myfolder\abc1.txt
	C:\myfolder\abc2.txt

Tenga en cuenta que el prefijo se aplica al directorio virtual, que forma la primera parte del nombre del blob. En el ejemplo mostrado anteriormente, el directorio virtual no coincide con el prefijo especificado, por lo que no se copia.


### Copia de un blob y sus instantáneas en otra cuenta de almacenamiento

	AzCopy /Source:https://sourceaccount.blob.core.windows.net/mycontainer/mycontainer1 /Dest:https://destaccount.blob.core.windows.net/mycontainer2 /SourceKey:key1 /DestKey:key2 /Pattern:abc.txt /Snapshot

Después de la operación de copia, el contenedor de destino incluirá el blob y sus instantáneas. Suponiendo que el blob del ejemplo anterior tiene dos instantáneas, el contenedor incluirá el siguiente blob e instantáneas:

	abc.txt
	abc (2013-02-25 080757).txt
	abc (2014-02-21 150331).txt


### Uso de un archivo de respuesta para especificar parámetros de línea de comandos

	AzCopy /@:"C:\myfolder\abc.txt"

Puede incluir cualquier parámetro de la línea de comandos de AzCopy en un archivo de respuesta. AzCopy procesa los parámetros del archivo como si hubieran sido especificados en la línea de comandos, realizando una sustitución directa con el contenido del archivo.

**Especificación de uno o varios archivos de respuesta de una sola línea**

Imaginemos un archivo de respuesta llamado "source.txt" que especifica un contenedor de origen:

	/Source:http://myaccount.blob.core.windows.net/mycontainer

Y un archivo de respuesta llamado "dest.txt" que especifica una carpeta de destino en el sistema de archivos:

	/Dest:C:\myfolder

Y un archivo de respuesta llamado "options.txt" que especifica opciones para AzCopy:

	/S /Y

Para llamar a AzCopy con estos archivos de respuesta, todos los cuales residen en un directorio "C:\responsefiles", use este comando:

	AzCopy /@:"C:\responsefiles\source.txt" /@:"C:\responsefiles\dest.txt" /SourceKey:<sourcekey> /@:"C:\responsefiles\options.txt"   

AzCopy procesa este comando como si se incluyeran todos los parámetros individuales en la línea de comandos:

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

**Especificación de un archivo de respuesta de varias líneas**

Imaginemos un archivo de respuesta llamado "copyoperation.txt", que contiene las siguientes líneas. Cada parámetro de AzCopy se especifica en su propia línea:

	/Source:http://myaccount.blob.core.windows.net/mycontainer
	/Dest:C:\myfolder
	/SourceKey:<sourcekey>
	/S 
	/Y

Para llamar a AzCopy con este archivo de respuesta, use este comando:

	AzCopy /@:"C:\responsefiles\copyoperation.txt"

AzCopy procesa este comando como si se incluyeran todos los parámetros individuales en la línea de comandos:	

	AzCopy /Source:http://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:<sourcekey> /S /Y

Tenga en cuenta que cada parámetro de AzCopy se debe especificar completamente en una línea. Por ejemplo, AzCopy no se procesará si divide el parámetro en dos líneas, tal y como se muestra aquí para el parámetro "/sourcekey":

	http://myaccount.blob.core.windows.net/mycontainer
 	C:\myfolder
	/sourcekey:
	[sourcekey]
	/S 
	/Y

### Especificación de una firma de acceso compartido (SAS)
	
**Especificación de una SAS para el contenedor de origen usando la opción /sourceSAS**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /DestC:\myfolder /SourceSAS:SAS /S

**Especificación de una SAS para el contenedor de origen en el URI de dicho contenedor**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1/?SourceSASToken /Dest:C:\myfolder /S

**Especificación de una SAS para el contenedor de destino usando la opción /destSAS**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer1 /DestSAS:SAS /Pattern:abc.txt

**Especificación de una SAS para los contenedores de origen y destino**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer1 /Dest:https://myaccount.blob.core.windows.net/mycontainer2 /SourceSAS:SAS1 /DestSAS:SAS2 /Pattern:abc.txt

### Especificación de una carpeta de archivo de diario

Cada vez que emite un comando a AzCopy, comprueba si existe un archivo de diario en la carpeta predeterminada o si existe una carpeta que especificó a través de esta opción. Si el archivo de diario no existe en ningún lugar, AzCopy trata la operación como una nueva y genera un nuevo archivo de diario.

Si el archivo de diario existe, AzCopy comprobará si la línea de comandos escrita coincide con la de dicho archivo. Si las dos líneas de comandos coinciden, AzCopy reanudará la operación incompleta. Si no coinciden, se le preguntará si desea sobrescribir el archivo de diario, iniciar una nueva operación o cancelar la operación actual. 

**Utilización de la ubicación predeterminada para el archivo de diario**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z

Si omite la opción "/Z", o bien especifica la opción "/Z" sin la ruta de acceso a la carpeta, como se ha mostrado anteriormente, AzCopy crea el archivo de diario en la ubicación predeterminada, que es "%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy". Si el archivo de diario ya existe, AzCopy reanuda la operación basándose en dicho archivo. 

**Especificación de una ubicación personalizada para el archivo de diario**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /Z:C:\journalfolder\

Este ejemplo crea el archivo de diario si todavía no existe. Si no existe, AzCopy reanuda la operación basándose en el archivo de diario.

**Reanudación de una operación de AzCopy**

	AzCopy /Z:C:\journalfolder\

Este ejemplo reanuda la última operación que es posible que no terminara.


### Generación de un archivo de registro

**Escritura del archivo de registro detallado en la ubicación predeterminada**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V

Si especifica la opción "/V" sin proporcionar una ruta de acceso al archivo de registro detallado, AzCopy crea el archivo de registro en la ubicación predeterminada, que es "%SystemDrive%\Users\%username%\AppData\Local\Microsoft\Azure\AzCopy".

**Escritura del archivo de registro detallado en una ubicación predeterminada**

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.blob.core.windows.net/mycontainer /DestKey:key /V:C:\myfolder\azcopy1.log

Tenga en cuenta que si especifica una ruta de acceso relativa después de la opción "/V", como "/V:test/azcopy1.log", el registro detallado se creará en el directorio de trabajo actual dentro de una subcarpeta llamada "test".


### Establecimiento de la hora de la última modificación de los archivos descargados para que coincida con la de los blobs de origen

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT

### Exclusión de los blobs de la operación de copia basándose en la hora de su última modificación

Especifique la opción "/MT" para comparar la hora de la última modificación del blob de origen y el archivo de destino.

**Exclusión de blobs que son más nuevos que el archivo de destino**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XN

**Exclusión de blobs que son más antiguos que el archivo de destino**

	AzCopy /Source:https://myaccount.blob.core.windows.net/mycontainer /Dest:C:\myfolder /SourceKey:key /MT /XO

### Especificación del número de operaciones simultáneas para iniciar

La opción "/NC" especifica el número de operaciones de copia simultáneas. De forma predeterminada, AzCopy iniciará operaciones simultáneas a ocho veces el número de procesadores de núcleo que tiene. Si ejecuta AzCopy en una red con un ancho de banda bajo, puede especificar un número bajo para esta opción para evitar errores provocados por la competencia de recursos.


###	Ejecución de AzCopy en recursos de blob en el emulador de almacenamiento

	AzCopy /Source:https://127.0.0.1:10004/myaccount/myfileshare/ /Dest:C:\myfolder /SourceKey:key /SourceType:Blob /S


## <a id="copy-files"></a> Copia de archivos en Almacenamiento de archivos de Azure con AzCopy (solo versión de vista previa)

Los ejemplos siguientes muestran diversos escenarios para copiar archivos de Azure con AzCopy.

### Descarga de un archivo desde un recurso compartido de archivos de Azure en el sistema de archivos

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/myfolder1/ /Dest:C:\myfolder /SourceKey:key /Pattern:abc.txt

Tenga en cuenta que si el origen especificado es un recurso compartido de archivos de Azure, tiene que especificar el nombre de archivo exacto (*por ejemplo*, "abc.txt") a fin de copiar un único archivo, o bien especificar la opción para copiar todos los archivos del recurso compartido de forma recursiva. Si intenta especificar tanto un patrón de archivos como la opción "/S" conjuntamente, se producirá un error.

### Descarga de archivos y carpetas de un recurso compartido de archivos de Azure en el sistema de archivos de forma recursiva

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S

Tenga en cuenta que ninguna carpeta vacía se copiará.


### Carga de archivos y carpetas desde el sistema de archivos en un recurso compartido de archivos de Azure

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /S

Tenga en cuenta que ninguna carpeta vacía se copiará.


### Carga de archivos que coincidan con el patrón de archivos especificado en un recurso compartido de Azure de forma recursiva

	AzCopy /Source:C:\myfolder /Dest:https://myaccount.file.core.windows.net/myfileshare/ /DestKey:key /Pattern:ab* /S

## <a id="copy-entities"></a> Copia de entidades en una Tabla de Azure con AzCopy (solo versión de vista previa)

Los ejemplos siguientes muestran diversos escenarios para copiar entidades de Tabla de Azure con AzCopy.

### Exportar entidades al sistema de archivos local

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key

### Exportar entidades a un blob de Azure

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:https://myaccount.blob.core.windows.net/mycontainer/ /SourceKey:key1 /Destkey:key2

AzCopy generará un archivo de datos JSON en la carpeta o el contenedor de blobs local con la siguiente convención de nomenclatura:

	<account name>_<table name>_<timestamp>_<volume index>_<CRC>.json

El archivo de datos JSON generado sigue el formato de carga para metadatos mínimos. Para obtener más información sobre el formato de carga, consulte [Formato de carga para las operaciones del servicio Tabla](http://msdn.microsoft.com/library/azure/dn535600.aspx).

### Dividir los archivos exportados

	AzCopy /Source:https://myaccount.file.core.windows.net/myfileshare/ /Dest:C:\myfolder /SourceKey:key /S /SplitSize:100

AzCopy utiliza un *índice de volumen* en los nombres de los archivos de datos divididos para distinguir los diversos archivos. El índice de volumen consta de dos partes, un *índice de rango de clave de partición* y un *índice de archivo dividido*. Ambos índices se basan en 0.

El rango de clave de partición será 0 si un usuario no especifica la opción "/PKRS" (que se presenta en la siguiente sección).

Por ejemplo, imagine que AzCopy genera dos archivos de datos después de que el usuario especifique la opción "/SplitSize". Los nombres de archivos de datos resultantes serían:

	myaccount_mytable_20140903T051850.8128447Z_0_0_C3040FE8.json
	myaccount_mytable_20140903T051850.8128447Z_0_1_0AB9AC20.json

Tenga en cuenta que el mínimo valor posible para la opción "/SplitSize" es 32 MB. Si el destino especificado es Almacenamiento de blobs, AzCopy dividirá el archivo de datos una vez que su tamaño alcance el límite especificado (200 GB), independientemente de si el usuario ha especificado o no la opción "/SplitSize".

### Exportar entidades simultáneamente

	AzCopy /Source:https://myaccount.table.core.windows.net/myTable/ /Dest:D:\test\ /SourceKey:key /PKRS:"aa#bb"

AzCopy iniciará operaciones simultáneas para exportar entidades cuando el usuario especifique la opción "/PKRS". Cada operación exporta un rango de claves de partición.

Tenga en cuenta que el número de operaciones simultáneas está controlado también por la opción "/NC". AzCopy utiliza el número de procesadores de núcleo como valor predeterminado de "/NC" al copiar entidades de tabla, incluso si "/NC" no se ha especificado. Si el usuario especifica la opción "/PKRS", AzCopy utiliza el valor más pequeño de los dos (rangos de clave de partición u operaciones simultáneas especificadas de manera implícita o explícita) para determinar el número de operaciones simultáneas que van a dar inicio. Para obtener más detalles, escriba "AzCopy /?:NC" en la línea de comandos.

### Importar entidades simultáneamente

	AzCopy /Source:D:\test\ /Dest:https://myaccount.table.core.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace 

Al exportar entidades de tabla, AzCopy escribe un archivo de manifiesto para la carpeta o contenedor de blobs de destino que se haya especificado. El archivo de manifiesto se utiliza por parte del proceso de importación para ubicar los archivos de datos necesarios y realizar la validación de datos durante el proceso de importación. El archivo de manifiesto utiliza la siguiente convención de nombres:

	<account name>_<table name>_<timestamp>.manifest

La opción "/EntityOperation" indica cómo insertar entidades en la tabla. Los valores posibles son:

- "InsertOrSkip": omite una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
- "InsertOrMerge": combina una entidad existente o inserta una nueva entidad si esta no existe en la tabla.
- "InsertOrReplace": reemplaza una entidad existente o inserta una nueva entidad si esta no existe en la tabla.

Tenga en cuenta que no puede especificar la opción "/PKRS" en el escenario de importación. A diferencia del escenario de exportación, en el que debe especificar la opción "/PKRS" para iniciar las operaciones simultáneas, AzCopy iniciará de manera predeterminada operaciones simultáneas cuando importe las entidades. El número predeterminado de operaciones simultáneas que se inician equivale al número de procesadores de núcleo; sin embargo, puede especificar un número diferente para la simultaneidad con la opción "/NC". Para obtener más detalles, escriba "AzCopy /?:NC" en la línea de comandos.


## <a id="versions"></a> Versiones de AzCopy

| Versión | Novedades                                                                                      				|
|---------|-----------------------------------------------------------------------------------------------------------------|
| **V4.0.0**  | **Versión de vista previa actual. Incluye toda la funcionalidad de V3.0.0. Admite también la copia de archivos a o desde el Almacenamiento de archivos de Azure, así como la copia de entidades a o desde el Almacenamiento de tablas de Azure.**	
| **V3.0.0**  | **Versión de vista previa actual. Modifica la sintaxis de línea de comandos de AzCopy para requerir nombres de parámetro, y rediseña la ayuda de la línea de comandos. Esta versión solo admite la copia a y desde el Almacenamiento de blobs de Azure.**	
| V2.5.1  | Optimiza el rendimiento al utilizar las opciones /xo y /xn. Soluciona errores relacionados con caracteres especiales en nombres de archivos originales y daños en archivos de diario después de que el usuario especifica una sintaxis de línea de comandos errónea.	
| V2.5.0  | Optimiza el rendimiento de escenarios de copia a gran escala e introduce varias mejoras importantes relacionadas con la facilidad de uso.	
| V2.4.1  | Admite la especificación de la carpeta de destino en el asistente para la instalación.                     			
| V2.4.0  | Admite la carga y descarga de archivos para Almacenamiento de archivos de Azure.                       				                              
| V2.3.0  | Admite cuentas de almacenamiento con redundancia geográfica de acceso de lectura.                                                  			|
| V2.2.2  | Actualizada para usar la versión 3.0.3 de la biblioteca del cliente de almacenamiento de Azure.                                            				                    
| V2.2.1  | Problema de rendimiento solucionado cuando se copian grandes cantidades de archivos dentro de la misma cuenta de almacenamiento.            				                                                
| V2.2    | Admite el establecimiento del delimitador de directorios virtuales para nombres de blob. Admite la especificación de la ruta de acceso al archivo de diario.		|
| V2.1    | Proporciona más de 20 opciones para admitir operaciones de carga, descarga y copia de blobs de una forma diferente.		|


## <a id="next-steps"></a> Pasos siguientes

Para obtener más información acerca de Almacenamiento de Azure y AzCopy, consulte los recursos siguientes.

### Documentación de Almacenamiento de Azure:

- [Introducción a Almacenamiento de Azure](http://azure.microsoft.com/en-us/documentation/articles/storage-introduction/)
- [Almacenamiento de archivos en Almacenamiento de blobs](http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-blobs/)
- [Creación de un recurso compartido de archivos SMB en Azure con Almacenamiento de archivos](http://azure.microsoft.com/en-us/documentation/articles/storage-dotnet-how-to-use-files/)

### Publicaciones en blobs de Almacenamiento de Azure

- [AzCopy 3.0: Presentación de la disponibilidad general de AzCopy 3.0 y de la versión de vista previa de AzCopy 4.0 con soporte para tablas y archivos](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/10/29/azcopy-announcing-general-availability-of-azcopy-3-0-plus-preview-release-of-azcopy-4-0-with-table-and-file-support.aspx)
- [AzCopy 2.5: Optimizada para escenarios de copia a gran escala](http://go.microsoft.com/fwlink/?LinkId=507682)
- [Introducción al servicio de archivos de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/05/12/introducing-microsoft-azure-file-service.aspx)
- [AzCopy: Compatibilidad para almacenamiento con redundancia geográfica de acceso de lectura](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/04/07/azcopy-support-for-read-access-geo-redundant-account.aspx)
- [AzCopy: Transferencia de datos con modo reiniciable y token SAS](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/09/07/azcopy-transfer-data-with-re-startable-mode-and-sas-token.aspx)
- [AzCopy: Uso de copia de blobs entre cuentas](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/04/01/azcopy-using-cross-account-copy-blob.aspx)
- [AzCopy: Carga y descarga de archivos para blobs de Microsoft Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2012/12/03/azcopy-uploading-downloading-files-for-windows-azure-blobs.aspx)


<!--HONumber=35.1-->
