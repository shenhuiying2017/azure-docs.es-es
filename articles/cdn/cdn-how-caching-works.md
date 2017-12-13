---
title: "Cómo funciona el almacenamiento en caché en Azure Content Delivery Network | Microsoft Docs"
description: "El almacenamiento en caché es el proceso de almacenar datos localmente para que se pueda tener acceso a las futuras solicitudes de esos datos más rápidamente."
services: cdn
documentationcenter: 
author: dksimpson
manager: 
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/23/2017
ms.author: v-deasim
ms.openlocfilehash: 638b105b4848d41b2755a4b153c13a77fb9ca08b
ms.sourcegitcommit: 5d3e99478a5f26e92d1e7f3cec6b0ff5fbd7cedf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/06/2017
---
# <a name="how-caching-works"></a>Cómo funciona el almacenamiento en caché

Este artículo proporciona una introducción a conceptos generales del almacenamiento en caché y cómo Azure Content Delivery Network (CDN) usa el almacenamiento en caché para mejorar el rendimiento. Si desea obtener información sobre cómo personalizar el comportamiento del almacenamiento en caché en el punto de conexión CDN, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de almacenamiento en caché](cdn-caching-rules.md) y [Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta](cdn-query-string.md).

## <a name="introduction-to-caching"></a>Introducción al almacenamiento en caché

El almacenamiento en caché es el proceso de almacenar datos localmente para que se pueda tener acceso a las futuras solicitudes de esos datos más rápidamente. En el tipo más común de almacenamiento en caché, el almacenamiento en caché del explorador web, un explorador web almacena copias de datos estáticos localmente en una unidad de disco duro local. Mediante el uso del almacenamiento en caché, el explorador web puede evitar la realización de varias ida y vuelta al servidor y, en su lugar, obtener acceso a los mismos datos localmente, lo que permite ahorrar tiempo y recursos. El almacenamiento en caché es ideal para administrar localmente datos pequeños y estáticos como imágenes estáticas, archivos CSS y archivos de JavaScript.

De forma similar, una red de entrega de contenido utiliza el almacenamiento en caché en los servidores perimetrales más cercanos al usuario para evitar que las solicitudes vayan hasta el origen y reducir la latencia del usuario final. A diferencia del almacenamiento en caché del explorador web, que es utilizado solo por un único usuario, la red CDN tiene una memoria caché compartida. En el almacenamiento en caché compartido de la red CDN, un archivo que solicita un usuario puede tener acceso más adelante por otros usuarios, lo que reduce considerablemente el número de solicitudes al servidor de origen.

No se almacenan en caché los recursos dinámicos que cambian con frecuencia o son únicos para un usuario individual. Dichos tipos de recursos, sin embargo, pueden sacar partido de la optimización de la aceleración de sitios dinámicos (DSA) en Azure Content Delivery Network para mejorar el rendimiento.

El almacenamiento en caché puede producirse en varios niveles entre el servidor de origen y el usuario final:

- Servidor web: utiliza una memoria caché compartida (para varios usuarios).
- Content Delivery Network: utiliza una memoria caché compartida (para varios usuarios).
- Proveedor de servicios de Internet: utiliza una memoria caché compartida (para varios usuarios).
- Explorador web: utiliza una memoria caché privada (para un usuario).

