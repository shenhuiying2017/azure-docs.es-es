<properties 
	pageTitle="Información general de protección del contenido" 
	description="En este artículo se ofrece información general de protección de contenido con Media Services." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/04/2015" 
	ms.author="juliako"/>

#Información general de protección del contenido

Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y PlayReady DRM. Los Servicios multimedia también proporcionan un servicio para entregar claves y licencias de PlayReady a los clientes autorizados. 

![content-protection][content-protection]

Para poder usar el cifrado dinámico, primero debe obtener al menos una unidad reservada de streaming en el extremo de streaming desde la que desea transmitir el contenido cifrado.

##Conceptos

###Cifrado dinámico

Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado de forma dinámica con Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits) y PlayReady DRM. 

Actualmente, puede cifrar los siguientes formatos de streaming: HLS, MPEG DASH y Smooth Streaming. No puede cifrar el formato de streaming HDS ni descargas progresivas.

Si desea que Servicios multimedia cifren un recurso, debe asociar una clave de cifrado (CommonEncryption o EnvelopeEncryption) con su recurso y, además, configurar directivas de autorización para la clave.

También necesita configurar la directiva de entrega del recurso. Si desea transmitir un recurso cifrado de almacenamiento, asegúrese de configurar la directiva de entrega del recurso para especificar cómo desea entregarlo.  

Cuando un reproductor solicita una secuencia, los Servicios multimedia usan la clave especificada para cifrar de forma dinámica el contenido mediante AES o el cifrado de PlayReady. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

###Servicios de entrega de licencias de DRM de PlayReady o claves sin cifrado de AES

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady y claves sin cifrado de AES a clientes autorizados. Puede utilizar el Portal de administración de Azure, API de REST o SDK de Servicios multimedia para .NET para configurar las directivas de autorización y autenticación para sus licencias y claves.

Observe que si usa el portal puede configura una directiva AES (que se aplicará a todo el contenido cifrado por AES) y una directiva de PlayReady (que se aplicará a todo el contenido cifrado por PlayReady). Use el SDK de Servicios multimedia para .NET si desea tener más control sobre las configuraciones.

###Plantilla de licencia de PlayReady

Servicios multimedia proporciona un servicio para entregar licencias de PlayReady. Cuando el reproductor del usuario final (por ejemplo, Silverlight) intenta reproduce el contenido protegido de PlayReady, se envía una solicitud al servicio de entrega de licencias para obtener una licencia. Si el servicio de licencia aprueba la solicitud, emite la licencia, la que se envía al cliente y se puede usar para descifrar y reproducir el contenido especificado.

Las licencias contienen los derechos y restricciones que desea para el tiempo de ejecución de DRM de PlayReady con la finalidad de hacer respetar los requisitos cuando un usuario intenta reproducir contenido protegido. Servicios multimedia proporciona API que le permiten configurar sus licencias de PlayReady. Para obtener más información, consulte [Introducción a la plantilla de licencia de PlayReady para Servicios multimedia](https://msdn.microsoft.com/library/azure/dn783459.aspx).

###Restricción de token

La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Servicios multimedia admite tokens en formato Token de web simple (SWT) y en formato Token de web JSON (JWT). Los Servicios multimedia no proporcionan Servicios de tokens seguros. Puede crear un STS personalizado o aprovechar el Servicio de control de acceso (ACS) de Microsoft Azure para emitir tokens. Se debe configurar el STS para crear un token firmado con las notificaciones de clave y emisión que especificó en la configuración de restricción de tokens. El servicio de entrega de claves de Servicios multimedia devolverá la clave solicitada (o licencia) al cliente si el token es válido y las reclamaciones del token coinciden con las configuradas para la clave (o licencia).
Al configurar la directiva de restricción de token, debe especificar los parámetros de clave de comprobación principal, emisor y público. La clave de comprobación principal contiene la clave con la que se firmó el token y el emisor es el servicio de tokens seguros que emite el token. El público (a veces denominado ámbito) describe la intención del token o del recurso cuyo acceso está autorizado por el token. El servicio de entrega de claves de los Servicios multimedia valida que estos valores del token coincidan con los valores de la plantilla.

##Escenarios comunes

###Protección del contenido en el almacenamiento, entrega de soportes de streaming cifrados dinámicamente  

1. Introduzca un archivo intermedio de alta calidad en un recurso. Aplique una opción de cifrado de almacenamiento al recurso.
2. Configure de extremos de streaming.
1. Codifique en conjunto de archivos MP4 de velocidad de bits adaptable. Aplique una opción de cifrado de almacenamiento al recurso de salida.
1. Cree la clave de cifrado de contenido para el recurso que desee que se cifre dinámicamente durante la reproducción.
2. Configure la directiva de autorización de claves de contenido.
1. Configure la directiva de entrega de recursos (usada por el empaquetado y el cifrado dinámicos).
1. Publique el recurso mediante la creación de un localizador a petición.
1. Trasmita el contenido publicado.

###Uso del servicio de entrega de clave y licencia de Servicios multimedia con su propio cifrado y servicios de streaming

Para obtener más información, consulte [Integración del servicio de licencia de PlayReady de Azure con su propio servidor de streaming/sistema de cifrado](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).

##Tareas comunes de cifrado de contenido

>[AZURE.NOTE]Si el contenido tiene el almacenamiento cifrado, asegúrese de configurar la directiva de entrega de recursos.

###Configuración de extremos de streaming

Para obtener información general acerca de los extremos de streaming e información acerca de cómo administrarlos, vea [Administración de extremos de streaming en una cuenta de Servicios multimedia](media-services-manage-origins.md).

###Creación de una clave de contenido

Cree una clave de contenido con la que desee cifrar el recurso con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-create-contentkey](../includes/media-services-selector-create-contentkey.md)]

###Configuración de la directiva de autorización de claves de contenido 

Configure la protección de contenido y las directivas de autorización de claves mediante **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

###Configuración de la directiva de entrega de recursos

Configure la directiva de entrega de recursos con **.NET** o **API de REST**.

[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]


##Vínculos relacionados

[Anuncio de PlayReady como servicio y cifrado dinámico AES con Servicios multimedia de Azure](http://mingfeiy.com/playready)

[Explicación del precio de la entrega de licencia de PlayReady de Servicios multimedia de Azure](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Depuración del flujo cifrado de AES en Servicios multimedia de Azure](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Autenticación de token JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Integración de la aplicación OWIN basada en MVC de Servicios multimedia de Azure con Azure Active Directory y restricción de la entrega de claves de contenido basada en notificaciones de JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/).

[Uso de ACS de Azure para emitir tokens](http://mingfeiy.com/acs-with-key-services).

[content-protection]: ./media/media-services-content-protection/media-services-content-protection.png


<!--HONumber=52-->