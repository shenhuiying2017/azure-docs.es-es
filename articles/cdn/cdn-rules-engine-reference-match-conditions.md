---
title: Condiciones de coincidencia del motor de reglas de Azure CDN | Microsoft Docs
description: "Documentación de referencia sobre las condiciones de coincidencia del motor de reglas de Azure Content Delivery Network"
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
ms.date: 12/21/2017
ms.author: rli
ms.openlocfilehash: 9986e654b076df099e3912f9da628728723b5c3d
ms.sourcegitcommit: 71fa59e97b01b65f25bcae318d834358fea5224a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2018
---
# <a name="match-conditions-for-the-azure-cdn-rules-engine"></a>Condiciones de coincidencia del motor de reglas de Azure CDN
En este artículo se muestran descripciones detalladas de las condiciones de coincidencia disponibles para el [motor de reglas](cdn-rules-engine.md) de Azure Content Delivery Network (CDN).

La segunda parte de una regla es la condición de coincidencia. Una condición de coincidencia identifica tipos específicos de solicitudes para los que se ejecutará un conjunto de características.

Por ejemplo, puede usar una condición de coincidencia para:
- Filtrar las solicitudes de contenido de una ubicación concreta.
- Filtrar las solicitudes generadas a partir de una dirección IP o país específicos.
- Filtrar las solicitudes por la información de encabezado.

## <a name="always-match-condition"></a>Condición de coincidencia Siempre

La condición de coincidencia Siempre aplica un conjunto predeterminado de características a todas las solicitudes.

