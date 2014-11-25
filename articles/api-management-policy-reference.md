<properties pageTitle="Azure API Management Policy Reference" metaKeywords="" description="Learn about the policies available to configure API Management." metaCanonical="" services="" documentationCenter="API Management" title="Azure API Management Policy Reference" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Referencia de la directiva de Administración de API de Azure

En este tema se indica una referencia para las siguientes directivas de Administración de API (vista previa). Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en Administración de API][Directivas en Administración de API].

-   [Directivas de restricción de acceso][Directivas de restricción de acceso]

    -   [Cuota de uso][Cuota de uso]: le permite exigir un volumen de llamadas renovables o permanentes o una cuota de ancho de banda.
    -   [Límite de tasa][Límite de tasa]: evita los picos de uso de la API al limitar las llamadas o la tasa de consumo de ancho de banda.
    -   [Restricción de IP de autor de llamada][Restricción de IP de autor de llamada]: filtra (permite/deniega) las llamadas de direcciones IP específicas o de intervalos de direcciones.
-   [Directivas de transformación de contenido][Directivas de transformación de contenido]

    -   [Establecer encabezado HTTP][Establecer encabezado HTTP]: asigna un valor a un encabezado de respuesta o de solicitud existente o agrega un nuevo encabezado de este tipo.
    -   [Convertir XML a JSON][Convertir XML a JSON]: convierte el cuerpo de la solicitud o respuesta de XML a una forma "JSON" o "fiel a XML" de JSON.
    -   [Reemplazar la cadena en el cuerpo][Reemplazar la cadena en el cuerpo]: encuentra una subcadena de solicitud o de respuesta y la reemplaza con una subcadena diferente.
    -   [Establecer el parámetro de cadena de consulta][Establecer el parámetro de cadena de consulta]: agrega, reemplaza el valor o elimina el parámetro de la cadena de consulta de la solicitud.
-   [Directivas de almacenamiento en caché][Directivas de almacenamiento en caché]

    -   [Almacenar en caché][Almacenar en caché]: almacena en caché la respuesta de acuerdo con la configuración del caché especificada.
    -   [Obtener de caché][Obtener de caché]: realiza una búsqueda en caché y devuelve una respuesta en caché válida cuando esté disponible.
-   [Otras directivas][Otras directivas]

    -   [URI de reescritura][URI de reescritura]: convierte una URL de solicitud de su forma pública al la forma esperada por el servicio web.
    -   [Enmascarar URL en el contenido][Enmascarar URL en el contenido]: reescribe (enmascara) vínculos en el cuerpo de respuesta y en el encabezado de la ubicación para que apunten al vínculo equivalente a través del proxy.
    -   [Permitir llamadas entre dominios][Permitir llamadas entre dominios]: permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.
    -   [JSONP][JSONP]: agrega JSON con soporte (JSONP) de relleno a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript.
    -   [CORS][CORS]: agrega soporte (CORS) de uso compartido de recursos entre orígenes a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.

## <a name="access-restriction-policies"> </a> Directivas de restricción de acceso

-   [Cuota de uso][Cuota de uso]: le permite exigir un volumen de llamadas renovables o permanentes o una cuota de ancho de banda.
-   [Límite de tasa][Límite de tasa]: evita los picos de uso de la API al limitar las llamadas o la tasa de consumo de ancho de banda.
-   [Restricción de IP de autor de llamada][Restricción de IP de autor de llamada]: filtra (permite/deniega) las llamadas de direcciones IP específicas o de intervalos de direcciones.

### <a name="usage-quota"> </a> Cuota de uso

**Descripción:**
exige un volumen de llamadas renovables o permanentes o una cuota de ancho de banda.

**Instrucción de la directiva:**

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
        <api name="name" calls="number" bandwidth="kilobytes">
            <operation name="name" calls="number" bandwidth="kilobytes" />
        </api>
    </quota>

