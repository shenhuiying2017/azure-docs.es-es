---
title: "Análisis de patrones de navegación del usuario mediante Flujos de usuarios de Azure Application Insights | Microsoft Docs"
description: "Analice cómo navegan los usuarios por las páginas y características de la aplicación web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 08/02/2017
ms.author: cfreeman
ms.openlocfilehash: d17ed3dff08f00a1d6a2108608e42b29f95fbd84
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="analyze-user-navigation-patterns-with-user-flows-in-application-insights"></a>Análisis de patrones de navegación del usuario mediante Flujos de usuarios de Application Insights

![Herramienta Flujos de usuarios de Application Insights](./media/app-insights-usage-flows/flows.png)

La herramienta Flujos de usuarios permite visualizar cómo navegan los usuarios por las páginas y características del sitio. Resulta idónea para responder a preguntas del tipo:
* ¿Cómo salen los usuarios de una página del sitio?
* ¿En qué hacen clic los usuarios en una página del sitio?
* ¿Dónde están los lugares que los usuarios abandonan con más frecuencia en su sitio?
* ¿Hay lugares donde los usuarios repitan la misma acción una y otra vez?

La herramienta Flujos de usuarios se inicia a partir de una vista de página o evento que especifique. En función de esta vista de página o evento personalizado, Flujos de usuario mostrará las vistas de página y eventos personalizados que los usuarios enviaron inmediatamente después durante una sesión, los que enviaron dos pasos después, y así sucesivamente. Aparecen líneas de varios grosores que muestran cuántas veces han seguido cada ruta los usuarios. Los nodos especiales de "Fin de la sesión" muestran cuántos usuarios no enviaron vistas de páginas ni eventos personalizados después del nodo anterior, destacando los momentos en los que los usuarios abandonaron su sitio.



> [!NOTE]
> El recurso de Application Insights debe contener vistas de página o eventos personalizados para poder utilizar la herramienta Flujos de usuarios. [Aprenda a configurar la aplicación para que recopile vistas de página automáticamente con el SDK de JavaScript de Application Insights](app-insights-javascript.md).
> 
> 

## <a name="start-by-choosing-an-initial-page-view-or-custom-event"></a>Empiece por elegir una vista de página inicial o un evento personalizado

![Elegir un evento inicial para Flujos de usuarios](./media/app-insights-usage-flows/flows-initial-event.png)

Para empezar a responder a las preguntas con la herramienta Flujos de usuarios, elija una vista de página inicial o un evento personalizado que sirva como punto de partida de la visualización:
1. Haga clic en el vínculo del título "¿Qué hacen los usuarios después de...?" o haga clic en el botón Editar. 
2. Seleccione una vista de página o un evento personalizado de la lista desplegable "Evento inicial".
3. Haga clic en "Crear grafo".

La columna "Paso 1" de la visualización muestra lo que los usuarios hicieron con más frecuencia justo después del evento inicial, ordenado de arriba a abajo de más a menos frecuente. El "Paso 2" y las columnas siguientes muestran lo que los usuarios hicieron a partir de ahí, creando una imagen con todas las maneras en que los usuarios navegaron por el sitio.

De forma predeterminada, la herramienta Flujos de usuarios muestrea de forma aleatoria solo las últimas 24 horas de vistas de página y eventos personalizados del sitio. Puede aumentar el intervalo de tiempo y cambiar el equilibrio de rendimiento y precisión para el muestreo aleatorio en el menú Edición.

Si algunos de los eventos personalizados y vistas de página no son pertinentes, haga clic en la "X" en los nodos que desea ocultar. Una vez que haya seleccionado los nodos que desea ocultar, haga clic en el botón "Crear grafo" situado debajo de la visualización. Para ver todos los nodos que ha ocultado, haga clic en el botón Editar y mire la sección "Eventos excluidos".

Si faltan vistas de página o eventos personalizados que esperaba ver en la visualización:
* Compruebe la sección "Eventos excluidos" en el menú Editar.
* Use el control "Nivel de detalle" en el menú Editar para incluir los eventos menos frecuentes en la visualización.
* Si los usuarios envían con poca frecuencia la vista de página o evento personalizado que espera, pruebe a aumentar el intervalo de tiempo de la visualización en el menú Editar.
* Asegúrese de que la vista de página o evento personalizado están configurados correctamente para su recopilación mediante el SDK de Application Insights en el código fuente del sitio. [Obtenga más información sobre la recopilación de eventos personalizados](app-insights-api-custom-events-metrics.md).

Si desea ver más pasos en la visualización, use el control deslizante "Número de pasos" del menú Editar.

## <a name="after-visiting-a-page-or-feature-where-do-users-go-and-what-do-they-click"></a>Después de visitar una página o una característica, ¿a dónde van los usuarios y dónde hacen clic?

