---
title: "Uso del portal de búsqueda de registros en Azure Log Analytics | Microsoft Docs"
description: "Este artículo incluye un tutorial que describe cómo crear búsquedas de registros y analizar los datos almacenados en el área de trabajo de Log Analytics mediante el portal de búsqueda de registros.  El tutorial incluye la ejecución de algunas consultas sencillas que devuelven distintos tipos de datos y el análisis de los resultados."
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
ms.date: 01/19/2018
ms.author: bwren
ms.openlocfilehash: 3a2e8803d51d81ab0eda3dc814d01822e17bc14e
ms.sourcegitcommit: 1fbaa2ccda2fb826c74755d42a31835d9d30e05f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/22/2018
---
# <a name="create-log-searches-in-azure-log-analytics-using-the-log-search-portal"></a>Creación de búsquedas de registros en Azure Log Analytics mediante el portal correspondiente

> [!NOTE]
> Este artículo describe el portal de búsqueda de registros de Azure Log Analytics mediante el nuevo lenguaje de consulta.  Puede obtener más información sobre el nuevo lenguaje y conocer el procedimiento para actualizar el área de trabajo en [Upgrade your Azure Log Analytics workspace to new log search](log-analytics-log-search-upgrade.md) (Actualización del área de trabajo de Azure Log Analytics al nuevo registro de búsquedas)///.  
>
> Si todavía no se ha actualizado el área de trabajo al nuevo lenguaje de consulta, vaya a [Búsqueda de datos mediante búsquedas de registros en Log Analytics](log-analytics-log-searches.md) para obtener información sobre la versión actual del portal de búsqueda de registros.

Este artículo incluye un tutorial que describe cómo crear búsquedas de registros y analizar los datos almacenados en el área de trabajo de Log Analytics mediante el portal de búsqueda de registros.  El tutorial incluye la ejecución de algunas consultas sencillas que devuelven distintos tipos de datos y el análisis de los resultados.  Se centra en las características del portal de búsqueda de registros que permiten modificar la consulta en lugar de hacerlo directamente.  Para más información sobre cómo editar directamente la consulta, vaya a la [referencia del lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856079).

