---
title: "Información general de Azure Time Series Insights | Microsoft Docs"
description: "Introducción a Azure Time Series Insight, que es un nuevo servicio de análisis de datos de series de tiempo y soluciones de IoT"
keywords: 
services: time-series-insights
documentationcenter: 
author: op-ravi
manager: santoshb
editor: cgronlun
ms.assetid: 
ms.service: time-series-insights
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/20/2017
ms.author: omravi
translationtype: Human Translation
ms.sourcegitcommit: 1cc1ee946d8eb2214fd05701b495bbce6d471a49
ms.openlocfilehash: cc35c00bec18ee1d9ed52b29b68ddacc1136dc70
ms.lasthandoff: 04/26/2017

---

# <a name="overview-of-azure-time-series-insights"></a>Información general de Azure Time Series Insights

Azure Time Series Insights es un servicio en la nube totalmente administrado con componentes de almacenamiento, análisis y visualización que hacen increíblemente sencillo ingerir, almacenar, explorar y analizar miles de millones de eventos al mismo tiempo. Time Series Insights proporciona una vista global de los datos, lo que le permite validar las soluciones de IoT de forma rápida y evitar costosos tiempos de inactividad en dispositivos críticos, además de ayudarle a detectar tendencias ocultas y anomalías, y realizar análisis de causas principales casi en tiempo real. Time Series Insights ingiere datos de series temporales de agentes de eventos (por ejemplo, IoT Hubs o Event Hubs), indexa los datos y los retira en función de una directiva de retención configurable. Los usuarios consumen los datos de su escenario de negocio a través de una experiencia del usuario intuitiva o a través de API de REST de consulta.

![Información general de Time Series Insight](media/overview/time-series-insights-overview-flow.png)

## <a name="primary-scenarios"></a>Escenarios principales

* Validación y supervisión de soluciones de IoT en minutos
* Visualización intuitiva y análisis de datos de IoT a escala
* Análisis rápido de causa principal y detección de anomalías
* Creación de una vista global de múltiples dispositivos, plantas y datos

## <a name="key-capabilities-and-benefits"></a>Ventajas y principales capacidades

* **Facilita el inicio**: Azure Time Series no requiere ninguna preparación de datos iniciales y es increíblemente rápido, por lo que puede conectar con miles de millones de eventos en Azure IoT Hub o Event Hubs en minutos. Una vez conectado, puede visualizar los datos de los sensores e interactuar con ellos en cuestión de segundos para validar la solución de IoT con rapidez. Time Series Insights es increíblemente intuitivo y fácil de usar; puede interactuar con los datos sin tener que escribir una sola línea de código.  Además no hay que aprender ningún lenguaje nuevo, ya que Time Series Insights proporciona una superficie de consultas granular y no textual para usuarios avanzados, así como exploración basada en clics para usuarios principiantes.

* **Visualización de detalles casi en tiempo real en segundos**: obtenga más valor de sus series temporales de datos con los componentes de almacenamiento, análisis y visualización de Time Series Insights, todo en un único lugar. Time Series Insights puede ingerir centenares de millones de eventos de sensores al día, con una latencia de un minuto, por lo que le permitirá reaccionar rápidamente a los cambios. Time Series Insights proporciona información detallada sobre los datos de sensores, ayudando a detectar tendencias y anomalías rápido, lo que le permite llevar a cabo fácilmente análisis de causa principal complejos y evitar costosos tiempos de inactividad. Al habilitar la correlación entre los datos en tiempo real e históricos, Time Series Insights permite a los usuarios desvelar tendencias ocultas en sus datos.

* **Crear soluciones personalizadas**: inserte los datos de Azure Time Series Insights en aplicaciones existentes o cree nuevas soluciones personalizadas con las API de REST de Time Series Insights.  Además, crear y compartir vistas personalizadas es algo sencillo, y permite a otros usuarios explorar fácilmente las detecciones que haya realizado.

* **Escalabilidad**: Time Series Insights está diseñado para admitir una escala de IoT. En la versión preliminar, puede incorporar de 1 a 100 millones de eventos al día, con un tiempo predeterminado de retención de 31 días. Time Series Insights permite a los usuarios visualizar y analizar flujos de datos activos casi en tiempo real, junto con grandes cantidades de datos históricos. Más adelante, las tasas de incorporación y retención aumentarán para adaptarse a una escala empresarial en constante evolución.

## <a name="taxonomy-of-time-series-insights"></a>Taxonomía de Time Series Insights

* **Entorno**: un entorno es un recurso de Azure con funcionalidades de incorporación y almacenamiento.  Los clientes aprovisionan los entornos mediante Azure Portal con la capacidad necesaria.
* **Origen de eventos**: un origen de eventos se deriva de un agente de eventos, como Azure Event Hubs.  Time Series Insights se conecta directamente a los orígenes de eventos e ingiere el flujo de datos sin necesidad de que los usuarios escriban una sola línea de código. Actualmente, Time Series Insights admite Azure Event Hubs y Azure IoT Hubs y, en el futuro, se agregarán más orígenes de eventos.
* **Datos de referencia**: Time Series Insights proporciona a los usuarios la posibilidad de combinar datos de series temporales con datos de referencia.  Los datos de referencia pueden incluir metadatos relacionados con dispositivos u otros datos estáticos que varíen con poca frecuencia. Time Series Insights combina datos de referencia con flujos de datos, lo que permite a los usuarios visualizar y analizar los datos casi en tiempo real.