**Ejemplo:**

    <policies>
        <inbound>
            <base />
            <quota calls="10000" bandwidth="40000" renewal-period="3600" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Dónde se aplica:**
se utiliza en la sección entrante en el ámbito del producto.

**Cuándo se aplica:**
cuando el uso de las operaciones o de las API deba limitarse para un producto.

**Por qué se aplica y por qué no:**
Para definir límites de uso para un producto basado en el tiempo y en el ancho de banda. Cuando una API alcanza el límite de cuota definido, las siguientes llamadas se rechazan con un mensaje de error. Las cuotas se definen por lo general como el número de mensajes de solicitud que se permiten en el curso de un intervalo de tiempo predefinido y de un límite de ancho de banda.

| Atributo                 | Descripción                                                                                                                                                    |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| quota calls="number"     | El número máximo de llamadas de suscripción que se permiten en el curso del período de renovación (por ejemplo, 10000)                                         |
| bandwidth="kilobytes"    | El número máximo de kilobytes que se permiten para el consumo de este producto, API u operación en el período de renovación especificado (por ejemplo, 40000). |
| renewal-period="seconds" | El período de tiempo en segundos durante el cual se aplica la cuota (por ejemplo, 3600)                                                                        |
| api name="name"          | El nombre de la llamada a la API, para la que se aplica la cuota.                                                                                              |
| calls="number"           | El número máximo de llamadas a la API u operación, que se puede realizar para el período de renovación especificado (por ejemplo, 5000).                       |
| operation name="name"    | El nombre de la operación para la que se aplica la cuota.                                                                                                      |

### <a name="rate-limit"> </a> Límite de tasa

**Descripción:**
detiene los picos de uso de la API al limitar la tasa de solicitudes que llegan a una API y opcionalmente una operación API específica. Cuando se desencadena la directiva, el consumidor recibe el código de estado de respuesta `429 Too Many Requests`.

**Instrucción de la directiva:**

    <rate-limit calls="number" renewal-period="seconds">
        <api name="name" calls="number" renewal-period="seconds">
            <operation name="name" calls="number" renewal-period="seconds" />
        </api>
    </rate-limit>

**Ejemplo:**

    <policies>
        <inbound>
            <base />
            <rate-limit calls="20" renewal-period="90" />
        </inbound>
        <outbound>
            <base />        
        </outbound>
    </policies>

**Dónde se aplica:**
se utiliza en la sección entrante en un ámbito del producto.

| Elemento/atributo        | Descripción                                                                       |
|--------------------------|-----------------------------------------------------------------------------------|
| calls="number"           | número de llamadas permitidas por período de renovación                           |
| renewal-period="seconds" | período de tiempo tras el cual se renueva la cuota de llamadas del límite de tasa |
| api name="name"          | El nombre de la API al que se aplica el límite de tasa                            |
| operation name="name"    | El nombre de la operación al que se aplica el límite de tasa                      |

### <a name="caller-ip-restriction"> </a> Restricción de IP de autor de llamada

**Descripción:**
filtra (permite/deniega) las llamadas de direcciones IP específicas o de intervalos de direcciones.

**Instrucción de la directiva:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Ejemplo:**

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address" />
    </ip-filter>

**Dónde se aplica:**
se utiliza en la sección entrante en cualquier ámbito.

**Cuándo se aplica:**
utilice esta directiva cuando se requiere un control específico sobre quién puede tener acceso al servicio.

**Por qué se aplica y por qué no:**
esta directiva solo se requiere cuando se precisa un estricto control sobre el acceso (por ejemplo, en aquellos servicios con elevados requisitos de seguridad) en cada host o intervalo de hosts.

| Atributo                                  | Descripción                                                                                      |
|-------------------------------------------|--------------------------------------------------------------------------------------------------|
| ip-filter action="allow | forbid"         | Especifica si se deben permitir o no las llamadas para la dirección o direcciones especificadas. |
| address="address"                         | Una o más direcciones IP a las que permitir o denegar el acceso.                                 |
| address-range from="address" to="address" | Un intervalo de direcciones IP a las que permitir o denegar el acceso.                           |

