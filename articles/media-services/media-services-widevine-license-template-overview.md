---
title: "Información general sobre las plantillas de licencias de Widevine | Microsoft Docs"
description: "Este tema proporciona información general sobre una plantilla de licencia de Widevine que se usó para configurar las licencias de Widevine."
author: juliako
manager: cfowler
editor: 
services: media-services
documentationcenter: 
ms.assetid: 0e6f1f05-7ed6-4ed6-82a0-0cc2182b075a
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: juliako
ms.openlocfilehash: 667ff16dc7608dab2a5b8b1fd7df715da4620ca1
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="widevine-license-template-overview"></a>Información general sobre las plantillas de licencias de Widevine
## <a name="overview"></a>Información general
Servicios multimedia de Azure ahora permite configurar y solicitar licencias de Widevine. Cuando el reproductor del usuario final intenta reproducir el contenido protegido de Widevine, se envía una solicitud al servicio de entrega de licencias para obtener una licencia. Si el servicio de licencia aprueba la solicitud, emite la licencia, la que se envía al cliente y se puede usar para descifrar y reproducir el contenido especificado.

La solicitud de licencia de Widevine recibe el formato de un mensaje JSON.  

>[!NOTE]
> Puede crear un mensaje vacío sin valores: simplemente use "{}" y se creará una plantilla de licencia con todos los valores predeterminados. La configuración predeterminada funciona para la mayoría de los casos. Por ejemplo, en el caso de escenarios de entrega de licencia basados en Microsoft, los valores siempre deberían ser los predeterminados. Si tiene que establecer los valores de "provider" y "content_id", el proveedor debe coincidir con las credenciales de Widevine de Google.

    {  
       “payload”:“<license challenge>”,
       “content_id”: “<content id>” 
       “provider”: ”<provider>”
       “allowed_track_types”:“<types>”,
       “content_key_specs”:[  
          {  
             “track_type”:“<track type 1>”
          },
          {  
             “track_type”:“<track type 2>”
          },
          …
       ],
       “policy_overrides”:{  
          “can_play”:<can play>,
          “can persist”:<can persist>,
          “can_renew”:<can renew>,
          “rental_duration_seconds”:<rental duration>,
          “playback_duration_seconds”:<playback duration>,
          “license_duration_seconds”:<license duration>,
          “renewal_recovery_duration_seconds”:<renewal recovery duration>,
          “renewal_server_url”:”<renewal server url>”,
          “renewal_delay_seconds”:<renewal delay>,
          “renewal_retry_interval_seconds”:<renewal retry interval>,
          “renew_with_usage”:<renew with usage>
       }
    }

## <a name="json-message"></a>Mensaje JSON
| Nombre | Valor | Description |
| --- | --- | --- |
| payload |cadena codificada en Base64 |La solicitud de licencia enviada por un cliente. |
| content_id |cadena codificada en Base64 |Identificador utilizado para derivar KeyId(s) y Content Key(s) para cada content_key_specs.track_type. |
| provider |string |Utilizado para buscar directivas y claves de contenido. En el caso de que se use la entrega de claves de Microsoft para la entrega de licencias de Widevine, este parámetro se omite. |
| policy_name |string |Nombre de una directiva previamente registrada. Opcional |
| allowed_track_types |enum |SD_ONLY o SD_HD. Controla qué claves de contenido deben incluirse en una licencia. |
| content_key_specs |matriz de estructuras JSON, vea **Especificaciones de clave de contenido** a continuación |Un control más preciso sobre qué claves de contenido se devolverán. Vea Especificaciones de clave de contenido para obtener más información.  Solo se puede especificar uno de los valores allowed_track_types y content_key_specs. |
| use_policy_overrides_exclusively |valor booleano. true o false |Utilice atributos de directiva especificados por policy_overrides y omita todas las directivas almacenadas previamente. |
| policy_overrides |Estructura JSON, vea **Invalidaciones de directivas** a continuación |Configuración de directiva para esta licencia.  En caso de que este activo tenga una directiva definida previamente, se usarán estos valores especificados. |
| session_init |Estructura JSON, vea **Inicialización de la sesión** a continuación |Los datos opcionales que se pasan a la licencia. |
| parse_only |valor booleano. true o false |Se analiza la solicitud de licencia, pero no se emite ninguna licencia. Sin embargo, los valores de la solicitud de licencia se devuelven en la respuesta. |

