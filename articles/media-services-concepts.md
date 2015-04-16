<properties 
	pageTitle="Conceptos de Servicios multimedia de Azure" 
	description="Este tema proporciona información general sobre los conceptos de Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/02/2015"
	ms.author="juliako"/>

# Conceptos de Servicios multimedia de Azure 

Este tema proporciona información general sobre los conceptos más importantes de Servicios multimedia.

## <a id="assets"></a>Recursos \ almacenamiento

### Recursos

Un [recurso](https://msdn.microsoft.com/library/azure/hh974277.aspx) contiene activos digitales (incluido vídeo, audio, imágenes, colecciones en miniatura, pistas de texto y archivos de subtítulos) y los metadatos sobre estos archivos. Una vez que los archivos digitales se cargan en un recurso, se pueden utilizar en los flujos de trabajo de codificación y streaming en Servicios multimedia.

Un recurso se asigna a un contenedor de blobs en la cuenta de almacenamiento de Azure y los archivos del recurso se almacenan como blobs en ese contenedor.

Cuando se decide qué contenido multimedia cargar y almacenar en un recurso, se aplican las siguientes consideraciones:

- Un recurso debe contener solo una instancia única de contenido multimedia. Por ejemplo, una edición única de un episodio de televisión, una película o un anuncio.
- Un recurso no puede contener varias representaciones o ediciones de un archivo audiovisual. Un ejemplo de uso inadecuado de un recurso sería intentar almacenar más de un episodio de televisión, anuncio o varios ángulos de cámara desde una sola producción dentro de un recurso. Almacenar varias representaciones o ediciones de un archivo audiovisual en un recurso puede generar dificultades en el envío de trabajos de codificación, en la transmisión y protección de la entrega del recurso más adelante en el flujo de trabajo.  

### Archivo de recursos 
Un [AssetFile](https://msdn.microsoft.com/library/azure/hh974275.aspx) representa un archivo de vídeo o audio real almacenado en un contenedor de blobs. Un archivo de recursos siempre está asociado con un recurso y un recurso puede contener uno o varios archivos. La tarea de Servicios multimedia produce un error si un objeto de archivo de recursos no está asociado a un archivo digital de un contenedor de blobs.

La instancia de **AssetFile** y el archivo multimedia real son dos objetos distintos. La instancia AssetFile contiene metadatos sobre el archivo multimedia, mientras que el archivo multimedia contiene el contenido multimedia real.

No debe intentar cambiar el contenido de los contenedores de blobs que generó Servicios multimedia sin usar las API de Servicios multimedia.

### Opciones de cifrado de recursos

Según el tipo de contenido que desea cargar, almacenar y entregar, Servicios multimedia proporciona varias opciones de cifrado de las que puede elegir.

**None**: no se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al utilizar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento.

Si planifica entregar un MP4 mediante una descarga progresiva, use esta opción para cargar el contenido.

**StorageEncrypted**: use esta opción para cifrar el contenido no cifrado de manera local mediante el cifrado AES de 256 bits y, a continuación, cargarlo al almacenamiento de Azure donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco. 

Para entregar a un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso para que Servicios multimedia sepa cómo desea entregar el contenido. Antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada (por ejemplo, AES, PlayReady o sin cifrado). 

**CommonEncryption**: utilice esta opción si desea cifrar contenido o cargar contenido ya cifrado con cifrado común o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con DRM de PlayReady).

**EnvelopeEncrypted**: utilice esta opción si desea proteger HTTP Live Streaming (HLS) cifrado (o cargar uno ya protegido) con estándar de cifrado avanzado (AES). Tenga en cuenta que si carga HLS ya cifrado con AES, se debe haber cifrado con Transform Manager.

### Directiva de acceso 

Un [AccessPolicy](https://msdn.microsoft.com/library/azure/hh974297.aspx) define los permisos (como lectura, escritura y lista) y la duración de acceso a un recurso. Normalmente pasaría un objeto AccessPolicy a un localizador que luego se usaría para tener acceso a los archivos contenidos en un recurso.


### Contenedor de blobs

Un contenedor de blobs proporciona una agrupación de un conjunto de blobs. Los contenedores de blobs se usan en Servicios multimedia como punto limítrofe para el control de acceso y localizadores de firma de acceso compartido (SAS) en los recursos. Una cuenta de almacenamiento de Azure puede contener una cantidad ilimitada de contenedores de blobs. un contenedor puede almacenar un número ilimitado de blobs.

>[AZURE.NOTE]No debe intentar cambiar el contenido de los contenedores de blobs que generó Servicios multimedia sin usar las API de Servicios multimedia.

### <a id="locators"></a>Localizadores

Los [localizador](https://msdn.microsoft.com/library/azure/hh974308.aspx)es proporciona un punto de entrada para tener acceso a los archivos contenidos en un recurso. Se usa una directiva de acceso para definir los permisos y la duración en que un cliente tiene acceso a un recurso determinado. Los localizadores pueden tener de varias a una relación con una directiva de acceso, de manera tal que distintos localizadores pueden proporcionar distintas horas de inicio y tipos de conexión a distintos clientes, mientras que todos usan la misma configuración de permiso y duración; sin embargo, debido a una restricción en la directiva de acceso compartido definida por los servicios de almacenamiento de Azure, no puede tener más de cinco localizadores únicos asociados con un recurso determinado a la vez. 

Servicios multimedia admite dos tipos de localizadores: Los localizadores OnDemandOrigin que se usan para transmitir multimedia (por ejemplo, MPEG DASH, HLS o Smooth Streaming) o para descargar multimedia de manera progresiva, y los localizadores SAS URL, que se usan para cargar o descargar archivos multimedia hacia y desde el almacenamiento de Azure. 

Observe que el permiso de lista (AccessPermissions.List) no se debe usar al crear un localizador OnDemandOrigin. 

### Cuenta de almacenamiento

todo el acceso al almacenamiento de Azure se realiza a través de una cuenta de almacenamiento. Una cuenta de Servicios multimedia se puede asociar con una o más cuentas de almacenamiento. Una cuenta puede contener una cantidad ilimitada de contenedores, siempre que su tamaño total no supere los 500 TB por cuenta de almacenamiento.  Servicios multimedia proporciona herramientas del nivel de SDK que le permiten administrar varias cuentas de almacenamiento y equilibrar la carga de la distribución de sus recursos durante la carga a estas cuentas según métricas o una distribución aleatoria. Para obtener más información, consulte Trabajo con [almacenamiento de Azure](https://msdn.microsoft.com/library/azure/dn767951.aspx). 

## Trabajos \ tareas

Un [trabajo](https://msdn.microsoft.com/library/azure/hh974289.aspx) se usa normalmente para procesar (por ejemplo, indexar o codificar) una presentación de audio/vídeo. Si procesa varios vídeos, cree un trabajo para cada vídeo que se va a codificar.

Un trabajo contiene metadatos acerca del procesamiento que se realizará. Cada trabajo contiene una o más [tarea](https://msdn.microsoft.com/library/azure/hh974286.aspx)s que especifican una tarea de procesamiento atómica, sus recursos de entrada, recursos de salida, un procesador de multimedia y su configuración asociada. Las tareas dentro de un trabajo se pueden encadenar en conjunto, donde el recurso de salida de una de las tareas se indica como el recurso de entrada de la tarea siguiente. De este modo, un trabajo puede contener todos los procesos necesarios para una presentación multimedia.

## <a id="encoding"></a>Codificación \ empaquetado

### Codificación

Para entregar vídeo digital a través de Internet, debe comprimir los archivos multimedia. Los archivos de vídeo digital son bastante grandes y pueden ser demasiado pesados para entregarlos a través de Internet o para que los dispositivos de sus clientes los muestren correctamente. Las personas ven vídeos en diversos dispositivos, que van desde televisores con decodificadores y equipos de escritorio hasta tabletas y smartphones. Cada uno de estos dispositivos tiene distintos requisitos de compresión y ancho de banda. La codificación es el proceso de comprimir vídeo y audio con compresores/descompresores o códecs. 

La transcodificación es el proceso de tomar un vídeo que se ha codificado y volver a codificarlo en un formato de codificación distinto. Como la mayoría de las cámaras codifican vídeo en cierta medida, la mayoría del trabajo de codificación realizado en Servicios multimedia de Azure es, técnicamente, transcodificación.

### Códecs y formatos de archivo 

Los códecs tienen dos componentes: uno para comprimir archivos multimedia digitales para su transmisión y el otro para descomprimir los archivos multimedia digitales para su reproducción. Existen códecs de audio que comprimen y descomprimen el audio y códecs de vídeo que comprimen y descomprimen vídeo. Los códecs pueden utilizar compresión sin pérdida de información o compresión con pérdida de información. Los códecs de compresión sin pérdida de información conservan toda la información cuando se produce la compresión. Cuando se descomprime el archivo, el resultado es un archivo idéntico al archivo multimedia de entrada, lo que hace que los códecs de compresión sin pérdida de información sean muy apropiados para el archivado y el almacenamiento. Los códecs con pérdida de información pierden parte de la información al codificar y generan archivos más pequeños (que el original) a costa de la calidad del vídeo, y son muy apropiados para la transmisión a través de Internet. Los dos principales códecs que el Codificador multimedia de Azure utiliza para codificar son H.264 y VC-1. Puede haber disponibles otros códecs en nuestro ecosistema de socios de codificadores. 

### Codificadores de Servicios multimedia

Para obtener información acerca de los codificadores compatibles, consulte [Codificadores](media-services-encode-asset.md).


## Streaming en vivo

### Transcodificador en vivo

Un codificador (o transcodificador) en vivo convierte el audio y/o vídeo que se transmite desde su cámara a un formato de streaming con velocidad de transmisión adaptable o RTMP de velocidad de bits múltiple. Luego, el transcodificador inserta las transmisiones adaptables o RTMP de velocidad de bits adaptable en un canal de Servicios multimedia. Servicios multimedia entonces difunde el evento en vivo.

### Canal

En Servicios multimedia, los [canal](https://msdn.microsoft.com/library/azure/dn783458.aspx)es son responsables de procesar el contenido de streaming en vivo. Un canal proporciona un extremo de entrada (dirección URL de introducción) que luego se brinda a un transcodificador en vivo. El canal recibe flujos de entrada en vivo desde el transcodificador en vivo y las deja a disposición del streaming a través de uno o más StreamingEndpoints. Los canales también proporcionan un extremo de vista previa (dirección URL de vista previa) que se puede utilizar para obtener una vista previa y validar el flujo antes de mayor procesamiento y entrega.

Puede obtener la dirección URL de introducción y la dirección URL de vista previa cuando crea el canal. Para obtener estas direcciones URL, el canal no puede encontrarse en el estado iniciado. Cuando está listo para comenzar a insertar datos desde un transcodificador en vivo al canal, este debe estar iniciado. Una vez que el transcodificar en vivo comienza a introducir datos, puede tener una vista previa de la transmisión.

### Programa 

Un [programa](https://msdn.microsoft.com/library/azure/dn783463.aspx) le permite controlar la publicación y el almacenamiento de segmentos en una secuencia en directo. Los canales administran los programas. La relación entre canales y programas es muy similar a los medios tradicionales, donde un canal tiene un flujo constante de contenido y un programa se enfoca algún evento programado en dicho canal.
Puede especificar la cantidad de horas que desea conservar el contenido grabado del programa en la configuración de la propiedad **ArchiveWindowLength**. Este valor se puede establecer desde un mínimo de cinco minutos a un máximo de 25 horas. 

ArchiveWindowLength también indica el tiempo máximo que los clientes pueden buscar hacia atrás a partir de la posición en vivo actual. Los programas pueden transmitirse durante la cantidad de tiempo especificada, pero el contenido que escape de esa longitud de ventana se descartará continuamente. El valor de esta propiedad también determina durante cuánto tiempo los manifiestos de cliente pueden crecer.

Cada programa está asociado a un recurso. Para publicar el programa, debe crear un localizador para el recurso asociado. Contar con este localizador le permitirá crear una dirección URL de streaming que puede proporcionar a sus clientes.
Un canal es compatible con hasta tres programas en ejecución simultánea, por lo que puede crear varios archivos del mismo flujo entrante. Esto le permite publicar y archivar distintas partes de un evento, según sea necesario. Por ejemplo, el requisito de su empresa es solo archivar seis horas de un programa, pero difundir solo los últimos diez minutos. Para lograrlo, necesita crear dos programas en ejecución simultánea. Un programa está definido para archivar seis horas del evento, pero no está publicado. El otro programa está definido para archivar durante diez minutos y este programa sí se publica.

## Protección del contenido

### Cifrado dinámico

Servicios multimedia de Microsoft Azure le permite entregar su contenido cifrado de manera dinámica con el estándar de cifrado avanzado (AES) (con claves de cifrado de 128 bits) y DRM de PlayReady. 

Actualmente, puede cifrar los siguientes formatos de streaming: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) con su recurso y, además, configurar directivas de autorización para la clave.

También necesita configurar la directiva de entrega del recurso. Si desea transmitir un recurso cifrado de almacenamiento, asegúrese de configurar la directiva de entrega del recurso para especificar cómo desea entregarlo.  

Cuando un reproductor solicita una secuencia, Servicios multimedia usa la clave especificada para cifrar de forma dinámica el contenido mediante AES o el cifrado de PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

### Servicios de entrega de licencias de DRM de PlayReady o claves sin cifrado de AES

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady y claves sin cifrado de AES a clientes autorizados. Puede utilizar el Portal de administración de Azure, API de REST o SDK de Servicios multimedia para .NET para configurar las directivas de autorización y autenticación para sus licencias y claves.

Observe que si usa el portal puede configura una directiva AES (que se aplicará a todo el contenido cifrado por AES) y una directiva de PlayReady (que se aplicará a todo el contenido cifrado por PlayReady). Use el SDK de Servicios multimedia para .NET si desea tener más control sobre las configuraciones.

### Plantilla de licencia de PlayReady

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady. Cuando el reproductor del usuario final (por ejemplo, Silverlight) intenta reproduce el contenido protegido de PlayReady, se envía una solicitud al servicio de entrega de licencias para obtener una licencia. Si el servicio de licencia aprueba la solicitud, emite la licencia, la que se envía al cliente y se puede usar para descifrar y reproducir el contenido especificado.

Las licencias contienen los derechos y restricciones que desea para el tiempo de ejecución de DRM de PlayReady con la finalidad de hacer respetar los requisitos cuando un usuario intenta reproducir contenido protegido. Servicios multimedia proporciona API que le permiten configurar sus licencias de PlayReady. Para obtener más información, consulte [Introducción a la plantilla de licencia de PlayReady para Servicios multimedia](https://msdn.microsoft.com/library/azure/dn783459.aspx).

### Restricción de token

La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP. La directiva de restricción de token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Servicios multimedia devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).
Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

## Entrega

### <a id="dynamic_packaging"></a>Empaquetado dinámico

Al trabajar con Servicios multimedia, se recomienda codificar los archivos intermedios en una conjunto MP4 de velocidad de bits adaptable y luego convertir el conjunto en el formato deseado con el [Empaquetado dinámico](https://msdn.microsoft.com/library/azure/jj889436.aspx). 


### Extremo de streaming

Un StreamingEndpoint representa un servicio de streaming que puede entregar contenido directamente a una aplicación de reproductor de cliente o a una red de entrega de contenido (CDN) para su posterior distribución (Servicios multimedia de Azure ahora proporciona la integración de CDN de Azure). La secuencia de salida de un servicio de StreamingEndpoint puede ser una secuencia en vivo o un recurso de vídeo bajo demanda en su cuenta de Servicios multimedia. Además, puede controlar la capacidad del servicio StreamingEndpoint para manejar las crecientes necesidades de ancho de banda mediante el ajuste de las unidades de escalado (también conocidas como unidades de streaming). Se recomienda asignar una o más unidades de escalado para las aplicaciones en el entorno de producción. Las unidades de escalado proporcionan capacidad de salida dedicada que puede adquirirse en incrementos de 200 Mbps y funcionalidad adicional que actualmente incluye el uso de empaquetado dinámico. 

Si piensa utilizar el empaquetado dinámico y/o el cifrado dinámico, debe tener, al menos, una unidad de streaming para el extremo desde el cual piensa transmitir. Para obtener más información, consulte [Escalado de unidades de streaming](media-services-manage-origins#scale_streaming_endpoints.md).

### Directiva de entrega de recursos

Uno de los pasos del flujo de trabajo de entrega de contenido de Servicios multimedia consiste en configurar [directivas de entrega para los recursos ](https://msdn.microsoft.com/library/azure/dn799055.aspx)que desea transmitir. La directiva de entrega de recursos indica a los Servicios multimedia cómo desea usted que se entregue el recurso: en qué protocolo de streaming se debe empaquetar de forma dinámica el recurso (por ejemplo, MPEG DASH, HLS, Smooth Streaming o todos) o si desea o no cifrar de forma dinámica el recurso y de qué manera (cifrado de sobre o común).

Si tiene un recurso cifrado de almacenamiento, antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada. Por ejemplo, para entregar el recurso cifrado con clave de cifrado del estándar de cifrado avanzado (AES), defina el tipo de directiva en DynamicEnvelopeEncryption. Para quitar el cifrado de almacenamiento y transmitir el recurso sin cifrar, defina el tipo de directiva en NoDynamicEncryption. 

### Descarga progresiva 

La descarga progresiva le permite comenzar a reproducir archivos multimedia antes de haber descargado todo el archivo. Solo puede descargar progresivamente un archivo MP4. 

Tenga en cuenta que debe descifrar los recursos cifrados si desea que estén disponibles para la descarga progresiva. 

Para proporcionar direcciones URL de descarga progresiva a los usuarios, primero debe crear un localizador OnDemandOrigin. Crear el localizador le brinda la ruta de acceso de base al recurso. Luego debe anexar el nombre del archivo MP4. Por ejemplo:

	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4

### Direcciones URL de streaming

Transmisión del contenido a los clientes. Para proporcionar direcciones URL de streaming a los usuarios, primero debe crear un localizador OnDemandOrigin. Crear el localizador le brinda la ruta de acceso de base al recurso que contiene el contenido que desea transmitir. Sin embargo, para poder transmitir este contenido, es necesario modificar aún más esta ruta de acceso. Para construir una dirección URL completa al archivo de manifiesto del streaming, debe concatenar el valor de la ruta de acceso del localizador y el nombre de archivo del manifiesto (filename.ism). Luego, anexe /Manifiesto y un formato adecuado (si corresponde) a la ruta de acceso del localizador. 

También puede transmitir el contenido por una conexión SSL. Para ello, asegúrese de que las URL de streaming comienzan por HTTPS.

Tenga en cuenta que solo puede transmitir por SSL si se creó el extremo de streaming desde el que se entrega el contenido a partir del 10 de septiembre de 2014. Si las direcciones URL de streaming se basan en los extremos de streaming creados después del 10 de septiembre, la dirección URL contendrá "streaming.mediaservices.windows.net" (el formato nuevo). Las direcciones URL de streaming que contengan "origin.mediaservices.windows.net" (el formato anterior) no son compatibles con SSL. Si la dirección URL tiene un formato antiguo y desea poder transmitir a través de SSL, cree un extremo de streaming nuevo. Utilice direcciones URL creadas en función del nuevo extremo de streaming para transmitir el contenido a través de SSL. 

En la siguiente lista se describen distintos formatos de streaming y aparecen ejemplos:

- Smooth Streaming

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{filename}.ism/Manifest
		
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest


- MPEG DASH

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=mpd-time-csf)
 
		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf)



- Apple HTTP Live Streaming (HLS) V4

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=m3u8-aapl)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl)



- Apple HTTP Live Streaming (HLS) V3

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=m3u8-aapl-v3)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3)

- HDS (solo para licenciatarios de Adobe PrimeTime/Access)

	{nombre de extremo de streaming-nombre de cuenta de servicios multimedia}.streaming.mediaservices.windows.net/{Id. de localizador}/{nombre de archivo}.ism/Manifest(formato=f4m-f4f)

		http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f) 


<!--HONumber=47-->
