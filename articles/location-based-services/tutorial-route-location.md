---
title: "Búsqueda de una ruta con Azure Location Based Services | Microsoft Docs"
description: "Ruta a un punto de interés mediante Azure Location Based Services"
services: location-based-services
keywords: 
author: dsk-2015
ms.author: dkshir
ms.date: 11/28/2017
ms.topic: tutorial
ms.service: location-based-services
documentationcenter: 
manager: timlt
ms.devlang: na
ms.custom: mvc
ms.openlocfilehash: 7303347444952d9c09dc6c04eea5b962e18729b4
ms.sourcegitcommit: 9890483687a2b28860ec179f5fd0a292cdf11d22
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/24/2018
---
# <a name="route-to-a-point-of-interest-using-azure-location-based-services"></a>Ruta a un punto de interés mediante Azure Location Based Services

En este tutorial se muestra cómo usar la cuenta de Azure Location Based Services y el SDK de Route Service para buscar la ruta al punto de interés. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Obtención de las coordenadas de dirección
> * Consultar en Route Service direcciones a un punto de interés

## <a name="prerequisites"></a>requisitos previos

Antes de continuar, asegúrese de [crear su cuenta de Azure Location Based Services](./tutorial-search-location.md#createaccount) y de [obtener la clave de suscripción de la cuenta](./tutorial-search-location.md#getkey). También puede observar cómo usar las API de Control de mapa y Search Service en el tutorial [Búsqueda de un punto de interés cercano mediante Azure Location Based Services](./tutorial-search-location.md).


<a id="getcoordinates"></a>

## <a name="get-address-coordinates"></a>Obtención de las coordenadas de dirección

Use los pasos siguientes para crear una página HTML estática insertada con la API de Control de mapa de Location Based Services. 

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
    Observe cómo el encabezado HTML inserta las ubicaciones de recursos de los archivos CSS y JavaScript de la biblioteca de Azure Location Based Services. Observe también el segmento *script* en el cuerpo del archivo HTML, que contendrá el código JavaScript insertado para acceder a las API de Azure Location Based Service.

3. Agregue el siguiente código JavaScript al bloque *script* del archivo HTML. Use la clave principal de la cuenta de Location Based Services en el script.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    **atlas.Map** proporciona el control para un mapa web visual e interactivo, y es un componente de la API de Control de mapa de Azure.

4. Agregue el siguiente código JavaScript al bloque *script*. De esta forma se agrega una capa de *linestrings* al Control de mapa para mostrar la ruta:

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

5. Agregue el siguiente código JavaScript para crear puntos iniciales y finales para la ruta:

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
    Este código crea dos [objetos GeoJSON](https://en.wikipedia.org/wiki/GeoJSON) para representar los puntos inicial y final de la ruta. El punto final es la combinación de latitud y longitud de una de las *gasolineras* buscadas en el tutorial anterior [Búsqueda de un punto de interés cercano mediante Azure Location Based Services](./tutorial-search-location.md).

6. Agregue el siguiente código JavaScript para agregar los marcadores de los puntos inicial y final del mapa:

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
    La API **map.setCameraBounds** ajusta la ventana del mapa de acuerdo con las coordenadas de los puntos inicial y final. La API **map.addPins** agrega los puntos al Control de mapa como componentes visuales.

7. Guarde el archivo **MapRoute.html** en la máquina. 

<a id="getroute"></a>

## <a name="query-route-service-for-directions-to-point-of-interest"></a>Consulta en Route Service direcciones a un punto de interés

En esta sección se muestra cómo usar la API de Route Service de Azure Location Based Services para buscar la ruta desde un punto inicial dado hasta un destino. Route Service proporciona API para planear la ruta más rápida, más corta o más económica entre dos ubicaciones, teniendo en cuenta las condiciones del tráfico en tiempo real. También permite a los usuarios planear rutas futuras mediante el uso de bases de datos que contienen un historial amplio del tráfico y la predicción de duraciones de una ruta en cualquier día y a cualquier hora. 

1. Abra el archivo **MapRoute.html** creado en la sección anterior y agregue el siguiente código JavaScript al bloque *script* para ilustrar el servicio Route Service.

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
    Este fragmento de código crea un elemento [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agrega un controlador de eventos para analizar la respuesta de entrada. Para obtener una respuesta correcta, crea una matriz de coordenadas de segmentos de línea de la primera ruta devuelta. A continuación, agrega este conjunto de coordenadas de esta ruta a la capa *linestrings* del mapa.

2. Agregue el código siguiente al bloque *script* para enviar el objeto XMLHttpRequest a Route Service de Azure Location Based Services:

    ```JavaScript
    var url = "https://atlas.microsoft.com/route/directions/json?";
    url += "&api-version=1.0";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&query=" + startPoint.coordinates[1] + "," + startPoint.coordinates[0] + ":" +
        destinationPoint.coordinates[1] + "," + destinationPoint.coordinates[0];

    xhttp.open("GET", url, true);
    xhttp.send();
    ```
    La solicitud anterior muestra los parámetros necesarios, que son la clave de la cuenta y las coordenadas de los puntos inicial y final, en el orden especificado. 

3. Guarde el archivo **MapRoute.html** localmente, ábralo en un explorador web de su elección y observe el resultado. Para que la conexión con las API de Location Based Services se realice correctamente, debe ver un mapa similar al siguiente. 

    ![Control de mapa y Route Service de Azure](./media/tutorial-route-location/lbs-map-route.png)


## <a name="next-steps"></a>pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Obtención de las coordenadas de dirección
> * Consultar en Route Service direcciones a un punto de interés

Continúe con el tutorial [Búsqueda de rutas para diferentes modos de desplazamiento mediante Azure Location Based Services](./tutorial-prioritized-routes.md) para aprender a usar Azure Location Based Services para establecer prioridades sobre las rutas a un punto de interés, en función del modo de transporte. 
