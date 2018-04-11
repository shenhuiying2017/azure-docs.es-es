---
title: Información general sobre Azure Load Balancer | Microsoft Docs
description: Información general sobre las características, la arquitectura y la implementación del Equilibrador de carga de Azure Obtenga información acerca de cómo funciona el equilibrador de carga y aproveche sus ventajas en la nube.
services: load-balancer
documentationcenter: na
author: KumudD
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2018
ms.author: kumud
ms.openlocfilehash: f4410932f00f8505ae5a894caa002e1223196d95
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/30/2018
---
# <a name="azure-load-balancer-overview"></a>Información general sobre el Equilibrador de carga de Azure

Azure Load Balancer permite escalar las aplicaciones y crear alta disponibilidad para sus servicios. Load Balancer admite escenarios de entrada y de salida, proporciona baja latencia y alta capacidad de proceso, y puede escalar hasta millones de flujos para todas las aplicaciones TCP y UDP.   

Load Balancer distribuirá los nuevos flujos de entrada que lleguen a las instancias del grupo de front-end a back-end del equilibrador de carga, según las reglas y los sondeos de mantenimiento.  

Además, un equilibrador de carga público puede traducir direcciones IP privadas en direcciones IP públicas para proporcionar conexiones de salida para máquinas virtuales dentro de la red virtual.

Azure Load Balancer está disponible en dos SKU diferentes: Básico y Estándar.  Hay diferencias en la escala, las características y los precios.  Cualquier escenario posible con Load Balancer Básico puede crearse con Load Balancer Estándar, aunque el enfoque puede ser ligeramente diferente.  Para conocer Load Balancer, es importante familiarizarse con los conceptos básicos y las diferencias específicas de cada SKU.

## <a name="why-use-load-balancer"></a>¿Por qué usar Load Balancer? 

Azure Load Balancer puede usarse para lo siguiente:

