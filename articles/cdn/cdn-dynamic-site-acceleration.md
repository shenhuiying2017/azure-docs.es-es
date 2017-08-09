---
title: "Aceleración de sitios dinámicos a través de la red CDN de Azure"
description: "Profundización de la aceleración de sitios dinámicos"
services: cdn
documentationcenter: 
author: smcevoy
manager: erikre
editor: 
ms.assetid: 
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/29/2017
ms.author: v-semcev
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: e886296fd0a0d49975a36fb86347dec8ad30b176
ms.contentlocale: es-es
ms.lasthandoff: 07/21/2017

---
# <a name="dynamic-site-acceleration-via-azure-cdn"></a>Aceleración de sitios dinámicos a través de la red CDN de Azure

Con la expansión de las redes sociales, el comercio electrónico y la web hiperpersonalizada, se genera un porcentaje creciente del contenido que se ofrece a los usuarios finales en tiempo real. Los usuarios esperan experiencias web rápidas, confiables y personalizadas, independientemente de su explorador, ubicación, dispositivo o red. Pero las mismas innovaciones que hacen que estas experiencias sean tan atractivas también ralentizan las descargas de páginas y ponen en riesgo la calidad de la experiencia del consumidor. 

La funcionalidad estándar de red CDN incluye la capacidad de almacenar los archivos más cerca de los usuarios finales para acelerar la entrega de archivos estáticos. Pero con las aplicaciones web dinámicas, no es posible almacenar en caché ese contenido en ubicaciones perimetrales, porque el servidor genera el contenido en respuesta al comportamiento del usuario. Acelerar la entrega de este tipo de contenido es más complejo que el almacenamiento en caché perimetral tradicional y requiere una solución integral en la que se ajusten con precisión todos los elementos de toda la ruta de datos, desde el comienzo hasta la entrega. Con la Aceleración de sitios dinámicos (DSA) de la red CDN de Azure, el rendimiento de las páginas web con contenido dinámico se ha mejorado significativamente.

La red CDN de Azure de Akamai ofrece optimización DSA a través del menú **Optimizado para** durante la creación del punto de conexión. Esta característica no está disponible todavía para los perfiles de Verizon, pero lo estará en breve. 

## <a name="configuring-cdn-endpoint-to-accelerate-delivery-of-dynamic-files"></a>Configuración de punto de conexión de red CDN para acelerar la entrega de archivos dinámicos

Puede configurar el punto de conexión de la red CDN para optimizar la entrega de archivos dinámicos a través de Azure Portal simplemente seleccionando la opción **Aceleración de sitios dinámicos** bajo la selección de la propiedad **Optimizado para** durante la creación del punto de conexión. También puede usar nuestras API de REST o cualquiera de los SDK de cliente para hacer esto. 

### <a name="probe-path"></a>Ruta de acceso de sondeo
La ruta de acceso de sondeo es una característica específica de la aceleración de sitios dinámicos y se requiere la creación de una válida. DSA usa un pequeño archivo de *ruta de acceso de sondeo* en el origen para optimizar las configuraciones de enrutamiento de red para la red CDN. Puede descargar y cargar nuestro archivo de ejemplo en el sitio o usar en su lugar un recurso existente en el origen que es de aproximadamente 10 KB para la ruta de acceso de sondeo, si existe.

> [!Note]
> DSA conlleva cargos adicionales. Vea la página de precios para más información.

Las capturas de pantalla siguientes ilustran el proceso a través de Azure Portal.
 
![Agregar un nuevo punto de conexión de red CDN](./media/cdn-dynamic-site-acceleration/01_Endpoint_Profile.png) 

*Figura 1: Adición de un nuevo punto de conexión de red CDN a partir de la opción Perfil de CDN*
 
![Creación de un nuevo punto de conexión de red CDN con DSA](./media/cdn-dynamic-site-acceleration/02_Optimized_DSA.png)  

*Figura 2: Creación de un punto de conexión de la red CDN con la opción de optimización de aceleración de sitios dinámicos seleccionada*

Una vez creado el punto de conexión de la red CDN, aplicará las optimizaciones de DSA para todos los archivos que coincidan con ciertos criterios. En la sección siguiente se describe esto en detalle.

## <a name="dsa-optimization-using-azure-cdn-from-akamai"></a>Optimización de DSA con la red CDN de Azure de Akamai

La aceleración de sitios dinámicos en la red CDN de Azure de Akamai acelera la entrega de activos dinámicos mediante las técnicas siguientes:

-   Optimización de rutas
-   Optimizaciones de TCP
-   Captura previa de objetos

### <a name="route-optimization"></a>Optimización de rutas

La optimización de rutas es importante porque Internet es un lugar muy dinámico, donde el tráfico y las interrupciones temporales cambian constantemente la topología de la red. El protocolo de puerta de enlace de borde (BGP) es el protocolo de enrutamiento de Internet, pero puede haber rutas más rápidas a través de servidores intermediarios de Akamai. 

La optimización de rutas elige la ruta óptima para el origen para que un sitio sea accesible de manera continua y el contenido dinámico se entregue a los usuarios finales a través de la ruta más rápida y más confiable posible. La red de Akamai usa técnicas para recopilar datos en tiempo real y comparar varias rutas de acceso a través de los diferentes nodos en el servidor de Akamai, así como la ruta BGP predeterminada a través de Internet, para determinar la ruta más rápida entre el origen y el perímetro de la red CDN. Esto evita que haya puntos de congestión en Internet y rutas innecesariamente largas. 
 
