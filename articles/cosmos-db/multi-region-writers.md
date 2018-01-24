---
title: Arquitecturas de bases de datos con varios maestros con Azure Cosmos DB | Documentos de Microsoft
description: "Obtenga información sobre cómo diseñar arquitecturas de aplicaciones con lecturas y escrituras locales en varias regiones geográficas con Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 706ced74-ea67-45dd-a7de-666c3c893687
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/23/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e02b286db42d8a9de8f1df8263f40c3732484038
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="multi-master-globally-replicated-database-architectures-with-azure-cosmos-db"></a>Arquitecturas de bases de datos de replicación global con varios maestros con Azure Cosmos DB
Azure Cosmos DB admite la [replicación global](distribute-data-globally.md) inmediata, que permite distribuir datos en varias regiones con un acceso de baja latencia en cualquier parte de la carga de trabajo. Este modelo se utiliza normalmente para las cargas de trabajo del publicador o el consumidor cuando hay un redactor en una única región geográfica y lectores distribuidos globalmente en otras regiones (lectura). 

También puede usar la compatibilidad con la replicación global de Azure Cosmos DB para compilar aplicaciones en las que los redactores y lectores están distribuidos globalmente. En este documento se describe un patrón que permite lograr un acceso de lectura y escritura local para redactores distribuidos con Azure Cosmos DB.

## <a id="ExampleScenario"></a>Publicación de contenido: un escenario de ejemplo
Echemos un vistazo a un escenario real para describir cómo puede usar patrones de lectura y escritura con varios maestros y varias regiones distribuidos globalmente con Azure Cosmos DB. Considere la posibilidad de una plataforma de publicación de contenido basada en Azure Cosmos DB. Estos son algunos requisitos que debe cumplir esta plataforma para una experiencia del usuario excelente para publicadores y consumidores.

* Los autores y los suscriptores se distribuyen en todo el mundo 
* Los autores deben publicar (escribir) artículos en su región local (más cercana)
* Los autores tienen lectores/suscriptores de los artículos que se distribuyen en todo el mundo. 
* Los suscriptores deben recibir una notificación cuando se publican artículos nuevos.
* Los suscriptores deben poder leer artículos desde su región local. También deben poder agregar revisiones a estos artículos. 
* Cualquiera, incluido al autor de los artículos, debe poder ver todas las revisiones asociadas a los artículos desde una región local. 

Si damos por supuesto millones de consumidores y publicadores con miles de millones de artículos, pronto tendremos que hacer frente a problemas de escala y para garantizar un acceso local. Al igual que con la mayoría de los problemas de escalabilidad, la solución se encuentra en una buena estrategia de creación de particiones. A continuación, veamos cómo modelar artículos, revisiones y notificaciones como documentos, configurar cuentas de Azure Cosmos DB e implementar una capa de acceso a datos. 

Si desea obtener más información sobre la creación de particiones y las claves de partición, consulte [Partición y escalado en Azure Cosmos DB](partition-data.md).

## <a id="ModelingNotifications"></a>Modelado de notificaciones
Las notificaciones son fuentes de datos específicos de un usuario. Por lo tanto, los patrones de acceso para los documentos de notificaciones están siempre en el contexto de un único usuario. Por ejemplo, podría enviar una notificación a un usuario o capturar todas las notificaciones para un determinado usuario. Por lo tanto, la opción óptima de clave de creación de particiones para este tipo sería `UserId`.

    class Notification 
    { 
        // Unique ID for Notification. 
        public string Id { get; set; }

        // The user Id for which notification is addressed to. 
        public string UserId { get; set; }

        // The partition Key for the resource. 
        public string PartitionKey 
        { 
            get 
            { 
                return this.UserId; 
            }
        }

        // Subscription for which this notification is raised. 
        public string SubscriptionFilter { get; set; }

        // Subject of the notification. 
        public string ArticleId { get; set; } 
    }

## <a id="ModelingSubscriptions"></a>Modelado de suscripciones
Las suscripciones se pueden crear para diversos criterios, como una categoría específica de artículos de interés o un publicador específico. Por lo tanto, `SubscriptionFilter` es una buena elección para la clave de partición.

    class Subscriptions 
    { 
        // Unique ID for Subscription 
        public string Id { get; set; }

        // Subscription source. Could be Author | Category etc. 
        public string SubscriptionFilter { get; set; }

        // subscribing User. 
        public string UserId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.SubscriptionFilter; 
            } 
        } 
    }

