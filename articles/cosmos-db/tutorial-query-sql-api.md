---
title: "¿Cómo realizar consultas con SQL en Azure Cosmos DB? | Microsoft Docs"
description: "Aprenda cómo realizar consultas con SQL en Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: rafats
manager: jhubbard
editor: 
tags: 
ms.assetid: 
ms.service: cosmos-db
ms.custom: tutorial-develop, mvc
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/10/2017
ms.author: rafats
ms.openlocfilehash: ffef6ec2120a80d907449470efb7b4ab6dca8037
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="azure-cosmos-db-how-to-query-using-sql"></a>Azure Cosmos DB: ¿cómo realizar consultas mediante SQL?

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

La [API de SQL](documentdb-introduction.md) de Azure Cosmos DB admite la consulta de documentos mediante SQL. En este artículo se proporciona un documento de ejemplo y dos consultas SQL de ejemplo y los resultados.

En este artículo se tratan las tareas siguientes: 

> [!div class="checklist"]
> * Consulta de datos con SQL

## <a name="sample-document"></a>Documento de ejemplo

En las consultas SQL de este artículo se usa el documento de ejemplo siguiente.

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```
## <a name="where-can-i-run-sql-queries"></a>¿Dónde puedo ejecutar consultas SQL?

Puede ejecutar consultas mediante el Explorador de datos en Azure Portal, a través de la [API de REST y los SDK](sql-api-sdk-dotnet.md) e incluso el [Query playground](https://www.documentdb.com/sql/demo) (Área de consultas), que ejecuta consultas sobre un conjunto existente de datos de ejemplo.

Para obtener más información sobre las consultas SQL, vea:
* [Consulta SQL y sintaxis SQL](sql-api-sql-query.md)

## <a name="prerequisites"></a>requisitos previos

En este tutorial se da por supuesto que tiene una colección y una cuenta de Azure Cosmos DB. ¿No tiene nada de lo anterior? Complete el [inicio rápido en 5 minutos](create-mongodb-nodejs.md) o el [tutorial de desarrolladores](tutorial-develop-mongodb.md) para crear una cuenta y una colección.

## <a name="example-query-1"></a>Consulta 1 de ejemplo

Dado el documento de familia de ejemplo anterior, la consulta SQL siguiente devuelve los documentos donde el campo Id. coincide con `WakefieldFamily`. Puesto que es una instrucción `SELECT *`, la salida de la consulta es el documento JSON completo:

**Consultar**

    SELECT * 
    FROM Families f 
    WHERE f.id = "WakefieldFamily"

**Resultados**

```json
{
  "id": "WakefieldFamily",
  "parents": [
      { "familyName": "Wakefield", "givenName": "Robin" },
      { "familyName": "Miller", "givenName": "Ben" }
  ],
  "children": [
      {
        "familyName": "Merriam", 
        "givenName": "Jesse", 
        "gender": "female", "grade": 1,
        "pets": [
            { "givenName": "Goofy" },
            { "givenName": "Shadow" }
        ]
      },
      { 
        "familyName": "Miller", 
         "givenName": "Lisa", 
         "gender": "female", 
         "grade": 8 }
  ],
  "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
  "creationDate": 1431620462,
  "isRegistered": false
}
```

## <a name="example-query-2"></a>Consulta 2 de ejemplo

La consulta siguiente devuelve todos los nombres proporcionados de los elementos secundarios de la familia cuyo id. coincida con `WakefieldFamily`, ordenados por su grado.

**Consultar**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.children.grade ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


## <a name="next-steps"></a>pasos siguientes

En este tutorial, ha hecho lo siguiente:

> [!div class="checklist"]
> * Ha obtenido información sobre cómo realizar consultas con SQL  

Ahora puede continuar con el tutorial siguiente para obtener información sobre cómo distribuir sus datos globalmente.

> [!div class="nextstepaction"]
> [Distribución de datos global](tutorial-global-distribution-sql-api.md)