## <a name="content-key-specs"></a>Especificaciones de clave de contenido
Si existe una directiva anterior, no es necesario especificar ninguno de los valores en las especificaciones de clave de contenido.  La directiva anterior asociada a este contenido se utilizará para determinar la protección de salida como HDCP y CGMS.  Si no hay una directiva anterior registrada con el servidor de licencias de Widevine, el proveedor de contenido puede insertar los valores en la solicitud de licencia.   

Cada valor content_key_specs debe especificarse para todas las pistas, independientemente de la opción use_policy_overrides_exclusively. 

| Nombre | Valor | Descripción |
| --- | --- | --- |
| content_key_specs. track_type |string |Un nombre de tipo de pista. Si se especifica content_key_specs en la solicitud de licencia, asegúrese de especificar todos los tipos de pista explícitamente. Si no lo hace, se producirán errores en la reproducción transcurridos 10 segundos. |
| content_key_specs  <br/> security_level |uint32 |Define los requisitos de solidez del cliente para la reproducción. <br/> 1 - Se requiere criptografía white-box basada en software. <br/> 2 - Se requiere criptografía de software y un descodificador de ofuscación. <br/> 3 - Las operaciones de criptografía y material clave deben realizarse en un entorno de ejecución de confianza con respaldo de hardware. <br/> 4 - La criptografía y la descodificación del contenido deben realizarse dentro de un entorno de ejecución de confianza con respaldo de hardware.  <br/> 5 - La criptografía, la descodificación y todo el tratamiento de los medios (comprimidos y descomprimidos) deben administrarse dentro de un entorno de ejecución de confianza con respaldo de hardware. |
| content_key_specs <br/> required_output_protection.hdc |cadena - una de HDCP_NONE, HDCP_V1, HDCP_V2 |Indica si se requiere HDCP. |
| content_key_specs <br/>key |cadena codificada en Base64 <br/>cadena codificada |Clave de contenido que se utilizará para esta pista. Si se especifica, se requiere track_type o key_id.  Esta opción permite que el proveedor de contenido inserte la clave de contenido para esta pista en lugar de permitir que el servidor de licencias de Widevine genere o busque una clave. |
| content_key_specs.key_id |Binario de cadena codificada en Base64, 16 bytes |Identificador único para la clave. |

## <a name="policy-overrides"></a>Invalidaciones de directivas
| Nombre | Valor | Descripción |
| --- | --- | --- |
| policy_overrides. can_play |valor booleano. true o false |Indica que la reproducción del contenido está permitida. El valor predeterminado es false. |
| policy_overrides. can_persist |valor booleano. true o false |Indica que la licencia puede conservarse en el almacenamiento no volátil para uso sin conexión. El valor predeterminado es false. |
| policy_overrides. can_renew |valor booleano. true o false |Indica que se permite la renovación de la presente licencia. Si es true, se puede ampliar la duración de la licencia mediante latido. El valor predeterminado es false. |
| policy_overrides. license_duration_seconds |int64 |Indica el período de tiempo para esta licencia específica. Un valor de 0 indica que no hay ningún límite para la duración. El valor predeterminado es 0. |
| policy_overrides. rental_duration_seconds |int64 |Indica el período de tiempo en el que se permite la reproducción. Un valor de 0 indica que no hay ningún límite para la duración. El valor predeterminado es 0. |
| policy_overrides. playback_duration_seconds |int64 |El período de tiempo de visualización una vez que la reproducción comienza en el plazo de duración de la licencia. Un valor de 0 indica que no hay ningún límite para la duración. El valor predeterminado es 0. |
| policy_overrides. renewal_server_url |cadena |Todas las solicitudes de latido (renovación) de esta licencia se dirigirán a la dirección URL especificada. Este campo solo se utiliza si can_renew es true. |
| policy_overrides. renewal_delay_seconds |int64 |El número de segundos después de license_start_time, antes de intentar la renovación por primera vez. Este campo solo se utiliza si can_renew es true. El valor predeterminado es 0. |
| policy_overrides. renewal_retry_interval_seconds |int64 |Especifica el plazo en segundos entre las posteriores solicitudes de renovación de licencia, en caso de error. Este campo solo se utiliza si can_renew es true. |
| policy_overrides. renewal_recovery_duration_seconds |int64 |El período de tiempo en el que la reproducción puede continuar mientras se intenta la renovación, aunque no se realice correctamente debido a problemas de back-end con el servidor de licencias. Un valor de 0 indica que no hay ningún límite para la duración. Este campo solo se utiliza si can_renew es true. |
| policy_overrides. renew_with_usage |valor booleano. true o false |Indica que la licencia se enviará para renovación cuando se inicie el uso. Este campo solo se utiliza si can_renew es true. |

