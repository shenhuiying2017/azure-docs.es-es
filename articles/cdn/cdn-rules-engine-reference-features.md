---
title: "Características del motor de reglas de la red CDN de Azure | Microsoft Docs"
description: "Documentación de referencia sobre las condiciones y características de coincidencia del motor de reglas de la red CDN de Azure."
services: cdn
documentationcenter: 
author: Lichard
manager: akucer
editor: 
ms.assetid: 669ef140-a6dd-4b62-9b9d-3f375a14215e
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: rli
ms.openlocfilehash: 858bc1dd2880583a3283522a01c9a48679b76296
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cdn-rules-engine-features"></a>Características del motor de reglas de la red CDN de Azure
En este tema se muestran descripciones detalladas de las características disponibles para el [motor de reglas](cdn-rules-engine.md)de Azure Content Delivery Network (CDN).

La tercera parte de una regla es la característica. Una característica define el tipo de acción que se aplica al tipo de solicitud identificado con un conjunto de condiciones de coincidencia.

## <a name="access-features"></a>Características de acceso

Estas características están diseñadas para controlar el acceso al contenido.


Nombre | Propósito
-----|--------
[Denegar acceso (403)](#deny-access-403) | Determina si todas las solicitudes se rechazan con una respuesta 403-Prohibido.
[Autenticación de token](#token-auth) | Determina si se aplica una solicitud de autenticación basada en tokens a una solicitud.
[Código de denegación de autenticación de tokens](#token-auth-denial-code) | Determina el tipo de respuesta que se devolverá al usuario cuando se deniega una solicitud debido a la autenticación basada en tokens.
[Ignorar mayúsculas y minúsculas en URL de autenticación de tokens](#token-auth-ignore-url-case) | Determina si las comparaciones de URL realizadas por la autenticación basada en tokens distinguen mayúsculas de minúsculas.
[Parámetro de autenticación de tokens](#token-auth-parameter) | Determina si debe cambiarse el parámetro de cadena de consulta de autenticación basada en tokens.


## <a name="caching-features"></a>Características de almacenamiento en caché

Estas características están diseñadas para personalizar cuándo y cómo se almacena el contenido en caché.

Nombre | Propósito
-----|--------
[Parámetros de ancho de banda](#bandwidth-parameters) | Determina si los parámetros de limitación de ancho de banda (por ejemplo, ec_rate y ec_prebuf) están activos.
[Limitación de ancho de banda](#bandwidth-throttling) | Limita el ancho de banda de la respuesta de los servidores perimetrales.
[Omisión de la memoria caché](#bypass-cache) | Determina si la solicitud debe omitir el almacenamiento en caché.
[Tratamiento de encabezados Cache-Control](#cache-control-header-treatment) | Controla la generación de encabezados `Cache-Control` mediante el servidor perimetral cuando está activa la característica externa Max-Age.
[Cadena de consulta de clave de caché](#cache-key-query-string) | Determina si la clave de caché incluirá o excluirá los parámetros de cadena de consulta asociados a una solicitud.
[Reescritura de clave de caché](#cache-key-rewrite) | Reescribe la clave de caché asociada a una solicitud.
[Relleno de la memoria caché completa](#complete-cache-fill) | Determina lo que ocurre cuando una solicitud tiene como resultado un error de caché parcial en un servidor perimetral.
[Comprimir tipos de archivo](#compress-file-types) | Define los formatos de archivo que se van a comprimir en el servidor.
[Max-Age interna predeterminada](#default-internal-max-age) | Determina el intervalo predeterminado de max-age para el servidor perimetral en la revalidación de caché del servidor de origen.
[Tratamiento del encabezado Expires](#expires-header-treatment) | Controla la generación de encabezados `Expires` mediante el servidor perimetral cuando está activa la característica externa Max-Age.
[Max-Age externa](#external-max-age) | Determina el intervalo de max-age para el explorador en la revalidación de caché del servidor de perimetral.
[Forzar Max-Age interna](#force-internal-max-age) | Determina el intervalo de max-age para el servidor perimetral en la revalidación de caché del servidor de origen.
[Compatibilidad de H.264 (descarga progresiva de HTTP)](#h264-support-http-progressive-download) | Determina los tipos de formatos de archivo H.264 que pueden usarse para transmitir contenido en streaming.
[Respetar la solicitud de no almacenar en caché](#honor-no-cache-request) | Determina si las solicitudes de no almacenar en caché de un cliente HTTP se reenviarán al servidor de origen.
[Ignorar la opción de no almacenar en caché de origen](#ignore-origin-no-cache) | Determina si la CDN ignora determinadas directivas procedentes de un servidor de origen.
[Ignorar intervalos que no se puedan satisfacer](#ignore-unsatisfiable-ranges) | Determina la respuesta que se devolverá a los clientes cuando una solicitud genere un código de estado "416 - No se puede satisfacer el intervalo solicitado".
[Max-Stale interna](#internal-max-stale) | Controla cuánto tiempo después de la hora de expiración normal puede atenderse un recurso almacenado en caché desde un servidor perimetral cuando el servidor perimetral no puede volver a validar el recurso almacenado en caché con el servidor de origen.
[Uso compartido de caché parcial](#partial-cache-sharing) | Determina si una solicitud puede generar contenido almacenado parcialmente en caché.
[Prevalidar el contenido guardado en caché](#prevalidate-cached-content) | Determina si el contenido almacenado en caché será apto para la revalidación temprana antes de que expire su período de vida.
[Actualizar archivos de caché de cero bytes](#refresh-zero-byte-cache-files) | Determina cómo controlan los servidores perimetrales la solicitud de un cliente HTTP para un recurso de la caché de 0 bytes.
[Establecer códigos de estado almacenables en caché](#set-cacheable-status-codes) | Define el conjunto de códigos de estado que puede dar lugar a contenido almacenado en caché.
[Entrega de contenido obsoleto en caso de error](#stale-content-delivery-on-error) | Determina si se entregará el contenido almacenado en caché cuando se produzca un error durante la revalidación de caché o al recuperar el contenido solicitado desde el servidor de origen del cliente.
[Obsoleto durante revalidación](#stale-while-revalidate) | Mejora el rendimiento al permitir que los servidores perimetrales sirvan un cliente obsoleto al solicitante mientras se lleva a cabo la revalidación.

## <a name="comment-feature"></a>Característica de comentario

Esta característica está diseñada para proporcionar información adicional dentro de una regla.

Nombre | Propósito
-----|--------
[Comment](#comment) | Permite agregar una nota en una regla.
 
## <a name="header-features"></a>Características de encabezado

Estas características están diseñadas para agregar, modificar o eliminar encabezados de la solicitud o respuesta.

Nombre | Propósito
-----|--------
[Encabezado de respuesta Age](#age-response-header) | Determina si se incluirá un encabezado de respuesta Age en la respuesta enviada al solicitante.
[Depurar encabezados de respuesta de la caché](#debug-cache-response-headers) | Determina si una respuesta puede incluir el encabezado de respuesta X-EC-Debug que proporciona información sobre la directiva de caché del recurso solicitado.
[Modificar encabezado de solicitud de cliente](#modify-client-request-header) | Sobrescribe, agrega o elimina un encabezado en una solicitud.
[Modificar encabezado de respuesta de cliente](#modify-client-response-header) | Sobrescribe, agrega o elimina un encabezado en una respuesta.
[Establecer encabezado personalizado de IP de cliente](#set-client-ip-custom-header) | Permite que la dirección IP del cliente solicitante se agregue a la solicitud como encabezado de solicitud personalizado.


## <a name="logging-features"></a>Características de registro

Estas características están diseñadas para personalizar los datos almacenados en archivos de registro sin procesar.

Nombre | Propósito
-----|--------
[Campo de registro personalizado 1](#custom-log-field-1) | Determina el formato y el contenido que se asignará al campo de registro personalizado en un archivo de registro sin procesar.
[Cadena de consulta del registro](#log-query-string) | Determina si una cadena de consulta se almacenará con la dirección URL en los registros de acceso.


<!---
## Optimize

These features determine whether a request will undergo the optimizations provided by Edge Optimizer.

Name | Purpose
-----|--------
Edge Optimizer | Determines whether Edge Optimizer can be applied to a request.
Edge Optimizer – Instantiate Configuration | Instantiates or activates the Edge Optimizer configuration associated with a site.

###Edge Optimizer
**Purpose:** Determines whether Edge Optimizer can be applied to a request.

If this feature has been enabled, then the following criteria must also be met before the request will be processed by Edge Optimizer:

- The requested content must use an edge CNAME URL.
- The edge CNAME referenced in the URL must correspond to a site whose configuration has been activated in a rule.

This feature requires the ADN platform and the Edge Optimizer feature.

Value|Result
-|-
Enabled|Indicates that the request is eligible for Edge Optimizer processing.
Disabled|Restores the default behavior. The default behavior is to deliver content over the ADN platform without any additional processing.

**Default Behavior:** Disabled
 

###Edge Optimizer - Instantiate Configuration
**Purpose:** Instantiates or activates the Edge Optimizer configuration associated with a site.

This feature requires the ADN platform and the Edge Optimizer feature.

Key information:

- Instantiation of a site configuration is required before requests to the corresponding edge CNAME can be processed by Edge Optimizer.
- This instantiation only needs to be performed a single time per site configuration. A site configuration that has been instantiated will remain in that state until the Edge Optimizer – Instantiate Configuration feature that references it is removed from the rule.
- The instantiation of a site configuration does not mean that all requests to the corresponding edge CNAME will automatically be processed by Edge Optimizer. The Edge Optimizer feature determines whether an individual request will be processed.

If the desired site does not appear in the list, then you should edit its configuration and verify that the Active option has been marked.

**Default Behavior:** Site configurations are inactive by default.
--->

## <a name="origin-features"></a>Características de origen

Estas características están diseñadas para controlar la forma en que la red CDN se comunica con un servidor de origen.

Nombre | Propósito
-----|--------
[Número máximo de solicitudes de conexión persistente](#maximum-keep-alive-requests) | Define el número máximo de solicitudes de conexión persistente antes de cerrarse.
[Encabezados de proxy especiales](#proxy-special-headers) | Define el conjunto de encabezados de solicitud específicos de la red CDN que se reenviará desde un servidor perimetral a un servidor de origen.


## <a name="specialty-features"></a>Características de especialidad

Estas características ofrecen funcionalidades avanzadas que solo deben utilizar los usuarios avanzados.

Nombre | Propósito
-----|--------
[Métodos HTTP almacenables en caché](#cacheable-http-methods) | Determina el conjunto de métodos HTTP adicionales que pueden almacenarse en caché en la red.
[Tamaño del cuerpo de solicitud almacenable en caché](#cacheable-request-body-size) | Define el umbral que determina si una respuesta POST se puede almacenar en caché.
[Variable USER](#user-variable) | Solo para uso interno.

 
## <a name="url-features"></a>Características URL

Estas características permiten reescribir una solicitud o redirigirla a una dirección URL diferente.

Nombre | Propósito
-----|--------
[Seguir redireccionamientos](#follow-redirects) | Determina si las solicitudes se pueden redirigir al nombre de host definido en el encabezado Ubicación que devuelve el servidor de origen de un cliente.
[Redirección de direcciones URL](#url-redirect) | Redirige las solicitudes a través del encabezado Ubicación.
[Reescritura de direcciones URL](#url-rewrite)  | Reescribe la dirección URL de la solicitud.



## <a name="azure-cdn-rules-engine-features-reference"></a>Referencia de las características del motor de reglas de la red CDN de Azure

---
### <a name="age-response-header"></a>Encabezado de respuesta Age
**Propósito**: determina si se incluirá un encabezado de respuesta Age en la respuesta enviada al solicitante.
Valor|Resultado
--|--
habilitado | El encabezado de respuesta Age se incluye en la respuesta enviada al solicitante.
Disabled | El encabezado de respuesta Age se excluye de la respuesta enviada al solicitante.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-parameters"></a>Parámetros de ancho de banda
**Objetivo:** determina si los parámetros de limitación de ancho de banda (por ejemplo, ec_rate y ec_prebuf) estarán activos.

Los parámetros de limitación de ancho de banda determinan si la velocidad de transferencia de datos para la solicitud de un cliente se limitará a una velocidad personalizada.

Valor|Resultado
--|--
habilitado|Permite que los servidores perimetrales admitan las solicitudes de limitación de ancho de banda.
Disabled|Hace que los servidores perimetrales ignoren los parámetros de limitación de ancho de banda. El contenido solicitado se servirá normalmente (es decir, sin limitación de ancho de banda).

**Comportamiento predeterminado**: habilitado.
 
[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bandwidth-throttling"></a>Limitación de ancho de banda
**Propósito**: limita el ancho de banda de la respuesta de los servidores perimetrales.

Dos de las siguientes opciones deben definirse para configurar correctamente la limitación de ancho de banda.

Opción|Descripción
--|--
Kbytes por segundo|Establezca esta opción en el ancho de banda máximo (Kb por segundo) que puede utilizarse para entregar la respuesta.
Segundos de búfer previo|Establezca esta opción en el número de segundos que los servidores perimetrales deben esperar hasta limitar el ancho de banda. El propósito de este período de tiempo de ancho de banda no restringido es evitar que un reproductor multimedia experimente problemas de cortes o de almacenamiento en búfer debido a la limitación del ancho de banda.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="bypass-cache"></a>Omisión de la memoria caché
**Propósito:** determina si la solicitud debe omitir el almacenamiento en caché.

Valor|Resultado
--|--
Enabled|Hace que todas las solicitudes pasen al servidor de origen, aunque el contenido se haya almacenado antes en caché en los servidores perimetrales.
Disabled|Hace que los servidores perimetrales almacenen en caché los recursos según la directiva de caché definida en sus encabezados de respuesta.

**Comportamiento predeterminado**:

- **HTTP grande:** deshabilitado

<!---
- **ADN:** Enabled

--->

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-http-methods"></a>Métodos HTTP almacenables en caché
**Propósito**: determina el conjunto de métodos HTTP adicionales que pueden almacenarse en caché en la red.

Información importante:

- Esta característica da por supuesto que las respuestas GET siempre deben almacenarse en caché. Por lo tanto, el método HTTP GET no se debe incluir al establecer esta característica.
- Esta característica solo admite el método HTTP POST. Para habilitar el almacenamiento en caché de la respuesta POST, establezca esta característica en `POST`.
- De forma predeterminada, solo se almacenan en caché las solicitudes cuyo cuerpo sea inferior a 14 Kb. Use la característica Tamaño del cuerpo de solicitud almacenable en caché para establecer el tamaño máximo del cuerpo de la solicitud.

**Comportamiento predeterminado:** solo se almacenan en caché las respuestas GET.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cacheable-request-body-size"></a>Tamaño del cuerpo de solicitud almacenable en caché
**Propósito**: define el umbral que determina si una respuesta POST se puede almacenar en caché.

Este umbral se determina especificando un tamaño máximo de cuerpo de solicitud. Las solicitudes que contengan un cuerpo de solicitud más grandes no se almacenarán en caché.

Información importante:

- Esta característica solo es aplicable cuando las respuestas POST son aptas para su almacenamiento en caché. Use la característica Métodos HTTP almacenables en caché para habilitar el almacenamiento en caché de solicitudes POST.
- El cuerpo de la solicitud se tiene en cuenta para:
    - valores x-www-form-urlencoded
    - Garantizar una clave de caché única
- Definir un tamaño de cuerpo de solicitud máximo grande puede afectar al rendimiento de la entrega de datos.
    - **Valor recomendado:** 14 Kb
    - **Valor mínimo:** 1 Kb

**Comportamiento predeterminado:** 14 Kb

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-control-header-treatment"></a>Tratamiento de encabezado Cache-Control
**Propósito**: controla la generación de encabezados `Cache-Control` mediante el servidor perimetral cuando está activa la característica externa Max-Age.

La manera más fácil de lograr este tipo de configuración es colocar las características Max-Age externa y Tratamiento de encabezados Cache-Control en la misma instrucción.

Valor|Resultado
--|--
Sobrescribir|Garantiza que se llevarán a cabo las siguientes acciones:<br/> - Sobrescribe el encabezado `Cache-Control` que generó el servidor de origen. <br/>- Agrega a la respuesta el encabezado `Cache-Control` que generó la característica externa Max-Age.
Pass Through|Garantiza que el encabezado `Cache-Control` que generó la característica externa Max-Age nunca se agregue a la respuesta. <br/> Si el servidor de origen genera un encabezado `Cache-Control`, se pasará directamente al usuario final. <br/> Si el servidor de origen no genera un encabezado `Cache-Control`, esta opción puede provocar que el encabezado de respuesta no contenga un encabezado `Cache-Control`.
Add if Missing|Si no se recibió un encabezado `Cache-Control` desde el servidor de origen, esta opción agrega el encabezado `Cache-Control` que generó la característica externa Max-Age. Esta opción es útil para garantizar que todos los recursos se asignan a un encabezado `Cache-Control`.
Remove| Esta opción garantiza que no se incluirá un encabezado `Cache-Control` en la respuesta de encabezado. Si ya se ha asignado un encabezado `Cache-Control`, se quitará de la respuesta de encabezado.

**Comportamiento predeterminado**: sobrescribir.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-query-string"></a>Cadena de consulta de clave de caché
**Propósito**: determina si la clave de caché incluirá o excluirá los parámetros de cadena de consulta asociados a una solicitud.

Información importante:

- Especifique uno o más nombres de parámetro de cadena de consulta. Cada nombre de parámetro debe delimitarse con un único espacio.
- Esta característica determina si los parámetros de cadena de consulta se incluirán o excluirán de la clave de caché. A continuación se proporciona información adicional para cada opción.

Tipo|Descripción
--|--
 Include|  Indica que cada parámetro especificado debe estar incluido en la clave de caché. Se generará una clave de caché única para cada solicitud que contenga un valor único para un parámetro de cadena de consulta definido en esta característica. 
 Include All  |Indica que se creará una clave de caché única para cada solicitud a un recurso que incluya una cadena de consulta única. Normalmente no se recomienda este tipo de configuración porque puede dar lugar a un pequeño porcentaje de aciertos de caché. Esto aumentará la carga en el servidor de origen, ya que tendrá que atender más solicitudes. Esta configuración reproduce el comportamiento de almacenamiento en caché que se conoce como "cache única" en la página Almacenamiento en caché de cadenas de consulta. 
 Exclude | Indica que solo los parámetros especificados se excluirán de la clave de caché. Todos los demás parámetros de cadena de consulta se incluirán en la clave de caché. 
 Exclude All  |Indica que todos los parámetros de cadena de consulta se excluirán de la clave de caché. Esta configuración reproduce el comportamiento predeterminado, que se conoce como "caché estándar" en la página Almacenamiento en caché de cadenas de consulta. 

La versatilidad del motor de reglas HTTP permite personalizar la manera en que se implementará el almacenamiento en caché de cadenas de consulta. Por ejemplo, puede especificar que el almacenamiento en caché de cadena consultas solo se realizará en determinadas ubicaciones o tipos de archivo.

Si desea replicar el comportamiento de almacenamiento en caché de cadenas de consulta conocido como "no almacenar en cache" en la página Almacenamiento en caché de cadenas de consulta, tendrá que crear una regla que contenga una condición de coincidencia de caracteres comodín de consulta de URL y una característica Omisión de la memoria caché. La condición de coincidencia de caracteres comodín de consulta de dirección URL debe establecerse en un asterisco (*).

#### <a name="sample-scenarios"></a>Escenarios de ejemplo

El siguiente ejemplo de uso de esta característica proporciona una solicitud de ejemplo y la clave de caché predeterminada:

- **Solicitud de ejemplo:** http://wpc.0001.&lt;Domain&gt;/800001/Origin/folder/asset.htm?sessionid=1234&amp;language=EN&amp;userid=01
- **Clave de caché predeterminada:** /800001/Origin/folder/asset.htm

##### <a name="include"></a>Include

Configuración de ejemplo:

- **Tipo:** Include
- **Parámetros:** idioma

Este tipo de configuración generaría la siguiente clave de caché de parámetro de cadena de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="include-all"></a>Include All

Configuración de ejemplo:

- **Tipo:** Include All

Este tipo de configuración generaría la siguiente clave de caché de parámetro de cadena de consulta:

    /800001/Origin/folder/asset.htm?sessionid=1234&language=EN&userid=01

##### <a name="exclude"></a>Exclude

Configuración de ejemplo:

- **Tipo:** Exclude
- **Parámetros:** id_sesión id_usuario

Este tipo de configuración generaría la siguiente clave de caché de parámetro de cadena de consulta:

    /800001/Origin/folder/asset.htm?language=EN

##### <a name="exclude-all"></a>Exclude All

Configuración de ejemplo:

- **Tipo:** Exclude All

Este tipo de configuración generaría la siguiente clave de caché de parámetro de cadena de consulta:

    /800001/Origin/folder/asset.htm

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="cache-key-rewrite"></a>Reescritura de clave de caché
**Propósito**: reescribe la clave de caché asociada a una solicitud.

Una clave de caché es la ruta de acceso relativa que identifica un recurso con fines de almacenamiento en caché. En otras palabras, los servidores buscarán una versión almacenada en caché de un recurso de acuerdo a la ruta de acceso que definió la clave de caché.

Para configurar esta característica, puede definir las siguientes opciones:

Opción|Descripción
--|--
Ruta de acceso original| Define la ruta de acceso relativa a los tipos de solicitudes cuyas claves de caché se reescribirán. Para definir una ruta de acceso relativa, seleccione una ruta de acceso de origen de base y, a continuación, define un patrón de expresión regular.
Nueva ruta de acceso|Define la ruta de acceso relativa de la nueva clave de caché. Para definir una ruta de acceso relativa, seleccione una ruta de acceso de origen de base y, a continuación, define un patrón de expresión regular. Esta ruta de acceso relativa se puede construir dinámicamente mediante variables HTTP.
**Comportamiento predeterminado:** la clave de caché de una solicitud viene determinada por el URI de solicitud.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="comment"></a>Comentario
**Propósito**: permite agregar una nota en una regla.

Un uso de esta característica es proporcionar información adicional sobre el uso general de una regla o por qué se agregó a la regla una determinada condición de coincidencia o característica.

Información importante:

- Se puede especificar un máximo de 150 caracteres.
- Use únicamente caracteres alfanuméricos.
- Esta característica no afecta al comportamiento de la regla. Su único objetivo es ofrecer un área donde se puede especificar información para futura referencia o que pueda ayudar a solucionar problemas de la regla.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="complete-cache-fill"></a>Relleno de la memoria caché completa
**Propósito**: determina lo que ocurre cuando una solicitud tiene como resultado un error de caché parcial en un servidor perimetral.

Un error de caché parcial describe el estado de la memoria caché para un recurso que no se ha descargado por completo en un servidor perimetral. Si un recurso se ha almacenado en caché solo parcialmente en un servidor perimetral, la siguiente solicitud para ese recurso se reenviará de nuevo al servidor de origen.
<!---
This feature is not available for the ADN platform. The typical traffic on this platform consists of relatively small assets. The size of the assets served through these platforms helps mitigate the effects of partial cache misses, since the next request will typically result in the asset being cached on that POP.

--->
Un error de caché parcial normalmente se produce después de que un usuario anule una descarga o en el caso de los recursos que se solicitan únicamente con solicitudes de intervalos HTTP. Esta característica resulta especialmente útil para recursos de gran tamaño que los usuarios no suelen descargar de principio a fin (por ejemplo, vídeos). Por lo tanto, esta característica está habilitada de forma predeterminada en la plataforma HTTP Large. Está deshabilitada en todas las demás plataformas.

Deje la configuración predeterminada de la plataforma HTTP Large, ya que reduce la carga en el servidor de origen del cliente y aumenta la velocidad con la que los clientes descargan el contenido.

Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta característica no se puede asociar con las siguientes condiciones de coincidencia: Cname perimetral, Literal de encabezado de solicitud, Carácter comodín de encabezado de solicitud, Literal de consulta de dirección URL y Carácter comodín de consulta de dirección URL.

Valor|Resultado
--|--
Enabled|Restablece el comportamiento predeterminado. El comportamiento predeterminado consiste en forzar al servidor perimetral para que inicie una captura de fondo del recurso desde el servidor de origen. Después, el recurso estará en la caché local del servidor perimetral.
Disabled|Evita que un servidor perimetral realice una captura de fondo del recurso. Esto significa que la próxima solicitud para ese recurso realizada desde esa región hará que un servidor perimetral lo solicite desde el servidor de origen del cliente.

**Comportamiento predeterminado**: habilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="compress-file-types"></a>Comprimir tipos de archivo
**Propósito**: define los formatos de archivo que se van a comprimir en el servidor.

Los formatos de archivo se pueden especificar con su tipo de medio de Internet (es decir, Content-Type). El tipo de medio de Internet son los metadatos independientes de la plataforma que permiten que los servidores identifiquen el formato de archivo de un recurso determinado. La siguiente es la lista de los tipos de medios más comunes de Internet.

Tipo de medio de Internet|Descripción
--|--
text/plain|Texto sin formato
text/html| Archivos HTML
text/css|Hojas de estilos en cascada (CSS)
application/x-javascript|Javascript
application/javascript|Javascript
Información importante:

- Especifique varios tipos de medios de Internet delimitando cada uno con un solo espacio. 
- Esta característica solo comprimirá recursos con un tamaño inferior a 1 MB. Nuestros servidores no comprimirán los recursos de mayor tamaño.
- Algunos tipos de contenido, como las imágenes, los vídeos y los recursos multimedia de audio (p. ej., JPG, MP3, MP4, etc.), ya están comprimidos. Una compresión adicional en estos tipos de recursos no reducirá significativamente el tamaño de archivo. Por lo tanto, se recomienda no habilitar la compresión en estos tipos de recursos.
- No se admiten caracteres comodín, como asteriscos.
- Antes de agregar esta característica a una regla, asegúrese de deshabilitar la opción Compression (Compresión) de la página Compression (Compresión) para la plataforma a la que se aplicará esta regla.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="custom-log-field-1"></a>Campo de registro personalizado 1
**Propósito**: determina el formato y el contenido que se asignará al campo de registro personalizado en un archivo de registro sin procesar.

El propósito principal de este campo personalizado es que le permite determinar qué valores de encabezado de solicitud y respuesta se almacenarán en los archivos de registro.

De forma predeterminada, el campo de registro personalizado se llama "x-ec_custom-1". Sin embargo, el nombre de este campo se puede personalizar en la página Configuración de registro sin procesar.

A continuación se define el formato que se debe usar para especificar encabezados de solicitud y respuesta.

Tipo de encabezado|Formato|Ejemplos
-|-|-
Encabezado de solicitud|%{[RequestHeader]()}[i]() | %{Accept-Encoding}i <br/> {Referer}i <br/> %{Authorization}i
Encabezado de respuesta|%{[ResponseHeader]()}[o]()| %{Age}o <br/> %{Content-Type}o <br/> %{Cookie}o

Información importante:

- Un campo de registro personalizado puede contener cualquier combinación de campos de encabezado y texto sin formato.
- Los caracteres válidos en este campo son los siguientes: alfanumérico (0-9, a-z y A-z), guiones, signo de dos puntos, punto y coma, apóstrofos, comas, puntos, caracteres de subrayado, signos de igual, paréntesis, corchetes y espacios. Solo se permiten los símbolos de porcentaje y las llaves cuando se usa para especificar un campo de encabezado.
- La ortografía para cada campo de encabezado especificado debe coincidir con el nombre de encabezado de solicitud o respuesta deseado.
- Si desea especificar varios encabezados, se recomienda utilizar un separador para indicar cada encabezado. Por ejemplo, podría utilizar una abreviatura para cada encabezado. Esta es una sintaxis de ejemplo.
    - AE: %{Accept-Encoding}i A: %{Authorization}i CT: %{Content-Type}o 

**Valor predeterminado** -:

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="debug-cache-response-headers"></a>Depurar encabezados de respuesta de la caché
**Propósito**: determina si una respuesta puede incluir el encabezado de respuesta X-EC-Debug, que proporciona información sobre la directiva de caché para el recurso solicitado.

Los encabezados de respuesta de depuración de caché se incluirán en la respuesta cuando se cumplan las dos condiciones siguientes:

- La característica Encabezados de respuesta de caché de depuración esté habilitada en la solicitud deseada.
- La solicitud anterior define el conjunto de encabezados de respuesta de caché de depuración que se incluirán en la respuesta.

Para solicitar los encabezados de respuesta de caché de depuración, incluya el siguiente encabezado y las directivas deseadas en la solicitud:

X-EC-Debug: _Directive1_,_Directive2_,_DirectiveN_

**Ejemplo:**

X-EC-Debug: x-ec-cache,x-ec-check-cacheable,x-ec-cache-key,x-ec-cache-state

Valor|Resultado
-|-
Enabled|Las solicitudes de encabezados de respuesta de caché de depuración devolverán una respuesta que incluye el encabezado X-EC-Debug.
Disabled|El encabezado de respuesta X-EC-Debug se excluirá de la respuesta.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="default-internal-max-age"></a>Max-Age interna predeterminada
**Propósito**: determina el intervalo predeterminado de max-age para el servidor perimetral en la revalidación de caché del servidor de origen. En otras palabras, la cantidad de tiempo que transcurrirá antes de que un servidor perimetral compruebe si un recurso almacenado en caché coincide con el recurso que se almacena en el servidor de origen.

Información importante:

- Esta acción solo se realizará para las respuestas de un servidor de origen que no asignen una indicación de max-age en el encabezado `Cache-Control` o `Expires`.
- Esta acción no se realizará para los recursos que no se consideran almacenables en caché.
- Esta acción no afecta a las revalidaciones de caché del explorador al servidor perimetral. Estos tipos de revalidaciones dependen de los encabezados `Cache-Control` o `Expires` enviados al explorador, los cuales se pueden personalizar con la característica externa Max-Age.
- Los resultados de esta acción no tienen un efecto visible en los encabezados de respuesta y el contenido devuelto por los servidores perimetrales, pero puede afectar a la cantidad de tráfico de revalidación enviado desde los servidores perimetrales al servidor de origen.
- Para configurar esta característica:
    - Seleccione el código de estado al que se puede aplicar un valor de max-age interna predeterminada.
    - Especifique un valor entero y seleccione la unidad de tiempo que desee (por ejemplo, segundos, minutos, horas, etc.). Este valor define el intervalo de max-age interna predeterminada.

- Establezca la unidad de tiempo en "Desactivado" para asignar un intervalo interno de max-age, predeterminado y de 7 días para las solicitudes que no tienen asignada una indicación de max-age en su encabezado `Cache-Control` o `Expires`.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta característica no se puede asociar con las siguientes condiciones de coincidencia: 
    - perimetral 
    - Cname
    - Literal de encabezado de solicitud
    - Carácter comodín de encabezado de solicitud
    - Método de solicitud
    - Literal de consulta de dirección URL
    - Carácter comodín de consulta de dirección URL

**Valor predeterminado:** 7 días

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="deny-access-403"></a>Denegar acceso (403)
**Propósito**: determina si todas las solicitudes se rechazan con una respuesta 403 Prohibido.

Valor | Resultado
------|-------
Enabled| Hace que todas las solicitudes que cumplen los criterios de coincidencia se rechacen con respuesta 403 Prohibido.
Disabled| Restablece el comportamiento predeterminado. El comportamiento predeterminado es permitir que el servidor de origen determine el tipo de respuesta que se devolverá.

**Comportamiento predeterminado**: deshabilitado.

> [!TIP]
   > Un uso posible para esta característica es asociarla a una condición de coincidencia del encabezado de solicitud para bloquear el acceso a los remitentes HTTP que usen vínculos en línea al contenido.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="expires-header-treatment"></a>Tratamiento del encabezado Expires
**Propósito**: controla la generación de encabezados `Expires` mediante el servidor perimetral cuando está activa la característica externa Max-Age.

La manera más fácil de lograr este tipo de configuración es colocar las características Max-Age externa y Tratamiento de encabezados Expires en la misma instrucción.

Valor|Resultado
--|--
Sobrescribir|Garantiza que se llevarán a cabo las siguientes acciones:<br/>- Sobrescribir el encabezado `Expires` que generó el servidor de origen.<br/>- Agregar a la respuesta el encabezado `Expires` que generó la característica externa Max-Age.
Pass Through|Garantiza que el encabezado `Expires` que generó la característica externa Max-Age nunca se agregue a la respuesta. <br/> Si el servidor de origen genera un encabezado `Expires`, pasará directamente al usuario final. <br/>Si el servidor de origen no genera un encabezado `Expires`, esta opción puede provocar que el encabezado de respuesta no contenga un encabezado `Expires`.
Add if Missing| Si no se recibió un encabezado `Expires` desde el servidor de origen, esta opción agrega el encabezado `Expires` que generó la característica externa Max-Age. Esta opción es útil para garantizar que todos los recursos se asignarán a un encabezado `Expires`.
Remove| Esta opción garantiza que no se incluirá un encabezado `Expires` en la respuesta de encabezado. Si ya se ha asignado un encabezado `Expires`, se quitará de la respuesta de encabezado.

**Comportamiento predeterminado**: sobrescribir.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="external-max-age"></a>Max-Age externa
**Propósito**: determina el intervalo de max-age para el explorador en la revalidación de caché del servidor de perimetral. En otras palabras, cantidad de tiempo que transcurrirá antes de que un explorador compruebe si hay una nueva versión de un recurso en un servidor perimetral.

Si habilita esta característica, se generarán los encabezados `Cache-Control: max-age` y `Expires` en los servidores perimetrales y se enviarán al cliente HTTP. De forma predeterminada, estos encabezados sobrescribirán los creados por el servidor de origen. Sin embargo, pueden utilizarse las características Tratamiento de encabezados Cache-Control y Tratamiento de encabezados Expires para modificar este comportamiento.

Información importante:

- Esta acción no afecta a las revalidaciones de caché del servidor perimetral al servidor de origen. Estos tipos de revalidaciones se determinan según los encabezados `Cache-Control` y `Expires` recibidos desde el servidor de origen, y se pueden personalizar mediante las características internas predeterminadas Max-Age y Forzar Max-Age.
- Para configurar esta característica, especifique un valor entero y seleccione la unidad de tiempo que desee (por ejemplo, segundos, minutos, horas, etc.).
- Si se establece esta característica en un valor negativo, los servidores perimetrales enviarán un encabezado `Cache-Control: no-cache` y una hora `Expires` establecida en el pasado con cada respuesta al explorador. Aunque un cliente HTTP no almacenará la respuesta en caché, esta configuración no afectará a la capacidad de los servidores perimetrales para almacenar en caché la respuesta del servidor de origen.
- Al establecer la unidad de tiempo en "Off" se deshabilitará esta característica. Los encabezados `Cache-Control` y `Expires` almacenados en caché que cuentan con la respuesta del servidor de origen, pasarán directamente al explorador.

**Comportamiento predeterminado:** desactivado

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="follow-redirects"></a>Seguir redireccionamientos
**Propósito**: determina si las solicitudes se pueden redirigir al nombre de host definido en el encabezado Ubicación que devuelve el servidor de origen de un cliente.

Información importante:

- Las solicitudes solo se pueden redirigir a los servidores perimetrales CNAME que correspondan a la misma plataforma.

Valor|Resultado
-|-
Enabled|Las solicitudes se pueden redirigir.
Disabled|Las solicitudes no se redirigirán.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="force-internal-max-age"></a>Forzar Max-Age interna
**Propósito**: determina el intervalo de max-age para el servidor perimetral en la revalidación de caché del servidor de origen. En otras palabras, la cantidad de tiempo que transcurrirá antes de que un servidor perimetral pueda comprobar si un recurso almacenado en caché coincide con el recurso que se almacena en el servidor de origen.

Información importante:

- Esta característica invalidará el intervalo de max-age definido en los encabezados `Cache-Control` o `Expires` generados en un servidor de origen.
- Esta característica no afecta a las revalidaciones de caché del explorador al servidor perimetral. Estos tipos de revalidaciones dependen de los encabezados `Cache-Control` o `Expires` que se envían al explorador.
- Esta característica no tiene un efecto visible en la respuesta entregada por un servidor perimetral al solicitante. Sin embargo, esto puede afectar a la cantidad de tráfico de revalidación enviado desde los servidores perimetrales al servidor de origen.
- Para configurar esta característica:
    - Seleccione el código de estado al que se aplicará un valor de max-age interna.
    - Especifique un valor entero y seleccione la unidad de tiempo que desee (por ejemplo, segundos, minutos, horas, etc.). Este valor define el intervalo de max-age interna de la solicitud.

- Al establecer la unidad de tiempo en "Off" se deshabilitará esta característica. No se asignará un intervalo de max-age interna a los recursos solicitados. Si el encabezado original no incluye instrucciones de almacenamiento en caché, el recurso se almacenará en caché según la configuración activa de la característica Max-Age interna predeterminada.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta característica no se puede asociar con las siguientes condiciones de coincidencia: 
    - perimetral 
    - Cname
    - Literal de encabezado de solicitud
    - Carácter comodín de encabezado de solicitud
    - Método de solicitud
    - Literal de consulta de dirección URL
    - Carácter comodín de consulta de dirección URL

**Comportamiento predeterminado:** desactivado

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="h264-support-http-progressive-download"></a>Compatibilidad de H.264 (Descarga progresiva de HTTP)
**Propósito**: determina los tipos de formatos de archivo H.264 que pueden usarse para transmitir contenido en streaming.

Información importante:

- Defina un conjunto de extensiones de nombre de archivo H.264 permitidas, delimitadas por espacios, en la opción File Extensions (Extensiones de archivo). La opción File Extensions (Extensiones de archivo) invalidará el comportamiento predeterminado. Para mantener la compatibilidad con MP4 y F4V, incluya esas extensiones de nombre de archivo al establecer esta opción. 
- Asegúrese de incluir un punto al especificar cada extensión de nombre de archivo (por ejemplo, .mp4, .f4v).

**Comportamiento predeterminado:** la descarga progresiva HTTP es compatible con medios MP4 y F4V de forma predeterminada.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="honor-no-cache-request"></a>Respetar solicitud de no almacenar en caché
**Propósito**: determina si las solicitudes de no almacenar en caché de un cliente HTTP se reenviarán al servidor de origen.

Una solicitud para no almacenar en caché se produce cuando el cliente HTTP envía un encabezado `Cache-Control: no-cache` o `Pragma: no-cache` en la solicitud HTTP.

Valor|Resultado
--|--
Enabled|Permite reenviar las solicitudes de no almacenar en caché de un cliente HTTP al servidor de origen, y el servidor de origen devolverá los encabezados y el cuerpo de respuesta a través del servidor perimetral al cliente HTTP.
Disabled|Restablece el comportamiento predeterminado. El comportamiento predeterminado es impedir que las solicitudes de no almacenar en caché se reenvíen al servidor de origen.

Para todo el tráfico de producción, se recomienda dejar esta característica deshabilitada de forma predeterminada. De lo contrario, los servidores de origen no estarán protegidos frente a usuarios finales que accidentalmente podrían desencadenar muchas solicitudes de no almacenar en caché al actualizar las páginas web, o frente a muchos reproductores de medios populares que están codificados para enviar un encabezado de no almacenar en caché con cada solicitud de vídeo. No obstante, esta característica puede ser útil para aplicarla a determinados directorios de ensayo o pruebas que no son de producción, para poder extraer contenido nuevo a petición desde el servidor de origen.

El estado de la caché que se notificará para una solicitud que se puede reenviar a un servidor de origen debido a esta característica es TCP_Client_Refresh_Miss. El informe Estados de la memoria caché, que está disponible en el módulo principal de informes, proporciona información estadística por estado de la caché. Esto le permite realizar un seguimiento del número y el porcentaje de solicitudes que se reenvían a un servidor de origen debido a esta característica.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-origin-no-cache"></a>Ignorar no almacenar en caché de origen
**Propósito**: determina si la CDN ignorará las siguientes directivas procedentes de un servidor de origen:

- `Cache-Control: private`
- `Cache-Control: no-store`
- `Cache-Control: no-cache`
- `Pragma: no-cache`

Información importante:

- Para configurar esta característica, defina una lista delimitada por espacios de códigos de estado para los que se pasarán por alto las directivas mencionadas anteriormente.
- El conjunto de códigos de estados válidos para esta característica son: 200, 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 y 505.
- Para deshabilitar esta característica, establézcala en un valor en blanco.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta característica no se puede asociar con las siguientes condiciones de coincidencia: 
    - perimetral 
    - Cname
    - Literal de encabezado de solicitud
    - Carácter comodín de encabezado de solicitud
    - Método de solicitud
    - Literal de consulta de dirección URL
    - Carácter comodín de consulta de dirección URL

**Comportamiento predeterminado:** el comportamiento predeterminado es respetar las directivas mencionadas anteriormente.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="ignore-unsatisfiable-ranges"></a>Ignorar intervalos que no se puedan satisfacer 
**Propósito**: determina la respuesta que se devolverá a los clientes cuando una solicitud genere un código de estado 416 No se puede satisfacer el intervalo solicitado.

De forma predeterminada, este código de estado se devuelve cuando un servidor perimetral no puede satisfacer la solicitud de intervalo de bytes especificado y no se ha especificado un campo de encabezado de solicitud If-Range.

Valor|Resultado
-|-
habilitado|Impide que nuestros servidores perimetrales respondan a una solicitud de intervalo de bytes no válida con un código de estado "416 - No se puede satisfacer el intervalo solicitado". En su lugar, los servidores proporcionarán el recurso solicitado y devolverán un valor "200 OK" al cliente.
Disabled|Restablece el comportamiento predeterminado. El comportamiento predeterminado es respetar el código de estado 416 No se puede satisfacer el intervalo solicitado.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="internal-max-stale"></a>Max-Stale interna
**Propósito**: controla cuánto tiempo después de la hora de expiración normal puede atenderse un recurso almacenado en caché desde un servidor perimetral cuando el servidor perimetral no puede volver a validar el recurso almacenado en caché con el servidor de origen.

Normalmente, cuando se agota el tiempo max-age de un recurso, el servidor perimetral envía una solicitud de revalidación al servidor de origen. El servidor de origen responde con 304 No modificado para proporcionar el servidor perimetral una nueva concesión sobre el recurso en caché, o con 200 OK para proporcionar al servidor perimetral una versión actualizada del recurso almacenado en caché.

Si el servidor perimetral no se puede establecer una conexión con el servidor de origen al intentar esta revalidación, esta característica Max-Stale interna controla si el servidor perimetral puede continuar proporcionando el recurso ahora obsoleto, y durante cuánto tiempo.

Tenga en cuenta que este intervalo de tiempo se inicia cuando expira la vigencia máxima del recurso, no cuando se produce la revalidación errónea. Por lo tanto, el período máximo durante el cual puede proporcionarse un recurso sin una revalidación correcta es la cantidad de tiempo especificadao por la combinación de max-age más max-stale. Por ejemplo, si un recurso se almacenó en caché a las 9:00 con un valor de max-age de 30 minutos y un valor de max-stale de 15 minutos, un intento de revalidación erróneo a las 9:44 haría que un usuario final recibiera el recurso obsoleto almacenado en caché, mientras que un intento de revalidación erróneo a las 9:46 haría que el usuario final recibiera un mensaje "504 - Tiempo de espera agotado para la puerta de enlace".

Los encabezados `Cache-Control: must-revalidate` o `Cache-Control: proxy-revalidate` recibidos desde el servidor de origen reemplazarán cualquier valor configurado para esta característica. Si alguno de los encabezados se recibe desde el servidor de origen cuando un recurso se almacena inicialmente en caché, el servidor perimetral no proporcionará un recurso almacenado en caché obsoleto. En este caso, si el servidor perimetral no se puede revalidar con el origen una vez transcurrido el intervalo de max-age del recurso, el servidor perimetral devuelve un mensaje de error "504 - Tiempo de espera agotado para la puerta de enlace".

Información importante:

- Para configurar esta característica:
    - Seleccione el código de estado al que se aplicará un valor de max-stale.
    - Especifique un valor entero y seleccione la unidad de tiempo que desee (por ejemplo, segundos, minutos, horas, etc.). Este valor define el tiempo máximo de vigencia interno que se aplicará.

- Al establecer la unidad de tiempo en "Off" se deshabilitará esta característica. No se proporcionarán recursos almacenados en caché más allá de la fecha de expiración normal.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta característica no se puede asociar con las siguientes condiciones de coincidencia: 
    - perimetral 
    - Cname
    - Literal de encabezado de solicitud
    - Carácter comodín de encabezado de solicitud
    - Método de solicitud
    - Literal de consulta de dirección URL
    - Carácter comodín de consulta de dirección URL

**Comportamiento predeterminado:** dos minutos

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="log-query-string"></a>Cadena de consulta del registro
**Propósito**: determina si una cadena de consulta se almacenará con la dirección URL en los registros de acceso.

Valor|Resultado
-|-
Enabled|Permite almacenar cadenas de consulta cuando se anotan direcciones URL en un registro de acceso. Si una dirección URL no contiene una cadena de consulta, esta opción no tendrá efecto.
Disabled|Restablece el comportamiento predeterminado. El comportamiento predeterminado es pasar por alto las cadenas de consulta cuando se anotan direcciones URL en un registro de acceso.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="maximum-keep-alive-requests"></a>Número máximo de solicitudes de conexión persistente
**Propósito**: define el número máximo de solicitudes de conexión persistente antes de que se cierre.

Es muy recomendable no establecer un valor bajo en el número máximo de solicitudes porque puede afectar negativamente al rendimiento.

Información importante:

- Especifique este valor como un número entero.
- No incluya comas ni puntos en el valor especificado.

**Valor predeterminado:** 10.000 solicitudes

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-request-header"></a>Modificar encabezado de solicitud de cliente
**Propósito:** cada solicitud contiene un conjunto de encabezados de solicitud que la describen. Esta característica puede:

- Anexar o sobrescribir el valor asignado a un encabezado de solicitud. Si el encabezado de solicitud especificado no existe, esta característica lo agregará a la solicitud.
- Eliminar un encabezado de solicitud de la solicitud.

Las solicitudes que se reenvían a un servidor de origen reflejarán los cambios realizados por esta característica.

En un encabezado de solicitud se puede realizar una de las siguientes acciones:

Opción|Descripción|Ejemplo
-|-|-
Append|El valor especificado se agregará al final del valor del encabezado de solicitud existente.|**Valor de encabezado de solicitud (cliente):**Valor1 <br/> **Valor de encabezado de solicitud (motor de reglas HTTP):**Valor2 <br/>**Nuevo valor de encabezado de solicitud:**Valor1Valor2
Sobrescribir|El valor del encabezado de solicitud se establecerá en el valor especificado.|**Valor de encabezado de solicitud (cliente):**Valor1 <br/>**Valor de encabezado de solicitud (motor de reglas HTTP):**Valor2 <br/>**Nuevo valor de encabezado de solicitud:**Valor2 <br/>
Eliminar|Elimina el encabezado de solicitud especificado.|**Valor de encabezado de solicitud (cliente):**Valor1 <br/> **Modificar configuración de encabezado de solicitud de cliente:** elimina el encabezado de solicitud en cuestión. <br/>**Resultado:** el encabezado de solicitud especificado no se reenviará al servidor de origen.

Información importante:

- Asegúrese de que el valor especificado en la opción Nombre coincide exactamente con el encabezado de solicitud deseado.
- No se tienen en cuenta las mayúsculas y minúsculas para identificar un encabezado. Por ejemplo, se puede utilizar cualquiera de las siguientes variaciones del nombre de encabezado `Cache-Control` para identificarlo:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Al especificar un nombre de encabezado, use solamente caracteres alfanuméricos, guiones o caracteres de subrayado.
- Si elimina un encabezado, impedirá que los servidores perimetrales lo reenvíen a un servidor de origen.
- Los encabezados siguientes están reservados y esta característica no los puede modificar:
    - forwarded
    - host
    - via
    - Warning (Advertencia)
    - x-forwarded-for
    - Todos los nombres de encabezado que empiezan por "x-ce" están reservados.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="modify-client-response-header"></a>Modificar encabezado de respuesta de cliente
Cada respuesta contiene un conjunto de encabezados de respuesta que lo describen. Esta característica puede:

- Anexar o sobrescribir el valor asignado a un encabezado de respuesta. Si el encabezado de respuesta especificado no existe, esta característica lo agregará a la respuesta.
- Eliminar un encabezado de respuesta de la respuesta.

De forma predeterminada, un servidor de origen y los servidores perimetrales definen los valores del encabezado de respuesta.

En un encabezado de respuesta se puede realizar una de las siguientes acciones:

Opción|Descripción|Ejemplo
-|-|-
Append|El valor especificado se agregará al final del valor del encabezado de respuesta existente.|**Valor de encabezado de respuesta (cliente):**Valor1 <br/> **Valor de encabezado de respuesta (motor de reglas HTTP):**Valor2 <br/>**Nuevo valor de encabezado de respuesta:**Valor1Valor2
Sobrescribir|El valor del encabezado de respuesta se establecerá en el valor especificado.|**Valor de encabezado de respuesta (cliente):**Valor1 <br/>**Valor de encabezado de respuesta (motor de reglas HTTP):**Valor2 <br/>**Nuevo valor de encabezado de respuesta:**Valor2 <br/>
Eliminar|Elimina el encabezado de respuesta especificado.|**Valor de encabezado de respuesta (cliente):** Valor1 <br/> **Modificar configuración de encabezado de respuesta de cliente:** elimina el encabezado de respuesta en cuestión. <br/>**Resultado:** el encabezado de respuesta especificado no se reenviará al solicitante.

Información importante:

- Asegúrese de que el valor especificado en la opción Nombre coincida exactamente con el encabezado de respuesta deseado. 
- No se tienen en cuenta las mayúsculas y minúsculas para identificar un encabezado. Por ejemplo, se puede utilizar cualquiera de las siguientes variaciones del nombre de encabezado `Cache-Control` para identificarlo:
    - cache-control
    - CACHE-CONTROL
    - cachE-Control
- Si elimina un encabezado, impedirá que se reenvíe al solicitante.
- Los encabezados siguientes están reservados y esta característica no los puede modificar:
    - accept-encoding
    - age
    - connection
    - content-encoding
    - content-length
    - content-range
    - fecha
    - server
    - trailer
    - transfer-encoding
    - upgrade
    - vary
    - via
    - Warning (Advertencia)
    - Todos los nombres de encabezado que empiezan por "x-ce" están reservados.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="partial-cache-sharing"></a>Uso compartido de caché parcial
**Propósito**: determina si una solicitud puede generar contenido almacenado parcialmente en caché.

Esta caché parcial puede utilizarse para atender nuevas solicitudes de ese contenido hasta que se almacene en caché todo el contenido solicitado.

Valor|Resultado
-|-
Enabled|Las solicitudes pueden generar contenido almacenado parcialmente en caché.
Disabled|Las solicitudes solamente pueden generar una versión completamente almacenada en caché del contenido solicitado.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="prevalidate-cached-content"></a>PreValidate contenido en caché
**Propósito**: determina si el contenido almacenado en caché será apto para la revalidación temprana antes de que expire su período de vida.

Defina la cantidad de tiempo antes de la expiración del período de vida del contenido solicitado durante el cual será apto para la revalidación temprana.

Información importante:

- Cuando se selecciona "Off" como unidad de tiempo, la revalidación debe realizarse después de que el contenido almacenado en caché haya expirado. No se debe especificar tiempo y se pasará por alto.

**Comportamiento predeterminado:** desactivado. La revalidación solo puede realizarse después de que expire el período de vida del contenido almacenado en caché.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="proxy-special-headers"></a>Encabezados de proxy especiales
**Propósito**: define el conjunto de encabezados de solicitud específicos de la red CDN que se reenviará desde un servidor perimetral a un servidor de origen.

Información importante:

- Cada encabezado de solicitud específico de la red CDN definido en esta característica se reenviará a un servidor de origen.
- Para evitar que un encabezado de solicitud específico de la red CDN se reenvíe a un servidor de origen, elimínelo de esta lista.

**Comportamiento predeterminado:** se reenviarán todos los encabezados de solicitud específica de la red CDN al servidor de origen.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="refresh-zero-byte-cache-files"></a>Actualizar archivos de caché de cero bytes
**Propósito**: determina cómo los servidores perimetrales controlan la solicitud de un cliente HTTP para un recurso de la caché de 0 bytes.

Los valores válidos son:

Valor|Resultado
--|--
habilitado|Hace que el servidor perimetral vuelva a capturar el recurso desde el servidor de origen.
Disabled|Restablece el comportamiento predeterminado. El comportamiento predeterminado es atender los recursos de caché válidos cuando se soliciten.
Esta característica no es necesaria para la entrega de contenido y un almacenamiento en caché correcto, pero puede resultar útil para solucionar este problema. Por ejemplo, los generadores de contenido dinámicos en los servidores de origen pueden provocar accidentalmente que se envíen respuestas de 0 bytes a los servidores perimetrales. Normalmente, los servidores perimetrales almacenan estos tipos de respuestas en la caché. Si sabe que una respuesta de 0 bytes nunca es una respuesta válida 

para ese tipo de contenido, esta característica puede impedir que se proporcionen estos tipos de recursos a los clientes.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-cacheable-status-codes"></a>Establecer códigos de estado almacenables en caché
**Propósito**: define el conjunto de códigos de estado que puede dar lugar a contenido almacenado en caché.

De forma predeterminada, el almacenamiento en caché solo está habilitado para las respuestas 200 OK.

Defina un conjunto de los códigos de estado deseados, delimitados por espacios.

Información importante:

- Habilite la característica Ignorar no almacenar en caché de origen. Si esa característica no está habilitada, las respuestas 200 OK podrían no almacenarse en caché.
- El conjunto de códigos de estados válidos para esta característica son: 203, 300, 301, 302, 305, 307, 400, 401, 402, 403, 404, 405, 406, 407, 408, 409, 410, 411, 412, 413, 414, 415, 416, 417, 500, 501, 502, 503, 504 y 505.
- Esta característica no se puede usar para deshabilitar el almacenamiento en caché de las respuestas que generan un código de estado 200 OK.

**Comportamiento predeterminado:** el almacenamiento en caché solo está habilitado para las respuestas que generan un código de estado 200 OK.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="set-client-ip-custom-header"></a>Establecer encabezado personalizado de IP de cliente
**Propósito:** agrega un encabezado personalizado que identifica el cliente solicitante por dirección IP de la solicitud.

La opción Header name (Nombre de encabezado) define el nombre del encabezado de solicitud personalizado donde se almacena la dirección IP del cliente.

Esta característica permite al servidor de origen del cliente averiguar las direcciones IP del cliente mediante un encabezado de solicitud personalizado. Si la solicitud se envió desde la memoria caché, no se informará al servidor de origen sobre la dirección IP del cliente. Por lo tanto, se recomienda usar esta característica con ADN o con recursos que no se almacenarán en caché.

Asegúrese de que el nombre de encabezado especificado no coincida con ninguno de los nombres siguientes:

- Nombres de encabezado de solicitud estándar. Encontrará una lista de nombres de encabezado estándar en [RFC 2616](http://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html).
- Nombres de encabezado reservados:
    - forwarded-for
    - host
    - vary
    - via
    - Warning (Advertencia)
    - x-forwarded-for
    - Todos los nombres de encabezado que empiezan por "x-ce" están reservados.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-content-delivery-on-error"></a>Entrega de contenido obsoleto en caso de error
**Propósito:** determina si se entregará el contenido expirado que está almacenado en caché cuando se produzca un error durante la revalidación de caché o al recuperar el contenido solicitado desde el servidor de origen del cliente.

Valor|Resultado
-|-
habilitado|Cuando se produce un error durante una conexión a un servidor de origen, se sirve contenido obsoleto al solicitante.
Disabled|El error del servidor de origen se reenvía al solicitante.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="stale-while-revalidate"></a>Obsoleto durante revalidación
**Propósito**: mejora el rendimiento al permitir que los servidores perimetrales proporcionen contenido obsoleto al solicitante mientras se lleva a cabo la revalidación.

Información importante:

- El comportamiento de esta característica varía según la unidad de tiempo seleccionada.
    - **Unidad de tiempo:** especifique un intervalo de tiempo y seleccione una unidad de tiempo (por ejemplo, segundos, minutos, horas, etc.) para permitir la entrega de contenido obsoleto. Este tipo de configuración permite que la red CDN amplíe el período de tiempo durante el cual puede entregar contenido antes de requerir la validación según la siguiente fórmula:**TTL** + **Contenido obsoleto durante el tiempo de revalidación** 
    - **Off:** seleccione "Off" para exigir la renovación antes de atender una solicitud de contenido obsoleto.
        - No especifique un período de tiempo ya que no es aplicable y se pasará por alto.

**Comportamiento predeterminado:** desactivado. La revalidación debe realizarse antes de que se pueda servir el contenido solicitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth"></a>Autenticación de token
**Propósito**: determina si se aplicará una autenticación basada en token a una solicitud.

Si está habilitada la autenticación basada en token, solo se admitirán las solicitudes que proporcionen un token cifrado y cumplan los requisitos especificados por ese token.

La clave de cifrado que se usa para cifrar y descifrar los valores del token se determina según la clave principal y las opciones de clave de copia de seguridad en la página de autenticación del token. Tenga en cuenta que las claves de cifrado son específicas de la plataforma.

Valor | Resultado
------|---------
Enabled | Protege el contenido solicitado con autenticación basada en token. Solo se admitirán las solicitudes de los clientes que proporcionen un token válido y cumplan los requisitos. Las transacciones de FTP se excluyen de la autenticación basada en token.
Disabled| Restablece el comportamiento predeterminado. El comportamiento predeterminado es permitir la configuración de la autenticación basada en token para determinar si se protegerá una solicitud.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-denial-code"></a>Código de denegación de autorización de token
**Propósito**: determina el tipo de respuesta que se devolverá al usuario cuando se deniegue una solicitud debido a la autenticación basada en token.

Los códigos de respuesta disponibles se enumeran a continuación.

Código de respuesta|Nombre de la respuesta|Descripción
----------------|-----------|--------
301|Movido permanentemente|Este código de estado redirige a los usuarios no autorizados a la dirección URL especificada en el encabezado Ubicación.
302|Encontrado|Este código de estado redirige a los usuarios no autorizados a la dirección URL especificada en el encabezado Ubicación. Este código de estado es el método estándar del sector para llevar a cabo una redirección.
307|Redirección temporal|Este código de estado redirige a los usuarios no autorizados a la dirección URL especificada en el encabezado Ubicación.
401|No autorizado|La combinación de este código de estado con el encabezado de respuesta WWW-Authenticate permite solicitar la autenticación al usuario.
403|Prohibido|Este es el mensaje de estado estándar 403 Prohibido que un usuario no autorizado verá al intentar obtener acceso a contenido protegido.
404|Archivo no encontrado|Este código de estado indica que el cliente HTTP fue capaz de comunicarse con el servidor, pero no se encontró el contenido solicitado.

#### <a name="url-redirection"></a>Redirección de URL

Esta característica admite la redirección de URL a una dirección URL definida por el usuario cuando se configura para devolver un código de estado 3xx. Para especificar esta dirección URL definida por el usuario, siga los pasos siguientes:

1. Seleccione un código de respuesta 3xx para la característica de código de denegación de autenticación de token.
2. Seleccione "Location" en la opción Optional Header Name (Nombre de encabezado opcional).
3. Establezca la opción Optional Header Value (Valor de encabezado opcional) en la dirección URL deseada.

Si una dirección URL no está definida para un código de estado 3xx, se devuelve al usuario la página de respuesta estándar para un código de estado 3xx.

La redirección de URL solo es aplicable a los códigos de respuesta 3xx.

La opción Optional Header Value (Valor de encabezado opcional) admite caracteres alfanuméricos, comillas y espacios.

#### <a name="authentication"></a>Autenticación

Esta característica permite incluir el encabezado WWW-Authenticate al responder a una solicitud no autorizada de contenido protegido por autenticación basada en token. Si el encabezado WWW-Authenticate se ha establecido en "basic" en la configuración, se pedirán las credenciales de cuenta al usuario no autorizado.

Para realizar la configuración anterior, siga los pasos siguientes:

1. Seleccione “401” como código de respuesta para la característica de código de denegación de autenticación de token.
2. Seleccione "WWW-Authenticate" en la opción Optional Header Name (Nombre de encabezado opcional).
3. Establezca la opción Optional Header Value (Valor de encabezado opcional) en "basic".

El encabezado WWW-Authenticate solo es aplicable a códigos de respuesta 401.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-ignore-url-case"></a>Ignorar mayúsculas y minúsculas en URL de autenticación de token
**Propósito:** determina si las comparaciones de URL que realiza la autenticación basada en tokens distinguen mayúsculas de minúsculas.

Los parámetros que se ve afectados por esta característica son:

- ec_url_allow
- ec_ref_allow
- ec_ref_deny

Los valores válidos son:

Valor|Resultado
---|----
habilitado|Hace que el servidor perimetral no tenga en cuenta las mayúsculas y minúsculas al comparar las direcciones URL de los parámetros de autenticación basada en token.
Disabled|Restablece el comportamiento predeterminado. Es el comportamiento predeterminado de la comparación de direcciones URL es que la autenticación basada en token distinga mayúsculas de minúsculas.

**Comportamiento predeterminado**: deshabilitado.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="token-auth-parameter"></a>Parámetro de autenticación de token
**Propósito**: determina si debe cambiarse el nombre del parámetro de cadena de consulta de autenticación basada en token.

Información importante:

- La opción Value (Valor) define el nombre del parámetro de cadena de consulta a través del cual se puede especificar un token.
- La opción Value (Valor) no se puede establecer en "ec_token."
- Asegúrese de que el nombre definido en la opción de valor contenga únicamente caracteres de dirección URL válidos.

Valor|Resultado
----|----
Enabled|La opción Value (Valor) define el nombre del parámetro de cadena de consulta a través del cual se definirá un token.
Disabled|Un token se puede especificar como un parámetro de cadena de consulta no definido en la dirección URL de la solicitud.

**Comportamiento predeterminado**: deshabilitado. Un token se puede especificar como un parámetro de cadena de consulta no definido en la dirección URL de la solicitud.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-redirect"></a>Redirección de URL
**Propósito**: redirige las solicitudes a través del encabezado Ubicación.

Para configurar esta característica hay que establecer las siguientes opciones:

Opción|Descripción
-|-
Código|Seleccione el código de respuesta que se devolverá al solicitante.
Origen y patrón| Esta opción define un patrón de URI de solicitud que identifica el tipo de solicitudes que se pueden redirigir. Solo se redirigirán las solicitudes cuya dirección URL satisfaga ambos criterios siguientes: <br/> <br/> **Origen (o punto de acceso a contenido):** seleccione una ruta de acceso relativa que identifique un servidor de origen. Se trata de la sección "/XXXX/" y el nombre del punto de conexión. <br/> **Origen (patrón):** se debe definir un patrón que identifique las solicitudes por ruta de acceso relativa. Este patrón de expresión regular debe definir una ruta de acceso que comienza directamente después del punto de acceso al contenido seleccionado anteriormente (vea más arriba). <br/> - Asegúrese de que los criterios de URI de solicitud (es decir, el origen y el patrón) definidos anteriormente no entren en conflicto con las condiciones de coincidencia definidas para esta característica. <br/> -Especifique un patrón; si usa un valor en blanco como patrón, se busca la coincidencia con todas las cadenas.
Destino| Defina la dirección URL a la que se redirigirán las solicitudes anteriores. <br/> Construya esta dirección URL dinámicamente mediante: <br/> - Un patrón de expresión regular <br/>- Variables HTTP <br/> Tome los valores capturados en el patrón de origen y sustitúyalos en el patrón de destino mediante $_n_, donde _n_ identifica un valor por el orden en el que se capturó. Por ejemplo, $1 representa el primer valor capturado en el patrón de origen, mientras que $2 representa el segundo valor. <br/> 
Es muy recomendable usar una dirección URL absoluta. El uso de direcciones URL relativas podría redirigir direcciones URL de la red CDN a rutas de acceso no válidas.

**Escenario de ejemplo**

En este ejemplo, se muestra cómo redirigir una dirección URL del servidor perimetral CNAME que se resuelve en esta dirección URL de la red CDN base: http://marketing.azureedge.net/brochures

Las solicitudes aptas se redirigirán a esta dirección URL del servidor perimetral CNAME: http://cdn.mydomain.com/resources

Esta redirección de URL se puede realizar con la siguiente configuración:![](./media/cdn-rules-engine-reference/cdn-rules-engine-redirect.png)

**Puntos clave:**

- La característica Redirección de URL define las direcciones URL de solicitud que se redirigirán. Como resultado, no se necesitan otras condiciones de coincidencia. Aunque la condición de coincidencia se definió como "Always", solo se redirigirán las solicitudes que apunten a la carpeta "brochures" en el origen del cliente "marketing". 
- Todas las solicitudes coincidentes se redirigirán a la dirección URL del servidor perimetral CNAME definida en la opción Destination (Destino). 
    - Escenario de ejemplo 1: 
        - Solicitud de ejemplo (dirección URL de CDN): http://marketing.azureedge.net/brochures/widgets.pdf 
        - URL de solicitud (después de la redirección): http://cdn.mydomain.com/resources/widgets.pdf  
    - Escenario de ejemplo 2: 
        - Solicitud de ejemplo (URL de servidor perimetral CNAME): http://marketing.mydomain.com/brochures/widgets.pdf 
        - URL de solicitud (después de la redirección): http://cdn.mydomain.com/resources/widgets.pdf Escenario de ejemplo
    - Escenario de ejemplo 3: 
        - Solicitud de ejemplo (URL de servidor perimetral CNAME): http://brochures.mydomain.com/campaignA/final/productC.ppt 
        - URL de solicitud (después de la redirección): http://cdn.mydomain.com/resources/campaignA/final/productC.ppt  
- La variable de esquema de solicitud (%{scheme}) se utiliza en la opción Destination (Destino). Esto garantiza que el esquema de la solicitud no cambiará después de la redirección.
- Los segmentos de dirección URL que se capturaron de la solicitud se anexan a la nueva dirección URL a través de "$1".

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="url-rewrite"></a>Reescritura de direcciones URL
**Propósito**: reescribe la dirección URL de la solicitud.

Información importante:

- Para configurar esta característica hay que establecer las siguientes opciones:

Opción|Descripción
-|-
 Origen y patrón | Esta opción define un patrón de URI de solicitud que identifica el tipo de solicitudes que se pueden reescribir. Solo se reescribirán las solicitudes cuya dirección URL satisfaga ambos criterios siguientes: <br/>     - **Origen (o punto de acceso al contenido)**: seleccione una ruta de acceso relativa que identifique un servidor de origen. Se trata de la sección "/XXXX/" y el nombre del punto de conexión. <br/> - **Origen (patrón):** se debe definir un patrón que identifique las solicitudes por ruta de acceso relativa. Este patrón de expresión regular debe definir una ruta de acceso que comienza directamente después del punto de acceso al contenido seleccionado anteriormente (vea más arriba). <br/> Compruebe que los criterios de URI de solicitud (es decir, el origen y el patrón) definidos anteriormente no entren en conflicto con las condiciones de coincidencia definidas para esta característica. Especifique un patrón; si usa un valor en blanco como patrón, se busca la coincidencia con todas las cadenas. 
 Destino  |Defina la dirección URL relativa en la que se sobrescribirán las solicitudes anteriores: <br/>    1. Seleccione un punto de acceso al contenido que identifique un servidor de origen. <br/>    2. Defina el uso de una ruta de acceso relativa: <br/>        - Un patrón de expresión regular <br/>        - Variables HTTP <br/> <br/> Tome los valores capturados en el patrón de origen y sustitúyalos en el patrón de destino mediante $_n_, donde _n_ identifica un valor por el orden en el que se capturó. Por ejemplo, $1 representa el primer valor capturado en el patrón de origen, mientras que $2 representa el segundo valor. 
 Esta característica permite que los servidores perimetrales vuelvan a escribir la dirección URL sin realizar una redirección tradicional. Esto significa que el solicitante recibirá el mismo código de respuesta que si hubiera solicitado la reescritura de la dirección URL.

**Escenario de ejemplo 1**

En este ejemplo, se muestra cómo redirigir una dirección URL del servidor perimetral CNAME que se resuelve en esta dirección URL de la red CDN base: http://marketing.azureedge.net/brochures/

Las solicitudes aptas se redirigirán a esta dirección URL del servidor perimetral CNAME: http://MyOrigin.azureedge.net/resources/

Esta redirección de URL se puede realizar con la siguiente configuración:![](./media/cdn-rules-engine-reference/cdn-rules-engine-rewrite.png)

**Escenario de ejemplo 2**

En este ejemplo, se muestra cómo redirigir una dirección URL del servidor perimetral CNAME de MAYÚSCULAS a minúsculas mediante expresiones regulares.

Esta redirección de URL se puede realizar con la siguiente configuración:![](./media/cdn-rules-engine-reference/cdn-rules-engine-to-lowercase.png)


**Puntos clave:**

- La característica Reescritura de URL define las direcciones URL de solicitud que se reescribirán. Como resultado, no se necesitan otras condiciones de coincidencia. Aunque la condición de coincidencia se definió como "Always", solo se reescribirán las solicitudes que apunten a la carpeta "brochures" en el origen del cliente "marketing".

- Los segmentos de dirección URL que se capturaron de la solicitud se anexan a la nueva dirección URL a través de "$1".

#### <a name="compatibility"></a>Compatibilidad

Esta característica incluye los criterios de coincidencia que deben cumplirse para poder aplicarla a una solicitud. Para evitar configurar criterios de coincidencia que generen conflictos, esta característica es incompatible con las siguientes condiciones de coincidencia:

- Número de sistema autónomo (AS)
- Origen de red CDN
- Dirección IP de cliente
- Origen de cliente
- Esquema de solicitud
- Directorio de ruta de acceso URL
- Extensión de ruta de acceso URL
- Nombre de archivo de ruta de acceso URL
- Literal de ruta de acceso URL
- Regex de ruta de acceso URL
- Carácter comodín de ruta de acceso URL
- Literal de consulta de dirección URL
- Parámetro de consulta de dirección URL
- Regex de consulta de dirección URL
- Carácter comodín de consulta de dirección URL

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

---
### <a name="user-variable"></a>Variable USER
**Propósito:** solo para uso interno.

[Volver arriba](#azure-cdn-rules-engine-features)

</br>

## <a name="next-steps"></a>Pasos siguientes
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Condiciones de coincidencia del motor de reglas](cdn-rules-engine-reference-match-conditions.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)
* [Información general de la red CDN de Azure](cdn-overview.md)