Como resultado, se entrega contenido totalmente dinámico y transaccional de forma más rápida y más confiable a los usuarios finales, incluso cuando no se puede almacenar en caché. 

### <a name="tcp-optimizations"></a>Optimizaciones de TCP

El Protocolo de control de transmisión (TCP) es un estándar del conjunto de protocolos de Internet que se usa para entregar información entre aplicaciones de una red IP.  De forma predeterminada, se necesitan varias solicitudes en ambos sentidos para configurar una conexión TCP, así como límites para evitar congestiones de red que dan lugar a ineficacias de escala. La red CDN de Azure de Akamai soluciona esto mediante la optimización en tres áreas: 

 - eliminación del inicio lento
 - aprovechamiento de las conexiones persistentes
 - ajuste de los parámetros de paquete TCP

#### <a name="eliminating-slow-start"></a>Eliminación del inicio lento

El *inicio lento* forma parte del protocolo TCP que evita la congestión de la red mediante la limitación de la cantidad de datos enviados a través de la red. Comienza con tamaños de intervalo de congestión pequeños entre el remitente y el receptor hasta que se alcanza el máximo o se detecta la pérdida de paquetes.

La red CDN de Azure de Akamai elimina el inicio lento en tres pasos:
1.  La red distribuida de servidores perimetrales notifica con regularidad su uso, mantenimiento de la conexión, ancho de banda y otros parámetros a los sistemas de supervisión de red de Akamai. 
2.  Los informes consolidados se comparten con todos los servidores perimetrales de la plataforma. De esta forma, todos los servidores perimetrales conocen la existencia del estado de los servidores perimetrales alrededor de ellos. 
3.  Los servidores perimetrales de la red CDN son capaces de realizar suposiciones sobre algunos parámetros de transmisión, como cuál debe ser el tamaño de intervalo óptimo al comunicarse con otros servidores perimetrales de la red CDN cercanos. Esto significa que se puede aumentar el tamaño inicial del intervalo de congestión si el estado de la conexión entre los servidores perimetrales de la red CDN admite transferencias de datos de paquete mayores. 

#### <a name="leveraging-persistent-connections"></a>Aprovechamiento de las conexiones persistentes

Mediante una red CDN, menos equipos únicos se conectan directamente al servidor de origen en comparación con los usuarios que se conectan directamente al origen. La red CDN de Azure de Akamai también agrupa las solicitudes de usuario para establecer menos conexiones con el origen.

Como se mencionó anteriormente, las conexiones TCP toman varias solicitudes en ambos sentidos en un protocolo de enlace para establecer una conexión nueva. Las conexiones persistentes, también conocidas como "HTTP Keep-Alive", reutilizan las conexiones TCP existentes para varias solicitudes HTTP para ahorrar tiempos de ida y vuelta, y acelerar la entrega. 

#### <a name="tuning-tcp-packet-parameters"></a>Ajuste de los parámetros de paquete TCP
La red CDN de Azure de Akamai también ajusta los parámetros que controlan las conexiones de servidor a servidor y reduce la cantidad de recorridos de ida y vuelta prolongados necesarios para recuperar el contenido insertado en el sitio haciendo lo siguiente:
1.  Aumentando el intervalo de congestión inicial de modo que se puedan enviar más paquetes sin tener que esperar una confirmación.
2.  Reduciendo el tiempo de espera de retransmisión inicial para que se detecten las pérdidas y la retransmisión se produzca con más rapidez.
3.  Reduciendo el tiempo de espera de retransmisión mínimo y máximo para reducir el tiempo de espera antes de asumir que los paquetes se han perdido durante la transmisión.

### <a name="object-prefetch"></a>Captura previa de objetos
La mayoría de sitios web tienen una página HTML que hace referencia a otros recursos como imágenes y scripts. Normalmente, cuando un cliente solicita una página web, el explorador primero descarga y analiza el objeto HTML y, después, realiza solicitudes adicionales a los recursos vinculados que son necesarios para cargar la página completa. 

La *captura previa* es una técnica para recuperar imágenes y scripts insertados en la página HTML mientras el código HTML se envía al explorador, y antes de que el explorador realice incluso estas solicitudes de objeto. 

Al activar la opción de **captura previa** cuando la red CDN envía la página base HTML al explorador del cliente, la red CDN analizará el archivo HTML y realizará solicitudes adicionales para todos los recursos vinculados, y lo almacenará en la memoria caché. Cuando el cliente realiza las solicitudes de los recursos vinculados, el servidor perimetral de la red CDN ya tendrá los objetos solicitados y puede enviarlos inmediatamente sin un recorrido de ida y vuelta al origen. Esta optimización beneficiará tanto al contenido que se puede almacenar en caché como al que no se puede almacenar en caché.

## <a name="adaptive-image-compression"></a>Compresión adaptable de imágenes

Algunos dispositivos, especialmente los dispositivos móviles, experimentan en ocasiones velocidades de red más lentas. En estos casos, resulta más conveniente para el usuario recibir imágenes más pequeñas en su página web más rápidamente, en lugar de esperar mucho tiempo a las imágenes de resolución completa.

Esta característica supervisa automáticamente la calidad de la red y usa los métodos estándar de compresión JPEG cuando la velocidad de la red es más lenta para mejorar el tiempo de entrega.

Compresión adaptable de imágenes | Extensiones de archivo  
--- | ---  
Compresión JPEG | .jpg, .jpeg, .jpe, .jig, .jgig, .jgi





