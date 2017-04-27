---
title: Expresiones de las directivas de Azure API Management | Microsoft Docs
description: "Obtenga información acerca de las expresiones de las directivas de Azure API Management."
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: d96dde8c74141d000c1f7c5fd9ef624ff5e46953
ms.lasthandoff: 04/07/2017

---
# <a name="api-management-policy-expressions"></a>Expresiones de las directivas de API Management
La sintaxis de las expresiones de las directivas es C# 6.0. Cada expresión tiene acceso a la variable de [contexto](api-management-policy-expressions.md#ContextVariables) proporcionada de forma implícita y a un [subconjunto](api-management-policy-expressions.md#CLRTypes) permitido de tipos de .NET Framework.  
  
> [!NOTE]
>  Para obtener más información sobre las expresiones de directiva, consulte el vídeo [Policy Expressions](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/) (Expresiones de directivas).  
>   
>  Para ver una demostración de la configuración de directivas que usan expresiones, consulte [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Episodio 177 de Cloud Cover: más características API Management con Vlad Vinogradsky). Este vídeo contiene las siguientes demostraciones de expresiones de directivas.  
>   
>  -   En el minuto 10:30 conocerá cómo se aplica la directiva en el nivel de API para proporcionar información de contexto al servicio back-end utilizando las directivas [de establecimiento del parámetro de cadena de consulta](api-management-transformation-policies.md#SetQueryStringParameter) y de [establecimiento del encabezado HTTP](api-management-transformation-policies.md#SetHTTPheader). En el minuto 12:10 se realiza una demostración de cómo llamar a una operación en el portal para desarrolladores donde podrá ver estas directivas en funcionamiento.  
> -   En el minuto 13:50 se muestra cómo utilizar la directiva de [validación de JWT](api-management-access-restriction-policies.md#ValidateJWT) para preautorizar el acceso a operaciones según notificaciones de token. Avance rápidamente hasta 15:00 para ver las directivas configuradas en el editor de directivas y hasta 18:50 para ver una demostración de llamada de una operación desde el portal para desarrolladores tanto con y sin el token de autorización necesario.  
> -   En el minuto 21:00 verá cómo se utiliza un seguimiento de [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) para ver cómo se evalúan las directivas y los resultados de las evaluaciones.  
> -   En el minuto 25:25 se muestra cómo usar expresiones de directivas con las directivas de [obtención de caché](api-management-caching-policies.md#GetFromCache) y [lmacenamiento en caché](api-management-caching-policies.md#StoreToCache) para configurar la duración del almacenamiento en memoria caché de la respuesta de API Management para que coincida con el almacenamiento en caché de respuesta del servicio back-end especificado por la directiva `Cache-Control` de dicho servicio.  
> -   En el minuto 34:30 se muestra cómo filtrar contenido quitando elementos de datos de la respuesta recibida desde el servicio back-end mediante las directivas de [flujo de control](api-management-advanced-policies.md#choose) y [establecimiento de cuerpo](api-management-transformation-policies.md#SetBody). Empiece en el minuto 31:50 para ver una introducción a la [API de previsión de Dark Sky](https://developer.forecast.io/) empleada en esta demostración.  
> -   Para descargar las declaraciones de directiva que se usan en este vídeo, acceda a [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) en el repositorio de GitHub.  
  
  
##  <a name="Syntax"></a> Sintaxis  
 Las expresiones de declaración única se incluyen en `@(expression)`, donde `expression` es una instrucción de expresión bien formada de C#.  
  
 Las expresiones de múltiples declaraciones se incluyen en `@{expression}`. Todas las rutas de código de las expresiones de múltiples declaraciones deben terminar con una declaración `return`.  
  
##  <a name="PolicyExpressionsExamples"></a> Ejemplos  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge") ? int.Parse((string)context.Variables["maxAge"]) : 3600)  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Uso  
 Las expresiones pueden utilizarse como valores de atributos o valores de texto en cualquiera de las [directivas](api-management-policies.md) de API Management, a menos que la referencia de la directiva especifique lo contrario.  
  
> [!IMPORTANT]
>  Tenga en cuenta que, al utilizar expresiones de directiva, solo hay una comprobación limitada de estas cuando se define la directiva. Dado que la puerta de enlace ejecuta las expresiones durante el tiempo de ejecución en la canalización de entrada o de salida, cualquier excepción de tiempo de ejecución que genere las expresiones de directiva producirá un error de tiempo de ejecución en la llamada API.  
  
##  <a name="CLRTypes"></a>Tipos de .NET framework que se permiten en expresiones de directiva  
 En la tabla siguiente se enumeran los tipos de .NET Framework que se permiten en las expresiones de directiva y sus miembros.  
  
|Tipos CLR|Métodos admitidos|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JArray|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JConstructor|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JContainer|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JObject|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JProperty|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JRaw|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JToken|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JTokenType|Se admiten todos los métodos.|  
|Newtonsoft.Json.Linq.JValue|Se admiten todos los métodos.|  
|System.Collections.Generic.IReadOnlyCollection<T\>|Todo|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|Todo|  
|System.Collections.Generic.ISet<TKey, TValue>|Todo|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key, Value.|  
|System.Collections.Generic.List<TKey, TValue>|Todo|  
|System.Collections.Generic.Queue<TKey, TValue>|Todo|  
|System.Collections.Generic.Stack<TKey, TValue>|Todo|  
|System.Convert|Todo|  
|System.DateTime|Todo|  
|System.DateTimeKind|UTC|  
|System.DateTimeOffset|Todo|  
|System.Decimal|Todo|  
|System.Double|Todo|  
|System.Guid|Todo|  
|System.IEnumerable<T\>|Todo|  
|System.IEnumerator<T\>|Todo|  
|System.Int16|Todo|  
|System.Int32|Todo|  
|System.Int64|Todo|  
|System.Linq.Enumerable<T\>|Se admiten todos los métodos.|  
|System.Math|Todo|  
|System.MidpointRounding|Todo|  
|System.Nullable<T\>|Todo|  
|System.Random|Todo|  
|System.SByte|Todo|  
|System.Security.Cryptography. HMACSHA384|Todo|  
|System.Security.Cryptography. HMACSHA512|Todo|  
|System.Security.Cryptography.HashAlgorithm|Todo|  
|System.Security.Cryptography.HMAC|Todo|  
|System.Security.Cryptography.HMACMD5|Todo|  
|System.Security.Cryptography.HMACSHA1|Todo|  
|System.Security.Cryptography.HMACSHA256|Todo|  
|System.Security.Cryptography.KeyedHashAlgorithm|Todo|  
|System.Security.Cryptography.MD5|Todo|  
|System.Security.Cryptography.RNGCryptoServiceProvider|Todo|  
|System.Security.Cryptography.SHA1|Todo|  
|System.Security.Cryptography.SHA1Managed|Todo|  
|System.Security.Cryptography.SHA256|Todo|  
|System.Security.Cryptography.SHA256Managed|Todo|  
|System.Security.Cryptography.SHA384|Todo|  
|System.Security.Cryptography.SHA384Managed|Todo|  
|System.Security.Cryptography.SHA512|Todo|  
|System.Security.Cryptography.SHA512Managed|Todo|  
|System.Single|Todo|  
|System.String|Todo|  
|System.StringSplitOptions|Todo|  
|System.Text.Encoding|Todo|  
|System.Text.RegularExpressions.Capture|Index, Length, Value.|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item.|  
|System.Text.RegularExpressions.Group|Captures, Success.|  
|System.Text.RegularExpressions.GroupCollection|Count, Item.|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result.|  
|System.Text.RegularExpressions.Regex|.ctor, IsMatch, Match, Matches, Replace.|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline.|  
|System.TimeSpan|Todo|  
|System.Tuple|Todo|  
|System.UInt16|Todo|  
|System.UInt32|Todo|  
|System.UInt64|Todo|  
|System.Uri|Todo|  
|System.Xml.Linq.Extensions|Se admiten todos los métodos.|  
|System.Xml.Linq.XAttribute|Se admiten todos los métodos.|  
|System.Xml.Linq.XCData|Se admiten todos los métodos.|  
|System.Xml.Linq.XComment|Se admiten todos los métodos.|  
|System.Xml.Linq.XContainer|Se admiten todos los métodos.|  
|System.Xml.Linq.XDeclaration|Se admiten todos los métodos.|  
|System.Xml.Linq.XDocument|Se admiten todos los métodos.|  
|System.Xml.Linq.XDocumentType|Se admiten todos los métodos.|  
|System.Xml.Linq.XElement|Se admiten todos los métodos.|  
|System.Xml.Linq.XName|Se admiten todos los métodos.|  
|System.Xml.Linq.XNamespace|Se admiten todos los métodos.|  
|System.Xml.Linq.XNode|Se admiten todos los métodos.|  
|System.Xml.Linq.XNodeDocumentOrderComparer|Se admiten todos los métodos.|  
|System.Xml.Linq.XNodeEqualityComparer|Se admiten todos los métodos.|  
|System.Xml.Linq.XObject|Se admiten todos los métodos.|  
|System.Xml.Linq.XProcessingInstruction|Se admiten todos los métodos.|  
|System.Xml.Linq.XText|Se admiten todos los métodos.|  
|System.Xml.XmlNodeType|Todo|  
  
##  <a name="ContextVariables"></a> Variable de contexto  
 Una variable denominada `context` está disponible implícitamente en todas las [expresiones](api-management-policy-expressions.md#Syntax) de directiva. Sus miembros proporcionan información relativa a `\request`. Todos los miembros de `context` son de solo lectura.  
  
|Variable de contexto|Métodos, propiedades y valores de parámetro admitidos|  
|----------------------|-------------------------------------------------------|  
|contexto|Api: IApi<br /><br /> Implementación<br /><br /> LastError<br /><br /> Operación<br /><br /> Producto<br /><br /> Solicitud<br /><br /> RequestId: cadena<br /><br /> Response<br /><br /> La suscripción<br /><br /> Tracing: bool<br /><br /> Usuario<br /><br /> Variables:IReadOnlyDictionary<cadena, objeto><br /><br /> void Trace(message: cadena)|  
|context.Api|Id: cadena<br /><br /> Name: cadena<br /><br /> Path: cadena<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: cadena<br /><br /> ServiceName: cadena|  
|context.LastError|Source: cadena<br /><br /> Reason: cadena<br /><br /> Message: cadena<br /><br /> Scope: cadena<br /><br /> Section: cadena<br /><br /> Path: cadena<br /><br /> PolicyId: cadena<br /><br /> Para obtener más información sobre context.LastError, consulte [Error handling in API Management policies](api-management-error-handling-policies.md) (Control de errores en directivas de API Management).|  
|context.Operation|Id: cadena<br /><br /> Method: cadena<br /><br /> Name: cadena<br /><br /> UrlTemplate: cadena|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: cadena<br /><br /> Name: cadena<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<cadena, cadena[]><br /><br /> IpAddress: cadena<br /><br /> MatchedParameters: IReadOnlyDictionary<cadena, cadena><br /><br /> Method: cadena<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: cadena, defaultValue: cadena)|headerName: cadena<br /><br /> defaultValue: cadena<br /><br /> Devuelve valores de encabezado de solicitud separados por comas o `defaultValue`, si no se encuentra el encabezado.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<cadena, cadena[]><br /><br /> StatusCode: int<br /><br /> StatusReason: cadena|  
|string context.Response.Headers.GetValueOrDefault(headerName: cadena, defaultValue: cadena)|headerName: cadena<br /><br /> defaultValue: cadena<br /><br /> Devuelve valores de encabezado de respuesta separados por comas o `defaultValue`, si no se encuentra el encabezado.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: cadena<br /><br /> Key: cadena<br /><br /> Name: cadena<br /><br /> PrimaryKey: cadena<br /><br /> SecondaryKey: cadena<br /><br /> StartDate: DateTime?|  
|context.User|Email: cadena<br /><br /> FirstName: cadena<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: cadena<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: cadena<br /><br /> Note: cadena<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: cadena<br /><br /> Name: cadena<br /><br /> Path: cadena<br /><br /> Protocols: IEnumerable<cadena\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: cadena<br /><br /> Name: cadena|  
|IMessageBody|As<T\>(preserveContent: bool = false): donde T: cadena, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> Los métodos `context.Request.Body.As<T>` y `context.Response.Body.As<T>` se usan para leer los cuerpos de un mensaje de respuesta y solicitud en un tipo `T` especificado. De forma predeterminada, el método usa la secuencia de cuerpo del mensaje original y lo presenta como no disponible tras la devolución. Para evitar este resultado haciendo que el método procese una copia de la secuencia del cuerpo, establezca el parámetro `preserveContent` en `true`. Vaya [aquí](api-management-transformation-policies.md#SetBody) para ver un ejemplo.|  
|IUrl|Host: cadena<br /><br /> Path: cadena<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<cadena, cadena[]><br /><br /> QueryString: cadena<br /><br /> Scheme: cadena|  
|IUserIdentity|Id: cadena<br /><br /> Provider: cadena|  
|ISubscriptionKeyParameterNames|Header: cadena<br /><br /> Query: cadena|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: cadena, defaultValue: cadena)|queryParameterName: cadena<br /><br /> defaultValue: cadena<br /><br /> Devuelve valores de parámetro de consulta separados por comas o `defaultValue`, si no se encuentra el parámetro.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: cadena, defaultValue: T)|variableName: cadena<br /><br /> defaultValue: T<br /><br /> Devuelve el valor de la variable convertido al tipo `T` o `defaultValue` si no se encuentra la variable.<br /><br /> Este método produce una excepción si el tipo especificado no coincide con el tipo real de la variable devuelta.|  
|BasicAuthCredentials AsBasic(input: esta cadena)|input: cadena<br /><br /> Si el parámetro de entrada contiene un valor válido de encabezado de solicitud de autorización de autenticación básica HTTP, el método devuelve un objeto de tipo `BasicAuthCredentials`; en caso contrario, el método devuelve nulo.|  
|bool TryParseBasic(input: esta cadena, result: BasicAuthCredentials de salida)|input: cadena<br /><br /> resultado: out BasicAuthCredentials<br /><br /> Si el parámetro de entrada contiene un valor válido de encabezado de solicitud de autorización de autenticación básica HTTP, el método devuelve `true` y el parámetro de resultado contiene un valor del tipo `BasicAuthCredentials`; en caso contrario, el método devuelve `false`.|  
|BasicAuthCredentials|Password: cadena<br /><br /> UserId: cadena|  
|Jwt AsJwt(input: esta cadena)|input: cadena<br /><br /> Si el parámetro de entrada contiene un valor de token JWT válido, el método devuelve un objeto de tipo `Jwt`; en caso contrario, el método devuelve `null`.|  
|bool TryParseJwt(input: esta cadena, result: Jwt de salida)|input: cadena<br /><br /> resultado: JWT de salida<br /><br /> Si el parámetro de entrada contiene un valor de token JWT válido, el método devuelve `true` y el parámetro de resultado contiene un valor de tipo `Jwt`; en caso contrario, el método devuelve `false`.|  
|Jwt|Algorithm: cadena<br /><br /> Audience: IEnumerable<cadena\><br /><br /> Claims: IReadOnlyDictionary<cadena, cadena[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: cadena<br /><br /> Issuer: cadena<br /><br /> NotBefore: DateTime?<br /><br /> Subject: cadena<br /><br /> Type: cadena|  
|cadena Jwt.Claims.GetValueOrDefault(claimName: cadena, defaultValue: cadena)|claimName: cadena<br /><br /> defaultValue: cadena<br /><br /> Devuelve valores de notificación separados por comas o `defaultValue`, si no se encuentra el encabezado.|

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo trabajar con directivas, consulte a [Directivas de API Management](api-management-howto-policies.md).  

