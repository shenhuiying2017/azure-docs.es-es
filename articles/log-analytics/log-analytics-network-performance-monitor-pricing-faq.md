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
ms.openlocfilehash: 1e7e43dc2e7ed386f8f77fd1ab186d2ff34af405
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/28/2018
ms.locfileid: "32177006"
---
# <a name="pricing-changes-for-azure-network-performance-monitor"></a>Cambios de precios de Azure Network Performance Monitor

Hemos escuchado sus comentarios y recientemente hemos introducido una [nueva experiencia de precios](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/) para diversos servicios de supervisión en Azure. En este artículo se incluyen los cambios de precios relacionados con Azure [Network Performance Monitor](https://docs.microsoft.com/azure/networking/network-monitoring-overview) (NPM), en un formato de preguntas y respuestas fácil de leer.

Network Performance Monitor consta de tres componentes:
* [Supervisión del rendimiento](https://docs.microsoft.com/azure/networking/network-monitoring-overview#performance-monitor)
* [Monitor de puntos de conexión de servicio](https://docs.microsoft.com/azure/networking/network-monitoring-overview#service-endpoint-monitor)
* [Supervisión de ExpressRoute](https://docs.microsoft.com/azure/networking/network-monitoring-overview#expressroute-monitor)

En las secciones siguientes se explican los cambios de precios de los componentes NPM.

## <a name="performance-monitor"></a>Supervisión del rendimiento

**Cómo se facturaba el uso del Monitor de rendimiento en el modelo antiguo?**

La facturación de NPM se basaba en la utilización y consumo de dos componentes:
* **Nodos**: todas las transacciones sintéticas se originan y terminan en los nodos. Los nodos también se conocen como agentes o agentes de administración de Microsoft.
* **Datos**: los resultados de las distintas pruebas de red se almacenan en el repositorio de Azure Log Analytics.

En el modelo anterior, la factura se calculaba según el número de nodos y el volumen de datos generado. 

**¿Cómo se cobra la utilización del Monitor de rendimiento en el nuevo modelo?**

La característica Monitor de rendimiento en NPM ahora se factura según una combinación de: 

* Vínculos de subred supervisados
* Volumen de datos

**¿Qué es un vínculo de subred?**

El Monitor de rendimiento supervisa la conectividad entre dos o más ubicaciones de la red. La conexión entre un grupo de nodos o agentes de una subred y un grupo de nodos de otra se denomina vínculo de subred.

**Tengo dos subredes (A y B) y varios agentes en cada subred. El Monitor de rendimiento supervisa la conectividad de todos los agentes de la subred A con todos los agentes de la subred B. ¿Se me cobrará en función del número de conexiones entre subredes?**

Nº Con fines de facturación, todas las conexiones de la subred A con la subred B se agrupan en un vínculo de subred. Se le factura por una única conexión. El Monitor de rendimiento sigue supervisando la conectividad entre varios agentes de cada subred.

**¿Cuáles son los costos por supervisar un vínculo de subred?**

Para información sobre el costo de supervisión de un único vínculo de subred durante todo el mes, vea la sección [Malla de ping](https://azure.microsoft.com/pricing/details/network-watcher/).

**¿Cuáles son los cargos por los datos que el Monitor de rendimiento genera?**

Los cargos por ingesta (carga de datos en Log Analytics, procesamiento e indexación) están disponibles en la [página de precios](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics, en la sección Ingesta de datos. Los cargos por la retención de datos (es decir, los datos retenidos como opción del cliente, más allá del primer mes) también están disponibles en la [página de precios](https://azure.microsoft.com/pricing/details/log-analytics/), en la sección Retención de datos.


## <a name="expressroute-monitor"></a>Supervisión de ExpressRoute

**¿Cuáles son los cargos por el uso de Supervisión de ExpressRoute?**

Los cargos por Supervisión de ExpressRoute se facturan según el volumen de los datos generados durante la supervisión. Para más información, vea "¿Cuáles son los cargos por los datos que el Monitor de rendimiento genera?"

**Uso ExpressRoute Monitor para supervisar varios circuitos ExpressRoute. ¿Se me cobra según el número de circuitos que se supervisan?**

No se le cobra en función del número de circuitos o el tipo de emparejamiento (por ejemplo, emparejamiento privado, emparejamiento de Microsoft). Se le cobra según el volumen de datos, como se explicó previamente.

**¿Cuál es el volumen de datos generados cuando ExpressRoute supervisa un único circuito?**

El volumen de datos generados por mes, cuando ExpressRoute supervisa una conexión de emparejamiento privado es el siguiente:

|Percentil      |Datos/mes (MB)|
| :---:          |           ---:|
|50<sup>th</sup> |            192|
|60<sup>th</sup> |            256|
|70<sup>th</sup> |            360|
|80<sup>th</sup> |            498|
|90<sup>th</sup> |            870|
|95<sup>th</sup> |           1560|


Según esta tabla, los clientes del percentil 50 pagan por 192 MB de datos. El costo en generado por supervisar un circuito, a 2,30 USD por GB, es de 0,43 USD (= 192 * 2,30 / 1024) durante el primer mes.

**¿Cuáles son algunos de los motivos de las variaciones en el volumen de datos?**

El volumen de datos de supervisión generados depende de varios factores, como por ejemplo:
* Número de agentes. La precisión del aislamiento de errores aumenta con un aumento en el número de agentes.
* Número de saltos en la red.
* El número de rutas de acceso entre el origen y el destino.

Normalmente, los clientes de los percentiles más altos (en la tabla anterior) supervisan sus circuitos desde varios puntos estratégicos de su red local. También se colocan varios agentes a mayor profundidad de la red, más alejados del enrutador perimetral del proveedor de servicios. Con frecuencia, los agentes se colocan en varios sitios de usuario, ramas y bastidores en los centros de datos.

## <a name="service-endpoint-monitor"></a>Monitor de puntos de conexión de servicio

**¿Cuáles son los cargos por el uso del Monitor de puntos de conexión de servicio?**

Los cargos por la utilización del Monitor de puntos de conexión de servicio se calculan en función del:
* Número de conexiones
* Volumen de datos

**¿Qué es una conexión?**

Una conexión es una prueba de accesibilidad a un punto de conexión (servicio de red o la dirección URL) desde un único agente durante todo el mes. Por ejemplo, la supervisión de una conexión a bing.com desde tres agentes constituye tres conexiones.

**¿Cuál es el costo del Monitor de puntos de conexión de servicio?**

Para conocer el costo de supervisión de un punto de conexión durante todo el mes, consulte [Supervisión de conexiones](https://azure.microsoft.com/pricing/details/network-watcher/). El cargo por datos está disponible en la [página de precios](https://azure.microsoft.com/pricing/details/log-analytics/) de Log Analytics, en la sección Ingesta de datos.

## <a name="references"></a>Referencias

[Preguntas frecuentes sobre los precios de Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/): la sección de preguntas frecuentes contiene información sobre el nivel gratuito, los precios por nodo y otros detalles de los precios.

