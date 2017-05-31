---
redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: c1547d9910ce2bbfda79718ba74cdd8e7ca468f5
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017



---
# <a name="automatic-indexing-in-azure-cosmos-db"></a>Indexación automática en Azure Cosmos DB
Este artículo es un extracto del documento ["Indexación independiente del esquema con Azure Cosmos DB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) que se presentará en la [Edición nº 41 de la Conferencia interna sobre bases de datos de gran tamaño](http://www.vldb.org/2015/) a celebrar entre el 31 de agosto y el 4 de septiembre de 2015 y es una introducción a la forma en la que funciona la indexación en Azure Cosmos DB. 

Después de leerlo, podrá responder a las siguientes preguntas:

* ¿Cómo deduce Azure Cosmos DB el esquema a partir de un documento JSON?
* ¿Cómo genera Azure Cosmos DB un índice en documentos dispares?
* ¿Cómo realiza Azure Cosmos DB la indexación automática a escala?

## <a id="HowDocumentDBIndexingWorks"></a> Funcionamiento de la indexación de DocumentDB
[Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/) es una base de datos verdaderamente libre de esquemas creada específicamente para JSON. No espera ni requiere definiciones de esquema o de índice secundario para indexar los datos a escala. Esto permite definir e iterar rápidamente en modelos de datos de aplicación con Azure Cosmos DB. A medida que se agregan documentos a una colección, Azure Cosmos DB indexará automáticamente las propiedades de los documentos para que estén disponibles para su consulta. La indexación automática permite almacenar documentos que pertenezcan a esquemas completamente arbitrarios sin preocuparse por esquemas o índices secundarios.

Con el objetivo de eliminar la discordancia de impedancia entre la base de datos y los modelos de programación de aplicaciones, Azure Cosmos DB aprovecha la sencillez de JSON y su falta de una especificación de esquema. No hace suposiciones sobre los documentos y permite que los documentos comprendidos en una colección de Azure Cosmos DB varíen de esquema, además de los valores específicos de la instancia. A diferencia de otras bases de datos documentales, el motor de base de datos de Azure Cosmos DB funciona directamente en el nivel de gramática JSON, que se mantiene independiente con respecto al concepto de un esquema de documento y desdibuja el límite entre los valores de instancia y de estructura de documentos. Lo que, a su vez, le permite indexar automáticamente documentos sin que sea necesario usar un esquema ni índices secundarios.

La indexación en Azure Cosmos DB aprovecha el hecho de que la gramática de JSON permite que los documentos se **representen en forma de árboles**. Para representar un documento JSON como un árbol, es necesario crear un nodo raíz ficticio que actúe como elemento primario para el resto de nodos reales del documento que dependen de dicho nodo ficticio. Cada etiqueta, que incluye los índices de la matriz en un documento JSON, se convierte en un nodo del árbol. La ilustración siguiente muestra un ejemplo de documento JSON y de su representación de árbol correspondiente.

> [!NOTE]
> Dado que JSON es autodescriptivo, es decir, cada documento incluye el esquema (metadatos) y los datos, por ejemplo, `{"locationId": 5, "city": "Moscow"}`, revela que hay dos propiedades `locationId` y `city`, y que tienen valores de propiedad de cadena y numéricos. Azure Cosmos DB puede deducir el esquema de los documentos e indexarlos cuando se insertan o reemplazan, sin que se tenga que definir ningún esquema ni índice secundario.
> 
> 

**Documentos JSON en forma de árboles:**

![Documentos en forma de árboles](media/documentdb-indexing/DocumentsAsTrees.png)

Por ejemplo, en el caso anterior:

* La propiedad JSON `{"headquarters": "Belgium"}` en el ejemplo anterior corresponde a la ruta de acceso /headquarters/Belgium.
* La matriz JSON `{"exports": [{"city": “Moscow"}`, `{"city": Athens"}]}` corresponde a las rutas de acceso `/exports/[]/city/Moscow` y `/exports/[]/city/Athens`.

Con indexación automática, (1) se indexa cada ruta de acceso de un árbol de documentos (a menos que el desarrollador configure explícitamente la directiva de indexación para que excluya determinados patrones de ruta de acceso). (2) Cada actualización de un documento en una colección de Azure Cosmos DB lleva a la actualización de la estructura del índice (es decir, hace que se agreguen o quiten nodos). Uno de los requisitos principales de la indexación automática de documentos es asegurarse de que el costo para indexar y consultar un documento con una estructura profundamente anidada (por ejemplo, 10 niveles) es el mismo que el de un documento sin formato de JSON que consta de pares clave-valor de un solo nivel de profundidad. Por consiguiente, una representación normalizada de la ruta de acceso es la base sobre la que se crean los dos subsistemas de consulta e indexación automática.

Una importante implicación de tratar los valores de esquema y de instancia uniformemente en cuanto a las rutas de acceso es que, lógicamente, igual que los documentos individuales, también se puede representar en forma de árbol un índice de los dos documentos mostrados que mantenga una asignación entre las rutas de acceso y los identificadores de documento que contienen dicha ruta de acceso. Azure Cosmos DB usa este hecho para generar un árbol de índice que se crea a partir de la unión de todos los árboles que representan documentos individuales dentro de la colección. El árbol de índice de las colecciones de Azure Cosmos DB aumenta con el tiempo a medida que se agregan nuevos documentos a la colección o se actualizan los existentes.

**Indexar Azure Cosmos DB como un árbol:**

![Índice en forma de árbol](media/documentdb-indexing/IndexAsTree.png)

A pesar de la ausencia de esquema, los lenguajes de consulta JavaScript y SQL de Azure Cosmos DB proporcionan filtros y proyecciones relacionales, navegación jerárquica entre documentos, operaciones espaciales e invocación de UDF escritos completamente en JavaScript. El tiempo de ejecución de consulta de Azure Cosmos DB tiene capacidad para admitir estas consultas, ya que funciona directamente con esta representación de árbol de índice de los datos.

La directiva de predeterminada de indexación indexa automáticamente todas las propiedades de todos los documentos y proporciona consultas coherentes (es decir, el índice se actualiza de forma sincrónica con la escritura de documento). ¿Cómo admite Azure Cosmos DB actualizaciones coherentes en el árbol de índice a escala? Azure Cosmos DB emplea técnicas de escritura optimizada, sin bloqueos, y de mantenimiento del índice estructurado de registros. Esto se traduce en que Azure Cosmos DB admite un volumen sostenido de escrituras rápidas mientras se sigue atendiendo consultas coherentes. 

La indexación de Azure Cosmos DB está diseñada en función de la eficacia de almacenamiento y el control multiinquilino. Para obtener rentabilidad, la sobrecarga de almacenamiento en el disco del índice es baja y predecible. Las actualizaciones del índice también se realizan dentro del presupuesto de recursos de sistema asignado por colección de Azure Cosmos DB.

## <a name="NextSteps"></a> Pasos siguientes
* Descargue ["Indexación independiente del esquema con Azure Cosmos DB"](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf)que se presentará en la Edición nº 41 de la Conferencia interna sobre bases de datos de gran tamaño, a celebrar entre el 31 de agosto y el 4 de septiembre de 2015.
* [Consulta con SQL de Base de datos de documentos](documentdb-sql-query.md)
* Más información sobre cómo personalizar el índice de Azure Cosmos DB [aquí](documentdb-indexing-policies.md)


