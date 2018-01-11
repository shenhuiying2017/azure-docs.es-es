---
title: Modelado de tipos de datos complejos en Azure Search | Microsoft Docs
description: "Las estructuras de datos jerárquicas o anidadas se pueden modelar en un índice de Azure Search mediante conjuntos de filas aplanados y el tipo de datos Collections."
services: search
documentationcenter: 
author: LiamCa
manager: pablocas
editor: 
tags: complex data types; compound data types; aggregate data types
ms.assetid: e4bf86b4-497a-4179-b09f-c1b56c3c0bb2
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: liamca
ms.openlocfilehash: d7a7400fe7470439dfa957f1ddb463e0a7f1a271
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="how-to-model-complex-data-types-in-azure-search"></a>Modelado de tipos de datos complejos en Azure Search
Los conjuntos de datos externos usados para rellenar un índice de Azure Search a veces incluyen subestructuras jerárquicas o anidadas que no se dividen perfectamente en un conjunto de filas tabular. Algunos ejemplos de estas estructuras son varias ubicaciones y números de teléfono de un solo cliente, varios tamaños y colores de un único SKU, varios autores de un único libro, etc. En términos de modelado, puede que vea referirse a *estas estructuras* como *tipos de datos complejos*, *tipos de datos compuestos* o *tipos de datos agregados*, por nombrar algunos.

Los tipos de datos complejos no se admiten de forma nativa en Azure Search, pero existe una solución probada que consiste en seguir un proceso en dos pasos para aplanar la estructura y, a continuación, utilizar un tipo de datos **Collection** para reconstituir la estructura interior. La técnica descrita en este artículo permite buscar, buscar con facetas, filtrar y ordenar el contenido.

## <a name="example-of-a-complex-data-structure"></a>Ejemplo de una estructura de datos complejos
Normalmente, los datos en cuestión residen como un conjunto de documentos JSON o XML o como elementos en un almacén de NoSQL, por ejemplo, Azure Cosmos DB. Estructuralmente, la dificultad radica en tener varios elementos secundarios que se deben buscar y filtrar.  Como punto de partida para ilustrar la solución alternativa, tome como ejemplo el siguiente documento JSON que enumera un conjunto de contactos:

~~~~~
[
  {
    "id": "1",
    "name": "John Smith",
    "company": "Adventureworks",
    "locations": [
      {
        "id": "1",
        "description": "Adventureworks Headquarters"
      },
      {
        "id": "2",
        "description": "Home Office"
      }
    ]
  }, 
  {
    "id": "2",
    "name": "Jen Campbell",
    "company": "Northwind",
    "locations": [
      {
        "id": "3",
        "description": "Northwind Headquarter"
      },
      {
        "id": "4",
        "description": "Home Office"
      }
    ]
}]
~~~~~

Mientras que los campos 'id', 'name' y 'company' se pueden asignar fácilmente uno a uno como campos en un índice de Azure Search, el campo 'locations' contiene una matriz de ubicaciones, con un conjunto de identificadores de ubicación así como descripciones de ubicación. Como Azure Search no tiene un tipo de datos que admita esto, necesitamos una manera diferente de modelarlo Azure Search. 

