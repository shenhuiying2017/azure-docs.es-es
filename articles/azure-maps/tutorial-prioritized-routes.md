---
title: Rutas múltiples con Azure Maps | Microsoft Docs
description: Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Maps
services: azure-maps
keywords: ''
author: kgremban
ms.author: kgremban
ms.date: 05/07/2018
ms.topic: tutorial
ms.service: azure-maps
documentationcenter: ''
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: b45394cedcb7bbcb46b9730b7e445b04c8cf4f18
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="find-routes-for-different-modes-of-travel-using-azure-maps"></a>Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Maps

En este tutorial se muestra cómo usar la cuenta de Azure Maps y servicio de rutas para buscar la ruta al punto de interés, con una prioridad establecida según el modo de desplazamiento. Se muestran dos rutas distintas en el mapa, una para automóviles y otra para camiones que podrían tener restricciones de ruta debido a la altura, el peso o una carga peligrosa. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una nueva página web con Map Control API
> * Visualizar el flujo de tráfico en el mapa
> * Crear consultas de ruta que declaran el modo de desplazamiento
> * Mostrar varias rutas en el mapa

## <a name="prerequisites"></a>requisitos previos

Antes de continuar, siga los pasos del primer tutorial para la [Creación de una cuenta de Azure Maps](./tutorial-search-location.md#createaccount) y la [Obtención de la clave de suscripción de la cuenta](./tutorial-search-location.md#getkey). 


## <a name="create-a-new-map"></a>Creación de un nuevo mapa 
En los pasos siguientes se muestra cómo crear una página HTML estática insertada con Map Control API. 

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
    El encabezado HTML inserta las ubicaciones de recursos de los archivos CSS y JavaScript de la biblioteca de Azure Maps. El segmento del *script* del cuerpo del archivo HTML contendrá el código JavaScript insertado para el mapa.
3. Agregue el siguiente código JavaScript al bloque *script* del archivo HTML. Reemplace la cadena **\<su clave de cuenta\>** por la clave principal que copió de la cuenta de Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    **atlas.Map** proporciona el control para un mapa web visual e interactivo, y es un componente de la API de Control de mapa de Azure.

4. Guarde el archivo y ábralo en el explorador. En este punto, tiene un mapa básico que puede desarrollar aún más. 

   ![Visualización del mapa básico](./media/tutorial-prioritized-routes/basic-map.png)

## <a name="visualize-traffic-flow"></a>Visualizar el flujo de tráfico

1. Si no indica al mapa dónde debe centrarse, verá la vista del mundo entero. Para poder ver los datos de tráfico, establecer un punto central y un nivel de zoom en el mapa. Reemplace el código que declara un `new atlas.Map` por el código JavaScript siguiente: 
    
    ```JavaScript
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey,
        center: [-118.2437,34.0522],
        zoom: 12
    });
    ```

    Este código establece el punto central del mapa y declara un nivel de zoom para que pueda centrarse en un área determinada de forma predeterminada. 

1. Agregue la visualización del flujo de tráfico al mapa:

    ```JavaScript
    // Add Traffic Flow to the Map
    map.setTraffic({
        flow: "relative"
    });
    ```
    Este código establece la circulación del tráfico en `relative`, que es la velocidad de la carretera relativa a la libre circulación. También podría establecerlo en velocidad `absolute` de la carretera o `relative-delay`, que muestra la velocidad relativa en el punto en que varía con respecto a la libre circulación. 

2. Guarde el archivo **MapTruckRoute.html** y actualice la página en el explorador. Debería ver las calles de Los Ángeles con sus datos de tráfico actuales.

   ![Visualización del mapa del tráfico](./media/tutorial-prioritized-routes/traffic-map.png)

<a id="queryroutes"></a>

## <a name="set-start-and-end-points"></a>Establecimiento de los puntos de inicio y fin

Para este tutorial, establezca el punto inicial como una compañía ficticia en Seattle llamada Fabrikam y el punto de destino como una oficina de Microsoft. 

1. Agregue el siguiente código JavaScript para crear los marcadores de los puntos inicial y final de la ruta:

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

2. Agregue el siguiente código JavaScript para agregar los puntos inicial y final al mapa:

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
    La llamada a **map.setCameraBounds** ajusta la ventana del mapa de acuerdo con las coordenadas de los puntos de inicio y fin. La API **map.addPins** agrega los puntos al Control de mapa como componentes visuales.

3. Guarde el archivo y actualice el explorador para ver los anclajes en el mapa. Incluso si ha declarado el mapa con un punto central en Los Ángeles, **map.setCameraBounds** mueve la vista para mostrar los puntos inicial y final. 

   ![Visualización del mapa con los puntos de inicio y fin](./media/tutorial-prioritized-routes/pins-map.png)


<a id="multipleroutes"></a>

## <a name="render-routes-prioritized-by-mode-of-travel"></a>Representar las rutas con una prioridad establecida según el modo de desplazamiento

En esta sección se muestra cómo usar la API del servicio de rutas de Maps para buscar varias rutas desde un punto inicial dado hasta un destino, según el modo de transporte. El servicio de rutas proporciona varias API para planear la ruta *más rápida*, *más corta*, *más económica* o *más emocionante* entre dos ubicaciones, teniendo en cuenta las condiciones del tráfico en tiempo real. También permite a los usuarios planear rutas futuras mediante el uso de bases de datos que contienen un historial amplio del tráfico y la predicción de duraciones de una ruta en cualquier día y a cualquier hora. Para más información, consulte [Obtención de direcciones de ruta](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. En primer lugar, agregue una nueva capa en el mapa para mostrar el recorrido de la ruta, o *linestring*. En este tutorial existen dos rutas diferentes, **ruta para automóvil** y **ruta para camión**, cada una con su propia aplicación de estilos. Agregue el siguiente código JavaScript al bloque *script*:

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

2. Agregue el siguiente código JavaScript al bloque *script* para solicitar la ruta para camión y mostrar los resultados en el mapa:

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
    truckRouteUrl += "&subscription-key=" + MapsAccountKey;
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
    
    Este fragmento de código también crea la consulta para el servicio de rutas de Maps mediante la clave de cuenta. La consulta incluye las coordenadas del punto inicial, las coordenadas del punto final y parámetros opcionales para indicar que la ruta es para un camión pesado.

2. Agregue el siguiente código JavaScript para solicitar la ruta para automóvil y mostrar los resultados:

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
    carRouteUrl += "&subscription-key=" + MapsAccountKey;
    carRouteUrl += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttpCar.open("GET", carRouteUrl, true);
    xhttpCar.send();
    ```
    Este fragmento de código crea otro elemento [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agrega un controlador de eventos para analizar la respuesta de entrada. Para una respuesta correcta, crea una matriz de coordenadas para la ruta devuelta y la agrega a la capa `carRouteLayerName` del mapa. 
    
    Este fragmento de código también crea la consulta para el servicio de rutas de Maps mediante la clave de cuenta. La consulta incluye las coordenadas del punto inicial y las coordenadas del punto final. Puesto que no se proporciona ningún parámetro adicional, el servicio de rutas usa como valor predeterminado *automóvil* para el modo de desplazamiento. 

3. Guarde el archivo **MapTruckRoute.html** y actualice el explorador para observar el resultado. Para una conexión correcta con las API de Maps, debe ver un mapa similar al siguiente. 

    ![Rutas con prioridad establecida con Azure Route Service](./media/tutorial-prioritized-routes/prioritized-routes.png)

    La ruta para camión es azul y más gruesa, mientras que la ruta para automóvil es púrpura y más estrecha. La ruta para automóvil transcurre a través de Lake Washington por la I-90, que pasa por túneles en áreas residenciales y por lo que se restringe la carga de residuos peligrosos. La ruta para camión, que especifica un tipo de carga USHazmatClass2, se dirige correctamente para usar una autopista diferente. 

## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una nueva página web con Map Control API
> * Visualizar el flujo de tráfico en el mapa
> * Crear consultas de ruta que declaran el modo de desplazamiento
> * Mostrar varias rutas en el mapa

Para más información sobre la cobertura y funcionalidades de Azure Maps, consulte [Niveles de zoom y cuadrícula de mosaico](zoom-levels-and-tile-grid.md) y los demás artículos de conceptos. 

Para más ejemplos de código y una experiencia de programación interactiva, consulte [Cómo usar el control de mapa](how-to-use-map-control.md) y otras guías de procedimientos. 