## <a name="content-transformation-policies"> </a> Directivas de transformación de contenido

-   [Establecer encabezado HTTP][Establecer encabezado HTTP]: asigna un valor a un encabezado de respuesta o de solicitud existente o agrega un nuevo encabezado de este tipo.
-   [Convertir XML a JSON][Convertir XML a JSON]: convierte el cuerpo de la solicitud o respuesta de XML a una forma "JSON" o "fiel a XML" de JSON.
-   [Reemplazar la cadena en el cuerpo][Reemplazar la cadena en el cuerpo]: encuentra una subcadena de solicitud o de respuesta y la reemplaza con una subcadena diferente.
-   [Establecer el parámetro de cadena de consulta][Establecer el parámetro de cadena de consulta]: agrega, reemplaza el valor o elimina el parámetro de la cadena de consulta de la solicitud.

### <a name="set-http-header"> </a> Establecer encabezado HTTP

**Descripción:**
asigna un valor a un encabezado de respuesta o de solicitud existente o agrega un nuevo encabezado de este tipo.

Inserta una lista de encabezados HTTP en un mensaje HTTP. Cuando se coloca en un proceso entrante, esta directiva establece los encabezados HTTP para la solicitud que se pasa al servicio de destino. Cuando se coloca en un proceso saliente, esta directiva establece los encabezados HTTP para la respuesta que se está enviando al cliente del proxy.

**Instrucción de la directiva:**

    <set-header name="header name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple headers with the same name add additional value elements-->
    </set-header>

**Ejemplo:**

    <set-header exists-action="override">
        <header name="some value to set" exists-action="override">
        <value>20</value> 
        </header>
    </set-header>

**Dónde se aplica:**
se utiliza en las secciones entrantes y salientes en cualquier ámbito, excepto *Publicador*.

**Cuándo se aplica:**
utilícelo para especificar los parámetros de operación o los valores devueltos de una transacción HTTP.

**Por qué se aplica y por qué no:**
la mayoría de las solicitudes HTTP y muchas respuestas requieren encabezados para especificar parámetros de entrada/respuesta. Por tanto, es probable que esta directiva se aplique a casi todos sus servicios.

<table>
<thead>
<tr>
  <th>Atributo</th>
  <th>Descripci&oacute;n</th>
</tr>
</thead>
<tbody>
<tr>
  <td>reconcile-action=&quot;override|skip|append&quot;</td>
  <td>Especifica la acci&oacute;n que hay que realizar cuando ya se especifica un encabezado. Nota: cuando se establece en &quot;override&quot;, se inscriben varias entradas con los resultados del mismo nombre que se est&aacute;n estableciendo de acuerdo a todas las entradas (que se inscribir&aacute;n varias veces); solo los valores mostrados se establecer&aacute;n en el resultado.</td>
</tr>
<tr>
  <td>header name=&quot;header name&quot;</td>
  <td>Especifica el nombre del encabezado que se va a establecer. Requerido.</td>
</tr>
<tr>
  <td>exists-action=&quot;override | skip | append&quot;</td>
  <td>Especifica la acci&oacute;n que hay que realizar cuando ya se especifica un encabezado.</td>
</tr>
<tr>
  <td>value=&quot;value&quot;</td>
  <td>Especifica el valor del encabezado que se va a establecer.</td>
</tr>
</tbody>
</table>

### <a name="convert-xml-to-json"> </a> Convertir XML en JSON

**Descripción:**
convierte el cuerpo de la solicitud o respuesta de XML a JSON.

**Instrucción de la directiva:**

    <xml-to-json kind="javascript-friendly | direct" apply="always | content-type-xml" consider-accept-header="true | false"/>

**Ejemplo:**

    <policies>
        <inbound>
            <base />
        </inbound>
        <outbound>
            <base />
            <xml-to-json kind="direct" apply="always" consider-accept-header="false" />
        </outbound>
    </policies>

