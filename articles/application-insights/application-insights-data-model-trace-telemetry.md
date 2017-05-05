---
title: "Modelo de datos de telemetría de Azure Application Insights: telemetría de seguimientos | Microsoft Docs"
description: "Modelo de datos de Application Insights para la telemetría de seguimientos"
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
ms.openlocfilehash: e7825b26c47a80debf92be1ad166e5a18bae4a61
ms.lasthandoff: 04/22/2017


---
# <a name="trace-telemetry-application-insights-data-model"></a>Telemetría de seguimientos: modelo de datos de Application Insights

La telemetría de seguimientos representa las instrucciones de seguimiento de estilo de `printf` en las que se busca el texto. `Log4Net`, `NLog` y las demás entradas de archivo de registro basadas en texto se convierten a instancias de este tipo. El seguimiento no tiene medidas como una extensibilidad.

## <a name="message"></a>Message

Mensaje de seguimiento.

Longitud máxima: 32 768 caracteres

## <a name="severity-level"></a>Nivel de gravedad

El nivel de gravedad del seguimiento. El valor puede ser `Verbose`, `Information`, `Warning`, `Error`, `Critical`.

## <a name="custom-properties"></a>Propiedades personalizadas

[!INCLUDE [application-insights-data-model-properties](../../includes/application-insights-data-model-properties.md)]

## <a name="next-steps"></a>Pasos siguientes

- Vea [modelo de datos](/application-insights-data-model.md) para los tipos y el modelo de datos de Application Insights.
- [Exploración de los registros de seguimiento de .NET en Application Insights](/app-insights-asp-net-trace-logs.md).
- [Exploración de los registros de seguimiento de Java en Application Insights](/app-insights-java-trace-logs.md).
- Consulte las [plataformas](/app-insights-platforms.md) compatibles con Application Insights.

