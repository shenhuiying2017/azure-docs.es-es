<properties pageTitle="Notas de la versi&oacute;n de Servicios multimedia" metaKeywords="Azure Media Services" description="Notas de la versi&oacute;n de Servicios multimedia" metaCanonical="" services="media-services" documentationCenter="Media" title="Notas de la versi&oacute;n de Servicios multimedia" authors="juliako" solutions="media" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="media" ms.devlang="dotnet" ms.topic="article" ms.date="10/15/2014" ms.author="juliako" />

# Notas de la versión de Servicios multimedia de Azure

Estas notas de la versión resumen los cambios realizados desde las versiones anteriores y los problemas conocidos.

> [AZURE.NOTE] Queremos recibir opiniones de nuestros clientes y centrarnos en la solución de los problemas que les afectan. Para informar de un problema o formular una pregunta, realice una entrada en el [foro de MSDN de Servicios multimedia de Azure][foro de MSDN de Servicios multimedia de Azure].

-   [Problemas actualmente conocidos][Problemas actualmente conocidos]
-   [Historial de versiones de API de REST][Historial de versiones de API de REST]
-   [Versión de octubre de 2014][Versión de octubre de 2014]
-   [Versión de septiembre de 2014][Versión de septiembre de 2014]
-   [Versión de agosto de 2014][Versión de agosto de 2014]
-   [Versión de julio de 2014][Versión de julio de 2014]
-   [Versión de mayo de 2014][Versión de mayo de 2014]
-   [Versión de abril de 2014][Versión de abril de 2014]
-   [Versiones de enero/febrero de 2014][Versiones de enero/febrero de 2014]
-   [Versión de diciembre de 2013][Versión de diciembre de 2013]
-   [Versión de noviembre de 2013][Versión de noviembre de 2013]
-   [Versión de agosto de 2013][Versión de agosto de 2013]
-   [Versión de junio de 2013][Versión de junio de 2013]
-   [Versión de diciembre de 2012][Versión de diciembre de 2012]
-   [Versión de noviembre de 2012][Versión de noviembre de 2012]
-   [Versión de vista previa de junio de 2012][Versión de vista previa de junio de 2012]

## <span id="issues"></span></a>Problemas actualmente conocidos

### <span id="general_issues"></span></a>Problemas generales de Servicios multimedia

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Problema</th>
<th align="left">Descripción</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Varios encabezados comunes HTTP no se proporcionan en la API de REST.</td>
<td align="left">Si desarrolla aplicaciones de Servicios multimedia mediante la API de REST, encontrará que algunos campos de encabezado comunes HTTP (como CLIENT-REQUEST-ID, REQUEST-ID y RETURN-CLIENT-REQUEST-ID) no se admiten. Los encabezados se agregarán en una futura actualización.</td>
</tr>
<tr class="even">
<td align="left">Al codificar un activo con un nombre de archivo que contiene caracteres de escape (por ejemplo, %20), se muestra el error &quot;MediaProcessor: archivo no encontrado&quot;.</td>
<td align="left">Los nombres de archivos que se van a agregar a un activo y que luego se van a codificar deben contener caracteres alfanuméricos y espacios. El problema se corregirá en una futura actualización.</td>
</tr>
<tr class="odd">
<td align="left">El método ListBlobs que es parte del SDK de almacenamiento de Azure, versión 3.x, no funciona correctamente.</td>
<td align="left">Los Servicios multimedia generan URL de SAS basadas en la versión del <a href="http://msdn.microsoft.com/es-es/library/azure/dn592123.aspx">12-02-2012</a>. Si desea usar el SDK de almacenamiento de Azure para mostrar los blobs de un contenedor de blobs, utilice el método <a href="http://msdn.microsoft.com/es-es/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> que es parte del SDK de almacenamiento de Azure, versión 2.x. El método ListBlobs que es parte del SDK de almacenamiento de Azure, versión 3.x, no funcionará correctamente.</td>
</tr>
<tr class="even">
<td align="left">El mecanismo de limitación de Servicios multimedia restringe el uso de recursos en las aplicaciones que realizan un número excesivo de solicitudes al servicio. El servicio puede devolver el código de estado HTTP de servicio no disponible (503).</td>
<td align="left">Para obtener más información, consulte la descripción del código de estado HTTP 503 en el tema <a href="http://msdn.microsoft.com/es-es/library/azure/dn168949.aspx">Códigos de error de Azure Media Services</a>.</td>
</tr>
</tbody>
</table>

