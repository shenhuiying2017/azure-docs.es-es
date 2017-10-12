---
title: Condiciones de coincidencia del motor de reglas de la red CDN de Azure | Microsoft Docs
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
ms.openlocfilehash: f4886b1d78dfa87cf25737fb46c12b5963034f27
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="azure-cdn-rules-engine-match-conditions"></a>Condiciones de coincidencia del motor de reglas de la red CDN de Azure
En este tema se muestran descripciones detalladas de las condiciones de coincidencia disponibles para el [Motor de reglas](cdn-rules-engine.md) de Azure Content Delivery Network (CDN).

La segunda parte de una regla es la condición de coincidencia. Una condición de coincidencia identifica tipos específicos de solicitudes para los que se ejecutará un conjunto de características.

Por ejemplo, puede usarse para filtrar solicitudes de contenido en una ubicación en concreto, solicitudes generadas desde una dirección IP o país en concreto, o por información de encabezado.

## <a name="always"></a>Siempre

La condición de coincidencia Siempre está diseñada para aplicar un conjunto predeterminado de características a todas las solicitudes.

## <a name="device"></a>Dispositivo

La condición de coincidencia Dispositivo identifica solicitudes realizadas desde un dispositivo móvil en función de sus propiedades.  La detección de dispositivos móviles se realiza a través de [WURFL](http://wurfl.sourceforge.net/).  A continuación se enumeran las funcionalidades de WURFL y sus variables de motor de reglas de red CDN.

> [!NOTE] 
> Las variables siguientes se admiten en las funciones **Modificar encabezado de solicitud de cliente** y **Modificar encabezado de respuesta de cliente**.

Capacidad | Variable | Descripción | Valores de ejemplo
-----------|----------|-------------|----------------
Nombre de la marca | %{wurfl_cap_brand_name} | Una cadena que indica el nombre de marca del dispositivo. | Samsung
Sistema operativo del dispositivo | %{wurfl_cap_device_os} | Una cadena que indica el sistema operativo instalado en el dispositivo. | IOS
Versión del sistema operativo del dispositivo | %{wurfl_cap_device_os_version} | Una cadena que indica el número de versión del sistema operativo instalado en el dispositivo. | 1.0.1
Orientación dual | %{wurfl_cap_dual_orientation} | Un valor booleano que indica si el dispositivo admite la orientación dual. | true
DTD preferido de HTML | %{wurfl_cap_html_preferred_dtd} | Una cadena que indica la definición de tipo de documento (DTD) preferido del dispositivo móvil para el contenido HTML. | Ninguna<br/>xhtml_basic<br/>html5
Imagen de inclusión | %{wurfl_cap_image_inlining} | Un valor booleano que indica si el dispositivo admite imágenes con codificación Base64. | false
Es Android | %{wurfl_vcap_is_android} | Un valor booleano que indica si el dispositivo usa el sistema operativo Android. | true
Es IOS | %{wurfl_vcap_is_ios} | Un valor booleano que indica si el dispositivo usa iOS. | false
Es Smart TV | %{wurfl_cap_is_smarttv} | Un valor booleano que indica si el dispositivo es una Smart TV. | false
Es smartphone | %{wurfl_vcap_is_smartphone} | Un valor booleano que indica si el dispositivo es un smartphone. | true
Es tablet | %{wurfl_cap_is_tablet} | Un valor booleano que indica si el dispositivo es una tablet. Esta es una descripción independiente del SO. | true
Es dispositivo inalámbrico | %{wurfl_cap_is_wireless_device} | Un valor booleano que indica si el dispositivo se considera un dispositivo inalámbrico. | true
Nombre de marca | %{wurfl_cap_marketing_name} | Una cadena que indica el nombre de marca del dispositivo. | BlackBerry 8100 Pearl
Explorador móvil | %{wurfl_cap_mobile_browser} | Una cadena que indica el explorador usado para solicitar el contenido del dispositivo. | Chrome
Versión del explorador móvil | %{wurfl_cap_mobile_browser_version} | Una cadena que indica la versión del explorador usada para solicitar el contenido del dispositivo. | 31
Nombre del modelo | %{wurfl_cap_model_name} | Una cadena que indica el nombre del modelo del dispositivo. | s3
Descarga progresiva | %{wurfl_cap_progressive_download} | Un valor booleano que indica si el dispositivo admite la reproducción de audio/vídeo mientras todavía se está descargando. | true
Fecha de lanzamiento | %{wurfl_cap_release_date} | Una cadena que indica el año y el mes en el que se agregó el dispositivo a la base de datos WURFL.<br/><br/>Formato: `yyyy_mm` | 2013_december
Altura de resolución | %{wurfl_cap_resolution_height} | Un entero que indica la altura del dispositivo en píxeles. | 768
Anchura de resolución | %{wurfl_cap_resolution_width} | Un entero que indica la anchura del dispositivo en píxeles. | 1024


## <a name="location"></a>Ubicación

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de la ubicación del solicitante.

Nombre | Propósito
-----|--------
Número de sistema autónomo (AS) | Identifica solicitudes que se originan en una red determinada.
País | Identifica solicitudes que se originan en países determinados.


## <a name="origin"></a>Origen

Estas condiciones de coincidencia están diseñadas para identificar solicitudes que señalan al servidor de origen de un cliente o de almacenamiento de CDN.

Nombre | Propósito
-----|--------
Origen de red CDN | Identifica solicitudes de contenido almacenado en el almacenamiento de CDN.
Origen de cliente | Identifica solicitudes de contenido almacenado en el servidor de origen de un cliente específico.


## <a name="request"></a>Solicitud

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de sus propiedades.

Nombre | Propósito
-----|--------
Dirección IP de cliente | Identifica solicitudes que se originan en una dirección IP determinada.
Parámetro de cookie | Busca el valor especificado en las cookies asociadas a cada solicitud.
Regex de parámetro de cookie | Busca la expresión regular especificada en las cookies asociadas a cada solicitud.
Cname perimetral | Identifica solicitudes que señalan a un CNAME perimetral específico .
Dominio de referencia | Identifica solicitudes a las que se haga referencia en los nombres de host especificados.
Literal de encabezado de solicitud | Identifica solicitudes que contienen el encabezado que se especifique establecido en unos valores especificados.
Regex de encabezado de solicitud | Identifica solicitudes que contienen el encabezado que se especifique establecido en la expresión regular especificada.
Carácter comodín de encabezado de solicitud | Identifica solicitudes que contienen el encabezado que se especifique establecido en un valor que coincide con el patrón especificado.
Método de solicitud | Identifica solicitudes por su método HTTP.
Esquema de solicitud | Identifica solicitudes por su protocolo HTTP.

## <a name="url"></a>URL

Estas condiciones de coincidencia están diseñadas para identificar solicitudes en función de sus direcciones URL.

Nombre | Propósito
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
Carácter comodín de consulta de dirección URL | Compara los valores especificados con la cadena de consulta de una solicitud.


## <a name="next-steps"></a>Pasos siguientes
* [Información general de la red CDN de Azure](cdn-overview.md)
* [Referencia del motor de reglas](cdn-rules-engine-reference.md)
* [Expresiones condicionales del motor de reglas](cdn-rules-engine-reference-conditional-expressions.md)
* [Funciones del motor de reglas](cdn-rules-engine-reference-features.md)
* [Invalidación del comportamiento HTTP predeterminado mediante el motor de reglas](cdn-rules-engine.md)

