---
title: "Transformación Dividir columna por ejemplo mediante Azure Machine Learning Workbench"
description: "Documento de referencia para la transformación \"Dividir columna por ejemplo\""
services: machine-learning
author: ranvijaykumar
ms.author: ranku
manager: mwinkle
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: mvc, reference
ms.topic: article
ms.date: 09/14/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 013c99045621e4651a44ab99c9f695fff6004654
ms.contentlocale: es-es
ms.lasthandoff: 09/25/2017

---

# <a name="split-column-by-example-transformation"></a>Transformación Dividir columna por ejemplo
Esta transformación divide de forma predictiva el contenido de una columna en límites significativos sin que sea necesaria la intervención del usuario. El algoritmo de división selecciona los límites después de analizar el contenido de la columna. Estos límites se podrían definir por:
* Un delimitador fijo
* Varios delimitadores arbitrarios que aparezcan en contextos determinados
* Patrones de datos o ciertos tipos de entidad

Los usuarios también pueden controlar el comportamiento de división en el modo avanzado, en el que pueden especificar los delimitadores o proporcionar ejemplos de la división deseada.

En teoría, las operaciones de división también se pueden efectuar en Workbench mediante una serie de transformaciones *Derivar columna por ejemplos*. Pero si hay varias columnas, derivar cada una de ellas de forma individual, incluso siguiendo un enfoque según el ejemplo, puede ser muy lento. La división predictiva permite efectuar divisiones fácilmente sin que el usuario deba proporcionar ejemplos para cada una de las columnas. 

## <a name="how-to-perform-this-transformation"></a>Cómo llevar a cabo esta transformación

1. Seleccione la columna que quiere dividir. 
2. Seleccione **Split Column by Example** (Dividir columna por ejemplo) en el menú **Transforms** (Transformaciones). También puede hacer clic con el botón derecho en el encabezado de la columna seleccionada y seleccionar **Split Column by Example** (Dividir columna por ejemplo) en el menú contextual. Se abrirá el Editor de transformación y se agregarán columnas nuevas junto a la columna seleccionada. En este momento, Workbench analiza la columna de entrada, determina los límites de la división y sintetiza un programa para dividir la columna tal y como se muestra en la cuadrícula. El programa sintetizado se ejecuta en todas las filas de la columna. Los delimitadores, si los hay, se excluyen del resultado final.
3. Puede hacer clic en el **modo avanzado** para tener un mayor control sobre la transformación de división. 
4. Revise la salida y haga clic en **Aceptar** para aceptar la transformación. 

La transformación tiene como objetivo generar el mismo número de columnas resultantes para todas las filas. Si una fila no se puede dividir en los límites determinados, generará *null* para todas las columnas de forma predeterminada. Este comportamiento se puede modificar en el **modo avanzado**.

### <a name="transform-editor-advanced-mode"></a>Editor de transformación: Modo avanzado
El **modo avanzado** ofrece una experiencia más completa para dividir columnas. 

La selección de **Keep Delimiter Columns** (Conservar columnas delimitadoras) incluye los delimitadores del resultado final. Los delimitadores se excluyen de forma predeterminada.

La especificación de **Delimiters** (Delimitadores) invalida la lógica de selección automática de delimitadores. Se pueden especificar como **delimitadores** varios delimitadores, uno en cada línea. Todos estos caracteres se usan como delimitadores para dividir la columna.

A veces, dividir un valor en determinados límites genera un número de columnas diferente que la mayoría de las demás. En esos casos se usa **Fill Direction** (Dirección de relleno) para decidir el orden en el que se deben rellenar las columnas.

Al hacer clic en **Show suggested examples** (Mostrar ejemplos sugeridos), se muestran las filas representativas para las que el usuario debe proporcionar un ejemplo de división. El usuario puede hacer clic en la flecha **hacia arriba**, situada a la derecha de la fila sugerida, para promover la fila como un ejemplo. 

El usuario puede **eliminar una columna** o **insertar columnas nuevas** haciendo clic con el botón derecho en el encabezado de la **tabla de ejemplos**.

