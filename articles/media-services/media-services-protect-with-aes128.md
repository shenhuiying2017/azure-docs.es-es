---
title: "Uso del cifrado dinámico AES-128 y del servicio de entrega de claves | Microsoft Docs"
description: "Servicios multimedia de Microsoft Azure le permite entregar el contenido cifrado con claves de cifrado AES de 128 bits. Servicios multimedia también proporciona el servicio de entrega de claves que distribuye claves de cifrado a los usuarios autorizados. En este tema se muestra cómo cifrar dinámicamente con AES-128 y usar el servicio de entrega de claves."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 4d2c10af-9ee0-408f-899b-33fa4c1d89b9
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/25/2017
ms.author: juliako
ms.openlocfilehash: a441e76fae0bda829cb112d307b3b436809b9c9b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="using-aes-128-dynamic-encryption-and-key-delivery-service"></a>Uso del cifrado dinámico AES-128 y del servicio de entrega de claves
> [!div class="op_single_selector"]
> * [.NET](media-services-protect-with-aes128.md)
> * [Java](https://github.com/southworkscom/azure-sdk-for-media-services-java-samples)
> * [PHP](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices)
> 
> 

## <a name="overview"></a>Información general
> [!NOTE]
> Vea [este](https://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-Protecting-your-Media-Content-with-AES-Encryption) vídeo para obtener información general sobre cómo proteger su contenido multimedia con el cifrado AES.
> 
> 

Servicios multimedia de Microsoft Azure le permite entregar secuencias Http-Live-Streaming (HLS) y Smooth Streaming cifradas con el Estándar de cifrado avanzado (AES) (mediante claves de cifrado de 128 bits). Servicios multimedia también proporciona el servicio de entrega de claves que distribuye claves de cifrado a los usuarios autorizados. Si desea que Servicios multimedia cifre un recurso, debe asociar una clave de cifrado con el recurso y, además, configurar directivas de autorización para la clave. Cuando un reproductor solicita una secuencia, Servicios multimedia usa la clave especificada para cifrar de forma dinámica el contenido mediante AES. Para descifrar la secuencia, el reproductor solicitará la clave del servicio de entrega de claves. Para decidir si el usuario está o no autorizado para obtener la clave, el servicio evalúa las directivas de autorización que especificó para la clave.

Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. La directiva de autorización de claves de acceso podría tener una o más restricciones de autorización: abrir o restricción de token. La directiva con restricción token debe ir acompañada de un token emitido por un Servicio de tokens seguros (STS). Media Services admite tokens en formato [Token de web simple (SWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2) y en formato [JSON Web Token (JWT)](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_3). Para obtener más información, consulte [Configuración de la directiva de autorización de la clave de contenido](media-services-protect-with-aes128.md#configure_key_auth_policy).

Para aprovechar las ventajas del cifrado dinámico, debe disponer de un recurso que contenga un conjunto de archivos MP4 o archivos de origen Smooth Streaming, de varias velocidades de bits. También debe configurar la directiva de entrega para el recurso (se describe más adelante en este tema). Luego, según el formato especificado en la URL de streaming, el servidor de streaming a petición se asegurará de que se reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente.

Este tema será útil para los desarrolladores que trabajan en aplicaciones que proporcionan contenido multimedia protegido. Se describe cómo configurar el servicio de entrega de claves con directivas de autorización para que solo los clientes autorizados puedan recibir las claves de cifrado. También se describe cómo usar el cifrado dinámico.


## <a name="aes-128-dynamic-encryption-and-key-delivery-service-workflow"></a>Flujo de trabajo de cifrado dinámico AES-128 y del servicio de entrega de claves

Éstos son los pasos generales que deberá realizar al cifrar los recursos con AES, mediante el servicio de entrega claves de Servicios multimedia y también mediante cifrado dinámico.

1. [Creación de un recurso y carga de los archivos en el recurso](media-services-protect-with-aes128.md#create_asset).
2. [Codificación del recurso que contiene el archivo con Adaptive Bitrate MP4 Set](media-services-protect-with-aes128.md#encode_asset).
3. [Creación de una clave de contenido y su asociación con el recurso codificado](media-services-protect-with-aes128.md#create_contentkey). En Servicios multimedia, la clave de contenido contiene la clave de cifrado del recurso.
4. [Configuración de la directiva de autorización de la clave de contenido](media-services-protect-with-aes128.md#configure_key_auth_policy). El usuario debe configurar la directiva de autorización de claves y el cliente (reproductor) debe conocerla para que se le entregue la clave de contenido.
5. [Configuración de la directiva de entrega para un recurso](media-services-protect-with-aes128.md#configure_asset_delivery_policy). La configuración de la directiva de entrega incluye: la URL de adquisición de claves y vector de inicialización (IV) (AES 128 requiere que se suministre el mismo IV en el cifrado y descifrado), el protocolo de entrega (por ejemplo, MPEG DASH, HLS y Smooth Streaming o todos) y el tipo de cifrado dinámico (por ejemplo Envelope o sin cifrado dinámico).

    Puede aplicar diferentes directivas a cada protocolo en el mismo recurso. Por ejemplo, puede aplicar cifrado PlayReady a Smooth/DASH y AES Envelope a HLS. Se bloqueará la transmisión para todos los protocolos que no estén definidos en una directiva de entrega (por ejemplo, si agrega una sola directiva que solo especifica HLS como el protocolo). La excepción a esta regla se produce en el caso de que no haya definido ninguna directiva de entrega de recursos. En tal caso, todos los protocolos estarán habilitados sin cifrar.

6. [Creación de un localizador a petición](media-services-protect-with-aes128.md#create_locator) para obtener una URL de streaming.

El tema también muestra [cómo una aplicación cliente puede solicitar una clave al servicio de entrega de claves](media-services-protect-with-aes128.md#client_request).

Encontrará un [ejemplo](media-services-protect-with-aes128.md#example) completo para .NET al final del tema.

La siguiente imagen muestra el flujo de trabajo que se ha descrito anteriormente. Aquí el token se usa para la autenticación.

![Protección con AES-128](./media/media-services-content-protection-overview/media-services-content-protection-with-aes.png)

El resto de este tema proporciona explicaciones detalladas, ejemplos de código y vínculos a temas que le muestran cómo lograr las tareas descritas arriba.

## <a name="current-limitations"></a>Limitaciones actuales
Si agrega o actualiza la directiva de entrega de recursos, debe eliminar un localizador existente (si hay) y crear uno nuevo.

## <a id="create_asset"></a>Creación de un recurso y carga de los archivos en el recurso
Para administrar, codificar y transmitir por secuencias sus vídeos, primero debe cargar el contenido en Servicios multimedia de Microsoft Azure. Una vez cargado, el contenido se almacena de forma segura en la nube para su posterior procesamiento y transmisión. 

Para obtener información detallada, consulte [Carga de archivos en una cuenta de Servicios multimedia](media-services-dotnet-upload-files.md).

## <a id="encode_asset"></a>Codificación del activo que contiene el archivo con Adaptive Bitrate MP4 Set.
Con el cifrado dinámico, todo lo que tiene que hacer es crear un recurso que contenga un conjunto de archivos MP4 o archivos Smooth Streaming, de varias velocidades de bits. Luego, según el formato especificado en la solicitud de manifiesto o fragmento, el servidor de streaming a petición se asegurará de que reciba la secuencia en el protocolo elegido. Como resultado, solo tendrá que almacenar y pagar los archivos en formato de almacenamiento único y Servicios multimedia creará y proporcionará la respuesta adecuada en función de las solicitudes de un cliente. Para más información, consulte el tema [Información general sobre el empaquetado dinámico](media-services-dynamic-packaging-overview.md) .

>[!NOTE]
>Cuando se crea la cuenta de AMS, se agrega un punto de conexión de streaming **predeterminado** a la cuenta en estado **Stopped** (Detenido). Para iniciar la transmisión del contenido y aprovechar el empaquetado dinámico y el cifrado dinámico, el punto de conexión de streaming desde el que va a transmitir el contenido debe estar en estado **Running** (En ejecución). 
>
>Además, para poder usar el empaquetado dinámico y el cifrado dinámico, el recurso debe contener un conjunto de archivos MP4 de velocidad de bits adaptable o archivos Smooth Streaming de velocidad de bits adaptable.

Para obtener instrucciones sobre cómo codificar, consulte [Codificación de un recurso mediante Codificador multimedia estándar](media-services-dotnet-encode-with-media-encoder-standard.md).

## <a id="create_contentkey"></a>Creación de una clave de contenido y su asociación con el activo codificado
En Servicios multimedia, la clave de contenido contiene la clave con la que desea cifrar un recurso.

Para obtener más información, consulte [Creación de la clave de contenido](media-services-dotnet-create-contentkey.md).

## <a id="configure_key_auth_policy"></a>Configuración de la directiva de autorización de claves de contenido
Servicios multimedia admite varias formas de autenticar a los usuarios que realizan solicitudes de clave. El usuario debe configurar la directiva de autorización de claves y el cliente (reproductor) debe conocerla para que se le entregue la clave. La directiva de autorización de clave de acceso podría tener una o más restricciones de autorización: abrir, restricción de token o restricción de IP.

Para obtener información detallada, consulte [Configuración de la directiva de autorización de claves de contenido](media-services-dotnet-configure-content-key-auth-policy.md).

## <a id="configure_asset_delivery_policy"></a>Configuración de directivas de entrega de recursos
Configure la directiva de entrega de sus recursos. Algunos de los elementos que incluye la configuración de la directiva de entrega de recursos son:

* La URL de adquisición de clave. 
* El vector de inicialización (IV) que se usará para el cifrado Envelope. AES 128 requiere que se suministre el mismo IV en el cifrado y descifrado. 
* El protocolo de entrega de recursos (por ejemplo, MPEG DASH, HLS, Smooth Streaming o todos).
* El tipo de cifrado dinámico (por ejemplo, AES Envelope) o sin cifrado dinámico. 

Para obtener más información, consulte [Configuración de la directiva de entrega de recursos ](media-services-rest-configure-asset-delivery-policy.md).

## <a id="create_locator"></a>Creación de un localizador de streaming a petición para obtener una URL de streaming
Deberá suministrar al usuario la URL de streaming para Smooth, DASH o HLS.

> [!NOTE]
> Si agrega o actualiza la directiva de entrega de recursos, debe eliminar un localizador existente (si hay) y crear uno nuevo.
> 
> 

Para obtener instrucciones sobre cómo publicar un recurso y generar una dirección URL de streaming, vea [Creación de una dirección URL de streaming](media-services-deliver-streaming-content.md).

## <a name="get-a-test-token"></a>Obtención de un token de prueba
Obtenga un token de prueba basado en la restricción de token que se usó para la directiva de autorización de claves.

    // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
    // back into a TokenRestrictionTemplate class instance.
    TokenRestrictionTemplate tokenTemplate = 
        TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);

    // Generate a test token based on the data in the given TokenRestrictionTemplate.
    //The GenerateTestToken method returns the token without the word “Bearer” in front
    //so you have to add it in front of the token string. 
    string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate);
    Console.WriteLine("The authorization token is:\nBearer {0}", testToken);

Puede usar [AMS Player](http://amsplayer.azurewebsites.net/azuremediaplayer.html) para probar la secuencia.

## <a id="client_request"></a>Cómo el cliente puede solicitar una clave al servicio de entrega de claves
En el paso anterior, creó la URL que apunta a un archivo de manifiesto. El cliente debe extraer la información necesaria de los archivos de manifiesto de streaming para realizar una solicitud al servicio de entrega de claves.

### <a name="manifest-files"></a>Archivos de manifiesto
El cliente debe extraer el valor de la URL (que también contiene el identificador de la clave de contenido) del archivo de manifiesto. El cliente intentará entonces obtener la clave de cifrado del servicio de entrega de claves. El cliente también debe extraer el valor de IV y usarlo para descifrar la transmisión. El siguiente fragmento de código muestra el elemento <Protection> del manifiesto de Smooth Streaming.

    <Protection>
      <ProtectionHeader SystemID="B47B251A-2409-4B42-958E-08DBAE7B4EE9">
        <ContentProtection xmlns:sea="urn:mpeg:dash:schema:sea:2012" schemeIdUri="urn:mpeg:dash:sea:2012">
          <sea:SegmentEncryption schemeIdUri="urn:mpeg:dash:sea:aes128-cbc:2013"/>
          <sea:KeySystem keySystemUri="urn:mpeg:dash:sea:keysys:http:2013"/>
          <sea:CryptoPeriod IV="0xD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7" 
                            keyUriTemplate="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
                                            kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d"/>
        </ContentProtection>
      </ProtectionHeader>
    </Protection>

En el caso de HLS, el manifiesto raíz se divide en archivos de segmento. 

Por ejemplo, el manifiesto raíz es http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/manifest(format=m3u8-aapl) y contiene una lista de nombres de archivo de segmento.

    . . . 
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=630133,RESOLUTION=424x240,CODECS="avc1.4d4015,mp4a.40.2",AUDIO="audio"
    QualityLevels(514369)/Manifest(video,format=m3u8-aapl)
    #EXT-X-STREAM-INF:PROGRAM-ID=1,BANDWIDTH=965441,RESOLUTION=636x356,CODECS="avc1.4d401e,mp4a.40.2",AUDIO="audio"
    QualityLevels(842459)/Manifest(video,format=m3u8-aapl)
    …

Si abre uno de los archivos de segmento en el editor de texto (por ejemplo, http://test001.origin.mediaservices.windows.net/8bfe7d6f-34e3-4d1a-b289-3e48a8762490/BigBuckBunny.ism/QualityLevels(514369)/Manifest(video,format=m3u8-aapl), debe incluir #EXT-X-CLAVE, que indica que el archivo está cifrado.

    #EXTM3U
    #EXT-X-VERSION:4
    #EXT-X-ALLOW-CACHE:NO
    #EXT-X-MEDIA-SEQUENCE:0
    #EXT-X-TARGETDURATION:9
    #EXT-X-KEY:METHOD=AES-128,
    URI="https://wamsbayclus001kd-hs.cloudapp.net/HlsHandler.ashx?
         kid=da3813af-55e6-48e7-aa9f-a4d6031f7b4d",
            IV=0XD7D7D7D7D7D7D7D7D7D7D7D7D7D7D7D7
    #EXT-X-PROGRAM-DATE-TIME:1970-01-01T00:00:00.000+00:00
    #EXTINF:8.425708,no-desc
    Fragments(video=0,format=m3u8-aapl)
    #EXT-X-ENDLIST

>[!NOTE] 
>Si va a planear la reproducción de una instancia de HLS cifrada mediante AES en Safari, vea [este blog](https://azure.microsoft.com/blog/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/).

### <a name="request-the-key-from-the-key-delivery-service"></a>Solicitud de la clave al servicio de entrega de claves

El código siguiente muestra cómo enviar una solicitud al servicio de entrega de claves de Media Services mediante un URI de entrega de claves (que se ha extrajo del manifiesto) y un token (en este tema no se habla de cómo obtener tokens web simples de un servicio de token seguro).

    private byte[] GetDeliveryKey(Uri keyDeliveryUri, string token)
    {
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(keyDeliveryUri);

        request.Method = "POST";
        request.ContentType = "text/xml";
        if (!string.IsNullOrEmpty(token))
        {
            request.Headers[AuthorizationHeader] = token;
        }
        request.ContentLength = 0;

        var response = request.GetResponse();

        var stream = response.GetResponseStream();
        if (stream == null)
        {
            throw new NullReferenceException("Response stream is null");
        }

        var buffer = new byte[256];
        var length = 0;
        while (stream.CanRead && length <= buffer.Length)
        {
            var nexByte = stream.ReadByte();
            if (nexByte == -1)
            {
                break;
            }
            buffer[length] = (byte)nexByte;
            length++;
        }
        response.Close();

        // AES keys must be exactly 16 bytes (128 bits).
        var key = new byte[length];
        Array.Copy(buffer, key, length);
        return key;
    }

## <a name="protect-your-content-with-aes-128-using-net"></a>Protección del contenido con AES-128 mediante .NET

### <a name="create-and-configure-a-visual-studio-project"></a>Creación y configuración de un proyecto de Visual Studio

1. Configure el entorno de desarrollo y rellene el archivo app.config con la información de la conexión, como se describe en [Desarrollo de Media Services con .NET](media-services-dotnet-how-to-use.md). 
2. Agregue los siguientes elementos al elemento **appSettings** definido en el archivo app.config:

        <add key="Issuer" value="http://testacs.com"/>
        <add key="Audience" value="urn:test"/>

### <a id="example"></a>Ejemplo

Sobrescriba el código del archivo Program.cs con el código mostrado en esta sección.
 
>[!NOTE]
>Hay un límite de 1 000 000 directivas para diferentes directivas de AMS (por ejemplo, para la directiva de localizador o ContentKeyAuthorizationPolicy). Debe usar el mismo identificador de directiva si siempre usa los mismos permisos de acceso y días, por ejemplo, directivas para localizadores que vayan a aplicarse durante mucho tiempo (directivas distintas a carga). Para obtener más información, consulte [este tema](media-services-dotnet-manage-entities.md#limit-access-policies) .

Asegúrese de actualizar las variables para que apunten a las carpetas donde se encuentran los archivos de entrada.

[!code-csharp[Main](../../samples-mediaservices-encryptionaes/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs)]

## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

