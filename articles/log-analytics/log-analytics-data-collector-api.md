---
title: Log Analytics HTTP Data Collector API | Microsoft Docs
description: "Puede usar Log Analytics HTTP Data Collector API para agregar datos POST JSON en el repositorio de Log Analytics desde cualquier cliente que pueda llamar a la API de REST. Este artículo describe cómo utilizar la API y contiene algunos ejemplos de cómo publicar datos mediante diferentes lenguajes de programación."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: a831fd90-3f55-423b-8b20-ccbaaac2ca75
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2018
ms.author: bwren
ms.openlocfilehash: 88d9c4b23eb676743c004c0d1b3ab45f6cd66055
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="send-data-to-log-analytics-with-the-http-data-collector-api-public-preview"></a>Envío de datos a Log Analytics con la API del recopilador de datos HTTP (versión preliminar pública)
Este artículo muestra cómo utilizar la API del recopilador de datos HTTP para enviar datos a Log Analytics desde un cliente de la API de REST.  Describe cómo dar formato a los datos recopilados por el script o la aplicación, incluirlos en una solicitud y hacer que esa solicitud se autorice por Log Analytics.  Se proporcionan ejemplos de PowerShell, C# y Python.

> [!NOTE]
> La API del recopilador de datos HTTP de Log Analytics se encuentra en versión preliminar pública.

## <a name="concepts"></a>Conceptos
Puede usar la API del recopilador de datos HTTP para enviar datos a Log Analytics desde cualquier cliente que pueda llamar a una API de REST.  Podría tratarse de un runbook en Azure Automation que recopila datos de administración de Azure u otra nube, o podría ser un sistema de administración alternativo que usa Log Analytics para consolidar y analizar los datos.

Todos los datos del repositorio de Log Analytics se almacenan como un registro con un tipo de registro concreto.  Se da formato a los datos para enviarlos a la API del recopilador de datos HTTP como varios registros en JSON.  Al enviar los datos, se crea un registro individual en el repositorio para cada registro en la carga de solicitud.


![Información general del recopilador de datos HTTP](media/log-analytics-data-collector-api/overview.png)



## <a name="create-a-request"></a>Creación de una solicitud
Para usar la API de recopilador de datos de HTTP, cree una solicitud POST que incluya los datos que se van a enviar en notación de objetos JavaScript (JSON).  Las siguientes tres tablas enumeran los atributos que son necesarios para cada solicitud. Se describirá cada atributo con más detalle más adelante en el artículo.

### <a name="request-uri"></a>URI de solicitud
| Atributo | Propiedad |
|:--- |:--- |
| Método |POST |
| URI |https://\<CustomerId\>.ods.opinsights.azure.com/api/logs?api-version=2016-04-01 |
| Tipo de contenido |application/json |

### <a name="request-uri-parameters"></a>Parámetros de URI de solicitud
| . | DESCRIPCIÓN |
|:--- |:--- |
| CustomerID |Identificador único del área de trabajo de Microsoft Operations Management Suite. |
| Recurso |Nombre de recurso de la API: /api/logs. |
| Versión de API |Versión de la API que se usará con esta solicitud. Actualmente, es 2016-04-01. |

### <a name="request-headers"></a>Encabezados de solicitud
| Encabezado | DESCRIPCIÓN |
|:--- |:--- |
| Autorización |Firma de la autorización. Más adelante en este artículo, encontrará información acerca de cómo crear un encabezado HMAC-SHA256. |
| Log-Type |Especifica el tipo de registro de los datos que se envían. Actualmente, el tipo de registro solo admite caracteres alfabéticos. No admite valores numéricos ni caracteres especiales. |
| x-ms-date |Fecha en que se procesó la solicitud, en formato RFC 1123. |
| time-generated-field |Nombre de un campo en los datos que contiene la marca de tiempo del elemento de datos. Si especifica un campo, su contenido se usa para **TimeGenerated**. Si no se especifica este campo, el valor predeterminado de **TimeGenerated** es el tiempo que el mensaje se ingiere. El contenido del campo de mensaje debe seguir el formato ISO 8601 AAAA-MM-DDThh:mm:ssZ. |

## <a name="authorization"></a>Autorización
Cualquier solicitud a Log Analytics HTTP Data Collector API debe incluir un encabezado de autorización. Para autenticar una solicitud, debe firmar la solicitud con la clave principal o secundaria del área de trabajo que realiza la solicitud. Después, pase esa firma como parte de la solicitud.   

Este es el formato del encabezado de autorización:

```
Authorization: SharedKey <WorkspaceID>:<Signature>
```