![Usar Flujos de usuarios para ver dónde hacen clic los usuarios](./media/app-insights-usage-flows/flows-one-step.png)

Si el evento inicial es una vista de página, la primera columna ("Paso 1") de la visualización es una forma rápida de ver lo que hicieron los usuarios inmediatamente después de visitar la página. Intente abrir el sitio en una ventana que esté junto a la visualización de Flujos de usuarios. Compare sus expectativas de cómo los usuarios interactúan con la página, con la lista de eventos de la columna "Paso 1". A menudo, hay veces en las que un elemento de la interfaz de usuario de la página, que le parece insignificante a su equipo, puede estar entre los elementos más usados de la página. Puede ser un buen punto inicial para realizar mejoras en el diseño del sitio.

Si el evento inicial es un evento personalizado, la primera columna muestra lo que los usuarios hicieron justo después de realizar esa acción. Al igual que con las vistas de página, vea si el comportamiento que se ha observado en los usuarios se corresponde con las expectativas y objetivos del equipo. Por ejemplo, si el evento inicial seleccionado es "Elemento agregado al carro de la compra", mire a ver si aparecen poco después "Ir a Finalizar la compra" y "Compra terminada" en la visualización. Si el comportamiento del usuario es muy diferente del esperable, use la visualización para entender si los usuarios se sienten "atraídos" por el diseño actual de su sitio.

## <a name="where-are-the-places-that-users-churn-most-from-your-site"></a>¿Dónde están los lugares que los usuarios abandonan con más frecuencia en su sitio?

Observe los nodos de "Fin de la sesión" que aparecen en la parte de arriba de una columna de la visualización, especialmente al principio de un flujo. Esto significa que muchos usuarios probablemente abandonaron su sitio después de seguir la ruta de acceso anterior de páginas e interacciones de la interfaz de usuario. En algunas ocasiones, aunque el abandono es previsible, como, por ejemplo, después de completar una compra en un sitio de comercio electrónico, este suele ser indicativo de problemas de diseño, bajo rendimiento u otras cuestiones mejorables.

Tenga en cuenta que los nodos "Fin de la sesión" se basan únicamente en la telemetría recopilada por este recurso de Application Insights. Aunque Application Insights no reciba datos de telemetría de ciertas interacciones de usuario, los usuarios pueden haber seguido interactuando con el sitio de esas maneras después de que la herramienta Flujos de usuarios indicase que la sesión había finalizado.

## <a name="are-there-places-where-users-repeat-the-same-action-over-and-over"></a>¿Hay lugares donde los usuarios repitan la misma acción una y otra vez?

Busque una vista de página o un evento personalizado que repitan muchos usuarios en los pasos subsiguientes de la visualización. Normalmente, esto significa que los usuarios están realizando acciones repetitivas en su sitio. Si observa mucha repetición, para reducirla, considere la posibilidad de cambiar el diseño de su sitio o agregar nuevas funcionalidades. Por ejemplo, agregue la funcionalidad de edición en masa si observa que los usuarios realizan acciones repetitivas en cada fila de un elemento de tabla.

## <a name="common-questions"></a>Preguntas frecuentes

### <a name="why-do-steps-appear-disconnected"></a>¿Por qué aparecen desconectados los pasos?

![Flujos de usuarios con pasos desconectados](./media/app-insights-usage-flows/flows-disconnected.png)

Si los pasos (columnas) de una visualización de Flujos de usuarios están desconectados, significa que ninguna de las rutas de acceso realizadas por los usuarios entre los pasos eran lo suficientemente frecuentes para mostrarse. Para mostrar los eventos menos frecuentes en la visualización para que los pasos aparezcan conectados, ajuste el control deslizante "Nivel de detalle" en el menú Editar.

### <a name="does-the-initial-event-represent-the-first-time-the-event-appears-in-a-session-or-any-time-it-appears-in-a-session"></a>¿Representa el evento inicial la primera vez que aparece el evento en una sesión, o las veces posteriores?

El evento inicial de la visualización solo representa la primera vez que un usuario envía esa vista de página o evento personalizado durante una sesión. Si los usuarios pueden enviar el evento inicial varias veces en una sesión, la columna "Paso 1" solo muestra el comportamiento de los usuarios después de la *primera* instancia del evento inicial, no de todas las instancias.

## <a name="next-steps"></a>Pasos siguientes

* [Información general del uso](app-insights-usage-overview.md)
* [Usuarios, sesiones y eventos](app-insights-usage-segmentation.md)
* [Retención](app-insights-usage-retention.md)
* [Incorporación de eventos personalizados a la aplicación](app-insights-api-custom-events-metrics.md)