El usuario puede copiar y pegar los valores de una celda a otra con el fin de proporcionar un ejemplo de división.

El usuario puede alternar entre el **modo básico** y el **modo avanzado**. Para ello, debe hacer clic en los vínculos del Editor de transformación.

### <a name="editing-an-existing-transformation"></a>Editar una transformación existente

Para editar una transformación **Dividir columna por ejemplo** existente, seleccione la opción **Edit** (Editar) del paso de transformación. Al hacer clic en **Edit** (Editar), se abre el Editor de transformación en **modo avanzado** y se muestran todos los ejemplos proporcionados durante la creación de la transformación.

## <a name="examples-of-splitting-on-a-fixed-single-character-delimiter"></a>Ejemplos de división en un delimitador fijo de carácter único

Todos los campos de datos están separados por un delimitador fijo único, como una coma en un formato CSV. La transformación de división intenta deducir estos delimitadores automáticamente. Por ejemplo, en el siguiente escenario deduce automáticamente el "." como delimitador.

### <a name="splitting-ip-addresses"></a>Dividir direcciones IP

Los valores de la primera columna se dividen de forma predictiva en cuatro columnas.

|IP|IP_1|IP_2|IP_3|IP_4|
|:-----|:-----|:-----|:-----|:-----|
|192.168.0.102|192|168|0|102|
|192.138.0.101|192|138|0|101|
|192.168.0.102|192|168|0|102|
|192.158.1.202|192|158|1|202|
|192.168.0.102|192|168|0|102|
|192.169.1.102|192|169|1|102|

## <a name="examples-of-splitting-on-multiple-delimiters-within-particular-contexts"></a>Ejemplos de división en varios delimitadores en contextos determinados

Los datos del usuario pueden incluir varios delimitadores diferentes que separen los distintos campos. Además, solo algunas apariciones de una cadena delimitadora pueden representar un delimitador, pero no todas. Por ejemplo, en el siguiente caso, el conjunto de delimitadores necesario es "-", "," y ":" para generar el resultado deseado. Pero no todas las apariciones de ":" deben ser un punto de división, puesto que no queremos dividir el tiempo, sino conservarlo en una única columna. La transformación de división deduce los delimitadores dentro de los contextos en los que tienen lugar en los datos de entrada, en lugar de cualquier posible aparición del delimitador. La transformación también tiene en cuenta los tipos de datos comunes, como las fechas y horas.   

### <a name="splitting-store-opening-timings"></a>Dividir los horarios de apertura de tiendas

Los valores de la siguiente columna *Horarios* se dividen de forma predictiva en nueve columnas, que se muestran en la tabla siguiente.

|Horarios|
|:-----|
|Lunes - viernes: 7:00 a. m. - 6:00 p. m.; sábado: 9:00 a. m. - 5:00 p. m.; domingo: cerrado|
|Lunes - viernes: 9:00 a. m. - 6:00 p. m.; sábado: 4:00 a. m. - 4:00 p. m.; domingo: cerrado|
|Lunes - viernes: 8:30 a. m. - 7:00 p. m.; sábado: 3:00 a. m. - 2:30 p. m.; domingo: cerrado|
|Lunes - viernes: 8:00 a. m. - 6:00 p. m.; sábado: 2:00 a. m. - 3:00 p. m.; domingo: cerrado|
|Lunes - viernes: 4:00 a. m. - 7:00 p. m.; sábado: 9:00 a. m. - 4:00 p. m.; domingo: cerrado|
|Lunes - viernes: 8:30 a. m. - 4:30 p. m.; sábado: 9:00 a. m. - 5:00 p. m.; domingo: cerrado|
|Lunes - viernes: 5:30 a. m. - 6:30 p. m.; sábado: 5:00 a. m. - 4:00 p. m.; domingo: cerrado|
|Lunes - viernes: 8:30 a. m. - 8:30 p. m.; sábado: 6:00 a. m. - 5:00 p. m.; domingo: cerrado|
|Lunes - viernes: 8:00 a. m. - 9:00 p. m.; sábado: 9:00 a. m. - 8:00 p. m.; domingo: cerrado|
|Lunes - viernes: 10:00 a. m. - 9:30 p. m.; sábado: 9:30 a. m. - 3:00 p. m.; domingo: cerrado|

