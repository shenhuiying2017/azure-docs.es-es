---
title: Preguntas frecuentes sobre precios de Azure Network Performance Monitor | Microsoft Docs
description: 'Preguntas frecuentes: Azure Network Performance Monitor'
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: cherylmc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: log-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/02/2018
ms.author: ajaycode
ms.openlocfilehash: 5b2335ee2584af07ed23ce87be92a869f3a07ba1
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/06/2018
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Cambios de precios de Azure Network Performance Monitor

Hemos escuchado sus comentarios y recientemente hemos introducido una [nueva experiencia de precios](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) para diversos servicios de supervisión en Azure.

En este documento se incluyen los cambios de precios relacionados con Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM), en un formato de preguntas y respuestas fácil de leer.

Network Performance Monitor consta de tres componentes:
* [Supervisión del rendimiento](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor de puntos de conexión de servicio](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [Supervisión de ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

En la siguiente sección se describen los cambios de precios de los componentes anteriores.

## <a name="performance-monitor-pm"></a>Monitor de rendimiento (PM)

**Cómo se facturaba el uso del Monitor de rendimiento en el modelo antiguo?**

La facturación de NPM se basaba en el uso o consumo de dos componentes:
* Nodos: todas las transacciones sintéticas se originan y terminan en los nodos. Los nodos también se conocen como agentes o MMA (agentes de administración de Microsoft).
* Datos: los resultados de las distintas pruebas de red se almacenan en el repositorio de Log Analytics.

En el modelo anterior, la factura se calculaba según el número de nodos y el volumen de datos generado. 

**¿Cómo se cobra el uso del Monitor de rendimiento en el nuevo modelo?**

La característica Monitor de rendimiento en NPM ahora se factura en una combinación de: 

* vínculos de subred supervisados y
* volumen de datos.

**¿Qué es un vínculo de subred?**

El Monitor de rendimiento supervisa la conectividad entre dos o más ubicaciones de la red.  La conexión entre un grupo de nodos o agentes de una subred y un grupo de nodos de otra se conoce como un vínculo de subred.

**Tengo dos subredes (subred A y B) y varios agentes en cada subred.  El Monitor de rendimiento supervisa la conectividad de todos los agentes de la subred A todos los agentes de la subred B. ¿Se me cobrará en función del número de conexiones entre subredes?**

Nº Con fines de facturación, todas las conexiones de la subred A la subred B se agrupan en un vínculo de subred y se le cobra por una única conexión.  El Monitor de rendimiento sigue supervisando la conectividad entre varios agentes de cada subred.

**¿Cuáles son los costos por supervisar un vínculo de subred?**

Consulte la sección titulada [Ping Mesh](https://azure.microsoft.com/pricing/details/network-watcher/) para información sobre el costo de supervisión de un único vínculo de subred durante el mes entero.

**¿Cuáles son los cargos por los datos generados por el Monitor de rendimiento?**

Los cargos por ingesta (carga de datos en Log Analytics, procesamiento e indexación) están disponibles en la [página de precios](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics.  (Sección: Ingesta de datos).

Los cargos por la retención de datos (es decir, los datos retenidos en la opción del cliente, más allá del primer mes) también están disponibles en la [página de precios](https://azure.microsoft.com/pricing/details/log-analytics/).  (Sección: Retención de datos).


## <a name="expressroute-monitor-erm"></a>Supervisión de ExpressRoute (ERM)

**¿Cuáles son los cargos por el uso de Supervisión de ExpressRoute?**

Los cargos por Supervisión de ExpressRoute se facturan según el volumen de los datos generados durante la supervisión.   Consulte la pregunta "¿Cuáles son los cargos por los datos generados por el Monitor de rendimiento?" para más información.

**Uso ERM para supervisar varios circuitos ExpressRoute. ¿Se me cobra según el número de circuitos que se supervisan?**

No se le cobra en función del número de circuitos o el tipo de emparejamiento (por ejemplo, emparejamiento privado, emparejamiento de Microsoft).  Se le cobra según el volumen de datos, como se explicó anteriormente.

**¿Cuál es el volumen de datos generados al supervisar un único circuito?**

El volumen de datos generados por mes, cuando se supervisa una conexión de emparejamiento privado es el siguiente:

|Percentil      |Datos/mes (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Según la tabla anterior, los clientes del percentil 50 pagan por 192 MB de datos. El costo en que se incurre por supervisar un circuito, a 2,30 USD por GB, es de 0,43 USD (= 192 * 2,30 / 1024) durante el primer mes.

**¿Cuáles son algunas de las razones de las variaciones en el volumen de datos?**

El volumen de datos de supervisión generados depende de varios factores, como por ejemplo:
* número de agentes: la precisión del aislamiento de errores aumenta con un aumento en el número de agentes.
* número de saltos en la red.
* número de rutas de acceso entre el origen y el destino.

Normalmente, los clientes de los percentiles más altos (en la tabla anterior) supervisan sus circuitos desde varios puntos estratégicos de su red local.  También se colocan varios agentes a mayor profundidad de la red, más alejados del enrutador perimetral del proveedor de servicios. Con frecuencia, los agentes se colocan en varios sitios de usuario, ramas y bastidores en los centros de datos.

## <a name="service-endpoint-monitor-sepm"></a>Monitor de puntos de conexión de servicio (SEPM)

**¿Cuáles son los cargos por el uso del Monitor de puntos de conexión de servicio?**

Los cargos por el uso del Monitor de puntos de conexión de servicio se calculan en función del:
* Número de conexiones
* Volumen de datos

**¿Qué es una conexión?**

Una conexión es una prueba de accesibilidad a un punto de conexión (servicio de red o la dirección URL) desde un único agente durante todo el mes. Por ejemplo, la supervisión de una conexión a bing.com desde tres agentes constituye tres conexiones.

**¿Cuál es el costo del Monitor de puntos de conexión de servicio?**

- Para conocer el costo de supervisión de un punto de conexión durante todo el mes, consulte [Supervisión de conexiones](https://azure.microsoft.com/pricing/details/network-watcher/).
- La carga de datos está disponible en la [página de precios](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics.  (Sección: Ingesta de datos).

## <a name="references"></a>Referencias

- [Preguntas frecuentes sobre los precios de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/): la sección de preguntas frecuentes contiene información sobre el nivel gratuito, los precios por nodo, etc.

