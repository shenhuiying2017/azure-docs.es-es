---
title: "Directivas de almacenamiento en caché de Azure API Management | Microsoft Docs"
description: "Aprenda sobre las directivas de almacenamiento en caché disponibles para su uso en Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 8147199c-24d8-439f-b2a9-da28a70a890c
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 488a4c4b7daf5c07ca5f6b6bb72464279658d372
ms.sourcegitcommit: 7f1ce8be5367d492f4c8bb889ad50a99d85d9a89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="api-management-caching-policies"></a>Directivas de almacenamiento en caché de API Management
En este tema se proporciona una referencia para las siguientes directivas de API Management. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CachingPolicies"></a> Directivas de almacenamiento en caché  
  
-   Directivas de almacenamiento en caché de respuesta  
  
    -   [Get from cache](api-management-caching-policies.md#GetFromCache): realiza una búsqueda en caché y devuelve una respuesta en caché válida cuando está disponible.  
    -   [Store to cache](api-management-caching-policies.md#StoreToCache) (Almacenar en la caché): almacena en caché la respuesta de acuerdo con la configuración de control de caché especificada.  
  
-   Directivas de almacenamiento en caché de valores  

    -   [Obtener valor de caché](#GetFromCacheByKey) : recupere un elemento almacenado en caché por clave. 
    -   [Almacenar valor en caché](#StoreToCacheByKey) : almacene un elemento en la memoria caché por clave. 
    -   [Quitar valor de caché](#RemoveCacheByKey); quita un elemento de la memoria caché por clave.  
  
##  <a name="GetFromCache"></a> Get from cache  
 Use la directiva `cache-lookup` para realizar una consulta en la caché y devolver una respuesta en caché válida cuando esté disponible. Esta directiva se puede aplicar en aquellos casos en los que el contenido de respuesta permanezca estático durante un período de tiempo. El almacenamiento en caché de respuesta reduce el ancho de banda y los requisitos de procesamiento impuestos sobre el servidor web de back-end y disminuye la latencia percibida por los consumidores de API.  
  
> [!NOTE]
>  Esta directiva debe tener una directiva [Store to cache](api-management-caching-policies.md#StoreToCache) (Almacenar en la caché) correspondiente.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false" allow-private-response-caching="@(expression to evaluate)">  
  <vary-by-header>Accept</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Accept-Charset</vary-by-header>  
  <!-- should be present in most cases -->  
  <vary-by-header>Authorization</vary-by-header>  
  <!-- should be present when allow-private-response-caching is "true"-->  
  <vary-by-header>header name</vary-by-header>  
  <!-- optional, can repeated several times -->  
  <vary-by-query-parameter>parameter name</vary-by-query-parameter>  
  <!-- optional, can repeated several times -->  
</cache-lookup>  
```  
  
### <a name="examples"></a>Ejemplos  
  
#### <a name="example"></a>Ejemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
        <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none" must-revalidate="true">  
            <vary-by-query-parameter>version</vary-by-query-parameter>  
        </cache-lookup>           
    </inbound>  
    <outbound>  
        <cache-store duration="seconds" />  
        <base />          
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Ejemplo de uso de expresiones de directiva  
 En este ejemplo se muestra cómo configurar la duración del almacenamiento en caché de respuesta de API Management para que coincida con el almacenamiento en caché de respuesta del servicio de back-end especificado por la directiva `Cache-Control` del servicio de back-end. Para ver una demostración de la configuración y el uso de esta directiva, consulte [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Episodio 177 de Cloud Cover: más características de API Management con Vlad Vinogradsky) y avance al minuto 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Para más información, consulte [Expresiones de directiva](api-management-policy-expressions.md) y [Variable de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|cache-lookup|Elemento raíz.|Sí|  
|vary-by-header|Inicia el almacenamiento en caché de respuestas por valor del encabezado especificado, por ejemplo, Accept, Accept-Charset, Accept-Encoding, Accept-Language, Authorization, Expect, From, Host, If-Match.|No|  
|vary-by-query-parameter|Inicia el almacenamiento en caché de las respuestas por valor de los parámetros de consulta especificados. Permite introducir uno o varios parámetros. Utilice el punto y coma como separador. Si no se especifica ninguno, se usan todos los parámetros de consulta.|No|  
  
### <a name="attributes"></a>Attributes  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|allow-private-response-caching|Cuando se establece en `true`, permite el almacenamiento en caché de las solicitudes que contienen un encabezado de autorización.|No|false|  
|downstream-caching-type|Este atributo debe establecerse en uno de los siguientes valores.<br /><br /> -   none: no se permite el almacenamiento en caché de bajada.<br />-   private: se permite el almacenamiento en caché de bajada privado.<br />-   public: se permite el almacenamiento en caché de bajada privado y compartido.|No|Ninguna|  
|must-revalidate|Cuando el almacenamiento en caché de bajada está habilitado, este atributo activa o desactiva la directiva de control de caché `must-revalidate` en las respuestas de puerta de enlace.|No|true|  
|vary-by-developer|Se establece en `true` para almacenar en caché las respuestas por clave de desarrollador.|Sí||  
|vary-by-developer-groups|Se establece en `true` para almacenar en caché las respuestas por rol de usuario.|Sí||  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound  
-   **Ámbitos de directiva:** API, operation, product  
  
##  <a name="StoreToCache"></a> Store to cache (Almacenar en la caché)  
 La directiva `cache-store` almacena en caché las respuestas según la configuración de caché especificada. Esta directiva se puede aplicar en aquellos casos en los que el contenido de respuesta permanezca estático durante un período de tiempo. El almacenamiento en caché de respuesta reduce el ancho de banda y los requisitos de procesamiento impuestos sobre el servidor web de back-end y disminuye la latencia percibida por los consumidores de API.  
  
> [!NOTE]
>  Esta directiva debe tener una directiva [Get from cache](api-management-caching-policies.md#GetFromCache) correspondiente.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<cache-store duration="seconds" />  
```  
  
### <a name="examples"></a>Ejemplos  
  
#### <a name="example"></a>Ejemplo  
  
```xml  
<policies>  
    <inbound>  
        <base />  
          <cache-lookup vary-by-developer="true | false" vary-by-developer-groups="true | false" downstream-caching-type="none | private | public" must-revalidate="true | false">  
                <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->  
        </cache-lookup>  
    </inbound>  
    <outbound>  
        <base />  
            <cache-store duration="3600" />  
    </outbound>  
</policies>  
```  
  
#### <a name="example-using-policy-expressions"></a>Ejemplo de uso de expresiones de directiva  
 En este ejemplo se muestra cómo configurar la duración del almacenamiento en caché de respuesta de API Management para que coincida con el almacenamiento en caché de respuesta del servicio de back-end especificado por la directiva `Cache-Control` del servicio de back-end. Para ver una demostración de la configuración y el uso de esta directiva, consulte [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) (Episodio 177 de Cloud Cover: más características de API Management con Vlad Vinogradsky) y avance al minuto 25:25.  
  
```xml  
<!-- The following cache policy snippets demonstrate how to control API Management reponse cache duration with Cache-Control headers sent by the backend service. -->  
  
<!-- Copy this snippet into the inbound section -->  
<cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="public" must-revalidate="true" >  
  <vary-by-header>Accept</vary-by-header>  
  <vary-by-header>Accept-Charset</vary-by-header>  
</cache-lookup>  
  
<!-- Copy this snippet into the outbound section. Note that cache duration is set to the max-age value provided in the Cache-Control header received from the backend service or to the deafult value of 5 min if none is found  -->  
<cache-store duration="@{  
    var header = context.Response.Headers.GetValueOrDefault("Cache-Control","");  
    var maxAge = Regex.Match(header, @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value;  
    return (!string.IsNullOrEmpty(maxAge))?int.Parse(maxAge):300;  
  }"  
 />  
```  
  
 Para más información, consulte [Expresiones de directiva](api-management-policy-expressions.md) y [Variable de contexto](api-management-policy-expressions.md#ContextVariables).  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|cache-store|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|duration|Período de vida de las entradas almacenadas en caché, especificado en segundos.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de las directivas.  
  
-   **Secciones de la directiva:** outbound    
-   **Ámbitos de directiva:** API, operation, product  
  
##  <a name="GetFromCacheByKey"></a> Get value from cache (Obtener valor de la caché)  
 Use la directiva `cache-lookup-value` para realizar la búsqueda en la caché por clave y devolver un valor almacenado en caché. La clave puede tener un valor de cadena arbitrario y normalmente se proporciona mediante una expresión de directiva.  
  
> [!NOTE]
>  Esta directiva debe tener una directiva [Store value in cache](#StoreToCacheByKey) (Almacenar valor en la caché) correspondiente.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<cache-lookup-value key="cache key value"   
    default-value="value to use if cache lookup resulted in a miss"   
    variable-name="name of a variable looked up value is assigned to" />  
```  
  
### <a name="example"></a>Ejemplo  
 Para más información y ver ejemplos de esta directiva, consulte [Custom caching in Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/) (Almacenamiento en caché personalizado en Azure API Management).  
  
```xml  
<cache-lookup-value  
    key="@("userprofile-" + context.Variables["enduserid"])"    
    variable-name="userprofile" />  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|cache-lookup-value|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|default-value|Un valor que se asignará a la variable si la búsqueda de la clave de caché da lugar a un error. Si no se especifica este atributo, se asigna `null`.|No|`null`|  
|key|Valor de clave de caché para usar en la búsqueda.|Sí|N/D|  
|variable-name|Nombre de la [variable de contexto](api-management-policy-expressions.md#ContextVariables) a la que se asignará el valor buscado si la búsqueda tiene éxito. Si se produce un error de búsqueda, se asignará a la variable el valor del atributo `default-value` o `null`, si se omite el atributo `default-value`.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
-   **Ámbitos de la directiva:** global, API, operación, producto  
  
##  <a name="StoreToCacheByKey"></a> Store value in cache (Almacenar valor en la caché)  
 La directiva `cache-store-value` realiza el almacenamiento en caché mediante una clave. La clave puede tener un valor de cadena arbitrario y normalmente se proporciona mediante una expresión de directiva.  
  
> [!NOTE]
>  Esta directiva debe tener una directiva [Get from cache](#GetFromCacheByKey) (Obtener de la caché) correspondiente.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<cache-store-value key="cache key value" value="value to cache" duration="seconds" />  
```  
  
### <a name="example"></a>Ejemplo  
 Para más información y ver ejemplos de esta directiva, consulte [Custom caching in Azure API Management](https://azure.microsoft.com/documentation/articles/api-management-sample-cache-by-key/) (Almacenamiento en caché personalizado en Azure API Management).  
  
```xml  
<cache-store-value  
    key="@("userprofile-" + context.Variables["enduserid"])"  
    value="@((string)context.Variables["userprofile"])" duration="100000" />  
  
```  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|cache-store-value|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|duration|El valor se almacenará en la caché según el valor de duración proporcionado, especificado en segundos.|Sí|N/D|  
|key|La clave de caché con la que se almacenará el valor.|Sí|N/D|  
|value|El valor que se almacenará en la caché.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
-   **Ámbitos de la directiva:** global, API, operación, producto  
  
###  <a name="RemoveCacheByKey"></a> Remove value from cache (Quitar valor de la caché)  
La directiva `cache-remove-value` elimina un elemento almacenado en caché identificado por su clave. La clave puede tener un valor de cadena arbitrario y normalmente se proporciona mediante una expresión de directiva.  
  
#### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
  
<cache-remove-value key="cache key value"/>  
  
```  
  
#### <a name="example"></a>Ejemplo  
  
```xml  
  
<cache-remove-value key="@("userprofile-" + context.Variables["enduserid"])"/>  
  
```  
  
#### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|cache-remove-value|Elemento raíz.|Sí|  
  
#### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|key|La clave del valor anteriormente almacenado en caché que se quitará de la memoria caché.|Sí|N/D|  
  
#### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound, outbound, backend, on-error  
-   **Ámbitos de la directiva:** global, API, operación, producto  

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo trabajar con directivas, consulte:

+ [Directivas de Azure API Management](api-management-howto-policies.md)
+ [API de transformación](transform-api.md)
+ En la [Referencia de directivas](api-management-policy-reference.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](policy-samples.md)   