### <span id="dotnet_issues"></span></a>Problemas del SDK de Servicios multimedia para .NET

<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Problema</th>
<th align="left">Descripción</yt>
</tr></th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Los objetos de Servicios multimedia del SDK no se pueden serializar y, como resultado, no funcionan con el almacenamiento en caché de Azure.</td>
<td align="left">Si intenta serializar el objeto AssetCollection del SDK para agregarlo al almacenamiento en caché de Azure, se produce una excepción.</td>
</tr>
</tbody>
</table>

## <span id="rest_version_history"></span></a>Historial de versiones de API de REST

Para obtener información sobre el historial de versiones de la API de REST de Servicios multimedia, consulte [Referencia de la API de REST de Servicios multimedia de Azure][Referencia de la API de REST de Servicios multimedia de Azure].

## <span id="october_changes_14"></span></a>Versión de octubre de 2014

### <span id="new_encoder_release"></span></a>Versión del codificador de Servicios multimedia

Anuncio de la nueva versión del codificador de Servicios multimedia de Azure. Con la última versión del codificador de Servicios multimedia de Azure solo se le cobran los GB de salida, pero, por lo demás, el nuevo codificador es una característica compatible con el codificador de Servicios multimedia de Microsoft Azure. Para obtener más información, consulte [Detalles de precios de Servicios multimedia][Detalles de precios de Servicios multimedia].

### <span id="oct_sdk"></span></a>SDK .NET de Servicios multimedia

La versión del SDK de Servicios multimedia para .NET es ahora la 3.0.0.8.

La versión de las extensiones del SDK de Servicios multimedia para .NET es ahora la 2.0.0.3.

## <span id="september_changes_14"></span></a>Versión de septiembre de 2014

La versión de los metadatos de REST de Servicios multimedia es ahora la 2.7. Para obtener más información sobre las últimas actualizaciones de REST, consulte [Referencia de la API de REST de Servicios multimedia de Azure][Referencia de la API de REST de Servicios multimedia de Azure].

El SDK de Servicios multimedia para .NET es ahora la versión 3.0.0.7.

### <span id="sept_14_breaking_changes"></span></a>Cambios de compatibilidad

-   **Origen** se llama ahora [StreamingEndpoint][StreamingEndpoint].
-   Un cambio en el comportamiento predeterminado al usar el **Portal de administración de Azure** para codificar y luego publicar archivos MP4.

    Anteriormente, cuando se usaba el Portal de administración para publicar un activo de vídeo de un solo archivo MP4, se creaba una URL de SAS (las URL de SAS le permiten descargar el vídeo de un almacenamiento de blobs). Ahora, cuando utiliza el Portal de administración para codificar y luego publicar un activo de vídeo MP4 de un solo archivo, la URL generada apunta a un extremo de streaming de Servicios multimedia de Azure. Este cambio no afecta a los vídeos MP4 que se cargan directamente en Servicios multimedia y se publican sin ser codificados por Servicios multimedia de Azure.

    Actualmente, cuenta con las dos opciones siguientes para resolver el problema.

    -   Habilitar las unidades de streaming y usar el empaquetado dinámico para transmitir por secuencias el activo de .mp4 como una presentación Smooth Streaming.

    -   Crear una URL de SAS para descargar (o reproducir de forma progresiva) el .mp4. Para obtener más información sobre cómo crear un localizador de SAS, consulte [Entrega de contenido][Entrega de contenido].

### <span id="sept_14_GA_changes"></span></a>Nuevas características/supuestos que forman parte de la versión GA

-   **Procesador multimedia de indización**. Para obtener más información, consulte [Indización de archivos multimedia con el Indizador multimedia de Azure][Indización de archivos multimedia con el Indizador multimedia de Azure].

