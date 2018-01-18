---
title: "Tutorial de distribución global de Azure Cosmos DB para API de SQL | Microsoft Docs"
description: "Obtenga información sobre cómo configurar la distribución global de Azure Cosmos DB con la API de SQL."
services: cosmos-db
keywords: "distribución global"
documentationcenter: 
author: rafats
manager: jhubbard
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: rafats
ms.custom: mvc
ms.openlocfilehash: 0cee55673c8abca29b7e389fa4fd62a48566904b
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-sql-api"></a>Cómo configurar la distribución global de Azure Cosmos DB con la API de SQL

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

En este artículo se muestra cómo usar Azure Portal para configurar la distribución global de Azure Cosmos DB y luego conectarse con la API de SQL.

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global con las [API de SQL](sql-api-introduction.md)

<a id="portal"></a>
[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-sql-api"></a>Conexión a una región de preferencia con la API de SQL

Para aprovechar las ventajas de la [distribución global](distribute-data-globally.md), las aplicaciones cliente pueden especificar la lista del orden de preferencia de regiones que se usará para llevar a cabo operaciones de documentos. Esto puede hacerse estableciendo la directiva de conexión. Según la configuración de la cuenta de Azure Cosmos DB, la disponibilidad regional actual y la lista de preferencias especificada, el SDK de SQL elegirá el punto de conexión óptimo para realizar las operaciones de lectura y escritura.

Esta lista de preferencias se especifica cuando se inicializa una conexión mediante los SDK de SQL. Los SDK aceptan un parámetro opcional "PreferredLocations", que es una lista ordenada de regiones de Azure.

El SDK enviará automáticamente todas las escrituras a la región de escritura actual.

Todas las lecturas se enviarán a la primera región disponible en la lista de PreferredLocations. Si se produce un error en la solicitud, el cliente pasará a la siguiente región de la lista y así sucesivamente.

Los SDK solo intentarán leer en las regiones especificadas en PreferredLocations. Así que, por ejemplo, si la cuenta de base de datos está disponible en cuatro regiones, pero el cliente solo especifica dos de las regiones de lectura (no escritura) para PreferredLocations, no se atenderá ninguna lectura desde la región de escritura que no se especifique en PreferredLocations. Si las regiones de lectura especificadas en PreferredLocations no están disponibles, las lecturas se atenderán fuera de la región de escritura.

La aplicación puede comprobar los puntos de conexión de escritura y lectura actuales elegidos por el SDK. Para ello, comprueba dos propiedades, WriteEndpoint y ReadEndpoint, disponibles en el SDK 1.8 y versiones posteriores.

Si la propiedad PreferredLocations no está establecida, atenderá todas las solicitudes procedentes de la región de escritura actual.

## <a name="net-sdk"></a>.NET SDK
Se puede usar el SDK sin cambiar el código. En este caso, el SDK dirige automáticamente tanto las lecturas como las escrituras a la región de escritura actual.

En la versión 1.8 y posteriores del SDK de .NET, el parámetro ConnectionPolicy para el constructor DocumentClient tiene una propiedad denominada Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations. Esta propiedad es del tipo Collection `<string>` y debería contener una lista de nombres de región. Se da formato a los valores de cadena según la columna Nombre de región en la página [Regiones de Azure][regions], sin espacios antes ni después del primer y el último carácter, respectivamente.

Los puntos de conexión de lectura y escritura actuales están disponibles en DocumentClient.ReadEndpoint y DocumentClient.WriteEndpoint, respectivamente.

> [!NOTE]
> Las direcciones URL de los puntos de conexión no deben considerarse constantes de larga duración. El servicio puede actualizarlas en cualquier momento. El SDK administra este cambio automáticamente.
>
>

```csharp
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
  
ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to DocDB
await docClient.OpenAsync().ConfigureAwait(false);
```

## <a name="nodejs-javascript-and-python-sdks"></a>SDK de NodeJS, JavaScript y Python
Se puede usar el SDK sin cambiar el código. En este caso, el SDK dirigirá automáticamente tanto las lecturas como las escrituras a la región de escritura actual.

En la versión 1.8 y posteriores de cada SDK, el parámetro ConnectionPolicy para el constructor DocumentClient tiene una nueva propiedad denominada DocumentClient.ConnectionPolicy.PreferredLocations. Este parámetro es una matriz de cadenas que acepta una lista de nombres de región. Se da formato a los nombres según la columna Nombre de región en la página [Regiones de Azure][regions]. También puede usar las constantes predefinidas en el objeto de conveniencia AzureDocuments.Regions.

Los puntos de conexión de lectura y escritura actuales están disponibles en DocumentClient.getReadEndpoint y DocumentClient.getWriteEndpoint, respectivamente.

> [!NOTE]
> Las direcciones URL de los puntos de conexión no deben considerarse constantes de larga duración. El servicio puede actualizarlas en cualquier momento. El SDK administrará este cambio automáticamente.
>
>

A continuación, se muestra un ejemplo de código para JavaScript y NodeJS. Python y Java siguen el mismo patrón.

```java
// Creating a ConnectionPolicy object
var connectionPolicy = new DocumentBase.ConnectionPolicy();

// Setting read region selection preference, in the following order -
// 1 - West US
// 2 - East US
// 3 - North Europe
connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

// initialize the connection
var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);
```

## <a name="rest"></a>REST
Una vez que una cuenta de base de datos está disponible en varias regiones, los clientes pueden consultar su disponibilidad llevando a cabo una solicitud GET en el siguiente URI.

    https://{databaseaccount}.documents.azure.com/

El servicio devolverá una lista de regiones y los URI de punto de conexión de Azure Cosmos DB correspondientes para las réplicas. Se indicará la región de escritura actual en la respuesta. Después, el cliente puede seleccionar el punto de conexión adecuado para las demás solicitudes de API de REST como sigue.

Respuesta de ejemplo

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* Todas las solicitudes PUT, POST y DELETE deben ir al URI de escritura indicado.
* Todas las solicitudes GET y las demás solicitudes de solo lectura (como las consultas) pueden ir a cualquier punto de conexión elegido por el cliente.

Las solicitudes de escritura a regiones de solo lectura producirán un error con el código de error HTTP 403 ("Prohibido").

Si la región de escritura cambia después de la fase de descubrimiento inicial del cliente, se producirá un error en las escrituras posteriores a la región de escritura anterior con el código de error HTTP 403 ("Prohibido"). A continuación, el cliente debería de nuevo conseguir con GET la lista de regiones para obtener la región de escritura actualizada.

De este modo finaliza este tutorial. Para información sobre cómo administrar la coherencia de la cuenta replicada globalmente, lea [Niveles de coherencia en Azure Cosmos DB](consistency-levels.md). Para más información sobre cómo funciona la replicación global de bases de datos en Azure Cosmos DB, consulte [Distribución global de datos con Azure Cosmos DB](distribute-data-globally.md).

## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Configuración de la distribución global con Azure Portal
> * Configuración de la distribución global con las API de SQL

Ahora puede continuar en el tutorial siguiente para aprender a desarrollar localmente con el emulador local de Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Desarrollo local con el emulador](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