|Timings_1|Timings_2|Timings_3|Timings_4|Timings_5|Timings_6|Timings_7|Timings_8|Timings_9|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|Lunes|Viernes|7:00 a. m.|6:00 p. m.|Sábado|9:00 a. m.|5:00 p. m.|Domingo|Closed|
|Lunes|Viernes|9:00 a. m.|6:00 p. m.|Sábado|4:00 a. m.|4:00 p. m.|Domingo|Closed|
|Lunes|Viernes|8:30 a. m.|7:00 p. m.|Sábado|3:00 a. m.|2:30 p. m.|Domingo|Closed|
|Lunes|Viernes|8:00 a. m.|6:00 p. m.|Sábado|2:00 a. m.|3:00 p. m.|Domingo|Closed|
|Lunes|Viernes|4:00 a. m.|7:00 p. m.|Sábado|9:00 a. m.|4:00 p. m.|Domingo|Closed|
|Lunes|Viernes|8:30 a. m.|4:30 p. m.|Sábado|9:00 a. m.|5:00 p. m.|Domingo|Closed|
|Lunes|Viernes|5:30 a. m.|6:30 p. m.|Sábado|5:00 a. m.|4:00 p. m.|Domingo|Closed|
|Lunes|Viernes|8:30 a. m.|8:30 p. m.|Sábado|6:00 a. m.|5:00 p. m.|Domingo|Closed|
|Lunes|Viernes|8:00 a. m.|9:00 p. m.|Sábado|9:00 a. m.|8:00 p. m.|Domingo|Closed|
|Lunes|Viernes|10:00 a. m.|9:30 p. m.|Sábado|9:30 a. m.|3:00 p. m.|Domingo|Closed|

### <a name="splitting-iis-log"></a>Dividir un registro de IIS

A continuación tiene otro ejemplo de varios delimitadores arbitrarios. En este ejemplo también se incluye un delimitador contextual, "/", que no se debe dividir en las direcciones URL ni en las rutas de acceso de archivo. Resulta tedioso efectuar esta división aplicando muchas transformaciones *Derivar columna por ejemplos* y proporcionando ejemplos para cada campo. Con la transformación de división se puede llevar a cabo la división de predicción sin tener que proporcionar ejemplos.