-   La entidad [StreamingEndpoint][StreamingEndpoint] ahora le permite agregar nombres de dominio personalizados (host).

    Para poder usar un nombre de dominio personalizado como nombre del extremo de streaming de Servicios multimedia, debe agregar nombres de host personalizados a su extremo de streaming. Use las API de REST de Servicios multimedia o el SDK .NET para agregar nombres de host personalizados.

    Se aplican las siguientes consideraciones:

    -   Debe tener la propiedad del nombre de dominio personalizado.

    -   Y esta propiedad se debe validar mediante Servicios multimedia de Azure.

    Para obtener más información, consulte la propiedad **CustomHostNames** en el tema [StreamingEndpoint][StreamingEndpoint].

### <span id="sept_14_preview_changes"></span></a>Nuevas características/supuestos que forman parte de la versión de vista previa pública

-   Vista previa de Live Streaming. Para obtener más información, consulte [Trabajo con Live Streaming de Servicios multimedia de Azure][Trabajo con Live Streaming de Servicios multimedia de Azure].

-   Servicio de entrega de claves. Para obtener más información, consulte [Uso de cifrado dinámico AES-128 y Servicio de entrega de claves][Uso de cifrado dinámico AES-128 y Servicio de entrega de claves].

-   Cifrado dinámico AES. Para obtener más información, consulte [Uso de cifrado dinámico AES-128 y Servicio de entrega de claves][Uso de cifrado dinámico AES-128 y Servicio de entrega de claves].

-   Servicio de entrega de licencias PlayReady. Para obtener más información, consulte [Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady][Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady].

-   Cifrado dinámico PlayReady. Para obtener más información, consulte [Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady][Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady].

-   Plantilla de licencias PlayReady de Servicios multimedia. Para obtener más información, consulte [Introducción a la plantilla de licencia de PlayReady de los Servicios multimedia][Introducción a la plantilla de licencia de PlayReady de los Servicios multimedia].

-   Activos cifrados de almacenamiento de streaming. Para obtener más información, consulte [Contenido cifrado de almacenamiento de streaming][Contenido cifrado de almacenamiento de streaming].

## <span id="august_changes_14"></span></a>Versión de agosto de 2014

Cuando se codifica un activo, se produce un activo de salida tras la finalización del trabajo de codificación. Hasta esta versión, el codificador de Servicios multimedia de Azure producía metadatos sobre activos de salida. A partir de esta versión, el codificador también produce metadatos sobre activos de entrada. Para obtener más información, consulte los temas [Metadatos de entrada][Metadatos de entrada] y [Metadatos de salida][Metadatos de salida].

## <span id="july_changes_14"></span></a>Versión de julio de 2014

Se han corregido los siguientes errores para Azure Media Services Packager y Encryptor:

-   Solo se reproduce el audio cuando se transmite un activo de un archivo en vivo a HTTP Live Streaming: este error se ha corregido y ahora se reproduce tanto el audio como el vídeo.

-   Al empaquetar un activo con HTTP Live Streaming y cifrado de sobre AES de 128 bits, las secuencias empaquetadas no se reproducen en dispositivos Android: este error se ha corregido y la secuencia empaquetada se reproduce en dispositivos Android compatibles con HTTP Live Streaming.

## <span id="may_changes_14"></span></a>Versión de mayo de 2014

### <span id="may_14_changes"></span></a>Actualizaciones generales de Servicios multimedia

Ahora puede usar [empaquetado dinámico][empaquetado dinámico] para transmitir por secuencias HTTP Live Streaming (HLS) v3. Para transmitir por secuencias HLS v3, agregue el siguiente formato a la ruta del localizador de origen: \*.ism/manifest(format=m3u8-aapl-v3). Para obtener más información, consulte el [blog de Nick Drouin][blog de Nick Drouin].

El empaquetado dinámico admite ahora también la entrega de HLS (v3 y v4) cifrado con PlayReady basado en Smooth Streaming cifrado estáticamente con PlayReady. Para obtener información sobre cómo cifrar Smooth Streaming con PlayReady, consulte [Protección de Smooth Streaming con PlayReady][Protección de Smooth Streaming con PlayReady].

### <a name="may_14_donnet_changes"></a>Actualizaciones del SDK .NET de Servicios multimedia

Las siguientes mejoras se incluyen en la versión 3.0.0.5 del SDK .NET de Servicios multimedia:

