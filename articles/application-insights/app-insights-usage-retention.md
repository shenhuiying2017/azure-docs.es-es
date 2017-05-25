---
title: "Análisis de retención de usuarios para aplicaciones web con Azure Application Insights | Microsoft Docs"
description: "¿Cuántos usuarios regresan a la aplicación?"
services: application-insights
documentationcenter: 
author: botatoes
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 05/03/2017
ms.author: cfreeman
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 17f6062537714fbef7c8509261ac4875f8a44b6e
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017

---

# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análisis de retención de usuarios para aplicaciones web con Application Insights

La hoja Retención de [Azure Application Insights](app-insights-overview.md) lo ayuda a analizar cuántos usuarios regresan a la aplicación, y con qué frecuencia realizan determinadas tareas o lograr objetivos. Por ejemplo, si ejecuta un sitio de juegos, puede comparar el número de usuarios que regresan al sitio después de perder una partida con la cantidad que vuelven tras ganar. Esta información puede ayudarlo a mejorar la experiencia de los usuarios y la estrategia empresarial.

## <a name="get-started"></a>Introducción

Si aún no ve los datos en la hoja Retención del portal de Application Insights, [obtenga información sobre cómo empezar a trabajar con las herramientas de uso](app-insights-usage-overview.md).

## <a name="the-retention-tool"></a>La herramienta Retención

![Herramienta Retención](./media/app-insights-usage-retention/retention.png)

a. La página Información general siempre está disponible. Además, puede guardar informes con nombre con una configuración de parámetros diferente. Guarde el informe en la carpeta de compartidos para que puedan verlo otros usuarios que tienen acceso al recurso.

b. De forma predeterminada, el gráfico cuenta todos los usuarios que usan cualquiera de los eventos personalizados o vistas de página recibidas de la aplicación. Seleccione uno, o un subconjunto, para centrarse en una actividad de usuario determinada. 

c. Agregue uno o varios filtros en las propiedades. Por ejemplo, podría centrarse en los usuarios de un país o una región determinados. Haga clic en **Actualizar** después de establecer los filtros.

d. **Restaurar valores predeterminados** siempre borra el filtro personalizado y los filtros de eventos.

e. El gráfico de resumen muestra los totales en el periodo seleccionado.

f. La cuadrícula muestra el número de usuarios que han regresado para repetir las acciones seleccionadas en un periodo determinado. Cada fila representa una cohorte de usuarios que llevó a cabo una de las acciones seleccionadas en el periodo mostrado. Cada celda de la fila muestra cuántos de esa cohorte regresaron al menos una vez en un periodo posterior. Algunos usuarios pueden regresar en más de un periodo.

Todos los usuarios que usaron la aplicación durante el intervalo de tiempo del gráfico se representan exactamente en una fila del gráfico. Cada usuario se cuenta en el periodo de cuando realizó por primera vez la acción seleccionada en el intervalo de tiempo del gráfico. Por tanto, suele haber un número mayor en la primera fila.


## <a name="use-business-events-to-track-retention"></a>Uso de eventos empresariales para realizar un seguimiento de la retención

Para obtener el análisis de retención más útil, mida los eventos que representan actividades empresariales importantes. 

Por ejemplo, muchos usuarios podrían abrir una página de la aplicación sin jugar al juego que se muestra. Al realizar solo el seguimiento de las vistas de página, se proporcionaría una estimación inexacta de cuántas personas vuelven a reproducir el juego después de haberlo jugado anteriormente. Para obtener una visión clara de los jugadores que regresan, la aplicación debe enviar un evento personalizado cuando un usuario juega realmente.  

Se recomienda codificar los eventos personalizados que representan las acciones clave del negocio y usarlos para realizar el análisis de retención. Para obtener el resultado del juego, tiene que escribir una línea de código con el fin enviar un evento personalizado a Application Insights. Si se escribe en el código de la página web o en Node.JS, se vería similar a esto:

```JavaScript
    appinsights.trackEvent("won game");
```

O en el código del servidor ASP.NET:

```C#
   telemetry.TrackEvent("won game");
```

[Obtenga más información sobre la creación de eventos personalizados](app-insights-api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Pasos siguientes

* [Información general del uso](app-insights-usage-overview.md)
* [Usuarios y sesiones](app-insights-usage-segmentation.md)
* [Eventos personalizados de codificación](app-insights-api-custom-events-metrics.md)


