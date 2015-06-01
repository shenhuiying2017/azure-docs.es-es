<properties 
	pageTitle="Protección del contenido" 
	description="El tema proporciona información general sobre cómo proteger el contenido con Servicios multimedia." 
	authors="Juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/15/2015" 
	ms.author="juliako"/>


#Protección del contenido

##Información general

Servicios multimedia de Azure le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Cuando se trabaja con Servicios multimedia, uno de los escenarios más comunes es:

###Protección del contenido en el almacenamiento, entrega medios de streaming cifrados dinámicamente  

Para poder usar el cifrado dinámico, primero debe obtener al menos una unidad reservada de streaming en el extremo de streaming desde la que desea transmitir el contenido cifrado.

1. Cargue un archivo intermedio de alta calidad en un recurso. Aplique una opción de cifrado de almacenamiento al recurso.
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable. Aplique una opción de cifrado de almacenamiento al recurso de salida.
1. Cree la clave de cifrado de contenido para el recurso que desee que se cifre dinámicamente durante la reproducción.
2. Configure la directiva de autorización de claves de contenido.
1. Configure la directiva de entrega de recursos (usada por el empaquetado y el cifrado dinámicos).
1. Publique el recurso mediante la creación de un localizador a petición.
1. Trasmita el contenido publicado.  

Este tema proporciona información general de los [conceptos](media-services-protecting-content-overview.md#concepts) principales y vínculos a temas que muestran cómo realizar la entrega de contenido relacionado con [tareas](media-services-protecting-content-overview.md#tasks).

##<a id="concepts"></a>Conceptos

###Opciones de cifrado de recursos

Según el tipo de contenido que desea cargar, almacenar y entregar, Servicios multimedia proporciona varias opciones de cifrado de las que puede elegir.

**Ninguno**: no se utiliza cifrado. Este es el valor predeterminado. Tenga en cuenta que al utilizar esta opción el contenido no está protegido en tránsito o en reposo en el almacenamiento.

Si planifica entregar un MP4 mediante una descarga progresiva, use esta opción para cargar el contenido.

**StorageEncrypted**: use esta opción para cifrar el contenido no cifrado de manera local mediante el cifrado AES de 256 bits y, a continuación, cargarlo al almacenamiento de Azure donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.

Para entregar a un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso para que Servicios multimedia sepa cómo desea entregar el contenido. Antes de poder transmitir el recurso, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada (por ejemplo, AES, PlayReady o sin cifrado).

**CommonEncryptionProtected**: utilice esta opción si desea cifrar contenido o cargar contenido ya cifrado con cifrado común o DRM de PlayReady (por ejemplo, Smooth Streaming protegido con DRM de PlayReady).

**EnvelopeEncryptedProtected**: utilice esta opción si desea proteger HTTP Live Streaming (HLS) cifrado (o cargar uno ya protegido) con estándar de cifrado avanzado (AES). Tenga en cuenta que si carga HLS ya cifrado con AES, se debe haber cifrado con Transform Manager.

###Cifrado dinámico

Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y PlayReady DRM.

Actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) con su recurso y, además, configurar directivas de autorización para la clave.

También necesita configurar la directiva de entrega del recurso. Si desea transmitir un recurso cifrado de almacenamiento, asegúrese de configurar la directiva de entrega del recurso para especificar cómo desea entregarlo.

Cuando un reproductor solicita una secuencia, los Servicios multimedia usan la clave especificada para cifrar de forma dinámica el contenido mediante AES o el cifrado de PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

>[AZURE.NOTE]Para aprovechar al máximo el cifrado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido cifrado. Para obtener más información, consulte [Escalación de Servicios multimedia](media-services-manage-origins.md#scale_streaming_endpoints).

###Servicios de entrega de licencias de DRM de PlayReady o claves sin cifrado de AES

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady y claves sin cifrado de AES a clientes autorizados. Puede utilizar el Portal de administración de Azure, API de REST o SDK de Servicios multimedia para .NET para configurar las directivas de autorización y autenticación para sus licencias y claves.

Observe que si usa el portal puede configura una directiva AES (que se aplicará a todo el contenido cifrado por AES) y una directiva de PlayReady (que se aplicará a todo el contenido cifrado por PlayReady). Use el SDK de Servicios multimedia para .NET si desea tener más control sobre las configuraciones.

###Plantilla de licencia de PlayReady

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady. Cuando el reproductor del usuario final (por ejemplo, Silverlight) intenta reproduce el contenido protegido de PlayReady, se envía una solicitud al servicio de entrega de licencias para obtener una licencia. Si el servicio de licencia aprueba la solicitud, emite la licencia, la que se envía al cliente y se puede usar para descifrar y reproducir el contenido especificado.

Las licencias contienen los derechos y restricciones que desea para el tiempo de ejecución de DRM de PlayReady con la finalidad de hacer respetar los requisitos cuando un usuario intenta reproducir contenido protegido. Servicios multimedia proporciona API que le permiten configurar sus licencias de PlayReady. Para obtener más información, consulte [Introducción a la plantilla de licencia de PlayReady para Servicios multimedia](https://msdn.microsoft.com/library/azure/dn783459.aspx).

###Restricción de token

La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Servicios multimedia devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).

Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

##<a id="tasks"></a>Tareas relacionadas

###Configuración de extremos de streaming

Para obtener información general acerca de los extremos de streaming e información acerca de cómo administrarlos, consulte [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md).

###Carga de elementos multimedia 

Cargue los archivos mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-upload-files](../includes/media-services-selector-upload-files.md)]

###Codificación de recursos

Codifique con el **Codificador de Servicios multimedia** mediante el **Portal de administración de Azure**, **.NET** o **API de REST**.
 
[AZURE.INCLUDE [media-services-selector-encode](../includes/media-services-selector-encode.md)]

###Creación de una clave de contenido

Cree una clave de contenido con la que desee cifrar el recurso con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Configuración de la directiva de autorización de claves de contenido 

Configure la protección de contenido y las directivas de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

###Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


<!--HONumber=52-->