## <a name="session-initialization"></a>Inicialización de la sesión
| Nombre | Valor | Description |
| --- | --- | --- |
| provider_session_token |cadena codificada en Base64 |Este token de sesión se pasa de nuevo en la licencia y existirá en renovaciones posteriores.  El token de sesión no se conservará una vez agotadas las sesiones. |
| provider_client_token |cadena codificada en Base64 |Token de cliente para devolver en la respuesta de licencia.  Si la solicitud de licencia contiene un token de cliente, este valor se omite. El token del cliente se conservará una vez agotadas las sesiones de licencia. |
| override_provider_client_token |valor booleano. true o false |Si es false y la solicitud de licencia contiene un token de cliente, use el token de la solicitud incluso si se especificó un token de cliente en esta estructura.  Si es true, utilice siempre el token especificado en esta estructura. |

## <a name="configure-your-widevine-licenses-using-net-types"></a>Configuración de las licencias de Widevine utilizando tipos de .NET
Servicios multimedia proporciona las API de .NET que le permiten configurar sus licencias de Widevine. 

### <a name="classes-as-defined-in-the-media-services-net-sdk"></a>Clases tal y como se definen en el SDK de .NET de Servicios multimedia
A continuación se definen estos tipos.

    public class WidevineMessage
    {
        public WidevineMessage();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public AllowedTrackTypes? allowed_track_types { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public ContentKeySpecs[] content_key_specs { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public object policy_overrides { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum AllowedTrackTypes
    {
        SD_ONLY = 0,
        SD_HD = 1
    }
    public class ContentKeySpecs
    {
        public ContentKeySpecs();

        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string key_id { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public RequiredOutputProtection required_output_protection { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public int? security_level { get; set; }
        [JsonProperty(NullValueHandling = NullValueHandling.Ignore)]
        public string track_type { get; set; }
    }

    public class RequiredOutputProtection
    {
        public RequiredOutputProtection();

        public Hdcp hdcp { get; set; }
    }

    [JsonConverter(typeof(StringEnumConverter))]
    public enum Hdcp
    {
        HDCP_NONE = 0,
        HDCP_V1 = 1,
        HDCP_V2 = 2
    }

### <a name="example"></a>Ejemplo
En el ejemplo siguiente se muestra cómo utilizar las API de .NET para configurar una licencia sencilla de Widevine.

    private static string ConfigureWidevineLicenseTemplate()
    {
        var template = new WidevineMessage
        {
            allowed_track_types = AllowedTrackTypes.SD_HD,
            content_key_specs = new[]
            {
                new ContentKeySpecs
                {
                    required_output_protection = new RequiredOutputProtection { hdcp = Hdcp.HDCP_NONE},
                    security_level = 1,
                    track_type = "SD"
                }
            },
            policy_overrides = new
            {
                can_play = true,
                can_persist = true,
                can_renew = false
            }
        };

        string configuration = JsonConvert.SerializeObject(template);
        return configuration;
    }


## <a name="media-services-learning-paths"></a>Rutas de aprendizaje de Servicios multimedia
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Envío de comentarios
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Consulte también
[Uso de cifrado dinámico común de PlayReady o Widevine](media-services-protect-with-drm.md)

