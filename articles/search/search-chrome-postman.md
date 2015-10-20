<properties
	pageTitle="Usar Chrome Postman con Búsqueda de Azure | Microsoft Azure"
	description="Usar Chrome Postman con Búsqueda de Azure. Instalar y configurar Postman. Creación de un índice de Búsqueda de Azure. Enviar documentos y consultar el índice con Postman."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="09/08/2015"
	ms.author="heidist"/>

# Cómo usar a Chrome Postman con Búsqueda de Azure #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") es una herramienta proporcionada como parte de Google Chrome que permite a los programadores trabajar eficazmente con los servicios de la API basada en REST como Búsqueda de Azure. Puede usar Postman para crear y consultar rápidamente sus índices de búsqueda mediante el envío de llamadas de API a través de Postman, sin tener que escribir ningún código. Este enfoque es una manera eficaz de aprender la API y de probar nuevas funciones.

![][1]

## Requisitos ##

Debe disponer de un servicio de Búsqueda de Azure. Como con cualquier aplicación personalizada que use Búsqueda de Azure, necesitará la dirección URL de su servicio, además de un administrador `api-key` para que pueda crear el índice. Consulte [Crear un servicio en el portal](search-create-service-portal.md) para obtener instrucciones sobre cómo obtener los valores de su servicio de búsqueda.

## Para instalar Postman ##
Para descargar Postman, visite la [Google Chrome Store](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm). El vínculo desde esta página le permite descargar e instalar el cliente REST para Postman. Una vez instalado, puede iniciar Postman desde Chrome App Launcher.

![][2]

## Para configurar Postman para realizar consultas de Búsqueda de Azure ##
Para configurar Postman, siga los pasos siguientes:

1. Escriba la dirección URL del servicio de Búsqueda de Azure donde dice "Introduzca la URL de solicitud aquí".  
2. Anexar a la dirección URL: `?api-version=2015-02-28`. También puede especificar una versión diferente de la API. Consulte [Versiones del servicio de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn864560.aspx) para obtener más información.
3. Asegúrese de seleccionar `GET`.
4. Haga clic en el botón **Encabezados**.
5. Introduzca valores para:
	- `api-key`: [clave de administrador]
	- `Content-Type`: `application/json; charset=utf-8`
6. Haga clic en **Enviar** para emitir la llamada REST para la Búsqueda de Azure y visualizar la respuesta JSON.

![][3]

## Para crear un índice de Búsqueda de Azure con Postman ##

A continuación, ampliaremos lo completado en el último paso mediante la emisión de una llamada REST para crear un nuevo índice de Búsqueda de Azure. A diferencia de la llamada anterior, la creación del índice requiere un HTTP PUT y un documento JSON con la definición del esquema de índice. En este ejemplo, vamos a crear un índice que almacenará una lista de caminos de montaña. Para ello, siga estos pasos:

1. Cambie la dirección URL a: `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28` usando el nombre del servicio de búsqueda.
2. Cambie el tipo de solicitud de `GET` a `PUT`.
3. En el contenido del cuerpo de texto sin formato, escriba el siguiente JSON:

	    {
	    "name": "trails",
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false},
	    {"name": "name", "type": "Edm.String"},
	    {"name": "county", "type": "Edm.String"},
	    {"name": "elevation", "type": "Edm.Int32"},
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. Haga clic en **Enviar**.

![][4]

## Para publicar documentos en un índice de Búsqueda de Azure con Postman ##
Ahora que se ha creado el índice, podemos cargar documentos en él. Para ello, publicaremos un grupo de documentos en un lote, con datos para cinco caminos del conjunto de datos del United States Geological Survey (USGS):

1. Cambie la dirección URL a: `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28` usando el nombre del servicio de búsqueda. Tenga en cuenta que la dirección URL incluye una ruta de acceso al índice que acaba de crear.
2. Cambie el tipo HTTP a `POST`.
3. En el contenido del cuerpo de texto sin formato, escriba el siguiente JSON:

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }

4. Haga clic en **Enviar**.

![][5]

## Para consultar el índice con Postman ##
El paso final consiste en consultar el índice y emitir una solicitud de búsqueda de texto completo simple para la palabra *camino*.

1. Escriba lo siguiente en la dirección URL: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail` usando el nombre del servicio de búsqueda. Observe que la dirección URL incluye el parámetro de consulta `search` y un término de búsqueda de *camino*.
2. Cambie el tipo de solicitud de HTTP a `GET`.
3. Haga clic en **Enviar**.

En la respuesta, debería ver los resultados de búsqueda de JSON obtenidos de Búsqueda de Azure.

![][6]

## Pasos siguientes ##
Ahora que hemos tratado todos los aspectos básicos del uso de Búsqueda de Azure con Postman, hay algunos aspectos que le ayudarán con los siguientes pasos:

1. Postman es compatible con `Collections`, que es una manera cómoda de guardar solicitudes emitidas con frecuencia. Puede compartir colecciones con otras personas para emitirlas en su propia copia de Postman.
2. En la documentación de Búsqueda de Azure, asegúrese de anotar el tipo de solicitud HTTP (`GET`, `PUT`, etc.) asociada con cada llamada y cambiarla según corresponda en Postman.

Puede encontrar documentación para la API de REST en [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

También puede visitar la [Lista de vídeos y tutoriales](search-video-demo-tutorial-list.md) para obtener más ejemplos.

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png

<!---HONumber=Oct15_HO3-->