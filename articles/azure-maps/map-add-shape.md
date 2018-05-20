---
title: Adición de una forma con Azure Maps | Microsoft Docs
description: Procedimiento para agregar una forma a un mapa de Javascript
services: azure-maps
keywords: ''
author: jinzh-azureiot
ms.author: jinzh
ms.date: 05/07/2018
ms.topic: article
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: codepen
ms.openlocfilehash: fb9ec0713d3db465cf835346465e70c4455b38ff
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="add-a-shape-to-a-map"></a>Adición de una forma a un mapa

Este artículo muestra cómo agregar una línea, un círculo y un polígono al mapa. 

<a id="addALine"></a>

## <a name="add-a-line"></a>Adición de una línea

<iframe height='500' scrolling='no' title='Adición de una línea a un mapa' src='//codepen.io/azuremaps/embed/qomaKv/?height=534&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/qomaKv/'>Adición de una línea a un mapa</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque de código construye un objeto mapa. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

En el segundo bloque de código se crea una línea. Una línea es una [Feature (Característica)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de LineString con LineStringProperties como su propiedad Feature. Use `new atlas.data.Feature(new atlas.data.LineString())` para crear una línea y definir sus propiedades. 

Una capa de líneas es una matriz de líneas. El último bloque de código usa la función [addLineStrings](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addlinestrings) de la clase Map para agregar la capa de líneas al mapa y definir las propiedades de la capa de líneas. Puede consultar las propiedades de una capa de líneas en [LinestringLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/linestringlayeroptions?view=azure-iot-typescript-latest).

<a id="addACircle"></a>

## <a name="add-a-circle"></a>Adición de un círculo

<iframe height='500' scrolling='no' title='Adición de un círculo a un mapa' src='//codepen.io/azuremaps/embed/PRmzJX/?height=538&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Adición de un círculo a un mapa</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque de código construye un objeto mapa. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

En el segundo bloque de código se crea un círculo. Un círculo es una [Feature (característica)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de [Point (punto)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) con [CircleProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circleproperties?view=azure-iot-typescript-latest) como su propiedad Feature. Use `new atlas.data.Feature(new atlas.data.Point())` para crear un círculo y definir sus propiedades.

Una capa de círculos es una matriz de círculos. El último bloque de código usa la función [addCircle](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addcircles) de la clase Map para agregar la capa de círculos al mapa y definir las propiedades de la capa de círculos. Consulte las propiedades de una capa de círculos en [CircleLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/circlelayeroptions?view=azure-iot-typescript-latest).

<a id="addAPolygon"></a>

## <a name="add-a-polygon"></a>Adición de un polígono
<iframe height='500' scrolling='no' title='Adición de un polígono a un mapa ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=543&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Adición de un polígono a un mapa</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque de código construye un objeto mapa. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

En el segundo bloque de código se crea un polígono. Un polígono es una [Feature (característica)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de [Polygon (polígono)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygon?view=azure-iot-typescript-latest) con [PolygonProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonproperties?view=azure-iot-typescript-latest) como su propiedad Feature. Use `new atlas.data.Feature(new atlas.data.Polygon())` para crear un polígono y definir sus propiedades. Proporcione las coordenadas en orden de la ruta del polígono en el constructor del polígono.

Una capa de polígonos es una matriz de polígonos. El último bloque de código usa la función [addPolygons](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpolygons) de la clase Map para agregar la capa de polígonos al mapa y definir sus propiedades. Consulte las propiedades de una capa de polígonos en [PolygonLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/polygonlayeroptions?view=azure-iot-typescript-latest). 
