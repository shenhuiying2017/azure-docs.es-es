---
title: "¿Qué es Azure Time Series Insights? | Microsoft Docs"
description: "Introducción a Azure Time Series Insights, un nuevo servicio de análisis de datos de series temporales y soluciones de IoT"
services: time-series-insights
ms.service: time-series-insights
author: ashannon7
ms.author: anshan
manager: jhubbard
editor: MarkMcGeeAtAquent
ms.reviewer: v-mamcge, jasonh, kfile, anshan
ms.workload: big-data
ms.topic: article
ms.date: 11/15/2017
ms.openlocfilehash: 2a0d3e829ed28912b56a022aa6aa82ade1718e35
ms.sourcegitcommit: e19f6a1709b0fe0f898386118fbef858d430e19d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2018
---
# <a name="what-is-azure-time-series-insights"></a>¿Qué es Azure Time Series Insights?

Time Series Insights se ha compilado para almacenar, visualizar y consultar grandes cantidades de datos de series temporales, como los generados por los dispositivos de IoT.  Si desea almacenar, administrar, consultar o visualizar los datos de una serie temporal en la nube, probablemente Time Series Insights sea lo que busca.  

Time Series Insights tiene cuatro trabajos clave:

- En primer lugar, está totalmente integrado con las puertas de enlace en la nube, como Azure IoT Hub y Azure Event Hubs. Y se conecta fácilmente a estos orígenes de eventos y analiza JSON desde mensajes y estructuras que tienen datos en columnas y filas limpias. Combina metadatos con telemetría e indexa los datos en un almacén en columnas.
- En segundo lugar, Time Series Insights administra el almacenamiento de los datos. Para garantizar que se puede acceder fácilmente a los datos en todo momento, almacena los datos en memoria y en SSD hasta 400 días. Puede consultar interactivamente miles de millones de eventos en segundos, a petición.
- En tercer lugar, Time Series Insights proporciona de forma estándar visualización mediante el explorador de TSI.  
- En cuarto lugar, Time Series Insights proporciona un servicio de consulta, tanto en el explorador de TSI como mediante el uso de API que son fáciles de integrar para incrustar datos de una serie temporal en aplicaciones personalizadas.  

Si va a compilar una aplicación, para consumo interno o para que la usen clientes externos, Time Series Insights puede utilizarse como back-end para la indexación, el almacenamiento y la agregación de datos de series temporales. A partir de aquí, puede compilar una experiencia de visualización y usuario personalizada.  Time Series Insights expone varias API de consulta para permitir este escenario.  

Si no está seguro de si los datos pertenecen a una serie temporal, esto es lo que debe saber.  Los datos de serie temporal representan cómo cambia un recurso o un proceso con el tiempo.  Son únicos por la marca de tiempo y el tiempo y son más significativos como eje.  Normalmente, los datos de serie temporal llegan en orden cronológico y suelen tratarse como inserción en lugar de actualización de la base de datos.  Dado que Time Series Insights captura y almacena los eventos nuevos como registros, el cambio se mide a lo largo del tiempo, lo cual permite buscar hacia atrás y predecir cambios.  En grandes volúmenes, almacenar, indexar, consultar, analizar y visualizar datos de series temporales pueden suponer un desafío.  

## <a name="primary-scenarios"></a>Escenarios principales

- Almacenamiento de datos de serie temporal de forma escalable.  
  - En esencia, Time Series Insights tiene una base de datos diseñada para datos de serie temporal.  Dado que es totalmente administrado y escalable, Time Series Insights realiza el trabajo de almacenar y administrar los eventos.

- Exploración de datos casi en tiempo real.  
  - Time Series Insights proporciona un explorador que visualiza todos los flujos de datos en un entorno.  Poco después de conectar un origen de eventos, los datos del evento se ven, exploran y consultan desde Time Series Insights.  Los datos son útiles para validar si un dispositivo emite los datos según lo previsto y para supervisar el estado, la productividad y la eficacia global de un recurso de IoT.  

