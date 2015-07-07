<properties 
	pageTitle="Referencia de la directiva de Administración de API de Azure" 
	description="Obtenga información acerca de las directivas disponibles para configurar Administración de API." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/10/2015" 
	ms.author="sdanie"/>

# Referencia de la directiva de Administración de API de Azure

Esta sección proporciona un índice de las directivas en la [Referencia de la directiva de Administración de API de Azure][]. Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API][].

Las expresiones de directiva pueden utilizarse como valores de atributos o valores de texto en cualquiera de las directivas de Administración de API, a menos que la directiva especifique lo contrario. Algunas directivas como [Flujo de control][] y [Establecer variable][] se basan en expresiones de directiva. Para obtener más información, consulte [Directivas avanzadas][], [Expresiones de directiva][] y el vídeo siguiente.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

## Índice de referencia de directiva

-	[Directivas de restricción de acceso][]
	-	[Activar encabezado HTTP][]: aplica la existencia o el valor de un encabezado HTTP.
	-	[Limitar la frecuencia de llamada][]: evita los picos de uso de la API al limitar las llamadas o la tasa de consumo de ancho de banda.
	-	[Restringir IP de autor de llamada][]: filtra (permite/deniega) las llamadas de direcciones IP específicas o de intervalos de direcciones.
	-	[Establecer cuota de uso][]: le permite aplicar un volumen de llamadas renovables o permanentes o una cuota de ancho de banda.
	-	[Validar JWT][]: aplica la existencia y la validez de un JWT extraído de un encabezado HTTP especificado o un parámetro de consulta especificado.
-	[Directivas avanzadas][]
	-	[Flujo de control][]: aplica condicionalmente instrucciones de directiva basadas en los resultados de la evaluación de [expresiones][] booleanas.
	-	[Establecer variable][]: conserva un valor en una variable de [contexto][] con nombre para el acceso posterior.
-	[Directivas de autenticación][]
	-	[Autenticar con opción básica][]: autenticar con un servicio de back-end mediante la autenticación básica.
	-	[Autenticar con certificado de cliente][]: autenticar con un servicio de back-end mediante certificados de cliente.
-	[Directivas de almacenamiento en caché][] 
	-	[Obtener de caché][]: realiza una búsqueda en caché y devuelve una respuesta en caché válida cuando esté disponible.
	-	[Almacenar en caché][]: almacena en caché la respuesta de acuerdo con la configuración de control de caché especificada.
-	[Directivas entre dominios][] 
	-	[Permitir llamadas entre dominios][]: permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.
	-	[CORS][]: agrega soporte de uso compartido de recursos entre orígenes (CORS) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.
	-	[JSONP][]: agrega JSON con soporte de relleno (JSONP) a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript.
-	[Directivas de transformación][] 
	-	[Convertir JSON a XML][] : convierte el cuerpo de solicitud o respuesta de JSON a XML.
	-	[Convertir XML a JSON][] : convierte el cuerpo de solicitud o respuesta de XML a JSON.
	-	[Buscar y reemplazar la cadena en el cuerpo][]: encuentra una subcadena de solicitud o de respuesta y la reemplaza por una subcadena diferente.
	-	[Enmascarar URL en el contenido][]: reescribe (enmascara) vínculos en el cuerpo de respuesta y en el encabezado de la ubicación para que apunten al vínculo equivalente a través del proxy.
	-	[Establecer el servicio back-end][]: cambia el servicio back-end para una solicitud entrante.
	-	[Establecer cuerpo][] -establece el cuerpo del mensaje para las solicitudes entrantes y salientes.
	-	[Establecer encabezado HTTP][]: asigna un valor a un encabezado de respuesta o de solicitud existente o agrega un nuevo encabezado de este tipo.
	-	[Establecer el parámetro de cadena de consulta][]: agrega, reemplaza el valor o elimina el parámetro de la cadena de consulta de la solicitud.
	-	[URL de reescritura][]: convierte una URL de solicitud de su forma pública a la forma esperada por el servicio web.


[Directivas de restricción de acceso]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Activar encabezado HTTP]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limitar la frecuencia de llamada]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restringir IP de autor de llamada]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Establecer cuota de uso]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validar JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Directivas avanzadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Flujo de control]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Establecer variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expresiones]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexto]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables

[Directivas de autenticación]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Autenticar con opción básica]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Autenticar con certificado de cliente]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Directivas de almacenamiento en caché]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Obtener de caché]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Almacenar en caché]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Directivas entre dominios]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Permitir llamadas entre dominios]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Directivas de transformación]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convertir JSON a XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convertir XML a JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Buscar y reemplazar la cadena en el cuerpo]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Enmascarar URL en el contenido]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Establecer el servicio back-end]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Establecer cuerpo]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Establecer encabezado HTTP]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Establecer el parámetro de cadena de consulta]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL de reescritura]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Directivas en Administración de API]: api-management-howto-policies.md
[Referencia de la directiva de Administración de API de Azure]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expresiones de directiva]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=62-->