**Dónde se aplica:**
se utiliza en las secciones entrantes o salientes en los ámbitos de la API o de operación.

**Por qué se aplica y por qué no:**
para modernizar las API basadas en servicios web de back-end solo de XML.

| Atributo                              | Descripción                                                                                                                                         |
|---------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| kind="javascript-friendly | direct    | El JSON convertido tiene un formulario idóneo para desarrolladores JavaScript | el JSON convertido refleja la estructura del documento XML original |
| apply= always | content-type-xml      | Convierte siempre | Convierte solo si el encabezado `Content-Type` indica la presencia de XML                                                       |
| consider-accept-header="true | false" | Aplica la conversión basada en el valor del encabezado `Accept` | Convertir siempre                                                                 |

### <a name="replace-string-in-body"> </a> Reemplazar la cadena en el cuerpo

**Descripción:**
encuentra una subcadena en la solicitud o en la respuesta y la reemplaza con una cadena diferente.

**Instrucción de la directiva:**

    <find-and-replace from="what to replace" to="replacement" />

**Ejemplo:**

    <find-and-replace from="notebook" to="laptop" />

**Dónde se aplica:**
se utiliza en las secciones entrantes y salientes en cualquier ámbito.

| Atributo               | Descripción                                                        |
|------------------------|--------------------------------------------------------------------|
| from="what to replace" | La cadena que se va a buscar.                                      |
| to="replacement"       | La cadena para poner en el lugar de la que se ha encontrado antes. |

### <a name="set-query-string-parameter"> </a> Establecer el parámetro de la cadena de consulta

**Descripción:**
agrega, reemplaza el valor o elimina el parámetro de la cadena de consulta de la solicitud.

**Instrucción de la directiva:**

    <set-query-parameter name="param name" exists-action="override | skip | append | delete">
        <value>value</value> <!--for multiple parameters with the same name add additional value elements-->
    </set-query-parameter>

**Ejemplo:**

    <policies>
        <inbound>
            <base />
            <set-query-parameter>
                <parameter name="api-key" exists-action="skip">
                    <value>12345678901</value>
                </parameter>
                <!-- for multiple parameters with the same name add additional value elements -->
            </set-query-parameter>
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Dónde se aplica:**
se puede utilizar en la sección entrante en cualquier ámbito.

**Por que se aplica y por qué no:**
se utiliza para pasar parámetros de consulta previstos por el servicio de back-end que son opcionales o que nunca están presentes en la solicitud.

| Elemento/atributo        | Descripción                                                        |
|--------------------------|--------------------------------------------------------------------|
| name="name"              | una cadena que da nombre al parámetro                              |
| exists-action="override" | reemplaza el valor del parámetro, si está presente en la solicitud |
| exists-action="skip"     | no hace nada si el parámetro está presente en la solicitud         |
| exists-action="append"   | anexa el valor al parámetro de la solicitud existente              |
| exists-action="delete"   | quita el parámetro de la solicitud\*                               |
| value="value"            | establece el valor del parámetro en el elemento encerrado          |

## <a name="caching-policies"> </a> Directivas de almacenamiento en caché

-   [Almacenar en caché][Almacenar en caché]: almacena en caché la respuesta de acuerdo con la configuración del caché especificada.
-   [Obtener de caché][Obtener de caché]: realiza una búsqueda en caché y devuelve una respuesta en caché válida cuando esté disponible.

### <a name="store-to-cache"> </a> Almacenar en caché

**Descripción:**
almacena en caché las respuestas de acuerdo con la configuración del caché especificada. Debe tener una directiva [Obtener de caché][Obtener de caché] correspondiente.

**Instrucción de la directiva:**

    cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />

**Ejemplo:**

    <policies>
        <inbound>
            <base />
              <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
                    <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
                    <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
                    <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
            </cache-lookup>
        </inbound>
        <outbound>
            <base />
                <cache-store duration="3600" caching-mode="cache-on" />
        </outbound>
    </policies>

