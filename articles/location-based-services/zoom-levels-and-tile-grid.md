---
title: Niveles de zoom y cuadrícula de mosaico en Azure Location Based Services | Microsoft Docs
description: Obtenga información acerca de los niveles de zoom y la cuadrícula de mosaico en Azure Location Based Services
services: location-based-services
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 3/6/2018
ms.topic: article
ms.service: location-based-services
documentationcenter: ''
manager: cpendle
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 3a86bf84ad31933cc5008591a275d4f4aa52c9f1
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="zoom-levels-and-tile-grid"></a>Niveles de zoom y cuadrícula de mosaico
Azure Location Based Services usa el sistema de coordenadas de la proyección de Mercator esférica (EPSG: 3857).

El mundo se divide en mosaicos cuadrados. Render (Raster) tiene 19 niveles de zoom, numerados de 0 a 18. Render (Vector) tiene 21 niveles de zoom, numerados de 0 a 20. En el nivel de zoom 0, todo el mundo cabe en un solo mosaico:

![Mosaico del mundo](./media/zoom-levels-and-tile-grid/world0.png)

El nivel de zoom 1 utiliza 4 mosaicos para representar el mundo: un cuadrado de 2 x 2

![Parte superior izquierda del mosaico del mundo](./media/zoom-levels-and-tile-grid/world1a.png)     ![Parte superior derecha del mosaico del mundo](./media/zoom-levels-and-tile-grid/world1c.png) 

![Parte inferior izquierda del mosaico del mundo](./media/zoom-levels-and-tile-grid/world1b.png)     ![Parte inferior derecha del mosaico del mundo](./media/zoom-levels-and-tile-grid/world1d.png) 


Cada nivel de zoom posterior divide por cuatro los mosaicos de la anterior, creando una cuadrícula de 2<sup>zoom</sup> x 2<sup>zoom</sup>. El nivel de zoom 20 es una cuadrícula 2<sup>20</sup> x 2<sup>20</sup>, o 1 048 576 x 1 048 576 mosaicos de (109 951 162 778 en total).

La tabla completa de valores para los niveles de zoom es la siguiente:

|Nivel de zoom|metros/píxel|metros/lado de mosaico|
|--- |--- |--- |
|0|156543|40075008|
|1|78271,5|20037504|
|2|39135,8|10018764,8|
|3|19567,9|5009382,4|
|4|9783,9|2504678,4|
|5|4892|1252352|
|6|2446|626176|
|7|1223|313088|
|8|611,5|156544|
|9|305,7|78259,2|
|10|152,9|39142,4|
|11|76,4|19558,4|
|12|38,2|9779,2|
|13|19,1|4889,6|
|14|9,6|2457,6|
|15|4.8|1228,8|
|16|2.4|614,4|
|17|1.2|307,2|
|18|0.6|152,8|
|19|0,3|76,4|
|20 |0,15|38,2|

Los mosaicos se denominan según las coordenadas x e y, y el nivel de zoom, correspondientes a la posición del mosaico en la cuadrícula para ese nivel de zoom.

Al determinar qué nivel de zoom se usará, recuerde que cada ubicación está en una posición fija en su mosaico. Esto significa que el número de mosaicos necesarios para mostrar una extensión determinada del territorio depende de la ubicación específica de la cuadrícula de zoom en el mundo. Por ejemplo, si hay dos puntos separados 900 metros, *puede* que al mostrar una ruta entre ellos en el nivel de zoom 17, solo ocupen tres mosaicos. Sin embargo, si el punto occidental está a la derecha de su mosaico y el punto oriental a la izquierda, puede ocupar cuatro:

![Escala de demostración del zoom](./media/zoom-levels-and-tile-grid/zoomdemo_scaled.png) 

Una vez determinado el nivel de zoom, se pueden calcular los valores x e y. El mosaico superior izquierdo de cada cuadrícula de zoom es x=0, y=0; el icono de la esquina inferior derecha está en x=2<sup>zoom -1</sup>, y = 2<sup>zoom-1</sup>.

Esta es la cuadrícula de zoom para el nivel de zoom 1:

![Cuadrícula de zoom para el nivel de zoom 1](./media/zoom-levels-and-tile-grid/api_x_y.png)
