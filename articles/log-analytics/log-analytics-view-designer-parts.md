---
title: "Guía de referencia de los elementos del Diseñador de vistas en Azure Log Analytics | Microsoft Docs"
description: "Mediante el Diseñador de vistas de Log Analytics, puede crear vistas personalizadas en Azure Portal que muestren diversas visualizaciones de datos en el área de trabajo de Log Analytics. Este artículo es una guía de referencia para la configuración de los elementos de visualización que están disponibles en las vistas personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 5718d620-b96e-4d33-8616-e127ee9379c4
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/17/2018
ms.author: bwren
ms.openlocfilehash: 6fd19cce955e1f06c9b6f5a9ef5d85d9fd63c1c1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/21/2018
---
# <a name="reference-guide-to-view-designer-visualization-parts-in-log-analytics"></a>Guía de referencia de los elementos de visualización del Diseñador de vistas en Log Analytics
Mediante el Diseñador de vistas de Azure Log Analytics, puede crear vistas personalizadas en Azure Portal que presenten diversas visualizaciones de datos del área de trabajo de Log Analytics. Este artículo es una guía de referencia para la configuración de los elementos de visualización que están disponibles en las vistas personalizadas.

Para más información acerca del Diseñador de vistas, consulte:

* [Diseñador de vistas](log-analytics-view-designer.md): proporciona información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas.
* [Referencia de los iconos](log-analytics-view-designer-tiles.md): proporciona una referencia de la configuración de cada uno de los iconos disponibles en las vistas personalizadas.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas de todas las vistas se deben escribir en el [nuevo lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856078). Todas las vistas que se crearon antes de actualizar el área de trabajo se convertirán automáticamente.

En la tabla siguiente se describen los tipos de icono disponibles del Diseñador de vistas:

