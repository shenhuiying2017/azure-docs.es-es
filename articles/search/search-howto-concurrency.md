---
title: "Cómo administrar las operaciones de escritura simultáneas en los recursos de Azure Search"
description: "Use la simultaneidad optimista para evitar colisiones en vuelo en las actualizaciones o eliminaciones de índices, indexadores u orígenes de datos de Azure Search."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: aee1b7376d4829e3e2f5a232525e3c3cb4df9d8e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Cómo administrar la simultaneidad en Azure Search

Al administrar recursos de Azure Search, como los índices y los orígenes de datos, es importante actualizarlos de forma segura, sobre todo si hay varios componentes de la aplicación que obtienen acceso a ellos de forma simultánea. Cuando dos clientes actualizan simultáneamente un recurso sin ninguna coordinación, es posible que se produzcan condiciones de carrera. Para evitarlo, Azure Search ofrece un *modelo de simultaneidad optimista*. No hay ningún bloqueo en un recurso, sino que hay un valor ETag para todos los recursos que identifica la versión del recurso de modo que pueda crear solicitudes que eviten sobrescrituras accidentales.

> [!Tip]
> El código conceptual de una [solución de C# de ejemplo](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer) explica el funcionamiento del control de simultaneidad en Azure Search. El código crea condiciones que invocan el control de simultaneidad. Con leer el [siguiente fragmento de código](#samplecode) probablemente sea suficiente para la mayoría de los desarrolladores pero, si quiere ejecutarlo, edite appSettings.json para agregar el nombre del servicio y una clave de API de administración. Si se indica la dirección URL de servicio `http://myservice.search.windows.net`, el nombre del servicio será `myservice`.

## <a name="how-it-works"></a>Cómo funciona

La simultaneidad optimista se implementa mediante comprobaciones de condiciones de acceso en las llamadas de API que se escriben en índices, indexadores, orígenes de datos y recursos de synonymMap. 

Todos los recursos tienen una [*etiqueta de entidad (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) que proporciona información sobre la versión del objeto. Si comprueba primero la etiqueta ETag, puede evitar que se produzcan actualizaciones simultáneas en un flujo de trabajo típico (obtener, modificar localmente, actualizar, etc.) asegurándose de que la etiqueta ETag del recurso coincida con la copia local. 

+ La API de REST usa una etiqueta [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) en el encabezado de solicitud.
+ El SDK de .NET establece la etiqueta ETag mediante un objeto accessCondition, estableciendo el [encabezado If-Match | If-Match-None](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) en el recurso. Todos los objetos heredados de [IResourceWithETag (SDK de .NET)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) tienen un objeto accessCondition.

Cada vez que actualice un recurso, la etiqueta ETag correspondiente cambiará de forma automática. Al implementar la administración de simultaneidad, lo único que está haciendo es colocar una condición previa en la solicitud de actualización que requiere que el recurso remoto tenga la misma ETag que la copia del recurso que modificó en el cliente. Si un proceso simultáneo ya cambió el recurso remoto, la etiqueta ETag no coincidirá con la condición previa y se producirá el error HTTP 412 en la solicitud. Si usa el SDK de .NET, esto se manifiesta como una excepción `CloudException`, donde el método de extensión `IsAccessConditionFailed()` devuelve "true".

> [!Note]
> Solo hay un mecanismo para la simultaneidad. Se usa siempre con independencia de la API que se use para las actualizaciones de recursos. 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>Casos de uso y código de ejemplo

En el siguiente código se muestran las comprobaciones accessCondition para las operaciones de actualización clave:

+ Error en una actualización si el recurso ya no existe
+ Error en una actualización si la versión del recurso cambia.

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>Código de ejemplo del [programa DotNetETagsExplainer](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
            // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
            // blank ETag.
            Console.WriteLine("Creating index...\n");
            index = serviceClient.Indexes.Create(index);
            
            Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

            // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
            // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
            // succeed if the index already exists.
            index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
            index =
                serviceClient.Indexes.CreateOrUpdate(
                    index,
                    accessCondition: AccessCondition.GenerateIfExistsCondition());

            Console.WriteLine(
                $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

            // More importantly, ETags protect you from concurrent updates to the same resource. If another
            // client tries to update the resource, it will fail as long as all clients are using the right
            // access conditions.
            Index indexForClient1 = index;
            Index indexForClient2 = serviceClient.Indexes.Get("test");

            Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
            Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

            // Client 1 successfully updates the index.
            indexForClient1.Fields.Add(new Field("a", DataType.Int32));
            indexForClient1 =
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient1,
                    accessCondition: AccessCondition.IfNotChanged(indexForClient1));

            Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

            // Client 2 tries to update the index, but fails, thanks to the ETag check.
            try
            {
                indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
                serviceClient.Indexes.CreateOrUpdate(
                    indexForClient2, 
                    accessCondition: AccessCondition.IfNotChanged(indexForClient2));

                Console.WriteLine("Whoops; This shouldn't happen");
                Environment.Exit(1);
            }
            catch (CloudException e) when (e.IsAccessConditionFailed())
            {
                Console.WriteLine("Client 2 failed to update the index, as expected.");
            }

            // You can also use access conditions with Delete operations. For example, you can implement an
            // atomic version of the DeleteTestIndexIfExists method from this sample like this:
            Console.WriteLine("Deleting index...\n");
            serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

            // This is slightly better than using the Exists method since it makes only one round trip to
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

            // And we're done! Bye!
            Console.WriteLine("Complete.  Press any key to end application...\n");
            Console.ReadKey();
        }

        private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
        {
            string searchServiceName = configuration["SearchServiceName"];
            string adminApiKey = configuration["SearchServiceAdminApiKey"];

            SearchServiceClient serviceClient =
                new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
            return serviceClient;
        }

        private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
        {
            if (serviceClient.Indexes.Exists("test"))
            {
                serviceClient.Indexes.Delete("test");
            }
        }

        private static Index DefineTestIndex() =>
            new Index()
            {
                Name = "test",
                Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
            };
    }
}
```

## <a name="design-pattern"></a>Modelo de diseño

Los modelos de diseño para la implementación de la simultaneidad optimista deben incluir un bucle que vuelva a intentar efectuar la comprobación de la condición de acceso, una prueba para la condición de acceso y, opcionalmente, que recupere un recurso actualizado antes de intentar aplicar de nuevo los cambios. 

En este fragmento de código se muestra la agregación de un synonymMap a un índice que ya existe. Este código está tomado del [tutorial C# de sinónimos (versión preliminar) para Azure Search](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk). 

El fragmento de código obtiene el índice "hotels", comprueba la versión del objeto en una operación de actualización, genera una excepción si se produce un error en la condición y, después, vuelve a intentar efectuar la operación (hasta tres veces) a partir de la recuperación del índice desde el servidor para obtener la versión más reciente.

        private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
        {
            int MaxNumTries = 3;

            for (int i = 0; i < MaxNumTries; ++i)
            {
                try
                {
                    Index index = serviceClient.Indexes.Get("hotels");
                    index = AddSynonymMapsToFields(index);

                    // The IfNotChanged condition ensures that the index is updated only if the ETags match.
                    serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

                    Console.WriteLine("Updated the index successfully.\n");
                    break;
                }
                catch (CloudException e) when (e.IsAccessConditionFailed())
                {
                    Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
                }
            }
        }
        
        private static Index AddSynonymMapsToFields(Index index)
        {
            index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
            index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
            return index;
        }


## <a name="next-steps"></a>Pasos siguientes

Consulte el [ejemplo de C# de sinónimos](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms) para obtener más contexto sobre cómo actualizar un índice existente de forma segura.

Intente modificar cualquiera de los ejemplos siguientes para que incluyan etiquetas ETag u objetos AccessCondition.

+ [Ejemplo de API de REST en Github](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Ejemplo de SDK para .NET en Github](https://github.com/Azure-Samples/search-dotnet-getting-started). Esta solución incluye el proyecto "DotNetEtagsExplainer", que contiene el código presentado en este artículo.

## <a name="see-also"></a>Otras referencias

  [Common HTTP request and response headers](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)   (Encabezados de solicitud y respuesta HTTP comunes)  
  [HTTP status codes](https://docs.microsoft.com/rest/api/searchservice/http-status-codes) (Códigos de estado HTTP) [Index operations (REST API)](https://docs.microsoft.com/\rest/api/searchservice/index-operations) (Operaciones de índice (API de REST))