- Análisis de las causas principales y detección de anomalías.
  - Time Series Insights tiene herramientas como patrones y vistas de perspectiva para llevar a cabo y guardar análisis de las causas principales de varios pasos.  Además, Time Series Insights funciona junto con servicios de alerta, como Azure Stream Analytics, para que las alertas y las anomalías detectadas se puedan ver casi en tiempo real en el explorador del primero.  

- Una vista global de los flujos de datos de serie temporal desde distintas ubicaciones permite la comparación entre varios recursos/sitios.
  - Puede conectar varios orígenes de eventos a un entorno de Time Series Insights.  Esto significa que los flujos de datos de varias ubicaciones dispares se pueden ver juntos casi en tiempo real.  Los usuarios pueden aprovechar esta visibilidad para compartir datos con líderes empresariales y permitir una mejor colaboración con expertos de dominio que puedan aplicar sus conocimientos para ayudar a resolver problemas, aplicar los procedimientos recomendados y compartir conocimientos.

- Compilación de una aplicación cliente a partir de Time Series Insights. 
  - Time Series Insights expone varias API de consulta de REST para que pueda compilar aplicaciones que usen datos de serie temporal.

## <a name="capabilities"></a>Capacidades

- **Inicio rápido**: Azure Time Series Insights no requiere la preparación inicial de los datos. Conéctese a millones de eventos en Azure IoT Hub o Event Hubs en minutos. Una vez conectado, visualice los datos de los sensores e interactúe con ellos para validar las soluciones de IoT con rapidez. Puede interactuar con los datos sin escribir código.
No hay que aprender ningún lenguaje nuevo; Time Series Insights proporciona una superficie de consultas granular y atextual para usuarios avanzados y permite apuntar y hacer clic como método de exploración.
- **Información casi en tiempo real**: Time Series Insights ingiere millones de eventos de sensor al día, con una latencia de un minuto. Time Series Insights ayuda a obtener información detallada sobre los datos de los sensores, al contribuir a detectar tendencias y anomalías, a llevar a cabo análisis de causa principal y a evitar costosos tiempos de inactividad. Al habilitar la correlación entre los datos en tiempo real e históricos, Time Series Insights le ayuda a desvelar tendencias ocultas en los datos.
- **Compilación de soluciones personalizadas**: inserte los datos de Azure Time Series Insights en aplicaciones existentes o cree soluciones personalizadas con las API de REST de Time Series Insights. Cree vistas personalizadas para compartir sus conocimientos con otros usuarios.
- **Escalabilidad**: Time Series Insights está diseñado para admitir IoT a escala. Puede incorporar de 1 a 100 millones de eventos al día, con un tiempo predeterminado de retención de 31 días. Puede visualizar y analizar flujos de datos activos casi en tiempo real, junto con datos históricos. Más adelante, las tasas de incorporación y retención aumentarán para adaptarse a una escala empresarial.

## <a name="getting-started"></a>Introducción
Con los primeros pasos se tarda menos de 5 minutos. 

1.  Para empezar, incorpore un entorno de Time Series Insights a Azure Portal. 
2.  Conecte un origen de eventos, por ejemplo, Azure IoT Hub o Event Hubs.  
3.  Cargue los datos de referencia (no es un servicio adicional).
4.  Consulte los datos en minutos con el explorador de Time Series Insights.

## <a name="time-series-insights-explorer"></a>Explorador de Time Series Insights
Este diagrama muestra un ejemplo de datos de Time Series Insights vistos a través del explorador: ![Explorador de Time Series Insights] (media/time-series-insights-explorer/explorer4.png)


## <a name="next-steps"></a>pasos siguientes
 - [Exploración del explorador de Time Series Insights en un entorno de demostración](./time-series-quickstart.md)
 - [Plan your own Time Series Insights environment](time-series-insights-environment-planning.md) (Planeamiento de un entorno de Time Series Insights propio)