*WorkspaceID* es el identificador único del área de trabajo de Microsoft Operations Management Suite. *Signature* es un [código de autenticación de mensajes basado en hash (HMAC)](https://msdn.microsoft.com/library/system.security.cryptography.hmacsha256.aspx) que se construye a partir de la solicitud y después se procesa mediante el [algoritmo SHA256](https://msdn.microsoft.com/library/system.security.cryptography.sha256.aspx). Luego se codifica con la codificación Base64.

Use este formato para codificar la cadena de firma **SharedKey**:

```
StringToSign = VERB + "\n" +
                  Content-Length + "\n" +
               Content-Type + "\n" +
                  x-ms-date + "\n" +
                  "/api/logs";
```

Este es un ejemplo de una cadena de firma:

```
POST\n1024\napplication/json\nx-ms-date:Mon, 04 Apr 2016 08:00:00 GMT\n/api/logs
```

Cuando tenga la cadena de firma, codifíquela usando un algoritmo HMAC-SHA256 en la cadena codificada mediante UTF-8 y, después, codifique el resultado como Base64. Use este formato:

```
Signature=Base64(HMAC-SHA256(UTF8(StringToSign)))
```

Los ejemplos de las secciones siguientes tienen código de ejemplo que le ayudarán a crear un encabezado de autorización.

## <a name="request-body"></a>Cuerpo de la solicitud
El cuerpo del mensaje debe estar en formato JSON. Debe incluir uno o varios registros con los pares de nombre y valor de propiedad en este formato:

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

Puede procesar por lotes varios registros en una sola solicitud con el formato siguiente. Todos los registros deben ser del mismo tipo de registro.

```
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
},
{
"property1": "value1",
" property 2": "value2"
" property 3": "value3",
" property 4": "value4"
}
```

## <a name="record-type-and-properties"></a>Propiedades y tipo de registro
Cuando se envían datos a través de Log Analytics HTTP Data Collector API se define un tipo de registro personalizado. Actualmente, no se pueden escribir datos en tipos de registros existentes creados por otros tipos de datos y soluciones. Log Analytics lee los datos entrantes y después crea las propiedades que coinciden con los tipos de datos de los valores que especifique.

Cada solicitud a Log Analytics API debe incluir un encabezado **Log-Type** con el nombre del tipo de registro. El sufijo **_CL** se anexa automáticamente al nombre que especifique para distinguirlo de otros tipos de registros como un registro personalizado. Por ejemplo, si escribe el nombre **MyNewRecordType**, Log Analytics crea un registro con el tipo de **MyNewRecordType_CL**. Esto ayuda a garantizar que no haya conflictos entre los nombres de tipo creados por el usuario y los incluidos en las soluciones de Microsoft actuales o futuras.

Para identificar el tipo de datos de una propiedad, Log Analytics agrega un sufijo al nombre de la propiedad. Si una propiedad contiene un valor null, la propiedad no se incluye en ese registro. Esta tabla enumera el tipo de datos de la propiedad y el sufijo correspondiente:

| Tipo de datos de la propiedad | Sufijo |
|:--- |:--- |
| string |_s |
| boolean |_b |
| Doble |_d |
| Fecha/hora |_t |
| GUID |_g |

El tipo de datos que utiliza Log Analytics para cada propiedad depende de si ya existe el tipo de registro para el nuevo registro.

* Si el tipo de registro no existe, Log Analytics crea uno nuevo. Log Analytics utiliza la inferencia de tipos de JSON para determinar el tipo de datos de cada propiedad para el nuevo registro.
* Si el tipo de registro existe, Log Analytics intenta crear un nuevo registro en función de las propiedades existentes. Si el tipo de datos de una propiedad en el nuevo registro no coincide y no se puede convertir al tipo existente, o si el registro incluye una propiedad que no existe, Log Analytics crea una nueva propiedad que tiene el sufijo pertinente.

Por ejemplo, esta entrada de envío crearía un registro con tres propiedades **number_d**, **boolean_b** y **string_s**:

![Registro de ejemplo 1](media/log-analytics-data-collector-api/record-01.png)

Si después se envía la entrada siguiente, con todos los valores con formato de cadena, las propiedades no cambiarían. Estos valores se pueden convertir a los tipos de datos existentes:

![Registro de ejemplo 2](media/log-analytics-data-collector-api/record-02.png)

Pero, si después se realiza el siguiente envío, Log Analytics crearía las nuevas propiedades **boolean_d** y **string_d**. Estos valores no se pueden convertir:

![Registro de ejemplo 3](media/log-analytics-data-collector-api/record-03.png)

Si después se envía la entrada siguiente, antes de que se cree el tipo de registro, Log Analytics crearía un registro con tres propiedades **number_s**, **boolean_s** y **string_s**. En esta entrada, se da un formato de cadena a cada uno de los valores iniciales:

![Registro de ejemplo 4](media/log-analytics-data-collector-api/record-04.png)

## <a name="data-limits"></a>Límites de datos
Existen algunas limitaciones en cuando a los datos enviados a la API de recopilación de datos de Log Analytics.

* Máximo de 30 MB por publicación en la API de recopilación de datos de Log Analytics. Se trata de un límite de tamaño para una sola publicación. Si los datos de una única publicación superan los 30 MB, debe dividir los datos en fragmentos más pequeños y enviarlos al mismo tiempo.
* Límite de 32 kB para los valores de campo. Si el valor del campo es mayor que 32 kB, se truncarán los datos.
* El número máximo recomendado de campos para un tipo determinado es 50. Se trata de un límite práctico desde una perspectiva de la experiencia de búsqueda y la facilidad de uso.  

## <a name="return-codes"></a>Códigos de retorno
El código de estado HTTP 200 significa que se ha recibido la solicitud para su procesamiento. Esto indica que el trabajo se ha completado correctamente.

Esta tabla muestra el conjunto completo de códigos de estado que el servicio puede devolver:

| Código | Status | Código de error | DESCRIPCIÓN |
|:--- |:--- |:--- |:--- |
| 200 |OK | |La solicitud se aceptó correctamente. |
| 400 |Solicitud incorrecta |InactiveCustomer |El área de trabajo se cerró. |
| 400 |Solicitud incorrecta |InvalidApiVersion |El servicio no reconoció la versión de API especificada. |
| 400 |Solicitud incorrecta |InvalidCustomerId |El identificador de área de trabajo especificado no es válido. |
| 400 |Solicitud incorrecta |InvalidDataFormat |No envió un formato JSON no válido. El cuerpo de la respuesta puede contener más información acerca de cómo resolver el error. |
| 400 |Solicitud incorrecta |InvalidLogType |El tipo de registro especificado contenía caracteres especiales o valores numéricos. |
| 400 |Solicitud incorrecta |MissingApiVersion |No se especificó la versión de API. |
| 400 |Solicitud incorrecta |MissingContentType |No se especificó el tipo de contenido. |
| 400 |Solicitud incorrecta |MissingLogType |No se especificó el tipo de registro de valor requerido. |
| 400 |Solicitud incorrecta |UnsupportedContentType |No se estableció el tipo de contenido en **application/json**. |
| 403 |Prohibido |InvalidAuthorization |El servicio no pudo autenticar la solicitud. Compruebe que el identificador del área de trabajo y la clave de conexión sean válidas. |
| 404 |No encontrado | | La dirección URL proporcionada no es correcta, bien o la solicitud es demasiado grande. |
| 429 |Demasiadas solicitudes | | El servicio está experimentando un gran volumen de datos de su cuenta. Vuelva a intentar realizar la solicitud más tarde. |
| 500 |Internal Server Error |UnspecifiedError |El servicio detectó un error interno. Vuelva a intentar realizar la solicitud. |
| 503 |Servicio no disponible |ServiceUnavailable |El servicio actualmente no está disponible para recibir solicitudes. Vuelva a intentar realizar la solicitud. |

## <a name="query-data"></a>Datos de consulta
Para consultar los datos enviados por Log Analytics HTTP Data Collector API, busque los registros cuyo **Type** sea igual al valor de **LogType** que especificó, con el sufijo **_CL**. Por ejemplo, si utilizó **MyCustomLog**, se devolverán todos los registros cuyo **Type= MyCustomLog_CL**.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), la consulta anterior cambiaría como sigue.