-   Mayor velocidad y resistencia para cargar/descargar activos multimedia.

-   Mejoras en la gestión de excepciones transitorias y la lógica de reintento:

    -   La detección de errores transitorios y la lógica de reintento se han mejorado en las excepciones ocasionadas al consultar, guardar cambios y cargar y descargar archivos.

    -   Al recibir excepciones web (por ejemplo, durante una solicitud de token de ACS), observará que ahora se producen con mayor rapidez errores graves.

Para obtener más información, consulte [Lógica de reintento en el SDK de Media Services para .NET][Lógica de reintento en el SDK de Media Services para .NET].

## <span id="april_changes_14"></span></a>Versión del codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Actualizaciones del codificador de Servicios multimedia

-   Se ha agregado compatibilidad con la ingesta de archivos AVI creados con el editor no lineal EDIUS de Grass Valley, donde el vídeo está comprimido ligeramente mediante el códec HQ/HQX de Grass Valley. Para obtener más información, consulte [Grass Valley anuncia EDIUS 7 Streaming a través de la nube][Grass Valley anuncia EDIUS 7 Streaming a través de la nube].

-   Se ha agregado compatibilidad con la especificación de la convención de nomenclatura para los archivos producidos por el codificador multimedia. Para obtener más información, consulte [Control de nombres de archivo de salida de Media Service Encoder][Control de nombres de archivo de salida de Media Service Encoder].

-   Se ha agregado compatibilidad con superposiciones de vídeo y/o audio. Para obtener más información, consulte [Creación de superposiciones][Creación de superposiciones].

-   Se ha agregado compatibilidad con la unión de varios segmentos de vídeo. Para obtener más información, consulte [Unión de segmentos de vídeo][Unión de segmentos de vídeo].

-   Se ha corregido un error relacionado con la transcodificación de MP4 en el que el audio se ha codificado con la capa 3 de nivel de audio de MPEG-1 (conocido como MP3).

## <span id="jan_feb_changes_14"></span></a>Versiones de enero/febrero de 2014

### <a name="jan_fab_14_donnet_changes"></a>SDK .NET de Servicios multimedia 3.0.0.1, 3.0.0.2 y 3.0.0.3

Los cambios en 3.0.0.1 y 3.0.0.2 incluyen los siguientes:

-   Se han corregido los problemas relacionados con el uso de consultas LINQ con instrucciones OrderBy.

-   Se han dividido las soluciones de prueba de [Github][Github] en pruebas basadas en unidades y pruebas basadas en supuestos.

Para obtener más información sobre los cambios, consulte: [SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2][SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2].

Se han realizado los siguientes cambios en la versión 3.0.0.3:

-   Se han actualizado las dependencias de almacenamiento de Azure para usar la versión 3.0.3.0.

-   Se ha corregido el problema de compatibilidad con versiones posteriores en las versiones 3.0.*.*.

## <span id="december_changes_13"></span></a>Versión de diciembre de 2013

### <a name="dec_13_donnet_changes"></a>SDK .NET de Servicios multimedia de Azure 3.0.0.0

> [AZURE.NOTE] Las versiones 3.0.x.x no son compatibles con las versiones 2.4.x.x.

La última versión del SDK de Servicios multimedia es ahora la 3.0.0.0. Puede descargar el último paquete de Nuget u obtener los bits de [Github][Github].

A partir de la versión 3.0.0.0 del SDK de Servicios multimedia, puede reutilizar los tokens del [Servicio de control de acceso de Active Directory (ACS) de Azure][Servicio de control de acceso de Active Directory (ACS) de Azure]. Para obtener más información, consulte la sección "Reutilización de tokens de Access Control Service" del tema [Conectar con Media Services mediante el SDK de Media Services][Conectar con Media Services mediante el SDK de Media Services].

### <a name="dec_13_donnet_ext_changes"></a>Extensiones del SDK .NET de Servicios multimedia de Azure 2.0.0.0

Las extensiones del SDK .NET de Servicios multimedia de Azure son un conjunto de métodos de extensión y funciones auxiliares que simplificarán su código y facilitarán el desarrollo con Servicios multimedia de Azure. Puede obtener los bits más recientes en [Extensiones del SDK .NET de Servicios multimedia de Azure][Extensiones del SDK .NET de Servicios multimedia de Azure].