**Dónde se aplica:**
puede estar presente en la sección saliente en uno o ambos ámbitos de API y de operación.

**Cuándo se aplica:**
debe aplicarse en aquellos casos en los que el contenido de la respuesta permanece estático en un período de tiempo.

**Por qué se aplica y por qué no:**
el almacenamiento en caché de las respuestas reduce el ancho de banda y los requisitos de procesamiento impuestos en el servidor web de back-end y reduce la latencia percibida por los consumidores de la API.

| Elemento/atributo       | Descripción                                                                                                                                                                                           |
|-------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| seconds                 | Duración de las entradas almacenadas en caché (en segundos, el valor predeterminado es 3600)                                                                                                          |
| cache-on | do-not-cache | Las respuestas están almacenadas en caché | Las respuestas no están almacenadas en caché y los encabezados relacionados con el caché se establecen para prohibir el almacenamiento en caché de bajada |

### <a name="get-from-cache"> </a> Obtener de caché

**Descripción:**
realiza una búsqueda de caché y devuelve una respuesta válida en caché, si está disponible. Responde de manera apropiada a las solicitudes de validación de caché desde los consumidores de la API. Debe tener una directiva [Almacenar en caché][Almacenar en caché] correspondiente.

**Instrucción de la directiva:**

    <cache-lookup vary-by-developer=*"true | false"* vary-by-developer-groups=*"true | false"* downstream-caching-type=*"none | private | public"*>
        <vary-by-header>Accept</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>Accept-Charset</vary-by-header> <!-- should be present in most cases -->
        <vary-by-header>header name</vary-by-header> <!-- optional, can repeated several times -->
        <vary-by-query-parameter>parameter name</vary-by-query-parameter> <!-- optional, can repeated several times -->
    </cache-lookup>

**Ejemplo:**

    <policies>
        <inbound>
            <base />
            <cache-lookup vary-by-developer="false" vary-by-developer-groups="false" downstream-caching-type="none">
                <vary-by-query-parameter>version</vary-by-query-parameter>
                <vary-by-header>Accept</vary-by-header>
                <vary-by-header>Accept-Charset</vary-by-header>
            </cache-lookup>         
        </inbound>
        <outbound>
            <cache-store duration="seconds" caching-mode="cache-on | do-not-cache" />
            <base />        
        </outbound>
    </policies>

**Dónde se aplica:**
puede estar presente en la sección entrante en uno o ambos ámbitos de API y de operación.

**Cuándo se aplica:**
debe aplicarse en aquellos casos en los que el contenido de la respuesta permanece estático en un período de tiempo.

**Por qué se aplica y por qué no:**
el almacenamiento en caché de las respuestas reduce el ancho de banda y los requisitos de procesamiento impuestos en el servidor web de back-end y reduce la latencia percibida por los consumidores de la API.

| Elemento/atributo                                 | Descripción                                                                                                                                                                                                                                                               |
|---------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| vary-by-developer="true | false"                  | Se establece en \*true\* para iniciar el almacenamiento en caché de las respuestas por clave de desarrollador; de manera predeterminada se establece en \*false\*                                                                                                         |
| vary-by-developer-groups="true | false"           | Se establece en \*true\* para iniciar el almacenamiento en caché de las respuestas por rol de usuario; de manera predeterminada se establece en \*false\*                                                                                                                 |
| downstream-caching-type="none | private | public" | \*none\* - no se permite el almacenamiento en caché de bajada; se establece de forma predeterminada | \*private\* - se permite el almacenamiento en caché privado de bajada | \*public\* - se permite el almacenamiento en caché de bajada privado y compartido.          |
| vary-by-header: "Accept"                          | Inicia el almacenamiento en caché de las respuestas por valor del encabezado `Accept`                                                                                                                                                                                     |
| vary-by-header: Accept-Charset"                   | Inicia el almacenamiento en caché de las respuestas por valor del encabezado `Accept-Charset`                                                                                                                                                                             |
| vary-by-header: "header name"                     | Inicia el almacenamiento en caché de las respuestas por valor del encabezado especificado, por ejemplo, `Accept | Accept-Charset | Accept-Encoding | Accept-Language | Authorization | Expect | From | Host | If-Match`                                                   |
| vary-by-query-parameter: "parameter name"         | Inicia el almacenamiento en caché de las respuestas por valor de los parámetros de consulta especificados. Permite introducir uno o varios parámetros. Utilice el punto y coma como separador. Si no se especifica ninguno, se utilizan todos los parámetros de consulta. |

