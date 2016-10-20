<properties 
	pageTitle="Información general de protección del contenido | Microsoft Azure" 
	description="En este artículo se ofrece información general de protección de contenido con Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/27/2016" 
	ms.author="juliako"/>

#Información general de protección del contenido


Servicios multimedia de Microsoft Azure le permite proteger su contenido multimedia desde el momento en que deja el equipo a través de almacenamiento, procesamiento y entrega. Media Services permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) o cualquiera de los DRM principales: Microsoft PlayReady, Google Widevine y Apple FairPlay. Media Services también proporciona un servicio para entregar claves AES y licencias de DMR (PlayReady, Widevine y FairPlay) a los clientes autorizados.

La siguiente imagen muestra los flujos de trabajo de protección de contenido que admite AMS.

![Protección con PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Para poder usar el cifrado dinámico, primero debe obtener al menos una unidad reservada de streaming en el extremo de streaming desde la que desea transmitir el contenido cifrado.

En este tema se explican los [conceptos y terminología](media-services-content-protection-overview.md) pertinentes para conocer la protección de contenido con AMS. El tema también contiene [vínculos](media-services-content-protection-overview.md#common-scenarios) a temas que muestran cómo realizar las tareas de protección de contenido.

##Cifrado dinámico

Microsoft Azure Media Services permite proporcionar el contenido cifrado de forma dinámica con la clave sin cifrado AES o cifrado DRM: Microsoft PlayReady, Google Widevine y Apple FairPlay.

Actualmente puede cifrar los formatos de streaming siguientes: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) con su recurso y, además, configurar directivas de autorización para la clave.

También necesita configurar la directiva de entrega del recurso. Si desea transmitir un recurso cifrado de almacenamiento, asegúrese de configurar la directiva de entrega del recurso para especificar cómo desea entregarlo.

Cuando un reproductor solicita una transmisión, Media Services usa la clave especificada para cifrar de forma dinámica el contenido mediante la clave sin cifrado de AES o el cifrado DRM. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

>[AZURE.NOTE]Para aprovechar al máximo el cifrado dinámico, primero debe obtener al menos una unidad de streaming a petición para el extremo de streaming desde el que va a entregar el contenido cifrado. Para obtener más información, consulte [Escalación de un Servicio multimedia](media-services-portal-manage-streaming-endpoints.md).

##Cifrado de almacenamiento

Utilice el cifrado de almacenamiento para cifrar el contenido no cifrado de manera local mediante el cifrado de AES de 256 bits y luego cargarlo a Azure Storage donde se almacena cifrado en reposo. Los recursos protegidos con el cifrado de almacenamiento se descifran automáticamente y se colocan en un sistema de archivos cifrados antes de la codificación y, opcionalmente, se vuelven a cifrar antes de volver a cargarlos como un nuevo recurso de salida. El caso de uso principal para el cifrado de almacenamiento es cuando desea proteger los archivos multimedia de entrada de alta calidad con un sólido cifrado en reposo en disco.

Para entregar a un recurso cifrado de almacenamiento, debe configurar la directiva de entrega del recurso para que Servicios multimedia sepa cómo desea entregar el contenido. Antes de poder transmitir el activo, el servidor de streaming quita el cifrado de almacenamiento y transmite el contenido usando la directiva de entrega especificada (por ejemplo, AES, cifrado común o sin cifrado).

###Detalles de la implementación 

El cifrado de almacenamiento de AMS aplica el cifrado de modo **AES-CTR** a todo el archivo. El modo AES-CTR es un cifrado de bloques que puede cifrar los datos de longitud arbitraria sin necesidad de relleno. Funciona mediante el cifrado de un bloque de contador con el algoritmo de AES y, a continuación, aplicando XOR al resultado de AES con los datos para el cifrado o el descifrado. El bloque de contador utilizado se crea copiando el valor de InitializationVector en bytes de 0 a 7 del valor de contador y los bytes de 8 y 15 del valor de contador se establecen en cero. Del bloque de contador de 16 bytes, los bytes de 8 a 15 (es decir, los bytes menos significativos) se utilizan como un entero sin firmar de 64 bits simple que se incrementa en uno por cada bloque siguiente de datos procesados y se mantiene en el orden de bytes de la red. Tenga en cuenta que si este entero alcanza el valor máximo (0xFFFFFFFFFFFFFFFF), incrementarlo restablece el contador de bloque a cero (bytes de 8 a 15) sin que afecte a los otros 64 bits del contador (es decir, bytes de 0 a 7). Para mantener la seguridad del cifrado del modo AES-CTR, el valor de InitializationVector para un KID determinado debe ser único para cada archivo y los archivos deben tener una longitud inferior a 2^64 bloques. Esto es para asegurarse de que un valor de contador nunca se reutiliza con una clave determinada. Para obtener más información sobre el modo CTR, consulte [esta página de la wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (el artículo de la wiki utiliza el término "Nonce" en lugar de "InitializationVector").

Si desea ver cómo funciona el algoritmo básico, consulte la implementación de .NET de AMS de los métodos siguientes:

- [ApplyEncryptionTransform](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.BlobTransfer/BlobTransferBase.cs)
- [AesCtr](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/FileEncryptionTransform.cs)


## Cifrado común (CENC)

El cifrado común se utiliza al cifrar el contenido con PlayReady o Widewine.

## Uso del cifrado cbcs-aapl

Cbcs-aapl se usa al cifrar el contenido con FairPlay.

## Cifrado de sobre 

Utilice esta opción si desea proteger su contenido con la clave sin cifrado de AES-128. Si desea una opción más segura, elija uno de los DRM enumerados en este tema.

##Servicio de entrega de licencias y claves

Media Services proporciona un servicio para proporcionar licencias DRM (PlayReady, Widevine, FairPlay) y claves sin cifrado de AES a clientes autorizados. Puede utilizar [Azure Portal](media-services-portal-protect-content.md), la API de REST o SDK de Media Services para .NET para configurar las directivas de autorización y autenticación de sus licencias y claves.

##Restricción de token

La directiva de autorización de claves de acceso podría tener una o más restricciones de autorización: abrir o restricción de token. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Servicios multimedia devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).

Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

##Direcciones URL de streaming

Si el recurso se cifró con más de un DRM, debe usar una etiqueta de cifrado en la dirección URL de streaming: (formato = 'm3u8-aapl', cifrado = 'xxx').

Se aplican las siguientes consideraciones:

- Se puede especificar solo un tipo de cifrado o ninguno.
- El tipo de cifrado no tiene que especificarse en la dirección URL si solo se aplicó un cifrado al recurso.
- El tipo de cifrado distingue mayúsculas de minúsculas.
- Se pueden especificar los siguientes tipos de cifrado:
	- **cenc**: cifrado común (Playready o Widevine)
	- **cbcs-aapl**: Fairplay
	- **cbc**: cifrado de sobre AES.

##Escenarios comunes

Los temas siguientes muestran cómo proteger el contenido en el almacenamiento, proporcionar medios de streaming cifrados dinámicamente, uso del servicio de entrega de claves\\licencia de AMS

- [Protección con AES](media-services-protect-with-aes128.md)
- [Protección con PlayReady y/o Widevine](media-services-protect-with-drm.md)
- [Transmisión de contenido HLS protegido con Apple FairPlay o PlayReady](media-services-protect-hls-with-fairplay.md)

### Otros escenarios

- [How to integrate Azure PlayReady License service with your own encryptor/streaming server](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server) (Integración del servicio de licencia de PlayReady de Azure con su propio servidor de streaming/sistema de cifrado).
- [Uso de castLabs para entregar licencias de DRM a Servicios multimedia de Azure](media-services-castlabs-integration.md)
 
##Rutas de aprendizaje de Servicios multimedia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Vínculos relacionados

[Anuncio de PlayReady como servicio y cifrado dinámico AES con Servicios multimedia de Azure](http://mingfeiy.com/playready)

[Explicación del precio de la entrega de licencia de PlayReady de Servicios multimedia de Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Depuración del flujo cifrado de AES en Servicios multimedia de Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integración de la aplicación OWIN basada en MVC de Servicios multimedia de Azure con Azure Active Directory y restricción de la entrega de claves de contenido basada en notificaciones de JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso de ACS de Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png

<!---HONumber=AcomDC_0928_2016-->