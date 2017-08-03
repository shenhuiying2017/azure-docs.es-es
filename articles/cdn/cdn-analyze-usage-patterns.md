---
title: "Análisis de patrones de uso de la red CDN de Azure | Microsoft Docs"
description: "Puede ver los patrones de uso de la red CDN mediante los siguientes informes: Ancho de banda, Datos transferidos, Aciertos, Estados de la memoria caché, Frecuencia de aciertos de caché, Datos de IPV4/IPV6 transferidos."
services: cdn
documentationcenter: 
author: zhangmanling
manager: erikre
editor: 
ms.assetid: 5a0d9018-8bdb-48ff-84df-23648ebcf763
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.translationtype: Human Translation
ms.sourcegitcommit: 3bbc9e9a22d962a6ee20ead05f728a2b706aee19
ms.openlocfilehash: aadbe872dd3384c8d337b432fb3be69422ca322b
ms.contentlocale: es-es
ms.lasthandoff: 07/06/2017


---
# <a name="analyze-azure-cdn-usage-patterns"></a>Análisis de patrones de uso de la red CDN de Azure

[!INCLUDE[cdn-verizon-only](../../includes/cdn-verizon-only.md)]

En la guía siguiente se explican los pasos para ver los informes básicos mediante el portal de administración de perfiles de Verizon. También puede exportar datos de análisis básico a Storage, Event Hubs o Log Analytics (OMS) para perfiles de Verizon y Akamai [a través de Azure Portal](cdn-log-analysis.md).

Puede ver los patrones de uso de la red CDN mediante los siguientes informes:

* Ancho de banda
* Datos transferidos
* Aciertos
* Estados de la memoria caché
* Frecuencia de aciertos de caché
* Datos de IPV4/IPV6 transferidos

## <a name="accessing-core-reports"></a>Acceso a informes básicos
1. En la hoja de perfil de CDN, haga clic en el botón **Administrar** .
   
    ![Botón de administración de hoja de perfil de red CDN](./media/cdn-reports/cdn-manage-btn.png)
   
    Se abre el portal de administración de CDN.
2. Mueva el puntero sobre la pestaña **Análisis** y después sobre el control flotante **Core Reports** (Informes básicos).  Haga clic en el informe que desee en el menú.
   
    ![Portal de administración de la red CDN - menú Core Reports (Informes básicos)](./media/cdn-reports/cdn-core-reports.png)

## <a name="bandwidth"></a>Ancho de banda
El informe de ancho de banda consta de una tabla de datos y un gráfico que indica el uso del ancho de banda para HTTP y HTTPS en un período de tiempo determinado. Puede ver el uso del ancho de banda a través de un POP determinado o todos los POP de CDN. Esto permite ver los picos de tráfico y la distribución a través de los POP de CDN en Mbps.

* Seleccione Todos los nodos perimetrales para ver el tráfico de todos los nodos o elija una región o un nodo específico en la lista desplegable.
* Seleccione el intervalo de fechas para ver datos de hoy, esta semana o este mes, etc. o especifique fechas personalizadas y haga clic en "Ir" para asegurarse de que se actualiza la selección.
* Para exportar y descargar los datos, haga clic en el icono de hoja de Excel situado junto a "Ir".

El informe se actualiza cada 5 minutos.

![Informe Ancho de banda](./media/cdn-reports/cdn-bandwidth.png)

## <a name="data-transferred"></a>Datos transferidos
El informe consta de una tabla de datos y un gráfico que indica el uso del tráfico para HTTP y HTTPS en un período de tiempo determinado. Puede ver el uso del tráfico a través de todos los POP de CDN o un POP determinado. Esto permite ver los picos de tráfico y la distribución a través de los POP de CDN en GB.

* Seleccione Todos los nodos perimetrales para ver el tráfico de todos los nodos o elija una región o un nodo específico en la lista desplegable.
* Seleccione el intervalo de fechas para ver datos de hoy, esta semana o este mes, etc. o especifique fechas personalizadas y haga clic en "Ir" para asegurarse de que se actualiza la selección.
* Para exportar y descargar los datos, haga clic en el icono de hoja de Excel situado junto a "Ir".