## <a id="ModelingArticles"></a>Modelado de artículos
Una vez que se identifica un artículo mediante las notificaciones, las consultas posteriores se basan normalmente en `Article.Id`. Por tanto, elegir `Article.Id` como clave de partición proporciona la mejor distribución para almacenar artículos dentro de una colección de Azure Cosmos DB. 

    class Article 
    { 
        // Unique ID for Article 
        public string Id { get; set; }
        
        public string PartitionKey 
        { 
            get 
            { 
                return this.Id; 
            } 
        }
        
        // Author of the article
        public string Author { get; set; }

        // Category/genre of the article
        public string Category { get; set; }

        // Tags associated with the article
        public string[] Tags { get; set; }

        // Title of the article
        public string Title { get; set; }
        
        //... 
    }

## <a id="ModelingReviews"></a>Modelado de revisiones
Al igual que los artículos, las revisiones suelen escribirse y leerse en el contexto del artículo. Elegir `ArticleId` como clave de partición proporciona la mejor distribución y un acceso eficaz a las revisiones asociadas al artículo. 

    class Review 
    { 
        // Unique ID for Review 
        public string Id { get; set; }

        // Article Id of the review 
        public string ArticleId { get; set; }

        public string PartitionKey 
        { 
            get 
            { 
                return this.ArticleId; 
            } 
        }
        
        //Reviewer Id 
        public string UserId { get; set; }
        public string ReviewText { get; set; }
        
        public int Rating { get; set; } }
    }

## <a id="DataAccessMethods"></a>Métodos de nivel de acceso a datos
Ahora echaremos un vistazo a los principales métodos de acceso a datos que es necesario para implementar. Esta es la lista de métodos que `ContentPublishDatabase` necesita:

    class ContentPublishDatabase 
    { 
        public async Task CreateSubscriptionAsync(string userId, string category);
    
        public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId);
    
        public async Task<Article> ReadArticleAsync(string articleId);
    
        public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating);
    
        public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId); 
    }

## <a id="Architecture"></a>Configuración de la cuenta de Azure Cosmos DB
Para garantizar lecturas y escrituras locales, debemos crear particiones para los datos basadas no solo en la clave de partición, sino también en el patrón de acceso geográfico en regiones. El modelo se basa en tener una cuenta de base de datos de Azure Cosmos DB georeplicada para cada región. Por ejemplo, con dos regiones, a continuación se muestra una configuración para operaciones de escritura en varias regiones:

| Nombre de cuenta | Región de escritura | Región de lectura |
| --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |

En el diagrama siguiente se muestra cómo se realizan las lecturas y escrituras en una aplicación típica con esta configuración:

![Arquitectura con varios maestros de Azure Cosmos DB](./media/multi-region-writers/multi-master.png)

En el siguiente fragmento de código se muestra cómo inicializar los clientes de un DAL que se ejecuta en la región `West US`.
    
    ConnectionPolicy writeClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    writeClientPolicy.PreferredLocations.Add(LocationNames.WestUS);
    writeClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

    DocumentClient writeClient = new DocumentClient(
        new Uri("https://contentpubdatabase-usa.documents.azure.com"), 
        writeRegionAuthKey,
        writeClientPolicy);

    ConnectionPolicy readClientPolicy = new ConnectionPolicy { ConnectionMode = ConnectionMode.Direct, ConnectionProtocol = Protocol.Tcp };
    readClientPolicy.PreferredLocations.Add(LocationNames.NorthEurope);
    readClientPolicy.PreferredLocations.Add(LocationNames.WestUS);

    DocumentClient readClient = new DocumentClient(
        new Uri("https://contentpubdatabase-europe.documents.azure.com"),
        readRegionAuthKey,
        readClientPolicy);

Con la configuración anterior, el nivel de acceso a datos puede reenviar todas las escrituras a la cuenta local en función de dónde se implemente. Las lecturas se realizan mediante la lectura de ambas cuentas para obtener una visión global de los datos. Este enfoque se puede extender a tantas regiones como sea necesario. Por ejemplo, la siguiente es una configuración con tres regiones geográficas:

| Nombre de cuenta | Región de escritura | Región de lectura 1 | Región de lectura 2 |
| --- | --- | --- | --- |
| `contentpubdatabase-usa.documents.azure.com` | `West US` |`North Europe` |`Southeast Asia` |
| `contentpubdatabase-europe.documents.azure.com` | `North Europe` |`West US` |`Southeast Asia` |
| `contentpubdatabase-asia.documents.azure.com` | `Southeast Asia` |`North Europe` |`West US` |

## <a id="DataAccessImplementation"></a>Implementación del nivel de acceso a datos
Ahora echaremos un vistazo a la implementación del nivel de acceso a datos (DAL) para una aplicación con dos regiones de escritura. El nivel de acceso a datos debe implementar los siguientes pasos:

