---
title: Búsqueda de una ruta con Azure Maps | Microsoft Docs
description: Ruta a un punto de interés mediante Azure Maps
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
ms.openlocfilehash: 73ca61140f05a65ca75cd703ed226773b9a43dfa
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="route-to-a-point-of-interest-using-azure-maps"></a>Ruta a un punto de interés mediante Azure Maps

En este tutorial se muestra cómo usar la cuenta de Azure Maps y el SDK de Route Service para buscar la ruta al punto de interés. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una nueva página web mediante la API de control de mapa
> * Establecer las coordenadas de dirección
> * Consultar en Route Service direcciones a un punto de interés

## <a name="prerequisites"></a>requisitos previos

Antes de continuar, siga los pasos del tutorial anterior [Create your Azure Maps account](./tutorial-search-location.md#createaccount) (Creación de una cuenta con Azure Maps) y [Get the subscription key for your account](./tutorial-search-location.md#getkey) (Obtención de la clave principal para la cuenta). 

<a id="getcoordinates"></a>

## <a name="create-a-new-map"></a>Creación de un nuevo mapa 
En los pasos siguientes se muestra cómo crear una página HTML estática insertada con Map Control API. 

1. En la máquina local, cree un nuevo archivo y asígnele el nombre **MapRoute.html**. 
2. Agregue los siguientes componentes HTML al archivo:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Route</title>
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
    El encabezado HTML inserta las ubicaciones de recursos de los archivos CSS y JavaScript de la biblioteca de Azure Maps. El segmento de *script* del cuerpo del archivo HTML contendrá el código JavaScript insertado para acceder a las API de Azure Maps.

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

   ![Visualización del mapa básico](./media/tutorial-route-location/basic-map.png)

## <a name="set-start-and-end-points"></a>Establecimiento de los puntos de inicio y fin

En este tutorial, establezca como punto de inicio Microsoft y como punto de destino una gasolinera de Seattle. 

1. En el mismo bloque de *script* del archivo **MapRoute.html**, agregue el siguiente código de JavaScript para crear los puntos de inicio y fin de la ruta:

    ```JavaScript
    // Create the GeoJSON objects which represent the start and end point of the route
    var startPoint = new atlas.data.Point([-122.130137, 47.644702]);
    var startPin = new atlas.data.Feature(startPoint, {
        title: "Microsoft",
        icon: "pin-round-blue"
    });

    var destinationPoint = new atlas.data.Point([-122.3352, 47.61397]);
    var destinationPin = new atlas.data.Feature(destinationPoint, {
        title: "Contoso Oil & Gas",
        icon: "pin-blue"
    });
    ```
    Este código crea dos [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar los puntos inicial y final de la ruta. 

2. Agregue el siguiente código JavaScript para agregar los marcadores de los puntos inicial y final del mapa:

    ```JavaScript
    // Fit the map window to the bounding box defined by the start and destination points
    var swLon = Math.min(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var swLat = Math.min(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    var neLon = Math.max(startPoint.coordinates[0], destinationPoint.coordinates[0]);
    var neLat = Math.max(startPoint.coordinates[1], destinationPoint.coordinates[1]);
    map.setCameraBounds({
        bounds: [swLon, swLat, neLon, neLat],
        padding: 50
    });

    // Add pins to the map for the start and end point of the route
    map.addPins([startPin, destinationPin], {
        name: "route-pins",
        textFont: "SegoeUi-Regular",
        textOffset: [0, -20]
    });
    ``` 
    **map.setCameraBounds** ajusta la ventana de mapa de acuerdo con las coordenadas de los puntos de inicio y fin. La API **map.addPins** agrega los puntos al Control de mapa como componentes visuales.

7. Guarde el archivo **MapRoute.html** y actualice el explorador. Ahora el mapa se centra en Seattle, y puede ver como la chincheta azul redonda marca el punto de inicio y otra chincheta azul el punto de fin.

   ![Visualización del mapa con los puntos de inicio y fin marcados](./media/tutorial-route-location/map-pins.png)

<a id="getroute"></a>

## <a name="get-directions"></a>Obtención de las direcciones

En esta sección se muestra cómo usar la API del servicio de ruta de Maps para buscar la ruta desde un punto de inicio hasta un destino. El servicio de ruta proporciona las API para planear la ruta *más rápida*, *más corta*, *ecológica* o *emocionante* entre dos ubicaciones. También permite a los usuarios planear rutas futuras mediante el uso de bases de datos que contienen un historial amplio del tráfico y la predicción de duraciones de una ruta en cualquier día y a cualquier hora. Para más información, consulte [Obtención de direcciones de ruta](https://docs.microsoft.com/rest/api/maps/route/getroutedirections).


1. En primer lugar, agregue una nueva capa en el mapa para mostrar el recorrido de la ruta, o *linestring*. Agregue el siguiente código JavaScript al bloque *script*:

    ```JavaScript
    // Initialize the linestring layer for routes on the map
    var routeLinesLayerName = "routes";
    map.addLinestrings([], {
        name: routeLinesLayerName,
        color: "#2272B9",
        width: 5,
        cap: "round",
        join: "round",
        before: "labels"
    });
    ```

2. Cree un objeto [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agregue un controlador de eventos para analizar la respuesta JSON enviada por el servicio de ruta de Maps. Este código construye una matriz de coordenadas para los segmentos de línea de la ruta y, luego, agrega el conjunto de coordenadas a la capa linestrings del mapa. 

    ```JavaScript
    // Perform a request to the route service and draw the resulting route on the map
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        if (this.readyState == 4 && this.status == 200) {
            var response = JSON.parse(xhttp.responseText);

            var route = response.routes[0];
            var routeCoordinates = [];
            for (var leg of route.legs) {
                var legCoordinates = leg.points.map((point) => [point.longitude, point.latitude]);
                routeCoordinates = routeCoordinates.concat(legCoordinates);
            }

            var routeLinestring = new atlas.data.LineString(routeCoordinates);
            map.addLinestrings([new atlas.data.Feature(routeLinestring)], { name: routeLinesLayerName });
        }
    };
    ```

3. Agregue el código siguiente para compilar la consulta y enviar el objeto XMLHttpRequest al servicio de ruta de Maps:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "api-version=1.0";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```

3. Guarde el archivo **MapRoute.html** y actualice el explorador web. Para conectarse correctamente con las API de Maps, debe ver un mapa similar al siguiente. 

    ![Control de mapa y Route Service de Azure](./media/tutorial-route-location/map-route.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una nueva página web mediante la API de control de mapa
> * Establecer las coordenadas de dirección
> * Consultar en el servicio de ruta las direcciones a un punto de interés

En el siguiente tutorial se muestra cómo crear una consulta de ruta con restricciones, como el modo de desplazamiento o el tipo de carga y, después, visualizar varias rutas en el mismo mapa. 

> [!div class="nextstepaction"]
> [Búsqueda de rutas para diferentes modos de desplazamiento](./tutorial-prioritized-routes.md)
