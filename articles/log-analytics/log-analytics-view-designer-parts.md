---
title: Diseñador de vistas de Log Analytics | Microsoft Docs
description: El Diseñador de vistas de Log Analytics permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo se proporciona una referencia de la configuración de cada uno de los elementos de visualización que hay disponibles para usarse en las vistas personalizadas.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: bwren

---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Referencia de los elementos de visualización del Diseñador de vistas de Log Analytics
El Diseñador de vistas de Log Analytics permite crear vistas personalizadas en la consola de OMS que contienen diferentes visualizaciones de datos del repositorio OMS. En este artículo se proporciona una referencia de la configuración de cada uno de los elementos de visualización que hay disponibles para usarse en las vistas personalizadas.

Estos son otros de los artículos disponibles sobre el Diseñador de vistas:

* [Diseñador de vistas](log-analytics-view-designer.md): información general del Diseñador de vistas, y procedimientos para crear y editar vistas personalizadas.
* [Referencia de los iconos](log-analytics-view-designer-tiles.md): referencia de la configuración de cada uno de los iconos que hay disponibles para usarse en las vistas personalizadas. 

En la siguiente tabla se describen los distintos tipos de iconos que se encuentran disponibles en el Diseñador de vistas.  En las secciones siguientes se describen en profundidad todos los tipos de iconos y sus propiedades.

