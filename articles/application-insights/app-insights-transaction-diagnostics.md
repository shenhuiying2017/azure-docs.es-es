---
title: "Diagnósticos de transacción de Azure Application Insights | Microsoft Docs"
description: "Diagnósticos de transacción de extremo a extremo de Application Insights"
services: application-insights
documentationcenter: .net
author: SoubhagyaDash
manager: victormu
ms.service: application-insights
ms.workload: TBD
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 01/19/2018
ms.author: sdash
ms.openlocfilehash: 1c7eaafe99717324ad03287a1f1e0699d77cc74f
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2018
---
# <a name="unified-cross-component-transaction-diagnostics"></a>Diagnósticos de transacción entre componentes unificados

*Esta experiencia se encuentra actualmente en versión preliminar y reemplaza las hojas de diagnóstico existentes para las dependencias, las excepciones y las solicitudes del lado servidor.*

La versión preliminar presenta una nueva experiencia de diagnóstico unificada que correlaciona automáticamente la telemetría del lado servidor a través de todos los componentes supervisados de Application Insights en una única visualización. No importa si tiene varios recursos con claves de instrumentación independientes. Application Insights detecta la relación subyacente y le permite diagnosticar fácilmente el componente, la dependencia o la excepción de la aplicación que provocaron una ralentización o un error en la transacción.

## <a name="what-is-a-component"></a>¿Qué es un componente?

Los componentes son partes que se pueden implementar independientemente de su aplicación de microservicios o distribuida. Los equipos de operaciones y los desarrolladores pueden ver el código o acceder a la telemetría que generan estos componentes de la aplicación.

* Los componentes son diferentes de las dependencias externas "observadas", como SQL, EventHub, etc. a las que su equipo u organización no pueden acceder (código o telemetría).
* Los componentes se ejecutan en cualquier número de instancias de rol, servidor o contenedor.
* Los componentes pueden ser claves de instrumentación de Application Insights independientes (incluso aunque las suscripciones sean diferentes) o diferentes roles que informan a una única clave de instrumentación de Application Insights. La nueva experiencia muestra los detalles en todos los componentes, independientemente de cómo se hayan configurado.