* Equilibrar la carga del tráfico entrante de Internet entre las máquinas virtuales. Esta configuración se conoce como [equilibrador de carga interno](#publicloadbalancer).
* Equilibrar la carga del tráfico entre las máquinas virtuales dentro de una red virtual. También puede tener acceso a un servidor front-end de Load Balancer desde una red local en un escenario híbrido. Ambos escenarios utilizan una configuración que se conoce como [equilibrador de carga interno](#internalloadbalancer).
* Reenviar el tráfico a un puerto específico de máquinas virtuales determinadas con reglas NAT de entrada.
* Proporcionar [conectividad de salida](load-balancer-outbound-connections.md) para máquinas virtuales dentro de la red virtual mediante el uso de un equilibrador de carga público.


>[!NOTE]
> Azure ofrece un conjunto de soluciones de equilibrio de carga completamente administradas para sus escenarios.  Si desea finalización de conexiones TLS ("descarga SSL") o procesamiento de capas de aplicación por solicitud HTTP/HTTPS, consulte [Application Gateway](../application-gateway/application-gateway-introduction.md).  Si desea equilibrio de carga de DNS global, consulte [Traffic Manager](../traffic-manager/traffic-manager-overview.md).  Sus escenarios pueden beneficiarse de extremo a extremo de la combinación de estas soluciones según sea necesario.

## <a name="what-is-load-balancer"></a>¿Qué es el Equilibrador de carga?

Un recurso de Load Balancer puede existir como un equilibrador de carga público o un equilibrador de carga interno. Las funciones de un recurso de Load Balancer se expresan como un front-end, una regla, un sondeo de mantenimiento y una definición de grupo de back-end.  Las máquinas virtuales se colocan en el grupo de back-end; para ello, se especifica el grupo de back-end desde la máquina virtual.

Los recursos de Load Balancer son objetos mediante los cuales se puede expresar cómo debe Azure programar su infraestructura multiinquilino para lograr el escenario que se desea crear.  No hay ninguna relación directa entre los recursos de Load Balancer y la infraestructura real; crear un equilibrador de carga no crea una instancia y su capacidad está siempre disponible. 

## <a name="fundamental-load-balancer-features"></a>Características fundamentales de Load Balancer

Load Balancer proporciona las siguientes funcionalidades básicas para las aplicaciones TCP y UDP:

* **Equilibrio de carga**

    Azure Load Balancer permite crear una regla de equilibrio de carga para distribuir el tráfico que llega a un front-end a las instancias del grupo de back-end.  Usa un algoritmo hash para la distribución de los flujos de entrada y, después, reescribe los encabezados de los flujos en las instancias del grupo de back-end. Un servidor está disponible para recibir nuevos flujos cuando el sondeo de estado indica un punto de conexión de back-end correcto.
    
    De forma predeterminada, usa un hash de tupla 5 compuesto por IP de origen, puerto de origen, IP de destino, puerto de destino y número de protocolo IP para asignar los flujos a los servidores disponibles.  Puede elegir crear afinidad con una dirección IP de origen específica; para ello, elija un hash de tupla 2 o 3 para una regla determinada.  Todos los paquetes del mismo flujo de paquetes llegan a la misma instancia subyacente al servidor front-end con equilibrio de carga.  Cuando el cliente inicia un nuevo flujo desde la misma dirección IP de origen, el puerto de origen cambia. Como resultado, la tupla 5 resultante puede hacer que el tráfico vaya a un punto de conexión de back-end diferente.

    Para más información, consulte [Modo de distribución de Load Balancer](load-balancer-distribution-mode.md). En el siguiente gráfico se muestra la distribución basada en hash:

    ![Distribución basada en hash](./media/load-balancer-overview/load-balancer-distribution.png)

    *Figura: Distribución basada en hash*

* **Reenvío de puertos**

    Azure Load Balancer permite crear una regla NAT de entrada para reenviar el tráfico desde un puerto específico de la dirección IP de un servidor front-end específico a un puerto específico de una instancia de back-end específica dentro de la red virtual. Esto también se realiza con la misma distribución basada en hash que se usa para el equilibrio de carga.  Los escenarios comunes para esta funcionalidad son sesiones de Protocolo de Escritorio remoto (RDP) o Secure Shell (SSH) en instancias de máquina virtual individuales dentro de la red virtual.  Puede asignar varios puntos de conexión internos a los diferentes puertos de la misma dirección IP de front-end. Estos se pueden usar para administrar las máquinas virtuales de forma remota a través de Internet sin necesidad de un servidor de salto adicional.

* **Independiente de la aplicación y transparente**

    Load Balancer no interactúa directamente con TCP, UDP ni el nivel de aplicación, y admite cualquier escenario de aplicación TCP o UDP.  Por ejemplo, aunque Load Balancer no termina las conexiones TLS por sí mismo, se pueden compilar y escalar horizontalmente aplicaciones TLS mediante Load Balancer y terminar la conexión TLS en la propia máquina virtual. Load Balancer no termina un flujo y protocolos de enlace se realizan siempre directamente entre el cliente y la instancia del grupo de back-end seleccionada por hash. Por ejemplo, un protocolo de enlace TCP se realiza siempre entre el cliente y la máquina virtual de back-end seleccionada.  Y una respuesta a una solicitud para un front-end es una respuesta generada en la máquina virtual de back-end.  El rendimiento de red de salida de Load Balancer solo está limitado por la SKU de máquina virtual que elija, y los flujos permanecerán activos durante largos períodos de tiempo si nunca se alcanza el tiempo de espera de inactividad.

* **Reconfiguración automática**

    El Equilibrador de carga de Azure se reconfigura inmediatamente al escalar instancias horizontalmente o reducirlas verticalmente. Al agregar o quitar máquinas virtuales del grupo de back-end, se vuelve a configurar el equilibrador de carga sin necesidad de operaciones adicionales en el recurso de Load Balancer.

* **Sondeos de mantenimiento**

    Azure Load Balancer usa los sondeos de mantenimiento que defina para determinar el mantenimiento de las instancias en el grupo de back-end. Cuando un sondeo no responde, el equilibrador de carga deja de enviar nuevas conexiones a las instancias incorrectas. Las conexiones existentes no se ven afectadas y continuarán hasta que la aplicación finalice el flujo, se produzca un tiempo de inactividad o se apague la máquina virtual.

    Se admiten tres tipos de sondeos:

    - **Sondeo HTTP personalizado**: puede usarlo para crear su propia lógica personalizada para determinar el estado de una instancia del grupo de back-end. El equilibrador de carga sondeará periódicamente el punto de conexión (de forma predeterminada, cada 15 segundos). La instancia se considera correcta si responde con HTTP 200 antes de que finalice el tiempo de espera (el valor predeterminado es 31 segundos). Cualquier estado distinto a HTTP 200 hace que se produzca un error en este sondeo.  Esto resulta útil también para implementar su propia lógica con el fin de quitar instancias de la rotación del equilibrador de carga. Por ejemplo, puede configurar la instancia para devolver un estado no 200 si la instancia está por encima del 90% de la CPU.   El sondeo invalida el sondeo predeterminado del agente invitado.

    - **Sonda personalizada de TCP:** esta se basa en el establecimiento correcto de sesiones TCP para un puerto de sondeo definido.  Siempre que exista el agente de escucha especificado en la máquina virtual, este sondeo se realizará correctamente. Si se rechaza la conexión, se producirá un error en el sondeo. El sondeo invalida el sondeo predeterminado del agente invitado.

    - **Sondeo de agente invitado (solo en máquinas virtuales de PaaS)**: el equilibrador de carga puede usar también el agente invitado dentro de la máquina virtual. El agente invitado escucha y responde con una respuesta HTTP 200 OK solo cuando la instancia está preparada. Si no responde con HTTP 200 OK, el equilibrador de carga marca la instancia como sin respuesta y deja de enviarle tráfico. El equilibrador de carga continúa intentando conectar con la instancia. Si el agente invitado responde con HTTP 200, el equilibrador de carga le enviará tráfico nuevamente.  Los sondeos de agente de invitado son el último recurso y no deben usarse cuando son posibles configuraciones de sondeo personalizado HTTP o TCP. 
    
* **Conexiones de salida (NAT de origen)**

    Todos los flujos de salida desde direcciones IP privadas dentro de la red virtual a direcciones IP públicas en Internet se pueden traducir a una dirección IP de front-end del equilibrador de carga. Cuando un front-end público está asociado a una máquina virtual de back-end por una regla de equilibrio de carga, Azure programa conexiones de salida que se deben traducir automáticamente a la dirección IP del front-end público. Esto también se denomina NAT de origen (SNAT). SNAT proporciona ventajas importantes:

    + Permite la actualización y la recuperación ante desastres de servicios de forma fácil, puesto que el front-end puede asignarse dinámicamente a otra instancia del servicio.
    + Facilita la administración de la lista de control de acceso (ACL). Las ACL que se expresan en términos de IP de front-end no cambian cuando los servicios se escalan o se reducen verticalmente, o se vuelven a implementar.

    Consulte el artículo sobre [conexiones de salida](load-balancer-outbound-connections.md) para obtener una explicación detallada de esta funcionalidad.

Load Balancer Estándar tiene funcionalidades específicas de cada SKU además de estos aspectos básicos.  Consulte el resto de este artículo para obtener más información.

## <a name="skus"></a> Comparación de las SKU de Load Balancer

Azure Load Balancer admite dos SKU diferentes: Básico y Estándar.  Hay diferencias en la escala del escenario, las características y los precios.  Cualquier escenario posible con Load Balancer Básico puede crearse con Load Balancer Estándar también.  De hecho, las API para ambas SKU son similares y se invocan especificando una SKU.  La API que admite las SKU de Load Balancer y la dirección IP pública es la versión 2017-08-01 y posteriores.  Ambas SKU tiene la misma API general y estructura.

Sin embargo, según la SKU que se elija, la configuración del escenario completo puede ser ligeramente diferente. La documentación de Load Balancer indica si un artículo se aplica únicamente a una SKU específica. Consulte la tabla siguiente para comparar y comprender las diferencias.  Consulte [Introducción a Azure Load Balancer Estándar](load-balancer-standard-overview.md) para obtener más información.

>[!NOTE]
> Para los nuevos diseños, deberá considerarse el uso de Load Balancer Estándar. 

Las máquinas virtuales independientes, los conjuntos de disponibilidad y los conjuntos de escalado de máquinas virtuales solo se pueden conectar a una SKU, nunca ambas. Cuando se usa con direcciones IP públicas, la SKU de Load Balancer y la dirección IP pública deben coincidir. Las SKU de Load Balancer y de la dirección IP pública no se pueden cambiar.

_El procedimiento recomendado es especificar las SKU de forma explícita, aunque aún no es obligatorio._  En este momento, se están realizando los cambios necesarios mínimos. Si no se especifica una SKU, se interpreta que la intención es usar la SKU Básico en la versión de API 2017-08-01.

>[!IMPORTANT]
>Load Balancer Estándar es un nuevo producto de Load Balancer y, en gran medida, un superconjunto de Load Balancer Básico.  Existen diferencias importantes y deliberadas entre ambos productos.  Cualquier escenario posible con Load Balancer Básico puede crearse con Load Balancer Estándar.  Si ya está acostumbrado a Load Balancer Básico, debe familiarizarse con Load Balancer Estándar para comprender los cambios importantes en el comportamiento de ambos, y su impacto. Consulte detenidamente esta sección.

| | [SKU estándar](load-balancer-standard-overview.md) | SKU Básico |
| --- | --- | --- |
| Tamaño de grupo de back-end | Hasta 1000 instancias | Hasta 100 instancias |
| Puntos de conexión del grupo de back-end | Cualquier máquina virtual en una única red virtual, incluida la combinación de máquinas virtuales, conjuntos de disponibilidad y grupos de escalado de máquinas virtuales. | Máquinas virtuales en un único conjunto de disponibilidad o conjunto de escalado de máquinas virtuales. |
| Zonas de disponibilidad | Front-ends zonales y con redundancia de zona para la entrada y la salida, asignaciones de flujos de salida, supervivencia a errores de zona, equilibrio de carga entre zonas. | / |
| Diagnóstico | Azure Monitor, métricas multidimensionales, incluidos bytes y contadores de paquetes, estado de sondeo de mantenimiento, intentos de conexión (TCP SYN), mantenimiento de la conexión de salida (flujos SNAT correctos e incorrectos), medidas de planos de datos activos. | Azure Log Analytics solo para equilibradores de carga públicos, alerta de agotamiento de SNAT, recuento de mantenimientos del grupo de back-end. |
| Puertos HA | Equilibrador de carga interno | / |
| Seguro de forma predeterminada | Cerrado de forma predeterminada para puntos de conexión públicos de direcciones IP y Load Balancer, y debe usarse un grupo de seguridad de red para incluirlos en una lista de permitidos de forma explícita para que el tráfico fluya. | Abierto de forma predeterminada, grupo de seguridad de red opcional. |
| Conexiones de salida | Varios front-end con posibilidad de exclusión para cada regla. Un escenario de salida _debe_ crearse explícitamente para que la máquina virtual pueda usar la conectividad de salida.  [Los puntos de conexión del servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md) son accesibles sin conectividad de salida y no se cuentan como datos procesados.  Las direcciones IP públicas, incluidos los servicios de PaaS de Azure que no están disponibles como puntos de conexión de servicio de red virtual, deben ser accesibles mediante conectividad de salida y cuentan como datos procesados. Si solo hay un equilibrador de carga interno dando servicio a una máquina virtual, no hay disponibles conexiones de salida mediante SNAT predeterminada. La programación de SNAT de salida es específica del protocolo de transporte de la regla de equilibrio de carga de entrada. | Único front-end, seleccionado de forma aleatoria, cuando hay varios front-ends.  Cuando solo un equilibrador de carga interno da servicio a una máquina virtual, se usa SNAT predeterminada. |
| Varios servidores front-end | Entrada y salida | Solo de entrada |
| Operaciones de administración | La mayoría de las operaciones en menos de 30 segundos | Normalmente, entre 60 y 90 segundos |
| Contrato de nivel de servicio | 99,99 % para la ruta de acceso a los datos con dos máquinas virtuales correctas | SLA de máquina virtual implícito | 
| Precios | Se cobra según el número de reglas, los datos procesados de entrada o salida asociados con el recurso.  | Sin cargo |

Consulte los [límites de servicio de Load Balancer](https://aka.ms/lblimits).  Para Load Balancer Estándar, consulte más detalles en los artículos de [información general](load-balancer-standard-overview.md), [precios](https://aka.ms/lbpricing) y [SLA](https://aka.ms/lbsla).

## <a name="concepts"></a>Conceptos

### <a name = "publicloadbalancer"></a>Equilibrador de carga público

El equilibrador de carga público asigna la dirección IP pública y el número de puerto del tráfico de entrada a la dirección IP privada y el número de puerto de la máquina virtual, y viceversa, para el tráfico de respuesta desde la máquina virtual. Las reglas de equilibrio de carga permiten distribuir tipos específicos de tráfico entre varias máquinas virtuales o servicios. Por ejemplo, puede distribuir la carga del tráfico de solicitudes web entre varios servidores web.

En la siguiente ilustración, se muestra un punto de conexión de carga equilibrada para el tráfico web que se comparte entre tres máquinas virtuales en el puerto TCP público y privado de 80. Estas tres máquinas virtuales se encuentran en un conjunto con equilibrio de carga.

![ejemplo de equilibrador de carga público](./media/load-balancer-overview/IC727496.png)

*Figura: Equilibrio de carga del tráfico web mediante una instancia pública de Load Balancer*

Cuando los clientes de Internet envían solicitudes de página web a la dirección IP pública de una aplicación web en el puerto TCP 80, Azure Load Balancer distribuye las solicitudes entre las tres máquinas virtuales del conjunto con equilibrio de carga. Para más información sobre algoritmos del equilibrador de carga, consulte la [página de información general del equilibrador de carga](load-balancer-overview.md#load-balancer-features).

De forma predeterminada, Azure Load Balancer distribuye el tráfico de red equitativamente entre varias instancias de máquina virtual. También puede configurar la afinidad de la sesión. Para más información, consulte [Modo de distribución de Load Balancer](load-balancer-distribution-mode.md).

### <a name = "internalloadbalancer"></a>Equilibrador de carga interno

El equilibrador de carga interno solo dirige el tráfico a los recursos que están dentro de una red virtual o que utilizan una VPN para acceder a la infraestructura de Azure. En este sentido, el equilibrador de carga interno difiere del equilibrador de carga público. La infraestructura de Azure restringe el acceso a las direcciones IP de front-end con equilibrio de carga de una red virtual. Las direcciones IP de front-end y las redes virtuales no se exponen nunca directamente a un punto de conexión de Internet. Las aplicaciones de línea de negocio internas se ejecutan en Azure y se accede a ellas desde Azure o desde recursos locales.

El equilibrador de carga interno permite los siguientes tipos de equilibrio de carga:

* En una red virtual: equilibrio de carga entre las máquinas virtuales de la red virtual y un conjunto de máquinas virtuales que residen en la misma red virtual.
* En una red virtual entre entornos locales: equilibrio de carga entre los equipos locales y un conjunto de máquinas virtuales que residen en la misma red virtual. 
* En aplicaciones de varios niveles: equilibrio de carga para aplicaciones de varios niveles accesibles desde Internet, a cuyos niveles de back-end no se puede acceder desde Internet. Los niveles de back-end requieren un equilibrio de carga del tráfico desde el nivel accesible desde Internet (consulte la figura 2).
* En aplicaciones de línea de negocio: equilibrio de carga para las aplicaciones de línea de negocio hospedadas en Azure sin un hardware ni un software adicional al equilibrador de carga. Este escenario incluye servidores locales que se encuentran en el conjunto de equipos de cuyo tráfico se va a equilibrar la carga.

![Ejemplo de equilibrador de carga interno](./media/load-balancer-overview/IC744147.png)

*Figura: Equilibrio de carga de aplicaciones de niveles múltiples mediante instancias públicas e internas de Load Balancer*

## <a name="pricing"></a>Precios
Load Balancer Estándar es un producto que se carga según el número de reglas de equilibrio de carga configuradas y de todos los datos de entrada y salida procesados. Para más información sobre los precios de Load Balancer Estándar, visite la página [Precios de Load Balancer](https://azure.microsoft.com/pricing/details/load-balancer/).

Load Balancer Básico se ofrece sin cargo.

## <a name="sla"></a>Contrato de nivel de servicio

Para obtener información sobre el SLA de Load Balancer Estándar, visite la página [SLA de Load Balancer](https://aka.ms/lbsla). 

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Load Balancer Estándar con más detalle](load-balancer-standard-overview.md)
- Más información acerca de cómo usar [Load Balancer Estándar y zonas de disponibilidad](load-balancer-standard-availability-zones.md).
- Más información acerca de cómo usar [Load Balancer para conexiones salientes](load-balancer-outbound-connections.md).
- Más información acerca de [los puertos HA de Load Balancer](load-balancer-ha-ports-overview.md).
- Más información acerca de cómo usar [Load Balancer con varios front-ends](load-balancer-multivip-overview.md).
- Más información acerca de [Puntos de conexión de servicio de red virtual](../virtual-network/virtual-network-service-endpoints-overview.md).
- Más información acerca de cómo crear un [equilibrador de carga público básico](load-balancer-get-started-internet-portal.md).