| Tipo de vista | Descripción |
|:--- |:--- |
| [Lista de consultas](#list-of-queries-part) |Muestra una lista de las consultas de búsqueda de registros.  El usuario puede hacer clic en cada una de las consultas para mostrar los resultados.  La vista incluirá una sola consulta de forma predeterminada y puede hacer clic en **+ Consulta** para agregar más. |
| [Number & list](#number-amp-list-part) (Número y lista) |El encabezado tiene un solo número que muestra la cantidad de registros de una consulta de búsqueda de registros.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica. |
| [Dos números y lista](#two-numbers-amp-list-part) |El encabezado tiene dos números que muestran la cantidad de registros de consultas de búsqueda de registros independientes.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica. |
| [Donut & list](#donut-amp-list-part) (Anillo y lista) |El encabezado muestra un solo número resumido obtenido a partir de una columna de valor de una consulta de registros.  El anillo muestra gráficamente los resultados de los tres registros principales. |
| [Dos escalas de tiempo y lista](#two-timelines-amp-list-part) |El encabezado muestra los resultados de dos consultas de registro en un periodo como gráficos de columnas con una llamada que presenta un solo número resumido obtenido a partir de una columna de valor de una consulta de registro.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica. |
| [Información](#information-part) |El encabezado muestra texto estático y un vínculo opcional.  La lista muestra uno o varios elementos con texto estático y un título. |
| [Gráfico de líneas, llamada, lista](#line-chart-callout-amp-list-part) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo y una llamada con un valor resumido.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica. |
| [Gráfico de líneas y lista](#line-chart-amp-list-part) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica. |
| [Gráfico de líneas y minigráficos](#line-chart-amp-sparklines-part) |El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  La lista muestra los diez primeros resultados de una consulta con un minigráfico que presenta el valor de una columna numérica en el periodo seleccionado. |

## <a name="list-of-queries-part"></a>Elemento Lista de consultas
Muestra una lista de las consultas de búsqueda de registros.  El usuario puede hacer clic en cada una de las consultas para mostrar los resultados.  

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

## <a name="number-&-list-part"></a>Elemento Number & list (Número y lista)
El encabezado tiene un solo número que muestra la cantidad de registros de una consulta de búsqueda de registros.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica.

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
| Color |Color de las barras. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.<br><br>Por ejemplo, una propiedad llamada "location" que incluya valores como *Redmond-Building41* y *Bellevue-Building12*.  Puede especificar "–" para el separador de nombre y valor, y *City-Building* para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "*City*" y "*Building*". |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Utilice {selected item} para incluir la sintaxis del elemento que ha seleccionado el usuario. <br><br>Por ejemplo, si la consulta tiene una columna llamada "Equipo" y la consulta de navegación es {selected item}, se ejecutaría una consulta como Equipo="miEquipo" cuando el usuario seleccione un equipo.  Si la consulta de navegación es Type=Event {selected item}, se ejecutaría la consulta Type=Event Equipo="miEquipo". |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

## <a name="two-numbers-&-list-part"></a>Elemento Dos números y lista
El encabezado tiene dos números que muestran la cantidad de registros de consultas de búsqueda de registros independientes.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica.

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
| **Segundo título** | |
| Leyenda |Texto que se muestra en la parte superior del encabezado. |
| Consultar |Consulta que se ejecutará en el encabezado.  Se mostrará la cantidad de registros que devuelva la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrarán las primeras dos propiedades de los diez primeros registros en los resultados.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Las barras se crean automáticamente en función del valor relativo de la columna numérica.<br><br>Use el comando sort de la consulta para ordenar los registros de la lista.  El usuario puede hacer clic en Ver todo para ejecutar la consulta y que se devuelvan todos los registros. |
| Color |Color de las barras. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.<br><br>Por ejemplo, una propiedad llamada "location" que incluya valores como Redmond-Building41 y Bellevue-Building12.  Puede especificar "–" para el separador de nombre y valor, y City-Building para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "City" y "Building". |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Utilice {selected item} para incluir la sintaxis del elemento que ha seleccionado el usuario. <br><br>Por ejemplo, si la consulta tiene una columna llamada "Equipo" y la consulta de navegación es {selected item}, se ejecutaría una consulta como Equipo="miEquipo" cuando el usuario seleccione un equipo.  Si la consulta de navegación es Type=Event {selected item}, se ejecutaría la consulta Type=Event Equipo="miEquipo". |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

## <a name="donut-&-list-part"></a>Elemento Donut & list (Anillo y lista)
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
| **Anillo** |**> Centro** |
| Texto |Texto que se muestra bajo el valor dentro del anillo. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados en un solo valor.<br><br>-Sum: suma los valores de todos los registros.<br>- Percentage: porcentaje de los registros que devuelven los valores de **Valores de resultado usados en la operación central** con respecto al número total de registros en la consulta. |
| Valores de resultado usados en la operación central |También puede hacer clic en el signo más para agregar uno o varios valores.  Los resultados de la consulta se reducirán a los registros con los valores de propiedad especificados.  Si no se agrega ninguno, se incluirán todos los registros en la consulta. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Color |Color de las barras. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.<br><br>Por ejemplo, una propiedad llamada "location" que incluya valores como *Redmond-Building41* y *Bellevue-Building12*.  Puede especificar "–" para el separador de nombre y valor, y *City-Building* para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "*City*" y "*Building*". |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.<br><br>Por ejemplo, si la consulta tiene una columna llamada "*Equipo*" y la consulta de navegación es *{selected item}*, se ejecutaría una consulta como *Equipo="miEquipo"* cuando el usuario seleccione un equipo.  Si la consulta de navegación es *Type=Event {selected item}*, se ejecutaría la consulta *Type=Event Equipo="miEquipo"*. |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

## <a name="two-timelines-&-list-part"></a>Elemento Dos escalas de tiempo y lista
El encabezado muestra los resultados de dos consultas de registro en un periodo como gráficos de columnas con una llamada que presenta un solo número resumido obtenido a partir de una columna de valor de una consulta de registro.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica.   

![Vista Dos escalas de tiempo y lista](media/log-analytics-view-designer/view-two-timelines-list.png)

| Configuración | Descripción |
|:--- |:--- |
| **General** | |
| Título de grupo |Texto que se muestra en la parte superior del icono. |
| Nuevo grupo |Seleccione esta opción para crear un grupo en la vista empezando por la actual. |
| Icono |Archivo de imagen que se mostrará junto a los resultados en el encabezado. |
| Usar icono |Seleccione esta opción para que se muestre el icono. |
| **Primer gráfico** | |
| Leyenda |Texto que se muestra bajo la llamada de la primera serie. |
| Color |Color que se utiliza para las columnas de la primera serie. |
| Consultar |Consulta que se ejecutará en la primera serie.  La cantidad de registros en cada intervalo de tiempo se representa mediante las columnas de gráfico. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br><br>- Average: promedio del valor de todos los registros.<br>- Count: cantidad total de los registros que devuelve la consulta.<br>- First Sample: valor del primer intervalo incluido en el gráfico.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- Max: valor máximo de los intervalos incluidos en el gráfico. |
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interval**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Color |Color de las barras. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.<br><br>Por ejemplo, una propiedad llamada "location" que incluya valores como *Redmond-Building41* y *Bellevue-Building12*.  Puede especificar "–" para el separador de nombre y valor, y *City-Building* para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "*City*" y "*Building*". |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.<br><br>Por ejemplo, si la consulta tiene una columna llamada "*Equipo*" y la consulta de navegación es *{selected item}*, se ejecutaría una consulta como *Equipo="miEquipo"* cuando el usuario seleccione un equipo.  Si la consulta de navegación es *Type=Event {selected item}*, se ejecutaría la consulta *Type=Event Equipo="miEquipo"*. |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

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
| **Encabezado** |**> Vínculo** |
| Etiqueta |Texto del vínculo. |
| URL |Dirección URL del vínculo. |
| **Elementos de información** | |
| Título |Texto que se muestra en el título de cada elemento. |
| Contenido |Texto que se muestra en cada elemento. |

## <a name="line-chart,-callout,-&-list-part"></a>Elemento Gráfico de líneas, llamada, lista
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo y una llamada con un valor resumido.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica.

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
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interval**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**> Llamada** |
| Título de la llamada |Texto que se muestra arriba del valor de la llamada. |
| Nombre de la serie |Valor de propiedad de la serie que se va a utilizar para el valor de la llamada.  Si no se proporciona ninguna serie, se utilizarán todos los registros de la consulta. |
| Operación |Operación que se va a realizar en la propiedad value para resumir los resultados de la llamada en un solo valor.<br><br>- Average: promedio del valor de todos los registros.<br>-Count: cantidad total de los registros que devuelve la consulta.<br>- Last Sample: valor del último intervalo incluido en el gráfico.<br>- Max: valor máximo de los intervalos incluidos en el gráfico.<br>- Min: valor mínimo de los intervalos incluidos en el gráfico.<br>- Sum: suma del valor de todos los registros. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Color |Color de las barras. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.<br><br>Por ejemplo, una propiedad llamada "location" que incluya valores como *Redmond-Building41* y *Bellevue-Building12*.  Puede especificar "–" para el separador de nombre y valor, y *City-Building* para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "*City*" y "*Building*". |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.<br><br>Por ejemplo, si la consulta tiene una columna llamada "*Equipo*" y la consulta de navegación es *{selected item}*, se ejecutaría una consulta como *Equipo="miEquipo"* cuando el usuario seleccione un equipo.  Si la consulta de navegación es *Type=Event {selected item}*, se ejecutaría la consulta *Type=Event Equipo="miEquipo"*. |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

## <a name="line-chart-&-list-part"></a>Elemento gráfico de líneas y lista
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  La lista muestra los diez primeros resultados de una consulta con barras horizontales que indican el valor relativo de una columna numérica.

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
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interval**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Color |Color de las barras. |
| Name & Value Separator (Separador de nombre y valor) |Delimitador de carácter único en caso de que quiera analizar la propiedad text en varios valores.  Si especifica un delimitador, puede proporcionar nombres para cada campo separándolos con el mismo delimitador del cuadro Nombre.<br><br>Por ejemplo, una propiedad llamada "location" que incluya valores como *Redmond-Building41* y *Bellevue-Building12*.  Puede especificar "–" para el separador de nombre y valor, y *City-Building* para el nombre.  De este modo, se analizaría cada valor en dos propiedades denominadas "*City*" y "*Building*". |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.<br><br>Por ejemplo, si la consulta tiene una columna llamada "*Equipo*" y la consulta de navegación es *{selected item}*, se ejecutaría una consulta como *Equipo="miEquipo"* cuando el usuario seleccione un equipo.  Si la consulta de navegación es *Type=Event {selected item}*, se ejecutaría la consulta *Type=Event Equipo="miEquipo"*. |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

## <a name="line-chart-&-sparklines-part"></a>Elemento Gráfico de líneas y minigráficos
El encabezado muestra un gráfico de líneas con varias series de una consulta de registro en un periodo.  La lista muestra los diez primeros resultados de una consulta con un minigráfico que presenta el valor de una columna numérica en el periodo seleccionado.

![Vista Gráfico de líneas y minigráficos](media/log-analytics-view-designer/view-line-chart-sparklines.png)

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
| Consultar |Consulta que se ejecutará en el gráfico de líneas.  La primera propiedad debe ser un valor de texto, y la segunda, un valor numérico.  Normalmente, se trata de una consulta que usa la palabra clave **measure** para resumir los resultados.  Si la consulta utiliza la palabra clave **interval**, el eje X del gráfico empleará este intervalo de tiempo.  Si la consulta no incluye la palabra clave **interval**, se usarán intervalos horarios en el eje X. |
| **Gráfico de líneas** |**> Eje Y** |
| Usar escala logarítmica |Seleccione esta opción para usar una escala logarítmica en el eje Y. |
| Unidades |Especifica las unidades de los valores que devuelve la consulta.  Esta información se utiliza para mostrar etiquetas en el gráfico que indican los tipos de valores y, de manera opcional, para convertirlos.  Tipo de unidad especifica la categoría de la unidad y define los valores disponibles del tipo de unidad actual.  Si selecciona un valor en Convertir en, los valores numéricos se convertirán del tipo Unidad actual a Convertir en. |
| Etiqueta personalizada |Texto que se muestra en el eje Y junto a la etiqueta del tipo de unidad.  Si no se especifica ninguna etiqueta, solo se mostrará el tipo de unidad. |
| **Lista** | |
| Consultar |Consulta que se ejecutará en la lista.  Se mostrará la cantidad de registros que devuelva la consulta. |
| Operación |La operación que se realizará en cada propiedad de la lista para calcular los valores del minigráfico.<br><br>- Last Sample: último valor de la serie en el intervalo de tiempo.<br>- Max: valor máximo de la serie en el intervalo de tiempo.<br>- Min: valor mínimo de la serie en el intervalo de tiempo.<br>- Sum: suma de los valores de la serie en el intervalo de tiempo.<br>- Summary: utiliza el mismo comando measure que la consulta del encabezado. |
| Consulta de navegación |La consulta que se ejecutará cuando el usuario seleccione un elemento de la lista.  Use *{selected item}* para incluir la sintaxis del elemento que ha seleccionado el usuario.<br><br>Por ejemplo, si la consulta tiene una columna llamada "*Equipo*" y la consulta de navegación es *{selected item}*, se ejecutaría una consulta como *Equipo="miEquipo"* cuando el usuario seleccione un equipo.  Si la consulta de navegación es *Type=Event {selected item}*, se ejecutaría la consulta *Type=Event Equipo="miEquipo"*. |
| **Lista** |**> Títulos de columna** |
| Nombre |Texto que se muestra en la parte superior de la primera columna de la lista. |
| Valor |Texto que se muestra en la parte superior de la segunda columna de la lista. |

## <a name="next-steps"></a>Pasos siguientes
* Obtenga información sobre las [búsquedas de registros](log-analytics-log-searches.md) para poder realizar consultas en elementos de visualización.

<!--HONumber=Oct16_HO2-->


