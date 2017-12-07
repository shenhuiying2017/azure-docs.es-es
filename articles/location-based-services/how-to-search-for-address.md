---
title: "Cómo buscar una dirección mediante el servicio de búsqueda Azure Location Based Services (versión preliminar) | Microsoft Docs"
description: "Obtenga información sobre cómo buscar una dirección mediante el servicio de búsqueda Azure Location Based Services (versión preliminar)"
services: location-based-services
keywords: No agregue o edite palabras clave sin consultar su campo SEO.
author: philmea
ms.author: philmea
ms.date: 11/29/2017
ms.topic: how-to
ms.service: location-based-services
ms.openlocfilehash: f7337c1c5821016987096da47dda4ac1124d7910
ms.sourcegitcommit: cfd1ea99922329b3d5fab26b71ca2882df33f6c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/30/2017
---
# <a name="how-to-find-an-address-using-the-azure-location-based-services-preview-search-service"></a>Cómo buscar una dirección mediante el servicio de búsqueda Azure Location Based Services (versión preliminar)
El servicio de búsqueda es un conjunto de RESTful de API diseñadas para que los desarrolladores busquen direcciones, lugares, puntos de interés, listados de empresas y otra información geográfica. El servicio de búsqueda asigna una latitud/longitud a una dirección específica, calle transversal, característica geográfica o punto de interés (POI). Los valores de latitud y longitud devueltos por las API del servicio de búsqueda se pueden usar como parámetros en otros servicios Azure Location Based Services como las API de ruta y flujo de tráfico.

