---
title: "Actualización de Azure Log Analytics para la nueva búsqueda de registros | Microsoft Docs"
description: "El nuevo lenguaje de consulta de Log Analytics ya casi está aquí, y puede participar en la versión preliminar pública.  En este artículo se describen las ventajas del nuevo lenguaje y cómo convertir el área de trabajo."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2017
ms.author: magoedte;bwren
ms.openlocfilehash: 36a08cab2b1d409d2de382a07cfd7259a1c94e13
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="azure-log-analytics-upgrade-to-new-log-search"></a>Actualización de Azure Log Analytics para la nueva búsqueda de registros

El nuevo lenguaje de consulta de Log Analytics ya está aquí, y es necesario actualizar el área de trabajo para aprovechar sus ventajas.  Puede actualizar el área de trabajo usted mismo o esperar a que se actualice automáticamente durante el período de lanzamiento que comienza a finales de octubre y continúa hasta el final del año.  En este artículo se describen las ventajas del nuevo lenguaje y cómo convertir el área de trabajo.  

## <a name="why-the-new-language"></a>¿Por qué usar el nuevo lenguaje?
Somos conscientes de que cualquier transición genera angustia, pero el lenguaje de consulta no se va a modificar solo por amor al arte.  Hay varias razones por las que este cambio proporcionará un valor significativo a los clientes de Log Analytics.

