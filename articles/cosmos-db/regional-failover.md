---
title: "Conmutación por error regional en Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información sobre el modo en que funciona la conmutación por error manual y automática con Azure Cosmos DB."
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: 
ms.assetid: 446e2580-ff49-4485-8e53-ae34e08d997f
ms.service: cosmos-db
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/17/2017
ms.author: arramac
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3a8b32440ce3ec6cd2da7aaccf218a94e0ee3e77
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="automatic-regional-failover-for-business-continuity-in-azure-cosmos-db"></a>Conmutación por error regional automática para la continuidad empresarial en Azure Cosmos DB
Azure Cosmos DB simplifica la distribución global de datos gracias a que ofrece [cuentas de base de datos de varias regiones](distribute-data-globally.md) completamente administradas que proporcionan un claro equilibrio entre coherencia, disponibilidad y rendimiento, todo ello con sus garantías correspondientes. Las cuentas de Cosmos DB ofrecen alta disponibilidad, latencias de ms de un solo dígito, varios [niveles de coherencia bien definidos](consistency-levels.md), conmutación por error regional transparente con API de hospedaje múltiple, y la posibilidad de escalar elásticamente el rendimiento y el almacenamiento en todo el mundo. 

Cosmos DB admite tanto las conmutaciones por error definidas por directivas como explícitas que le permiten controlar todo el comportamiento del sistema en caso de errores. En este artículo, nos centramos en los siguientes temas:

* Funcionamiento de las conmutaciones por error manuales en Cosmos DB
* Funcionamiento de las conmutaciones por error automáticas en Cosmos DB y efectos del bloqueo de un centro de datos
* Uso de las conmutaciones por error manuales en arquitecturas de aplicación

También obtendrá más información sobre las conmutaciones por error en este vídeo de Azure Friday con Scott Hanselman y el administrador de ingeniería principal Karthik Raman.

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a id="ConfigureMultiRegionApplications"></a>Configuración de aplicaciones de varias regiones
Antes de profundizar en los modos de conmutación por error, veremos cómo puede configurar una aplicación para que aproveche las ventajas que ofrece la disponibilidad de varias regiones y para que sea resistente en caso de una conmutación por error regional.

