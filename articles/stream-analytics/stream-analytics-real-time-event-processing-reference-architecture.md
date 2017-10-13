---
title: 'Arquitectura de referencia: procesado de eventos en tiempo real con Microsoft Azure Stream Analytics | Microsoft Docs'
description: "Aprenda cómo puede interoperar un conjunto de servicios de Azure para habilitar el procesado de eventos en tiempo real y el análisis."
keywords: procesamiento en tiempo real, procesamiento de eventos, arquitectura de referencia
services: stream-analytics,event-hubs,storage,sql-database
documentationcenter: 
author: samacha
manager: jhubbard
editor: 
ms.assetid: 11af48bc-313c-4527-8c80-91088dc9f3c6
ms.service: stream-analytics
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2017
ms.author: samacha
ms.openlocfilehash: a5206f74e61c3cfd210ff80654e31f466ce08977
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="reference-architecture-real-time-event-processing-with-microsoft-azure-stream-analytics"></a>Arquitectura de referencia: procesado de eventos en tiempo real con Análisis de transmisiones de Microsoft Azure
La arquitectura de referencia para el procesado de eventos en tiempo real con Análisis de transmisiones de Azure está diseñada para proporcionar un plano genérico para implementar una plataforma en tiempo real como una solución de procesado de transmisiones (PaaS) de servicio con Microsoft Azure.

## <a name="summary"></a>Resumen
Tradicionalmente, las soluciones de análisis se basaban en funciones como ETL (extracción, transformación y carga de datos) y el almacenamiento de datos, donde se almacenan los datos antes del análisis. Los requisitos en continuo cambio y los datos que llegan más rápidamente están llevando este modelo al límite. Una solución consiste en analizar los datos de las transmisiones en movimiento antes del almacenamiento pero, aunque no se trata de una capacidad nueva, este enfoque no se ha adoptado ampliamente en todas las verticales del sector. 

Microsoft Azure proporciona un catálogo muy amplio de tecnologías de análisis que pueden admitir una matriz de diferentes requisitos y escenarios de solución. El proceso de seleccionar qué servicios de Azure se van a implementar en una solución de un extremo a otro puede ser todo un desafío dada la variedad de ofertas. Este documento está diseñado para describir las capacidades y la interoperación de los distintos servicios de Azure que admiten una solución de transmisión de eventos. Asimismo, explica algunos de los escenarios en los que los clientes pueden beneficiarse de este tipo de enfoque.

## <a name="contents"></a>Contenido
* Resumen ejecutivo
* Introducción al análisis en tiempo real
* Propuesta de valor de datos en tiempo real en Azure
* Escenarios comunes para el análisis en tiempo real
* Arquitectura y componentes
  * Orígenes de datos
  * Capa de integración de datos
  * Capa de análisis en tiempo real
  * Capa de almacenamiento de datos
  * Presentación / capa de consumo
* Conclusión

**Autor:** Charles Feddersen, arquitecto de soluciones, Centro de excelencia de Data Insights, Microsoft Corporation

**Publicado:** enero de 2015

**Revisión:** 1.0

**Descarga:**[Procesado de eventos en tiempo real con Análisis de transmisiones de Microsoft Azure](http://download.microsoft.com/download/6/2/3/623924DE-B083-4561-9624-C1AB62B5F82B/real-time-event-processing-with-microsoft-azure-stream-analytics.pdf)

## <a name="get-help"></a>Obtener ayuda
Para obtener más ayuda, pruebe nuestro [foro de Análisis de transmisiones de Azure](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Pasos siguientes
* [Introducción al Análisis de transmisiones de Azure](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Análisis de transmisiones de Azure](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Análisis de transmisiones de Azure](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Referencia de API de REST de administración de Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

