---
title: "Inspectores admitidos disponibles con la preparación de datos de Azure Machine Learning | Microsoft Docs"
description: "En este documento se proporciona una lista completa de los inspectores disponibles para la preparación de datos de Azure ML."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/11/2017
ms.openlocfilehash: bc14c051fb0f518b1cff2236a61d24cb052700f0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="supported-inspectors-for-this-preview"></a>Inspectores admitidos para esta versión preliminar
En este documento se describe el conjunto de inspectores disponibles en esta versión preliminar.

## <a name="the-halo-effect"></a>El efecto de halo 
Algunos inspectores admiten el efecto de Halo. Dicho efecto usa dos colores diferentes para mostrar inmediatamente el cambio de manera visual en una transformación. El color gris representa el valor antes de la transformación más reciente, mientras que el color azul muestra el valor actual. Este efecto puede habilitarse o deshabilitarse en las opciones.

## <a name="graphical-filtering"></a>Filtrado gráfico 
Algunos de los inspectores admiten el filtrado de datos mediante el uso del inspector como si se tratara de un editor. Para ello, se seleccionan los elementos gráficos y, después, se usa la barra de herramientas de la parte superior derecha de la ventana del inspector para filtrar los valores seleccionados. 

## <a name="column-statistics"></a>Estadísticas de columna
Para las columnas numéricas, este inspector proporciona diversas estadísticas sobre la columna. Las estadísticas incluyen:
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
Calcula y muestra un histograma de una sola columna numérica. El número predeterminado de depósitos se calcula mediante la regla de Scott. Dicha regla se puede invalidar a través de las opciones.
Este Inspector es compatible con el efecto de halo.


### <a name="options"></a>Opciones
- Número mínimo de depósitos (se aplica incluso cuando está seleccionada la creación de depósitos predeterminada)
- Número predeterminado de depósitos (regla de Scott) 
- Mostrar halo
- Superposición de trazado de densidad de kernel (kernel gaussiano) 


### <a name="actions"></a>Acciones
Este inspector admite el filtrado en los depósitos, ya sean de selección única o múltiple, y aplica los filtros como se ha descrito anteriormente.

## <a name="value-counts"></a>Recuentos de valor
Este inspector presenta una tabla de frecuencias de valores para la columna seleccionada. La visualización predeterminada muestra los 6 valores superiores. El límite puede cambiarse a cualquier número o contar de abajo arriba. Este Inspector es compatible con el efecto de halo.

### <a name="options"></a>Opciones 
- Número de valores superiores
- Descendente
- Incluir valores null o de error
- Mostrar halo


### <a name="actions"></a>Acciones 
Este inspector admite el filtrado en las barras, ya sean de selección única o múltiple, y aplica los filtros como se ha descrito anteriormente.

## <a name="box-plot"></a>Diagrama de caja 
Diagrama de cajas y bigotes de una columna numérica.

### <a name="options"></a>Opciones 
- Agrupar por columna

## <a name="scatter-plot"></a>Gráfico de dispersión
Gráfico de dispersión para dos columnas numéricas. Los datos se reducen por motivos de rendimiento. El tamaño de muestra se puede invalidar mediante las opciones.

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
Mapa con puntos trazados, suponiendo que se han especificado la latitud y la longitud. La latitud debe seleccionarse primero.

### <a name="options"></a>Opciones
- Columna de latitud
- Columna de longitud
- Agrupación en clústeres activada
- Agrupar por columna


### <a name="actions"></a>Acciones
Este inspector admite el filtrado mediante la selección de puntos en el mapa. Presione la tecla Control y, después, haga clic y arrastre con el mouse para formar un cuadrado alrededor de los puntos. Después, aplique los filtros como se ha descrito anteriormente.
Es posible cambiar el tamaño del mapa rápidamente para mostrar todos los puntos posibles y ninguno más. Para ello, presione la **E** situada en el lado izquierdo del mapa.
