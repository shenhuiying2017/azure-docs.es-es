---
title: "Inspectores admitidos disponibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los inspectores disponibles para la preparación de datos para Azure Machine Learning"
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: garyericson, jasonwhowell, mldocs
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: ff5fcbc6df8cb07e0b98b877f20d981d6bef5117
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="supported-inspectors-for-the-azure-machine-learning-data-preparation-preview"></a>Inspectores admitidos disponibles para la versión preliminar de la preparación de datos de Azure Machine Learning
En este documento se describe el conjunto de inspectores disponibles en esta versión preliminar.

## <a name="the-halo-effect"></a>El efecto de halo 
Algunos inspectores admiten el efecto de halo. Dicho efecto usa dos colores diferentes para mostrar inmediatamente el cambio de manera visual en una transformación. El color gris representa el valor más reciente antes de la transformación, mientras que el color azul muestra el valor actual. Este efecto puede habilitarse o deshabilitarse en las opciones.

## <a name="graphical-filtering"></a>Filtrado gráfico 
Algunos de los inspectores admiten el filtrado de datos mediante el uso del inspector como si se tratara de un editor. Para ello, se seleccionan los elementos gráficos y, después, se usa la barra de herramientas de la parte superior derecha de la ventana del inspector para filtrar los valores seleccionados. 

## <a name="column-statistics"></a>Estadísticas de columna
Para las columnas numéricas, este inspector proporciona diversas estadísticas sobre la columna. Las estadísticas incluyen las siguientes medidas: 
- Mínima
- Cuartil inferior
- Media
- Cuartil superior
- Máxima
- Media
- Desviación estándar


### <a name="options"></a>Opciones 
- None

## <a name="histogram"></a>Histograma 
Calcula y muestra un histograma de una sola columna numérica. El número predeterminado de cubos se calcula utilizando la regla de Scott. De todas formas, se puede invalidar la regla a través de las opciones.

Este inspector admite el efecto de halo.


### <a name="options"></a>Opciones
- Número mínimo de cubos (se aplica incluso cuando está seleccionada la creación de cubos predeterminada)
- Número predeterminado de cubos (regla de Scott) 
- Mostrar halo
- Superposición de trazado de densidad de kernel (kernel gaussiano) 


### <a name="actions"></a>Acciones
Este inspector admite el filtrado a través de cubos, que pueden incluir uno o una selección de varios cubos. Aplique los filtros como se ha descrito anteriormente.

## <a name="value-counts"></a>Recuentos de valor
Este inspector presenta una tabla de frecuencias de valores para la columna seleccionada en la actualidad. La visualización predeterminada es para los seis valores superiores. De todas formas puede cambiar el límite a cualquier número. También puede establecer la visualización a contar desde la parte inferior en lugar de la parte superior. Este inspector admite el efecto de halo.

### <a name="options"></a>Opciones 
- Número de valores superiores
- Descendente
- Incluir valores null o de error
- Mostrar halo


### <a name="actions"></a>Acciones 
Este inspector admite el filtrado a través de barras, que pueden incluir uno o una selección de varias barras. Aplique los filtros como se ha descrito anteriormente.

## <a name="box-plot"></a>Diagrama de caja 
Gráfico de cajas y bigotes de una columna numérica.

### <a name="options"></a>Opciones 
- Agrupar por columna

## <a name="scatter-plot"></a>gráfico de dispersión
Un gráfico de dispersión para dos columnas numéricas. La muestra de datos es reducida por motivos de rendimiento. El tamaño de muestra se puede reemplazar en las opciones.

### <a name="options"></a>Opciones  
- Columna del eje X
- Columna del eje Y
- Tamaño de muestra
- Agrupar por columna


## <a name="time-series"></a>Serie temporal
Gráfico de líneas con la característica de reconocimiento de tiempo en el eje X.

### <a name="options"></a>Opciones
- Columna de fecha
- Columna numérica
- Tamaño de muestra


### <a name="actions"></a>Acciones
Este inspector admite el filtrado mediante el método de selección de hacer clic y arrastrar para seleccionar un intervalo en el gráfico. Después de completar la selección, aplique los filtros como se ha descrito anteriormente.


## <a name="map"></a>Map 
Un mapa con puntos trazados, suponiendo que se han especificado la latitud y la longitud. La latitud debe seleccionarse primero.

### <a name="options"></a>Opciones
- Columna de latitud
- Columna de longitud
- Agrupación en clústeres activada
- Agrupar por columna


### <a name="actions"></a>Acciones
Este inspector admite el filtrado mediante la selección de puntos en el mapa. Presione la tecla **Ctrl** y, después, haga clic y arrastre con el mouse para formar un cuadrado alrededor de los puntos. Después, aplique los filtros como se ha descrito anteriormente.

Puede cambiar el tamaño del mapa rápidamente para mostrar solamente todos los puntos posibles. Para ello, presione la **E** situada en el lado izquierdo del mapa.


## <a name="pattern-frequency"></a>Frecuencia de patrón 

Este inspector muestra una lista de patrones en la columna Cadena seleccionada. Los patrones se representan mediante una sintaxis similar a las expresiones regulares. Al mantener el mouse sobre el modelo, se muestran los ejemplos de los valores representados por ese patrón. Junto con los patrones, también se muestran las coberturas aproximadas en forma de porcentaje.

![Imagen del inspector de patrones](media/data-prep-appendix4-supported-inspectors/PatternInspectorProductNumber.png)

### <a name="options"></a>Opciones
- Número de valores superiores
- Descendente
- Mostrar halo

### <a name="actions"></a>Acciones
Este inspector admite el filtrado basado en los patrones mostrados. Presione la tecla **Ctrl** y, a continuación, seleccione las barras rellenas en el inspector de patrones. Después, aplique los filtros como se ha descrito anteriormente. Como resultado de la acción del usuario, se agrega un paso Filtro avanzado. Puede ver y modificar el código Python generado invocando la opción de edición del paso Filtro avanzado.