* En primer lugar, implemente su aplicación en varias regiones.
* Para garantizar un acceso de baja latencia desde todas las regiones en las que su aplicación se ha implementado, configure la correspondiente [lista de regiones preferidas](https://msdn.microsoft.com/library/microsoft.azure.documents.client.connectionpolicy.preferredlocations.aspx#P:Microsoft.Azure.Documents.Client.ConnectionPolicy.PreferredLocations) para cada región a través de uno de los SDK compatibles.

El siguiente fragmento de código muestra cómo inicializar una aplicación de varias regiones. Aquí, la cuenta de Azure Cosmos DB `contoso.documents.azure.com` se configura con dos regiones: oeste de EE. UU. y Europa del Norte. 

* La aplicación se implementa en la región oeste de EE. UU. (mediante Azure App Services por ejemplo). 
* Se ha configurado con `West US` como la primera región preferida para lecturas de baja latencia.
* Se ha configurado con `North Europe` como la segunda región preferida (para lograr alta disponibilidad en caso de errores regionales).

En la API de SQL, esta configuración es similar al fragmento de código siguiente:

```cs
ConnectionPolicy usConnectionPolicy = new ConnectionPolicy 
{ 
    ConnectionMode = ConnectionMode.Direct,
    ConnectionProtocol = Protocol.Tcp
};

usConnectionPolicy.PreferredLocations.Add(LocationNames.WestUS);
usConnectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope);

DocumentClient usClient = new DocumentClient(
    new Uri("https://contosodb.documents.azure.com"),
    "memf7qfF89n6KL9vcb7rIQl6tfgZsRt5gY5dh3BIjesarJanYIcg2Edn9uPOUIVwgkAugOb2zUdCR2h0PTtMrA==",
    usConnectionPolicy);
```

La aplicación también se implementa en la región de Europa del Norte con el orden de las regiones preferidas invertidas. Es decir, la región de Europa del Norte se especifica primero para lecturas de latencia baja. Después, la región del oeste de EE. UU. se configura como la segunda región preferida para lograr alta disponibilidad en caso de errores regionales.

El siguiente diagrama de arquitectura muestra una implementación de aplicación de varias regiones donde Cosmos DB y la aplicación se configuran para que estén disponibles en cuatro regiones geográficas de Azure.  

![Implementación de aplicaciones distribuidas globalmente con Azure Cosmos DB](./media/regional-failover/app-deployment.png)

Ahora, veamos cómo el servicio Cosmos DB administra los errores regionales mediante conmutaciones por error automáticas. 

## <a id="AutomaticFailovers"></a>Conmutaciones por error automáticas
En el caso excepcional de una interrupción regional de Azure o del centro de datos, Cosmos DB desencadena automáticamente conmutaciones por error de todas las cuentas de Cosmos DB presentes en la región afectada. 

**¿Qué ocurre si una región de lectura sufre una interrupción?**

Las cuentas de Cosmos DB con una región de lectura que esté entre las regiones afectadas se desconectan automáticamente de su área de escritura y se marcan como desconectadas. Los SDK de Cosmos DB implementan un protocolo de detección regional que les permite detectar automáticamente si una región está disponible, y redireccionan las llamadas de lectura a la siguiente región disponible de la lista de regiones preferidas. Si ninguna de las regiones de la lista de las regiones preferidas está disponible, las llamadas se devuelven automáticamente a la región actual de escritura. No es necesario realizar ningún cambio en el código de su aplicación para gestionar las conmutaciones por error regionales. Durante todo este proceso, Cosmos DB sigue cumpliendo su garantía de coherencia.

![Errores de la región de lectura en Azure Cosmos DB](./media/regional-failover/read-region-failures.png)

Una vez que la región afectada se recupera de la interrupción, el servicio recupera automáticamente todas las cuentas de Cosmos DB afectadas de la región. Las cuentas de Cosmos DB que tenían una región de lectura en la zona afectada se sincronizan automáticamente con la región de escritura actual y se conecta. Los SDK de Cosmos DB detectan la disponibilidad de la nueva región y evalúan si deben seleccionarla como la región de lectura actual de acuerdo con la lista de regiones preferidas configurada para la aplicación. Las siguientes lecturas se redirigen a la región recuperada sin necesidad de realizar cambios en el código de la aplicación.

**¿Qué ocurre si una región de escritura sufre una interrupción?**

Si la región afectada es la región actual de escritura y la conmutación automática por error está habilitada para la cuenta de Azure Cosmos DB, la región se marcará automáticamente como desconectada. Después, se promueve una región alternativa como región de escritura para cada cuenta de Azure Cosmos DB afectada. Puede habilitar la conmutacion automática por error y controlar completamente el orden de selección de las regiones para las cuentas de Azure Cosmos DB a través de Azure Portal o [mediante programación](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_FailoverPriorityChange). 

![Prioridades de conmutación por error de Azure Cosmos DB](./media/regional-failover/failover-priorities.png)

Durante las conmutaciones por error automáticas, Azure Cosmos DB elige automáticamente la siguiente región de escritura para una cuenta concreta de Azure Cosmos DB según el orden de prioridad especificado. Las aplicaciones pueden utilizar la propiedad WriteEndpoint o la clase DocumentClient para detectar el cambio en la región de escritura.

![Errores de la región de escritura en Azure Cosmos DB](./media/regional-failover/write-region-failures.png)

Una vez que la región afectada se recupera de la interrupción, el servicio recupera automáticamente todas las cuentas de Cosmos DB afectadas de la región. 

* Los datos presentes en la región anterior de escritura que no se replicaron en las regiones de lectura durante la interrupción se publican como una fuente de conflictos. Las aplicaciones pueden leer la fuente de conflictos, resolver los conflictos de acuerdo con la lógica específica de la aplicación y escribir los datos actualizados de nuevo en la cuenta de Azure Cosmos DB según corresponda. 
* Se vuelve a crear la región anterior de escritura como una región de lectura y se pone en línea de nuevo automáticamente. 
* Puede reconfigurar la región de lectura que se puso de nuevo en línea automáticamente como región de escritura mediante una conmutación por error manual a través de Azure Portal o [mediante programación](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate).

El fragmento de código siguiente muestra cómo procesar conflictos una vez que la región afectada se recupera de la interrupción.

```cs
string conflictsFeedContinuationToken = null;
do
{
    FeedResponse<Conflict> conflictsFeed = client.ReadConflictFeedAsync(collectionLink,
        new FeedOptions { RequestContinuation = conflictsFeedContinuationToken }).Result;

    foreach (Conflict conflict in conflictsFeed)
    {
        Document doc = conflict.GetResource<Document>();
        Console.WriteLine("Conflict record ResourceId = {0} ResourceType= {1}", conflict.ResourceId, conflict.ResourceType);

        // Perform application specific logic to process the conflict record / resource
    }

    conflictsFeedContinuationToken = conflictsFeed.ResponseContinuation;
} while (conflictsFeedContinuationToken != null);
```

## <a id="ManualFailovers"></a> Conmutaciones por error manuales

Además de la conmutación por error automática, puede cambiar de forma dinámica y manual la región de escritura actual de una cuenta de Cosmos DB a una de las regiones de lectura existentes. Las conmutaciones por error manuales se pueden iniciar a través de Azure Portal o [mediante programación](https://docs.microsoft.com/rest/api/documentdbresourceprovider/databaseaccounts#DatabaseAccounts_CreateOrUpdate). 

Las conmutaciones por error manuales garantizan que haya **cero pérdida de datos** y **cero pérdida de disponibilidad**, y transfieren sin problemas el estado de escritura de la región de escritura anterior a la nueva para la cuenta de Cosmos DB especificada. Al igual que en la conmutación por error automática, el SDK de Cosmos DB controla automáticamente los cambios de región de escritura durante las conmutaciones por error manuales y garantiza que las llamadas se redirijan automáticamente a la nueva región de escritura. No se requiere ningún cambio de código o configuración de la aplicación para administrar las conmutaciones por error. 

![Conmutaciones por error manuales de Azure Cosmos DB](./media/regional-failover/manual-failovers.png)

Algunos de los escenarios comunes en los que la conmutación por error manual puede ser útil son:

**Modelo de funcionamiento ininterrumpido**: si las aplicaciones tienen patrones de tráfico predecibles en función de la hora del día, puede cambiar periódicamente el estado de escritura a la región geográfica más activa en función de la hora del día.

**Actualización de servicio**: ciertas implementaciones de aplicaciones distribuidas globalmente pueden requerir que se redirija el tráfico a una región diferente mediante Traffic Manager durante actualizaciones de servicio programadas. Dicha implementación de aplicaciones puede usar la conmutación por error manual para mantener el estado de escritura en la región que tendrá tráfico activo durante el período de actualización del servicio.

**Exploraciones de continuidad empresarial y recuperación ante desastres (BCDR) y alta disponibilidad y recuperación ante desastres (HADR)**: la mayoría de las aplicaciones empresariales incluyen pruebas de continuidad empresarial en su proceso de desarrollo y de lanzamiento. Las pruebas de BCDR y HADR a menudo son un paso importante de los certificados de cumplimiento y garantizan la disponibilidad del servicio en caso de una interrupción regional. Puede probar si las aplicaciones que usan Cosmos DB para el almacenamiento están preparadas para BCDR activando una conmutación por error manual de su cuenta de Cosmos DB o agregando y quitando una región de forma dinámica.

En este artículo, hemos revisado cómo funcionan las conmutaciones por error manuales y automáticas en Cosmos DB, y cómo puede configurar sus aplicaciones y cuentas de Cosmos DB para que estén disponibles globalmente. Use la compatibilidad con replicación global de Cosmos DB para mejorar la latencia de extremo a extremo y asegurarse de que estén altamente disponibles incluso en caso de errores de región. 

## <a id="NextSteps"></a>Pasos siguientes
* Obtenga información sobre la compatibilidad de Cosmos DB con la [distribución global](distribute-data-globally.md)
* Más información sobre la [coherencia global con Azure Cosmos DB](consistency-levels.md)
* Desarrollo con varias regiones mediante la [API de SQL](tutorial-global-distribution-sql-api.md) de Azure Cosmos DB
* Más información sobre la compilación de [arquitecturas de sistema de escritura de varias regiones](multi-region-writers.md) con Azure Cosmos DB

