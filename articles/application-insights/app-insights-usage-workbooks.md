---
title: "Investigación y uso compartido de datos de uso con libros interactivos en Azure Application Insights | Microsoft Docs"
description: "Este artículo trata sobre el análisis de los usuarios de su aplicación web."
services: application-insights
documentationcenter: 
author: numberbycolors
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: multiple
ms.topic: article
ms.date: 06/12/2017
ms.author: mbullwin
ms.openlocfilehash: 3edaefa942b834a070c55fe28daf60c74ea9f59d
ms.sourcegitcommit: adf6a4c89364394931c1d29e4057a50799c90fc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2017
---
# <a name="investigate-and-share-usage-data-with-interactive-workbooks-in-application-insights"></a>Investigación y uso compartido de datos de uso con libros interactivos en Application Insights

Los libros combinan las visualizaciones de datos de [Azure Application Insights](app-insights-overview.md), las [consultas de Analytics](app-insights-analytics.md) y texto en documentos interactivos. Otros miembros del equipo con acceso al mismo recurso de Azure pueden editar los libros. Esto significa que las consultas y los controles utilizados para crear un libro están disponibles para las demás personas que leen el libro, lo que facilita su exploración, ampliación y la búsqueda de errores.

Los libros son útiles en escenarios como:

* Exploración del uso de la aplicación cuando no conoce de antemano las métricas de interés: número de usuarios, tasas de retención, tasas de conversión, etc. A diferencia de otras herramientas de análisis de uso de Application Insights, los libros le permiten combinar varios tipos de visualizaciones y análisis, lo cual los hace idóneos para este tipo de exploración de forma libre.
* A la hora de explicar al equipo el rendimiento de una característica recientemente publicada, mostrando recuentos de usuarios para las interacciones principales y otras métricas.
* Uso compartido de los resultados de un experimento A o B de la aplicación con otros miembros del equipo. Puede explicar los objetivos del experimento con texto y luego mostrar cada métrica de uso y la consulta de Analytics que se usa para evaluar el experimento, junto con indicadores claros sobre si cada métrica está por encima o por debajo del objetivo.
* Notificación del impacto de una interrupción del servicio en el uso de la aplicación, combinación de datos, explicación del texto y análisis de los pasos siguientes para evitar más interrupciones en el futuro.

> [!NOTE]
> El recurso de Application Insights debe contener vistas de página o eventos personalizados para utilizar los libros. [Aprenda a configurar la aplicación para que recopile vistas de página automáticamente con el SDK de JavaScript de Application Insights](app-insights-javascript.md).
> 
> 

## <a name="editing-rearranging-cloning-and-deleting-workbook-sections"></a>Edición, reorganización, clonación y eliminación de secciones de libro

Un libro está compuesto de secciones: visualizaciones de uso que se pueden editar independientemente, gráficos, tablas, texto o resultados de consultas de Analytics.

Para editar el contenido de una sección de un libro, haga clic en el botón **Editar** situado en la parte inferior derecha de la sección del libro.

![Controles de edición de secciones de libros de Application Insights](./media/app-insights-usage-workbooks/editing-controls.png)

1. Cuando haya terminado de editar una sección, haga clic en **Edición finalizada** en la esquina inferior izquierda de la sección.

2. Para crear un duplicado de una sección, haga clic en el icono **Clone this section** (Clonar este sección). La creación de secciones duplicadas es una forma ideal de iterar una consulta sin perder las iteraciones anteriores.

3. Para mover hacia arriba una sección de un libro, haga clic en el icono **Subir** o **Bajar**.

4. Para eliminar una sección de forma permanente, haga clic en el icono **Quitar**.

## <a name="adding-usage-data-visualization-sections"></a>Incorporación de secciones de visualización de datos de uso

Los libros ofrecen cuatro tipos de visualizaciones de análisis de uso integradas. Cada una de ellas responde a una pregunta habitual acerca del uso de la aplicación. Para agregar tablas y gráficos distintos a estas cuatro secciones, agregue secciones de consultas de Analytics (consulte a continuación).

Para agregar una sección de Usuarios, Sesiones, Eventos o Retención al libro, use el botón **Agregar usuarios** o el botón correspondiente situado en la parte inferior del libro, o en la parte inferior de cualquier sección.

![Sección Usuarios en los libros](./media/app-insights-usage-workbooks/users-section.png)

Las secciones **Usuarios** dan respuesta a la pregunta "¿cuántos usuarios vieron una página determinada o utilizaron alguna característica de mi sitio?"

Las secciones **Sesiones** dan respuesta a la pregunta "¿cuántas sesiones emplearon los usuarios para ver una página determinada o usar alguna característica de mi sitio?"

Las secciones **Eventos** dan respuesta a la pregunta "¿cuántas veces vieron los usuarios una página determinada o utilizaron alguna característica de mi sitio?"

Cada uno de estos tres tipos de sección ofrece los mismos conjuntos de controles y visualizaciones:

