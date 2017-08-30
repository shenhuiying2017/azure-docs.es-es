---
title: Evitar interrupciones de servicio con trabajos de Azure Stream Analytics | Microsoft Docs
description: "Guía sobre cómo realizar la actualización resistente de los trabajos de Stream Analytics."
services: stream-analytics
documentationCenter: 
authors: samacha
manager: jhubbard
editor: cgronlun
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 03/28/2017
ms.author: samacha
ms.translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: de803447ec379f35b453890d92359a91f4fd8427
ms.contentlocale: es-es
ms.lasthandoff: 03/06/2017

---

# <a name="guarantee-stream-analytics-job-reliability-during-service-updates"></a>Garantía de la confiabilidad del trabajo de Stream Analytics durante las actualizaciones del servicio

Parte de ser un servicio completamente administrado es la capacidad de introducir nuevas funcionalidades de servicio y mejoras a un ritmo rápido. Como resultado, Stream Analytics puede tener una actualización de servicio implementada de forma semanal (o más frecuentemente). Con independencia de cuántas pruebas se realicen, sigue siendo un riesgo que un trabajo existente y en ejecución pueda interrumpirse debido a la introducción de un error. Para aquellos clientes que ejecutan trabajos de procesamiento de transmisión críticos estos riesgos deben evitarse. Un mecanismo que los clientes pueden utilizar para reducir este riesgo es el modelo de **[región emparejada](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** de Azure. 

## <a name="how-do-azure-paired-regions-address-this-concern"></a>¿Cómo solucionan este problema las regiones emparejadas de Azure?

Stream Analytics garantiza que los trabajos de las regiones emparejadas se actualicen en lotes separados. Como resultado, hay un intervalo de tiempo suficiente entre las actualizaciones para identificar posibles errores de interrupción y corregirlos.

_A excepción del centro de la India_ (cuya región emparejada, India del Sur, no tiene presencia de Stream Analytics), la implementación de una actualización para Stream Analytics no se producirá al mismo tiempo en un conjunto de regiones emparejadas. Pueden producirse implementaciones en varias regiones **del mismo grupo** **al mismo tiempo**.

El artículo sobre **[disponibilidad y regiones emparejadas](https://docs.microsoft.com/azure/best-practices-availability-paired-regions)** tiene la información más reciente sobre qué regiones están emparejadas.

Se recomienda a los clientes que implementen trabajos idénticos en ambas regiones emparejadas. Además de las capacidades de supervisión interna de Stream Analytics, también se recomienda a los clientes que supervisen los trabajos como si **ambos** fueran trabajos de producción. Si no se identifica una interrupción como resultado de la actualización de servicio de Stream Analytics, escale de forma adecuada y conmute por error los consumidores que siguen en la cadena a la salida de trabajo correcta. El escalado al soporte impedirá que la región emparejada se vea afectada por la nueva implementación y mantenga la integridad de los trabajos emparejados.