Cada almacenamiento en caché normalmente administra su propia actualización de recursos y realiza la validación cuando un archivo está obsoleto. Este comportamiento se define en la especificación de almacenamiento en caché de HTTP, [RFC 7234](https://tools.ietf.org/html/rfc7234).

### <a name="resource-freshness"></a>Actualización de recursos

Dado que un recurso almacenado en caché podría estar obsoleto (en comparación con el recurso correspondiente en el servidor de origen), es importante para cualquier mecanismo de almacenamiento en caché controlar cuándo se actualiza el contenido. Para ahorrar en el consumo de tiempo y ancho de banda, un recurso almacenado en caché no se compara con la versión en el servidor de origen cada vez que se tiene acceso a él. En su lugar, siempre que se considere que un recurso almacenado en caché está actualizado, se supone que es la versión más reciente y se envía directamente al cliente. Se considera que un recurso almacenado en caché está actualizado cuando su antigüedad es menor que el tiempo o el período definido por una configuración de caché. Por ejemplo, cuando un explorador vuelve a cargar una página web, comprueba que cada recurso almacenado en caché en el disco duro está actualizado y lo carga. Si el recurso no está actualizado (obsoleto), se carga una copia actualizada desde el servidor.

### <a name="validation"></a>Validación

Si un recurso se considera obsoleto, se solicita al servidor de origen que lo valide, es decir, que determine si los datos en la memoria caché aún coinciden con lo que aparecen en el servidor de origen. Si el archivo se ha modificado en el servidor de origen, el almacenamiento en caché actualiza su versión del recurso. En caso contrario, si el recurso está actualizado, los datos se entregan directamente desde la memoria caché sin validarlos primero.

### <a name="cdn-caching"></a>Almacenamiento en caché de la red CDN

El almacenamiento en caché es una parte integral de la forma en que funciona una red CDN para acelerar la entrega y reducir la carga de origen para recursos estáticos como imágenes, fuentes y vídeos. En el almacenamiento en caché de la red CDN, los recursos estáticos se almacenan selectivamente en servidores ubicados estratégicamente que sean más locales a un usuario y ofrece las siguientes ventajas:

- Dado que la mayor parte del tráfico web es estático (por ejemplo, imágenes, fuentes y vídeos), el almacenamiento en caché de la red CDN reduce la latencia moviendo el contenido más cerca del usuario, lo que reduce la distancia que recorren los datos.

- Mediante la descarga de trabajo a una red CDN, el almacenamiento en caché puede reducir el tráfico de red y la carga en el servidor de origen. De este modo se reducen los requisitos de recursos y el costo de la aplicación, incluso si existen grandes cantidades de usuarios.

De modo similar a un explorador web, puede controlar cómo se realiza el almacenamiento en caché de la red CDN mediante el envío de encabezados de directiva de caché. Los encabezados de directiva de caché son encabezados HTTP, normalmente agregados por el servidor de origen. Aunque la mayoría de estos encabezados se diseñaron originalmente para tratar el almacenamiento en caché en los exploradores de cliente, ahora también se usan en todas las memorias caché intermedias, como las redes CDN. Se pueden utilizar dos encabezados para definir la actualización del almacenamiento en caché: `Cache-Control` y `Expires`. `Cache-Control` es más reciente y tiene prioridad sobre `Expires`, si ambos existen. También hay dos tipos de encabezados que se utilizan para la validación (llamados validadores): `ETag` y `Last-Modified`. `ETag` es más reciente y tiene prioridad sobre `Last-Modified`, si ambos están definidos.  

## <a name="cache-directive-headers"></a>Encabezados de la directiva de caché

Azure CDN admite los siguientes encabezados de directiva de caché HTTP, que definen la duración de la memoria caché y el uso compartido de la memoria caché: 

`Cache-Control`  
- Incorporado en HTTP 1.1 para conceder a los editores web más control sobre su contenido y para resolver las limitaciones del encabezado `Expires`.
- Invalida el encabezado `Expires` si este y `Cache-Control` están definidos.
- Cuando se utiliza en un encabezado de solicitud: Azure CDN lo omite de forma predeterminada.
- Cuando se utiliza en un encabezado de respuesta: Azure CDN respeta las siguientes directivas de `Cache-Control` en situaciones de entrega web general, descarga de archivos de gran tamaño y optimizaciones de streaming multimedia generales y de vídeo bajo demanda:  
   - `max-age`: una memoria caché puede almacenar el contenido durante el número de segundos especificado. Por ejemplo: `Cache-Control: max-age=5`. Esta directiva especifica la cantidad máxima de tiempo que el contenido se considera actualizado.
   - `private`: el contenido va dirigido a un usuario único; no se almacena el contenido en la memoria caché compartida, como CDN.
   - `no-cache`: se almacena en caché el contenido, pero se debe validar el contenido cada vez antes de su entrega desde la memoria caché. Equivalente a `Cache-Control: max-age=0`.
   - `no-store`: nunca se almacena en caché el contenido. Se elimina el contenido si se ha almacenado anteriormente.

`Expires` 
- Encabezado heredado incorporado en HTTP 1.0; admitido por compatibilidad con versiones anteriores.
- Usa un tiempo de expiración basado en fechas con precisión de segundos. 
- Similar a `Cache-Control: max-age`.
- Se usa cuando no existe `Cache-Control`.

`Pragma` 
   - De forma predeterminada, Azure CDN no lo cumple.
   - Encabezado heredado incorporado en HTTP 1.0; admitido por compatibilidad con versiones anteriores.
   - Utilizado como un encabezado de solicitud de cliente con la siguiente directiva: `no-cache`. Esta directiva indica al servidor que debe entregar una versión actualizada del recurso.
   - `Pragma: no-cache` equivale a `Cache-Control: no-cache`.

De forma predeterminada, las optimizaciones de DSA omiten estos encabezados. Puede ajustar la forma en que Azure CDN trata estos encabezados mediante reglas de almacenamiento en caché de CDN. Para más información, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de almacenamiento en caché](cdn-caching-rules.md).

## <a name="validators"></a>Validadores

Cuando la memoria caché está obsoleta, los validadores de almacenamiento en caché HTTP se utilizan para comparar la versión en caché de un archivo con la versión en el servidor de origen. **La red CDN de Azure de Verizon** admite validadores ETag y Last-Modified de forma predeterminada, mientras que **la red CDN de Azure de Akamai** solo admite Last-Modified de forma predeterminada.

