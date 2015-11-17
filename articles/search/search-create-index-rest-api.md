<properties
	pageTitle="Creación de un índice de Búsqueda de Azure con una API de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Creación de un índice en código con Búsqueda de Azure y una API de REST de HTTP"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Creación de un índice de Búsqueda de Azure con una API de REST
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Este artículo muestra cómo crear un índice con la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenido a continuación procede de [Creación de indices (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Consulte el artículo primario para ver más contexto.

Los requisitos previos para crear un índice incluyen tener establecida una conexión con el servicio de búsqueda, normalmente mediante un 'httpClient'.

Un enfoque de tres partes para crear un índice mediante la API de REST incluye establecer una conexión, proporcionar un esquema de índice y ejecutar un comando que crea el índice. Los fragmentos de código pertenecen al [ejemplo de perfiles de puntuación](search-get-started-scoring-profiles.md).

##Definición del esquema de índice

La API de REST acepta solicitudes en JSON. Una forma de crear el esquema es incluir un archivo de esquema independiente como un documento JSON. En el ejemplo siguiente se proporciona una ilustración.

En el ejemplo del que se deriva este fragmento de código, el siguiente JSON se guarda en un archivo llamado schema.json.

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##Configuración de una solicitud HTTP para conectarse y generar el índice

Las API de REST formulan la conexión HTTP para cada solicitud. Cada solicitud siempre establece la dirección URL del servicio, la versión de la API que está usando y una clave de administración que concede operaciones de lectura y escritura en el servicio (denominada a partir de ahora clave principal, ya que así es cómo se llama la clave en el portal).

El fragmento de código usa valores estáticos como entradas, incluido un nombre para el índice, como se especifica en el archivo app.config (no se muestra). El ejemplo se creó de este modo para simplificar el código.

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

En el ejemplo siguiente verá una solicitud HTTP PUT a la dirección URL del servicio junto con `api-version` y el nombre del índice que se va a crear.

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->