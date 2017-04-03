---
title: "Predicción del estado de vehículo y los hábitos de conducción - Azure | Microsoft Docs"
description: "Aproveche las posibilidades de Cortana Intelligence para obtener información en tiempo real y predictiva del estado de los vehículos y los hábitos de conducción."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: 3467c5549381f1354987fead424646afe847739c
ms.lasthandoff: 01/24/2017


---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Cuaderno de estrategias de soluciones de análisis de telemetría de vehículo
Este **menú** vincula a los capítulos de este cuaderno de estrategias. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Información general
Se han incorporado superequipos a coches vanguardistas, con una infinidad de sensores, lo que ofrece la posibilidad de realizar un seguimiento y de supervisar millones de eventos por segundo. Esperamos que para el 2020, la mayoría de estos vehículos se conectará a Internet. Imagínese explotar esta riqueza de datos para ofrecer la mejor seguridad, confiabilidad y experiencia de conducción de su clase. Microsoft ha convertido ese sueño en una realidad gracias a Cortana Intelligence.

La solución Cortana Intelligence de Microsoft es un conjunto de aplicaciones de análisis avanzados y macrodatos totalmente administrado que le permite transformar los datos en acciones inteligentes. Queremos presentarle la plantilla de la solución de análisis de telemetría de vehículos de Cortana Intelligence. Esta solución ilustra cómo los fabricantes y los concesionarios de automóviles, así como las compañías de seguros, pueden usar las funciones de Cortana Intelligence para obtener información predictiva y en tiempo real sobre el estado de los vehículos y los hábitos de conducción. 

La solución se implementa como un [patrón de arquitectura lambda](https://en.wikipedia.org/wiki/Lambda_architecture) en el que se muestran todas las posibilidades de la plataforma Cortana Intelligence para procesar datos por lotes y en tiempo real. La solución consigue lo siguiente: 

* Proporcionar un simulador telemático de vehículo
* Utilizar Event Hubs para la introducción de millones de eventos de telemetría de vehículo en Azure 
* Usar Stream Analytics para obtener información en tiempo real sobre el estado del vehículo
* Conservar los datos en un almacenamiento a largo plazo para realizar análisis por lotes más detallados 
* Aprovechar las ventajas de Machine Learning para la detección de anomalías en tiempo real y el procesamiento por lotes para obtener información predictiva
* Usar HDInsight para transformar los datos a escala y Data Factory controla la orquestación, la programación, la administración de recursos y la supervisión de la canalización del procesamiento por lotes 
* Ofrecer a esta solución un panel completo de datos en tiempo real y visualizaciones de análisis predictivo mediante Power BI

## <a name="architecture"></a>Arquitectura
![Diagrama de la arquitectura de la solución](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figura 1: Arquitectura de la solución de análisis de telemetría de vehículos*

Esta solución incorpora los siguientes **componentes de Cortana Intelligence** y presenta su integración completa:

* **Centros de eventos** para la introducción de millones de eventos de telemetría del vehículo en Azure.
* **Análisis de transmisiones** para obtener información en tiempo real sobre el estado del vehículo y conserva los datos en el almacenamiento a largo plazo para análisis de lotes más completo.
* **Aprendizaje automático** para la detección de anomalías en tiempo real y el procesamiento por lotes para obtener información predictiva.
* **HDInsight** se aprovecha para transformar datos a escala.
* **Factoría de datos** controla la orquestación, la programación, la administración de recursos y la supervisión de la canalización del procesamiento por lotes.
* **Power BI** ofrece a esta solución un panel completo para datos en tiempo real y visualizaciones de análisis predictivo.

Esta solución tiene acceso a dos **orígenes de datos**diferentes: 

* **Diagnósticos y señales de vehículos simuladas**: un simulador telemático de vehículo emite información de diagnóstico y señales que se corresponden con el estado del vehículo y el patrón de conducción en un momento determinado. 
* **Catálogo de vehículo**: un conjunto de datos de referencia que contiene una asignación de número de identificación del vehículo a modelo.