|logtext|
|:-----|
|192.128.138.20 - - [16/Oct/2016 16:22:33 -0200] "GET /images/picture.gif HTTP/1.1" 234 343 www.yahoo.com "http://www.example.com/" "Mozilla/4.0 (compatible; MSIE 4)" "-"|
|10.128.72.213 - - [17/Oct/2016 12:43:12 +0300] "GET /news/stuff.html HTTP/1.1" 200 6233 www.aol.com "http://www.sample.com/" "Mozilla/5.0 (MSIE)" "-"|
|192.165.71.165 - - [12/Nov/2016 14:22:44 -0500] "GET /sample.ico HTTP/1.1" 342 7342 www.facebook.com "-" "Mozilla/5.0 (Windows; U; Windows NT 5.1; rv:1.7.3)" "-"|
|10.166.64.165 - - [23/Nov/2016 01:52:45 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.167.1.193 - - [16/Jan/2017 22:34:56 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|192.147.76.193 - - [28/Jan/2017 26:36:16 +0800] "GET /search.php HTTP/1.1" 400 1777 www.bing.com "-" "Mozilla/4.0 (compatible; MSIE 6.0; Windows NT 5.1)" "-"|
|192.166.64.165 - - [23/Mar/2017 01:55:25 -0800] "GET /style.css HTTP/1.1" 200 2552 www.google.com "http://www.test.com/index.html" "Mozilla/5.0 (Windows)" "-"|
|11.167.1.193 - - [16/Apr/2017 11:34:36 +0200] "GET /js/ads.js HTTP/1.1" 200 23462 www.microsoft.com "http://www.illustration.com/index.html" "Mozilla/5.0 (Windows)" "-"|

Se divide así:

|logtext_1|logtext_2|logtext_3|logtext_4|logtext_5|logtext_6|logtext_7|logtext_8|logtext_9|logtext_10|logtext_11|logtext_12|logtext_13|logtext_14|logtext_15|
|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|:-----|
|192.128.138.20|16/oct/2016|16:22:33|-0200|GET|images/picture.gif|HTTP|1.1|234|343|www.yahoo.com|http://www.ejemplo.com/|Mozilla|4.0|compatible; MSIE 4|
|10.128.72.213|17/oct/2016|12:43:12|+0300|GET|news/stuff.html|HTTP|1.1|200|6233|www.aol.com|http://www.ejemplo.com/|Mozilla|5.0|MSIE|
|192.165.71.165|12/nov/2016|14:22:44|-0500|GET|sample.ico|HTTP|1.1|342|7342|www.facebook.com|-|Mozilla|5.0|Windows; U; Windows NT 5.1; rv:1.7.3|
|10.166.64.165|23/nov/2016|01:52:45|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|192.167.1.193|16/ene/2017|22:34:56|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|
|192.147.76.193|28/ene/2017|26:36:16|+0800|GET|search.php|HTTP|1.1|400|1777|www.bing.com|-|Mozilla|4.0|compatible; MSIE 6.0; Windows NT 5.1|
|192.166.64.165|23/mar/2017|01:55:25|-0800|GET|style.css|HTTP|1.1|200|2552|www.google.com|http://www.test.com/index.html|Mozilla|5.0|Windows|
|11.167.1.193|16/abr/2017|11:34:36|+0200|GET|js/ads.js|HTTP|1.1|200|23462|www.microsoft.com|http://www.illustration.com/index.html|Mozilla|5.0|Windows|

## <a name="examples-of-splitting-without-delimiters"></a>Ejemplos de división sin delimitadores
En algunos casos no hay ningún delimitador real y los campos de datos pueden aparecer de forma contigua los unos junto a los otros. En este caso, la transformación de división detecta automáticamente patrones en los datos para deducir puntos probablemente divididos. Por ejemplo, en el siguiente escenario queremos separar el importe del tipo de moneda, y la división deduce automáticamente el límite entre los datos numéricos y los datos no numéricos como punto de división.

### <a name="splitting-amount-with-currency-symbol"></a>Dividir un importe con el símbolo de moneda

|Importe|Amount_1|Amount_2|
|:-----|:-----|:-----|
|\$14|$|14|
|£9|£|9|
|\$34|$|34|
|€ 18|€ |18|
|\$42|$|42|
|\$7|$|7|
|£42|£|42|
|\$16|$|16|
|€ 16|€ |16|
|\$15|$|15|
|\$16|$|16|
|€ 64|€ |64|

En el siguiente ejemplo nos gustaría separar los valores de peso de las unidades de medida. Otra vez, la inferencia de división detecta automáticamente el límite significativo y lo prefiere a él antes que otros posibles delimitadores, como el carácter ".". 

### <a name="splitting-weights-with-units"></a>Dividir pesos con unidades

|Peso|Weight_1|Weight_2|
|:-----|:-----|:-----|
|2,27 kg|2,27|KG|
|1 L|1|L|
|2,5 kg|2.5|KG|
|2 kg|2|KG|
|1,7 kga|1.7|kga|
|3 kg|3|KG|
|2 kg|2|KG|
|125 g|125|G|
|500 g|500|G|
|1,5 kga|1.5|kga|

## <a name="technical-notes"></a>Notas técnicas

La característica de transformación de división se basa en la técnica de **síntesis de programa predictivo**. En esta técnica, los programas de transformación de datos se aprenden automáticamente en función de los datos de entrada. Los programas se sintetizan en un lenguaje específico de dominio. El DSL se basa en delimitadores y campos que aparecen en determinados contextos de expresiones regulares. Encontrará más información sobre esta tecnología en una [publicación reciente sobre este tema](https://www.microsoft.com/en-us/research/publication/automated-data-extraction-using-predictive-program-synthesis/). 