* [Más información sobre la edición de las secciones Usuarios, Sesiones y Eventos](app-insights-usage-segmentation.md)
* Active o desactive el gráfico principal, las cuadrículas de histograma, los análisis automáticos y las visualizaciones de los usuarios de ejemplo mediante las casillas **Mostrar gráfico**, **Mostrar cuadrícula**, **Mostrar Insights** y **Ejemplo de estos usuarios** situadas en la parte superior de cada sección.

![Sección Retención en los libros](./media/app-insights-usage-workbooks/retention-section.png)

Las secciones **Retención** dan respuesta a la pregunta: "De las personas que vieron una página determinada o usaron alguna característica en un día o semana concretos, ¿cuántos regresaron otro día o semana posteriores?"

* [Más información acerca de la edición de las secciones de retención](app-insights-usage-retention.md)
* Active o desactive el gráfico de retenciones totales mediante la casilla **Mostrar gráfico de retenciones totales** situada en la parte superior de la sección.

## <a name="adding-application-insights-analytics-sections"></a>Incorporación de secciones de análisis de Application Insights

![Sección de análisis en los libros](./media/app-insights-usage-workbooks/analytics-section.png)

Para agregar una sección de consultas de análisis de Application Insights en el libro, use el botón **Agregar consulta de análisis** situado en la parte inferior del libro o en la parte inferior de cualquier sección.

Las secciones de consulta de análisis le permiten agregar consultas arbitrarias sobre los datos de Application Insights a los libros. Esta flexibilidad significa que las secciones de consultas de análisis pueden ser el medio para responder a preguntas sobre el sitio distintas a las de las cuatro categorías mencionadas anteriormente de usuarios, sesiones, eventos y retención, como:

* ¿Cuántas excepciones produjo el sitio durante un período de tiempo equivalente al de un declive del uso?
* ¿Cuál fue la distribución de los tiempos de carga de página de los usuarios que vieron alguna página?
* ¿Cuántos usuarios vieron un conjunto de páginas determinado de su sitio, pero no otro? Esto puede ser útil para comprender si tiene clústeres de usuarios que utilizan distintos subconjuntos de funcionalidad del sitio (use el operador `join` con el modificador `kind=leftanti` en el lenguaje de consulta de Log Analytics).

Use la [referencia de lenguaje de consulta de Log Analytics](https://docs.loganalytics.io/) para más información sobre la escritura de consultas.

## <a name="adding-text-and-markdown-sections"></a>Adición de texto y secciones de Markdown

La adición de títulos, explicaciones y comentarios a los libros le ayuda a convertir un conjunto de tablas y gráficos en una descripción. Las secciones de texto de los libros son compatibles con la [sintaxis de Markdown](https://daringfireball.net/projects/markdown/) para dar formato a los textos como títulos, negrita, cursiva y listas con viñetas.

Para agregar una sección de texto al libro, use el botón **Agregar texto** situado en la parte inferior del libro o en la parte inferior de cualquier sección.

## <a name="saving-and-sharing-workbooks-with-your-team"></a>Almacenamiento y uso compartido de libros con el equipo

Los libros se guardarán en un recurso de Application Insights, ya sea en la sección privada de **Mis informes** o en la sección **Informes compartidos** a la que pueden acceder todos los usuarios con acceso al recurso de Application Insights. Para ver todos los libros del recurso, haga clic en el botón **Abrir** de la barra de acciones.

Para compartir un libro que está actualmente en **Mis informes**:

1. Haga clic en **Abrir** en la barra de acciones
2. Haga clic en el botón "..." situado junto al libro que desea compartir
3. Haga clic en **Move to Shared Reports** (Mover a informes compartidos).

Para compartir un libro con un vínculo o por correo electrónico, haga clic en **Compartir** en la barra de acciones. Tenga en cuenta que los destinatarios del vínculo necesitarán acceder a este recurso en Azure Portal para ver el libro. Para realizar ediciones, los destinatarios necesitan al menos permisos de colaborador para el recurso.

Para anclar un vínculo a un libro en un panel de Azure:

1. Haga clic en **Abrir** en la barra de acciones
2. Haga clic en el botón "..." situado junto al libro que desea anclar
3. Haga clic en **Anclar al panel**.

## <a name="next-steps"></a>Pasos siguientes

## <a name="next-steps"></a>Pasos siguientes
- Para habilitar las experiencias de uso, empiece por enviar [eventos personalizados](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#trackevent) o [vistas de páginas](https://docs.microsoft.com/azure/application-insights/app-insights-api-custom-events-metrics#page-views).
- Si ya ha enviado eventos personalizados o vistas de página, explore las herramientas de uso para obtener información sobre cómo los usuarios utilizan el servicio.
    - [Usuarios, sesiones, eventos](app-insights-usage-segmentation.md)
    - [Embudos](usage-funnels.md)
    - [Retención](app-insights-usage-retention.md)
    - [Flujos de usuario](app-insights-usage-flows.md)
    - [Adición de contexto de usuario](app-insights-usage-send-user-context.md)
    
