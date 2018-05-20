---
title: Adición de un anclaje con Azure Maps | Microsoft Docs
description: Procedimiento para agregar un anclaje a un mapa de Javascript
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
ms.openlocfilehash: 189b30ff0b5c47d0a4bd3181c8eb2143213ad6cd
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="add-pins-to-the-map"></a>Adición de anclajes al mapa

Este artículo muestra cómo agregar un anclaje a un mapa.  

## <a name="understand-the-code"></a>Comprensión del código

<iframe height='500' scrolling='no' title='Adición de un anclaje a un mapa' src='//codepen.io/azuremaps/embed/ZrVpEa/?height=504&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/ZrVpEa/'>Adición de un anclaje a un mapa</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque de código construye un objeto mapa. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

En el segundo bloque de código, se crea un anclaje y se agrega al mapa. Un anclaje es una [Feature (característica)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/feature?view=azure-iot-typescript-latest) de [Point (punto)](https://docs.microsoft.com/javascript/api/azure-maps-javascript/point?view=azure-iot-typescript-latest) con [PinProperties](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinproperties?view=azure-iot-typescript-latest) como su propiedad Feature. Use `new atlas.data.Feature(new atlas.data.Point())` para crear un anclaje y definir sus propiedades. Una capa de anclajes es una matriz de anclajes. Use la función [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins) de la clase map para agregar una capa de anclajes al mapa y definir las propiedades de la capa de anclajes. Consulte las propiedades de una capa de anclajes en [PinLayerOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/pinlayeroptions?view=azure-iot-typescript-latest). 

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las clases y los métodos utilizados en este artículo: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addPins](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addpins)