## <a name="other-policies"> </a> Otras directivas

-   [URI de reescritura][URI de reescritura]: convierte una URL de solicitud de su forma pública al la forma esperada por el servicio web.
-   [Enmascarar URL en el contenido][Enmascarar URL en el contenido]: reescribe (enmascara) vínculos en el cuerpo de respuesta y en el encabezado de la ubicación para que apunten al vínculo equivalente a través del proxy.
-   [Permitir llamadas entre dominios][Permitir llamadas entre dominios]: permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.
-   [JSONP][JSONP]: agrega JSON con soporte (JSONP) de relleno a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript.
-   [CORS][CORS]: agrega soporte (CORS) de uso compartido de recursos entre orígenes a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.

### <a name="rewrite-uri"> </a> URI de reescritura

**Descripción:**
convierte una URL de solicitud de su forma pública a la forma esperada por el servicio web.

**URL pública:** <http://api.example.com/storenumber/ordernumber>

**URL de solicitud:** <http://api.example.com/v2/US/hardware/storenumber&ordernumber?City&State>.

No agregue ningún parámetro de ruta de plantilla adicional en la URL de reescritura. Solo puede agregar parámetros de cadena de consulta.

**Instrucción de la directiva:**

    <rewrite-uri template="uri template" />

**Ejemplo:**

    <policies>
        <inbound>
            <base />
            <rewrite-uri template="/v2/US/hardware/{storenumber}&{ordernumber}?City=city&State=state" />
        </inbound>
        <outbound>
            <base />
        </outbound>
    </policies>

**Dónde se aplica:**
en la sección entrante en el ámbito de operación únicamente.

**Cuándo se aplica:**
se utiliza cuando una URL apta para el explorador o para individuos debe transformarse en el formato de URL esperado por el servicio web.

**Por qué se aplica y por qué no:**
solo hay que aplicarla al exponer un formato URL alternativo. Las URL limpias, las URL RESTful, las URL fáciles de usar o las URL aptas para SEO son URL puramente estructurales que no contienen una cadena de consulta y por el contrario contiene solo la ruta del recurso (después del esquema y de la autoridad). Este procedimiento se realiza con fines estéticos, de usabilidad o de optimización del motor de búsqueda (SEO).

| Atributo                | Descripción                                                             |
|-------------------------|-------------------------------------------------------------------------|
| template="uri template" | URL del servicio web real con cualquier parámetro de cadena de consulta |

### <a name="mask-urls-in-content"> </a> Enmascarar URL en el contenido

**Descripción:**
reescribe (enmascara) vínculos en el cuerpo de respuesta y en el encabezado de la ubicación para que apunten al vínculo equivalente a través del proxy.

**Instrucción de la directiva:**

    <redirect-body-urls />

**Ejemplo:**

    <redirect-body-urls />

**Dónde se aplica:**
se aplica en los ámbitos *API* y *Operación*. Se puede aplicar en la sección entrante o en la saliente.

### <a name="allow-cross-domain-calls"> </a> Permitir llamadas entre dominios

**Descripción:**
permite que la API sea accesible desde los clientes basados en explorador de Adobe Flash y Microsoft Silverlight.

**Instrucción de la directiva:**

    <cross-domain />

**Ejemplo:**

    <cross-domain />

