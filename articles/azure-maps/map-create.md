---
title: Creación de un mapa con Azure Maps | Microsoft Docs
description: Procedimiento para crear un mapa de Javascript
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
ms.openlocfilehash: 9a7c611860a6d32f82d6714d945c62e6c562f91f
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="create-a-map"></a>Creación de un mapa

Este artículo muestra cómo crear un mapa.  

## <a name="understand-the-code"></a>Comprensión del código

Hay dos maneras de crear un mapa. Puede establecer la cámara del mapa especificando el punto central y el nivel de zoom o puede establecer los límites de la cámara del mapa especificando el punto del límite suroeste y el punto del límite nordeste.

<a id="setCameraOptions"></a>

### <a name="setting-the-camera"></a>Configuración de la cámara

<iframe height='310' scrolling='no' title='Creación de un mapa mediante CameraOptions' src='//codepen.io/azuremaps/embed/qxKBMN/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/qxKBMN/'>Creación de un mapa mediante CameraOptions</a> de Azure LBS (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior se crea un [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) mediante `new atlas.Map()`. Las propiedades del mapa, como el centro y el nivel de zoom, forman parte de [CameraOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraoptions?view=azure-iot-typescript-latest). CameraOptions se puede definir en el constructor del mapa o a través de la función [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera) de la clase Map.

<a id="setCameraBoundsOptions"></a>

### <a name="setting-the-camera-bounds"></a>Configuración de los límites de cámara

<iframe height='310' scrolling='no' title='Creación de un mapa mediante CameraBoundsOptions' src='//codepen.io/azuremaps/embed/ZrRbPg/?height=265&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Consulte el Pen <a href='https://codepen.io/azuremaps/pen/ZrRbPg/'>Creación de un mapa mediante CameraBoundsOptions</a> de Azure Maps (<a href='https://codepen.io/azuremaps'>@azuremaps</a>) en <a href='https://codepen.io'>CodePen</a>.
</iframe>

En el código anterior se crea un [objeto Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest) mediante `new atlas.Map()`. Las propiedades del mapa, como el cuadro de límite, forman parte de [CameraBoundsOptions](https://docs.microsoft.com/javascript/api/azure-maps-javascript/cameraboundsoptions?view=azure-iot-typescript-latest). Se puede definir CameraBoundsOptions a través de la función [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds) de la clase Map.

## <a name="try-out-the-code"></a>Prueba del código 

Eche un vistazo al código de ejemplo anterior. Puede modificar el código JavaScript en la pestaña JS a la izquierda y ver los cambios de la vista de mapa en la pestaña de resultados de la derecha. También puede hacer clic en el botón "Editar en CodePen" y modificar el código en CodePen. 

<a id="relatedReference"></a>

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las clases y los métodos utilizados en este artículo: 
* [Map](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest)
    * [setCamera](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamera)
    * [setCameraBounds](https://docs.microsoft.com/javascript/api/azure-maps-javascript/map?view=azure-iot-typescript-latest#setcamerabounds)