> `MyCustomLog_CL`

## <a name="sample-requests"></a>Solicitudes de ejemplo
En las secciones siguientes, encontrará ejemplos de cómo enviar datos a Log Analytics HTTP Data Collector API usando diferentes lenguajes de programación.

Para cada ejemplo, siga estos pasos para establecer las variables para el encabezado de autorización:

1. En el portal de Operations Management Suite, seleccione el icono **Configuración** y, después, seleccione la pestaña **Orígenes conectados**.
2. A la derecha de **Id. de área de trabajo**, seleccione el icono de copia y pegue el identificador como valor de la variable **Id. de cliente**.
3. A la derecha de **Clave principal**, seleccione el icono de copia y pegue el identificador como valor de la variable **Clave compartida**.

También puede cambiar las variables del tipo de registro y de los datos JSON.

### <a name="powershell-sample"></a>Ejemplo de PowerShell
```
# Replace with your Workspace ID
$CustomerId = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"  

# Replace with your Primary Key
$SharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# Specify the name of the record type that you'll be creating
$LogType = "MyRecordType"

# Specify a field with the created time for the records
$TimeStampField = "DateValue"


# Create two records with the same set of properties to create
$json = @"
[{  "StringValue": "MyString1",
    "NumberValue": 42,
    "BooleanValue": true,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "9909ED01-A74C-4874-8ABF-D2678E3AE23D"
},
{   "StringValue": "MyString2",
    "NumberValue": 43,
    "BooleanValue": false,
    "DateValue": "2016-05-12T20:00:00.625Z",
    "GUIDValue": "8809ED01-A74C-4874-8ABF-D2678E3AE23D"
}]
"@

# Create the function to create the authorization signature
Function Build-Signature ($customerId, $sharedKey, $date, $contentLength, $method, $contentType, $resource)
{
    $xHeaders = "x-ms-date:" + $date
    $stringToHash = $method + "`n" + $contentLength + "`n" + $contentType + "`n" + $xHeaders + "`n" + $resource

    $bytesToHash = [Text.Encoding]::UTF8.GetBytes($stringToHash)
    $keyBytes = [Convert]::FromBase64String($sharedKey)

    $sha256 = New-Object System.Security.Cryptography.HMACSHA256
    $sha256.Key = $keyBytes
    $calculatedHash = $sha256.ComputeHash($bytesToHash)
    $encodedHash = [Convert]::ToBase64String($calculatedHash)
    $authorization = 'SharedKey {0}:{1}' -f $customerId,$encodedHash
    return $authorization
}


# Create the function to create and post the request
Function Post-LogAnalyticsData($customerId, $sharedKey, $body, $logType)
{
    $method = "POST"
    $contentType = "application/json"
    $resource = "/api/logs"
    $rfc1123date = [DateTime]::UtcNow.ToString("r")
    $contentLength = $body.Length
    $signature = Build-Signature `
        -customerId $customerId `
        -sharedKey $sharedKey `
        -date $rfc1123date `
        -contentLength $contentLength `
        -fileName $fileName `
        -method $method `
        -contentType $contentType `
        -resource $resource
    $uri = "https://" + $customerId + ".ods.opinsights.azure.com" + $resource + "?api-version=2016-04-01"

    $headers = @{
        "Authorization" = $signature;
        "Log-Type" = $logType;
        "x-ms-date" = $rfc1123date;
        "time-generated-field" = $TimeStampField;
    }

    $response = Invoke-WebRequest -Uri $uri -Method $method -ContentType $contentType -Headers $headers -Body $body -UseBasicParsing
    return $response.StatusCode

}

# Submit the data to the API endpoint
Post-LogAnalyticsData -customerId $customerId -sharedKey $sharedKey -body ([System.Text.Encoding]::UTF8.GetBytes($json)) -logType $logType  
```

