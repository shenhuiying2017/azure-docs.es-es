---
title: Directivas avanzadas de Azure API Management | Microsoft Docs
description: Aprenda sobre las directivas avanzadas disponibles para su uso en Azure API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: b8c181282dd28582a8fb02f611424ffd608fd1ec
ms.sourcegitcommit: 176c575aea7602682afd6214880aad0be6167c52
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2018
---
# <a name="api-management-advanced-policies"></a>Directivas avanzadas de API Management
En este tema se proporciona una referencia para las siguientes directivas de API Management. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API](http://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AdvancedPolicies"></a> Directivas avanzadas  
  
-   [Flujo de control](api-management-advanced-policies.md#choose): aplica condicionalmente instrucciones de directiva basadas en los resultados de la evaluación de [expresiones](api-management-policy-expressions.md) booleanas.  
-   [Reenviar solicitud](#ForwardRequest) : reenvía la solicitud al servicio back-end.
-   [Limitar la simultaneidad](#LimitConcurrency): evita que las directivas delimitadas las ejecute simultáneamente un número de solicitudes mayor que el especificado.
-   [Registro en centro de eventos](#log-to-eventhub): envía mensajes en el formato especificado a un centro de eventos definido por una entidad de registrador. 
-   [Mock response](#mock-response) (Simular respuesta): anula la ejecución de la canalización y devuelve la respuesta ficticia directamente al llamador.
-   [Reintentar](#Retry): reintenta ejecutar las instrucciones de directiva adjuntas, si y hasta que se cumple la condición. La ejecución se repite en los intervalos de tiempo especificados y hasta el número de reintentos indicado.  
-   [Devolver respuesta](#ReturnResponse) : anula la ejecución de la canalización y devuelve la respuesta especificada directamente al llamador. 
-   [Enviar solicitud unidireccional](#SendOneWayRequest) : envía una solicitud a la dirección URL especificada sin esperar una respuesta.  
-   [Enviar solicitud](#SendRequest) : envía una solicitud a la dirección URL especificada.  
-   [Establecer el proxy HTTP](#SetHttpProxy): permite enrutar las solicitudes reenviadas a través de un proxy HTTP.  
-   [Establecer método de solicitud](#SetRequestMethod) : le permite cambiar el método HTTP de una solicitud.  
-   [Establecimiento de código de estado](#SetStatus): cambia el código de estado HTTP al valor especificado.  
-   [Establecimiento de variable](api-management-advanced-policies.md#set-variable): conserva un valor en una variable [context](api-management-policy-expressions.md#ContextVariables) con nombre para su posterior acceso.  
-   [Seguimiento](#Trace): agrega una cadena a la salida de [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/).  
-   [Espera](#Wait): espera a que se completen las directivas adjuntas de [envío de solicitud](api-management-advanced-policies.md#SendRequest), [obtención del valor de caché](api-management-caching-policies.md#GetFromCacheByKey) o [flujo de control](api-management-advanced-policies.md#choose) antes de continuar.  
  
##  <a name="choose"></a> Flujo de control  
 La directiva `choose` aplica las declaraciones de directiva adjuntas en función del resultado de la evaluación de las expresiones booleanas, de forma similar a una estructura if-then-else o de conmutador en un lenguaje de programación.  
  
###  <a name="ChoosePolicyStatement"></a> Declaración de la directiva  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 La directiva de flujo de control debe contener al menos un elemento `<when/>`. El elemento `<otherwise/>` es opcional. Las condiciones de los elementos `<when/>` se evalúan en orden de aparición dentro de la directiva. Se aplicarán las declaraciones de directivas adjuntas en el primer elemento `<when/>` cuyo atributo de condición sea igual a `true`. Las directivas incluidas dentro del elemento `<otherwise/>`, si está presente, se aplicarán si todos los atributos de condición del elemento `<when/>` son `false`.  
  
### <a name="examples"></a>Ejemplos  
  
####  <a name="ChooseExample"></a> Ejemplo  
 En el ejemplo siguiente se muestra una directiva [set-variable](api-management-advanced-policies.md#set-variable) y dos directivas de flujo de control.  
  
 La directiva de establecimiento de variable se encuentra en la sección de entrada y crea una variable de [contexto](api-management-policy-expressions.md#ContextVariables) booleana `isMobile` que se establece en true si el encabezado de la solicitud `User-Agent` contiene el texto `iPad` o `iPhone`.  
  
 La primera directiva de flujo de control se encuentra también en la sección de entrada y aplica condicionalmente una de las dos directivas de [establecimiento del parámetro de cadena de consulta](api-management-transformation-policies.md#SetQueryStringParameter) dependiendo del valor de la variable de contexto `isMobile`.  
  
 La segunda directiva de flujo de control se encuentra en la sección de salida y aplica la directiva de [onversión de XML a JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) solo cuando `isMobile` está establecida en `true`.  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### <a name="example"></a>Ejemplo  
 En este ejemplo se muestra cómo filtrar contenido quitando elementos de datos de la respuesta recibida del servicio back-end al usar el producto `Starter`. Para ver una demostración de la configuración y el uso de esta directiva, vea [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Episodio 177 de Cloud Cover: más características API Management con Vlad Vinogradsky) y avance al minuto 34:30. Empiece en el minuto 31:50 para ver una introducción a la [API de previsión de Dark Sky](https://developer.forecast.io/) empleada en esta demostración.  
  
```xml  
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->  
<choose>  
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">  
    <set-body>@{  
        var response = context.Response.Body.As<JObject>();  
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {  
          response.Property (key).Remove ();  
        }  
        return response.ToString();  
      }  
    </set-body>  
  </when>  
</choose>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|choose|Elemento raíz.|Sí|  
|when|La condición que se va a usar para las partes `if` o `ifelse` de la directiva `choose`. Si la directiva `choose` tiene varias secciones `when`, se evalúan de forma secuencial. Una vez que la instancia de `condition` de un elemento when se evalúa en `true`, ya no se evalúan más condiciones `when`.|Sí|  
|otherwise|Contiene el fragmento de código de directiva que se utilizará si ninguna de las condiciones `when` se evalúan como `true`.|Sin |  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|  
|---------------|-----------------|--------------|  
|condition="Constante booleana &#124; Constante booleana"|La expresión o constante booleana que se evalúa cuando se evalúa la declaración de la directiva `when` que la contiene.|Sí|  
  
###  <a name="ChooseUsage"></a> Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="ForwardRequest"></a> Reenvío de solicitud  
 La directiva `forward-request` reenvía la solicitud entrante al servicio back-end especificado en el [contexto](api-management-policy-expressions.md#ContextVariables) de la solicitud. La dirección URL del servicio back-end se especifica en la [configuración](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) de la API y se puede cambiar mediante la directiva de [establecimiento del servicio back-end](api-management-transformation-policies.md).  
  
> [!NOTE]
>  Si quita los resultados de esta directiva en la solicitud no se reenviarán al servicio back-end, y las directivas de la sección de salida se evaluarán inmediatamente tras la finalización correcta de las directivas en la sección de entrada.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### <a name="examples"></a>Ejemplos  
  
#### <a name="example"></a>Ejemplo  
 La siguiente directiva de nivel de API reenvía todas las solicitudes al servicio back-end con un intervalo de tiempo de espera de 60 segundos.  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Ejemplo  
 Esta directiva de nivel de operación utiliza el elemento `base` para heredar la directiva de back-end del ámbito de nivel de API principal.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Ejemplo  
 Esta directiva de nivel de operación reenvía explícitamente todas las solicitudes al servicio back-end con un tiempo de espera de 120 y no hereda la directiva de back-end de nivel de API principal.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### <a name="example"></a>Ejemplo  
 Esta directiva de nivel de operación no reenvía solicitudes al servicio back-end.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|forward-request|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|timeout="entero"|Tiempo de espera en segundos antes de que se produzca un error de la llamada al servicio back-end.|Sin |300 segundos|  
|follow-redirects="true &#124; false"|Especifica si la puerta de enlace sigue los redireccionamientos desde el servicio back-end o si estos se devuelven al autor de la llamada.|Sin |false|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** back-end  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="LimitConcurrency"></a> Limitar la simultaneidad  
 La directiva `limit-concurrency` evita que las directivas delimitadas las ejecute en un momento dado un número de solicitudes mayor que el especificado. Al superar ese número, las nuevas solicitudes producirán un error inmediatamente con el código de estado de 429 Demasiadas solicitudes.
  
###  <a name="LimitConcurrencyStatement"></a> Declaración de la directiva  
  
```xml  
<limit-concurrency key="expression" max-count="number">
        <!— nested policy statements -->  
</limit-concurrency>
``` 

### <a name="examples"></a>Ejemplos  
  
#### <a name="example"></a>Ejemplo  
 En el ejemplo siguiente se muestra cómo limitar el número de solicitudes que se reenvían a un back-end en función del valor de una variable de contexto.
 
```xml  
<policies>
  <inbound>…</inbound>
  <backend>
    <limit-concurrency key="@((string)context.Variables["connectionId"])" max-count="3">
      <forward-request timeout="120"/>
    <limit-concurrency/>
  </backend>
  <outbound>…</outbound>
</policies>
```

### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|    
|límite de simultaneidad|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|--------------|  
|key|Una cadena. Expresión que se permite. Especifica el ámbito de la simultaneidad. Puede compartirse entre varias directivas.|Sí|N/D|  
|número máximo|Un entero. Especifica el número máximo de solicitudes que se pueden especificar en la directiva.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  

##  <a name="log-to-eventhub"></a>Registro en centro de eventos  
 La directiva `log-to-eventhub` envía mensajes en el formato especificado a un centro de eventos definido por una entidad del registrador. Como su nombre indica, la directiva se usa para guardar información de contexto de respuesta o solicitud que se ha seleccionado para su análisis en línea o sin conexión.  
  
> [!NOTE]
>  Para obtener una guía paso a paso acerca de cómo configurar un centro de eventos y eventos de registro, consulte [Cómo registrar eventos de API Management con Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### <a name="example"></a>Ejemplo  
 Puede utilizar cualquier cadena como valor que se registrará en Event Hubs. En este ejemplo, la fecha y hora, el nombre del servicio de implementación, el identificador de solicitud, la dirección IP y el nombre de la operación de todas las llamadas entrantes se registran en el registrador del centro de eventos con el identificador `contoso-logger`.  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|log-to-eventhub|Elemento raíz. El valor de este elemento es la cadena que sirve para iniciar sesión en su centro de eventos.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|  
|---------------|-----------------|--------------|  
|logger-id|El identificador del registrador registrado con el servicio API Management.|Sí|  
|partition-id|Especifica el índice de la partición desde donde se envían los mensajes.|Opcional. Este atributo no se puede utilizar cuando se usa `partition-key`.|  
|partition-key|Especifica el valor utilizado para la asignación de partición cuando se envían mensajes.|Opcional. Este atributo no se puede utilizar cuando se usa `partition-id`.|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  

##  <a name="mock-response"></a> Similar respuesta  
`mock-response`, como el nombre indica, se utiliza para simular las API y las operaciones. Se anula la ejecución de la canalización normal y devuelve una respuesta simulada al llamador. La directiva siempre trata de devolver las respuestas de mayor fidelidad. Prefiere ejemplos de contenido de respuesta, siempre que estén disponibles. Genera las respuestas de ejemplo a partir de esquemas, cuando se proporcionan esquemas y no ejemplos. Si no se encuentran ni ejemplos ni esquemas, se devuelven las respuestas sin contenido.
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<mock-response status-code="code" content-type="media type"/>  
  
```  
  
### <a name="examples"></a>Ejemplos  
  
```xml  
<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code. First found content type is used. If no example or schema is found, the content is empty. -->
<mock-response/>

<!-- Returns 200 OK status code. Content is based on an example or schema, if provided for this 
status code and media type. If no example or schema found, the content is empty. -->
<mock-response status-code='200' content-type='application/json'/>  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|mock-response|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|--------------|  
|status-code|Especifica el código de estado de la respuesta y se utiliza para seleccionar el ejemplo o el esquema correspondientes.|Sin |200|  
|content-type|Especifica el valor de encabezado de la respuesta `Content-Type` y se utiliza para seleccionar el ejemplo o el esquema correspondientes.|Sin |None|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** entrante, saliente y en caso de error  
  
-   **Ámbitos de la directiva:** todos los ámbitos

##  <a name="Retry"></a> Reintento  
 La directiva `retry` ejecuta sus directivas secundarias una vez y después vuelve a tratar de ejecutarla hasta que el elemento `condition` del reintento pasa a ser `false` o se agota el número correspondiente al elemento `count` del reintento.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### <a name="example"></a>Ejemplo  
 En el siguiente ejemplo de solicitud, el reenvío de solicitud se vuelve a intentar hasta diez veces usando un algoritmo exponencial de reintentos. Como `first-fast-retry` se ha establecido en false, todos los reintentos quedan sujetos al algoritmo exponencial de reintentos.  
  
```xml  
  
<retry  
    condition="@(context.Response.StatusCode == 500)"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|retry|Elemento raíz. Puede contener cualquier otra directiva como elemento secundario.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|condition|[Expresión](api-management-policy-expressions.md) o literal booleanos que especifican si los reintentos se deben detener (`false`) o continuar (`true`).|Sí|N/D|  
|count|Número positivo que especifica el número máximo de reintentos que deben realizarse.|Sí|N/D|  
|interval|Número positivo en segundos que especifica el intervalo de espera entre los reintentos.|Sí|N/D|  
|max-interval|Número positivo en segundos que especifica el intervalo máximo de espera entre los reintentos. Se utiliza para implementar un algoritmo exponencial de reintentos.|Sin |N/D|  
|delta|Número positivo en segundos que especifica el incremento del intervalo de espera. Se usa para implementar los algoritmos lineales y exponenciales de reintentos.|Sin |N/D|  
|first-fast-retry|Si establece en `true`, el primer reintento se lleva a cabo inmediatamente.|Sin |`false`|  
  
> [!NOTE]
>  Cuando solamente se especifica `interval`, los reintentos se llevan a cabo a intervalos **fijos**.  
>  Cuando solamente se especifican `interval` y `delta`, se utiliza el algoritmo de reintentos de intervalo **lineal**, en el que el tiempo de espera entre reintentos se calcula según la siguiente fórmula: `interval + (count - 1)*delta`.  
>  Cuando se especifican `interval`, `max-interval` y `delta`, se aplica un algoritmo de reintentos de intervalo **exponencial**, en el que el tiempo de espera entre los reintentos crece exponencialmente desde el valor de `interval` al valor de `max-interval` según la siguiente fórmula: `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva. Tenga en cuenta que esta directiva heredará las restricciones de uso de directivas secundarias.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="ReturnResponse"></a> Devolución de respuesta  
 La directiva `return-response` anula la ejecución de la canalización y devuelve una respuesta personalizada o predeterminada al autor de la llamada. La respuesta predeterminada es `200 OK` sin cuerpo. La respuesta personalizada se puede especificar mediante declaraciones de directiva o variable de contexto. Cuando se especifican ambas, las declaraciones de la directiva modifican la respuesta que se encuentra en la variable de contexto antes de devolverla al autor de la llamada.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### <a name="example"></a>Ejemplo  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
   <set-header name="WWW-Authenticate" exists-action="override">  
      <value>Bearer error="invalid_token"</value>  
   </set-header>  
</return-response>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|return-response|Elemento raíz.|Sí|  
|set-header|Una declaración de directiva [set-header](api-management-transformation-policies.md#SetHTTPheader).|Sin |  
|set-body|Una declaración de directiva [set-body](api-management-transformation-policies.md#SetBody).|Sin |  
|set-status|Una declaración de directiva [set-status](api-management-advanced-policies.md#SetStatus).|Sin |  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|  
|---------------|-----------------|--------------|  
|response-variable-name|Nombre de la variable de contexto a la que se hace referencia, por ejemplo, desde una directiva [send-request](api-management-advanced-policies.md#SendRequest) de canal de subida y que contiene un objeto `Response`.|Opcional.|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="SendOneWayRequest"></a> Envío de solicitud unidireccional  
 La directiva `send-one-way-request` envía la solicitud proporcionada a la dirección URL especificada sin esperar una respuesta.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### <a name="example"></a>Ejemplo  
 Esto es un ejemplo de cómo usar la directiva `send-one-way-request` para enviar un mensaje a un salón de chat de Slack cuando el código de respuesta HTTP es mayor o igual que 500. Si desea más información sobre este ejemplo, consulte [Uso de servicios externos del servicio Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|send-one-way-request|Elemento raíz.|Sí|  
|URL|Dirección URL de la solicitud.|No si mode=copy; de lo contrario, sí.|  
|estático|Método HTTP usado en la solicitud.|No si mode=copy; de lo contrario, sí.|  
|encabezado|Encabezado de la solicitud. Utilice varios elementos de encabezado si hay varios encabezados de solicitud.|Sin |  
|Cuerpo|Cuerpo de la solicitud.|Sin |  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|mode="cadena"|Determina si se trata de una solicitud nueva o de una copia de la solicitud actual. En el modo de salida, mode=copy no inicializa el cuerpo de la solicitud.|Sin |Nuevo|  
|Nombre|Especifica el nombre del encabezado que se va a establecer.|Sí|N/D|  
|exists-action|Especifica la acción que se debe realizar cuando ya se ha especificado un encabezado. Este atributo debe tener uno de los siguientes valores:<br /><br /> -   override: sustituye el valor del encabezado existente.<br />-   skip: no sustituye el valor del encabezado existente.<br />-   append: anexa el valor al encabezado existente.<br />-   delete: quita el encabezado de la solicitud.<br /><br /> Cuando se establece en `override`, si se inscriben varias entradas con el mismo nombre, se establece el encabezado de acuerdo con todas ellas (que se inscribirán varias veces); solo los valores mostrados se establecerán en el resultado.|Sin |override|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="SendRequest"></a> Envío de solicitud  
 La directiva `send-request` envía la solicitud proporcionada a la dirección URL que se ha especificado, aunque no espera más del valor de tiempo de espera establecido.  
  
### <a name="policy-statement"></a>Declaración de la directiva  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### <a name="example"></a>Ejemplo  
 En este ejemplo se muestra una forma de comprobar un token de referencia con un servidor de autorización. Si desea más información sobre este ejemplo, consulte [Uso de servicios externos del servicio Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<inbound>  
  <!-- Extract Token from Authorization header parameter -->  
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />  
  
  <!-- Send request to Token Server to validate token (see RFC 7662) -->  
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">  
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>  
    <set-method>POST</set-method>  
    <set-header name="Authorization" exists-action="override">  
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>  
    </set-header>  
    <set-header name="Content-Type" exists-action="override">  
      <value>application/x-www-form-urlencoded</value>  
    </set-header>  
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>  
  </send-request>  
  
  <choose>  
        <!-- Check active property in response -->  
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
            <!-- Return 401 Unauthorized with http-problem payload -->  
            <return-response response-variable-name="existing response variable">  
                <set-status code="401" reason="Unauthorized" />  
                <set-header name="WWW-Authenticate" exists-action="override">  
                    <value>Bearer error="invalid_token"</value>  
                </set-header>  
            </return-response>  
        </when>  
    </choose>  
  <base />  
</inbound>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|send-request|Elemento raíz.|Sí|  
|URL|Dirección URL de la solicitud.|No si mode=copy; de lo contrario, sí.|  
|estático|Método HTTP usado en la solicitud.|No si mode=copy; de lo contrario, sí.|  
|encabezado|Encabezado de la solicitud. Utilice varios elementos de encabezado si hay varios encabezados de solicitud.|Sin |  
|Cuerpo|Cuerpo de la solicitud.|Sin |  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|mode="cadena"|Determina si se trata de una solicitud nueva o de una copia de la solicitud actual. En el modo de salida, mode=copy no inicializa el cuerpo de la solicitud.|Sin |Nuevo|  
|response-variable-name="cadena"|Si no está presente, se utiliza `context.Response`.|Sin |N/D|  
|timeout="entero"|Intervalo de tiempo de espera en segundos antes de que se produzca un error de la llamada a la dirección URL.|Sin |60|  
|ignore-error|Si es true y la solicitud tiene como resultado un error:<br /><br /> -   Si se especificó response-variable-name, contendrá un valor nulo.<br />-   Si no se especificó response-variable-name, no se actualizará context.Request.|Sin |false|  
|Nombre|Especifica el nombre del encabezado que se va a establecer.|Sí|N/D|  
|exists-action|Especifica la acción que se debe realizar cuando ya se ha especificado un encabezado. Este atributo debe tener uno de los siguientes valores:<br /><br /> -   override: sustituye el valor del encabezado existente.<br />-   skip: no sustituye el valor del encabezado existente.<br />-   append: anexa el valor al encabezado existente.<br />-   delete: quita el encabezado de la solicitud.<br /><br /> Cuando se establece en `override`, si se inscriben varias entradas con el mismo nombre, se establece el encabezado de acuerdo con todas ellas (que se inscribirán varias veces); solo los valores mostrados se establecerán en el resultado.|Sin |override|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="SetHttpProxy"></a> Establecer proxy HTTP  
 La directiva `proxy` le permite enrutar las solicitudes reenviadas a los back-ends a través de un proxy HTTP. Solo se admite HTTP (no HTTPS) entre la puerta de enlace y el proxy. Solo autenticación básica y NTLM.
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<proxy url="http://hostname-or-ip:port" username="username" password="password" />  
  
```  
  
### <a name="example"></a>Ejemplo  
Observe el uso de [propiedades](api-management-howto-properties.md) como valores de nombre de usuario y contraseña para evitar almacenar información confidencial en el documento de directiva.  
  
```xml  
<proxy url="http://192.168.1.1:8080" username={{username}} password={{password}} />
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|proxy|Elemento raíz|Sí|  

### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|url="string"|Dirección URL del proxy en forma de http://host:port.|Sí|N/D|  
|username="string"|Nombre de usuario que se usará para la autenticación con el servidor proxy.|Sin |N/D|  
|password="string"|Contraseña que se usará para la autenticación con el servidor proxy.|Sin |N/D|  

### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound (entrada)  
  
-   **Ámbitos de la directiva:** todos los ámbitos  

##  <a name="SetRequestMethod"></a> Establecimiento de método de solicitud  
 La directiva `set-method` le permite cambiar el método de solicitud de HTTP para una solicitud.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### <a name="example"></a>Ejemplo  
 En este ejemplo, donde se usa la directiva `set-method`, se ilustra cómo enviar un mensaje a un salón de chat de Slack cuando el código de respuesta HTTP es mayor o igual que 500. Si desea más información sobre este ejemplo, consulte [Uso de servicios externos del servicio Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|set-method|Elemento raíz. El valor del elemento especifica el método HTTP.|Sí|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** entrante y en caso de error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="SetStatus"></a> Establecimiento de código de estado  
 La directiva `set-status` establece el código de estado HTTP en el valor especificado.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### <a name="example"></a>Ejemplo  
 En este ejemplo se muestra cómo devolver una respuesta 401 si el token de autorización no es válido. Si desea más información, consulte [Uso de servicios externos del servicio Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
    <return-response response-variable-name="existing response variable">  
      <set-status code="401" reason="Unauthorized" />  
      <set-header name="WWW-Authenticate" exists-action="override">  
        <value>Bearer error="invalid_token"</value>  
      </set-header>  
    </return-response>  
  </when>  
</choose>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|set-status|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|code="entero"|Código de estado HTTP que se devuelve.|Sí|N/D|  
|reason="cadena"|Una descripción del motivo por el que se devuelve el código de estado.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** saliente, back-end y en caso de error  
-   **Ámbitos de la directiva:** todos los ámbitos  

##  <a name="set-variable"></a> Establecimiento de variable  
 La directiva `set-variable` declara una variable de [contexto](api-management-policy-expressions.md#ContextVariables) y le asigna un valor que se especifica mediante una [expresión](api-management-policy-expressions.md) o un literal de cadena. Si la expresión contiene un valor literal, se convertirá en una cadena y el tipo del valor será `System.String`.  
  
###  <a name="set-variablePolicyStatement"></a> Declaración de la directiva  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a> Ejemplo  
 En el ejemplo siguiente se muestra una directiva de establecimiento de variable en la sección de entrada. Esta directiva de establecimiento de variable crea una variable de [contexto](api-management-policy-expressions.md#ContextVariables) booleana de `isMobile` que se establece en true si el encabezado de la solicitud `User-Agent` contiene el texto `iPad` o `iPhone`.  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|set-variable|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|  
|---------------|-----------------|--------------|  
|Nombre|El nombre de la variable.|Sí|  
|value|El valor de la variable. Puede ser una expresión o un valor literal.|Sí|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
###  <a name="set-variableAllowedTypes"></a> Tipos permitidos  
 Las expresiones usadas en la directiva `set-variable` deben devolver uno de los siguientes tipos básicos.  
  
-   System.Boolean  
-   System.SByte  
-   System.Byte  
-   System.UInt16  
-   System.UInt32  
-   System.UInt64  
-   System.Int16  
-   System.Int32  
-   System.Int64  
-   System.Decimal  
-   System.Single  
-   System.Double  
-   System.Guid  
-   System.String  
-   System.Char  
-   System.DateTime  
-   System.TimeSpan  
-   System.Byte?  
-   System.UInt16?  
-   System.UInt32?  
-   System.UInt64?  
-   System.Int16?  
-   System.Int32?  
-   System.Int64?  
-   System.Decimal?  
-   System.Single?  
-   System.Double?  
-   System.Guid?  
-   System.String?  
-   System.Char?  
-   System.DateTime?  

##  <a name="Trace"></a> Seguimiento  
 La directiva `trace` agrega una cadena a la salida de [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/). La directiva se ejecutará solamente cuando se desencadena el seguimiento, es decir, cuando el encabezado de solicitud `Ocp-Apim-Trace` está presente y establecido en `true`, y cuando el encabezado de solicitud `Ocp-Apim-Subscription-Key` está presente y contiene una clave válida asociada a la cuenta de administrador.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|trace|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|de origen|Literal de cadena que resulta significativo para el visor de seguimiento y especifica el origen del mensaje.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
##  <a name="Wait"></a> Espera  
 La directiva `wait` ejecuta sus directivas secundarias inmediatas en paralelo y espera a que se completen todas o una de ellas para finalizar. La directiva de espera puede tener las directivas de [envío de solicitud](api-management-advanced-policies.md#SendRequest), [obtención del valor de caché](api-management-caching-policies.md#GetFromCacheByKey) y [flujo de control](api-management-advanced-policies.md#choose) como directivas secundarias inmediatas.  
  
### <a name="policy-statement"></a>Instrucción de la directiva  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### <a name="example"></a>Ejemplo  
 En el ejemplo siguiente hay dos directivas `choose` que son directivas secundarias inmediatas de la directiva `wait`. Cada una de estas directivas `choose` se ejecuta en paralelo. Cada directiva `choose` intenta recuperar un valor almacenado en memoria caché. Si se produce un error de memoria caché, se llama a un servicio back-end para que proporcione el valor. En este ejemplo la directiva `wait` no se completa hasta que lo han hecho todas sus directivas secundarias inmediatas, ya que el atributo `for` está establecido en `all`.   Las variables de contexto (`execute-branch-one`, `value-one`, `execute-branch-two` y `value-two`) quedan fuera del ámbito de esta directiva de ejemplo.  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Elemento|DESCRIPCIÓN|Requerido|  
|-------------|-----------------|--------------|  
|wait|Elemento raíz. Solo puede contener como elementos secundarios a las directivas `send-request`, `cache-lookup-value` y `choose`.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Atributo|DESCRIPCIÓN|Requerido|Valor predeterminado|  
|---------------|-----------------|--------------|-------------|  
|for|Determina si la directiva `wait` espera a que se hayan completado todas las directivas secundarias inmediatas o solo una. Los valores permitidos son:<br /><br /> -   `all`: espera a que se hayan completado todas las directivas secundarias inmediatas.<br />- any: espera a que se haya completado cualquier directiva secundaria inmediata. En cuanto se completa la primera, la directiva `wait` también se completa y finaliza la ejecución de cualquier otra directiva secundaria inmediata.|Sin |todas|  
  
### <a name="usage"></a>Uso  
 
Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** entrante, saliente y back-end  
-   **Ámbitos de la directiva:** todos los ámbitos  
  
## <a name="next-steps"></a>pasos siguientes

Para obtener más información sobre cómo trabajar con directivas, consulte:
+ [Directivas de Azure API Management](api-management-howto-policies.md) 
+ [Expresiones de directiva](api-management-policy-expressions.md)
+ En la [Referencia de directivas](api-management-policy-reference.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](policy-samples.md)   