| Tipo de vista | DESCRIPCIÓN |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Muestra una lista de las consultas de búsqueda de registros. Puede seleccionar cada una de las consultas para mostrar los resultados. |
| [Number and list](#number-amp-list-part) (Número y lista) |El encabezado presenta un solo número que muestra la cantidad de registros de una consulta de búsqueda de registros. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período. |
| [Two numbers and list](#two-numbers-amp-list-part) (Dos números y lista) |El encabezado presenta dos números que muestran la cantidad de registros de consultas de búsqueda de registros independientes. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período. |
| [Donut and list](#donut-amp-list-part) (Anillo y lista) |El encabezado muestra un solo número que resume una columna de valor de una consulta de registro. El anillo muestra gráficamente los resultados de los tres registros principales. |
| [Two timelines and list](#two-timelines-amp-list-part) (Dos escalas de tiempo y lista) |El encabezado presenta los resultados de dos consultas de registro en un período como gráficos de columnas con una llamada que muestra un solo número que resume una columna de valor de una consulta de registro. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período. |
| [Información](#information-part) |El encabezado muestra texto estático y un vínculo opcional. La lista muestra uno o varios elementos con texto y título estáticos. |
| [Line chart, callout, and list](#line-chart-callout-amp-list-part) (Gráfico de líneas, llamada y lista) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un período y una llamada con un valor resumido. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período. |
| [Line chart and list](#line-chart-amp-list-part) (Gráfico de líneas y lista) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un período. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período. |
| [Elemento Stack of line charts (Pila de gráficos de líneas)](#stack-of-line-charts-part) |Muestra tres gráficos de líneas independientes con varias series de una consulta de registro en un período. |

Las siguientes secciones describen los tipos de icono y sus propiedades en detalle.

## <a name="list-of-queries-part"></a>Elemento Lista de consultas
El elemento Lista de consultas muestra una lista de las consultas de búsqueda de registros. Puede seleccionar cada una de las consultas para mostrar los resultados. La vista incluye una sola consulta de forma predeterminada y puede seleccionar **+ Query** (+ Consulta) para agregar más.

![Vista Lista de consultas](media/log-analytics-view-designer/view-list-queries.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título |Texto que se muestra en la parte superior de la vista. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Filtros previamente seleccionados |Lista delimitada por comas de propiedades que se incluirán en el panel de filtro izquierdo al seleccionar una consulta. |
| Modo de representación |Vista inicial que se muestra cuando se selecciona la consulta. Puede seleccionar todas las vistas disponibles después de que abran la consulta. |
| **Consultas** | |
| Consulta de búsqueda |Consulta que se va a ejecutar. |
| Nombre descriptivo | Nombre descriptivo que se muestra. |

## <a name="number-and-list-part"></a>Elemento Number and list (Número y lista)
El encabezado presenta un solo número que muestra la cantidad de registros de una consulta de búsqueda de registros. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período.

![Vista Lista de consultas](media/log-analytics-view-designer/view-number-list.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior de la vista. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| Usar icono |Seleccione este vínculo para mostrar el icono. |
| **Título** | |
| Leyenda |Texto que se muestra en la parte superior del encabezado. |
| Consultar |Consulta que se ejecuta en el encabezado. Se muestra el número de los registros devueltos por la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecuta en la lista. Se muestran las dos primeras propiedades de los diez primeros registros en los resultados. La primera propiedad es un valor de texto y la segunda, un valor numérico. Las barras se crean automáticamente en función del valor relativo de la columna numérica.<br><br>Use el comando `Sort` de la consulta para ordenar los registros de la lista. Para ejecutar la consulta y que se devuelvan todos los registros puede seleccionar **See all** (Ver todo). |
| Hide graph (Ocultar gráfico) |Seleccione este vínculo para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. Para más información, consulte [Configuración común](#sparklines). |
| Color |Color de las barras o los minigráficos. |
| Name and Value Separator (Separador de nombre y valor) |Delimitador de carácter único para analizar la propiedad text en varios valores. Para más información, consulte [Configuración común](#sparklines). |
| Consulta de navegación |Consulta que se ejecuta cuando selecciona un elemento de la lista. Para más información, consulte [Configuración común](#navigation-query). |
| **Lista** |**> Títulos de columna** |
| NOMBRE |Texto que se muestra en la parte superior de la primera columna. |
| Valor |Texto que se muestra en la parte superior de la segunda columna. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para habilitar los umbrales. Para más información, consulte [Configuración común](#thresholds). |

## <a name="two-numbers-and-list-part"></a>Elemento Two numbers and list (Dos números y lista)
El encabezado tiene dos números que presentan la cantidad de registros de consultas de búsqueda de registros independientes. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período.

![Vista Dos números y lista](media/log-analytics-view-designer/view-two-numbers-list.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior de la vista. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| Usar icono |Seleccione este vínculo para mostrar el icono. |
| **Título** | |
| Leyenda |Texto que se muestra en la parte superior del encabezado. |
| Consultar |Consulta que se ejecuta en el encabezado. Se muestra el número de los registros devueltos por la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecuta en la lista. Se muestran las dos primeras propiedades de los diez primeros registros en los resultados. La primera propiedad es un valor de texto y la segunda, un valor numérico. Las barras se crean automáticamente en función del valor relativo de la columna numérica.<br><br>Use el comando `Sort` de la consulta para ordenar los registros de la lista. Para ejecutar la consulta y que se devuelvan todos los registros puede seleccionar **See all** (Ver todo). |
| Hide graph (Ocultar gráfico) |Seleccione este vínculo para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. Para más información, consulte [Configuración común](#sparklines). |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico. Para más información, consulte [Configuración común](#sparklines). |
| Name and Value Separator (Separador de nombre y valor) |Delimitador de carácter único para analizar la propiedad text en varios valores. Para más información, consulte [Configuración común](#sparklines). |
| Consulta de navegación |Consulta que se ejecuta cuando selecciona un elemento de la lista. Para más información, consulte [Configuración común](#navigation-query). |
| **Lista** |**> Títulos de columna** |
| NOMBRE |Texto que se muestra en la parte superior de la primera columna. |
| Valor |Texto que se muestra en la parte superior de la segunda columna. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para habilitar los umbrales. Para más información, consulte [Configuración común](#thresholds). |

## <a name="donut-and-list-part"></a>Elemento Donut and list (Anillo y lista)
El encabezado muestra un solo número que resume una columna de valor de una consulta de registro. El anillo muestra gráficamente los resultados de los tres registros principales.

![Vista Donut and list (Anillo y lista)](media/log-analytics-view-designer/view-donut-list.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| Usar icono |Seleccione este vínculo para mostrar el icono. |
| **Encabezado** | |
| Título |Texto que se muestra en la parte superior del encabezado. |
| Subtítulo |Texto que se muestra bajo el título en la parte superior del encabezado. |
| **Anillo** | |
| Consultar |Consulta que se ejecuta en el anillo. La primera propiedad es un valor de texto y la segunda, un valor numérico. |
| **Anillo** |**&gt; Centro** |
| Texto |Texto que se muestra en la parte inferior del valor dentro del anillo. |
| Operación |Operación que se va a realizar en la propiedad value para resumirla en un solo valor.<ul><li>Sum: suma los valores de todos los registros.</li><li>Percentage: proporción de los registros devueltos por los valores de **Result values used in center operation** (Valores de resultado usados en la operación del centro) con respecto al número total de registros en la consulta.</li></ul> |
| Valores de resultado usados en la operación central |También puede seleccionar el signo más (+) para agregar uno o varios valores. Los resultados de la consulta se limitan a los registros con los valores de propiedad especificados. Si no se agrega ningún valor, se incluirán todos los registros en la consulta. |
| **Opciones adicionales** |**&gt; Colores** |
| Color 1<br>Color 2<br>Color 3 |Seleccione el color de cada uno de los valores que aparecen en el anillo. |
| **Opciones adicionales** |**> Asignación de color avanzada** |
| Valor de campo |Escriba el nombre de un campo para que se muestre como otro color distinto si está incluido en el anillo. |
| Color |Seleccione el color del campo único. |
| **Lista** | |
| Consultar |Consulta que se ejecuta en la lista. Se muestra el número de los registros devueltos por la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione este vínculo para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. Para más información, consulte [Configuración común](#sparklines). |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico. Para más información, consulte [Configuración común](#sparklines). |
| Name and Value Separator (Separador de nombre y valor) |Delimitador de carácter único para analizar la propiedad text en varios valores. Para más información, consulte [Configuración común](#sparklines). |
| Consulta de navegación |Consulta que se ejecuta cuando selecciona un elemento de la lista. Para más información, consulte [Configuración común](#navigation-query). |
| **Lista** |**> Títulos de columna** |
| NOMBRE |Texto que se muestra en la parte superior de la primera columna. |
| Valor |Texto que se muestra en la parte superior de la segunda columna. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para habilitar los umbrales. Para más información, consulte [Configuración común](#thresholds). |

## <a name="two-timelines-and-list-part"></a>Elemento Two timelines and list (Dos escalas de tiempo y lista)
El encabezado presenta los resultados de dos consultas de registro en un período como gráficos de columnas con una llamada que muestra un solo número que resume una columna de valor de una consulta de registro. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período.

![Vista Two timelines and list (Dos escalas de tiempo y lista)](media/log-analytics-view-designer/view-two-timelines-list.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| Usar icono |Seleccione este vínculo para mostrar el icono. |
| **Primer gráfico<br>Segundo gráfico** | |
| Leyenda |Texto que se muestra en la parte inferior de la llamada de la primera serie. |
| Color |Color que se utiliza para las columnas de la serie. |
| Consultar |Consulta que se ejecuta en la primera serie. El número de los registros en cada intervalo de tiempo se representa mediante las columnas de gráfico. |
| Operación |Operación que se va a realizar en la propiedad value para resumirla en un solo valor en la llamada.<ul><li>Sum: suma de los valores de todos los registros.</li><li>Average: promedio de los valores de todos los registros.</li><li>Last Sample: valor del último intervalo incluido en el gráfico.</li><li>First Sample: valor del primer intervalo incluido en el gráfico.</li><li>Count: número de todos los registros devueltos por la consulta.</li></ul> |
| **Lista** | |
| Consultar |Consulta que se ejecuta en la lista. Se muestra el número de los registros devueltos por la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione este vínculo para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. Para más información, consulte [Configuración común](#sparklines). |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico. Para más información, consulte [Configuración común](#sparklines). |
| Consulta de navegación |Consulta que se ejecuta cuando selecciona un elemento de la lista. Para más información, consulte [Configuración común](#navigation-query). |
| **Lista** |**> Títulos de columna** |
| NOMBRE |Texto que se muestra en la parte superior de la primera columna. |
| Valor |Texto que se muestra en la parte superior de la segunda columna. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para habilitar los umbrales. Para más información, consulte [Configuración común](#thresholds). |

## <a name="information-part"></a>Elemento Información
El encabezado muestra texto estático y un vínculo opcional. La lista muestra uno o varios elementos con texto y título estáticos.

![Vista Información](media/log-analytics-view-designer/view-information.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Color |Color de fondo del encabezado. |
| **Encabezado** | |
| Imagen |Archivo de imagen que se muestra en el encabezado. |
| Etiqueta |Texto que se muestra en el encabezado. |
| **Encabezado** |**&gt; Vínculo** |
| Etiqueta |Texto del vínculo. |
| URL |Dirección URL del vínculo. |
| **Elementos de información** | |
| Título |Texto que se muestra en el título de cada elemento. |
| Contenido |Texto que se muestra en cada elemento. |

## <a name="line-chart-callout-and-list-part"></a>Elemento Line chart, callout, and list (Gráfico de líneas, llamada y lista)
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un período y una llamada con un valor resumido. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período.

![Vista Line chart, callout, and list (Gráfico de líneas, llamada y lista)](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| Usar icono |Seleccione este vínculo para mostrar el icono. |
| **Encabezado** | |
| Título |Texto que se muestra en la parte superior del encabezado. |
| Subtítulo |Texto que se muestra bajo el título en la parte superior del encabezado. |
| **Gráfico de líneas** | |
| Consultar |Consulta que se ejecuta en el gráfico de líneas. La primera propiedad es un valor de texto y la segunda, un valor numérico. Normalmente, esta consulta usa la palabra clave *measure* para resumir los resultados. Si la consulta utiliza la palabra clave *interval*, el eje X del gráfico emplea este intervalo de tiempo. Si la consulta no incluye la palabra clave *interval*, el eje X emplea intervalos horarios. |
| **Gráfico de líneas** |**&gt; Llamada** |
| Título de la llamada |Texto que se muestra en la parte superior del valor de la llamada. |
| Nombre de la serie |Valor de propiedad de la serie que se va a utilizar para el valor de la llamada. Si no se proporciona ninguna serie, se utilizarán todos los registros de la consulta. |
| Operación |Operación que se va a realizar en la propiedad value para resumirla en un solo valor en la llamada.<ul><li>Average: promedio de los valores de todos los registros.</li><li>Count: número de todos los registros devueltos por la consulta.</li><li>Last Sample: valor del último intervalo incluido en el gráfico.</li><li>Max: valor máximo de los intervalos incluidos en el gráfico.</li><li>Min: valor mínimo de los intervalos incluidos en el gráfico.</li><li>Sum: suma de los valores de todos los registros.</li></ul> |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione este vínculo para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta. Esta información se utiliza para mostrar etiquetas de gráfico que indican los tipos de valores y, de manera opcional, para convertirlos. El tipo *Unidad* especifica la categoría de la unidad y define los valores disponibles del tipo *Unidad actual*. Si selecciona un valor en *Convertir a*, los valores numéricos se convertirán del tipo *Unidad actual* al tipo *Convertir a*. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo *Unidad*. Si no se especifica ninguna etiqueta, solo se mostrará el tipo *Unidad*. |
| **Lista** | |
| Consultar |Consulta que se ejecuta en la lista. Se muestra el número de los registros devueltos por la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione este vínculo para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. Para más información, consulte [Configuración común](#sparklines). |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico. Para más información, consulte [Configuración común](#sparklines). |
| Name and Value Separator (Separador de nombre y valor) |Delimitador de carácter único para analizar la propiedad text en varios valores. Para más información, consulte [Configuración común](#sparklines). |
| Consulta de navegación |Consulta que se ejecuta cuando selecciona un elemento de la lista. Para más información, consulte [Configuración común](#navigation-query). |
| **Lista** |**> Títulos de columna** |
| NOMBRE |Texto que se muestra en la parte superior de la primera columna. |
| Valor |Texto que se muestra en la parte superior de la segunda columna. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para habilitar los umbrales. Para más información, consulte [Configuración común](#thresholds). |

## <a name="line-chart-and-list-part"></a>Elemento Line chart and list (Gráfico de líneas y lista)
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un período. La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un período.

![Vista Line chart and list (Gráfico de líneas y lista)](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| Usar icono |Seleccione este vínculo para mostrar el icono. |
| **Encabezado** | |
| Título |Texto que se muestra en la parte superior del encabezado. |
| Subtítulo |Texto que se muestra bajo el título en la parte superior del encabezado. |
| **Gráfico de líneas** | |
| Consultar |Consulta que se ejecuta en el gráfico de líneas. La primera propiedad es un valor de texto y la segunda, un valor numérico. Normalmente, esta consulta usa la palabra clave *measure* para resumir los resultados. Si la consulta utiliza la palabra clave *interval*, el eje X del gráfico emplea este intervalo de tiempo. Si la consulta no incluye la palabra clave *interval*, el eje X emplea intervalos horarios. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione este vínculo para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta. Esta información se utiliza para mostrar etiquetas de gráfico que indican los tipos de valores y, de manera opcional, para convertirlos. El tipo *Unidad* especifica la categoría de la unidad y define los valores disponibles del tipo *Unidad actual*. Si selecciona un valor en *Convertir a*, los valores numéricos se convertirán del tipo *Unidad actual* al tipo *Convertir a*. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo *Unidad*. Si no se especifica ninguna etiqueta, solo se mostrará el tipo *Unidad*. |
| **Lista** | |
| Consultar |Consulta que se ejecuta en la lista. Se muestra el número de los registros devueltos por la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione este vínculo para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. Para más información, consulte [Configuración común](#sparklines). |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico. Para más información, consulte [Configuración común](#sparklines). |
| Name and Value Separator (Separador de nombre y valor) |Delimitador de carácter único para analizar la propiedad text en varios valores. Para más información, consulte [Configuración común](#sparklines). |
| Consulta de navegación |Consulta que se ejecuta cuando selecciona un elemento de la lista. Para más información, consulte [Configuración común](#navigation-query). |
| **Lista** |**> Títulos de columna** |
| NOMBRE |Texto que se muestra en la parte superior de la primera columna. |
| Valor |Texto que se muestra en la parte superior de la segunda columna. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para habilitar los umbrales. Para más información, consulte [Configuración común](#thresholds). |

## <a name="stack-of-line-charts-part"></a>Elemento Stack of line charts (Pila de gráficos de líneas)
La pila del gráfico de líneas muestra tres gráficos de líneas independientes con varias series de una consulta de registro en un período, tal como se muestra aquí:

![Stack of line charts (Pila de gráficos de líneas)](media/log-analytics-view-designer/view-stack-line-charts.png)

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione este vínculo para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se muestra junto a los resultados en el encabezado. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**&gt; Encabezado** |
| Título |Texto que se muestra en la parte superior del gráfico. |
| Subtítulo |Texto que se muestra bajo el título en la parte superior del gráfico. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**Gráfico de líneas** |
| Consultar |Consulta que se ejecuta en el gráfico de líneas. La primera propiedad es un valor de texto y la segunda, un valor numérico. Normalmente, esta consulta usa la palabra clave *measure* para resumir los resultados. Si la consulta utiliza la palabra clave *interval*, el eje X del gráfico emplea este intervalo de tiempo. Si la consulta no incluye la palabra clave *interval*, el eje X emplea intervalos horarios. |
| **Gráfico** |**> Eje Y** |
| Usar escala logarítmica |Seleccione este vínculo para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta. Esta información se utiliza para mostrar etiquetas de gráfico que indican los tipos de valores y, de manera opcional, para convertirlos. El tipo *Unidad* especifica la categoría de la unidad y define los valores disponibles del tipo *Unidad actual*. Si selecciona un valor en *Convertir a*, los valores numéricos se convertirán del tipo *Unidad actual* al tipo *Convertir a*. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo *Unidad*. Si no se especifica ninguna etiqueta, solo se mostrará el tipo *Unidad*. |

## <a name="common-settings"></a>Configuración común
En las secciones siguientes se describe la configuración que es común a varios elementos de visualización.

### <a name="name-value-separator"></a>Name and Value Separator (Separador de nombre y valor)
El separador de nombre y valor es el delimitador de carácter único para analizar la propiedad text de una consulta de lista en varios valores. Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro **Nombre**.

Por ejemplo, una propiedad llamada *Location* que incluya valores como *Redmond-Building 41* y *Bellevue-Building 12*. Puede especificar un guión (-) para el separador de nombre y valor, y *City-Building* para el nombre. Este enfoque analiza cada valor en dos propiedades denominadas *City* y *Building*.

### <a name="navigation-query"></a>Consulta de navegación
La consulta de navegación es un consulta que se ejecuta cuando selecciona un elemento de la lista. Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.

Por ejemplo, si la consulta tiene una columna llamada *Equipo* y la consulta de navegación es *{selected item}*, al seleccionar un equipo se ejecuta una consulta como *Equipo="miEquipo"*. Si la consulta de navegación es *Type=Event {selected item}*, se ejecuta la consulta *Type=Event Equipo="miEquipo"*.

### <a name="sparklines"></a>Sparklines (Minigráficos)
Un minigráfico es un pequeño gráfico de líneas que muestra el valor de una entrada de lista en un periodo. Para los elementos de visualización con una lista, puede elegir si quiere mostrar una barra horizontal que indique el valor relativo de una columna numérica o un minigráfico que señale su valor en un período.

En la tabla siguiente se describe la configuración de los minigráficos:

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| Enable sparklines (Habilitar los minigráficos) |Seleccione este vínculo para mostrar un minigráfico en lugar de la barra horizontal. |
| Operación |Si los minigráficos están habilitados, será la operación que se realizará en cada propiedad de la lista para calcular los valores del minigráfico.<ul><li>Last Sample: último valor de la serie en el intervalo de tiempo.</li><li>Max: valor máximo de la serie en el intervalo de tiempo.</li><li>Min: valor mínimo de la serie en el intervalo de tiempo.</li><li>Sum: suma de los valores de la serie en el intervalo de tiempo.</li><li>Summary: utiliza el mismo comando `measure` que la consulta del encabezado.</li></ul> |

### <a name="thresholds"></a>Umbrales
Mediante los umbrales, puede mostrar un icono de color junto a cada elemento de una lista. Los umbrales le proporcionan un indicador visual rápido de los elementos que superan un valor concreto o se encuentran dentro de un rango determinado. Por ejemplo, puede mostrar un icono verde para los elementos que tienen un valor aceptable; amarillo si el valor está dentro de un rango que indica una advertencia y rojo si supera un valor de error.

Al habilitar umbrales en un elemento, debe especificar uno o varios umbrales. Si el valor de un elemento es mayor que un valor de umbral y menor que el valor de umbral siguiente, se utiliza el color para ese valor. Si el elemento es mayor que el valor de umbral máximo, se usa otro color. 

Cada conjunto de umbral tiene un umbral con un valor de **Predeterminado**. Este es el color que se establece si no se superan los valores. Puede agregar o quitar umbrales seleccionando los botones **Agregar** (+) o **Eliminar** (x).

En la tabla siguiente se describe la configuración de los umbrales:

| Configuración | DESCRIPCIÓN |
|:--- |:--- |
| Enable Thresholds (Habilitar umbrales) |Seleccione este vínculo para mostrar un icono de color a la izquierda de cada valor. El icono indica el mantenimiento del valor en relación con los umbrales especificados. |
| NOMBRE |Nombre del valor de umbral. |
| Umbral |Valor del umbral. El color de mantenimiento de cada elemento de lista se establece en el color del valor de umbral máximo que se ve superado por el valor del elemento. Si no se supera ningún valor de umbral, se utiliza un color predeterminado. |
| Color |Color que indica el valor de umbral. |

## <a name="next-steps"></a>pasos siguientes
* Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para poder realizar consultas en elementos de visualización.