> [!NOTE]
> * **¿Faltan los vínculos a elementos relacionados?** Toda la telemetría relacionada con la solicitud de servidor, la dependencia y la excepción están en las secciones [superior](#cross-component-transaction-chart) e [inferior](#all-telemetry-related-to-the-selected-component-operation) del lado izquierdo. 
> * La sección [superior](#cross-component-transaction-chart) correlaciona la transacción en todos los componentes. Para obtener resultados óptimos, asegúrese de que todos los componentes se instrumentan con los SDK más recientes y estables de Application Insights. Si hay diferentes recursos de Application Insights, asegúrese de que tiene los derechos adecuados para ver su telemetría.
> * La sección [inferior](#all-telemetry-related-to-the-selected-component-operation) del lado izquierdo muestra **toda** la telemetría, incluidos los seguimientos y los eventos relacionados con la solicitud del componente seleccionado.

## <a name="enable-transaction-diagnostics-experience"></a>Habilitación de la experiencia de diagnósticos de transacción
Habilite "Unified details: E2E Transaction Diagnostics" (Detalles unificados: Diagnóstico de transacciones E2E) en la [lista de versiones preliminares](app-insights-previews.md)

![Habilitación de la versión preliminar](media/app-insights-e2eTxn-diagnostics/previews.png)

Esta versión preliminar está disponible actualmente para las excepciones, las dependencias y las solicitudes del lado servidor. Puede acceder a la nueva experiencia desde las experiencias de evaluación de prioridades de **resultados de búsqueda**, **rendimiento** o **errores**. La versión preliminar reemplaza las hojas clásicas de detalles correspondientes.

![Ejemplos de rendimiento](media/app-insights-e2eTxn-diagnostics/performanceSamplesClickThrough.png)

## <a name="transaction-diagnostics-experience"></a>Experiencia de diagnósticos de la transacción
Esta vista tiene tres partes principales: un gráfico de transacción entre componentes, una lista de secuencia de tiempo de la telemetría de una determinada operación de un componente, y el panel de detalles de cualquier elemento de telemetría seleccionado a la izquierda.

![Elementos clave](media/app-insights-e2eTxn-diagnostics/3partsCrossComponent.png)

## <a name="cross-component-transaction-chart"></a>Gráfico de transacciones entre componentes

Este gráfico proporciona una escala de tiempo con barras horizontales que muestra la duración de las solicitudes y las dependencias entre los componentes. Las excepciones que se recopilan también se marcan en la escala de tiempo.

* La fila superior de este gráfico representa el punto de entrada, la solicitud entrante al primer componente que se llamó en esta transacción. La duración es el tiempo total necesario para que se complete la transacción.
* Las llamadas a las dependencias externas son filas sencillas que no se pueden contraer, con iconos que representan el tipo de dependencia.
* Las llamadas a otros componentes son filas que se pueden contraer. Cada fila corresponde a una operación específica que se invoca en el componente.
* De forma predeterminada, en el gráfico se muestra la solicitud, la dependencia o la excepción que se seleccionó inicialmente.
* Seleccione cualquier fila para ver sus [detalles a la derecha](#details-of-the-selected-telemetry). 

> [!NOTE]
Las llamadas a otros componentes tienen dos filas: una fila representa la llamada saliente (dependencia) desde el componente del llamador, y la otra fila corresponde a la solicitud entrante al componente del que recibe la llamada. El icono inicial y los distintos estilos de las barras de duración le ayudan a diferenciarlos.

## <a name="all-telemetry-related-to-the-selected-component-operation"></a>Toda la telemetría relacionada con la operación del componente seleccionado

Cualquier fila seleccionada en el gráfico de transacciones entre componentes se relaciona con una operación invocada en un determinado componente. Esta operación del componente seleccionado se refleja en el título de la sección inferior. Abra esta sección para ver una secuencia de tiempo sin formato de todos los datos de telemetría relacionados con esa operación en particular. Puede seleccionar cualquier elemento de telemetría en esta lista para ver sus [detalles a la derecha](#details-of-the-selected-telemetry).

![Secuencia de tiempo de todos los datos de telemetría](media/app-insights-e2eTxn-diagnostics/allTelemetryDrawerOpened.png)

## <a name="details-of-the-selected-telemetry"></a>Detalles de la telemetría seleccionada

En este panel se muestran los detalles de los elementos seleccionados en cualquiera de las dos secciones de la izquierda. "Mostrar todo" enumera todos los atributos estándares recopilados. Los atributos personalizados se muestran por separado después del conjunto estándar. Haga clic en "Open profiler traces" (Abrir seguimientos de Profiler) o "Abrir instantánea de depuración" para obtener diagnósticos al nivel del código en los paneles de detalles correspondientes.

![Detalle de la excepción](media/app-insights-e2eTxn-diagnostics/exceptiondetail.png)

## <a name="profiler-and-snapshot-debugger"></a>Profiler y Snapshot Debugger

[Application Insights Profiler](app-insights-profiler.md) y [Snapshot Debugger](app-insights-snapshot-debugger.md) le ayudan a realizar diagnósticos al nivel de código para conocer los problemas de rendimiento y errores. Con esta experiencia, podrá ver seguimientos de Profiler o instantáneas de cualquier componente con tan solo un clic.

Si no pudo conseguir que funcionara Profiler, póngase en contacto con **serviceprofilerhelp@microsoft.com**.

Si no pudo conseguir que funcionara Snapshot Debugger, póngase en contacto con **snapshothelp@microsoft.com**.

![Integración de Debugger](media/app-insights-e2eTxn-diagnostics/debugSnapshot.png)

## <a name="faq"></a>Preguntas más frecuentes

*Solo veo un componente en el gráfico y el resto solo se muestran como dependencias externas sin detalles acerca de lo que sucedió dentro de esos componentes.*

Posibles razones:

* ¿Existen otros componentes instrumentados con Application Insights?
* ¿Usa la versión más reciente y estable de SDK de Application Insights?
* Si estos componentes son recursos independientes de Application Insights, ¿tiene el acceso requerido para su telemetría?

Si tiene acceso y los componentes se instrumentan con el SDK más reciente de Application Insights, háganoslo saber a través del canal de comentarios en la parte superior derecha.

*Solo tengo dependencias externas. ¿Podría resultarme interesante esta versión preliminar?*

Sí. La nueva experiencia unifica todos los datos de telemetría del lado servidor en una única vista. Las hojas de detalles anteriores se reemplazaran por esta experiencia en el futuro, así que le recomendamos que la pruebe y nos envíe sus comentarios. Este es su aspecto para una transacción de componente único:

![Experiencia de componente único](media/app-insights-e2eTxn-diagnostics/singleComponent.png)

*Veo filas duplicadas para las dependencias. ¿Es normal?*

Por ahora, presentamos la llamada de dependencia de salida de forma independiente de la solicitud entrante. Por lo general, las dos llamadas son idénticas y solo difiere el valor de duración debido al recorrido de ida y vuelta. El icono inicial y los distintos estilos de las barras de duración le ayudan a diferenciarlos. ¿Le resulta confusa esta presentación de los datos? Envíenos sus comentarios.

*¿Qué hay de los desfases temporales entre las instancias de los diferentes componentes?*

Las escalas de tiempo se ajustan para los desfases temporales en el gráfico de transacciones. Puede ver las marcas de tiempo exactas en el panel de detalles o con Analytics.

*¿Por qué faltan en la nueva experiencia la mayoría de las consultas de elementos relacionadas?*

Esto es así por diseño. Todos los elementos relacionados, en todos los componentes, están disponibles en el lado izquierdo (secciones superior e inferior). La nueva experiencia tiene dos elementos relacionados que no se tratan en el lado izquierdo: todos los datos de telemetría de los cinco minutos anteriores y posteriores a este evento y la escala de tiempo del usuario.

*¿Por qué la nueva experiencia no incluye esa características que tanto me gustaba de las hojas anteriores?*

Envíenos sus comentarios. Queremos saber lo que piensa antes de que esta experiencia esté disponible en general, ya que entonces las vistas antiguas dejarán de utilizarse. Por ahora, puede deshabilitar la versión preliminar para volver a utilizar las hojas anteriores.

## <a name="known-issues"></a>Problemas conocidos

* Los ejemplos de errores de la Asignación de aplicaciones están vinculados a las hojas de detalles anteriores.
* La información basada en autocluster en los resultados de búsqueda está vinculada a las hojas de detalles anteriores.
* La integración de elementos de trabajo no está disponible en la nueva experiencia.
