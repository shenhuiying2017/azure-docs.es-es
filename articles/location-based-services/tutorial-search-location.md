---
title: "Búsqueda con Azure Location Based Services | Microsoft Docs"
description: "Búsqueda de un punto de interés cercano mediante Azure Location Based Services"
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
ms.openlocfilehash: f03cdd5491868b78de0514bb66184235dd7df5c2
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/08/2018
---
# <a name="search-nearby-points-of-interest-using-azure-location-based-services"></a>Búsqueda de puntos de interés cercanos mediante Azure Location Based Services

En este tutorial se muestra cómo configurar una cuenta con Azure Location Based Services y luego usar las API proporcionadas para buscar un punto de interés. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una cuenta con Azure Location Based Services
> * Conocer la clave principal de una cuenta de Azure Location Based Services
> * Creación de una página web con la API de Control de mapa
> * Uso de Search Service para encontrar un punto de interés cercano

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-location-based-services"></a>Creación de una cuenta con Azure Location Based Services

Siga estos pasos para crear una nueva cuenta de Location Based Services.

1. En la esquina superior izquierda de [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**.
2. En el cuadro *Buscar en Marketplace*, escriba **location based services**.
3. En *Resultados*, haga clic en **Location Based Services (versión preliminar)**. Haga clic en el botón **Crear** que aparece debajo del mapa. 
4. En la página **Crear una cuenta de Location Based Services Account**, escriba los siguientes valores:
    - El *nombre* de la nueva cuenta. 
    - La *suscripción* que quiere usar para esta cuenta.
    - El nombre del *grupo de recursos* para esta cuenta. Puede elegir *Crear nuevo* o *Usar existente* para el grupo de recursos.
    - Seleccione *Ubicación del grupo de recursos*.
    - Lea los *Términos de la versión preliminar* y marque la casilla para aceptarlos. 
    - Finalmente, haga clic en el botón **Crear**.
   
    ![Creación de una cuenta de Location Based Services en el portal](./media/tutorial-search-location/create-lbs-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obtención de la clave principal de una cuenta

Cuando haya creado correctamente la cuenta de Location Based Services, siga los pasos para vincularla a sus API de búsqueda de mapas:

1. Abra su cuenta de Location Based Services en el portal.
2. Vaya a la opción **CONFIGURACIÓN** de la cuenta y seleccione **Claves**.
3. Copie la **clave principal** al Portapapeles. Guárdela localmente para usarla en los pasos anteriores. 

    ![Obtención de la clave principal en el portal](./media/tutorial-search-location/lbs-get-key.png)


<a id="createmap"></a>

## <a name="create-new-web-page-using-azure-map-control-api"></a>Creación de una nueva página web mediante la API de Control de mapa de Azure
La API de Control de mapa de Azure es una práctica biblioteca de cliente que le permite integrar fácilmente Azure Location Based Services en su aplicación web. Oculta la complejidad de las llamadas sin servicio de REST e impulsa la productividad con componentes que se pueden diseñar y personalizar. Los pasos siguientes muestran cómo crear una página HTML estática insertada con la API de Control de mapa de Location Based Services. 

1. En el equipo local, cree un nuevo archivo y asígnele el nombre **MapSearch.html**. 
2. Agregue los siguientes componentes HTML al archivo:

    ```HTML
    <!DOCTYPE html>
    <html lang="en">

    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, user-scalable=no" />
        <title>Map Search</title>

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
    Observe que el encabezado HTML incluye los archivos de recursos CSS y JavaScript hospedados por la biblioteca de Control de mapa de Azure. Tenga en cuenta el segmento *script* agregado al *cuerpo* del archivo HTML. Este segmento va a contener el código JavaScript insertado para acceder a las API de Azure Location Based Service.
 
3.  Agregue el siguiente código JavaScript al bloque *script* del archivo HTML. Use la clave principal de la cuenta de Location Based Services en el script. 

    ```JavaScript
    // Instantiate map to the div with id "map"
    var LBSAccountKey = "<_your account key_>";
    var map = new atlas.Map("map", {
        "subscription-key": LBSAccountKey
    });
    ```
    Este segmento inicia la API de Control de mapa para la clave de su cuenta de Azure Location Based Services. **Atlas** es el espacio de nombres que contiene la API de Control de mapa de Azure y los componentes visuales relacionados. **atlas.Map** proporciona el control para un mapa visual e interactivo. Para ver el aspecto del mapa, abra la página HTML en el explorador. 

4. Agregue el siguiente código de JavaScript al bloque *script* para agregar una capa de marcadores de búsqueda al Control de mapa:

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

5. Guarde el archivo en su máquina. 


<a id="usesearch"></a>

## <a name="use-search-service-to-find-nearby-point-of-interest"></a>Uso de Search Service para encontrar un punto de interés cercano

En esta sección se muestra cómo usar la API de Search Service de Azure Location Based Services para buscar un punto de interés en el mapa. Se trata de una API de RESTful diseñada para los desarrolladores para la búsqueda de direcciones, puntos de interés u otra información geográfica. Search Service asigna información de latitud y longitud a una dirección especificada. 

1. Abra el archivo **MapSearch.html** creado en la sección anterior y agregue el siguiente código JavaScript al bloque *script* para ilustrar el servicio Search Service. 
    ```JavaScript
    // Perform a request to the search service and create a pin on the map for each result
    var xhttp = new XMLHttpRequest();
    xhttp.onreadystatechange = function () {
        var searchPins = [];

        if (this.readyState === 4 && this.status === 200) {
            var response = JSON.parse(this.responseText);

            var poiResults = response.results.filter((result) => { return result.type === "POI" }) || [];

            searchPins = poiResults.map((poiResult) => {
                var poiPosition = [poiResult.position.lon, poiResult.position.lat];
                return new atlas.data.Feature(new atlas.data.Point(poiPosition), {
                    name: poiResult.poi.name,
                    address: poiResult.address.freeformAddress,
                    position: poiResult.position.lat + ", " + poiResult.position.lon
                });
            });

            map.addPins(searchPins, {
                name: searchLayerName
            });

            var lons = searchPins.map((pin) => { return pin.geometry.coordinates[0] });
            var lats = searchPins.map((pin) => { return pin.geometry.coordinates[1] });

            var swLon = Math.min.apply(null, lons);
            var swLat = Math.min.apply(null, lats);
            var neLon = Math.max.apply(null, lons);
            var neLat = Math.max.apply(null, lats);

            map.setCameraBounds({
                bounds: [swLon, swLat, neLon, neLat],
                padding: 50
            });
        }
    };
    ```
    Este fragmento de código crea un elemento [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agrega un controlador de eventos para analizar la respuesta de entrada. Para obtener una respuesta correcta, recopila las direcciones, los nombres y la información de latitud y longitud de cada ubicación devuelta, en la variable `searchPins`. Por último, agrega esta colección de puntos de ubicación al control `map` en forma de marcadores. 

2. Agregue el código siguiente al bloque *script* para enviar el objeto XMLHttpRequest a Search Service de Azure Location Based Services:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "&api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + LBSAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Este fragmento de código usa la API de búsqueda básica de Search Service llamada **Fuzzy Search**. Esta API administra las entradas más aproximadas en cualquier combinación de dirección o token de *POI*. Busca la **gasolinera** más cercana, según la dirección dada de latitud y longitud, y dentro del radio especificado. Usa la clave principal de su cuenta que se proporcionó anteriormente en el archivo de ejemplo para realizar la llamada a Location Based Services. Devuelve los resultados como pares de latitud y longitud de las ubicaciones encontradas. Para ver los marcadores de búsqueda, abra la página HTML en el explorador. 

3. Agregue las líneas siguientes al bloque *script* para crear elementos emergentes para los puntos de interés devueltos por Search Service:

    ```JavaScript
    // Add a popup to the map which will display some basic information about a search result on hover over a pin
    var popup = new atlas.Popup();
    map.addEventListener("mouseover", searchLayerName, (e) => {
        var popupContentElement = document.createElement("div");
        popupContentElement.style.padding = "5px";

        var popupNameElement = document.createElement("div");
        popupNameElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupNameElement);

        var popupAddressElement = document.createElement("div");
        popupAddressElement.innerText = e.features[0].properties.address;
        popupContentElement.appendChild(popupAddressElement);

        var popupPositionElement = document.createElement("div");
        popupPositionElement.innerText = e.features[0].properties.position;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    La API **atlas.Popup** proporciona una ventana de información anclada en la posición requerida en el mapa. Este fragmento de código establece el contenido y la posición de la ventana emergente, y agrega un agente de escucha de eventos al control `map`, que espera a que el _mouse_ ruede sobre el elemento emergente. 

4. Guarde el archivo y, a continuación, abra el archivo **MapSearch.html** en un explorador web de su elección y observe el resultado. En este momento, el mapa en el explorador muestra los elementos emergentes de información cuando se mantiene el puntero sobre cualquiera de los marcadores de búsqueda mostrados, de forma parecida a esta: 

    ![Control de mapa y Search Service de Azure](./media/tutorial-search-location/lbs-map-search.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Creación de una cuenta con Azure Location Based Services
> * Obtención de la clave principal de una cuenta
> * Crear una nueva página web mediante la API de Control de mapa
> * Uso de Search Service para encontrar un punto de interés cercano

Continúe con el tutorial [Ruta a un punto de interés mediante Azure Location Based Services](./tutorial-route-location.md) para aprender a usar Azure Location Based Services para crear una ruta a su punto de interés. 