`ETag`
- **La red CDN de Azure de Verizon** utiliza `ETag` de forma predeterminada, pero **la red CDN de Azure de Akamai** no lo hace.
- `ETag` define una cadena que es única para cada archivo y versión de un archivo. Por ejemplo: `ETag: "17f0ddd99ed5bbe4edffdd6496d7131f"`.
- Incorporado en HTTP 1.1, es más reciente que `Last-Modified`. Resulta útil cuando es difícil determinar la fecha de última modificación.
- Admite tanto la validación segura como la validación débil; sin embargo, la red CDN de Azure admite solo validación segura. Para la validación segura, las representaciones de dos recursos deben ser idénticas byte a byte. 
- Una memoria caché valida un archivo que usa `ETag` mediante el envío de un encabezado `If-None-Match` con uno o varios validadores `ETag` en la solicitud. Por ejemplo: `If-None-Match: "17f0ddd99ed5bbe4edffdd6496d7131f"`. Si la versión del servidor coincide con un validador `ETag` de la lista, envía el código de estado 304 (no modificado) en la respuesta. Si la versión es diferente, el servidor responde con el código de estado 200 (OK) y el recurso actualizado.

`Last-Modified`
- Para **la red CDN de Azure de Verizon únicamente**, Last-Modified se utiliza si ETag no forma parte de la respuesta HTTP. 
- Especifica la fecha y hora en la que el servidor de origen determina que se modificó por última vez el recurso. Por ejemplo: `Last-Modified: Thu, 19 Oct 2017 09:28:00 GMT`.
- Una memoria caché valida un archivo mediante `Last-Modified` enviando un encabezado `If-Modified-Since` con una fecha y hora en la solicitud. El servidor de origen compara esa fecha con el encabezado `Last-Modified` del recurso más reciente. Si el recurso no se ha modificado desde la hora especificada, el servidor devuelve el código de estado 304 (no modificado) en la respuesta. Si se ha modificado el recurso, el servidor devuelve el código de estado 200 (OK) y el recurso actualizado.

## <a name="determining-which-files-can-be-cached"></a>Determinación de los archivos que pueden almacenarse en caché

No todos los recursos se pueden almacenar en caché. La siguiente tabla muestra los recursos que pueden almacenarse en caché, según el tipo de respuesta HTTP. No se almacenarán en caché los recursos entregados con respuestas HTTP que no cumplen todas estas condiciones. Únicamente para **la red CDN de Azure de Verizon Premium**, puede usar el motor de reglas para personalizar algunas de estas condiciones.

|                   | Azure CDN de Verizon | Azure CDN de Akamai            |
|------------------ |------------------------|----------------------------------|
| Códigos de estado HTTP | 200                    | 200, 203, 300, 301, 302 y 401 |
| HTTP method       | GET                    | GET                              |
| Tamaño de archivo         | < 300 GB                 | <ul><li>Optimización de entrega de web general: 1,8 GB</li> <li>Optimizaciones de streaming multimedia: 1,8 GB</li> <li>Optimización de archivos grandes: 150 GB</li> |

## <a name="default-caching-behavior"></a>Comportamiento predeterminado del almacenamiento en caché

En la tabla siguiente se describe el valor predeterminado del comportamiento del almacenamiento en caché para los productos de la red CDN de Azure y sus optimizaciones.

|                    | Verizon: entrega web general | Verizon: aceleración de sitios dinámicos | Akamai: entrega web general | Akamai: aceleración de sitios dinámicos | Akamai: descarga de archivos de gran tamaño | Akamai: streaming de multimedia general o de vídeo bajo demanda |
|--------------------|--------|------|-----|----|-----|-----|
| **Respetar origen**   | Sí    | No   | Sí | No | Sí | Sí |
| **Duración de la caché de la red CDN** | 7 días | Ninguna | 7 días | None | 1 día | 1 año |

**Respetar origen**: especifica si se deben respetar los [encabezados de la directiva de caché admitidos](#http-cache-directive-headers) si existen en la respuesta HTTP del servidor de origen.

**Duración de la caché de la red CDN**: especifica la cantidad de tiempo que se almacena en caché un recurso en la red CDN de Azure. Sin embargo, si **Respetar origen** es "Sí" y la respuesta HTTP del servidor de origen incluye el encabezado de la directiva de caché `Expires` o `Cache-Control: max-age`, la red CDN de Azure usa el valor de duración especificado por el encabezado en su lugar. 

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información sobre cómo personalizar e invalidar el comportamiento del almacenamiento en caché predeterminado en la red CDN mediante reglas de almacenamiento en caché, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con reglas de almacenamiento en caché](cdn-caching-rules.md). 
- Para obtener información sobre cómo usar cadenas de consulta para controlar el comportamiento del almacenamiento en caché, consulte [Control del comportamiento del almacenamiento en caché de Azure CDN con cadenas de consulta](cdn-query-string.md).



