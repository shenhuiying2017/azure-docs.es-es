<properties
	pageTitle="Creación de consultas en Búsqueda de Azure con llamadas de REST | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Cree una consulta de búsqueda en Búsqueda de Azure y use los parámetros de búsqueda para filtrar, ordenar y crear facetas en el resultado de búsqueda mediante la biblioteca de .NET o el SDK."
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Creación de consultas en Búsqueda de Azure mediante llamadas REST 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

En este artículo se muestra cómo crear una consulta en un índice con la [API de REST de Búsqueda de Azure](https://msdn.microsoft.com/library/azure/dn798935.aspx). Parte del contenido siguiente procede de [Búsqueda de documentos (API de REST de Búsqueda de Azure)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Consulte el artículo primario para ver más contexto.

Los requisitos previos para la importación incluyen tener un índice existente preparado, cargado con documentos que proporcionen los datos que se pueden buscar.

Al usar la API de REST, las consultas se basan en una solicitud GET HTTP. Los fragmentos de código pertenecen al [ejemplo de perfiles de puntuación](search-get-started-scoring-profiles.md).

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->