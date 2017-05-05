---
title: "Modelo de datos de telemetría de Azure Application Insights: telemetría de eventos | Microsoft Docs"
description: "Modelo de datos de Application Insights para la telemetría de eventos"
services: application-insights
documentationcenter: .net
author: SergeyKanzhelev
manager: azakonov-ms
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 04/17/2017
ms.author: sergkanz
translationtype: Human Translation
ms.sourcegitcommit: 9eafbc2ffc3319cbca9d8933235f87964a98f588
ms.openlocfilehash: 5b8b803f26dca82b5112568f486e5c347602a409
ms.lasthandoff: 04/22/2017


---
# <a name="event-telemetry-application-insights-data-model"></a>Telemetría de eventos: modelo de datos de Application Insights

Los eventos representan un punto en el tiempo en el que se produjo una acción en la aplicación. Normalmente es una interacción del usuario como un clic de botón o la finalización de un pedido. También puede ser un evento de ciclo de vida de la aplicación como la actualización de la inicialización o la configuración. El nombre del evento debe ser una cadena corta de cardinalidad baja. 

Semánticamente, ahora los eventos pueden estar correlacionados o no con las solicitudes. Pero si se usa correctamente, la telemetría de eventos es más importante que las solicitudes o los seguimientos. Los eventos representan la telemetría de negocio y deberían estar sujetos a un muestreo independiente, menos agresivo.

## <a name="name"></a>Nombre

Nombre del evento. Mantener la cardinalidad baja para permitir una correcta agrupación y métricas útiles.

Longitud máxima: 512 caracteres

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="custom-measurements"></a>Medidas personalizadas

[!INCLUDE [application-insights-data-model-measurements](../../includes/application-insights-data-model-measurements.md)]

## <a name="next-steps"></a>Pasos siguientes

- Vea [modelo de datos](/application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.
- Obtenga información sobre cómo usar la [API de Application Insights para eventos y métricas personalizados](/app-insights-asp-net-dependencies.md).
- Consulte las [plataformas](/app-insights-platforms.md) compatibles con Application Insights.

