---
title: "Referencia de los elementos del Diseñador de vistas de Log Analytics (OMS) | Microsoft Docs"
description: "El Diseñador de vistas de Log Analytics permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo se proporciona una referencia de la configuración de cada uno de los elementos de visualización que hay disponibles para usarse en las vistas personalizadas."
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
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 40a6101576708936404447576d704a49666143fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Referencia de los elementos de visualización del Diseñador de vistas de Log Analytics
El Diseñador de vistas de Log Analytics permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo se proporciona una referencia de la configuración de cada uno de los elementos de visualización que hay disponibles para usarse en las vistas personalizadas.

Estos son otros de los artículos disponibles sobre el Diseñador de vistas:

* [Diseñador de vistas](log-analytics-view-designer.md): información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas.
* [Referencia de los iconos](log-analytics-view-designer-tiles.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas de todas las vistas se deben escribir en el [nuevo lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas las vistas que se crearon antes de actualizar el área de trabajo se convertirán automáticamente.

En la siguiente tabla se describen los distintos tipos de iconos que se encuentran disponibles en el Diseñador de vistas.  En las secciones siguientes se describen en profundidad todos los tipos de iconos y sus propiedades.

| Tipo de vista | Descripción |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Muestra una lista de las consultas de búsqueda de registros.  El usuario puede hacer clic en cada una de las consultas para mostrar los resultados. |
| [Number &amp; list](#number-amp-list-part) (Número y lista) |El encabezado tiene un solo número que muestra la cantidad de registros de una consulta de búsqueda de registros.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo. |
| [Dos números y lista](#two-numbers-amp-list-part) |El encabezado tiene dos números que muestran la cantidad de registros de consultas de búsqueda de registros independientes.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo. |
| [Donut &amp; list](#donut-amp-list-part) (Anillo y lista) |El encabezado muestra un solo número resumido obtenido a partir de una columna de valor de una consulta de registros.  El anillo muestra gráficamente los resultados de los tres registros principales. |
| [Dos escalas de tiempo y lista](#two-timelines-amp-list-part) |El encabezado muestra los resultados de dos consultas de registro en un periodo como gráficos de columnas con una llamada que presenta un solo número resumido obtenido a partir de una columna de valor de una consulta de registro.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo. |
| [Información](#information-part) |El encabezado muestra texto estático y un vínculo opcional.  La lista muestra uno o varios elementos con texto estático y un título. |
| [Gráfico de líneas, llamada, lista](#line-chart-callout-amp-list-part) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo y una llamada con un valor resumido.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo. |
| [Gráfico de líneas y lista](#line-chart-amp-list-part) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo. |
| [Elemento Stack of line charts (Pila de gráficos de líneas)](#stack-of-line-charts-part) |Muestra tres gráficos de líneas independientes con varias series de una consulta de registro en un periodo. |

## <a name="list-of-queries-part"></a>Elemento Lista de consultas
Muestra una lista de las consultas de búsqueda de registros.  El usuario puede hacer clic en cada una de las consultas para mostrar los resultados.  La vista incluirá una sola consulta de forma predeterminada y puede hacer clic en **+ Consulta** para agregar más.

![Vista Lista de consultas](media/log-analytics-view-designer/view-list-queries.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título |Texto que se muestra en la parte superior de la vista. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Filtros previamente seleccionados |Lista delimitada por comas de propiedades que se incluirán en el panel de filtro izquierdo cuando el usuario seleccione una consulta. |
| Modo de representación |Vista inicial que se muestra cuando se selecciona la consulta.  El usuario puede seleccionar todas las vistas disponibles después de abrir la consulta. |
| **Consultas** | |
| Consulta de búsqueda |La consulta que se ejecutará. |
| Nombre descriptivo |Nombre descriptivo de la consulta que se mostrará al usuario. |

## <a name="number--list-part"></a>Elemento Number & list (Número y lista)
El encabezado tiene un solo número que muestra la cantidad de registros de una consulta de búsqueda de registros.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo.

![Vista Lista de consultas](media/log-analytics-view-designer/view-number-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior de la vista. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Título** | |
| Leyenda |Texto que se muestra en la parte superior del encabezado. |
| Consultar |Consulta que se ejecutará en el encabezado.  Se mostrará la cantidad de registros que devuelva la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrarán las primeras dos propiedades de los diez primeros registros en los resultados.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Las barras se crean automáticamente en función del valor relativo de la columna numérica.<br><br>Use el comando sort de la consulta para ordenar los registros de la lista.  El usuario puede hacer clic en Ver todo para ejecutar la consulta y que se devuelvan todos los registros. |
| Hide graph (Ocultar gráfico) |Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Color |Color de las barras o los minigráficos. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Consulte [Configuración común](#name-value-separator) para obtener más información. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Consulte [Configuración común](#navigation-query) para obtener más información. |
| **Lista** |**&gt; Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para habilitar los umbrales.  Consulte [Configuración común](#thresholds) para obtener más información. |

## <a name="two-numbers--list-part"></a>Elemento Dos números y lista
El encabezado tiene dos números que muestran la cantidad de registros de consultas de búsqueda de registros independientes.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo.

![Vista Dos números y lista](media/log-analytics-view-designer/view-two-numbers-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior de la vista. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Título** | |
| Leyenda |Texto que se muestra en la parte superior del encabezado. |
| Consultar |Consulta que se ejecutará en el encabezado.  Se mostrará la cantidad de registros que devuelva la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrarán las primeras dos propiedades de los diez primeros registros en los resultados.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Las barras se crean automáticamente en función del valor relativo de la columna numérica.<br><br>Use el comando sort de la consulta para ordenar los registros de la lista.  El usuario puede hacer clic en Ver todo para ejecutar la consulta y que se devuelvan todos los registros. |
| Hide graph (Ocultar gráfico) |Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Consulte [Configuración común](#name-value-separator) para obtener más información. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Consulte [Configuración común](#navigation-query) para obtener más información. |
| **Lista** |**&gt; Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para habilitar los umbrales.  Consulte [Configuración común](#thresholds) para obtener más información. |

## <a name="donut--list-part"></a>Elemento Donut & list (Anillo y lista)
El encabezado muestra un solo número resumido obtenido a partir de una columna de valor de una consulta de registros.  El anillo muestra gráficamente los resultados de los tres registros principales.

![Vista Donut & list (Anillo y lista)](media/log-analytics-view-designer/view-donut-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Encabezado** | |
| Título |Texto que se muestra en la parte superior del encabezado. |
| Subtítulo |Texto que se muestra bajo el título de la parte superior del encabezado. |
| **Anillo** | |
| Consultar |Consulta que se ejecutará en el anillo.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico. |
| **Anillo** |**&gt; Centro** |
| Texto |Texto que se muestra bajo el valor dentro del anillo. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados en un solo valor.<br><br>-Sum: suma los valores de todos los registros.<br>- Percentage: porcentaje de los registros que devuelven los valores de **Valores de resultado usados en la operación central** con respecto al número total de registros en la consulta. |
| Valores de resultado usados en la operación central |También puede hacer clic en el signo más para agregar uno o varios valores.  Los resultados de la consulta se reducirán a los registros con los valores de propiedad especificados.  Si no se agrega ninguno, se incluirán todos los registros en la consulta. |
| **Opciones adicionales** |**&gt; Colores** |
| Color 1<br>Color 2<br>Color 3 |Seleccione el color de cada uno de los valores que aparecen en el anillo. |
| **Opciones adicionales** |**&gt; Asignación de color avanzada** |
| Valor de campo |Escriba el nombre de un campo para que se muestre como otro color distinto si está incluido en el anillo. |
| Color |Seleccione el color del campo único. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Consulte [Configuración común](#name-value-separator) para obtener más información. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Consulte [Configuración común](#navigation-query) para obtener más información. |
| **Lista** |**&gt; Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para habilitar los umbrales.  Consulte [Configuración común](#thresholds) para obtener más información. |

## <a name="two-timelines--list-part"></a>Elemento Dos escalas de tiempo y lista
El encabezado muestra los resultados de dos consultas de registro en un periodo como gráficos de columnas con una llamada que presenta un solo número resumido obtenido a partir de una columna de valor de una consulta de registro.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo.

![Vista Dos escalas de tiempo y lista](media/log-analytics-view-designer/view-two-timelines-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Primer gráfico<br>Segundo gráfico** | |
| Leyenda |Texto que se muestra bajo la llamada de la primera serie. |
| Color |Color que se utiliza para las columnas de la serie. |
| Consultar |Consulta que se ejecutará en la primera serie.  La cantidad de registros en cada intervalo de tiempo se representa mediante las columnas de gráfico. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br><br>-Sum: suma del valor de todos los registros.<br>- Average: promedio del valor de todos los registros.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- First Sample: valor del primer intervalo incluido en el gráfico.<br>- Count: cantidad total de los registros que devuelve la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Consulte [Configuración común](#navigation-query) para obtener más información. |
| **Lista** |**&gt; Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para habilitar los umbrales.  Consulte [Configuración común](#thresholds) para obtener más información. |

## <a name="information-part"></a>Elemento Información
El encabezado muestra texto estático y un vínculo opcional.  La lista muestra uno o varios elementos con texto estático y un título.

![Vista Información](media/log-analytics-view-designer/view-information.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Color |Color de fondo del encabezado. |
| **Encabezado** | |
| Imagen |Archivo de imagen que se mostrará en el encabezado. |
| Etiqueta |Texto que se muestra en el encabezado. |
| **Encabezado** |**&gt; Vínculo** |
| Etiqueta |Texto del vínculo. |
| URL |Dirección URL del vínculo. |
| **Elementos de información** | |
| Título |Texto que se muestra en el título de cada elemento. |
| Contenido |Texto que se muestra en cada elemento. |

## <a name="line-chart-callout--list-part"></a>Elemento Gráfico de líneas, llamada, lista
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo y una llamada con un valor resumido.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo.

![Vista Gráfico de líneas, llamada, lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Encabezado** | |
| Título |Texto que se muestra en la parte superior del encabezado. |
| Subtítulo |Texto que se muestra bajo el título de la parte superior del encabezado. |
| **Gráfico de líneas** | |
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interva**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**&gt; Llamada** |
| Título de la llamada |Texto que se muestra arriba del valor de la llamada. |
| Nombre de la serie |Valor de propiedad de la serie que se va a utilizar para el valor de la llamada.  Si no se proporciona ninguna serie, se utilizarán todos los registros de la consulta. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br><br>- Average: promedio del valor de todos los registros.<br>-Count: cantidad total de los registros que devuelve la consulta.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- Max: valor máximo de los intervalos incluidos en el gráfico.<br>- Min: valor mínimo de los intervalos incluidos en el gráfico.<br>- Sum: suma del valor de todos los registros. |
| **Gráfico de líneas** |**&gt; Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Consulte [Configuración común](#name-value-separator) para obtener más información. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Consulte [Configuración común](#navigation-query) para obtener más información. |
| **Lista** |**&gt; Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para habilitar los umbrales.  Consulte [Configuración común](#thresholds) para obtener más información. |

## <a name="line-chart--list-part"></a>Elemento gráfico de líneas y lista
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  La lista muestra los diez primeros resultados de una consulta con un gráfico que indica el valor relativo de una columna numérica o cómo cambia en un periodo.

![Vista Gráfico de líneas y lista](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Encabezado** | |
| Título |Texto que se muestra en la parte superior del encabezado. |
| Subtítulo |Texto que se muestra bajo el título de la parte superior del encabezado. |
| **Gráfico de líneas** | |
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interva**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**&gt; Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Hide graph (Ocultar gráfico) |Seleccione esta opción para deshabilitar el gráfico a la derecha de la columna numérica. |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Color |Color de las barras o los minigráficos. |
| Operación |Operación que se realiza en el minigráfico.  Consulte [Configuración común](#sparklines) para obtener más información. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Consulte [Configuración común](#name-value-separator) para obtener más información. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Consulte [Configuración común](#navigation-query) para obtener más información. |
| **Lista** |**&gt; Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |
| **Lista** |**&gt; Umbrales** |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para habilitar los umbrales.  Consulte [Configuración común](#thresholds) para obtener más información. |

## <a name="stack-of-line-charts-part"></a>Elemento Stack of line charts (Pila de gráficos de líneas)
Muestra tres gráficos de líneas independientes con varias series de una consulta de registro en un periodo.

![Stack of line charts (Pila de gráficos de líneas)](media/log-analytics-view-designer/view-stack-line-charts.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**&gt; Encabezado** |
| Título |Texto que se muestra en la parte superior del gráfico. |
| Subtítulo |Texto que se muestra bajo el título de la parte superior del gráfico. |
| **Gráfico 1<br>Gráfico 2<br>Gráfico 3** |**Gráfico de líneas** |
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interva**, se usarán intervalos horarios en el eje X. |
| **Gráfico** |**&gt; Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |

## <a name="common-settings"></a>Configuración común
En las secciones siguientes se describe la configuración común a varios elementos de visualización.

### <a name="name-value-separator">Name &amp; Value Separator</a> (Separador de nombre y valor)
Delimitador de carácter único en caso de que quiera analizar la propiedad text de una consulta de lista en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.

Por ejemplo, una propiedad llamada "*location*" que incluya valores como *Redmond-Building41* y *Bellevue-Building12*.  Puede especificar "–" para el separador de nombre y valor, y *City-Building* para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "*City*" y "*Building*".

### <a name="navigation-query">Consulta de navegación</a>
La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.

Por ejemplo, si la consulta tiene una columna llamada "*Equipo*" y la consulta de navegación es *{selected item}*, se ejecutaría una consulta como *Equipo="miEquipo"* cuando el usuario seleccione un equipo.  Si la consulta de navegación es *Type=Event {selected item}*, se ejecutaría la consulta *Type=Event Equipo="miEquipo"*.

### <a name="sparklines">Minigráficos</a>
Un minigráfico es un pequeño gráfico de líneas que muestra el valor de una entrada de lista en un periodo.  Para los elementos de visualización con una lista, puede elegir si quiere mostrar una barra horizontal que indique el valor relativo de una columna numérica o un minigráfico que señale su valor en un periodo.

En la tabla siguiente se describe la configuración de los minigráficos.

| Configuración | Descripción |
|:--- |:--- |
| Enable sparklines (Habilitar los minigráficos) |Seleccione esta opción para mostrar los minigráficos en lugar de la barra horizontal. |
| Operación |Si los minigráficos están habilitados, será la operación que se realizará en cada propiedad de la lista para calcular los valores del minigráfico.<br><br>- Last Sample: último valor de la serie en el intervalo de tiempo.<br>- Max: valor máximo de la serie en el intervalo de tiempo.<br>- Min: valor mínimo de la serie en el intervalo de tiempo.<br>- Sum: suma de los valores de la serie en el intervalo de tiempo.<br>- Summary: utiliza el mismo comando measure que la consulta del encabezado. |

### <a name="thresholds">Umbrales</a>
Los umbrales permiten mostrar un icono de color junto a cada elemento de una lista, con lo que obtendrá un indicador visual rápido de elementos que superan un valor determinado o que se encuentren dentro de un intervalo concreto.  Por ejemplo, podría mostrar un icono verde para los elementos que tienen un valor aceptable; amarillo si el valor está dentro de un intervalo que indica una advertencia, y rojo si supera un valor de error.

Al habilitar umbrales en un elemento, debe especificar uno o varios umbrales.  Si el valor de un elemento es mayor que un valor de umbral y menor que el valor de umbral siguiente, se utiliza ese color.  Si el elemento es mayor que el valor de umbral máximo, se establece ese color.   

Cada conjunto de umbral tiene un umbral con un valor de **Predeterminado**.  Este es el color que se establece si no se superan los valores.  Puede agregar o quitar umbrales haciendo clic en los botones **+** o **x**.

En la tabla siguiente se describe la configuración de los umbrales.

| Configuración | Description |
|:--- |:--- |
| Enable Thresholds (Habilitar umbrales) |Seleccione esta opción para mostrar un icono de color a la izquierda de cada valor que indica su estado en relación con los umbrales especificados. |
| Nombre |Nombre para identificar el valor de umbral. |
| Umbral |Valor del umbral.  El color de estado de cada elemento de lista se establece en el color del valor de umbral máximo superado por el valor del elemento.  Hay un umbral Predeterminado, que es el color para los casos en que no se superen valores de umbral. |
| Color |Color del valor del umbral. |

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para poder realizar consultas en elementos de visualización.