## <span id="november_changes_13"></span></a>Versión de noviembre de 2013

### <a name="nov_13_donnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

A partir de esta versión, el SDK de Servicios multimedia para .NET gestiona los errores transitorios que se pueden producir al realizar llamadas a la capa API de REST de Servicios multimedia.

## <span id="august_changes_13"></span></a>Versión de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets de PowerShell de Servicios multimedia incluidos en las herramientas del SDK de Azure

Los siguientes cmdlets de PowerShell de Servicios multimedia se incluyen ahora en [azure-sdk-tools][azure-sdk-tools].

-   Get-AzureMediaServices

    Por ejemplo, `Get-AzureMediaServicesAccount`.

-   New-AzureMediaServicesAccount

    Por ejemplo, `New-AzureMediaServicesAccount -Name “MediaAccountName” -Location “Region” -StorageAccountName “StorageAccountName”`.

-   New-AzureMediaServicesKey

    Por ejemplo, `New-AzureMediaServicesKey -Name “MediaAccountName” -KeyType Secondary -Force`.

-   Remove-AzureMediaServicesAccount

    Por ejemplo, `Remove-AzureMediaServicesAccount -Name “MediaAccountName” -Force`.

## <span id="june_changes_13"></span></a>Versión de junio de 2013

### <a name="june_13_general_changes"></a>Cambios en los Servicios multimedia de Azure

Los cambios mencionados en esta sección son actualizaciones incluidas en las versiones de Servicios multimedia de 2013.

-   Posibilidad de vincular varias cuentas de almacenamiento a una cuenta de Servicios multimedia.

    StorageAccount

    Asset.StorageAccountName and Asset.StorageAccount

-   Posibilidad de actualizar Job.Priority.

-   Entidades y propiedades relacionadas con notificaciones:

    JobNotificationSubscription

    NotificationEndPoint

    Job

-   Asset.Uri

-   Locator.Name

### <a name="june_13_dotnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

Los siguientes cambios se incluyen en las versiones del SDK de Servicios multimedia de junio de 2013. El último SDK de Servicios multimedia está disponible en GitHub.

-   A partir de la versión 2.3.0.0, el SDK de Servicios multimedia admite la vinculación de varias cuentas de almacenamiento a una cuenta de Servicios multimedia. Las siguientes API admiten esta característica:

    El tipo IStorageAccount.

    La propiedad Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.

    La propiedad StorageAccount.

    La propiedad StorageAccountName.

    Para obtener más información, consulte [Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento][Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento].

-   API relacionadas con notificaciones. A partir de la versión 2.2.0.0, tiene la posibilidad de escuchar notificaciones de almacenamiento de la cola de Azure. Para obtener más información, consulte [Administrar notificaciones de trabajo de Media Services][Administrar notificaciones de trabajo de Media Services].

    La propiedad Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.

    El tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.

    El tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.

    El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.

    El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.

    El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

-   Dependencia del SDK del cliente de almacenamiento de Azure 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

-   Dependencia de OData 5.5 (Microsoft.Data.OData.dll).

## <span id="december_changes_12"></span></a>Versión de diciembre de 2012

### <a name="dec_12_dotnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

-   Intellisense: se ha agregado documentación de Intellisense que faltaba para muchos tipos.

-   Microsoft.Practices.TransientFaultHandling.Core: se ha corregido un problema en el que el SDK tenía aún dependencia con una versión anterior de este ensamblado. El SDK hace referencia ahora a la versión 5.1.1209.1 de este ensamblado.

Correcciones de problemas encontrados en el SDK de noviembre de 2012:

-   IAsset.Locators.Count: este recuento se notifica ahora correctamente en las nuevas interfaces IAsset después de que todos los localizadores se han eliminado.

-   IAssetFile.ContentFileSize: este valor está ahora correctamente definido después de una carga por IAssetFile.Upload(filepath).

-   IAssetFile.ContentFileSize: esta propiedad se puede establecer ahora al crear un archivo de activo. Anteriormente era de solo lectura.

