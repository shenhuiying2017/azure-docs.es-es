---
title: Varias rutas con Azure Location Based Services | Microsoft Docs
description: "Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Location Based Services"
services: location-based-services
keywords: 
author: kgremban
ms.author: kgremban
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 986e8667ca421baa78647e77e43fef92d81a7982
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-location-based-services"></a>Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Location Based Services

En este tutorial se muestra cómo usar la cuenta de Azure Location Based Services y el SDK de Route Service para buscar la ruta al punto de interés, con una prioridad establecida según el modo de desplazamiento. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configuración de la consulta de Route Service
> * Representar las rutas con una prioridad establecida según el modo de desplazamiento

## <a name="prerequisites"></a>requisitos previos

Antes de continuar, asegúrese de [crear su cuenta de Azure Location Based Services](./tutorial-search-location.md#createaccount) y de [obtener la clave de la cuenta](./tutorial-search-location.md#getkey). También puede observar cómo se usan las API de Control de mapa y Search Service en el tutorial [Search nearby point of interest using Azure Location Based Services](./tutorial-search-location.md) (Búsqueda de puntos de interés cercanos mediante Azure Location Based Services), y conocer el uso básico de las API de Route Service en el tutorial [Route to a point of interest using Azure Location Based Services](./tutorial-route-location.md) (Ruta a un punto de interés mediante Azure Location Based Services).


<a id="queryroutes"></a>

## <a name="configure-your-route-service-query"></a>Configuración de la consulta de Route Service

Use los pasos siguientes para crear una página HTML estática insertada con la API de Control de mapa de Location Based Services. 

1. En la máquina local, cree un nuevo archivo y asígnele el nombre **MapTruckRoute.html**. 
2. Agregue los siguientes componentes HTML al archivo:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Truck Route</title>
        <link rel="stylesheet" href="https://atlas.microsoft.com/sdk/css/atlas.min.css?api-version=1.0" type="text/css" />
        <script src="https://atlas.microsoft.com/sdk/js/atlas.min.js?api-version=1.0"></script>
        <style>
            html,
            body {
                width: 100%;
                height: 100%;
                padding: 0;
                margin: 0;
            }

            #map {
                width: 100%;
                height: 100%;
            }
        </style>
    </head>
    
    <body>
        <div id="map"></div>
        <script>
            // Embed Map Control JavaScript code here
        </script>
    </body>

    </html>
    ```
    Observe que el encabezado HTML inserta las ubicaciones de recursos de los archivos CSS y JavaScript de la biblioteca de Azure Location Based Services. Observe también que se ha agregado el segmento *script* al cuerpo del archivo HTML para que contenga el código JavaScript insertado para acceder a la API de Control de mapa de Azure.
3. Agregue el siguiente código JavaScript al bloque *script* del archivo HTML. Reemplace el marcador de posición *<insert-key>* por la clave principal de la cuenta de Location Based Services.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** proporciona el control para un mapa web visual e interactivo, y es un componente de la API de Control de mapa de Azure.

4. Agregue el siguiente código JavaScript al bloque *script* para agregar al mapa la visualización de la circulación del tráfico:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Este código establece la circulación del tráfico en `relative`, que es la velocidad de la carretera relativa a la libre circulación. También podría establecerlo en velocidad `absolute` de la carretera o `relative-delay`, que muestra la velocidad relativa en el punto en que varía con respecto a la libre circulación. 

5. Agregue el siguiente código JavaScript para crear los marcadores de los puntos inicial y final de la ruta:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.356099, 47.580045]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Fabrikam, Inc.",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Microsoft",
        icon: "pin-blue"
    });
    ```
    Este código crea dos [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar los puntos inicial y final de la ruta. 

6. Agregue el siguiente código JavaScript para agregar capas de *linestrings* al Control de mapa a fin de mostrar las rutas según el modo de transporte, por ejemplo, _coche_ y _camión_.

    ```JavaScript
    // Place route layers on the map
    var carRouteLayerName = "car-route";
    map.addLinestrings([], {
        name: carRouteLayerName,
        color: "#B76DAB",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });

    var truckRouteLayerName = "truck-route";
    map.addLinestrings([], {
        name: truckRouteLayerName,
        color: "#2272B9",
        width: 9,
        cap: "round",
        join: "round",
        before: carRouteLayerName
    });
    ```

7. Agregue el siguiente código JavaScript para agregar los puntos inicial y final al mapa:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 100
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    La API **map.setCameraBounds** ajusta la ventana del mapa de acuerdo con las coordenadas de los puntos inicial y final. La API **map.addPins** agrega los puntos al Control de mapa como componentes visuales.

8. Guarde el archivo **MapTruckRoute.html** en su máquina. 

<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Representación de las rutas con una prioridad establecida según el modo de desplazamiento

En esta sección se muestra cómo usar la API de Route Service de Azure Location Based Services para buscar varias rutas desde un punto inicial dado hasta un destino, según el modo de transporte. Route Service proporciona API para planear la ruta más rápida, más corta o más económica entre dos ubicaciones, teniendo en cuenta las condiciones del tráfico en tiempo real. También permite a los usuarios planear rutas futuras mediante el uso de bases de datos que contienen un historial amplio del tráfico y la predicción de duraciones de una ruta en cualquier día y a cualquier hora. 

1. Abra el archivo **MapTruckRoute.html** creado en la sección anterior y agregue el siguiente código JavaScript al bloque *script* para obtener la ruta correspondiente a un camión mediante Route Service.

    ```JavaScript
    // Perform a request to the route service and draw the resulting truck route on the map
    var xhttpTruck = new XMLHttpRequest();
    xhttpTruck.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: truckRouteLayerName
            });
        }
    };

    var truckRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    truckRouteUrl += "&api-version=1.0";
    truckRouteUrl += "&subscription-key=" + LBSAccountKey;
    truckRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];
    truckRouteUrl += "&travelMode=truck";
    truckRouteUrl += "&vehicleWidth=2";
    truckRouteUrl += "&vehicleHeight=2";
    truckRouteUrl += "&vehicleLength=5";
    truckRouteUrl += "&vehicleLoadType=USHazmatClass2";

    xhttpTruck.open("GET", truckRouteUrl, true);
    xhttpTruck.send();
    ```
    Este fragmento de código crea un elemento [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agrega un controlador de eventos para analizar la respuesta de entrada. Para obtener una respuesta correcta, crea una matriz de coordenadas para la ruta devuelta y agrega la capa `truckRouteLayerName` del mapa. 
    
    Este fragmento de código también envía la consulta a Route Service para obtener la ruta con los puntos inicial y final especificados, que corresponde a la clave de la cuenta. Los siguientes parámetros opcionales se usan para indicar la ruta de un camión pesado:
   - El parámetro `travelMode=truck` especifica el modo de desplazamiento, como *camión*. Otros modos de desplazamiento admitidos son *taxi*, *autobús*, *furgoneta*, *moto* y *coche*, que es el predeterminado.
   - Los parámetros `vehicleWidth`, `vehicleHeight` y `vehicleLength` especifican las dimensiones del vehículo, en metros, y solo se tienen en cuenta si el modo de desplazamiento es *camión*.
   - El parámetro `vehicleLoadType` clasifica la carga como peligrosa y restringida en algunas carreteras. Actualmente, este parámetro solo se tiene en cuenta para el modo de desplazamiento *camión*.

2. Agregue el siguiente código JavaScript para obtener la ruta en coche mediante Route Service:

    ```JavaScript
    // Perform a request to the route service and draw the resulting car route on the map
    var xhttpCar = new XMLHttpRequest();
    xhttpCar.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(this.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], {
                name: carRouteLayerName
            });
        }
    };

    var carRouteUrl = "https://atlas.microsoft.com/route/directions/json?";
    carRouteUrl += "&api-version=1.0";
    carRouteUrl += "&subscription-key=" + LBSAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Este fragmento de código crea otro elemento [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agrega un controlador de eventos para analizar la respuesta de entrada. Para obtener una respuesta correcta, crea una matriz de coordenadas para la ruta devuelta y agrega la capa `carRouteLayerName` del mapa. 
    
    Este fragmento de código también envía la consulta a Route Service para obtener la ruta con los puntos inicial y final especificados, que corresponde a la clave de la cuenta. Dado que no se usa ningún otro parámetro, se devuelve la ruta con el modo predeterminado de desplazamiento de *coche*. 

3. Guarde el archivo **MapTruckRoute.html** localmente, ábralo en un explorador web de su elección y observe el resultado. Para que la conexión con las API de Location Based Services se realice correctamente, debe ver un mapa similar al siguiente. 

    ![Rutas con prioridad establecida con Azure Route Service](./media/tutorial-prioritized-routes/lbs-prioritized-routes.png)

    Observe que la ruta del camión está en color azul, mientras que la del coche está en violeta.

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Configuración de la consulta de Route Service
> * Representar las rutas con una prioridad establecida según el modo de desplazamiento

Prosiga con los artículos de **conceptos** y **procedimientos** para conocer en profundidad el SDK de Azure Location Based Services. 