El informe se actualiza cada 5 minutos.

![Informe Datos transferidos](./media/cdn-reports/cdn-data-transferred.png)

## <a name="hits-status-codes"></a>Aciertos (códigos de estado)
Este informe describe la distribución de códigos de estado de la solicitud para su contenido. Todas las solicitudes de contenido generarán un código de estado HTTP. El código de estado describe cómo los POP perimetrales procesaron la solicitud. Por ejemplo, los códigos de estado 2xx indican que la solicitud se ha servido correctamente a un cliente, mientras que un código de estado 4xx indica que se produjo un error. Para obtener más detalles sobre el código de estado HTTP, consulte [códigos de estado](https://en.wikipedia.org/wiki/List_of_HTTP_status_codes).

* Seleccione el intervalo de fechas para ver datos de hoy, esta semana o este mes, etc. o especifique fechas personalizadas y haga clic en "Ir" para asegurarse de que se actualiza la selección.
* Para exportar y descargar los datos, haga clic en el icono de hoja de Excel situado junto a "Ir".

![Informe Aciertos](./media/cdn-reports/cdn-hits.png)

## <a name="cache-statuses"></a>Estados de la memoria caché
Este informe describe la distribución de aciertos de caché y errores de caché de la solicitud del cliente. Puesto que el rendimiento más rápido procede de los aciertos de caché, para optimizar velocidades de entrega de datos puede minimizar los errores de caché y aciertos de caché expirados. Los errores de caché pueden reducirse mediante la configuración de su servidor de origen para evitar la asignación de encabezados de respuesta "no-cache", mediante la supresión del almacenamiento en caché de la cadena de consulta excepto cuando sea estrictamente necesario y mediante la supresión de los códigos de respuesta no almacenables en caché. Los aciertos de caché expirados pueden evitarse haciendo que la duración máxima de un recurso sea lo más larga posible para minimizar el número de solicitudes al servidor de origen.

![Informe Estados de la memoria caché](./media/cdn-reports/cdn-cache-statuses.png)

### <a name="main-cache-statuses-include"></a>Los principales estados de la memoria caché incluyen:
* TCP_HIT: servido desde el extremo. El objeto estaba en la memoria caché y no ha excedido su duración máxima.
* TCP_MISS: servido desde el origen. El objeto no estaba en la memoria caché y la respuesta se devolvió al origen.
* TCP_EXPIRED _MISS: servido desde el origen después de la revalidación con el origen. El objeto estaba en la memoria caché, pero ha excedido su duración máxima. Como resultado de una revalidación con el origen, el objeto de caché será reemplazado por una nueva respuesta desde el origen.
* TCP_EXPIRED _HIT: servido desde el extremo después de la revalidación con el origen. El objeto estaba en la memoria caché, pero ha excedido su duración máxima. Como resultado de una revalidación con el servidor de origen, el objeto de caché no se ha modificado.
* Seleccione el intervalo de fechas para ver datos de hoy, esta semana o este mes, etc. o especifique fechas personalizadas y haga clic en "Ir" para asegurarse de que se actualiza la selección.
* Para exportar y descargar los datos, haga clic en el icono de hoja de Excel situado junto a "Ir".

### <a name="full-list-of-cache-statuses"></a>Lista completa de los estados de la memoria caché
* TCP_HIT: este estado se comunica cuando una solicitud se sirve directamente desde el POP al cliente. Un activo se sirve inmediatamente desde un POP cuando se almacena en caché en el POP más cercano al cliente y tiene un valor de período de vida o TTL válido. TTL se determina mediante los siguientes encabezados de respuesta:
  
  * Cache-Control: s-maxage
  * Cache-Control: max-age
  * Expira
* TCP_MISS: este estado indica que no se encontró una versión en caché del recurso solicitado en el POP más cercano al cliente. Se solicitará el recurso desde un servidor de origen o un servidor de escudo de origen. Si el servidor de origen o el servidor de escudo de origen devuelve un recurso, se sirve al cliente y se almacena en caché en el cliente y el servidor perimetral. De lo contrario, se devolverá un código de estado que no sea 200 (p. ej., 403 Prohibido, 404 no encontrado, etc.).
* TCP_EXPIRED _HIT: este estado se comunica cuando una solicitud destinada a un recurso con TTL expirado, por ejemplo, cuando max-age del activo ha expirado, se sirve directamente desde el POP al cliente.
  
    Una solicitud expirada normalmente produce una solicitud de revalidación al servidor de origen. Para que se produzca un _HIT TCP_EXPIRED, el servidor de origen debe indicar que no existe una versión más reciente del recurso. Este tipo de situación normalmente actualizará los encabezados Cache-Control y Expires de dicho recurso.
* TCP_EXPIRED _MISS: este estado se comunica cuando se proporciona una versión más reciente de un recurso almacenado en caché expirado desde el POP al cliente. Esto se produce cuando ha expirado el período de vida para un recurso almacenado en caché (por ejemplo, max-age expirado) y el servidor de origen devuelve una versión más reciente de dicho recurso. Esta nueva versión del recurso se servirá al cliente en lugar de la versión en caché. Además, se almacenará en el servidor perimetral y el cliente.
* CONFIG_NOCACHE: este estado indica que una configuración específica del cliente en nuestro POP perimetral impidió que el recurso se almacene en caché.
* NONE: este estado indica que no se realizó una comprobación de actualización del contenido de la memoria caché.
* TCP_ CLIENT_REFRESH _MISS: este estado se comunica cuando un cliente HTTP (por ejemplo, explorador) fuerza un POP perimetral para recuperar una nueva versión de un recurso obsoleto desde el servidor de origen.
  
    De forma predeterminada, nuestros servidores impiden que un cliente HTTP fuerce nuestros servidores perimetrales para recuperar una nueva versión del recurso desde el servidor de origen.
* TCP_ PARTIAL_HIT: este estado se comunica cuando una solicitud de intervalo de bytes da como resultado un acierto para un recurso almacenado parcialmente en caché. El intervalo de bytes solicitado se sirve inmediatamente desde el POP al cliente.
* UNCACHEABLE: este estado se comunica cuando los encabezados Cache-Control y Expires de un recurso indican que no se debe almacenar en caché en un POP o mediante el cliente HTTP. Estos tipos de solicitudes se sirven desde el servidor de origen

## <a name="cache-hit-ratio"></a>Frecuencia de aciertos de caché
Este informe indica el porcentaje de solicitudes almacenadas en caché que se sirven directamente desde la memoria caché.

El informe proporciona los detalles siguientes:

* El contenido solicitado se almacenó en caché en el POP más cercano al solicitante.
* La solicitud se sirve directamente desde el extremo de la red.
* La solicitud no requería revalidación con el servidor de origen.

El informe no incluye:

* Solicitudes denegadas debido a las opciones de filtrado de país.
* Solicitudes de recursos cuyos encabezados indican que no se deben almacenar en caché. Por ejemplo, los encabezados Cache-Control: private, Cache-Control: no-cache o Pragma: no-cache impedirán que un recurso se almacene en caché.
* Solicitudes de intervalo de bytes para contenido almacenado parcialmente en caché.

La fórmula es: (TCP_ HIT/(TCP_ HIT+TCP_MISS))*100

* Seleccione el intervalo de fechas para ver datos de hoy, esta semana o este mes, etc. o especifique fechas personalizadas y haga clic en "Ir" para asegurarse de que se actualiza la selección.
* Para exportar y descargar los datos, haga clic en el icono de hoja de Excel situado junto a "Ir".

![Informe Frecuencia de aciertos de caché](./media/cdn-reports/cdn-cache-hit-ratio.png)

## <a name="ipv4ipv6-data-transferred"></a>Datos de IPV4/IPV6 transferidos
Este informe muestra la distribución de uso de tráfico en IPV4 frente a IPV6.

![Datos de IPV4/IPV6 transferidos](./media/cdn-reports/cdn-ipv4-ipv6.png)

* Seleccione el intervalo de fechas para ver datos de hoy, esta semana o este mes, etc. o especifique fechas personalizadas.
* A continuación, haga clic en "Ir" para asegurarse de que la selección se actualiza.

## <a name="considerations"></a>Consideraciones
Los informes solo pueden generarse en los últimos 18 meses.


