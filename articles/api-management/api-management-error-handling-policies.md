---
title: Control de errores en las directivas de Azure API Management | Microsoft Docs
description: Aprenda a responder a las condiciones de error que se pueden producir durante el procesamiento de solicitudes en Azure API Management.
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: apimpm
ms.openlocfilehash: 6bc71c0745493d52128553a78a31c45a3bca30f8
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/04/2017
---
# <a name="error-handling-in-api-management-policies"></a>Control de errores en las directivas de API Management
Azure API Management proporciona un objeto `ProxyError` que permite a los publicadores responder a las condiciones de error que se pueden producir durante el procesamiento de solicitudes en el proxy. Para acceder al objeto `ProxyError` se usa la propiedad [context.LastError](api-management-policy-expressions.md#ContextVariables). Este objeto se puede usar en las directivas de la sección de directivas `on-error`. En este tema se proporciona una referencia a las funcionalidades de control de errores de Azure API Management.  
  
## <a name="error-handling-in-api-management"></a>Control de errores en API Management  
 Las directivas de Azure API Management están divididas en las secciones `inbound`, `backend`, `outbound` y `on-error`, como se muestra en el ejemplo siguiente.  
  
```xml  
<policies>  
  <inbound>  
    <!-- statements to be applied to the request go here -->  
  </inbound>  
  <backend>  
    <!-- statements to be applied before the request is   
         forwarded to the backend service go here -->  
    </backend>  
    <outbound>  
      <!-- statements to be applied to the response go here -->  
    </outbound>  
    <on-error>  
        <!-- statements to be applied if there is an error   
             condition go here -->  
  </on-error>  
</policies>  
```  
  
 Durante el procesamiento de una solicitud, se ejecutan pasos integrados junto con las directivas que están en el ámbito de la solicitud. Si se produce un error, el procesamiento salta inmediatamente a la sección de directivas `on-error`. La sección de directivas `on-error` se puede usar en cualquier ámbito, y los publicadores de API pueden configurar comportamientos predeterminados, como registrar el error en los centros de eventos o crear una nueva respuesta para devolver al autor de la llamada.  
  
> [!NOTE]
>  La sección `on-error` no está presente en las directivas de forma predeterminada. Para agregar la sección `on-error` a una directiva, vaya a la directiva deseada en el editor de directivas y agréguela. Para más información sobre cómo configurar directivas, consulte [Directivas en API Management](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/).  
>   
>  Si no hay ninguna sección `on-error`, los autores de la llamada recibirán mensajes de respuesta HTTP 400 o 500 si se produce una condición de error.  
  
### <a name="policies-allowed-in-on-error"></a>Directivas permitidas en on-error  
 Las siguientes directivas se pueden usar en la sección de directivas `on-error`.  
  
-   [choose](api-management-advanced-policies.md#choose)  
-   [set-variable](api-management-advanced-policies.md#set-variable)  
-   [find-and-replace](api-management-transformation-policies.md#Findandreplacestringinbody)  
-   [return-response](api-management-advanced-policies.md#ReturnResponse)  
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)  
-   [set-method](api-management-advanced-policies.md#SetRequestMethod)  
-   [set-status](api-management-advanced-policies.md#SetStatus)  
-   [send-request](api-management-advanced-policies.md#SendRequest)  
-   [send-one-way-request](api-management-advanced-policies.md#SendOneWayRequest)  
-   [log-to-eventhub](api-management-advanced-policies.md#log-to-eventhub)  
-   [json-to-xml](api-management-transformation-policies.md#ConvertJSONtoXML)  
-   [xml-to-json](api-management-transformation-policies.md#ConvertXMLtoJSON)  
  
## <a name="lasterror"></a>LastError  
 Cuando se produce un error y el control salta a la sección de directivas `on-error`, el error se almacena en la propiedad [context.LastError](api-management-policy-expressions.md#ContextVariables) a la que pueden acceder las directivas de la sección `on-error` y tiene las siguientes propiedades.  
  
|Nombre|Tipo|Descripción|Obligatorio|  
|----------|----------|-----------------|--------------|  
|Origen|string|Nombre del elemento donde se produjo el error. Puede ser el nombre de una directiva o el nombre de un paso de canalización integrado.|Sí|  
|Motivo|string|Código de error reconocible por la máquina, que se puede utilizar en el control de errores.|No|  
|Message|string|Descripción del error legible para el usuario.|Sí|  
|Scope|string|Nombre del ámbito donde se produjo el error; podría ser "global", "producto", "api" u "operación"|No|  
|Sección|string|Nombre de la sección donde se produjo el error; podría ser: "entrada", "back-end", "salida" o "error".|No|  
|Ruta de acceso|string|Especifica directivas anidadas, por ejemplo, "choose[3]/when[2]".|No|  
|PolicyId|string|Valor del atributo `id`, si lo especifica el cliente, en la directiva donde se produjo el error.|No|  
  
> [!NOTE]
>  Todas las directivas tienen un atributo `id` opcional que se pude agregar al elemento raíz de la directiva. Si este atributo está presente en una directiva cuando se produce una condición de error, el valor del atributo se puede recuperar mediante la propiedad `context.LastError.PolicyId`.  
  
## <a name="predefined-errors-for-built-in-steps"></a>Errores predefinidos para pasos integrados  
 Los errores siguientes están predefinidos para condiciones de error que se pueden producir durante la evaluación de pasos de procesamiento integrados.  
  
|Origen|Condición|Motivo|Message|  
|------------|---------------|------------|-------------|  
|configuración|El URI no coincide con ninguna API u operación.|OperationNotFound|No se puede hacer coincidir la solicitud entrante con una operación.|  
|authorization|No se ha proporcionado la clave de suscripción.|SubscriptionKeyNotFound|Acceso denegado debido a que falta la clave de suscripción. Asegúrese de incluir la clave de la suscripción al realizar solicitudes a esta API.|  
|authorization|El valor de la clave de suscripción no es válido.|SubscriptionKeyInvalid|Acceso denegado debido a una clave de suscripción no válida. Asegúrese de proporcionar una clave válida para una suscripción activa.|  
  
## <a name="predefined-errors-for-policies"></a>Errores predefinidos en directivas  
 Los errores siguientes están predefinidos para condiciones de error que se pueden producir durante la evaluación de directivas.  
  
|Origen|Condición|Motivo|Message|  
|------------|---------------|------------|-------------|  
|rate-limit|Límite de velocidad excedido|RateLimitExceeded|Rate limit is exceeded (Se ha excedido el límite de velocidad).|  
|quota|Cuota superada|QuotaExceeded|Out of call volume quota. (Fuera de la cuota de volumen de la llamada.) La cuota se reabastecerá en xx:xx:xx. O bien, Out of bandwidth quota (Fuera de la cuota de ancho de banda). La cuota se reabastecerá en xx:xx:xx.|  
|jsonp|El valor del parámetro de devolución de llamada no es válido (contiene caracteres incorrectos).|CallbackParameterInvalid|Value of callback parameter {callback-parameter-name} is not a valid JavaScript identifier (El valor del parámetro de devolución de llamada {callback-parameter-name} no es un identificador de JavaScript válido).|  
|ip-filter|No se pudo analizar la IP de autor de llamada de la solicitud.|FailedToParseCallerIP|Failed to establish IP address for the caller. Access denied (No se pudo establecer la dirección IP del autor de la llamada. Acceso denegado).|  
|ip-filter|La IP del autor de la llamada no está en la lista de permitidos.|CallerIpNotAllowed|Caller IP address {ip-address} is not allowed. Access denied (No se permite la IP del autor de la llamada {ip-address}. Acceso denegado).|  
|ip-filter|La IP del autor de la llamada está en la lista de bloqueados.|CallerIpBlocked|Caller IP address is blocked. Access denied (La IP del autor de la llamada está bloqueada. Acceso denegado).|  
|check-header|El encabezado necesario no está presente o falta un valor.|HeaderNotFound|Header {header-name} was not found in the request. Access denied (No se encontró el encabezado {header-name} en la solicitud. Acceso denegado).|  
|check-header|El encabezado necesario no está presente o falta un valor.|HeaderValueNotAllowed|Header {header-name} value of {header-value} is not allowed. Access denied (No se permite el valor {header-name} del encabezado de {header-value}. Acceso denegado).|  
|validate-jwt|Falta el token Jwt en la solicitud.|TokenNotFound|JWT not found in the request. Access denied (No se encuentra JWT en la solicitud. Acceso denegado).|  
|validate-jwt|Error de validación de contraseña.|TokenSignatureInvalid|<mensaje de la biblioteca de jwt\>. Acceso denegado.|  
|validate-jwt|Audiencia no válida.|TokenAudienceNotAllowed|<mensaje de la biblioteca de jwt\>. Acceso denegado.|  
|validate-jwt|Emisor no válido|TokenIssuerNotAllowed|<mensaje de la biblioteca de jwt\>. Acceso denegado.|  
|validate-jwt|Token expirado|TokenExpired|<mensaje de la biblioteca de jwt\>. Acceso denegado.|  
|validate-jwt|La clave de firma no se resolvió por el id.|TokenSignatureKeyNotFound|<mensaje de la biblioteca de jwt\>. Acceso denegado.|  
|validate-jwt|Faltan las notificaciones necesarias del token.|TokenClaimNotFound|JWT token is missing the following claims (Falta el token de JWT en las siguientes notificaciones): <c1\>, <c2\>, … Acceso denegado.|  
|validate-jwt|Error de coincidencia de valores de notificación.|TokenClaimValueNotAllowed|Claim {claim-name} value of {claim-value} is not allowed. Acceso denegado.|  
|validate-jwt|Otros errores de validación|JwtInvalid|<mensaje de la biblioteca de jwt\>.|

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre cómo trabajar con directivas, consulte:

+ [Directivas de Azure API Management](api-management-howto-policies.md)
+ [API de transformación](transform-api.md)
+ En la [Referencia de directivas](api-management-policy-reference.md) se muestra una lista completa de declaraciones de directivas y su configuración
+ [Ejemplos de directivas](policy-samples.md)   