-   IAssetFile.Upload(filepath): se ha corregido un problema en el que el método de carga sincrónico mostraba el siguiente error al cargar varios archivos en el activo. El error era "El servidor no pudo autenticar la solicitud. Asegúrese de que el valor del encabezado de autenticación tenga el formato correcto, incluida la firma".

-   IAssetFile.UploadAsync: se ha corregido un problema en el que no más de 5 archivos se podían cargar a la vez.

-   IAssetFile.UploadProgressChanged: este evento se proporciona ahora en el SDK.

-   IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Ahora se proporciona esta sobrecarga del método.

-   IAssetFile.DownloadAsync: se ha corregido un problema en el que no más de 5 archivos se podían descargar a la vez.

-   IAssetFile.Delete(): se ha corregido un problema en el que al llamar al método delete se puede producir una excepción si no se ha cargado ningún archivo para IAssetFile.

-   Trabajos: se ha corregido un problema en el que al encadenar una "tarea de MP4 a transmisiones por secuencias suaves" con una "tarea de protección de PlayReady" mediante una plantilla de trabajo, no se creaba ninguna tarea.

-   EncryptionUtils.GetCertificateFromStore(): este método ya no produce una excepción de referencia nula debido a errores de búsqueda del certificado basados en problemas de configuración del certificado.

## <span id="november_changes_12"></span></a>Versión de noviembre de 2012

Los cambios mencionados en esta sección eran actualizaciones incluidas en el SDK de noviembre de 2012 (versión 2.0.0.0). Estos cambios pueden requerir la modificación o reescritura de cualquier código escrito para la versión del SDK de vista previa de junio de 2012.

-   Activos

    IAsset.Create(assetName) es la ÚNICA función de creación de activos. IAsset.Create ya no carga archivos como parte de la llamada al método. Use IAssetFile para cargar.

    El método IAsset.Publish y el valor de enumeración AssetState.Publish se han eliminado del SDK de servicios. Todo código que dependa de este valor se debe reescribir.

-   FileInfo

    Esta clase se ha eliminado y se ha sustituido por IAssetFile.

    IAssetFiles

    IAssetFile sustituye a FileInfo y tiene un comportamiento diferente. Para usarla, cree una instancia del objeto IAssetFiles, seguida de una carga de archivos mediante el SDK de Servicios multimedia o el SDK de almacenamiento de Azure. Se pueden usar las siguientes sobrecargas de IAssetFile.Upload:

    -   IAssetFile.Upload(filePath): un método sincrónico que bloquea el subproceso y solo se recomienda al cargar un único archivo.

    -   IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): un método asincrónico. Este es el mecanismo de carga preferido.

    Error conocido: mediante cancellationToken se cancelará realmente la carga; sin embargo, el estado de cancelación de las tareas puede ser cualquiera de una serie de estados. Debe capturar y gestionar correctamente las excepciones.

-   Localizadores

    Las versiones específicas del origen se han eliminado. El valor context.Locators.CreateSasLocator(activo, accessPolicy) específico del contexto se marcará como en desuso o se eliminará con carácter general. Consulte la sección Localizadores en Nueva funcionalidad para ver el comportamiento actualizado.

## <span id="june_changes_12"></span></a>Versión de vista previa de junio de 2012

La siguiente funcionalidad era nueva en la versión de noviembre del SDK.

-   Eliminación de entidades

    Los objetos IAsset, IAssetFile, ILocator, IAccessPolicy y IContentKey se han eliminado ahora a nivel de objeto, es decir, IObject.Delete(), en lugar de exigir una eliminación en la colección, que es cloudMediaContext.ObjCollection.Delete(objInstance).

-   Localizadores

    Los localizadores se deben crear ahora usando el método CreateLocator y se deben usar los valores de enumeración LocatorType.SAS o LocatorType.OnDemandOrigin como argumento para el tipo específico de localizador que desea crear.

    Se han agregado nuevas propiedades a los localizadores para que sea más fácil obtener URL utilizables para su contenido. Este rediseño de los localizadores tenía como fin proporcionar una mayor flexibilidad para la futura extensibilidad de terceros y aumentar la facilidad de uso de las aplicaciones cliente multimedia.

