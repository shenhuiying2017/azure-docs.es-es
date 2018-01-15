---
title: "Detección inteligente: Azure Application Insights detectó una posible fuga de memoria | Microsoft Docs"
description: Supervise las aplicaciones con Azure Application Insights para detectar posibles fugas de memoria.
services: application-insights
documentationcenter: 
author: mrbullwinkle
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: mbullwin
ms.openlocfilehash: e98caaa387418d746905990436b69925a591b260
ms.sourcegitcommit: 48fce90a4ec357d2fb89183141610789003993d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2018
---
# <a name="memory-leak-detection-preview"></a>Detección de fugas de memoria (versión preliminar)

Application Insights analiza automáticamente el consumo de memoria de cada uno de los procesos de la aplicación y le avisará sobre posibles fugas de memoria o de un aumento en el consumo de memoria.

Esta característica no requiere ninguna configuración especial, excepto la [configuración de los contadores de rendimiento](https://docs.microsoft.com/azure/application-insights/app-insights-performance-counters) de la aplicación. Se activa cuando la aplicación genera suficiente telemetría en los contadores de rendimiento de memoria (por ejemplo, bytes privados).


## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>¿Cuándo recibiré este tipo de notificación de detección inteligente?
Una notificación típica suele generarse por un aumento uniforme del consumo de memoria durante un largo período de tiempo (unas horas), en uno o más procesos y/o en uno o varios equipos, que forman parte de la aplicación.
Los algoritmos de aprendizaje automático se utilizan para detectar un aumento en el consumo de memoria que coincide con un patrón de una fuga de memoria, a diferencia de un aumento del consumo de memoria debido al aumento natural del uso de la aplicación.

## <a name="does-my-app-definitely-have-a-problem"></a>Entonces, ¿mi aplicación tiene un problema?
No, una notificación no significa que la aplicación tenga un problema. Aunque los patrones de fuga de memoria generalmente indican un problema de aplicación, estos patrones pueden ser típicos de su proceso específico, o pueden tener una justificación comercial natural, y pueden ignorarse.

## <a name="how-do-i-fix-it"></a>¿Cómo puedo corregirlo?
Las notificaciones incluyen información de diagnóstico para facilitar el proceso de análisis de diagnóstico:
1. **Evaluación de errores.** La notificación muestra cuánto aumentó la memoria (en GB) y el intervalo de tiempo en el que se produjo ese aumento. Esto puede ayudarlo a asignar una prioridad al problema.
2. **Ámbito.** ¿Cuántas máquinas mostraron el patrón de fuga de memoria? ¿Cuántas excepciones se desencadenaron durante la posible fuga de memoria? Esta información puede obtenerse de la notificación.
3. **Diagnóstico.** La detección contiene el patrón de fuga de memoria, y muestra el consumo de memoria del proceso a lo largo del tiempo. También puede usar los elementos relacionados y los vínculos de informes para obtener información que puede ayudarle a efectuar un diagnóstico exhaustivo del problema.