### <a name="c-sample"></a>Ejemplo de C#
```
using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Security.Cryptography;
using System.Text;
using System.Threading.Tasks;

namespace OIAPIExample
{
    class ApiExample
    {
        // An example JSON object, with key/value pairs
        static string json = @"[{""DemoField1"":""DemoValue1"",""DemoField2"":""DemoValue2""},{""DemoField3"":""DemoValue3"",""DemoField4"":""DemoValue4""}]";

        // Update customerId to your Operations Management Suite workspace ID
        static string customerId = "xxxxxxxx-xxx-xxx-xxx-xxxxxxxxxxxx";

        // For sharedKey, use either the primary or the secondary Connected Sources client authentication key   
        static string sharedKey = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx";

        // LogName is name of the event type that is being submitted to Log Analytics
        static string LogName = "DemoExample";

        // You can use an optional field to specify the timestamp from the data. If the time field is not specified, Log Analytics assumes the time is the message ingestion time
        static string TimeStampField = "";

        static void Main()
        {
            // Create a hash for the API signature
            var datestring = DateTime.UtcNow.ToString("r");
            var jsonBytes = Encoding.UTF8.GetBytes(message);
            string stringToHash = "POST\n" + jsonBytes.Length + "\napplication/json\n" + "x-ms-date:" + datestring + "\n/api/logs";
            string hashedString = BuildSignature(stringToHash, sharedKey);
            string signature = "SharedKey " + customerId + ":" + hashedString;

            PostData(signature, datestring, json);
        }

        // Build the API signature
        public static string BuildSignature(string message, string secret)
        {
            var encoding = new System.Text.ASCIIEncoding();
            byte[] keyByte = Convert.FromBase64String(secret);
            byte[] messageBytes = encoding.GetBytes(message);
            using (var hmacsha256 = new HMACSHA256(keyByte))
            {
                byte[] hash = hmacsha256.ComputeHash(messageBytes);
                return Convert.ToBase64String(hash);
            }
        }

        // Send a request to the POST API endpoint
        public static void PostData(string signature, string date, string json)
        {
            try
            {
                string url = "https://" + customerId + ".ods.opinsights.azure.com/api/logs?api-version=2016-04-01";

                System.Net.Http.HttpClient client = new System.Net.Http.HttpClient();
                client.DefaultRequestHeaders.Add("Accept", "application/json");
                client.DefaultRequestHeaders.Add("Log-Type", LogName);
                client.DefaultRequestHeaders.Add("Authorization", signature);
                client.DefaultRequestHeaders.Add("x-ms-date", date);
                client.DefaultRequestHeaders.Add("time-generated-field", TimeStampField);

                System.Net.Http.HttpContent httpContent = new StringContent(json, Encoding.UTF8);
                httpContent.Headers.ContentType = new MediaTypeHeaderValue("application/json");
                Task<System.Net.Http.HttpResponseMessage> response = client.PostAsync(new Uri(url), httpContent);

                System.Net.Http.HttpContent responseContent = response.Result.Content;
                string result = responseContent.ReadAsStringAsync().Result;
                Console.WriteLine("Return Result: " + result);
            }
            catch (Exception excep)
            {
                Console.WriteLine("API Post Exception: " + excep.Message);
            }
        }
    }
}

```

