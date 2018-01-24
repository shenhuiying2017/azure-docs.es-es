---
title: Compatibilidad de Azure Cosmos DB con Gremlin | Microsoft Docs
description: "Más información sobre el lenguaje Gremlin de Apache TinkerPop y sobre las características y pasos que están disponibles en Azure Cosmos DB"
services: cosmos-db
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: 
tags: 
ms.assetid: 6016ccba-0fb9-4218-892e-8f32a1bcc590
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 01/02/2018
ms.author: lbosq
ms.openlocfilehash: 59d926f54c8dfc2991929f2eb42b20056e3a09c3
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="azure-cosmos-db-gremlin-graph-support"></a>Compatibilidad de Azure Cosmos DB con grafos Gremlin
Azure Cosmos DB admite el lenguaje de recorrido de grafos de [Apache Tinkerpop](http://tinkerpop.apache.org), [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), que es una API Graph para crear entidades de grafo y realizar operaciones de consulta de grafos. Puede usar el lenguaje Gremlin para crear entidades de grafo (vértices y aristas), modificar las propiedades de las entidades, realizar consultas y recorridos, y eliminar entidades. 

Azure Cosmos DB aporta características empresariales a las bases de datos de grafos, Esto incluye distribución global, escalado independiente del almacenamiento y el rendimiento, latencias predecibles inferiores a 10 milisegundos, indexación automática, SLA y disponibilidad de lectura para cuentas de base de datos que abarcan dos o más regiones de Azure. Dado que Azure Cosmos DB admite TinkerPop/Gremlin, puede migrar fácilmente aplicaciones escritas con otra base de datos de grafos sin tener que hacer cambios en el código. Además, gracias a la compatibilidad con Gremlin, Azure Cosmos DB se integra perfectamente entornos de análisis habilitados para TinkerPop, tales como [Apache Spark GraphX](http://spark.apache.org/graphx/). 

En este artículo, se proporciona un tutorial rápido de Gremlin y se enumeran las características y los pasos de Gremlin que se admiten en Graph API.

## <a name="gremlin-by-example"></a>Gremlin con ejemplos
Vamos a usar un grafo de ejemplo para entender cómo se expresan las consultas en Gremlin. La siguiente ilustración muestra una aplicación empresarial que administra datos de usuarios, intereses y dispositivos en forma de grafo.  

![Base de datos de ejemplo que muestra personas, dispositivos e intereses](./media/gremlin-support/sample-graph.png) 

Este grafo tiene los siguientes tipos de vértices (llamados "etiquetas" en Gremlin):

- Personas: el grafo tiene tres personas, Robin, Thomas y Ben
- Intereses: sus intereses; en este ejemplo, el fútbol
- Dispositivos: los dispositivos que las personas usan
- Sistemas operativos: los sistemas operativos que se ejecutan en los dispositivos

Las relaciones entre estas entidades se representan con las siguientes etiquetas o tipos de arista:

- Conoce a: por ejemplo, "Thomas conoce a Robin"
- Tiene interés en: representa los intereses de las personas de nuestro grafo; por ejemplo, "Ben tiene interés en Fútbol"
- EjecutaSO: Portátil ejecuta el sistema operativo Windows
- Usa: representa el dispositivo que una persona usa. Por ejemplo, Robin usa un teléfono Motorola con número de serie 77.

Vamos a ejecutar algunas operaciones en este grafo mediante la [consola de Gremlin](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console). También puede realizar estas operaciones mediante controladores de Gremlin en la plataforma que elija (Java, Node.js, Python o .NET).  Antes de adentrarnos en lo que se admite en Azure Cosmos DB, veamos algunos ejemplos para familiarizarnos con la sintaxis.

Primero, echemos un vistazo a CRUD. La siguiente instrucción de Gremlin inserta el vértice "Thomas" en el grafo:

```
:> g.addV('person').property('id', 'thomas.1').property('firstName', 'Thomas').property('lastName', 'Andersen').property('age', 44)
```

La siguiente instrucción de Gremlin inserta un arista "conoce a" entre Thomas y Robin.

```
:> g.V('thomas.1').addE('knows').to(g.V('robin.1'))
```

La consulta siguiente devuelve los vértices "persona" en orden descendente de sus nombres:
```
:> g.V().hasLabel('person').order().by('firstName', decr)
```

En el lugar donde brillan los grafos es donde se necesita responder preguntas como "¿Qué sistemas operativos usan los amigos de Thomas?". Puede ejecutar este recorrido de Gremlin simple para obtener esa información en el grafo:

```
:> g.V('thomas.1').out('knows').out('uses').out('runsos').group().by('name').by(count())
```
Ahora, veamos qué proporciona Azure Cosmos DB a los desarrolladores de Gremlin.

## <a name="gremlin-features"></a>Características de Gremlin
TinkerPop es una norma que abarca una amplia gama de tecnologías de grafos. Por lo tanto, usa una terminología estándar para describir qué características proporciona un proveedor de grafos. Azure Cosmos DB proporciona una base de datos de grafos de escritura, persistente y de alta simultaneidad que se puede dividir entre varios servidores o clústeres. 

En la tabla siguiente se enumeran las características de TinkerPop implementadas por Azure Cosmos DB: 

| Categoría | Implementación de Azure Cosmos DB |  Notas | 
| --- | --- | --- |
| Características de grafos | Proporciona persistencia y acceso simultáneo. Diseñado para admitir transacciones | Los métodos de proceso se pueden implementar mediante el conector de Spark. |
| Características de variables | Admite valores Boolean, Integer, Byte, Double, Float, Integer, Long, String. | Admite tipos primitivos; es compatible con tipos complejos mediante el modelo de datos. |
| Características de vértices | Admite RemoveVertices, MetaProperties, AddVertices, MultiProperties, StringIds, UserSuppliedIds, AddProperty, RemoveProperty.  | Permite crear, modificar y eliminar vértices. |
| Características de propiedades de vértices | StringIds, UserSuppliedIds, AddProperty, RemoveProperty, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Permite crear, modificar y eliminar propiedades de vértices. |
| Características de aristas | AddEges, RemoveEdges, StringIds, UserSuppliedIds, AddProperty, RemoveProperty | Permite crear, modificar y eliminar aristas. |
| Características de propiedades de aristas | Properties, BooleanValues, ByteValues, DoubleValues, FloatValues, IntegerValues, LongValues, StringValues | Permite crear, modificar y eliminar propiedades de aristas. |

## <a name="gremlin-wire-format-graphson"></a>Formato de alambre de Gremlin: GraphSON

Azure Cosmos DB usa el [formato GraphSON](https://github.com/thinkaurelius/faunus/wiki/GraphSON-Format) al devolver los resultados de las operaciones de Gremlin. GraphSON es el formato estándar de Gremlin para representar los vértices, las aristas y las propiedades (propiedades con uno o varios valores) mediante JSON. 

Por ejemplo, el fragmento de código siguiente muestra una representación de GraphSON de un vértice *devuelto al cliente* desde Azure Cosmos DB. 

```json
  {
    "id": "a7111ba7-0ea1-43c9-b6b2-efc5e3aea4c0",
    "label": "person",
    "type": "vertex",
    "outE": {
      "knows": [
        {
          "id": "3ee53a60-c561-4c5e-9a9f-9c7924bc9aef",
          "inV": "04779300-1c8e-489d-9493-50fd1325a658"
        },
        {
          "id": "21984248-ee9e-43a8-a7f6-30642bc14609",
          "inV": "a8e3e741-2ef7-4c01-b7c8-199f8e43e3bc"
        }
      ]
    },
    "properties": {
      "firstName": [
        {
          "value": "Thomas"
        }
      ],
      "lastName": [
        {
          "value": "Andersen"
        }
      ],
      "age": [
        {
          "value": 45
        }
      ]
    }
  }
```

Las propiedades utilizadas por GraphSON para los vértices son las siguientes:

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| id | Identificador del vértice. Debe ser único (en combinación con el valor de _partition, si corresponde). |
| label | Etiqueta del vértice. Es opcional y se utiliza para describir el tipo de entidad. |
| Tipo | Se usa para distinguir los vértices de los documentos que no son grafos. |
| propiedades | Contenedor de propiedades definidas por el usuario asociadas con el vértice. Cada propiedad puede tener varios valores. |
| _partition (configurable) | Clave de partición del vértice. Se puede usar para escalar horizontalmente grafos en varios servidores. |
| outE | Contiene una lista de las aristas de un vértice. Almacenar la información de proximidad con los vértices permite ejecutar recorridos rápidamente. Las aristas se agrupan en función de sus etiquetas. |

La arista contiene la siguiente información para ayudar a la navegación a otras partes del grafo.

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| id | Identificador de la arista. Debe ser único (en combinación con el valor de _partition, si corresponde). |
| label | Etiqueta de la arista. Esta propiedad es opcional y se usa para describir el tipo de relación. |
| inV | Contiene una lista de vértices para una línea. Almacenar la información de proximidad con la línea permite ejecutar recorridos rápidamente. Los vértices se agrupan en función de sus etiquetas. |
| propiedades | Contenedor de propiedades definidas por el usuario asociadas con la arista. Cada propiedad puede tener varios valores. |

Cada propiedad puede almacenar varios valores dentro de una matriz. 

| Propiedad | DESCRIPCIÓN |
| --- | --- |
| value | Valor de la propiedad.

## <a name="gremlin-partitioning"></a>Particiones de Gremlin

En Azure Cosmos DB, los grafos se almacenan en contenedores que se pueden escalar independientemente en cuanto a almacenamiento y capacidad de proceso (expresada en solicitudes normalizadas por segundo). Cada contenedor debe definir una propiedad de clave de partición opcional, aunque recomendada, que determina el límite de una partición lógica para los datos relacionados. Cada vértice o arista debe tener una propiedad `id` que es única para las entidades dentro de ese valor de clave de partición. Los detalles se tratan en [Particiones en Azure Cosmos DB](partition-data.md).

Las operaciones de Gremlin funcionan perfectamente en los datos de grafos repartidos en varias particiones en Azure Cosmos DB. Sin embargo, para los grafos, se recomienda elegir una clave de partición que se use normalmente como filtro en las consultas, que tenga muchos valores distintos y una frecuencia similar acceso a estos valores. 

## <a name="gremlin-steps"></a>Pasos de Gremlin
Ahora, echemos un vistazo a los pasos de Gremlin que Azure Cosmos DB admite. Para una referencia completa de Gremlin, consulte la [referencia de TinkerPop](http://tinkerpop.apache.org/docs/current/reference).

| paso | DESCRIPCIÓN | Documentación de TinkerPop 3.2. | Notas |
| --- | --- | --- | --- |
| `addE` | Agrega una arista entre dos vértices. | [Paso addE](http://tinkerpop.apache.org/docs/current/reference/#addedge-step) | |
| `addV` | Agrega un vértice al grafo. | [Paso addV](http://tinkerpop.apache.org/docs/current/reference/#addvertex-step) | |
| `and` | Garantiza que todos los recorridos devuelven un valor. | [y un paso](http://tinkerpop.apache.org/docs/current/reference/#and-step). | |
| `as` | Modulador de pasos para asignar una variable a la salida de un paso. | [paso as](http://tinkerpop.apache.org/docs/current/reference/#as-step) | |
| `by` | Modulador de pasos que se usa con `group` y `order`. | [paso by](http://tinkerpop.apache.org/docs/current/reference/#by-step) | |
| `coalesce` | Devuelve el primer recorrido que devuelve un resultado. | [paso coalesce](http://tinkerpop.apache.org/docs/current/reference/#coalesce-step) | |
| `constant` | Devuelve un valor constante. Se usa con `coalesce`.| [paso constant](http://tinkerpop.apache.org/docs/current/reference/#constant-step) | |
| `count` | Devuelve el número del recorrido. | [paso count](http://tinkerpop.apache.org/docs/current/reference/#count-step) | |
| `dedup` | Devuelve los valores sin duplicados. | [paso dedup](http://tinkerpop.apache.org/docs/current/reference/#dedup-step) | |
| `drop` | Quita los valores (vértice/arista). | [paso drop](http://tinkerpop.apache.org/docs/current/reference/#drop-step) | |
| `fold` | Actúa como una barrera que calcula el agregado de los resultados.| [paso fold](http://tinkerpop.apache.org/docs/current/reference/#fold-step) | |
| `group` | Agrupa los valores en función de las etiquetas especificadas.| [paso group](http://tinkerpop.apache.org/docs/current/reference/#group-step) | |
| `has` | Se utiliza para filtrar las propiedades, los vértices y las aristas. Admite las variantes `hasLabel`, `hasId`, `hasNot` y `has`. | [paso has](http://tinkerpop.apache.org/docs/current/reference/#has-step) | |
| `inject` | Inserta valores en un flujo.| [paso inject](http://tinkerpop.apache.org/docs/current/reference/#inject-step) | |
| `is` | Se usa para filtrar mediante una expresión booleana. | [paso is](http://tinkerpop.apache.org/docs/current/reference/#is-step) | |
| `limit` | Se usa para limitar el número de elementos en el recorrido.| [paso limit](http://tinkerpop.apache.org/docs/current/reference/#limit-step) | |
| `local` | Encapsula localmente una sección de un recorrido, de forma similar a una subconsulta. | [paso local](http://tinkerpop.apache.org/docs/current/reference/#local-step) | |
| `not` | Se usa para generar la negación de un filtro. | [paso not](http://tinkerpop.apache.org/docs/current/reference/#not-step) | |
| `optional` | Devuelve el resultado del recorrido especificado si produce un resultado; en caso contrario, devuelve el elemento que realiza la llamada. | [paso optional](http://tinkerpop.apache.org/docs/current/reference/#optional-step) | |
| `or` | Garantiza que al menos uno de los recorridos devuelve un valor. | [paso or](http://tinkerpop.apache.org/docs/current/reference/#or-step) | |
| `order` | Muestra los resultados con el criterio de ordenación especificado. | [paso order](http://tinkerpop.apache.org/docs/current/reference/#order-step) | |
| `path` | Devuelve la ruta de acceso completa del recorrido. | [paso path](http://tinkerpop.apache.org/docs/current/reference/#path-step) | |
| `project` | Proyecta las propiedades como un mapa. | [paso project](http://tinkerpop.apache.org/docs/current/reference/#project-step) | |
| `properties` | Devuelve las propiedades de las etiquetas especificadas. | [paso properties](http://tinkerpop.apache.org/docs/current/reference/#properties-step) | |
| `range` | Filtra el intervalo de valores especificado.| [paso range](http://tinkerpop.apache.org/docs/current/reference/#range-step) | |
| `repeat` | Repite el paso el número de veces especificado. Se usa para crear bucles. | [paso repeat](http://tinkerpop.apache.org/docs/current/reference/#repeat-step) | |
| `sample` | Se usa para tomar muestras de datos del recorrido. | [paso sample](http://tinkerpop.apache.org/docs/current/reference/#sample-step) | |
| `select` | Se usa para proyectar los resultados del recorrido. |  [paso select](http://tinkerpop.apache.org/docs/current/reference/#select-step) | |
| `store` | Se utiliza para realizar agregados sin bloqueo del recorrido. | [paso store](http://tinkerpop.apache.org/docs/current/reference/#store-step) | |
| `tree` | Agrega las rutas de acceso desde un vértice en un árbol. | [paso tree](http://tinkerpop.apache.org/docs/current/reference/#tree-step) | |
| `unfold` | Extrae un iterador como un paso.| [paso unfold](http://tinkerpop.apache.org/docs/current/reference/#unfold-step) | |
| `union` | Combina los resultados de varios recorridos.| [paso union](http://tinkerpop.apache.org/docs/current/reference/#union-step) | |
| `V` | Incluye los pasos necesarios para los recorridos entre los vértices y las aristas `V`, `E`, `out`, `in`, `both`, `outE`, `inE`, `bothE`, `outV`, `inV`, `bothV` y `otherV` | [pasos vertex](http://tinkerpop.apache.org/docs/current/reference/#vertex-steps) | |
| `where` | Se usa para filtrar los resultados del recorrido. Admite los operadores `eq`, `neq`, `lt`, `lte`, `gt`, `gte` y `between`  | [paso where](http://tinkerpop.apache.org/docs/current/reference/#where-step) | |

De forma predeterminada, el motor optimizado para escritura de Azure Cosmos DB admite la indexación automática de todas las propiedades dentro de vértices y aristas. Por lo tanto, las consultas con filtros, las consultas de intervalo, la ordenación o los agregados de cualquier propiedad se procesan a partir del índice y se sirven de forma eficiente. Para más información sobre cómo funciona la indexación en Azure Cosmos DB, consulte nuestro artículo sobre la [indexación independiente del esquema](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf).

## <a name="next-steps"></a>Pasos siguientes
* Empezar a compilar una aplicación de grafos [con nuestros SDK](create-graph-dotnet.md) 
* Más información sobre la [compatibilidad de Azure Cosmos DB con grafos](graph-introduction.md)