-   Compatibilidad con el método asincrónico

    Se ha agregado compatibilidad asincrónica a todos los métodos.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [foro de MSDN de Servicios multimedia de Azure]: http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MediaServices
  [Problemas actualmente conocidos]: #issues
  [Historial de versiones de API de REST]: #rest_version_history
  [Versión de octubre de 2014]: #october_changes_14
  [Versión de septiembre de 2014]: #september_changes_14
  [Versión de agosto de 2014]: #august_changes_14
  [Versión de julio de 2014]: #july_changes_14
  [Versión de mayo de 2014]: #may_changes_14
  [Versión de abril de 2014]: #april_changes_14
  [Versiones de enero/febrero de 2014]: #jan_feb_changes_14
  [Versión de diciembre de 2013]: #december_changes_13
  [Versión de noviembre de 2013]: #november_changes_13
  [Versión de agosto de 2013]: #august_changes_13
  [Versión de junio de 2013]: #june_changes_13
  [Versión de diciembre de 2012]: #december_changes_12
  [Versión de noviembre de 2012]: #november_changes_12
  [Versión de vista previa de junio de 2012]: #june_changes_12
  [Referencia de la API de REST de Servicios multimedia de Azure]: http://msdn.microsoft.com/es-es/library/azure/hh973617.aspx
  [Detalles de precios de Servicios multimedia]: http://azure.microsoft.com/es-es/pricing/details/media-services/
  [StreamingEndpoint]: http://msdn.microsoft.com/es-es/library/azure/dn783468.aspx
  [Entrega de contenido]: http://msdn.microsoft.com/es-es/library/azure/hh973618.aspx
  [Indización de archivos multimedia con el Indizador multimedia de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn783455.aspx
  [Trabajo con Live Streaming de Servicios multimedia de Azure]: http://msdn.microsoft.com/es-es/library/azure/dn783466.aspx
  [Uso de cifrado dinámico AES-128 y Servicio de entrega de claves]: http://msdn.microsoft.com/es-es/library/azure/dn783457.aspx
  [Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady]: http://msdn.microsoft.com/es-es/library/azure/dn783467.aspx
  [Introducción a la plantilla de licencia de PlayReady de los Servicios multimedia]: http://msdn.microsoft.com/es-es/library/azure/dn783459.aspx
  [Contenido cifrado de almacenamiento de streaming]: http://msdn.microsoft.com/es-es/library/azure/dn783451.aspx
  [Metadatos de entrada]: http://msdn.microsoft.com/es-es/library/azure/dn783120.aspx
  [Metadatos de salida]: http://msdn.microsoft.com/es-es/library/azure/dn783217.aspx
  [empaquetado dinámico]: http://msdn.microsoft.com/es-es/library/azure/jj889436.aspx
  [blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
  [Protección de Smooth Streaming con PlayReady]: http://msdn.microsoft.com/es-es/library/azure/dn189154.aspx
  [Lógica de reintento en el SDK de Media Services para .NET]: http://msdn.microsoft.com/es-es/library/azure/dn745650.aspx
  [Grass Valley anuncia EDIUS 7 Streaming a través de la nube]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
  [Control de nombres de archivo de salida de Media Service Encoder]: http://msdn.microsoft.com/es-es/library/azure/dn303341.aspx
  [Creación de superposiciones]: http://msdn.microsoft.com/es-es/library/azure/dn640496.aspx
  [Unión de segmentos de vídeo]: http://msdn.microsoft.com/es-es/library/azure/dn640504.aspx
  [Github]: https://github.com/Azure/azure-sdk-for-media-services
  [SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
  [Servicio de control de acceso de Active Directory (ACS) de Azure]: http://msdn.microsoft.com/es-es/library/hh147631.aspx
  [Conectar con Media Services mediante el SDK de Media Services]: http://msdn.microsoft.com/es-es/library/azure/jj129571.aspx
  [Extensiones del SDK .NET de Servicios multimedia de Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
  [azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
  [Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento]: http://msdn.microsoft.com/es-es/library/azure/dn271889.aspx
  [Administrar notificaciones de trabajo de Media Services]: http://msdn.microsoft.com/es-es/library/azure/dn261241.aspx
