---
title: "Claves únicas en Azure Cosmos DB | Microsoft Docs"
description: "Obtenga información acerca de cómo usar las claves únicas en la base de datos de Azure Cosmos DB."
services: cosmos-db
keywords: "restricción de clave única, infracción de restricción de clave única"
author: rafats
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b15d5041-22dd-491e-a8d5-a3d18fa6517d
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: rafats
ms.openlocfilehash: c530b34edf9bfa0651b7b114dcf7e8add0d906ed
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="unique-keys-in-azure-cosmos-db"></a>Claves únicas en Azure Cosmos DB

Las claves únicas proporcionan a los desarrolladores la capacidad de agregar una capa de integridad de datos a su base de datos. Al crear una directiva de clave única al crear un contenedor, se garantiza la unicidad de uno o más valores por [clave de partición](partition-data.md). Una vez creado un contenedor con una directiva de clave única, impide la creación de cualquier elemento nuevo o actualizado con valores que dupliquen los valores especificados por la restricción de clave única.   

> [!NOTE]
> Las claves únicas se admiten en las versiones más recientes de los SDK de SQL de [.NET](sql-api-sdk-dotnet.md) y [.NET Core](sql-api-sdk-dotnet-core.md) y de la [API de MongoDB](mongodb-feature-support.md#unique-indexes). Table API y API Graph no admiten claves únicas en este momento. 
> 
>

## <a name="use-case"></a>Caso de uso

Como ejemplo, veamos cómo una base de datos de usuario asociada a una [aplicación social](use-cases.md#web-and-mobile-applications) podría beneficiarse de tener una directiva de clave única en las direcciones de correo electrónico. Al hacer que la dirección de correo electrónico del usuario sea una clave única, se asegura de que cada registro tenga una dirección de correo electrónico única y que no se puedan crear nuevos registros con direcciones de correo electrónico duplicadas. 

Si desea que los usuarios puedan crear varios registros con la misma dirección de correo electrónico, pero no con el mismo nombre, apellidos y dirección de correo electrónico, puede añadir otras rutas de acceso a la directiva de claves únicas. Por lo tanto, en lugar de crear una clave única basada simplemente en una dirección de correo electrónico, puede crear una clave única que sea una combinación de nombre, apellido y correo electrónico. En este caso, se permite cada combinación única de las tres rutas de acceso, por lo que la base de datos podría contener elementos que tengan los siguientes valores de ruta de acceso. Cada uno de estos registros aprobaría la directiva de clave única.  

**Valores permitidos para la clave única de nombre, apellidos y correo electrónico**

|Nombre|Apellidos|Dirección de correo electrónico|
|---|---|---|
|Gaby|Duperre|gaby@contoso.com |
|Gaby|Duperre|gaby@fabrikam.com|
|Ivan|Duperre|gaby@fabrikam.com|
|    |Duperre|gaby@fabrikam.com|
|    |       |gaby@fabraikam.com|

Si ha intentado insertar otro registro con cualquiera de las combinaciones enumeradas en la tabla anterior, podría recibir un error que indica que no se ha cumplido la restricción de clave única. El error que Azure Cosmos DB devuelve es "Ya existe un recurso con el id. o nombre especificado". O "Ya existe un recurso con el id., nombre o índice único especificado". 

## <a name="using-unique-keys"></a>Uso de claves únicas

Las claves únicas deben definirse cuando se crea el contenedor y la clave única se explora en la clave de partición. Para crear sobre el ejemplo anterior, si realiza una partición basada en el código postal, podría tener duplicados los registros de la tabla en cada partición.

Los contenedores existentes no se pueden actualizar para utilizar claves únicas.

Cuando se crea un contenedor con una directiva de clave única, esta directiva no se puede cambiar a menos que se vuelva a crear el contenedor. Si tiene datos existentes sobre los que desea implementar claves únicas, cree el nuevo contenedor y utilice después la herramienta de migración de datos apropiada para mover los datos al nuevo contenedor. Para los contenedores de SQL, use la [Herramienta de migración de datos](import-data.md). Para los contenedores de MongoDB, utilice [mongoimport.exe o mongorestore.exe](mongodb-migrate.md).

En cada clave única se puede incluir un máximo de 16 valores de ruta de acceso (por ejemplo /firstName, /lastName, /address/zipCode, etc.). 

Cada directiva de clave única puede tener un máximo de 10 restricciones o combinaciones de clave única. Por lo tanto, el ejemplo anterior que usa el nombre, el apellido y la dirección de correo electrónico es solo una restricción, y utiliza tres de las 16 posibles rutas de acceso disponibles. 

Los cargos por unidad de solicitud para crear, actualizar y eliminar un elemento son ligeramente superiores cuando hay una directiva de clave única en el contenedor. 

No se admiten las claves únicas dispersas. Si faltan valores para algunas rutas de acceso únicas, se tratan como un valor nulo especial, que participa en la restricción de unicidad.

## <a name="sql-api-sample"></a>Ejemplo de API de SQL

En el ejemplo de código siguiente se muestra cómo crear un nuevo contenedor de SQL con dos restricciones de clave única. La primera restricción es la del nombre, apellido y correo electrónico descrita en el ejemplo anterior. La segunda restricción es la dirección y el código postal de los usuarios. Un archivo JSON de ejemplo que utiliza las rutas de acceso en esta directiva de clave única sigue el ejemplo de código. 

```csharp
// Create a collection with two separate UniqueKeys, one compound key for /firstName, /lastName,
// and /email, and another for /address/zipCode.
private static async Task CreateCollectionIfNotExistsAsync(string dataBase, string collection)
{
    try
    {
        await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri(dataBase, collection));
    }
    catch (DocumentClientException e)
    {
        if (e.StatusCode == System.Net.HttpStatusCode.NotFound)
        {
            DocumentCollection myCollection = new DocumentCollection();
            myCollection.Id = collection;
            myCollection.PartitionKey.Paths.Add("/pk");
            myCollection.UniqueKeyPolicy = new UniqueKeyPolicy
            {
                UniqueKeys =
                new Collection<UniqueKey>
                {
                    new UniqueKey { Paths = new Collection<string> { "/firstName" , "/lastName" , "/email" }}
                    new UniqueKey { Paths = new Collection<string> { "/address/zipCode" } },

                }
            };
            await client.CreateDocumentCollectionAsync(
                UriFactory.CreateDatabaseUri(dataBase),
                myCollection,
                new RequestOptions { OfferThroughput = 2500 });
        }
        else
        {
            throw;
        }
    }
```

Documento JSON de ejemplo.

```json
{
    "id": "1",
    "firstName": "Gaby",
    "lastName": "Duperre",
    "email": "gaby@contoso.com",
    "address": [
        {            
            "line1": "100 Some Street",
            "line2": "Unit 1",
            "city": "Seattle",
            "state": "WA",
            "zipCode": 98012
        }
    ],
}
```
## <a name="mongodb-api-sample"></a>Ejemplo de API de MongoDB

El ejemplo de comando siguiente muestra cómo crear un índice único en los campos de nombre, apellido y correo electrónico de la colección de usuarios para la API de MongoDB. Esto garantiza la unicidad de una combinación de los tres campos en todos los documentos de la colección. Para las colecciones de la API de MongoDB, se crea el índice único después de crear la colección, pero antes de rellenarla.

```
db.users.createIndex( { firstName: 1, lastName: 1, email: 1 }, { unique: true } )
```

## <a name="next-steps"></a>pasos siguientes

En este artículo, ha aprendido a crear claves únicas para los elementos de una base de datos. Si está creando un contenedor por primera vez, consulte [Partición y escalado en Azure Cosmos DB](partition-data.md) pues las claves únicas y las claves de partición dependen unas de otras. 