- **Sencillez, pero eficacia.** El nuevo lenguaje es más fácil de entender y similar a SQL, con construcciones más parecidas al lenguaje natural que al lenguaje heredado.
- **Lenguaje de canalización completa.**  El nuevo lenguaje tiene funcionalidades de canalización más amplias que el lenguaje heredado.  Prácticamente cualquier salida se puede canalizar a otro comando para crear consultas más complejas que antes.
- **Extracciones de campo de tiempo de búsqueda.**  El nuevo lenguaje admite campos calculados en tiempo de ejecución más complejos que el lenguaje heredado.  Puede utilizar los cálculos complejos para los campos extendidos y después usar los campos calculados para comandos adicionales que incluyen las combinaciones y agregaciones.
- **Combinaciones avanzadas.**  El nuevo lenguaje proporciona combinaciones más avanzadas que el lenguaje heredado, incluida la capacidad de combinar tablas en varios campos, usar combinaciones internas y externas y realizar combinaciones en los campos extendidos.
- **Funciones de fecha y hora.**  El nuevo lenguaje tiene funciones de fecha y hora más avanzadas que el lenguaje heredado.
- **Análisis inteligente.**  El nuevo lenguaje tiene algoritmos avanzados para evaluar patrones de los conjuntos de datos y comparar distintos conjuntos de datos.
- **Portal de análisis avanzado.**  El portal de análisis avanzado ofrece características de análisis que no están disponibles en el portal de Log Analytics, incluidas la edición multilínea de consultas, visualizaciones adicionales y diagnósticos avanzados.
- **Coherencia con otras aplicaciones.**  El nuevo lenguaje y el portal de análisis avanzado ya se usan para realizar análisis en Application Insights.  Su implementación en Log Analytics ofrece coherencia entre los servicios de Azure.
- **Mejor integración con Power BI.** Las consultas en el nuevo lenguaje pueden exportarse a Power BI Desktop, para que pueda utilizar sus funcionalidades de transformación de datos enriquecidos.
- **Y mucho más.** Consulte el sitio [Azure Log Analytics Query Language](https://docs.loganalytics.io) (Lenguaje de consulta de Azure Log Analytics) para obtener información detallada y ver tutoriales sobre el nuevo lenguaje.


## <a name="when-can-i-upgrade"></a>¿Cuándo se puede actualizar?
La actualización se lanzarán en todas las regiones de Azure, por lo que puede estar disponible en algunas regiones antes que en otras.  Sabrá si el área de trabajo está disponible para actualizarse cuando aparezca un banner en la parte superior del área de trabajo en el que se invita a realizar la actualización.

![Actualización 1](media/log-analytics-log-search-upgrade/upgrade-01a.png)

Si el área de trabajo se actualiza automáticamente, verá un banner que indicará que se ha actualizado, con un resumen en el que se identifica si se ha detectado algún problema.

 ![actualización automática](media/log-analytics-log-search-upgrade/auto-upgrade.png)


## <a name="what-happens-after-the-upgrade"></a>¿Qué ocurre después de la actualización?
Los siguientes cambios se realizan en el área de trabajo cuando se actualiza:

- Las búsquedas guardadas, las reglas de alertas y las vistas creadas con el diseñador de vistas se convierten automáticamente al nuevo lenguaje.  Las búsquedas incluidas en las soluciones no se convierten automáticamente, pero sí se convierten sobre la marcha cuando las abre.  
- [Mi panel](log-analytics-dashboards.md) quedará en desuso y se reemplazará por el [Diseñador de vistas](log-analytics-view-designer.md) y los [paneles de Azure](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards.md).  Los elementos agregados a Mi panel aún estarán disponibles, pero serán de solo lectura.
- La [Integración de Power BI](log-analytics-powerbi.md) se sustituye por un nuevo proceso.  Se deshabilitarán las programaciones de Power BI existentes que creó y deberá reemplazarlas por el nuevo proceso.
- Las respuestas de [acciones de alerta](log-analytics-alerts-actions.md) utilizando webhooks y runbooks tienen un nuevo formato y, por ello, puede que necesite actualizar las reglas de alertas.
- Eche un vistazo a la [preguntas más frecuentes sobre la búsqueda de registros](log-analytics-log-search-faq.md) para conocer las preguntas habituales sobre la actualización.

## <a name="how-do-i-know-if-there-were-any-issues-from-the-upgrade"></a>¿Cómo puedo saber si ha habido algún problema con la actualización?
Una vez finalizada la actualización, se encontrará disponible la sección **Resumen de la actualización** en la configuración del área de trabajo.  Revise esta sección para obtener información sobre la actualización y ver cualquier problema que se encontrara.

 ![resumen de la actualización](media/log-analytics-log-search-upgrade/upgrade-summary.png)

## <a name="how-do-i-manually-perform-the-upgrade"></a>¿Cómo se realiza la actualización manual?
Puede actualizar el área de trabajo cuando vea el banner en la parte superior del portal.  

1.  Para iniciar el proceso de actualización, haga clic en el banner en el que se lee **Obtenga más información y actualice**.

    ![Actualización 2](media/log-analytics-log-search-upgrade/upgrade-01a.png)<br>

2.  Lea la información adicional sobre la actualización en la página de información de la actualización.

    ![Actualización 2](media/log-analytics-log-search-upgrade/upgrade-03.png)<br>

3.  Haga clic en **Actualizar ahora** para iniciar la actualización.

    ![Actualización 4](media/log-analytics-log-search-upgrade/upgrade-04.png)<br>Un cuadro de notificación en la esquina superior derecha muestra el estado.
    
    ![Actualización 5](media/log-analytics-log-search-upgrade/upgrade-05.png)

4.  ¡Ya está!  Revise la página de búsqueda de registros y eche un vistazo a su área de trabajo actualizada.

    ![Actualización 6](media/log-analytics-log-search-upgrade/upgrade-06.png)

Si se produce un problema que provoca un error en la actualización, puede ir al [foro de debate](https://social.msdn.microsoft.com/Forums/azure/home?forum=opinsights) y publicar su pregunta o [crear una solicitud de soporte técnico](../azure-supportability/how-to-create-azure-support-request.md) desde Azure Portal.

## <a name="how-do-i-learn-the-new-language"></a>¿Cómo se puede aprender el nuevo lenguaje?
Puesto que se trata de un lenguaje que usan varios servicios, se ha creado un [sitio externo para hospedar la documentación](https://docs.loganalytics.io/) de este nuevo lenguaje.  Incluye tutoriales, ejemplos y una referencia completa para facilitar su aprendizaje. Puede comenzar con un tutorial sobre el nuevo lenguaje en [Getting Started with Queries](https://go.microsoft.com/fwlink/?linkid=856078) (Introducción a las consultas) y acceder a la referencia del lenguaje en [Log Analytics query language](https://go.microsoft.com/fwlink/?linkid=856079) (Lenguaje de consulta de Log Analytics).  

Si desea probar el nuevo lenguaje en un entorno de demostración que incluye una serie de datos de ejemplo, eche un vistazo al [entorno de prueba](https://portal.loganalytics.io/demo#/discover/home).

Si ya está familiarizado con el lenguaje de consulta heredado de Log Analytics, puede usar el convertidor de lenguaje agregado al área de trabajo como parte de la actualización.  Solo tiene que escribir la consulta heredada y luego hacer clic en **Convertir** para ver la versión traducida.  También puede hacer clic en el botón de búsqueda para ejecutar la búsqueda, o bien copiar y pegar la consulta convertida para usarla en otro lugar, como una regla de alertas.  También puede echar un vistazo a nuestra [hoja de referencia rápida](log-analytics-log-search-transition.md) en la que se comparan directamente las consultas comunes del lenguaje heredado.

![Convertidor de lenguaje](media/log-analytics-log-search-upgrade/language-converter.png)


## <a name="next-steps"></a>Pasos siguientes
- Consulte un [tutorial sobre el nuevo lenguaje](https://go.microsoft.com/fwlink/?linkid=856078).
- Consulte un [tutorial sobre cómo usar el portal de la búsqueda de registros](log-analytics-log-search-log-search-portal.md) con el nuevo lenguaje de consulta.
- Obtenga una introducción al nuevo [Portal de análisis avanzado](https://go.microsoft.com/fwlink/?linkid=856587).