**Dónde se aplica:**
se utiliza en la sección entrante en el ámbito *Publicadores*.

### <a name="jsonp"> </a> JSONP

**Descripción:**
agrega JSON con soporte (JSONP) de relleno a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador de JavaScript. JSONP es un método utilizado en los programas JavaScript para solicitar datos desde un servidor en un dominio diferente. JSONP sortea la limitación exigida por la mayoría de los exploradores web donde el acceso a las páginas web debe estar en el mismo dominio.

**Instrucción de la directiva:**

    <jsonp callback-parameter-name="callback function name" />

**Ejemplo:**

    <jsonp callback-parameter-name="cb" />

Si llama al método sin el parámetro de devolución de llamada `?cb=XXX`, devolverá un JSON sin formato (sin un envoltorio de llamada de función).

Si agrega el parámetro de devolución de llamada `?cb=XXX`, devolverá un resultado JSONP, envolviendo los resultados JSON originales en torno a la función de devolución de llamada como `XXX('<json result goes here>')`;

**Dónde se aplica:**
se utiliza únicamente en la sección saliente.

**Cuándo se aplica:**
para solicitar datos desde un servidor en un dominio diferente.

| Atributo                | Descripción                                                                                                                |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------|
| callback-parameter-name | La llamada de función de JavaScript entre dominios prefijada con el nombre de dominio completo en donde reside la función. |

### <a name="cors"> </a> CORS

**Descripción:**
agrega soporte (CORS) de uso compartido de recursos entre orígenes a una operación o a una API para permitir llamadas entre dominios desde clientes basados en explorador.

CORS permite a un explorador y a un servidor interactuar y determinar si se permiten o no solicitudes específicas entre orígenes (por ejemplo, llamadas XMLHttpRequests realizadas desde JavaScript en una página web a otros dominios). Esto permite más flexibilidad que si solo se permiten solicitudes del mismo origen, pero es más seguro que permitir todas las solicitudes entre orígenes.

**Instrucción de la directiva:**

     <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>URI</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
     </cors>

**Ejemplo:**

    <cors>
        <allowed-origins>
            <origin>*</origin> <!-- allow any -->
            <!-- OR a list of one or more specific URIs (case-sensitive) -->
            <origin>http://contoso.com:81</origin> <!-- URI must include scheme, host, and port. If port is omitted, 80 is assumed for http and 443 is assumed for https. -->
        </allowed-origins>
    </cors>

**Dónde se aplica:**
se utiliza en la sección entrante y únicamente en los ámbitos *API* y *Operación*.

| Atributo             | Descripción                                                                                                                      |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------|
| <origin>\*</origin>  | Se permite cualquiera O una lista de uno o varios URI específicos                                                                |
| <origin>URI</origin> | El URI debe incluir un esquema, un host y un puerto. Si el puerto se omite, se asume el puerto 80 para http y el 443 para https. |

  [Directivas en Administración de API]: ../api-management-howto-policies
  [Directivas de restricción de acceso]: #access-restriction-policies
  [Cuota de uso]: #usage-quota
  [Límite de tasa]: #rate-limit
  [Restricción de IP de autor de llamada]: #caller-ip-restriction
  [Directivas de transformación de contenido]: #content-transformation-policies
  [Establecer encabezado HTTP]: #set-http-header
  [Convertir XML a JSON]: #convert-xml-to-json
  [Reemplazar la cadena en el cuerpo]: #replace-string-in-body
  [Establecer el parámetro de cadena de consulta]: #set-query-string-parameter
  [Directivas de almacenamiento en caché]: #caching-policies
  [Almacenar en caché]: #store-to-cache
  [Obtener de caché]: #get-from-cache
  [Otras directivas]: #other-policies
  [URI de reescritura]: #rewrite-uri
  [Enmascarar URL en el contenido]: #mask-urls-in-content
  [Permitir llamadas entre dominios]: #allow-cross-domain-calls
  [JSONP]: #jsonp
  [CORS]: #cors
