---
title: "Información general de Azure Time Series Insights | Microsoft Docs"
description: "Introducción a Azure Time Series Insight, un nuevo servicio de análisis de datos de series de tiempo y soluciones de IoT"
keywords: 
services: tsi
documentationcenter: 
author: op-ravi
manager: jhubbard
editor: 
ms.assetid: 
ms.service: tsi
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: omravi
ms.openlocfilehash: 1814459e47280af62450a4093140ab6ab9b765fc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="what-is-azure-time-series-insights"></a>¿Qué es Azure Time Series Insights?

Azure Time Series Insights es un servicio en la nube administrado con componentes de almacenamiento, análisis y visualización que hacen más fácil ingerir, almacenar, explorar y analizar miles de millones de eventos al mismo tiempo. Time Series Insights proporciona una vista global de los datos, lo que le permite validar las soluciones de IoT de forma rápida y evitar costosos tiempos de inactividad en dispositivos, además de ayudarle a detectar tendencias ocultas y anomalías, y realizar análisis de causas principales casi en tiempo real. Time Series Insights ingiere datos de series temporales de agentes de eventos (por ejemplo, IoT Hubs o Event Hubs), indexa los datos y los retira en función de una directiva de retención configurable. Los usuarios consumen los datos a través de una experiencia del usuario intuitiva o a través de API de consulta de REST.

![Información general de Time Series Insight](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Escenarios principales

* Supervisar y validar soluciones de IoT en minutos.
* Visualizar y analizar datos de IoT a escala.
* Análisis rápido de causas principales y detección de anomalías.
* Crear una vista global de múltiples dispositivos, plantas y datos.

## <a name="capabilities-and-benefits"></a>Funcionalidades y ventajas

* **Es fácil empezar**: Azure Time Series Insights no requiere ninguna preparación de datos iniciales y es increíblemente rápido. Conéctese a miles de millones de eventos en Azure IoT Hub o el centro de eventos en minutos. Una vez conectado, visualice los datos de sensores e interactúe con ellos en cuestión de segundos para validar las soluciones de IoT con rapidez. Time Series Insights es fácil de usar; puede interactuar con los datos sin escribir una sola línea de código.  No hay que aprender ningún lenguaje nuevo; Time Series Insights proporciona una superficie de consultas granular y no textual para usuarios avanzados, así como exploración basada en clics para todos.

* **Información casi en tiempo real**: Time Series Insights puede ingerir centenares de millones de eventos de sensores al día, con una latencia de un minuto, por lo que le permitirá reaccionar rápidamente a los cambios. Time Series Insights ayuda a obtener información detallada sobre los datos de sensores, contribuyendo a detectar tendencias y anomalías rápido, llevar a cabo fácilmente análisis de causa principal complejos y evitar costosos tiempos de inactividad. Al habilitar la correlación entre los datos en tiempo real e históricos, Time Series Insights le ayuda a desvelar tendencias ocultas en los datos.

* **Crear soluciones personalizadas**: inserte los datos de Azure Time Series Insights en aplicaciones existentes o cree nuevas soluciones personalizadas con las API de REST de Time Series Insights. Al crear y compartir vistas personalizadas, permite a otros usuarios explorar sus descubrimientos.

* **Escalabilidad**: Time Series Insights está diseñado para admitir IoT a escala. En la versión preliminar, puede incorporar de 1 a 100 millones de eventos al día, con un tiempo predeterminado de retención de 31 días. Puede visualizar y analizar flujos de datos activos casi en tiempo real, junto con grandes cantidades de datos históricos. Más adelante, las tasas de incorporación y retención aumentarán para adaptarse a una escala empresarial en constante evolución.

## <a name="time-series-insights-glossary"></a>Glosario de Time Series Insights

* **Entorno**: un entorno es un recurso de Azure con funcionalidades de incorporación y almacenamiento.  Los clientes aprovisionan los entornos mediante Azure Portal con la capacidad necesaria.
* **Origen de eventos**: un origen de eventos se deriva de un agente de eventos, como Azure Event Hubs.  Time Series Insights se conecta directamente a orígenes de eventos e ingiere el flujo de datos sin necesidad de escribir código. Actualmente, Time Series Insights es compatible tanto con Azure Event Hubs como con Azure IoT Hubs.
* **Datos de referencia**: Time Series Insights proporciona a los usuarios la posibilidad de combinar datos de series temporales con datos de referencia.  Los datos de referencia pueden incluir metadatos relacionados con dispositivos u otros datos estáticos que varíen con poca frecuencia. Time Series Insights combina datos de referencia con flujos de datos, lo que permite a los usuarios visualizar y analizar los datos casi en tiempo real.