Para crear búsquedas en el portal de análisis avanzado en lugar de en el portal de búsqueda de registros, consulte [Getting Started with the Analytics Portal](https://go.microsoft.com/fwlink/?linkid=856587) (Introducción al portal de análisis).  Ambos portales usan el mismo lenguaje de consulta para acceder a los mismos datos en el área de trabajo de Log Analytics.

## <a name="prerequisites"></a>requisitos previos
En este tutorial se da por supuesto que ya tiene un área de trabajo de Log Analytics con al menos un origen conectado que genera los datos para que las consultas los analicen.  

- Si no tiene un área de trabajo, puede crear una gratis mediante el procedimiento descrito en [Introducción a un área de trabajo de Log Analytics](log-analytics-get-started.md).
- Conecte al menos un [agente de Windows](log-analytics-windows-agent.md) o un [agente de Linux](log-analytics-linux-agents.md) al área de trabajo.  

## <a name="open-the-log-search-portal"></a>Abrir el portal de búsqueda de registros
En primer lugar, abra el portal de búsqueda de registros. 

1. Abra Azure Portal.
2. Vaya a Log Analytics y seleccione su área de trabajo.
3. Seleccione **Búsqueda de registros**.

![Botón Log Search (Búsqueda de registros)](media/log-analytics-log-search-log-search-portal/log-search-button.png)

## <a name="create-a-simple-search"></a>Creación de una búsqueda simple
La forma más rápida de recuperar algunos datos con los que trabajar es con una consulta simple que devuelve todos los registros en una tabla.  Si tiene clientes de Windows o Linux conectados al área de trabajo, tendrá datos en las tablas Event (Windows) o Syslog (Linux).

Escriba una de las consultas siguientes en el cuadro de búsqueda y haga clic en el botón de búsqueda.  

```
Event
```
```
Syslog
```

Se devuelven los datos en la vista de lista predeterminada y puede ver el número total de registros que se han devuelto.

![Consulta sencilla](media/log-analytics-log-search-log-search-portal/log-search-portal-01.png)

Se muestran solo las primeras propiedades de cada registro.  Haga clic en **Mostrar más** para ver todas las propiedades de un registro concreto.

![Detalles del registro](media/log-analytics-log-search-log-search-portal/log-search-portal-02.png)

## <a name="set-the-time-scope"></a>Establecimiento del ámbito de tiempo
Cada registro recopilado por Log Analytics tiene una propiedad **TimeGenerated** que contiene la fecha y hora en que se creó el registro.  Una consulta en el portal de búsqueda de registros solo devuelve registros con una propiedad **TimeGenerated** dentro del ámbito de tiempo que aparece en el lado izquierdo de la pantalla.  

Puede cambiar el filtro de tiempo seleccionando la lista desplegable o modificando el control deslizante.  El control deslizante muestra un gráfico de barras que muestra el número relativo de registros para cada segmento de tiempo dentro del rango.  Este segmento variará según el rango.

El ámbito de tiempo predeterminado es **1 día**.  Cambie este valor a **7 días** y el número total de registros aumentará.

![Ámbito de tiempo y fecha](media/log-analytics-log-search-log-search-portal/log-search-portal-03.png)

## <a name="filter-results-of-the-query"></a>Filtrado de los resultados de la consulta
En el lado izquierdo de la pantalla está el panel de filtros que le permite agregar filtros a la consulta sin necesidad de modificarla directamente.  Varias propiedades de los registros devueltos aparecen con sus diez valores principales junto con su recuento de registros.

Si está trabajando con **Event**, active la casilla junto a **Error** en **EVENTLEVELNAME**.   Si está trabajando con **Syslog**, active la casilla junto a **Error** en **SEVERITYLEVEL**.  Esto cambia la consulta a una de las siguientes para limitar los resultados a los eventos de error.

```
Event | where (EventLevelName == "Error")
```
```
Syslog | where (SeverityLevel == "err")
```

![Filtrar](media/log-analytics-log-search-log-search-portal/log-search-portal-04.png)

Agregue propiedades al panel de filtros seleccionando **Agregar a filtros** en el menú de propiedades de uno de los registros.

![Menú Agregar a filtros](media/log-analytics-log-search-log-search-portal/log-search-portal-02a.png)

Puede establecer el mismo filtro seleccionando **Filtrar** desde el menú de propiedades de un registro con el valor que desea filtrar.  

Solo aquellas propiedades que tienen el nombre en azul tienen la opción **Filtrar**.  Se trata de campos que permiten *búsquedas* que se indexan para las condiciones de búsqueda.  Los campos en gris son campos que *permiten realizar búsquedas de texto libre* que solo tienen la opción **Mostrar referencias**.  Esta opción devuelve los registros que contienen ese valor en cualquier propiedad.

![Menú Filtrar](media/log-analytics-log-search-log-search-portal/log-search-portal-01a.png)

Puede agrupar los resultados de una sola propiedad seleccionando la opción **Agrupar por** en el menú de registro.  Esto agregará un operador [summarize](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/summarize-operator) a la consulta que muestra los resultados en un gráfico.  Puede agrupar por más de una propiedad, pero deberá editar la consulta directamente.  Seleccione el menú de registro situado junto a la propiedad **Computer** y seleccione **Agrupar por "Computer"**.  

![Agrupar por equipo](media/log-analytics-log-search-log-search-portal/log-search-portal-10.png)

## <a name="work-with-results"></a>Trabajo con resultados
El portal de búsqueda de registros tiene una variedad de características para trabajar con los resultados de una consulta.  Puede ordenar, filtrar y agrupar los resultados para analizar los datos sin modificar la consulta real.  Los resultados de una consulta no se ordenan de forma predeterminada.

Para ver los datos en un formato de tabla que proporciona opciones adicionales para filtrar y ordenar, haga clic en **Tabla**.  

![Vista Tabla](media/log-analytics-log-search-log-search-portal/log-search-portal-05.png)

Haga clic en la flecha junto a un registro para ver los detalles de ese registro.

![Ordenar resultados](media/log-analytics-log-search-log-search-portal/log-search-portal-06.png)

Ordene cualquier campo haciendo clic en el encabezado de la columna.

![Ordenar resultados](media/log-analytics-log-search-log-search-portal/log-search-portal-07.png)

Filtre los resultados en un valor específico de la columna haciendo clic en el botón de filtro y proporcionando una condición de filtro.

![Filtrar resultados](media/log-analytics-log-search-log-search-portal/log-search-portal-08.png)

Agrupe por una columna arrastrando el encabezado a la parte superior de los resultados.  Puede agrupar por varios campos arrastrando varias columnas a la parte superior.

![Agrupar resultados](media/log-analytics-log-search-log-search-portal/log-search-portal-09.png)



## <a name="work-with-performance-data"></a>Trabajo con datos de rendimiento
Los datos de rendimiento de los agentes de Windows y Linux se almacenan en el área de trabajo de Log Analytics en la tabla **Perf**.  Los registros de rendimiento tienen la misma apariencia que cualquier otro registro y puede escribir una consulta sencilla que devuelva todos los datos de rendimiento igual que se hace con los eventos.

```
Perf
```

![Datos de rendimiento](media/log-analytics-log-search-log-search-portal/log-search-portal-11.png)

La devolución de millones de registros para todos los objetos y contadores de rendimiento no resulta muy útil.  Puede usar los mismos métodos empleados anteriormente para filtrar los datos o simplemente escribir la siguiente consulta directamente en el cuadro de búsqueda de registros.  Esta devolverá únicamente los registros de utilización del procesador para los equipos con Windows y Linux.

```
Perf | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time")
```

![Utilización del procesador](media/log-analytics-log-search-log-search-portal/log-search-portal-12.png)

Esto limita los datos a un contador determinado, pero aún no están en un formato que resulte especialmente útil.  Puede mostrar los datos en un gráfico de líneas, pero primero debe agruparlos por las propiedades Computer y TimeGenerated.  Para agrupar por varios campos, debe modificar la consulta directamente, así que modifíquela de la siguiente manera.  Esta utiliza la función [avg](https://docs.loganalytics.io/docs/Language-Reference/Aggregation-functions/avg()) de la propiedad **CounterValue** para calcular el valor medio durante cada hora.

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated
```

![Gráfico con datos de rendimiento](media/log-analytics-log-search-log-search-portal/log-search-portal-13.png)

Ahora que los datos están agrupados adecuadamente, puede mostrarlos en un gráfico visual agregando el operador [render](https://docs.loganalytics.io/docs/Language-Reference/Tabular-operators/render-operator).  

```
Perf  | where (ObjectName == "Processor")  | where (CounterName == "% Processor Time") | summarize avg(CounterValue) by Computer, TimeGenerated | render timechart
```

![Gráfico de líneas](media/log-analytics-log-search-log-search-portal/log-search-portal-14.png)

## <a name="next-steps"></a>pasos siguientes

- Obtenga más información sobre el lenguaje de consultas de Log Analytics en [Getting Started with the Analytics Portal](https://go.microsoft.com/fwlink/?linkid=856079) (Introducción al portal de análisis).
- Realice un tutorial en el [portal avanzado de análisis](https://go.microsoft.com/fwlink/?linkid=856587) que le permita ejecutar las mismas consultas y acceder a los mismos datos que el portal de búsqueda de registros.