> [!NOTE]
> En su entrada de blog [Indexing Azure Cosmos DB with Azure Search](https://blogs.msdn.microsoft.com/kaevans/2015/03/09/indexing-documentdb-with-azure-seach/) (Indexación de Azure Cosmos DB con Azure Search), Kirk Evans muestra una técnica llamada "aplanamiento de los datos", según la cual tendría dos campos llamados `locationsID` y `locationsDescription`, ambos [collections](https://msdn.microsoft.com/library/azure/dn798938.aspx) (o una matriz de cadenas).   
> 
> 

## <a name="part-1-flatten-the-array-into-individual-fields"></a>Parte 1: Aplanar la matriz en campos individuales
Para crear un índice de Azure Search que dé cabida a este conjunto de datos, cree campos individuales para la subestructura anidada: `locationsID` y `locationsDescription` con un tipo de datos [collection](https://msdn.microsoft.com/library/azure/dn798938.aspx) (o una matriz de cadenas). En estos campos, los valores '1' y '2' se indizan en el campo `locationsID` para John Smith y los valores '3' y '4' en el campo `locationsID` para Jen Campbell.  

Los datos en Azure Search tendrían el siguiente aspecto: 

![datos de ejemplo, 2 filas](./media/search-howto-complex-data-types/sample-data.png)

## <a name="part-2-add-a-collection-field-in-the-index-definition"></a>Parte 2: Agregar un campo de colección en la definición del índice
En el esquema de índice, las definiciones de campo podrían ser similares a las de este ejemplo.

~~~~
var index = new Index()
{
    Name = indexName,
    Fields = new[]
    {
        new Field("id", DataType.String) { IsKey = true },
        new Field("name", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("company", DataType.String) { IsSearchable = true, IsFilterable = false, IsSortable = false, IsFacetable = false },
        new Field("locationsId", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true },
        new Field("locationsDescription", DataType.Collection(DataType.String)) { IsSearchable = true, IsFilterable = true, IsFacetable = true }
    }
};
~~~~

## <a name="validate-search-behaviors-and-optionally-extend-the-index"></a>Validación de los comportamientos de búsqueda y extensión opcional del índice
Suponiendo que ha creado el índice y cargado los datos, ahora puede probar la solución para comprobar la ejecución de la consulta de búsqueda en el conjunto de datos. Cada campo **collection** debería tener las propiedades **searchable**, **filterable** y **facetable**. Debe poder ejecutar consultas como estas:

* Buscar todas las personas que trabajan en ‘Adventureworks Headquarters’.
* Obtener el número de personas que trabajan en ‘Home Office’.  
* De las personas que trabajan en ‘Home Office’, mostrar con qué otras oficinas trabajan, además del número de personas en cada ubicación.  

Esta técnica no funciona correctamente cuando se necesita realizar una búsqueda que combina el identificador de ubicación y la descripción de la ubicación. Por ejemplo:

* Buscar todas las personas cuya ubicación es Home Office y su identificador de ubicación es 4.  

Si recuerda, el contenido original tenía el siguiente aspecto:

~~~~
   {
        id: '4',
        description: 'Home Office'
   }
~~~~

Sin embargo, ahora que hemos separado los datos en campos independientes, no hay ninguna manera de saber si la ubicación Home Office de Jen Campbell está relacionada con `locationsID 3` o `locationsID 4`.  

En este caso, defina otro campo en el índice que combine todos los datos en una sola colección.  En nuestro ejemplo, llamaremos a este campo `locationsCombined` y separaremos el contenido con un `||`, aunque puede elegir cualquier separador que piense que sería un conjunto único de caracteres para su contenido. Por ejemplo: 

![datos de ejemplo, 2 filas con separador](./media/search-howto-complex-data-types/sample-data-2.png)

Con este campo `locationsCombined` , ahora podemos dar cabida a más consultas, por ejemplo:

* Mostrar el número de personas que trabajan en la ubicación 'Home Office' con un identificador de ubicación '4'.  
* Buscar las personas que trabajan en la ubicación 'Home Office' con un identificador de ubicación '4'. 

## <a name="limitations"></a>Limitaciones
Esta técnica es útil para diversos escenarios, pero no es aplicable en todos los casos.  Por ejemplo:

1. Si no tiene un conjunto de campos estático en el tipo de datos complejos y no hay manera de asignar todos los tipos posibles a un único campo. 
2. Actualizar los objetos anidados requiere trabajo adicional para determinar exactamente lo que hay que actualizar en el índice de Azure Search.

## <a name="sample-code"></a>Código de ejemplo
En este [repositorio de GitHub](https://github.com/liamca/AzureSearchComplexTypes)puede ver un ejemplo de cómo indizar un conjunto de datos JSON complejo en Azure Search y realizar diversas consultas sobre este conjunto de datos.

## <a name="next-step"></a>Paso siguiente
[Vote a favor de la compatibilidad nativa con tipos de datos complejos](https://feedback.azure.com/forums/263029-azure-search) en la página UserVoice de Azure Search y díganos qué le gustaría que tuviéramos en cuenta para la implementación de la característica. Puede ponerse en contacto conmigo directamente en Twitter en @liamca.