## <a name="prerequisites"></a>Requisitos previos
Instale la [aplicación Postman](https://www.getpostman.com/apps).

Una cuenta y una clave de suscripción de Azure Location Based Services. Para obtener información sobre cómo crear una cuenta y recuperar una clave de suscripción, consulte [How to manage your Azure Location Based Services account and keys](how-to-manage-account-keys.md) (Cómo administrar la cuenta y las claves de Azure Location Based Services). 

## <a name="using-fuzzy-search"></a>Uso de búsqueda aproximada

La API predeterminada del servicio de búsqueda es la búsqueda aproximada, que controla las entradas de cualquier combinación de tokens de dirección o POI. Esta API de búsqueda es la "búsqueda de línea única" canonical y es útil cuando no se sabe qué aporta el usuario como consulta de búsqueda. La API de búsqueda aproximada es una combinación de geocodificación y búsqueda de POI. La API también se puede ponderar con una posición contextual (par de latitud y longitud), completamente restringida por una coordenada y un radio, o bien se puede ejecutar de forma más general sin ningún punto de anclaje de polarización geográfica.

La mayoría de las consultas de búsqueda tienen como valor predeterminado 'maxFuzzyLevel=1' para aumentar el rendimiento y reducir los resultados inusuales. Este valor predeterminado puede invalidarse según requiera la solicitud transmitiendo el parámetro de consulta 'maxFuzzyLevel=2' o '3'.

### <a name="search-for-an-address-using-fuzzy-search"></a>Buscar una dirección mediante la búsqueda aproximada

1. Abra la aplicación Postman, haga clic en Nuevo | Crear nuevo y seleccione **Solicitud GET**. Escriba un nombre de solicitud de **búsqueda aproximada**, seleccione una colección o carpeta donde guardarlo y haga clic en **Guardar**.

    Para obtener más información, consulte la documentación de solicitudes de Postman.

2. En la pestaña Builder, seleccione el método HTTP **GET** y escriba la dirección URL de solicitud para el punto de conexión de API.

    ![Búsqueda aproximada ](./media/how-to-search-for-address/fuzzy_search_url.png)

    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | https://atlas.microsoft.com/search/fuzzy/json? |
    | Autorización | Sin autenticación |

    El atributo **json** de la ruta de acceso URL determina el formato de respuesta. Usa json en todo este artículo para una fácil utilización y legibilidad. Puede encontrar los formatos de respuesta disponibles en la definición **Get Search Fuzzy** (Obtener búsqueda aproximada) de la [referencia de API funcional de Location Based Services] (https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchfuzzy).

3. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:

    ![Búsqueda aproximada ](./media/how-to-search-for-address/fuzzy_search_params.png)

    | Clave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *clave de suscripción* |
    | query | pizza |

4. Haga clic en **Enviar** y revise el cuerpo de la respuesta. 

    La cadena de consulta ambigua de "pizza" devolvió 10 resultados de punto de interés (POI) con categorías correspondientes a "pizza" y "restaurante". Cada resultado devuelve una dirección, valores de latitud y longitud, ventanilla y puntos de entrada para la ubicación.
    
    Los resultados son variados para esta consulta, no se asocian a ninguna ubicación de referencia determinada. Puede usar el parámetro **countrySet** para especificar solo los países para los que su aplicación necesita cobertura, ya que el comportamiento predeterminado es buscar en todo el mundo, siendo posible la devolución de resultados innecesarios.

5. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,countrySet=US
    ```
    >[!NOTE] 
    >Asegúrese de separar con comas los parámetros de URI adicionales en la cadena de consulta.
    
    Ahora los resultados están limitados por el código de país y la consulta devuelve pizzerías de los Estados Unidos.
    
    Para proporcionar resultados orientados a una ubicación determinada, puede consultar un punto de interés y usar los valores de latitud y longitud devueltos en la llamada al servicio de búsqueda aproximada. En este caso, usó el servicio de búsqueda para devolver la ubicación de Space Needle de Seattle y utilizó los valores de latitud y longitud para orientar la búsqueda.
    
4. En Params (Parámetros), escriba los siguientes pares clave-valor y haga clic en **Enviar**:

    ![Búsqueda aproximada ](./media/how-to-search-for-address/fuzzy_search_latlon.png)
    
    | Clave | Valor |
    |-----|------------|
    | latitud | 47.62039 |
    | longitud | -122.34928 |

## <a name="search-for-address-properties-and-coordinates"></a>Buscar las coordenadas y las propiedades de dirección 

Puede pasar una dirección completa o parcial a la API de dirección de búsqueda y recibir una respuesta que incluya propiedades de dirección detalladas como municipalidad o subdivisión, así como valores posicionales de latitud y longitud.

1. En Postman, haga clic en **Nueva solicitud** | **Solicitud GET** y asígnele el nombre **Búsqueda de direcciones**.
2. En la pestaña Builder, seleccione el método HTTP **GET**, escriba la dirección URL de solicitud para el punto de conexión de API y seleccione un protocolo de autorización, si procede.

    ![Búsqueda de direcciones ](./media/how-to-search-for-address/address_search_url.png)
    
    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | https://atlas.microsoft.com/search/address/json? |
    | Autorización | Sin autenticación |

2. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:
    
    ![Búsqueda de direcciones ](./media/how-to-search-for-address/address_search_params.png)
    
    | Clave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *clave de suscripción* |
    | query | 400 Broad St, Seattle, WA 98109 |
    
3. Haga clic en **Enviar** y revise el cuerpo de la respuesta. 
    
    En este caso, especificó una consulta de dirección completa y recibe un solo resultado en el cuerpo de respuesta. 
    
4. En Params (Parámetros), edite la cadena de consulta de modo que tenga el siguiente valor:
    ```
        400 Broad, Seattle
    ```

5. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,typeahead
    ```

    La marca **typeahead** indica a la API de búsqueda de direcciones que trate la consulta como una entrada parcial y devuelva una matriz de valores predictivos.

## <a name="search-for-a-street-address-using-reverse-address-search"></a>Buscar una dirección mediante la búsqueda de direcciones inversa
1. En Postman, haga clic en **Nueva solicitud** | **Solicitud GET** y asígnele el nombre **Búsqueda de direcciones inversa**.

2. En la pestaña Builder, seleccione el método HTTP **GET** y escriba la dirección URL de solicitud para el punto de conexión de API.
    
    ![Dirección URL de Búsqueda de direcciones inversa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | https://atlas.microsoft.com/search/address/reverse/json? |
    | Autorización | Sin autenticación |
    
2. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:
    
    ![Parámetros de Búsqueda de direcciones inversa ](./media/how-to-search-for-address/reverse_address_search_params.png)
    
    | Clave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *clave de suscripción* |
    | query | 47.59093,-122.33263 |
    
3. Haga clic en **Enviar** y revise el cuerpo de la respuesta. 
    
    La respuesta incluye la entrada POI para Safeco Field con una categoría de POI de "estadio". 
    
4. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,number
    ```
    Si el parámetro de consulta [number](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters) se envía con la solicitud, la respuesta puede incluir el lado de la calle (izquierda o derecha) y también una posición de desplazamiento para ese número.
    
5. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,spatialKeys
    ```

    Cuando se establece el parámetro de consulta [spatialKeys](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la respuesta contiene información importante geoespacial propietaria para una ubicación especificada.

6. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,returnSpeedLimit
    ```
    
    Cuando se establece el parámetro de consulta [returnSpeedLimit](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la respuesta devuelve el límite de velocidad publicado.

7. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,returnRoadUse
    ```

    Cuando se establece el parámetro de consulta [returnRoadUse](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters), la respuesta devuelve la matriz de uso de carretera para códigos geográficos a nivel de calle.

8. Agregue el siguiente valor a la cadena de consulta y haga clic en **Enviar**:
    ```
        ,roadUse
    ```

    Puede restringir la consulta de código geográfico inversa a un tipo específico de uso de carretera mediante el parámetro de consulta [roadUse](https://docs.microsoft.com/en-us/rest/api/location-based-services/search/getsearchaddressreverse#search_getsearchaddressreverse_uri_parameters).
    
## <a name="search-for-the-cross-street-using-reverse-address-cross-street-search"></a>Buscar la calle transversal mediante la búsqueda de calles transversales de direcciones inversa

1. En Postman, haga clic en **Nueva solicitud** | **Solicitud GET** y asígnele el nombre **Búsqueda de calles transversales de direcciones inversa**.

2. En la pestaña Builder, seleccione el método HTTP **GET** y escriba la dirección URL de solicitud para el punto de conexión de API.
    
    ![Búsqueda de calles transversales de direcciones inversa ](./media/how-to-search-for-address/reverse_address_search_url.png)
    
    | Parámetro | Valor sugerido |
    |---------------|------------------------------------------------|
    | HTTP method | GET |
    | URL de la solicitud | https://atlas.microsoft.com/search/address/reverse/crossstreet/json? |
    | Autorización | Sin autenticación |
    
3. Haga clic en **Params** (Parámetros) y especifique los siguientes pares clave-valor para utilizar como parámetros de consulta o ruta de acceso en la dirección URL de la solicitud:
    
    | Clave | Valor |
    |------------------|-------------------------|
    | api-version | 1.0 |
    | subscription-key | *clave de suscripción* |
    | query | 47.59093,-122.33263 |
    
4. Haga clic en **Enviar** y revise el cuerpo de la respuesta. 

## <a name="next-steps"></a>Pasos siguientes
- Explore la documentación de la API [Azure Location Based Serices Search service](https://docs.microsoft.com/en-us/rest/api/location-based-services/search) (Servicio de búsqueda Azure Location Based Services) 