NOMBRE | Propósito
-----|--------
[Siempre](#always) | Se aplica un conjunto predeterminado de características a todas las solicitudes.

## <a name="device-match-condition"></a>Condición de coincidencia Dispositivo

La condición de coincidencia Dispositivo identifica solicitudes realizadas desde un dispositivo móvil en función de sus propiedades.  

NOMBRE | Propósito
-----|--------
[Dispositivo](#device) | Identifica solicitudes realizadas desde un dispositivo móvil en función de sus propiedades.

## <a name="location-match-conditions"></a>Condiciones de coincidencia Ubicación

Las condiciones de coincidencia Ubicación identifican solicitudes en función de la ubicación del solicitante.

NOMBRE | Propósito
-----|--------
[Número de sistema autónomo (AS)](#as-number) | Identifica solicitudes que se originan en una red determinada.
[País](#country) | Identifica solicitudes que se originan en países determinados.

## <a name="origin-match-conditions"></a>Condiciones de coincidencia Origen

Las condiciones de coincidencia Origen identifican solicitudes que señalan al servidor de origen de un cliente o de almacenamiento de Content Delivery Network.

NOMBRE | Propósito
-----|--------
[Origen de red CDN](#cdn-origin) | Identifica las solicitudes de contenido almacenado en el almacenamiento de Content Delivery Network.
[Origen de cliente](#customer-origin) | Identifica solicitudes de contenido almacenado en el servidor de origen de un cliente específico.

## <a name="request-match-conditions"></a>Condiciones de coincidencia Solicitud

Las condiciones de coincidencia Solicitud identifican solicitudes en función de sus propiedades.

NOMBRE | Propósito
-----|--------
[Dirección IP de cliente](#client-ip-address) | Identifica solicitudes que se originan en una dirección IP determinada.
[Parámetro de cookie](#cookie-parameter) | Busca el valor especificado en las cookies asociadas a cada solicitud.
[Regex de parámetro de cookie](#cookie-parameter-regex) | Busca la expresión regular especificada en las cookies asociadas a cada solicitud.
[Cname perimetral](#edge-cname) | Identifica solicitudes que señalan a un CNAME perimetral específico .
[Dominio de referencia](#referring-domain) | Identifica solicitudes a las que se haga referencia en los nombres de host especificados.
[Literal de encabezado de solicitud](#request-header-literal) | Identifica solicitudes que contienen el encabezado que se especifique en un valor concreto.
[Regex de encabezado de solicitud](#request-header-regex) | Identifica solicitudes que contienen el encabezado que se especifique establecido en la expresión regular especificada.
[Carácter comodín de encabezado de solicitud](#request-header-wildcard) | Identifica solicitudes que contienen el encabezado que se especifique establecido en un valor que coincide con el patrón especificado.
[Método de solicitud](#request-method) | Identifica solicitudes por su método HTTP.
[Esquema de solicitud](#request-scheme) | Identifica solicitudes por su protocolo HTTP.

## <a name="url-match-conditions"></a>Condiciones de coincidencia URL

Las condiciones de coincidencia URL identifican solicitudes en función de sus direcciones URL.

NOMBRE | Propósito
-----|--------
Directorio de ruta de acceso URL | Identifica solicitudes por su ruta de acceso relativa.
Extensión de ruta de acceso URL | Identifica solicitudes por su extensión de nombre de archivo.
Nombre de archivo de ruta de acceso URL | Identifica solicitudes por su nombre de archivo.
Literal de ruta de acceso URL | Compara la ruta de acceso relativa de una solicitud con el valor especificado.
Regex de ruta de acceso URL | Compara la ruta de acceso relativa de una solicitud con la expresión regular especificada.
Carácter comodín de ruta de acceso URL | Compara la ruta de acceso relativa de una solicitud con el patrón especificado.
Literal de consulta de dirección URL | Compara la cadena de consulta de una solicitud con el valor especificado.
Parámetro de consulta de dirección URL | Identifica solicitudes que contienen el parámetro de cadena de consulta que se especifique establecido en un valor que coincide con el patrón especificado.
Regex de consulta de dirección URL | Identifica solicitudes que contienen el parámetro de cadena de consulta que se especifique establecido en un valor que coincide con la expresión regular especificada.
Carácter comodín de consulta de dirección URL | Compara los valores especificados con la cadena de consulta de la solicitud.


## <a name="reference-for-rules-engine-match-conditions"></a>Referencia de las condiciones de coincidencia del motor de reglas

---
### <a name="always"></a>Siempre

La condición de coincidencia Siempre aplica un conjunto predeterminado de características a todas las solicitudes.

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="as-number"></a>Número de sistema autónomo (AS) 
La red del número de sistema autónomo se define por el número de sistema autónomo (ASN). Se proporciona una opción para indicar si esta condición se cumplirá cuando la red de un cliente "coincida" o "no coincida" con el ASN especificado.

Información importante:
- Especifique varios números de sistema autónomo mediante la delimitación de cada uno con un espacio. Por ejemplo, 64514 64515 coincide con las solicitudes que llegan desde 64514 o 64515.
- Algunas solicitudes no pueden devolver un ASN válido. Un signo de interrogación (?) coincidirá con las solicitudes para las que no se pueda determinar un ASN válido.
- Debe especificar el ASN completo para la red deseada. No se encontrarán coincidencias con valores parciales.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cdn-origin"></a>Origen de red CDN
La condición de coincidencia Origen de red CDN se cumple cuando lo hacen ambas condiciones siguientes:
- Se solicita el contenido del almacenamiento de Content Delivery Network.
- El URI de la solicitud usa el punto de acceso al contenido (por ejemplo, /000001) que se define en esta condición de coincidencia.
  - Dirección URL de Content Delivery Network: el URI de la solicitud debe contener el punto de acceso al contenido seleccionado.
  - Dirección URL de CNAME perimetral: la configuración de CNAME perimetral correspondiente debe apuntar al punto de acceso al contenido seleccionado.
  
Información importante:
 - El punto de acceso al contenido identifica el servicio que debe atender el contenido solicitado.
 - No use una instrucción AND IF para combinar determinadas condiciones de coincidencia. Por ejemplo, la combinación de una condición de coincidencia Origen de red CDN con una Origen de cliente crearía un patrón de coincidencia que no se puede cumplir. Por esta razón, no se pueden combinar dos condiciones de coincidencia Origen de red CDN mediante una instrucción AND IF.

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="client-ip-address"></a>Dirección IP de cliente
Se proporciona una opción para indicar si la condición Dirección IP de cliente se cumplirá cuando la dirección IP de un cliente "coincida" o "no coincida" con la especificada.

Información importante:
- Asegúrese de usar la notación CIDR.
- Especifique varias direcciones IP o bloques de direcciones IP al delimitarlas individualmente con un espacio.
  - **Ejemplo IPv4**: 1.2.3.4 10.20.30.40 coincide con las solicitudes que llegan desde 1.2.3.4 o 10.20.30.40.
  - **Ejemplo IPv6**: 1:2:3:4:5:6:7:8 10:20:30:40:50:60:70:80 coincide con las solicitudes que llegan desde 1:2:3:4:5:6:7:8 o 10:20:30:40:50:60:70:80.
- La sintaxis de un bloque de direcciones IP es la dirección IP de base seguida por una barra diagonal y el tamaño del prefijo.
  - **Ejemplo IPv4**: 5.5.5.64/26 coincide con las solicitudes que llegan desde 5.5.5.64 o 5.5.5.127.
  - **Ejemplo de IPv6**: 1:2:3:/48 coincide con todas las solicitudes que llegan desde 1:2:3:0:0:0:0:0 a través de 1:2:3:ffff:ffff:ffff:ffff:ffff.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter"></a>Parámetro de cookie
La opción **Matches**/**Does Not Match** (Coincide/No coincide) determina las condiciones para que se cumpla la condición de coincidencia Parámetro de cookie.
- **Matches** (Coincide): requiere que una solicitud contenga la cookie especificada con un valor que coincida con al menos uno de los que se definen en esta condición de coincidencia.
- **Does Not Match** (No coincide): requiere que la solicitud cumpla cualquiera de los siguientes criterios:
  - No contenga la cookie especificada.
  - Contenga la cookie especificada, pero que su valor no coincida con ninguno de los que se definen en esta condición de coincidencia.
  
Información importante:
- Nombre de la cookie: 
  - No se admiten caracteres especiales, como el asterisco, al especificar un nombre de cookie. Esto significa que para la comparación solo son aptas las coincidencias exactas de nombre de cookie.
  - Solo se puede especificar un nombre de cookie por instancia de esta condición de coincidencia.
  - Las comparaciones de nombres de cookie distinguen entre mayúsculas y minúsculas.
- Valor de la cookie: 
  - Especifique varios valores de cookie mediante la delimitación de cada uno con un espacio.
  - Para los valores de cookie se pueden utilizar caracteres especiales. 
  - Si no se ha especificado un carácter comodín, solo una coincidencia exacta cumplirá esta condición de coincidencia. Por ejemplo, especificar que "Value" coincida con "Value", pero no "Value1" o "Value2".
  - La opción **Ignore Case** (No distinguir mayúsculas y minúsculas) determina si en la comparación se distinguirán mayúsculas y minúsculas en el valor de la cookie de la solicitud.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="cookie-parameter-regex"></a>Regex de parámetro de cookie
La condición de coincidencia Regex de parámetro de cookie define el valor y el nombre de la cookie. Puede utilizar expresiones regulares para definir el valor de cookie. 

La opción **Matches**/**Does Not Match** (Coincide/No coincide) determina las condiciones para que se cumpla esta condición de coincidencia.
- **Matches** (Coincide): identifica solicitudes que contienen la cookie determinada con un valor que coincida con la expresión regular especificada.
- **Does Not Match** (No coincide): requiere que la solicitud cumpla cualquiera de los siguientes criterios:
  - No contenga la cookie especificada.
  - Contenga la cookie especificada, pero que su valor no coincida con la expresión regular especificada.
  
Información importante:
- Nombre de la cookie: 
  - No se admiten expresiones regulares ni caracteres especiales, como el asterisco, al especificar un nombre de cookie. Esto significa que para la comparación solo son aptas las coincidencias exactas de nombre de cookie.
  - Solo se puede especificar un nombre de cookie por instancia de esta condición de coincidencia.
  - Las comparaciones de nombres de cookie distinguen entre mayúsculas y minúsculas.
- Valor de la cookie: 
  - Para los valores de cookie se pueden utilizar expresiones regulares.
  - La opción **Ignore Case** (No distinguir mayúsculas y minúsculas) determina si en la comparación se distinguirán mayúsculas y minúsculas en el valor de la cookie de la solicitud.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

--- 
### <a name="country"></a>País
Puede especificar un país mediante el código de país. Se proporciona una opción para indicar si esta condición se cumplirá cuando el país donde se origina una solicitud "coincida" o "no coincida" con los valores especificados.

Información importante:
- Especifique varios códigos de país mediante la delimitación de cada uno con un espacio.
- No se admiten caracteres comodín al especificar un código de país.
- Los códigos de país "EU" y "AP" no incluyen todas las direcciones IP de esas regiones.
- Algunas solicitudes pueden no devolver un código de país válido. Un signo de interrogación (?) coincidirá con las solicitudes para las que no se pueda determinar un código de país válido.
- Los códigos de país distinguen entre mayúsculas y minúsculas.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="customer-origin"></a>Origen de cliente

Información importante: 
- La condición de coincidencia Origen de cliente se cumplirá independientemente de si se solicita contenido mediante una dirección URL de Content Delivery Network o de CNAME perimetral que apunte al origen del cliente seleccionado.
- No se puede eliminar de la página Origen de cliente una configuración de origen de cliente a la que haga referencia una regla. Antes de intentar eliminar una configuración de origen de cliente, asegúrese de que las siguientes configuraciones de no hacen referencia a ella:
  - Una condición de coincidencia Origen de cliente
  - Una configuración de CNAME perimetral
- No use una instrucción AND IF para combinar determinadas condiciones de coincidencia. Por ejemplo, la combinación de una condición de coincidencia Origen de cliente con una Origen de red CDN crearía un patrón de coincidencia que no se puede cumplir. Por esta razón, no se pueden combinar dos condiciones de coincidencia Origen de cliente mediante una instrucción AND IF.

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="device"></a>Dispositivo

La condición de coincidencia Dispositivo identifica solicitudes realizadas desde un dispositivo móvil en función de sus propiedades. La detección de dispositivos móviles se realiza a través de [WURFL](http://wurfl.sourceforge.net/). En la tabla siguiente se enumeran las funcionalidades WURFL y sus variables para el motor de reglas Content Delivery Network.
<br>
> [!NOTE] 
> Las variables siguientes se admiten en las funciones **Modify Client Request Header** (Modificar encabezado de solicitud de cliente) y **Modify Client Response Header** (Modificar encabezado de respuesta de cliente).

Capacidad | Variable | DESCRIPCIÓN | Valores de ejemplo
-----------|----------|-------------|----------------
Nombre de la marca | %{wurfl_cap_brand_name} | Una cadena que indica el nombre de marca del dispositivo. | Samsung
Sistema operativo del dispositivo | %{wurfl_cap_device_os} | Una cadena que indica el sistema operativo instalado en el dispositivo. | IOS
Versión del sistema operativo del dispositivo | %{wurfl_cap_device_os_version} | Una cadena que indica el número de versión del sistema operativo instalado en el dispositivo. | 1.0.1
Orientación dual | %{wurfl_cap_dual_orientation} | Un valor booleano que indica si el dispositivo admite la orientación dual. | true
DTD preferido de HTML | %{wurfl_cap_html_preferred_dtd} | Una cadena que indica la definición de tipo de documento (DTD) preferido del dispositivo móvil para el contenido HTML. | None<br/>xhtml_basic<br/>html5
Imagen de inclusión | %{wurfl_cap_image_inlining} | Un valor booleano que indica si el dispositivo admite imágenes con codificación Base64. | false
Es Android | %{wurfl_vcap_is_android} | Un valor booleano que indica si el dispositivo usa el sistema operativo Android. | true
Es IOS | %{wurfl_vcap_is_ios} | Un valor booleano que indica si el dispositivo usa iOS. | false
Es Smart TV | %{wurfl_cap_is_smarttv} | Un valor booleano que indica si el dispositivo es una Smart TV. | false
Es smartphone | %{wurfl_vcap_is_smartphone} | Un valor booleano que indica si el dispositivo es un smartphone. | true
Es tablet | %{wurfl_cap_is_tablet} | Un valor booleano que indica si el dispositivo es una tablet. Esta es una descripción independiente del SO. | true
Es dispositivo inalámbrico | %{wurfl_cap_is_wireless_device} | Un valor booleano que indica si el dispositivo se considera un dispositivo inalámbrico. | true
Nombre de marca | %{wurfl_cap_marketing_name} | Una cadena que indica el nombre de marca del dispositivo. | BlackBerry 8100 Pearl
Explorador móvil | %{wurfl_cap_mobile_browser} | Una cadena que indica el explorador que se usa para solicitar el contenido del dispositivo. | Chrome
Versión del explorador móvil | %{wurfl_cap_mobile_browser_version} | Una cadena que indica la versión del explorador que se usa para solicitar el contenido del dispositivo. | 31
Nombre del modelo | %{wurfl_cap_model_name} | Una cadena que indica el nombre del modelo del dispositivo. | s3
Descarga progresiva | %{wurfl_cap_progressive_download} | Un valor booleano que indica si el dispositivo admite la reproducción de audio y vídeo durante la descarga. | true
Fecha de lanzamiento | %{wurfl_cap_release_date} | Una cadena que indica el año y el mes en el que se agregó el dispositivo a la base de datos WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura de resolución | %{wurfl_cap_resolution_height} | Un entero que indica la altura del dispositivo en píxeles. | 768
Anchura de resolución | %{wurfl_cap_resolution_width} | Un entero que indica la anchura del dispositivo en píxeles. | 1024

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="edge-cname"></a>Cname perimetral
Información importante: 
- La lista de CNAME perimetrales se limita a los que se han configurado en la página de CNAME perimetral que se corresponde con la plataforma donde se configura el motor de reglas de HTTP.
- Antes de eliminar una configuración de CNAME perimetral, asegúrese de que no hace referencia a ella ninguna condición de coincidencia CNAME perimetral. No se pueden eliminar de la página de CNAME perimetral las configuraciones de CNAME perimetral que se hayan definido en una regla. 
- No use una instrucción AND IF para combinar determinadas condiciones de coincidencia. Por ejemplo, la combinación de una condición de coincidencia CNAME perimetral con una Origen de cliente crearía un patrón de coincidencia que no se puede cumplir. Por esta razón, no se pueden combinar dos condiciones de coincidencia CNAME perimetral mediante una instrucción AND IF.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="referring-domain"></a>Dominio de referencia
El nombre de host asociado con el origen de referencia mediante el cual se solicitó contenido determina si se cumple la condición Dominio de referencia. Se proporciona una opción para indicar si esta condición se cumplirá cuando el nombre de host de referencia "coincida" o "no coincida" con los valores especificados.

Información importante:
- Especifique varios nombres de host mediante la delimitación de cada uno con un espacio.
- Esta condición de coincidencia admite caracteres especiales.
- Si el valor especificado no contiene asteriscos, debe ser una coincidencia exacta para el nombre de host del sitio de referencia. Por ejemplo, especificar "mydomain.com" no coincidiría con "www.mydomain.com."
- La opción **Ignore Case** (No distinguir mayúsculas y minúsculas) determina si en la comparación se distinguirán mayúsculas y minúsculas.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-literal"></a>Literal de encabezado de solicitud
La opción **Matches**/**Does Not Match** (Coincide/No coincide) determina las condiciones para que se cumpla esta condición de coincidencia.
- **Matches** (Coincide): requiere que la solicitud para contenga el encabezado especificado. Su valor debe coincidir con el que se define en esta condición de coincidencia.
- **Does Not Match** (No coincide): requiere que la solicitud cumpla cualquiera de los siguientes criterios:
  - No contenga el encabezado especificado.
  - Contenga el encabezado especificado, pero que su valor no coincida con el que se define en esta condición de coincidencia.
  
Información importante:
- Las comparaciones de nombres de encabezado distinguen siempre entre mayúsculas y minúsculas. La opción **Ingore Case** (No distinguir mayúsculas y minúsculas) determina si se distinguen las mayúsculas y las minúsculas del encabezado en las comparaciones.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---  
### <a name="request-header-regex"></a>Regex de encabezado de solicitud
La opción **Matches**/**Does Not Match** (Coincide/No coincide) determina las condiciones para que se cumpla la condición de coincidencia Regex de encabezado de solicitud.
- **Matches** (Coincide): requiere que la solicitud para contenga el encabezado especificado. Su valor debe coincidir con el patrón que se define en la expresión regular especificada.
- **Does Not Match** (No coincide): requiere que la solicitud cumpla cualquiera de los siguientes criterios:
  - No contenga el encabezado especificado.
  - Contenga el encabezado especificado, pero que su valor no coincida con la expresión regular especificada.

Información importante:
- Nombre de encabezado: 
  - Las comparaciones de nombres de encabezado distinguen entre mayúsculas y minúsculas.
  - Los espacios en el nombre del encabezado deben reemplazarse por "%20". 
- Valor de encabezado: 
  - Para los valores de encabezado se pueden utilizar expresiones regulares.
  - La opción **Ingore Case** (No distinguir mayúsculas y minúsculas) determina si se distinguen las mayúsculas y las minúsculas del encabezado en las comparaciones.
  - Esta condición la cumplen únicamente las coincidencias exactas de encabezado con al menos uno de los patrones especificados.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna 

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-header-wildcard"></a>Carácter comodín de encabezado de solicitud
La opción **Matches**/**Does Not Match** (Coincide/No coincide) determina las condiciones para que se cumpla la condición de coincidencia Carácter comodín de encabezado de solicitud.
- **Matches** (Coincide): requiere que la solicitud para contenga el encabezado especificado. Su valor debe coincidir con al menos uno de los valores que se definen en esta condición de coincidencia.
- **Does Not Match** (No coincide): requiere que la solicitud cumpla cualquiera de los siguientes criterios:
  - No contenga el encabezado especificado.
  - Contenga el encabezado especificado, pero que su valor no coincida con ninguno de los valores especificados.
  
Información importante:
- Nombre de encabezado: 
  - Las comparaciones de nombres de encabezado distinguen entre mayúsculas y minúsculas.
  - Los espacios en el nombre del encabezado deben reemplazarse por "%20". También puede utilizar este valor para especificar espacios en un valor de encabezado.
- Valor de encabezado: 
  - Para los valores de encabezado se pueden utilizar caracteres especiales.
  - La opción **Ingore Case** (No distinguir mayúsculas y minúsculas) determina si se distinguen las mayúsculas y las minúsculas del encabezado en las comparaciones.
  - Esta condición la cumplen únicamente las coincidencias exactas de encabezado con al menos uno de los patrones especificados.
  - Especifique varios valores de encabezado mediante la delimitación de cada uno con un espacio.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-method"></a>Método de solicitud
Solo los recursos que se solicitan mediante el método de solicitud seleccionado cumplirán la condición Método de solicitud. Los métodos de solicitud disponibles son:
- GET
- HEAD 
- POST 
- OPCIONES 
- PUT 
- DELETE 
- TRACE 
- CONNECT 

Información importante:
- De forma predeterminada, solo el método de solicitud GET genera contenido almacenado en caché en la red. Los demás métodos de solicitud los procesa el proxy mediante la red.
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

---
### <a name="request-scheme"></a>Esquema de solicitud
Solo los recursos que se solicitan mediante el protocolo seleccionado cumplirán la condición Esquema de solicitud. Los protocolos disponibles son HTTP y HTTPS.

Información importante:
- Debido a la manera en que se realiza el seguimiento de la configuración de la memoria caché, esta condición de coincidencia es incompatible con las siguientes características:
  - Relleno de la memoria caché completa
  - Max-Age interna predeterminada
  - Forzar Max-Age interna
  - Ignorar no almacenar en caché de origen
  - Max-Stale interna

[Volver arriba](#match-conditions-for-the-azure-cdn-rules-engine)

</br>

## <a name="next-steps"></a>pasos siguientes
* [Introducción a Azure Content Delivery Network](cdn-overview.md)
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Características del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)