* Crear varias instancias de `DocumentClient` para cada cuenta. Con dos regiones, cada instancia del nivel DAL tiene elementos `writeClient` y `readClient`. 
* En función de la región de la aplicación implementada, configure los puntos de conexión `writeclient` y `readClient`. Por ejemplo, el nivel DAL implementado en `West US` utiliza `contentpubdatabase-usa.documents.azure.com` para realizar escrituras. El nivel DAL implementado en `NorthEurope` utiliza `contentpubdatabase-europ.documents.azure.com` para las operaciones de escritura.

Con la configuración anterior, se pueden implementar los métodos de acceso de datos. Las operaciones de escritura reenvían la escritura al elemento `writeClient` correspondiente.

    public async Task CreateSubscriptionAsync(string userId, string category)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Subscriptions
        {
            UserId = userId,
            SubscriptionFilter = category
        });
    }

    public async Task WriteReviewAsync(string articleId, string userId, string reviewText, int rating)
    {
        await this.writeClient.CreateDocumentAsync(this.contentCollection, new Review
        {
            UserId = userId,
            ArticleId = articleId,
            ReviewText = reviewText,
            Rating = rating
        });
    }

Para leer las notificaciones y las revisiones, debe leer de ambas regiones y unir los resultados, como se muestra en el fragmento siguiente:

    public async Task<IEnumerable<Notification>> ReadNotificationFeedAsync(string userId)
    {
        IDocumentQuery<Notification> writeAccountNotification = (
            from notification in this.writeClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();
        
        IDocumentQuery<Notification> readAccountNotification = (
            from notification in this.readClient.CreateDocumentQuery<Notification>(this.contentCollection) 
            where notification.UserId == userId 
            select notification).AsDocumentQuery();

        List<Notification> notifications = new List<Notification>();

        while (writeAccountNotification.HasMoreResults || readAccountNotification.HasMoreResults)
        {
            IList<Task<FeedResponse<Notification>>> results = new List<Task<FeedResponse<Notification>>>();

            if (writeAccountNotification.HasMoreResults)
            {
                results.Add(writeAccountNotification.ExecuteNextAsync<Notification>());
            }

            if (readAccountNotification.HasMoreResults)
            {
                results.Add(readAccountNotification.ExecuteNextAsync<Notification>());
            }

            IList<FeedResponse<Notification>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Notification> feed in notificationFeedResult)
            {
                notifications.AddRange(feed);
            }
        }
        return notifications;
    }

    public async Task<IEnumerable<Review>> ReadReviewsAsync(string articleId)
    {
        IDocumentQuery<Review> writeAccountReviews = (
            from review in this.writeClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();
        
        IDocumentQuery<Review> readAccountReviews = (
            from review in this.readClient.CreateDocumentQuery<Review>(this.contentCollection) 
            where review.ArticleId == articleId 
            select review).AsDocumentQuery();

        List<Review> reviews = new List<Review>();
        
        while (writeAccountReviews.HasMoreResults || readAccountReviews.HasMoreResults)
        {
            IList<Task<FeedResponse<Review>>> results = new List<Task<FeedResponse<Review>>>();

            if (writeAccountReviews.HasMoreResults)
            {
                results.Add(writeAccountReviews.ExecuteNextAsync<Review>());
            }

            if (readAccountReviews.HasMoreResults)
            {
                results.Add(readAccountReviews.ExecuteNextAsync<Review>());
            }

            IList<FeedResponse<Review>> notificationFeedResult = await Task.WhenAll(results);

            foreach (FeedResponse<Review> feed in notificationFeedResult)
            {
                reviews.AddRange(feed);
            }
        }

        return reviews;
    }

Por lo tanto, al elegir una buena clave de creación de particiones y una creación de particiones estática basada en cuentas, puede lograr que las lecturas y escrituras locales en varias regiones usen Azure Cosmos DB.

## <a id="NextSteps"></a>Pasos siguientes
En este artículo, se describe cómo puede usar los patrones de lectura y escritura en varias regiones distribuidos globalmente con Azure Cosmos DB, y usar la publicación de contenido como un escenario de ejemplo.

* Más información sobre la compatibilidad de Azure Cosmos DB con la [distribución global](distribute-data-globally.md)
* Más información sobre las [conmutaciones por error manuales y automáticas en Azure Cosmos DB](regional-failover.md)
* Más información sobre la [coherencia global con Azure Cosmos DB](consistency-levels.md)
* Desarrollar con varias regiones mediante [Azure Cosmos DB: API de SQL](tutorial-global-distribution-sql-api.md)
* Desarrollar con varias regiones mediante [Azure Cosmos DB: MongoDB API](tutorial-global-distribution-MongoDB.md)
* Desarrollar con varias regiones mediante [Azure Cosmos DB: Table API](tutorial-global-distribution-table.md)
