<properties 
	pageTitle="Notas de la versión de Servicios multimedia" 
	description="Notas de la versión de Servicios multimedia" 
	services="media-services" 
	documentationCenter="media" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="media" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="juliako"/>


# Notas de la versión de Servicios multimedia de Azure

Estas notas de la versión resumen los cambios realizados desde las versiones anteriores y los problemas conocidos.

>[AZURE.NOTE] Queremos recibir opiniones de nuestros clientes y centrarnos en la solución de los problemas que les afectan. Para informar de un problema o formular una pregunta, realice una entrada en el [foro de MSDN de Servicios multimedia de Azure].

- [Problemas actualmente conocidos](#issues)
- [Historial de versiones de API de REST](#rest_version_history)
- [Versión de enero de 2015](#january_changes_15)
- [Versión de diciembre de 2014](#december_changes_14)
- [Versión de noviembre de 2014](#november_changes_14)
- [Versión de octubre de 2014](#october_changes_14)
- [Versión de septiembre de 2014](#september_changes_14)
- [Versión de agosto de 2014](#august_changes_14)
- [Versión de julio de 2014](#july_changes_14)
- [Versión de mayo de 2014](#may_changes_14)
- [Versión de abril de 2014](#april_changes_14) 
- [Versiones de enero/febrero de 2014](#jan_feb_changes_14) 
- [Versión de diciembre de 2013](#december_changes_13)
- [Versión de noviembre de 2013](#november_changes_13)
- [Versión de agosto de 2013](#august_changes_13)
- [Versión de junio de 2013](#june_changes_13)
- [Versión de diciembre de 2012](#december_changes_12)
- [Versión de noviembre de 2012](#november_changes_12)
- [Versión de vista previa de junio de 2012](#june_changes_12)


##<a id="issues"></a>Problemas actualmente conocidos

### <a id="general_issues"></a>Problemas generales de Servicios multimedia

<table border="1">
<tr><th>Problema</th><th>Descripción</yt></tr>
<tr><td>Varios encabezados comunes HTTP no se proporcionan en la API de REST.</td><td>Si desarrolla aplicaciones de Servicios multimedia mediante la API de REST, encontrará que algunos campos de encabezado comunes HTTP (como CLIENT-REQUEST-ID, REQUEST-ID y RETURN-CLIENT-REQUEST-ID) no se admiten. Los encabezados se agregarán en una futura actualización.</td></tr>
<tr><td>Al codificar un activo con un nombre de archivo que contiene caracteres de escape (por ejemplo, %20), se muestra el error "MediaProcessor: archivo no encontrado".</td><td>Los nombres de archivos que se van a agregar a un activo y que luego se van a codificar deben contener caracteres alfanuméricos y espacios. El problema se corregirá en una futura actualización.</td></tr>
<tr><td>El método ListBlobs que es parte del SDK de almacenamiento de Azure, versión 3.x, no funciona correctamente.</td><td>Los Servicios multimedia generan URL de SAS basadas en la versión del <a href="http://msdn.microsoft.com/library/azure/dn592123.aspx">12/02/2012</a> . Si desea utilizar el SDK de almacenamiento de Azure para enumerar blobs en un contenedor de blob, utilice el método <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.listblobs.aspx">CloudBlobContainer.ListBlobs</a> que forma parte del SDK de almacenamiento de Azure versión 2.x. El método ListBlobs que es parte del SDK de almacenamiento de Azure, versión 3.x, no funcionará correctamente.</td></tr>
<tr><td>El mecanismo de limitación de Servicios multimedia restringe el uso de recursos en las aplicaciones que realizan un número excesivo de solicitudes al servicio. El servicio puede devolver el código de estado HTTP de servicio no disponible (503).</td><td>Para obtener más información, consulte la descripción del código de estado HTTP 503 en el tema <a href="http://msdn.microsoft.com/library/azure/dn168949.aspx">Códigos de error de Servicios multimedia de Azure.</a> .</td></tr>
</table><br/>

### <a id="dotnet_issues"></a>Problemas del SDK de Servicios multimedia para .NET

<table border="1">
<tr><th>Problema</th><th>Descripción</yt></tr>
<tr><td>Los objetos de Servicios multimedia del SDK no se pueden serializar y, como resultado, no funcionan con el almacenamiento en caché de Azure.</td><td>Si intenta serializar el objeto AssetCollection del SDK para agregarlo al almacenamiento en caché de Azure, se produce una excepción.</td></tr>
</table><br/>

##<a id="rest_version_history"></a>Historial de versiones de API de REST

Para obtener información sobre el historial de versiones de la API de REST de Servicios multimedia, consulte [Referencia de la API de REST de Servicios multimedia de Azure].

##<a id="january_changes_15"></a>Versión de enero de 2015

### Actualizaciones generales de Servicios multimedia

Anuncio de disponibilidad General (GA) de protección de contenido con cifrado dinámico. Para más información, vea [Los Servicios multimedia de Azure mejoran la seguridad de streaming con la disponibilidad general de tecnología DRM](http://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

###Actualizaciones del SDK .NET de Servicios multimedia

La versión actual del SDK de Servicios multimedia de Azure para .NET es 3.1.0.1.

Esta versión marcó el constructor Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization.TokenRestrictionTemplate como obsoleto. El nuevo constructor toma TokenType como argumento.

	TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);


##<a id="december_changes_14"></a>Versión de diciembre de 2014

###Actualizaciones generales de Servicios multimedia

- Se agregaron algunas actualizaciones y nuevas características al procesador multimedia del indizador de Azure. Para obtener más información, consulte [Notas de la versión del indizador multimedia de Azure versión 1.1.6.7](http://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
- Se agregó una nueva API de REST que permite actualizar unidades reservadas de codificación: [EncodingReservedUnitType con REST](http://msdn.microsoft.com/library/azure/dn859236.aspx).
- Se agregó compatibilidad con CORS para el servicio de entrega de claves.
- Se realizaron mejoras en el rendimiento de la directiva de autorización de consultas.
- En el centro de datos de China, la [dirección URL de entrega de claves](http://msdn.microsoft.com/library/azure/ef4dfeeb-48ae-4596-ab28-44d6b36d8769#get_delivery_service_url) es ahora por cliente (al igual que en otros centros de datos).
- Se agregó la duración de destino automático de HLS. Cuando se realiza el streaming en vivo, HLS siempre se empaqueta dinámicamente. De forma predeterminada, Servicios multimedia calcula automáticamente la relación de empaquetado por segmento HLS (FragmentsPerSegment) según el intervalo de fotogramas clave (KeyFrameInterval), también denominado grupo de imágenes, GOP, que se recibe del codificador en vivo. Para obtener más información, consulte [Trabajo con Live Streaming de Servicios multimedia de Azure].
 
###Actualizaciones del SDK .NET de Servicios multimedia

- [SDK .NET de Servicios multimedia de Azure](http://www.nuget.org/packages/windowsazure.mediaservices/) ahora es la versión 3.1.0.0
- Se ha actualizado la dependencia del SDK de .Net a .NET Framework 4.5.
- Se agregó una nueva API que permite actualizar unidades reservadas de codificación. Para obtener más información, consulte [Actualización del tipo de unidades reservadas y aumento de las unidades reservadas de codificación mediante .NET](http://msdn.microsoft.com/library/azure/jj129582.aspx).
- Se agregó compatibilidad con JWT (token web de JSON) para la autenticación de token. Para obtener más información, consulte [Autenticación de token JWD en Servicios multimedia y Cifrado dinámico de Azure](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
- Se agregaron desplazamientos relativos para BeginDate y ExpirationDate en la plantilla de licencia de PlayReady.


##<a id="november_changes_14"></a>Versión de noviembre de 2014

- Servicios multimedia permite introducir un contenido de Smooth Streaming (FMP4) en vivo en una conexión SSL. Para introducir en SSL, asegúrese de actualizar la dirección URL de introducción a HTTPS.  Para obtener más información acerca del streaming en vivo, consulte [Trabajar con streaming en vivo de Servicios multimedia de Azure].
- Tenga en cuenta que, en la actualidad, no se puede introducir un stream en vivo RTMP por una conexión SSL.
- También puede transmitir el contenido por una conexión SSL. Para ello, asegúrese de que las URL de streaming comienzan por HTTPS.
- Tenga en cuenta que solo puede transmitir por SSL si se creó el extremo de streaming desde el que se entrega el contenido a partir del 10 de septiembre de 2014. Si las direcciones URL de streaming se basan en los extremos de streaming creados después del 10 de septiembre, la dirección URL contendrá "streaming.mediaservices.windows.net" (el formato nuevo). Las direcciones URL de streaming que contengan "origin.mediaservices.windows.net" (el formato anterior) no son compatibles con SSL. Si la dirección URL tiene el formato antiguo y desea poder transmitir a través de SSL, [cree un nuevo extremo de streaming](http://azure.microsoft.com/ documentation/articles/media-services-manage-origins/). Utilice direcciones URL creadas en función del nuevo extremo de streaming para transmitir el contenido a través de SSL.
   
##<a id="october_changes_14"></a>Versión de octubre de 2014

### <a id="new_encoder_release"></a>Versión del codificador de Servicios multimedia

Anuncio de la nueva versión del codificador de Servicios multimedia de Azure. Con la última versión del codificador de Servicios multimedia de Azure solo se le cobran los GB de salida, pero, por lo demás, el nuevo codificador es una característica compatible con el codificador de Servicios multimedia de Microsoft Azure. Para obtener más información, consulte [Detalles de precios de Servicios multimedia].

### <a id="oct_sdk"></a>SDK .NET de Servicios multimedia 

La versión de las extensiones del SDK de Servicios multimedia para .NET es ahora la 2.0.0.3.

La versión del SDK de Servicios multimedia para .NET es ahora la 3.0.0.8.

Se han realizado los siguientes cambios:

- Refactorización de clases de directivas de reintento.
- Adición de una cadena de agente de usuario a los encabezados de solicitud http.
- Adición del paso de compilación de restauración de nuget.
- Corrección de pruebas del escenario para utilizar el certificado x509 del repositorio.
- Validación de la configuración al actualizar el canal y extremo de streaming.
 

### Nuevo repositorio de GitHub para hospedar las muestras de Servicios multimedia

Las muestras se encuentran en el [repositorio de GitHub de muestras de Servicios multimedia de Azure](https://github.com/Azure/Azure-Media-Services-Samples).


##<a id="september_changes_14"></a>Versión de septiembre de 2014

La versión de los metadatos de REST de Servicios multimedia es ahora la 2.7. Para obtener más información sobre las últimas actualizaciones de REST, consulte [Referencia de la API de REST de Servicios multimedia de Azure].

El SDK de Servicios multimedia para .NET es ahora la versión 3.0.0.7.
 
### <a id="sept_14_breaking_changes"></a>Cambios importantes

* El nombre de **Origen** ha cambiado a [StreamingEndpoint].
* Un cambio en el comportamiento predeterminado al usar el **Portal de administración de Azure** para codificar y luego publicar archivos MP4. 

	Anteriormente, cuando se usaba el Portal de administración para publicar un activo de vídeo de un solo archivo MP4, se creaba una URL de SAS (las URL de SAS le permiten descargar el vídeo de un almacenamiento de blobs). Ahora, cuando utiliza el Portal de administración para codificar y luego publicar un activo de vídeo MP4 de un solo archivo, la URL generada apunta a un extremo de streaming de Servicios multimedia de Azure.  Este cambio no afecta a los vídeos MP4 que se cargan directamente en Servicios multimedia y se publican sin ser codificados por Servicios multimedia de Azure.
	
	Actualmente, cuenta con las dos opciones siguientes para resolver el problema. 
	
	* Habilitar las unidades de streaming y usar el empaquetado dinámico para transmitir por secuencias el activo de .mp4 como una presentación Smooth Streaming.
	
	* Crear una URL de SAS para descargar (o reproducir de forma progresiva) el .mp4. Para obtener más información sobre cómo crear un localizador de SAS, consulte [Entrega de contenido]. 


### <a id="sept_14_GA_changes"></a>Nuevas características/supuestos que forman parte de la versión GA

* **Procesador multimedia de indización**. Para obtener más información, consulte [Indización de archivos multimedia con el Indizador multimedia de Azure].

* La entidad [StreamingEndpoint] ahora le permite agregar nombres de dominio personalizados (host).

	Para poder usar un nombre de dominio personalizado como nombre del extremo de streaming de Servicios multimedia, debe agregar nombres de host personalizados a su extremo de streaming. Use las API de REST de Servicios multimedia o el SDK .NET para agregar nombres de host personalizados.
	
	Se aplican las siguientes consideraciones:
	
	* Debe tener la propiedad del nombre de dominio personalizado.
	
	* Y esta propiedad se debe validar mediante Servicios multimedia de Azure. Para validar el dominio, cree un CName que asigne <MediaServicesAccountId>.<parent domain> a verifydns.<mediaservices-dns-zone>. 
	
	* Debe crear otro CName que asigne el nombre de host personalizado (por ejemplo, sports.contoso.com) al nombre de host de su StreamingEndpoint de Servicios multimedia (por ejemplo, amstest.streaming.mediaservices.windows.net).


	Para obtener más información, consulte la propiedad **CustomHostNames** en el tema [StreamingEndpoint].

### <a id="sept_14_preview_changes"></a>Nuevas características/supuestos que forman parte de la versión de vista previa pública

* Vista previa de Live Streaming. Para obtener más información, consulte [Trabajo con Live Streaming de Servicios multimedia de Azure].

* Servicio de entrega de claves. Para obtener más información, consulte [Uso de cifrado dinámico AES-128 y Servicio de entrega de claves].

* Cifrado dinámico AES. Para obtener más información, consulte [Uso de cifrado dinámico AES-128 y Servicio de entrega de claves].

* Servicio de entrega de licencias PlayReady. Para obtener más información, consulte [Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady].

* Cifrado dinámico PlayReady. Para obtener más información, consulte [Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady].

* Plantilla de licencias PlayReady de Servicios multimedia. Para obtener más información, consulte [Introducción a la plantilla de licencia de PlayReady de los Servicios multimedia].

* Activos cifrados de almacenamiento de streaming. Para obtener más información, consulte [Contenido cifrado de almacenamiento de streaming].

##<a id="august_changes_14"></a>Versión de agosto de 2014

Cuando se codifica un activo, se produce un activo de salida tras la finalización del trabajo de codificación. Hasta esta versión, el codificador de Servicios multimedia de Azure producía metadatos sobre activos de salida. A partir de esta versión, el codificador también produce metadatos sobre activos de entrada. Para obtener más información, consulte los temas [Metadatos de entrada] y [Metadatos de salida].


##<a id="july_changes_14"></a>Versión de julio de 2014

Se han corregido los siguientes errores para Azure Media Services Packager y Encryptor:

* Solo se reproduce el audio cuando se transmite un activo de un archivo en vivo a HTTP Live Streaming: este error se ha corregido y ahora se reproduce tanto el audio como el vídeo.

* Al empaquetar un activo con HTTP Live Streaming y cifrado de sobre AES de 128 bits, las secuencias empaquetadas no se reproducen en dispositivos Android: este error se ha corregido y la secuencia empaquetada se reproduce en dispositivos Android compatibles con HTTP Live Streaming.

##<a id="may_changes_14"></a>Versión de mayo de 2014

### <a id="may_14_changes"></a>Actualizaciones generales de Servicios multimedia

Ahora puede usar [empaquetado dinámico] para transmitir HTTP Live Streaming (HLS) v3. Para transmitir por secuencias HLS v3, agregue el siguiente formato a la ruta del localizador de origen: *.ism/manifest(format=m3u8-aapl-v3). Para obtener más información, consulte el [blog de Nick Drouin].

El empaquetado dinámico admite ahora también la entrega de HLS (v3 y v4) cifrado con PlayReady basado en Smooth Streaming cifrado estáticamente con PlayReady. Para obtener información sobre cómo cifrar Smooth Streaming con PlayReady, consulte [Protección de Smooth Streaming con PlayReady].

### <a name="may_14_donnet_changes"></a>Actualizaciones del SDK .NET de Servicios multimedia

Las siguientes mejoras se incluyen en la versión 3.0.0.5 del SDK .NET de Servicios multimedia:

* Mayor velocidad y resistencia para cargar/descargar activos multimedia.

* Mejoras en la gestión de excepciones transitorias y la lógica de reintento: 

	* La detección de errores transitorios y la lógica de reintento se han mejorado en las excepciones ocasionadas al consultar, guardar cambios y cargar y descargar archivos. 
	
	* Al recibir excepciones web (por ejemplo, durante una solicitud de token de ACS), observará que ahora se producen con mayor rapidez errores graves.

Para obtener más información, consulte [Lógica de reintento en el SDK de Servicios multimedia para .NET].

##<a id="april_changes_14"></a>Versión del codificador de abril de 2014

### <a name="april_14_enocer_changes"></a>Actualizaciones del codificador de Servicios multimedia

* Se ha agregado compatibilidad con la ingesta de archivos AVI creados con el editor no lineal EDIUS de Grass Valley, donde el vídeo está comprimido ligeramente mediante el códec HQ/HQX de Grass Valley. Para obtener más información, consulte [Grass Valley anuncia EDIUS 7 Streaming a través de la nube].

* Se ha agregado compatibilidad con la especificación de la convención de nomenclatura para los archivos producidos por el codificador multimedia. Para obtener más información, consulte [Control de nombres de archivo de salida del codificador de Servicios multimedia].

* Se ha agregado compatibilidad con superposiciones de vídeo y/o audio. Para obtener más información, consulte [Creación de superposiciones].

* Se ha agregado compatibilidad con la unión de varios segmentos de vídeo. Para obtener más información, consulte [Unión de segmentos de vídeo].

* Se ha corregido un error relacionado con la transcodificación de MP4 en el que el audio se ha codificado con la capa 3 de nivel de audio de MPEG-1 (conocido como MP3).


##<a id="jan_feb_changes_14"></a>Versiones de enero/febrero de 2014

### <a name="jan_fab_14_donnet_changes"></a>SDK .NET de Servicios multimedia 3.0.0.1, 3.0.0.2 y 3.0.0.3

Los cambios en 3.0.0.1 y 3.0.0.2 incluyen los siguientes:

* Se han corregido los problemas relacionados con el uso de consultas LINQ con instrucciones OrderBy.

* Se han dividido las soluciones de prueba de [Github] en pruebas basadas en unidades y pruebas basadas en supuestos.

Para obtener más información sobre los cambios, consulte: [SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2].

Se han realizado los siguientes cambios en la versión 3.0.0.3:

* Se han actualizado las dependencias de almacenamiento de Azure para usar la versión 3.0.3.0. 

* Se ha corregido el problema de compatibilidad con versiones posteriores en las versiones 3.0.*.* 


##<a id="december_changes_13"></a>Versión de diciembre de 2013

### <a name="dec_13_donnet_changes"></a>SDK .NET de Servicios multimedia de Azure 3.0.0.0

>[AZURE.NOTE] Las versiones 3.0.x.x no son compatibles con las versiones 2.4.x.x.

La última versión del SDK de Servicios multimedia es ahora la 3.0.0.0. Puede descargar el último paquete de Nuget u obtener los bits de [Github].

A partir de la versión 3.0.0.0 del SDK de Servicios multimedia, puede reutilizar los tokens del [Servicio de control de acceso de Active Directory (ACS) de Azure]. Para obtener más información, consulte la sección "Reutilización de tokens de Access Control Service" del tema [Conexión con Servicios multimedia mediante el SDK de .NET].

### <a name="dec_13_donnet_ext_changes"></a>Extensiones del SDK .NET de Servicios multimedia de Azure 2.0.0.0

Las extensiones del SDK .NET de Servicios multimedia de Azure son un conjunto de métodos de extensión y funciones auxiliares que simplificarán su código y facilitarán el desarrollo con Servicios multimedia de Azure. Puede obtener los bits más recientes en [Extensiones del SDK .NET de Servicios multimedia de Azure].

##<a id="november_changes_13"></a>Versión de noviembre de 2013

### <a name="nov_13_donnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

A partir de esta versión, el SDK de Servicios multimedia para .NET gestiona los errores transitorios que se pueden producir al realizar llamadas a la capa API de REST de Servicios multimedia.

##<a id="august_changes_13"></a>Versión de agosto de 2013

### <a name="aug_13_powershell_changes"></a>Cmdlets de PowerShell de Servicios multimedia incluidos en las herramientas del SDK de Azure

Los siguientes cmdlets de PowerShell de Servicios multimedia se incluyen ahora en [azure-sdk-tools].

* Get-AzureMediaServices 

	Por ejemplo, `Get-AzureMediaServicesAccount`.

* New-AzureMediaServicesAccount 

	Por ejemplo, `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`.

* New-AzureMediaServicesKey 

	Por ejemplo, `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`.

* Remove-AzureMediaServicesAccount 

	Por ejemplo, `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`.

##<a id="june_changes_13"></a>Versión de junio de 2013

### <a name="june_13_general_changes"></a>Cambios en los Servicios multimedia de Azure

Los cambios mencionados en esta sección son actualizaciones incluidas en las versiones de Servicios multimedia de 2013.

* Posibilidad de vincular varias cuentas de almacenamiento a una cuenta de Servicios multimedia. 

	StorageAccount
	
	Asset.StorageAccountName and Asset.StorageAccount

* Posibilidad de actualizar Job.Priority. 

* Entidades y propiedades relacionadas con notificaciones: 

	JobNotificationSubscription
	
	NotificationEndPoint
	
	Job

* Asset.Uri 

* Locator.Name 

### <a name="june_13_dotnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

Los siguientes cambios se incluyen en las versiones del SDK de Servicios multimedia de junio de 2013. El último SDK de Servicios multimedia está disponible en GitHub.

* A partir de la versión 2.3.0.0, el SDK de Servicios multimedia admite la vinculación de varias cuentas de almacenamiento a una cuenta de Servicios multimedia. Las siguientes API admiten esta característica:
	
	El tipo IStorageAccount.
	
	La propiedad Microsoft.WindowsAzure.MediaServices.Client.CloudMediaContext.StorageAccounts.
	
	La propiedad StorageAccount.
	
	La propiedad StorageAccountName.
	
	Para obtener más información, consulte [Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento].

* API relacionadas con notificaciones. A partir de la versión 2.2.0.0, tiene la posibilidad de escuchar notificaciones de almacenamiento de la cola de Azure. Para obtener más información, consulte [Control de notificaciones de trabajos de Servicios multimedia].
	
	La propiedad Microsoft.WindowsAzure.MediaServices.Client.IJob.JobNotificationSubscriptions.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.INotificationEndPoint.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.IJobNotificationSubscription.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointCollection.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationEndPointType.
	
	El tipo Microsoft.WindowsAzure.MediaServices.Client.NotificationJobState.

* Dependencia del SDK del cliente de almacenamiento de Azure 2.0 (Microsoft.WindowsAzure.StorageClient.dll).

* Dependencia de OData 5.5 (Microsoft.Data.OData.dll).


##<a id="december_changes_12"></a>Versión de diciembre de 2012

### <a name="dec_12_dotnet_changes"></a>Cambios en el SDK .NET de Servicios multimedia de Azure

* Intellisense: se ha agregado documentación de Intellisense que faltaba para muchos tipos.

* Microsoft.Practices.TransientFaultHandling.Core: se ha corregido un problema en el que el SDK tenía aún dependencia con una versión anterior de este ensamblado. El SDK hace referencia ahora a la versión 5.1.1209.1 de este ensamblado.

Correcciones de problemas encontrados en el SDK de noviembre de 2012:

* IAsset.Locators.Count: este recuento se notifica ahora correctamente en las nuevas interfaces IAsset después de que todos los localizadores se han eliminado.

* IAssetFile.ContentFileSize: este valor está ahora correctamente definido después de una carga por IAssetFile.Upload(filepath).

* IAssetFile.ContentFileSize: esta propiedad se puede establecer ahora al crear un archivo de activo. Anteriormente era de solo lectura.

* IAssetFile.Upload(filepath): se ha corregido un problema en el que el método de carga sincrónico mostraba el siguiente error al cargar varios archivos en el activo. El error era "El servidor no pudo autenticar la solicitud. Asegúrese de que el valor del encabezado de autenticación tenga el formato correcto, incluida la firma".

* IAssetFile.UploadAsync: se ha corregido un problema en el que no más de 5 archivos se podían cargar a la vez.

* IAssetFile.UploadProgressChanged: este evento se proporciona ahora en el SDK.

* IAssetFile.DownloadAsync(string, BlobTransferClient, ILocator, CancellationToken): Ahora se proporciona esta sobrecarga del método.

* IAssetFile.DownloadAsync: se ha corregido un problema en el que no más de 5 archivos se podían descargar a la vez.

* IAssetFile.Delete(): se ha corregido un problema en el que al llamar al método delete se puede producir una excepción si no se ha cargado ningún archivo para IAssetFile.

* Trabajos: se ha corregido un problema en el que al encadenar una "tarea de MP4 a transmisiones por secuencias suaves" con una "tarea de protección de PlayReady" mediante una plantilla de trabajo, no se creaba ninguna tarea.

* EncryptionUtils.GetCertificateFromStore(): este método ya no produce una excepción de referencia nula debido a errores de búsqueda del certificado basados en problemas de configuración del certificado.

##<a id="november_changes_12"></a>Versión de noviembre de 2012

Los cambios mencionados en esta sección eran actualizaciones incluidas en el SDK de noviembre de 2012 (versión 2.0.0.0). Estos cambios pueden requerir la modificación o reescritura de cualquier código escrito para la versión del SDK de vista previa de junio de 2012.

* Activos
	
	IAsset.Create(assetName) es la ÚNICA función de creación de activos. IAsset.Create ya no carga archivos como parte de la llamada al método. Use IAssetFile para cargar.
	
	El método IAsset.Publish y el valor de enumeración AssetState.Publish se han eliminado del SDK de servicios. Todo código que dependa de este valor se debe reescribir.

* FileInfo

	Esta clase se ha eliminado y se ha sustituido por IAssetFile.

	IAssetFiles

	IAssetFile sustituye a FileInfo y tiene un comportamiento diferente. Para usarla, cree una instancia del objeto IAssetFiles, seguida de una carga de archivos mediante el SDK de Servicios multimedia o el SDK de almacenamiento de Azure. Se pueden usar las siguientes sobrecargas de IAssetFile.Upload:

	* IAssetFile.Upload(filePath): un método sincrónico que bloquea el subproceso y solo se recomienda al cargar un único archivo.
	
	* IAssetFile.UploadAsync(filePath, blobTransferClient, locator, cancellationToken): un método asincrónico. Este es el mecanismo de carga preferido. 

	Error conocido: mediante cancellationToken se cancelará realmente la carga; sin embargo, el estado de cancelación de las tareas puede ser cualquiera de una serie de estados. Debe capturar y gestionar correctamente las excepciones.

* Localizadores
	
	Las versiones específicas del origen se han eliminado. El context.Locators.CreateSasLocator(asset, accessPolicy) específico de SAS se marcará como obsoleto o se eliminará de la disponibilidad general. Consulte la sección Localizadores en Nuevas funcionalidades del comportamiento actualizado.


##<a id="june_changes_12"></a>Versión de vista previa de junio de 2012

La siguiente funcionalidad era nueva en la versión de noviembre del SDK.

* Eliminación de entidades

	Los objetos IAsset, IAssetFile, ILocator, IAccessPolicy y IContentKey se han eliminado ahora a nivel de objeto, es decir, IObject.Delete(), en lugar de exigir una eliminación en la colección, que es cloudMediaContext.ObjCollection.Delete(objInstance).

* Localizadores

	Los localizadores se deben crear ahora usando el método CreateLocator y se deben usar los valores de enumeración LocatorType.SAS o LocatorType.OnDemandOrigin como argumento para el tipo específico de localizador que desea crear.

	Se han agregado nuevas propiedades a los localizadores para que sea más fácil obtener URL utilizables para su contenido. Este rediseño de los localizadores tenía como fin proporcionar una mayor flexibilidad para la futura extensibilidad de terceros y aumentar la facilidad de uso de las aplicaciones cliente multimedia.

* Compatibilidad con el método asincrónico

	Se ha agregado compatibilidad asincrónica a todos los métodos.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Foro de MSDN de Servicios multimedia de Azure]: http://social.msdn.microsoft.com/forums/azure/en-US/home?forum=MediaServices
[Referencia de la API de REST de Servicios multimedia de Azure]: http://msdn.microsoft.com/library/azure/hh973617.aspx 
[Detalles de precios de Servicios multimedia]: http://azure.microsoft.com/ pricing/details/media-services/
[Metadatos de entrada]: http://msdn.microsoft.com/library/azure/dn783120.aspx
[Metadatos de salida]: http://msdn.microsoft.com/library/azure/dn783217.aspx
[Entrega de contenido]: http://msdn.microsoft.com/library/azure/hh973618.aspx
[Indización de archivos multimedia con el Indizador multimedia de Azure]: http://msdn.microsoft.com/library/azure/dn783455.aspx
[StreamingEndpoint]: http://msdn.microsoft.com/library/azure/dn783468.aspx
[Trabajo con Live Streaming de Servicios multimedia de Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Trabajar con streaming en vivo de Servicios multimedia de Azure]: http://msdn.microsoft.com/library/azure/dn783466.aspx
[Uso de cifrado dinámico AES-128 y Servicio de entrega de claves]: http://msdn.microsoft.com/library/azure/dn783457.aspx
[Uso del cifrado dinámico y del Servicio de entrega de licencias PlayReady]: http://msdn.microsoft.com/library/azure/dn783467.aspx
[Vista previa de características]: http://azure.microsoft.com/ services/preview/
[Introducción a la plantilla de licencia de PlayReady de los Servicios multimedia]: http://msdn.microsoft.com/library/azure/dn783459.aspx
[Contenido cifrado de almacenamiento de streaming]: http://msdn.microsoft.com/library/azure/dn783451.aspx
[Portal de administración de Azure]: https://manage.windowsazure.com
[Empaquetado dinámico]: http://msdn.microsoft.com/library/azure/jj889436.aspx
[Blog de Nick Drouin]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protección de Smooth Streaming con PlayReady]: http://msdn.microsoft.com/library/azure/dn189154.aspx
[Lógica de reintento en el SDK de Servicios multimedia para .NET]: http://msdn.microsoft.com/library/azure/dn745650.aspx
[Grass Valley anuncia EDIUS 7 Streaming a través de la nube]: http://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control de nombres de archivo de salida del codificador de Servicios multimedia]: http://msdn.microsoft.com/library/azure/dn303341.aspx
[Creación de superposiciones]: http://msdn.microsoft.com/library/azure/dn640496.aspx
[Unión de segmentos de vídeo]: http://msdn.microsoft.com/library/azure/dn640504.aspx
[SDK .NET de Servicios multimedia de Azure 3.0.0.1 y 3.0.0.2]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Servicio de control de acceso de Active Directory (ACS) de Azure]: http://msdn.microsoft.com/library/hh147631.aspx
[Conexión con Servicios multimedia mediante el SDK de .NET]: http://msdn.microsoft.com/library/azure/jj129571.aspx
[Extensiones del SDK .NET de Servicios multimedia de Azure]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[azure-sdk-tools]: https://github.com/Azure/azure-sdk-tools
[Github]: https://github.com/Azure/azure-sdk-for-media-services
[Administración de recursos de Servicios multimedia entre varias cuentas de almacenamiento]: http://msdn.microsoft.com/library/azure/dn271889.aspx
[Control de notificaciones de trabajos de Servicios multimedia]: http://msdn.microsoft.com/library/azure/dn261241.aspx

<!--HONumber=45--> 