### <a name="python-sample"></a>Ejemplo de Python
```
import json
import requests
import datetime
import hashlib
import hmac
import base64

# Update the customer ID to your Operations Management Suite workspace ID
customer_id = 'xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'

# For the shared key, use either the primary or the secondary Connected Sources client authentication key   
shared_key = "xxxxxxxxxxxxxxxxxxxxxxxxxxxxxx"

# The log type is the name of the event that is being submitted
log_type = 'WebMonitorTest'

# An example JSON web monitor object
json_data = [{
   "slot_ID": 12345,
    "ID": "5cdad72f-c848-4df0-8aaa-ffe033e75d57",
    "availability_Value": 100,
    "performance_Value": 6.954,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "true"
},
{   
    "slot_ID": 67890,
    "ID": "b6bee458-fb65-492e-996d-61c4d7fbb942",
    "availability_Value": 100,
    "performance_Value": 3.379,
    "measurement_Name": "last_one_hour",
    "duration": 3600,
    "warning_Threshold": 0,
    "critical_Threshold": 0,
    "IsActive": "false"
}]
body = json.dumps(json_data)

#####################
######Functions######  
#####################

# Build the API signature
def build_signature(customer_id, shared_key, date, content_length, method, content_type, resource):
    x_headers = 'x-ms-date:' + date
    string_to_hash = method + "\n" + str(content_length) + "\n" + content_type + "\n" + x_headers + "\n" + resource
    bytes_to_hash = bytes(string_to_hash).encode('utf-8')  
    decoded_key = base64.b64decode(shared_key)
    encoded_hash = base64.b64encode(hmac.new(decoded_key, bytes_to_hash, digestmod=hashlib.sha256).digest())
    authorization = "SharedKey {}:{}".format(customer_id,encoded_hash)
    return authorization

# Build and send a request to the POST API
def post_data(customer_id, shared_key, body, log_type):
    method = 'POST'
    content_type = 'application/json'
    resource = '/api/logs'
    rfc1123date = datetime.datetime.utcnow().strftime('%a, %d %b %Y %H:%M:%S GMT')
    content_length = len(body)
    signature = build_signature(customer_id, shared_key, rfc1123date, content_length, method, content_type, resource)
    uri = 'https://' + customer_id + '.ods.opinsights.azure.com' + resource + '?api-version=2016-04-01'

    headers = {
        'content-type': content_type,
        'Authorization': signature,
        'Log-Type': log_type,
        'x-ms-date': rfc1123date
    }

    response = requests.post(uri,data=body, headers=headers)
    if (response.status_code >= 200 and response.status_code <= 299):
        print 'Accepted'
    else:
        print "Response code: {}".format(response.status_code)

post_data(customer_id, shared_key, body, log_type)
```

## <a name="next-steps"></a>pasos siguientes
- Use la [API de búsqueda de registros](log-analytics-log-search-api.md) para recuperar datos desde el repositorio de Log Analytics.
