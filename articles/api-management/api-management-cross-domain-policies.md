---
title: Directivas entre dominios de Azure API Management | Microsoft Docs
description: Aprenda sobre las directivas entre dominios disponibles para su uso en Azure API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 7689d277-8abe-472a-a78c-e6d4bd43455d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 590831454e8a18678e357b4824eb35a717d1fee0
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="api-management-cross-domain-policies"></a>Directivas entre dominios de API Management
En este tema se proporciona una referencia para las siguientes directivas de API Management. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="CrossDomainPolicies"></a> Directivas entre dominios  
  
-   [Permitir llamadas entre dominios](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.  
-   [CORS](api-management-cross-domain-policies.md#CORS) : agrega soporte de uso compartido de recursos entre orígenes (CORS) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.  
-   [JSONP](api-management-cross-domain-policies.md#JSONP) : agrega JSON con soporte de relleno (JSONP) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript.  
  
##  <a name="AllowCrossDomainCalls"></a> Allow cross-domain calls (Permitir llamadas entre dominios)  
 Use la directiva `cross-domain` para que la API sea accesible desde Adobe Flash y clientes basados en explorador de Microsoft Silverlight.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<cross-domain>  
   <!-Policy configuration is in the Adobe cross-domain policy file format,   
      see http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html-->  
</cross-domain>  
```  
  
### <a name="example"></a>Ejemplo  
  
```xml  
<cross-domain>  
    <cross-domain-policy>  
        <allow-http-request-headers-from domain='*' headers='*' />  
    </cross-domain-policy>  
</cross-domain>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|entre dominios|Elemento raíz. Los elementos secundarios deben ajustarse a la [especificación de archivos de directivas entre dominios de Adobe](http://www.adobe.com/devnet/articles/crossdomain_policy_file_spec.html).|Sí|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound  
-   **Ámbitos de la directiva:** global  
  
##  <a name="CORS"></a> CORS  
 La directiva `cors` agrega compatibilidad con el uso compartido de recursos entre orígenes (CORS) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.  
  
 CORS permite a un explorador y a un servidor interactuar y determinar si se permiten o no solicitudes específicas entre orígenes (por ejemplo, llamadas XMLHttpRequests realizadas desde JavaScript en una página web a otros dominios). Esto permite más flexibilidad que si solo se permiten solicitudes del mismo origen, pero es más seguro que permitir todas las solicitudes entre orígenes.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<cors allow-credentials="false|true">  
    <allowed-origins>  
        <origin>origin uri</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="number of seconds">  
        <method>http verb</method>  
    </allowed-methods>  
    <allowed-headers>  
        <header>header name</header>  
    </allowed-headers>  
    <expose-headers>  
        <header>header name</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="example"></a>Ejemplo  
 En este ejemplo se muestra cómo admitir solicitudes preparatorias, como aquellas con encabezados o métodos personalizados distintos de GET y POST. Para admitir encabezados personalizados y verbos HTTP adicionales, use las secciones `allowed-methods` y `allowed-headers` tal como se muestra en el ejemplo siguiente.  
  
```xml  
<cors allow-credentials="true">  
    <allowed-origins>  
        <!-- Localhost useful for development -->  
        <origin>http://localhost:8080/</origin>  
        <origin>http://example.com/</origin>  
    </allowed-origins>  
    <allowed-methods preflight-result-max-age="300">  
        <method>GET</method>  
        <method>POST</method>  
        <method>PATCH</method>  
        <method>DELETE</method>  
    </allowed-methods>  
    <allowed-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
        <header>x-zumo-version</header>  
        <header>x-zumo-auth</header>  
        <header>content-type</header>  
        <header>accept</header>  
    </allowed-headers>  
    <expose-headers>  
        <!-- Examples below show Azure Mobile Services headers -->  
        <header>x-zumo-installation-id</header>  
        <header>x-zumo-application</header>  
    </expose-headers>  
</cors>  
```  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|cors|Elemento raíz.|Sí|N/D|  
|allowed-origins|Contiene elementos `origin` que describen los orígenes permitidos para las solicitudes entre dominios. `allowed-origins` puede contener un único elemento `origin` que especifica `*` para permitir cualquier origen, o uno o varios elementos `origin` que contienen un identificador URI.|Sí|N/D|  
|origin|El valor puede ser `*` para permitir todos los orígenes o un identificador URI que especifica un único origen. El URI debe incluir un esquema, un host y un puerto.|Sí|Si se omite el puerto en un identificador URI, se usa el puerto 80 para HTTP y el puerto 443 para HTTPS.|  
|allowed-methods|Este elemento es necesario si se permiten métodos distintos de GET o POST. Contiene elementos `method` que especifican los verbos HTTP admitidos.|No|Si esta sección no está presente, se admiten GET y POST.|  
|estático|Especifica un verbo HTTP.|Al menos un elemento `method` es necesario si la sección `allowed-methods` está presente.|N/D|  
|allowed-headers|Este elemento contiene elementos `header` que especifican los nombres de los encabezados que pueden incluirse en la solicitud.|No|N/D|  
|expose-headers|Este elemento contiene elementos `header` que especifican los nombres de los encabezados a los que tendrá acceso el cliente.|No|N/D|  
|encabezado|Especifica un nombre de encabezado.|Al menos un elemento `header` es necesario en `allowed-headers` o `expose-headers` si está presente la sección.|N/D|  
  
### <a name="attributes"></a>Attributes  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|allow-credentials|El encabezado `Access-Control-Allow-Credentials` de la respuesta preparatoria se establecerá en el valor de este atributo e influirá en la posibilidad de que el cliente pueda enviar credenciales en solicitudes entre dominios.|No|false|  
|preflight-result-max-age|El encabezado `Access-Control-Max-Age` de la respuesta preparatoria se establecerá en el valor de este atributo e influirá en la posibilidad de que el agente del usuario pueda almacenar en caché la respuesta preparatoria.|No|0|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de directiva.  
  
-   **Secciones de la directiva:** inbound  
-   **Ámbitos de la directiva:** API, operación  
  
##  <a name="JSONP"></a> JSONP  
 La directiva `jsonp` agrega JSON con compatibilidad con relleno (JSONP) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript. JSONP es un método utilizado en los programas JavaScript para solicitar datos desde un servidor en un dominio diferente. JSONP sortea la limitación exigida por la mayoría de los exploradores web donde el acceso a las páginas web debe estar en el mismo dominio.  
  
### <a name="policy-statement"></a>Declaración de directiva  
  
```xml  
<jsonp callback-parameter-name="callback function name" />  
```  
  
### <a name="example"></a>Ejemplo  
  
```xml  
<jsonp callback-parameter-name="cb" />  
```  
  
 Si llama al método sin el parámetro de devolución de llamada ?cb=XXX, devolverá JSON sin formato (sin un envoltorio de llamada de función).  
  
 Si agrega el parámetro de devolución de llamada `?cb=XXX`, devolverá un resultado JSONP, envolviendo los resultados JSON originales en torno a la función de devolución de llamada como `XYZ('<json result goes here>');`.  
  
### <a name="elements"></a>Elementos  
  
|Nombre|Descripción|Obligatorio|  
|----------|-----------------|--------------|  
|jsonp|Elemento raíz.|Sí|  
  
### <a name="attributes"></a>Atributos  
  
|Nombre|Descripción|Obligatorio|Valor predeterminado|  
|----------|-----------------|--------------|-------------|  
|callback-parameter-name|La llamada de función de JavaScript entre dominios prefijada con el nombre de dominio completo en donde reside la función.|Sí|N/D|  
  
### <a name="usage"></a>Uso  
 Esta directiva puede usarse en las siguientes [secciones](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) y [ámbitos](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) de las directivas.  
  
-   **Secciones de la directiva:** outbound  
-   **Ámbitos de la directiva:** global, producto, API, operación  
  
## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo trabajar con directivas, consulte:

+ [Directivas de Azure API Management](api-management-howto-policies.md)
+ [API de transformación](transform-api.md)
+ En la [Referencia de directivas](api-management-policy-reference.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](policy-samples.md)   