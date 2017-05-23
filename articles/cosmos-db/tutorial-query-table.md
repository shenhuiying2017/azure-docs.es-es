---
title: "¿Cómo consultar datos de tabla en Azure Cosmos DB? | Microsoft Docs"
description: Aprender a consultar datos de tabla en Azure Cosmos DB
services: cosmosdb
documentationcenter: 
author: kanshiG
manager: jhubbard
editor: 
tags: 
ms.assetid: 14bcb94e-583c-46f7-9ea8-db010eb2ab43
ms.service: cosmosdb
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: govindk
ms.translationtype: Human Translation
ms.sourcegitcommit: 5e92b1b234e4ceea5e0dd5d09ab3203c4a86f633
ms.openlocfilehash: cdd855aeac7dd30c52accb407289ca6db7dab4ae
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---

# <a name="azure-cosmos-db-how-to-query-with-the-table-api-preview"></a>Azure Cosmos DB: ¿Cómo consultar con Table API (versión preliminar)?

[Table API](table-introduction.md) (versión preliminar) de Azure Cosmos DB admite consultas de OData y [LINQ](https://docs.microsoft.com/rest/api/storageservices/fileservices/writing-linq-queries-against-the-table-service) en los datos de clave-valor (tabla).  

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Consulta de datos con Table API

## <a name="sample-table"></a>Tabla de ejemplo

En las consultas de este artículo se usa la tabla `People` de ejemplo siguiente:

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0101 |
| Smith | Walter | Ben@contoso.com| 425-555-0102 |
| Smith | Jeff | Jeff@contoso.com| 425-555-0104 | 

## <a name="about-the-table-api-preview"></a>Acerca de Table API (versión preliminar)

Puesto que Azure Cosmos DB es compatible con las API de Azure Table Storage, consulte [Consulta de tablas y entidades](https://docs.microsoft.com/rest/api/storageservices/fileservices/querying-tables-and-entities) para detalles sobre cómo consultar con Table API. 

Para más información sobre las funcionalidades premium que ofrece Azure Cosmos DB, consulte [Azure Cosmos DB: Table API](table-introduction.md) (Azure Cosmos DB: Table API) y [Develop with the Table API using .NET](tutorial-develop-table-dotnet.md) (Desarrollo con Table API mediante .NET). 

## <a name="prerequisites"></a>Requisitos previos

Para que estas consultas funcionen, debe tener una cuenta de Azure Cosmos DB, así como datos de la entidad en el contenedor. ¿No tiene nada de lo anterior? Complete el [inicio rápido en 5 minutos](https://aka.ms/acdbtnetqs) o el [tutorial de desarrolladores](https://aka.ms/acdbtabletut) para crear una cuenta y rellenar la base de datos.

## <a name="querying-on-partition-key-and-row-key"></a>Consultas en la clave de partición y clave de fila
Dado que las propiedades PartitionKey y RowKey forman la clave principal de una entidad, puede usar una sintaxis especial para identificar dicha entidad, como se indica a continuación: 

**Consultar**

```
https://<mytableendpoint>/People(PartitionKey='Harp',RowKey='Walter')  
```
**Resultados**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Harp | Walter | Walter@contoso.com| 425-555-0104 |

Como alternativa, puede especificar estas propiedades como parte de la opción `$filter`, tal y como se muestra en la sección siguiente. Tenga en cuenta que los nombres de propiedad de clave y los valores constantes distinguen entre mayúsculas y minúsculas. Las propiedades PartitionKey y RowKey son de tipo String. 

## <a name="querying-with-an-odata-filter"></a>Consultas con un filtro de ODATA
Al construir una cadena de filtro, tenga en cuenta estas reglas: 

* Utilice los operadores lógicos definidos por la especificación del protocolo OData para comparar una propiedad con un valor. Tenga en cuenta que no es posible comparar una propiedad con un valor dinámico; uno de los lados de la expresión debe ser una constante. 
* El nombre de la propiedad, el operador y el valor constante se deben separar por espacios con codificación URL. Para codificar un espacio con codificación URL se usa `%20`. 
* Todas las partes de la cadena de filtro distinguen mayúsculas de minúsculas. 
* Para que el filtro devuelva resultados válidos, el valor constante debe ser del mismo tipo de datos que la propiedad. Para obtener más información sobre los tipos de propiedades que se admiten, consulte [Introducción al modelo de datos del servicio Tabla](https://docs.microsoft.com/rest/api/storageservices/understanding-the-table-service-data-model). 

Esta es una consulta de ejemplo que muestra cómo filtrar por PartitionKey y la propiedad Email con `$filter` de ODATA.

**Consultar**

```
https://<mytableapi-endpoint>/People()?$filter=PartitionKey%20eq%20'Smith'%20and%20Email%20eq%20'Ben@contoso.com'
```

En [Querying Tables and Entities](https://docs.microsoft.com/rest/api/storageservices/querying-tables-and-entities) (Consulta de tablas y entidades) puede obtener más detalles sobre cómo construir expresiones de filtro para diversos tipos de datos.

**Resultados**

| PartitionKey | RowKey | Email | PhoneNumber |
| --- | --- | --- | --- |
| Ben |Smith | Ben@contoso.com| 425-555-0102 |

## <a name="querying-with-linq"></a>Consultas con LINQ 
También puede consultar con LINQ, que traduce a las expresiones de consulta de ODATA correspondientes. Este es un ejemplo de cómo crear consultas mediante el SDK. de NET.

```csharp
CloudTableClient tableClient = account.CreateCloudTableClient();
CloudTable table = tableClient.GetTableReference("people");

TableQuery<CustomerEntity> query = new TableQuery<CustomerEntity>()
    .Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition(PartitionKey, QueryComparisons.Equal, "Smith"),
            TableOperators.And,
            TableQuery.GenerateFilterCondition(Email, QueryComparisons.Equal,"Ben@contoso.com")
    ));

await table.ExecuteQuerySegmentedAsync<CustomerEntity>(query, null);
```

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Aprendió a consultar mediante Table API (versión preliminar) 

Ahora puede continuar con el tutorial siguiente para obtener información sobre cómo distribuir sus datos globalmente.

> [!div class="nextstepaction"]
> [Distribución de datos global](tutorial-global-distribution-documentdb.md)

