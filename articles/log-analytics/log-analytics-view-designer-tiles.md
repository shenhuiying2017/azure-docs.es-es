---
title: "Referencia de los iconos del Diseñador de vistas de Log Analytics (OMS) | Microsoft Docs"
description: "El Diseñador de vistas de Log Analytics permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo se proporciona una referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas."
services: log-analytics
documentationcenter: 
author: bwren
manager: jwhit
editor: 
ms.assetid: 41787c8f-6c13-4520-b0d3-5d3d84fcf142
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2017
ms.author: bwren
ms.openlocfilehash: 2bce5c63b4c6edd3753f1b234cc4f493dcf53dea
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2017
---
# <a name="log-analytics-view-designer-tile-reference"></a>Referencia de los iconos del Diseñador de vistas de Log Analytics
El Diseñador de vistas de Log Analytics permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo se proporciona una referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.

Estos son otros de los artículos disponibles sobre el Diseñador de vistas:

* [Diseñador de vistas](log-analytics-view-designer.md): información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas.
* [Referencia de los elementos de visualización](log-analytics-view-designer-parts.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas.

>[!NOTE]
> Si el área de trabajo se ha actualizado al [nuevo lenguaje de consulta de Log Analytics](log-analytics-log-search-upgrade.md), las consultas de todas las vistas se deben escribir en el [nuevo lenguaje de consulta](https://go.microsoft.com/fwlink/?linkid=856078).  Todas las vistas que se crearon antes de actualizar el área de trabajo se convertirán automáticamente.

En la siguiente tabla se muestran los distintos tipos de iconos que se encuentran disponibles en el Diseñador de vistas.  En las secciones siguientes se describen en profundidad todos los tipos de iconos y sus propiedades.

| Icono | Descripción |
|:--- |:--- |
| [Number](#number-tile) |Un solo número que muestra la cantidad de registros de una consulta. |
| [Dos números](#two-numbers-tile) |Dos números que muestra las cantidades de registros de dos consultas diferentes. |
| [Anillo](#donut-tile) |Gráfico de anillos basado en una consulta con un valor de resumen en el centro. |
| [Llamada de gráfico de líneas](#line-chart-amp-callout-tile) |Gráfico de líneas basado en una consulta y una llamada con un valor de resumen. |
| [Gráfico de líneas](#line-chart-tile) |Gráfico de líneas basado en una consulta. |
| [Dos escalas de tiempo](#two-timelines-tile) |Gráfico de columnas con dos series, cada una de ellas basada en una consulta independiente. |

## <a name="number-tile"></a>Icono de Número
El icono **Número** muestra un solo número que muestra la cantidad de registros de una consulta de registro y una etiqueta.

![Icono de Número](media/log-analytics-view-designer/tile-number.png)

| Configuración | Descripción |
|:--- |:--- |
| Nombre |Texto que se muestra en la parte superior del icono. |
| Descripción |Texto que se muestra bajo el nombre del icono. |
| **Icono** | |
| Leyenda |Texto que se muestra bajo el valor. |
| Consultar |La consulta que se ejecutará.  Se mostrará la cantidad de registros que devuelva la consulta. |
| **Avanzado** |**> Comprobación del flujo de datos** |
| Enabled |Seleccione si debe habilitarse la comprobación del flujo de datos en el icono.  Esta opción proporciona un mensaje alternativo si no hay datos disponibles para el icono.  Normalmente, se utiliza para ofrecer un mensaje durante el periodo temporal en el que se instala la vista y se reflejan los datos en ella. |
| Consultar |Consulta que se ejecutará para comprobar si hay datos disponibles para la vista.  Si la consulta no devuelve resultados, se muestra un mensaje en lugar del valor de la consulta principal. |
| Message |Mensaje que se muestra si la consulta de comprobación del flujo de datos no devuelve datos.  Si no proporciona ningún mensaje, se mostrará *Realizando evaluación*. |


## <a name="two-numbers-tile"></a>Icono de Dos números
El icono de **Dos números** muestra dos números que revelan la cantidad de registros de dos consultas de registro diferentes y una etiqueta en cada uno de ellos.

![Icono de Dos números](media/log-analytics-view-designer/tile-two-numbers.png)

| Configuración | Descripción |
|:--- |:--- |
| Nombre |Texto que se muestra en la parte superior del icono. |
| Descripción |Texto que se muestra bajo el nombre del icono. |
| **Primer icono** | |
| Leyenda |Texto que se muestra bajo el valor. |
| Consultar |La consulta que se ejecutará.  Se mostrará la cantidad de registros que devuelva la consulta. |
| **Segundo icono** | |
| Leyenda |Texto que se muestra bajo el valor. |
| Consultar |La consulta que se ejecutará.  Se mostrará la cantidad de registros que devuelva la consulta. |
| **Avanzado** |**> Comprobación del flujo de datos** |
| Enabled |Seleccione si debe habilitarse la comprobación del flujo de datos en el icono.  Esta opción proporciona un mensaje alternativo si no hay datos disponibles para el icono.  Normalmente, se utiliza para ofrecer un mensaje durante el periodo temporal en el que se instala la vista y se reflejan los datos en ella. |
| Consultar |Consulta que se ejecutará para comprobar si hay datos disponibles para la vista.  Si la consulta no devuelve resultados, se muestra un mensaje en lugar del valor de la consulta principal. |
| Message |Mensaje que se muestra si la consulta de comprobación del flujo de datos no devuelve datos.  Si no proporciona ningún mensaje, se mostrará *Realizando evaluación*. |


## <a name="donut-tile"></a>Icono Anillo
El icono **Anillo** muestra un solo número resumido obtenido a partir de una columna de valor de una consulta de registros.  El anillo muestra gráficamente los resultados de los tres registros principales.

![Icono Anillo](media/log-analytics-view-designer/tile-donut.png)

| Configuración | Descripción |
|:--- |:--- |
| Nombre |Texto que se muestra en la parte superior del icono. |
| Descripción |Texto que se muestra bajo el nombre del icono. |
| **Anillo** | |
| Consultar |Consulta que se ejecutará en el anillo.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados. |
| **Anillo** |**> Centro** |
| Texto |Texto que se muestra bajo el valor dentro del anillo. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados en un solo valor.<br><br>- Sum: suma los valores de todos los registros con el valor de propiedad.<br>- Percentage: porcentaje de los valores sumados de los registros con el valor de propiedad en comparación con los valores de suma de todos los registros. |
| Valores de resultado usados en la operación central |También puede hacer clic en el signo más para agregar uno o varios valores.  Los resultados de la consulta se reducirán a los registros con los valores de propiedad especificados.  Si no se agrega ninguno, se incluirán todos los registros en la consulta. |
| **Anillo** |**> Opciones adicionales** |
| Colores |Color que muestra en cada una de las tres propiedades principales.  Si quiere especificar colores alternativos para valores de propiedad concretos, utilice la característica Asignación de color avanzada. |
| Asignación de color avanzada |Muestra un color para valores de propiedad concretos.  Si el valor especificado se encuentra en las tres principales, se muestra el color alternativo en lugar del estándar.  Si la propiedad no está en las tres principales, no se muestra el color. |
| **Avanzado** |**> Comprobación del flujo de datos** |
| Enabled |Seleccione si debe habilitarse la comprobación del flujo de datos en el icono.  Esta opción proporciona un mensaje alternativo si no hay datos disponibles para el icono.  Normalmente, se utiliza para ofrecer un mensaje durante el periodo temporal en el que se instala la vista y se reflejan los datos en ella. |
| Consultar |Consulta que se ejecutará para comprobar si hay datos disponibles para la vista.  Si la consulta no devuelve resultados, se muestra un mensaje en lugar del valor de la consulta principal. |
| Message |Mensaje que se muestra si la consulta de comprobación del flujo de datos no devuelve datos.  Si no proporciona ningún mensaje, se mostrará *Realizando evaluación*. |


## <a name="line-chart-tile"></a>Icono de Gráfico de líneas
El icono de **Gráfico de línea**s muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  

![Icono de Llamada de gráfico de líneas](media/log-analytics-view-designer/tile-line-chart.png)

| Configuración | Descripción |
|:--- |:--- |
| Nombre |Texto que se muestra en la parte superior del icono. |
| Descripción |Texto que se muestra bajo el nombre del icono. |
| **Gráfico de líneas** | |
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interva**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  **Tipo de unidad** especifica la categoría de la unidad y define los valores disponibles del **tipo de unidad actual**.  Si selecciona un valor en **Convertir en**, los valores numéricos se convertirán del tipo **Unidad actual** a **Convertir en**. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Avanzado** |**> Comprobación del flujo de datos** |
| Enabled |Seleccione si debe habilitarse la comprobación del flujo de datos en el icono.  Esta opción proporciona un mensaje alternativo si no hay datos disponibles para el icono.  Normalmente, se utiliza para ofrecer un mensaje durante el periodo temporal en el que se instala la vista y se reflejan los datos en ella. |
| Consultar |Consulta que se ejecutará para comprobar si hay datos disponibles para la vista.  Si la consulta no devuelve resultados, se muestra un mensaje en lugar del valor de la consulta principal. |
| Message |Mensaje que se muestra si la consulta de comprobación del flujo de datos no devuelve datos.  Si no proporciona ningún mensaje, se mostrará *Realizando evaluación*. |


## <a name="line-chart--callout-tile"></a>Icono de Llamada de gráfico de líneas
El icono de **Llamada de gráfico de líneas** muestra un gráfico de líneas con varias series de una consulta de registro en un periodo y una llamada con un valor resumido.  

![Icono de Llamada de gráfico de líneas](media/log-analytics-view-designer/tile-line-chart-callout.png)

| Configuración | Descripción |
|:--- |:--- |
| Nombre |Texto que se muestra en la parte superior del icono. |
| Descripción |Texto que se muestra bajo el nombre del icono. |
| **Gráfico de líneas** | |
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interva**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**> Llamada** |
| Llamada |Título: texto que se muestra encima del valor de la llamada. |
| Nombre de la serie |Valor de propiedad de la serie que se va a utilizar para el valor de la llamada.  Si no se proporciona ninguna serie, se utilizarán todos los registros de la consulta. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br>- Average: promedio del valor de todos los registros.<br><br>- Count: cantidad total de los registros que devuelve la consulta.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- Max: valor máximo de los intervalos incluidos en el gráfico.<br>- Min: valor mínimo de los intervalos incluidos en el gráfico.<br>- Sum: suma del valor de todos los registros. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  **Tipo de unidad** especifica la categoría de la unidad y define los valores disponibles del **tipo de unidad actual**.  Si selecciona un valor en **Convertir en**, los valores numéricos se convertirán del tipo **Unidad actual** a **Convertir en**. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Avanzado** |**> Comprobación del flujo de datos** |
| Enabled |Seleccione si debe habilitarse la comprobación del flujo de datos en el icono.  Esta opción proporciona un mensaje alternativo si no hay datos disponibles para el icono.  Normalmente, se utiliza para ofrecer un mensaje durante el periodo temporal en el que se instala la vista y se reflejan los datos en ella. |
| Consultar |Consulta que se ejecutará para comprobar si hay datos disponibles para la vista.  Si la consulta no devuelve resultados, se muestra un mensaje en lugar del valor de la consulta principal. |
| Message |Mensaje que se muestra si la consulta de comprobación del flujo de datos no devuelve datos.  Si no proporciona ningún mensaje, se mostrará *Realizando evaluación*. |


## <a name="two-timelines-tile"></a>Icono de Dos escalas de tiempo
El icono de **Dos escalas de tiempo** muestra los resultados de dos consultas de registro en un periodo como gráficos de columnas.  Para cada serie, se muestra una llamada.  

![Icono de Dos escalas de tiempo](media/log-analytics-view-designer/tile-two-timelines.png)

| Configuración | Descripción |
|:--- |:--- |
| Nombre |Texto que se muestra en la parte superior del icono. |
| Descripción |Texto que se muestra bajo el nombre del icono. |
| Primer gráfico | |
| Leyenda |Texto que se muestra bajo la llamada de la primera serie. |
| Color |Color que se utiliza para las columnas de la primera serie. |
| Consulta de gráfico |Consulta que se ejecutará en la primera serie.  La cantidad de registros en cada intervalo de tiempo se representa mediante las columnas de gráfico. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br><br>- Average: promedio del valor de todos los registros.<br>- Count: cantidad total de los registros que devuelve la consulta.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- Max: valor máximo de los intervalos incluidos en el gráfico. |
| **Segundo gráfico** | |
| Leyenda |Texto que se muestra bajo la llamada de la segunda serie. |
| Color |Color que se utiliza para las columnas de la segunda serie. |
| Consulta de gráfico |Consulta que se ejecutará en la segunda serie.  La cantidad de registros en cada intervalo de tiempo se representa mediante las columnas de gráfico. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br><br>- Average: promedio del valor de todos los registros.<br>- Count: cantidad total de los registros que devuelve la consulta.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- Max: valor máximo de los intervalos incluidos en el gráfico. |
| **Avanzado** |**> Comprobación del flujo de datos** |
| Enabled |Seleccione si debe habilitarse la comprobación del flujo de datos en el icono.  Esta opción proporciona un mensaje alternativo si no hay datos disponibles para el icono.  Normalmente, se utiliza para ofrecer un mensaje durante el periodo temporal en el que se instala la vista y se reflejan los datos en ella. |
| Consultar |Consulta que se ejecutará para comprobar si hay datos disponibles para la vista.  Si la consulta no devuelve resultados, se muestra un mensaje en lugar del valor de la consulta principal. |
| Message |Mensaje que se muestra si la consulta de comprobación del flujo de datos no devuelve datos.  Si no proporciona ningún mensaje, se mostrará *Realizando evaluación*. |


## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para poder realizar consultas en iconos.
* Agregue [elementos de visualización](log-analytics-view-designer-parts.md) a la vista personalizada.
