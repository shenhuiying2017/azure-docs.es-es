---
title: Búsqueda con Azure Maps | Microsoft Docs
description: Búsqueda de un punto cercano de interés mediante Azure Maps
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
ms.openlocfilehash: a4479ceebd4c8aad477b5f13a5bcc06d24c1202d
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2018
---
# <a name="search-nearby-points-of-interest-using-azure-maps"></a>Búsqueda de puntos cercanos de interés mediante Azure Maps

En este tutorial se muestra cómo configurar una cuenta con Azure Maps y luego usar las API de Maps para buscar un punto de interés. En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Crear una cuenta de Azure Maps
> * Recuperar la clave principal de la cuenta de Maps
> * Crear una nueva página web mediante la API de control de mapa
> * Usar el servicio de búsqueda de Maps para encontrar un punto cercano de interés

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.

## <a name="log-in-to-the-azure-portal"></a>Iniciar sesión en Azure Portal
Inicie sesión en [Azure Portal](https://portal.azure.com).

<a id="createaccount"></a>

## <a name="create-an-account-with-azure-maps"></a>Creación de una cuenta con Azure Maps

Cree una nueva cuenta de Maps con los pasos siguientes:

1. En la esquina superior izquierda de [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**.
2. En el campo *Buscar en el Marketplace*, escriba **Maps**.
3. En *Resultados*, seleccione **Maps**. Haga clic en el botón **Crear** que aparece debajo del mapa. 
4. En la página **Create Maps Account** (Crear una cuenta de Azure Maps), escriba los siguientes valores:
    - El *nombre* de la nueva cuenta. 
    - La *suscripción* que quiere usar para esta cuenta.
    - El nombre del *grupo de recursos* para esta cuenta. Puede elegir *Crear nuevo* o *Usar existente* para el grupo de recursos.
    - Seleccione *Ubicación del grupo de recursos*.
    - Lea la *licencia* y la *declaración de privacidad* y active la casilla para aceptar los términos. 
    - Haga clic en el botón **Crear** .
   
    ![Creación de una cuenta de Maps en el portal](./media/tutorial-search-location/create-account.png)


<a id="getkey"></a>

## <a name="get-the-primary-key-for-your-account"></a>Obtención de la clave principal de una cuenta

Una vez que se haya creado correctamente la cuenta de Maps, recupere la clave que le permite consultar las API de Maps.

1. Abra su cuenta de Maps en el portal.
2. En la sección de configuración, seleccione **Claves**.
3. Copie la **clave principal** al Portapapeles. Guárdela localmente para usarla más adelante en este tutorial. 

    ![Obtención de la clave principal en el portal](./media/tutorial-search-location/get-key.png)


<a id="createmap"></a>

## <a name="create-a-new-map"></a>Creación de un nuevo mapa 
Map Control API es una práctica biblioteca cliente que le permite integrar fácilmente Maps en su aplicación web. Oculta la complejidad de las llamadas sin servicio de REST e impulsa la productividad con componentes que se pueden diseñar y personalizar. En los pasos siguientes se muestra cómo crear una página HTML estática insertada con Map Control API. 

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
    Observe que el encabezado HTML incluye los archivos de recursos CSS y JavaScript hospedados por la biblioteca de Control de mapa de Azure. Tenga en cuenta el segmento *script* agregado al *cuerpo* del archivo HTML. Este segmento contendrá el código JavaScript insertado para acceder a las API de Azure Maps.
 
3. Agregue el siguiente código JavaScript al bloque *script* del archivo HTML. Reemplace la cadena **\<su clave de cuenta\>** por la clave principal que copió de la cuenta de Maps.

    ```JavaScript
    // Instantiate map to the div with id "map"
    var MapsAccountKey = "<your account key>";
    var map = new atlas.Map("map", {
        "subscription-key": MapsAccountKey
    });
    ```
    Este segmento inicia Map Control API para la clave de cuenta de Azure Maps. **Atlas** es el espacio de nombres que contiene la API y los componentes visuales relacionados. **Atlas.Map** proporciona el control para un mapa web visual e interactivo. 
    
4. Guarde los cambios en el archivo y abra la página HTML en un explorador. Este es el mapa más básico que puede crear con una llamada a **atlas.map** y una clave de cuenta. 

   ![Visualización del mapa](./media/tutorial-search-location/basic-map.png)


<a id="usesearch"></a>

## <a name="add-search-capabilities"></a>Adición de funcionalidades de búsqueda

En esta sección se muestra cómo usar Search API de Azure Maps para buscar un punto de interés en el mapa. Se trata de una API RESTful diseñada para los desarrolladores para buscar direcciones, puntos de interés y otra información geográfica. El servicio Search asigna información de latitud y longitud a una dirección especificada. 

1. Agregue una nueva capa a su mapa para mostrar los resultados de búsqueda. Agregue el siguiente código Javascript al bloque de *script*, tras el código que inicializa el mapa. 

    ```JavaScript
    // Initialize the pin layer for search results to the map
    var searchLayerName = "search-results";
    map.addPins([], {
        name: searchLayerName,
        cluster: false,
        icon: "pin-round-darkblue"
    });
    ```

2. Cree un elemento [XMLHttpRequest](https://xhr.spec.whatwg.org/) y agregue un controlador de eventos para analizar la respuesta JSON que envía el servicio Search de Azure Maps. Este fragmento de código compila el controlador de eventos para recopilar las direcciones, los nombres y la información de longitud de cada ubicación devuelta en la variable `searchPins`. Por último, agrega esta colección de puntos de ubicación al control `map` en forma de marcadores. 

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

3. Agregue el código siguiente al bloque de *script* para compilar la consulta y enviar el objeto XMLHttpRequest al servicio Search de Azure Maps:

    ```JavaScript
    var url = "https://atlas.microsoft.com/search/fuzzy/json?";
    url += "api-version=1.0";
    url += "&query=gasoline%20station";
    url += "&subscription-key=" + MapsAccountKey;
    url += "&lat=47.6292";
    url += "&lon=-122.2337";
    url += "&radius=100000";

    xhttp.open("GET", url, true);
    xhttp.send();
    ``` 
    Este fragmento de código usa la API de búsqueda básica de Search Service llamada **Fuzzy Search**. Administra las entradas más aproximadas, lo que incluye cualquier combinación de tokens de dirección o punto de interés (POI). Busca las **gasolineras** más cercanas en un radio especificado de las coordenadas de latitud y longitud dadas. Usa la clave principal de su cuenta que se proporcionó anteriormente en el archivo de ejemplo para realizar la llamada a Maps. Devuelve los resultados como pares de latitud y longitud de las ubicaciones encontradas. 
    
4. Guarde el archivo **MapSearch.html** y actualice el explorador. Ahora debería ver que el mapa se centra en Seattle y que aparecen marcadas con chinchetas azules las ubicaciones de las gasolineras del área. 

   ![Visualización del mapa con los resultados de la búsqueda](./media/tutorial-search-location/pins-map.png)

5. Para ver los datos sin procesar que se representan en el mapa, tome el objeto XMLHTTPRequest que compiló en el archivo y escríbalo en el explorador. Reemplace \<su clave de cuenta\> por la clave principal. 

   ```http
   https://atlas.microsoft.com/search/fuzzy/json?api-version=1.0&query=gasoline%20station&subscription-key=<your account key>&lat=47.6292&lon=-122.2337&radius=100000
   ```

En este momento, la página MapSearch puede mostrar las ubicaciones de los puntos de interés que se devuelven en una consulta de búsqueda aproximada. Vamos a agregar algunas funcionalidades interactivas y más información sobre las ubicaciones. 

## <a name="add-interactive-data"></a>Adición de datos interactivos

La asignación que hemos hecho hasta ahora solo examina los datos de latitud o longitud de los resultados de búsqueda. Sin embargo, si examina el archivo JSON sin formato que devuelve el servicio Search de Azure Maps, verá que contiene información adicional sobre cada gasolinera, incluidos el nombre y la dirección postal. Puede incorporar esos datos al mapa con cuadros emergentes interactivos. 

1. Agregue las líneas siguientes al bloque *script* para crear elementos emergentes para los puntos de interés devueltos por Search Service:

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
        popupPositionElement.innerText = e.features[0].properties.name;
        popupContentElement.appendChild(popupPositionElement);

        popup.setPopupOptions({
            position: e.features[0].geometry.coordinates,
            content: popupContentElement
        });

        popup.open(map);
    });
    ```
    La API **atlas.Popup** proporciona una ventana de información anclada en la posición requerida en el mapa. Este fragmento de código establece el contenido y la posición de la ventana emergente, y agrega un agente de escucha de eventos al control `map`, que espera a que el _mouse_ ruede sobre el elemento emergente. 

4. Guarde el archivo y actualice el explorador. Ahora el mapa del explorador muestra los cuadros emergentes de información cuando se mantiene el mouse sobre cualquiera de las chinchetas de búsqueda. 

    ![Control de mapa y Search Service de Azure](./media/tutorial-search-location/popup-map.png)


## <a name="next-steps"></a>Pasos siguientes
En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Crear una cuenta con Azure Maps
> * Obtención de la clave principal de una cuenta
> * Crear una nueva página web mediante la API de Control de mapa
> * Uso de Search Service para encontrar un punto de interés cercano

En el siguiente tutorial se muestra cómo visualizar una ruta entre dos ubicaciones. 

> [!div class="nextstepaction"]
> [Ruta a un destino](./tutorial-route-location.md)
