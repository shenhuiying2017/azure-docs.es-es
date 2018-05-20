---
title: Adición de HTML personalizado en Azure Maps | Microsoft Docs
description: Procedimiento para agregar HTML personalizado a un mapa de Javascript
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
ms.openlocfilehash: df50774e1bdca53034d4856f38a6133fe8e89855
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="add-custom-html-to-the-map"></a>Adición de HTML personalizado al mapa

Este artículo muestra cómo agregar código HTML personalizado, como un archivo de imagen, al mapa. 

## <a name="understand-the-code"></a>Comprensión del código

<iframe height='466' scrolling='no' title='Adición de HTML personalizado a un mapa: png' src='//codepen.io/azuremaps/embed/MVoeVw/?height=466&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/MVoeVw/'>Adición de HTML personalizado a un mapa: png</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior, el primer bloque de código construye un objeto mapa. Puede consultar [Creación de un mapa](./map-create.md) para obtener instrucciones.

El segundo bloque de código crea un elemento HTML a partir de una imagen.

El último bloque de código usa la función [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml) de la clase Map para agregar la imagen en la posición especificada del mapa.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las clases y los métodos utilizados en este artículo: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [addHtml](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#addhtml)
