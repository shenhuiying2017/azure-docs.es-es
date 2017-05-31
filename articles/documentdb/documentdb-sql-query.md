---
title: Sintaxis SQL y consulta SQL para Azure Cosmos DB | Microsoft Docs
description: "Más información sobre la sintaxis SQL, los conceptos de base de datos y las consultas SQL para Azure Cosmos DB. Puede usar SQL como lenguaje de consulta JSON en Azure Cosmos DB."
keywords: consulta sql, consultas sql, sintaxis sql, lenguaje de consulta json, conceptos de base de datos y consultas sql, funciones de agregado
services: cosmosdb
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: a73b4ab3-0786-42fd-b59b-555fce09db6e
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/08/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 11dc9d2e4c6cabfbfe7d68c88e5b210c94a3ec64
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="sql-query-and-sql-syntax-in-azure-cosmos-db"></a>Consulta SQL y sintaxis SQL en Azure Cosmos DB
Microsoft Azure Cosmos DB admite la consulta de documentos con SQL (lenguaje de consulta estructurado) como lenguaje de consulta de JSON. Cosmos DB realmente no tiene esquemas. En virtud de su compromiso con el modelo de datos JSON que se encuentra directamente en el motor de base de datos, proporciona índices automáticos de documentos JSON sin necesidad de un esquema explícito o de la creación de índices secundarios. 

Durante el diseño del lenguaje de consulta de Cosmos DB, teníamos dos objetivos en mente:

* En lugar de inventar un nuevo lenguaje de consulta JSON, preferimos ofrecer compatibilidad con el lenguaje SQL. SQL es uno de los lenguajes de consulta más familiares y populares. SQL de Cosmos DB proporciona un modelo de programación formal para consultas enriquecidas en documentos JSON.
* Igual que una base de datos de documentos JSON capaz de ejecutar JavaScript directamente en el motor de base de datos, queríamos usar el modelo de programación de JavaScript como base para nuestro lenguaje de consulta. SQL de la API de DocumentDB se basa en el sistema de tipos de JavaScript, la evaluación de expresiones y la invocación de funciones. A su vez, este proporciona un modelo de programación natural para proyecciones relacionales, navegación jerárquica por documentos JSON, autocombinaciones, consultas espaciales e invocación de funciones definidas por el usuario (UDF) escritas íntegramente en JavaScript, entre otras características. 

Creemos que estas capacidades son clave para reducir la fricción entre la aplicación y la base de datos, y que son cruciales para la productividad de los desarrolladores.

Se recomienda comenzar por ver el vídeo siguiente, donde Aravind Ramachandran muestra las capacidades de consulta de Cosmos DB y visitar [Query Playground](http://www.documentdb.com/sql/demo), donde puede probar Cosmos DB y ejecutar consultas SQL con nuestro conjunto de datos.

> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/DataExposedQueryingDocumentDB/player]
> 
> 

Luego, vuelva a este artículo, donde comenzaremos con un tutorial de consulta SQL que le guiará a través de algunos documentos sencillos de JSON y comandos SQL.

## <a id="GettingStarted"></a>Introducción a los comandos SQL en Cosmos DB
Para consultar SQL de Cosmos DB trabajando, empezaremos con unos documentos JSON sencillos y realizaremos algunas consultas fáciles con él. Tenga en cuenta estos dos documentos JSON sobre dos familias. No olvide que, con Cosmos DB, no es preciso que creemos ningún esquema ni índice secundario de forma explícita. Simplemente tenemos que insertar los documentos JSON en una colección Cosmos DB y posteriormente realizar una consulta. Aquí tenemos un documento JSON sencillo para la familia Andersen, los padres, los hijos (y sus mascotas), la dirección y la información de registro. El documento tiene cadenas, números, booleanos, matrices y propiedades anidadas. 

**Documento**  

```JSON
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow", 
         "gender": "female", 
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

Aquí se muestra un segundo documento con una sutil diferencia: se usan `givenName` y `familyName` en lugar de `firstName` y `lastName`.

**Documento**  

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

Ahora realicemos algunas consultas con estos datos para entender algunos aspectos clave de SQL de la API de DocumentDB. Por ejemplo, la consulta siguiente devolverá los documentos en los que el campo de id. coincida con `AndersenFamily`. Puesto que es `SELECT *`, la salida de la consulta es el documento JSON completo:

**Consultar**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]


Ahora preste atención al caso donde debemos cambiar el formato al resultado JSON con otra forma. Esta consulta proyecta un nuevo objeto JSON con dos campos seleccionados, Nombre y Ciudad, cuando la ciudad de la dirección tiene el mismo nombre que el estado. En este caso, "NY, NY" coincide.

**Consultar**    

    SELECT {"Name":f.id, "City":f.address.city} AS Family 
    FROM Families f 
    WHERE f.address.city = f.address.state

**Resultados**

    [{
        "Family": {
            "Name": "WakefieldFamily", 
            "City": "NY"
        }
    }]


La consulta siguiente devuelve todos los nombres proporcionados de los niños de la familia cuyo id. coincida con `WakefieldFamily`, ordenados por ciudad de residencia.

**Consultar**

    SELECT c.givenName 
    FROM Families f 
    JOIN c IN f.children 
    WHERE f.id = 'WakefieldFamily'
    ORDER BY f.address.city ASC

**Resultados**

    [
      { "givenName": "Jesse" }, 
      { "givenName": "Lisa"}
    ]


Nos gustaría llamar la atención sobre algunos aspectos destacados del lenguaje de consulta de Cosmos DB a través de los ejemplos que hemos visto hasta el momento:  

* Como SQL de la API de DocumentDB funciona con valores JSON, trata entidades en forma de árbol en lugar de filas y columnas. Por consiguiente, el lenguaje permite que se haga referencia a los nodos del árbol a cualquier profundidad arbitraria, como `Node1.Node2.Node3…..Nodem`, de forma similar al lenguaje SQL relacional que hace alusión a la referencia dos partes de `<table>.<column>`.   
* El lenguaje de consulta estructurado trabaja con datos sin esquemas. Por lo tanto, es necesario que el sistema de tipo se enlace dinámicamente. La misma expresión podría producir diversos tipos en distintos documentos. El resultado de una consulta es un valor JSON válido, pero no se garantiza que sea de un esquema fijo.  
* Cosmos DB solo admite documentos JSON estrictos. Esto significa que el sistema de tipo y las expresiones se restringen para tratar únicamente tipos JSON. Para obtener más información, consulte la [especificación de JSON](http://www.json.org/) .  
* Una recopilación de Cosmos DB es un contenedor sin esquemas de documentos JSON. Las relaciones en las entidades de datos dentro de los documentos de una colección y entre ellos se capturan de manera implícita por contención y no por relaciones entre clave principal y clave externa. Se trata de un aspecto importante que merece la pena señalar teniendo en cuenta las combinaciones internas descritas posteriormente en este artículo.

## <a id="Indexing"></a> Indexación de Cosmos DB
Antes de entrar en la sintaxis de SQL de la API de DocumentDB, vale la pena explorar el diseño de indexación de la API Cosmos DB. 

El objetivo de los índices de base de datos es atender consultas en sus diversas formas con un consumo de los recursos mínimo (como CPU y entrada y salida) mientras se proporcionan un buen rendimiento y una latencia baja. A menudo, la elección del índice adecuado para consultar una base de datos requiere mucha planificación y experimentación. Este enfoque plantea un desafío para las bases de datos sin esquemas en las que los datos no cumplen un esquema estricto y evolucionan rápidamente. 

Por lo tanto, al diseñar el subsistema de indexación de Cosmos DB, establecemos los siguientes objetivos:

* Indexar documentos sin necesidad de esquema: el subsistema de indexación no requiere información de esquema alguna ni la realización de ninguna suposición sobre el esquema de los documentos. 
* Compatibilidad con consultas eficaces, enriquecidas jerárquicas y relacionales: el índice admite el lenguaje de consulta de Cosmos DB de manera eficaz, incluida la compatibilidad con proyecciones jerárquicas y relacionales.
* Compatibilidad con consultas coherentes frente a un volumen de escrituras sostenido: en el caso de las cargas de trabajo de alto rendimiento de escritura con consultas coherentes, el índice se actualiza paulatinamente, de forma eficaz y en línea frente a un volumen de escrituras sostenido. La actualización del índice coherente es crucial para atender las consultas en el nivel de coherencia en el que el usuario configura el servicio de documentos.
* Compatibilidad con la arquitectura multiempresa: una vez proporcionado el modelo basado en la reserva para la gobernanza de recursos de los inquilinos, se realizan actualizaciones de los índices sin sobrepasar el presupuesto de los recursos del sistema (CPU, memoria, IOPS) asignadas por réplica. 
* Eficacia de almacenamiento: para obtener rentabilidad, se enlaza la sobrecarga de almacenamiento en el disco del índice y es predecible. Esto es fundamental porque Cosmos DB permite que el desarrollador haga concesiones basadas en el coste entre la sobrecarga de índices y el rendimiento de las consultas.  

Consulte los [ejemplos de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) en MSDN para ver casos en los que se muestra cómo configurar la directiva de indexación para una colección. Adentrémonos ahora en los detalles de la sintaxis de SQL de Azure Cosmos DB.

## <a id="Basics"></a>Conceptos básicos de una consulta SQL de Azure Cosmos DB
Todas las consultas constan de una cláusula SELECT y cláusulas FROM y WHERE opcionales por estándares ANSI-SQL. Normalmente, para cada consulta, se enumera el origen de la cláusula FROM. A continuación, el filtro de la cláusula WHERE se aplica en el origen para recuperar un subconjunto de documentos JSON. Por último, la cláusula SELECT se usa para proyectar los valores JSON solicitados en la lista seleccionada.

    SELECT <select_list> 
    [FROM <from_specification>] 
    [WHERE <filter_condition>]
    [ORDER BY <sort_specification]    


## <a id="FromClause"></a>Cláusula FROM
La cláusula `FROM <from_specification>` es opcional, a menos que el origen se filtre o se proyecte posteriormente en la consulta. El objetivo de esta cláusula es especificar el origen de datos sobre el que debe operar la consulta. Normalmente, toda la recopilación es el origen pero, en su lugar, puede especificarse un subconjunto de la recopilación. 

Una consulta como `SELECT * FROM Families` indica que toda la colección Families es el origen sobre el que se va a realizar la enumeración. Se puede usar una RAÍZ de identificador especial para representar la colección en lugar de usar el nombre de la colección. La lista siguiente contiene las reglas que se aplican por consulta:

* Puede establecerse para la colección un alias como `SELECT f.id FROM Families AS f`, o simplemente `SELECT f.id FROM Families f`. Aquí, `f` es el equivalente de `Families`. `AS` es una palabra clave opcional para establecer un alias para el identificador.
* Tenga en cuenta que, una vez establecido un alias, no podrá enlazarse el origen original. Por ejemplo, `SELECT Families.id FROM Families f` no es válido sintácticamente porque el identificador "Families" no puede resolverse.
* Todas las propiedades a las que es necesario hacer referencia deben estar completas. A falta de un cumplimiento del esquema estricto, esto se impone para evitar cualquier enlace ambiguo. Por lo tanto, `SELECT id FROM Families f` no es válido sintácticamente porque la propiedad `id` no está enlazada.

### <a name="sub-documents"></a>Subdocumentos
El origen también se puede reducir a un subconjunto más pequeño. Por ejemplo, para enumerar únicamente un subárbol en cada documento, la subraíz podría convertirse en el origen, como se muestra en el ejemplo siguiente.

**Consultar**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
            "firstName": "Henriette Thaulow",
            "gender": "female",
            "grade": 5,
            "pets": [
              {
                  "givenName": "Fluffy"
              }
            ]
        }
      ],
      [
        {
            "familyName": "Merriam",
            "givenName": "Jesse",
            "gender": "female",
            "grade": 1
        },
        {
            "familyName": "Miller",
            "givenName": "Lisa",
            "gender": "female",
            "grade": 8
        }
      ]
    ]

Aunque en el ejemplo anterior se usó una matriz como origen, también podría usarse un objeto como origen como se muestra en el ejemplo siguiente. Cualquier valor JSON válido (que no sea Undefined) que se pueda encontrar en el origen se tendrá en cuenta para su inclusión en el resultado de la consulta. Si algunas familias no tienen un valor `address.state` , se excluirán del resultado de la consulta.

**Consultar**

    SELECT * 
    FROM Families.address.state

**Resultados**

    [
      "WA", 
      "NY"
    ]


## <a id="WhereClause"></a>Cláusula WHERE
La cláusula WHERE (**`WHERE <filter_condition>`**) es opcional. Especifica las condiciones de que los documentos JSON proporcionados por el origen deben satisfacer para incluirse como parte del resultado. Cualquier documento JSON debe evaluar las condiciones especificadas en "true" para su consideración para el resultado. La capa de índice usa la cláusula WHERE para determinar el subconjunto más pequeño absoluto de documentos de origen que pueden formar parte del resultado. 

En la consulta siguiente se solicitan documentos que contienen una propiedad de nombre cuyo valor es `AndersenFamily`. Cualquier otro documento que no tenga una propiedad de nombre o en el que el valor no coincida con `AndersenFamily` se excluye. 

**Consultar**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


En el ejemplo anterior se mostraba una sencilla consulta de igualdad. SQL de la API de DocumentDB también admite diversas expresiones escalares. Las que más se suelen usar son binarias y unarias. Las referencias de propiedad del objeto JSON de origen también son expresiones válidas. 

Actualmente se admiten los siguientes operadores binarios y pueden usarse en consultas como se muestra en los ejemplos siguientes:  

<table>
<tr>
<td>Aritméticos</td>    
<td>+,-,*,/,%</td>
</tr>
<tr>
<td>Bit a bit</td>    
<td>|, &, ^, <<, >>, >>> (desplazamiento a la derecha con relleno de ceros)</td>
</tr>
<tr>
<td>Lógicos</td>
<td>AND, OR, NOT</td>
</tr>
<tr>
<td>De comparación</td>    
<td>=, !=, &lt;, &gt;, &lt;=, &gt;=, <></td>
</tr>
<tr>
<td>Cadena</td>    
<td>|| (concatenar)</td>
</tr>
</table>  


Echemos un vistazo a algunas consultas usando operadores binarios.

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT * 
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5     -- matching grades == 5


También se admiten los operadores unarios +,-, ~ y NOT, y se pueden usar dentro de consultas como se muestra en el ejemplo siguiente:

    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5



Además de los operadores unarios y binarios, también se permiten referencias de propiedad. Por ejemplo, `SELECT * FROM Families f WHERE f.isRegistered` devuelve el documento JSON que contenga la propiedad `isRegistered` en la que el valor de la propiedad sea igual al valor `true` JSON. Cualquier otro valor (false, null, Undefined, `<number>`, `<string>`, `<object>`, `<array>`, etc.) hace que el documento de origen se excluya del resultado. 

### <a name="equality-and-comparison-operators"></a>Operadores de igualdad y de comparación
En la siguiente tabla se muestra el resultado de las comparaciones de igualdad en el lenguaje SQL de la API de DocumentDB entre dos tipos JSON cualesquiera.

<table style = "width:300px">
   <tbody>
      <tr>
         <td valign="top">
            <strong>Op</strong>
         </td>
         <td valign="top">
            <strong>Undefined</strong>
         </td>
         <td valign="top">
            <strong>Null</strong>
         </td>
         <td valign="top">
            <strong>Booleano</strong>
         </td>
         <td valign="top">
            <strong>Número</strong>
         </td>
         <td valign="top">
            <strong>Cadena</strong>
         </td>
         <td valign="top">
            <strong>Objeto</strong>
         </td>
         <td valign="top">
            <strong>Matriz</strong>
         </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Undefined<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Null<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Booleano<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Número<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Cadena<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Objeto<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
         <td valign="top">
Undefined </td>
      </tr>
      <tr>
         <td valign="top">
            <strong>Matriz<strong>
         </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
Undefined </td>
         <td valign="top">
            <strong>OK</strong>
         </td>
      </tr>
   </tbody>
</table>

Para otros operadores de comparación como >, >=, !=, < y <=, se aplican las siguientes reglas:   

* La comparación entre los tipos da lugar a Undefined.
* La comparación entre dos objetos o dos matrices da lugar a Undefined.   

Si el resultado de la expresión escalar del filtro es Undefined, el documento correspondiente no se incluiría en el resultado, pues Undefined no es igual lógicamente a "true".

### <a name="between-keyword"></a>Palabra clave BETWEEN
También puede usar la palabra clave BETWEEN para expresar consultas en intervalos de valores como en ANSI SQL. BETWEEN puede utilizarse con cadenas o números.

Por ejemplo, esta consulta devuelve todos los documentos de la familia en los que el curso del primer hijo se encuentra entre 1 y 5 (ambos inclusive). 

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5

Al contrario que en ANSI SQL, también se puede usar la cláusula BETWEEN en la cláusula FROM, como en el ejemplo siguiente.

    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c

Para que la consulta se ejecute de forma más rápida, no olvide crear una directiva de indización que use un tipo de índice de intervalo en cualquier ruta o propiedad numérica que se filtre en la cláusula BETWEEN. 

La principal diferencia entre usar BETWEEN en la API de DocumentDB y SQL ANSI es que puede expresar consultas por rangos en propiedades de tipos mixtos. Por ejemplo, "grade" podría ser un número (5) en algunos documentos y una cadena ("grade4") en otros. En estos casos, al igual que en JavaScript, una comparación entre dos tipos distintos da como resultado "undefined" y el documento se omitirá.

### <a name="logical-and-or-and-not-operators"></a>Operadores lógicos (Y, O y NO)
Los operadores lógicos operan en valores booleanos. Las tablas de verdad lógica para estos operadores se muestran en las siguientes tablas.

| OR | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |True |True |
| False |True |False |Undefined |
| Undefined |True |Undefined |Undefined |

| Y | True | False | Undefined |
| --- | --- | --- | --- |
| True |True |False |Undefined |
| False |False |False |False |
| Undefined |Undefined |False |Undefined |

| NO |  |
| --- | --- |
| True |False |
| False |True |
| Undefined |Undefined |

### <a name="in-keyword"></a>Palabra clave IN
La palabra clave IN puede usarse para comprobar si un valor especificado coincide con algún valor de una lista. Por ejemplo, esta consulta devuelve todos los documentos de la familia en los que el identificador sea "WakefieldFamily" o "AndersenFamily". 

    SELECT *
    FROM Families 
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')

Este ejemplo devuelve todos los documentos en los que el estado es cualquiera de los valores especificados.

    SELECT *
    FROM Families 
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")

### <a name="ternary--and-coalesce--operators"></a>Operadores ternario (?) y de fusión (??)
El operador ternario y el operador de combinación pueden usarse para crear expresiones condicionales, de forma similar a lenguajes de programación populares como C# y JavaScript. 

El operador ternario (?) puede ser muy útil al construir nuevas propiedades JSON sobre la marcha. Así, ahora puede escribir consultas para clasificar los niveles de clase en formato de lenguaje natural, por ejemplo Principiante/Intermedio/Avanzado, como se muestra a continuación.

     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel 
     FROM Families.children[0] c

También puede anidar las llamadas al operador como en la consulta siguiente.

    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high")  AS gradeLevel 
    FROM Families.children[0] c

Como ocurre con otros operadores de consulta, si las propiedades a las que se hace referencia en la expresión condicional faltan en cualquier documento, o si los tipos que se comparan son diferentes, esos documentos se excluirán de los resultados de la consulta.

El operador de fusión (??) se puede usar para comprobar eficazmente la presencia de una propiedad (es decir, si esta se ha definido) en un documento. Esto es útil cuando se consultan datos semiestructurados o de tipos combinados. Por ejemplo, esta consulta devuelve el valor "lastName" si está presente o "surname" si no lo está.

    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f

### <a id="EscapingReservedKeywords"></a>Descriptor de acceso de propiedad entre comillas
También es posible obtener acceso a las propiedades mediante el operador de la propiedad entre comillas `[]`. Por ejemplo, `SELECT c.grade` and `SELECT c["grade"]` son equivalentes. Esta sintaxis es útil cuando se necesita crear una secuencia de escape para una propiedad que contiene espacios en blanco, caracteres especiales, o que comparte el nombre con una palabra clave SQL o una palabra reservada.

    SELECT f["lastName"]
    FROM Families f
    WHERE f["id"] = "AndersenFamily"


## <a id="SelectClause"></a>Cláusula SELECT
La cláusula SELECT (**`SELECT <select_list>`**) es obligatoria y especifica los valores que se recuperarán de la consulta, de la misma forma que en ANSI-SQL. El subconjunto que se ha filtrado en la parte superior de los documentos de origen pasa a la fase de proyección, en la cual se recuperan los valores JSON especificados y se construye un nuevo objeto JSON para cada una de las entradas que pasan a él. 

En el ejemplo siguiente se muestra una consulta SELECT típica: 

**Consultar**

    SELECT f.address
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "address": {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }
    }]


### <a name="nested-properties"></a>Propiedades anidadas
En el ejemplo siguiente, se proyectan dos propiedades anidadas, `f.address.state` and `f.address.city`.

**Consultar**

    SELECT f.address.state, f.address.city
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "state": "WA", 
      "city": "seattle"
    }]


La proyección también admite experiencias JSON, como se muestra en el siguiente ejemplo.

**Consultar**

    SELECT { "state": f.address.state, "city": f.address.city, "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle", 
        "name": "AndersenFamily"
      }
    }]


Analicemos el rol que `$1` tiene aquí. La cláusula `SELECT` debe crear un objeto JSON y, como no se proporciona ninguna clave, usamos nombres de variable de argumentos implícitos que empiezan por `$1`. Por ejemplo, esta consulta devuelve dos variables de argumentos implícitos, etiquetadas como `$1` and `$2`.

**Consultar**

    SELECT { "state": f.address.state, "city": f.address.city }, 
           { "name": f.id }
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "$1": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "$2": {
        "name": "AndersenFamily"
      }
    }]


### <a name="aliasing"></a>Establecimiento de alias
Ampliemos ahora el ejemplo anterior con un establecimiento de alias explícito para valores. AS es la palabra clave usada para el establecimiento de alias. Tenga en cuenta que es opcional, como se muestra al proyectarse el segundo valor como `NameInfo`. 

En caso de que una consulta tenga dos propiedades con el mismo nombre, el establecimiento de alias debe usarse para cambiar el nombre de las propiedades de modo que se elimine su ambigüedad en el resultado proyectado.

**Consultar**

    SELECT 
           { "state": f.address.state, "city": f.address.city } AS AddressInfo, 
           { "name": f.id } NameInfo
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
      "AddressInfo": {
        "state": "WA", 
        "city": "seattle"
      }, 
      "NameInfo": {
        "name": "AndersenFamily"
      }
    }]


### <a name="scalar-expressions"></a>Expresiones escalares
Además de las referencias de propiedad, la cláusula SELECT también admite expresiones escalares como constantes, expresiones aritméticas, expresiones lógicas, etc. Por ejemplo, aquí hay una sencilla consulta "Hello World".

**Consultar**

    SELECT "Hello World"

**Resultados**

    [{
      "$1": "Hello World"
    }]


A continuación se muestra un ejemplo más complejo que usa una expresión escalar.

**Consultar**

    SELECT ((2 + 11 % 7)-2)/3    

**Resultados**

    [{
      "$1": 1.33333
    }]


En el ejemplo siguiente, el resultado de la expresión escalar es un valor booleano.

**Consultar**

    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f    

**Resultados**

    [
      {
        "AreFromSameCityState": false
      }, 
      {
        "AreFromSameCityState": true
      }
    ]


### <a name="object-and-array-creation"></a>Creación de objetos y matrices
Otra característica clave del lenguaje SQL de la API de DocumentDB es la creación de matrices u objetos. En el ejemplo anterior, observe que creamos un nuevo objeto JSON. De manera similar, uno también puede construir matrices como se muestra en los siguientes ejemplos.

**Consultar**

    SELECT [f.address.city, f.address.state] AS CityState 
    FROM Families f    

**Resultados**  

    [
      {
        "CityState": [
          "seattle", 
          "WA"
        ]
      }, 
      {
        "CityState": [
          "NY", 
          "NY"
        ]
      }
    ]

### <a id="ValueKeyword"></a>Palabra clave VALUE
La palabra clave **VALUE** proporciona una forma de devolver un valor JSON. Por ejemplo, la consulta que se muestra a continuación devuelve la expresión escalar `"Hello World"`, en lugar de `{$1: "Hello World"}`.

**Consultar**

    SELECT VALUE "Hello World"

**Resultados**

    [
      "Hello World"
    ]


La siguiente consulta devuelve el valor JSON sin la etiqueta `"address"` en los resultados.

**Consultar**

    SELECT VALUE f.address
    FROM Families f    

**Resultados**  

    [
      {
        "state": "WA", 
        "county": "King", 
        "city": "seattle"
      }, 
      {
        "state": "NY", 
        "county": "Manhattan", 
        "city": "NY"
      }
    ]

El ejemplo siguiente se amplía para mostrar cómo devolver valores primitivos JSON (el nivel de hoja del árbol JSON). 

**Consultar**

    SELECT VALUE f.address.state
    FROM Families f    

**Resultados**

    [
      "WA",
      "NY"
    ]


### <a name="-operator"></a>Operador *
Se admite el operador especial (*) para proyectar el documento tal cual. Al usarse, debe ser el único campo proyectado. Aunque una consulta como `SELECT * FROM Families f` es válida, `SELECT VALUE * FROM Families f ` y `SELECT *, f.id FROM Families f ` no lo son.

**Consultar**

    SELECT * 
    FROM Families f 
    WHERE f.id = "AndersenFamily"

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

### <a id="TopKeyword"></a>Operador TOP
La palabra clave TOP se puede usar para limitar la cantidad de valores de una consulta. Cuando se usa TOP junto con la cláusula ORDER BY, el conjunto de resultados se limita a los primeros N valores ordenados; de otro modo, devuelve los primeros N resultados en orden indefinido. Como procedimiento recomendado, en una instrucción SELECT, siempre use una cláusula ORDER BY con la cláusula TOP. Esta es la única forma previsible de indicar qué filas afecta TOP. 

**Consultar**

    SELECT TOP 1 * 
    FROM Families f 

**Resultados**

    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]

TOP se puede usar con un valor constante (como se muestra anteriormente) o con un valor variable usando consultas con parámetros. Si desea obtener más información, consulte las consultas con parámetros que aparecen a continuación.

### <a id="Aggregates"></a>Funciones de agregado
También puede realizar agregaciones en la cláusula `SELECT`. Las funciones de agregado realizan un cálculo en un conjunto de valores y devuelven un valor único. Por ejemplo, la consulta siguiente devuelve el número de documentos de la familia dentro de la colección.

**Consultar**

    SELECT COUNT(1) 
    FROM Families f 

**Resultados**

    [{
        "$1": 2
    }]

También puede devolver el valor escalar del agregado mediante la palabra clave `VALUE`. Por ejemplo, la siguiente consulta devuelve el número de valores como un único número:

**Consultar**

    SELECT VALUE COUNT(1) 
    FROM Families f 

**Resultados**

    [ 2 ]

También puede realizar agregados en combinación con filtros. Por ejemplo, la consulta siguiente devuelve aquellos documentos con dirección en el estado de Washington.

**Consultar**

    SELECT VALUE COUNT(1) 
    FROM Families f
    WHERE f.address.state = "WA" 

**Resultados**

    [{
        "$1": 1
    }]

En las tablas siguientes se muestra la lista de funciones de agregado compatibles de la API de DocumentDB. `SUM`y `AVG` se aplican a valores numéricos, mientras que `COUNT`, `MIN` y `MAX` se pueden aplicar a números, cadenas, y valores booleanos y NULL. 

| Uso | Descripción |
|-------|-------------|
| COUNT | Devuelve el número de elementos de la expresión. |
| SUM   | Devuelve la suma de todos los valores de la expresión. |
| MÍN   | Devuelve el valor mínimo de la expresión. |
| MÁX   | Devuelve el valor máximo de la expresión. |
| MEDIA   | Devuelve la media de los valores de la expresión. |

Las funciones de agregado también se pueden aplicar a los resultados de una iteración de la matriz. Para más información, consulte [Iteración de matriz en consultas](#Iteration).

> [!NOTE]
> Al utilizar el Explorador de consultas de Azure Portal, tenga en cuenta que las consultas de agregación pueden devolver resultados agregados parcialmente a través de una página de consulta. Los SDK generarán un único valor acumulado en todas las páginas. 
> 
> Para realizar consultas de agregación mediante código, necesita .NET SDK 1.12.0, .NET Core SDK 1.1.0 o Java SDK 1.9.5 o superior.    
>

## <a id="OrderByClause"></a>Cláusula ORDER BY
Al igual que en ANSI SQL, puede incluir una cláusula Order By opcional al realizar la consulta. La cláusula puede incluir un argumento ASC o DESC opcional para especificar el orden en que se deben recuperar los resultados.

Por ejemplo, aquí hay una consulta que recupera las familias ordenadas por nombre de la ciudad de residencia.

**Consultar**

    SELECT f.id, f.address.city
    FROM Families f 
    ORDER BY f.address.city

**Resultados**

    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"    
      }
    ]

Y la siguiente es una consulta que recupera las familias ordenadas por fecha de creación, que se almacena como un número que representa el tiempo epoch, es decir, el tiempo transcurrido desde el 1 de enero de 1970, en segundos.

**Consultar**

    SELECT f.id, f.creationDate
    FROM Families f 
    ORDER BY f.creationDate DESC

**Resultados**

    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472    
      }
    ]

## <a id="Advanced"></a>Conceptos avanzados de base de datos y consultas SQL

### <a id="Iteration"></a>Iteración
Se ha agregado una nueva construcción mediante la palabra clave **IN** en SQL de la API de DocumentDB que proporcionar compatibilidad con la iteración en las matrices JSON. El origen FROM proporciona compatibilidad con la iteración. Empecemos con el ejemplo siguiente:

**Consultar**

    SELECT * 
    FROM Families.children

**Resultados**  

    [
      [
        {
          "firstName": "Henriette Thaulow", 
          "gender": "female", 
          "grade": 5, 
          "pets": [{ "givenName": "Fluffy"}]
        }
      ], 
      [
        {
            "familyName": "Merriam", 
            "givenName": "Jesse", 
            "gender": "female", 
            "grade": 1
        }, 
        {
            "familyName": "Miller", 
            "givenName": "Lisa", 
            "gender": "female", 
            "grade": 8
        }
      ]
    ]

Analicemos ahora otra consulta que realice una iteración sobre elementos secundarios de la recopilación. Observe la diferencia en la matriz de salida. En este ejemplo se divide `children` y se reducen los resultados a una sola matriz.  

**Consultar**

    SELECT * 
    FROM c IN Families.children

**Resultados**  

    [
      {
          "firstName": "Henriette Thaulow",
          "gender": "female",
          "grade": 5,
          "pets": [{ "givenName": "Fluffy" }]
      },
      {
          "familyName": "Merriam",
          "givenName": "Jesse",
          "gender": "female",
          "grade": 1
      },
      {
          "familyName": "Miller",
          "givenName": "Lisa",
          "gender": "female",
          "grade": 8
      }
    ]

Esto puede usarse más veces para filtrar por cada entrada individual de la matriz como se muestra en el ejemplo siguiente.

**Consultar**

    SELECT c.givenName
    FROM c IN Families.children
    WHERE c.grade = 8

**Resultados**  

    [{
      "givenName": "Lisa"
    }]

También puede aplicar agregaciones al resultado de la iteración de la matriz. Por ejemplo, la consulta siguiente cuenta el número de hijos entre todas las familias.

**Consultar**

    SELECT COUNT(child) 
    FROM child IN Families.children

**Resultados**  

    [
      { 
        "$1": 3
      }
    ]

### <a id="Joins"></a>Combinaciones
En una base de datos relacional, la necesidad de combinar en tablas es muy importante. Es la consecuencia lógica de diseñar esquemas normalizados. Al contrario que esto, la API de DocumentDB aborda el modelo de datos desnormalizado de documentos sin esquemas. Este es el equivalente lógico de una "autocombinación".

La sintaxis que admite el lenguaje es <from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>. Generalmente, esto devuelve un conjunto de **N** tuplas (tupla con **N** valores). Cada tupla tiene valores generados por sus respectivos conjuntos en iteración de todos los alias de colección. En otras palabras, se trata de un producto cruzado completo de los conjuntos que participan en la combinación.

En los ejemplos siguientes se muestra cómo funciona la cláusula JOIN. En el siguiente ejemplo, el resultado está vacío porque el producto cruzado de cada documento del origen y un conjunto vacío está vacío.

**Consultar**

    SELECT f.id
    FROM Families f
    JOIN f.NonExistent

**Resultados**  

    [{
    }]


En el ejemplo siguiente, la combinación se realiza entre la raíz del documento y la subraíz de `children` . Es un producto cruzado entre dos objetos JSON. El hecho de que los elementos secundarios sean una matriz no funciona en JOIN porque abordamos una sola raíz que es la matriz secundaria. Así pues, en el resultado se incluyen únicamente dos resultados, pues el producto cruzado de cada documento con la matriz produce exactamente solo un documento.

**Consultar**

    SELECT f.id
    FROM Families f
    JOIN f.children

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]


En el ejemplo siguiente se muestra una combinación más convencional:

**Consultar**

    SELECT f.id
    FROM Families f
    JOIN c IN f.children 

**Resultados**

    [
      {
        "id": "AndersenFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }, 
      {
        "id": "WakefieldFamily"
      }
    ]



Lo primero que hay que tener en cuenta es que `from_source` de la cláusula **JOIN** es un iterador. Por lo tanto, el flujo en este caso es como sigue:  

* Amplía cada elemento secundario **c** en la matriz.
* Aplique un producto cruzado con la raíz del documento **f** con cada elemento secundario **c** del que se quitó el formato en el primer paso.
* Por último, proyecte solo la propiedad de nombre **f** del objeto raíz. 

El primer documento (`AndersenFamily`) contiene únicamente un elemento secundario, por lo que el conjunto de resultados contiene solo un objeto correspondiente a este documento. El segundo documento (`WakefieldFamily`) contiene dos elementos secundarios. De este modo, el producto cruzado produce un objeto independiente para cada elemento secundario, lo que da lugar a dos objetos, uno para cada elemento secundario correspondiente a este documento. Tenga en cuenta que los campos raíces de estos dos documentos serán los mismos, justo como esperaría en un producto cruzado.

La utilidad real de JOIN es la formación de tuplas a partir del producto cruzado con una forma que de otro modo es difícil de proyectar. Además, como veremos en el siguiente ejemplo, podría filtrarse por la combinación de una tupla que permite que el usuario elija una condición satisfecha por las tuplas en general.

**Consultar**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets

**Resultados**

    [
      {
        "familyName": "AndersenFamily", 
        "childFirstName": "Henriette Thaulow", 
        "petName": "Fluffy"
      }, 
      {
        "familyName": "WakefieldFamily", 
        "childGivenName": "Jesse", 
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]



Este ejemplo es una ampliación natural del anterior y realiza una combinación doble. De este modo, el producto cruzado se puede ver como el pseudocódigo siguiente.

    for-each(Family f in Families)
    {    
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName, 
                  c.givenName AS childGivenName, 
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }

`AndersenFamily` tiene un hijo que tiene una mascota. De esta manera, el producto cruzado produce una fila (1*1*1) a partir de esta familia. La familia Wakefield tiene, sin embargo, dos hijos, pero solo uno, "Jesse", tiene mascotas. Tiene dos mascotas, sin embargo. Así pues, el producto cruzado produce 1*1*2 = 2 filas a partir de esta familia.

En el ejemplo siguiente, hay un filtro adicional en `pet` Este excluye todas las tuplas donde el nombre de mascota no sea "Shadow". Tenga en cuenta que podemos crear tuplas a partir de matrices, filtrar por cualquiera de los elementos de la tupla y proyectar cualquier combinación de los elementos. 

**Consultar**

    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName 
    FROM Families f 
    JOIN c IN f.children 
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"

**Resultados**

    [
      {
       "familyName": "WakefieldFamily", 
       "childGivenName": "Jesse", 
       "petName": "Shadow"
      }
    ]


## <a id="JavaScriptIntegration"></a>Integración de JavaScript
Azure Cosmos DB proporciona un modelo de programación para ejecutar una lógica de aplicación basada en JavaScript directamente en las recopilaciones en términos de procedimientos y desencadenadores almacenados. Esto les proporciona:

* La posibilidad de realizar operaciones CRUD transaccionales de alto rendimiento y consultas en los documentos de una recopilación en virtud de una mayor integración del tiempo de ejecución de JavaScript directamente en el motor de base de datos. 
* Un modelo natural de flujo de control, ámbito variable, asignación e integración de primitivas de control de excepciones con transacciones de base de datos. Para más detalles sobre la compatibilidad de Azure Cosmos DB con la integración de JavaScript, consulte la documentación de programación del servidor de JavaScript.

### <a id="UserDefinedFunctions"></a>Funciones definidas por el usuario (UDF)
Junto con los tipos ya definidos en este artículo, el lenguaje SQL de la API de DocumentDB ofrece compatibilidad para las funciones definidas por el usuario (UDF). En particular, se admiten las UDF escalares allí donde los desarrolladores puedan proporcionar cero o muchos argumentos y devolver un solo resultado de argumento. Cada uno de estos argumentos se comprueba para ver si se trata de valores JSON legales.  

La sintaxis del lenguaje SQL de la API DocumentDB se amplía para admitir una lógica de aplicación personalizada usando estas funciones definidas por el usuario. Las funciones definidas por el usuario pueden registrarse con la API de DocumentDB y, a continuación, se puede hacer referencia a ellas como parte de una consulta de SQL. De hecho, las UDF están exquisitamente diseñadas para su invocación por parte de las consultas. Como resultado de esta opción, las UDF no tienen acceso al objeto de contexto que los otros tipos de JavaScript (procedimientos y desencadenadores almacenados) tienen. Puesto que las consultas se ejecutan como de solo lectura, pueden ejecutarse en réplicas principales o secundarias. Por consiguiente, las UDF están diseñadas para ejecutarse en réplicas secundarias, a diferencia de otros tipos de JavaScript.

A continuación, vemos un ejemplo de cómo puede registrarse una UDF en la base de datos de Cosmos DB, concretamente en una recopilación de documentos.

       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) { 
                       return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
           regexMatchUdf).Result;  

El ejemplo anterior crea una UDF cuyo nombre es `REGEX_MATCH`. Acepta dos valores de cadena JSON, `input` and `pattern` , y comprueba si el primero coincide con el patrón especificado en el segundo mediante la función string.match() de JavaScript.

Ahora podemos usar esta UDF en una consulta de una proyección. Las UDF deben estar calificadas con el prefijo, que distingue mayúsculas de minúsculas, "udf." cuando se las llama desde las consultas. 

> [!NOTE]
> Antes del 17/03/2015, Cosmos DB admitía las llamadas de UDF sin el prefijo "udf." al igual que SELECT REGEX_MATCH(). Este patrón de llamada está desusado.  
> 
> 

**Consultar**

    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families

**Resultados**

    [
      {
        "$1": true
      }, 
      {
        "$1": false
      }
    ]

La UDF también puede usarse en un filtro tal como se muestra en el ejemplo siguiente, calificado igualmente con el prefijo "udf." prefix :

**Consultar**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")

**Resultados**

    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]


Básicamente, las UDF son expresiones escalares válidas y pueden usarse en ambas proyecciones y filtros. 

Para expandir el poder de las UDF, echemos un vistazo a otro ejemplo con lógica condicional:

       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("testdb", "families"), 
                seaLevelUdf);


A continuación se muestra un ejemplo que ejerce la UDF.

**Consultar**

    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f    

**Resultados**

     [
      {
        "city": "seattle", 
        "seaLevel": 520
      }, 
      {
        "city": "NY", 
        "seaLevel": 410
      }
    ]


Al igual que en los ejemplos anteriores se muestran casos, las UDF integran el poder del lenguaje de JavaScript con el lenguaje SQL de la API de DocumentDB para proporcionar una interfaz programable enriquecida a fin de hacer lógica condicional de procedimientos compleja con la ayuda de capacidades en tiempo real de JavaScript integradas.

SQL de la API de DocumentDB proporciona los argumentos a las UDF para cada uno de los documentos del origen en la fase actual (cláusulas WHERE o SELECT) de procesamiento de la UDF. El resultado se incorpora en el proceso de ejecución general perfectamente. Si las propiedades a las que los parámetros UDF hacen referencia no están disponibles en el valor JSON, el parámetro se considera Undefined y así pues, la invocación de UDF se omite por completo. De forma similar, si el resultado de la UDF es Undefined, no se incluye en el resultado. 

En resumen, las UDF son excelentes herramientas para hacer lógica de negocios como parte de la consulta.

### <a name="operator-evaluation"></a>Evaluación de operadores
Cosmos DB, en virtud de ser una base de datos JSON, establece paralelismos con los operadores de JavaScript y su semántica de evaluación. Aunque Cosmos DB intenta conservar la semántica de JavaScript en términos de soporte para JSON, la evaluación de operaciones se desvía en algunas instancias.

En SQL de la API de DocumentDB, a diferencia del lenguaje SQL tradicional, los tipos de valores no suelen conocerse hasta que los valores se recuperan realmente de la base de datos. Para ejecutar consultas de forma eficaz, la mayoría de los operadores tienen requisitos de tipo estrictos. 

SQL de la API de DocumentDB no realiza conversiones implícitas a diferencia de JavaScript. Por ejemplo, una consulta como `SELECT * FROM Person p WHERE p.Age = 21` encuentra documentos que contienen una propiedad Age cuyo valor es 21. Cualquier otro documento cuya propiedad Age coincida con la cadena "21", u otras variaciones posiblemente infinitas, como "021", "21,0", "0021", "00021", etc. no producirán coincidencias. Esto es en contraste al JavaScript donde los valores de cadena se convierten de manera implícita en números (en función del operador, por ejemplo, ==). Esta opción es fundamental para conseguir una coincidencia de índices eficaz en SQL de la API de DocumentDB. 

## <a name="parameterized-sql-queries"></a>Consultas SQL con parámetros
Cosmos DB admite las consultas con parámetros que se expresen con la notación @ ya conocida. El uso de SQL con parámetros permite controlar y evitar de forma sólida la entrada por parte de los usuarios, impidiendo así la exposición accidental de datos a través de la inyección de código SQL. 

Por ejemplo, puede escribir una consulta que acepte los apellidos y el estado de la dirección como parámetros y, a continuación, ejecutarla para distintos valores de los parámetros mencionados en función de la entrada del usuario.

    SELECT * 
    FROM Families f
    WHERE f.lastName = @lastName AND f.address.state = @addressState

Después, esta solicitud puede enviarse a Cosmos DB como consulta JSON con parámetros, como se muestra a continuación.

    {      
        "query": "SELECT * FROM Families f WHERE f.lastName = @lastName AND f.address.state = @addressState",     
        "parameters": [          
            {"name": "@lastName", "value": "Wakefield"},         
            {"name": "@addressState", "value": "NY"},           
        ] 
    }

El argumento para TOP se puede definir mediante el uso de consultas con parámetros, tal como se muestra a continuación.

    {      
        "query": "SELECT TOP @n * FROM Families",     
        "parameters": [          
            {"name": "@n", "value": 10},         
        ] 
    }

Los valores de los parámetros pueden ser cualquier tipo de JSON válido (cadenas, números, booleanos, null o incluso matrices o JSON anidado). Además, como Cosmos DB no tiene ningún esquema, los parámetros no se validan respecto a ningún tipo.

## <a id="BuiltinFunctions"></a>Funciones integradas
Cosmos DB también admite un número de funciones integradas para operaciones comunes, que se pueden usar dentro de las consultas como funciones definidas por el usuario (UDF).

| Grupo de funciones          | Operaciones                                                                                                                                          |
|-------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------|
| Funciones matemáticas  | ABS, CEILING, EXP, FLOOR, LOG, LOG10, POWER, ROUND, SIGN, SQRT, SQUARE, TRUNC, ACOS, ASIN, ATAN, ATN2, COS, COT, DEGREES, PI, RADIANS, SIN y TAN |
| Funciones de comprobación de tipos | IS_ARRAY, IS_BOOL, IS_NULL, IS_NUMBER, IS_OBJECT, IS_STRING, IS_DEFINED e IS_PRIMITIVE                                                           |
| Funciones de cadena        | CONCAT, CONTAINS, ENDSWITH, INDEX_OF, LEFT, LENGTH, LOWER, LTRIM, REPLACE, REPLICATE, REVERSE, RIGHT, RTRIM, STARTSWITH, SUBSTRING y UPPER       |
| Funciones de matriz         | ARRAY_CONCAT, ARRAY_CONTAINS, ARRAY_LENGTH y ARRAY_SLICE                                                                                         |
| Funciones espaciales       | ST_DISTANCE, ST_WITHIN, ST_INTERSECTS, ST_ISVALID y ST_ISVALIDDETAILED                                                                           | 

Si actualmente utiliza una función definida por el usuario (UDF) para la que ahora hay disponible una función integrada, debe usar la función integrada correspondiente ya que se va a ejecutar más rápidamente y va a ser más eficaz. 

### <a name="mathematical-functions"></a>Funciones matemáticas
Las funciones matemáticas realizan un cálculo, basado normalmente en valores de entrada proporcionados como argumentos, y devuelven un valor numérico. Esta es una tabla de las funciones matemáticas integradas admitidas.


| Uso | Descripción |
|----------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [ABS (num_expr)](#bk_abs) | Devuelve el valor absoluto (positivo) de la expresión numérica especificada. |
| [CEILING (num_expr)](#bk_ceiling) | Devuelve el valor entero más pequeño mayor o igual que la expresión numérica especificada. |
| [FLOOR (num_expr)](#bk_floor) | Devuelve el valor entero más grande menor o igual que la expresión numérica especificada. |
| [EXP (num_expr)](#bk_exp) | Devuelve el exponente de la expresión numérica especificada. |
| [LOG (num_expr [,base])](#bk_log) | Devuelve el logaritmo natural de la expresión numérica especificada o bien el logaritmo que utiliza la base especificada |
| [LOG10 (num_expr)](#bk_log10) | Devuelve el valor logarítmico de base 10 de la expresión numérica especificada. |
| [ROUND (num_expr)](#bk_round) | Devuelve un valor numérico, redondeado al valor entero más cercano. |
| [TRUNC (num_expr)](#bk_trunc) | Devuelve un valor numérico, truncado al valor entero más cercano. |
| [SQRT (num_expr)](#bk_sqrt) | Devuelve la raíz cuadrada de la expresión numérica especificada. |
| [SQUARE (num_expr)](#bk_square) | Devuelve el cuadrado de la expresión numérica especificada. |
| [POWER (num_expr, num_expr)](#bk_power) | Devuelve la potencia de la expresión numérica especificada al valor especificado. |
| [SIGN (num_expr)](#bk_sign) | Devuelve el valor de signo (-1, 0, 1) de la expresión numérica especificada. |
| [ACOS (num_expr)](#bk_acos) | Devuelve el ángulo, en radianes, cuyo coseno es la expresión numérica especificada; también se denomina arcocoseno. |
| [ASIN (num_expr)](#bk_asin) | Devuelve el ángulo, en radianes, cuyo seno es la expresión numérica especificada. También se denomina arcoseno. |
| [ATAN (num_expr)](#bk_atan) | Devuelve el ángulo, en radianes, cuya tangente es la expresión numérica especificada. También se denomina arcotangente. |
| [ATN2 (num_expr)](#bk_atn2) | Devuelve el ángulo, en radianes, entre el eje x positivo y el rayo desde el origen hasta el punto (y, x), donde x e y son los valores de las dos expresiones de punto flotante especificadas. |
| [COS (num_expr)](#bk_cos) | Devuelve el coseno trigonométrico del ángulo especificado, en radianes, en la expresión especificada. |
| [COT (num_expr)](#bk_cot) | Devuelve la cotangente trigonométrica del ángulo especificado, en radianes, en la expresión numérica especificada. |
| [DEGREES (num_expr)](#bk_degrees) | Devuelve el ángulo correspondiente en grados de un ángulo especificado en radianes. |
| [PI ()](#bk_pi) | Devuelve el valor constante de PI. |
| [RADIANS (num_expr)](#bk_radians) | Devuelve radianes cuando se especifica una expresión numérica en grados. |
| [SIN (num_expr)](#bk_sin) | Devuelve el seno trigonométrico del ángulo especificado, en radianes, en la expresión especificada. |
| [TAN (num_expr)](#bk_tan) | Devuelve la tangente de la expresión de entrada en la expresión especificada. |

Por ejemplo, ya puede ejecutar consultas similares a las siguientes:

**Consultar**

    SELECT VALUE ABS(-4)

**Resultados**

    [4]

La principal diferencia entre funciones de Cosmos DB y SQL ANSI es que están diseñadas para funcionar bien con datos sin esquemas y datos de esquemas mixtos. Por ejemplo, si tiene un documento donde falta la propiedad de tamaño o tiene un valor no numérico, como "desconocido", se omite el documento, en lugar de devolver un error.

### <a name="type-checking-functions"></a>Funciones de comprobación de tipos
Las funciones de comprobación de tipos permiten comprobar el tipo de una expresión dentro de consultas SQL. Las funciones de comprobación de tipos pueden utilizarse para determinar el tipo de propiedades dentro de los documentos sobre la marcha cuando es variable o desconocido Esta es una tabla de las funciones de comprobación de tipos integradas admitidas.

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_array">IS_ARRAY (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es una matriz.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_bool">IS_BOOL (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es un valor booleano.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_null">IS_NULL (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es nulo.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_number">IS_NUMBER (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es un número.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_object">IS_OBJECT (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es un objeto JSON.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_string">IS_STRING (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es una cadena.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_defined">IS_DEFINED (expr)</a></td>
  <td>Devuelve un valor booleano que indica si se ha asignado un valor a la propiedad.</td>
</tr>
<tr>
  <td><a href="https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_is_primitive">IS_PRIMITIVE (expr)</a></td>
  <td>Devuelve un valor booleano que indica si el tipo del valor es una cadena, número, valor booleano o null.</td>
</tr>

</table>

Con estas funciones, ya puede ejecutar consultas similares a las siguientes:

**Consultar**

    SELECT VALUE IS_NUMBER(-4)

**Resultados**

    [true]

### <a name="string-functions"></a>Funciones de cadena
Las siguientes funciones escalares realizan una operación sobre un valor de entrada de cadena y devuelven una cadena, un valor numérico o un booleano. A continuación se facilita una tabla de funciones de cadena integradas:

| Uso | Descripción |
| --- | --- |
| [LENGTH (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_length) |Devuelve el número de caracteres de la expresión de cadena especificada. |
| [CONCAT (str_expr, str_expr [, str_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_concat) |Devuelve una cadena que es el resultado de concatenar dos o más valores de cadena. |
| [SUBSTRING (str_expr, num_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_substring) |Devuelve parte de una expresión de cadena. |
| [STARTSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_startswith) |Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda. |
| [ENDSWITH (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_endswith) |Devuelve un valor booleano que indica si la primera expresión de cadena finaliza con la segunda. |
| [CONTAINS (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_contains) |Devuelve un valor booleano que indica si la primera expresión de cadena contiene la segunda. |
| [INDEX_OF (str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_index_of) |Devuelve la posición inicial de la primera aparición de la expresión de la segunda cadena dentro de la primera expresión de cadena especificada, o -1 si no se encuentra la cadena. |
| [LEFT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_left) |Devuelve la parte izquierda de una cadena con el número especificado de caracteres. |
| [RIGHT (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_right) |Devuelve la parte derecha de una cadena con el número especificado de caracteres. |
| [LTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_ltrim) |Devuelve una expresión de cadena después de quitar los espacios en blanco iniciales. |
| [RTRIM (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_rtrim) |Devuelve una expresión de cadena después de truncar todos los espacios en blanco finales. |
| [LOWER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_lower) |Devuelve una expresión de cadena después de convertir los datos de caracteres en mayúsculas a minúsculas. |
| [UPPER (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_upper) |Devuelve una expresión de cadena después de convertir datos de caracteres en minúsculas a mayúsculas. |
| [REPLACE (str_expr, str_expr, str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replace) |Reemplaza todas las apariciones de un valor de cadena especificado por otro valor de cadena. |
| [REPLICATE (str_expr, num_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_replicate) |Repite un valor de cadena un número especificado de veces. |
| [REVERSE (str_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_reverse) |Devuelve el orden inverso de un valor de cadena. |

Con estas funciones, ya puede ejecutar consultas similares a las siguientes. Por ejemplo, puede devolver el nombre de familia en mayúsculas del modo siguiente:

**Consultar**

    SELECT VALUE UPPER(Families.id)
    FROM Families

**Resultados**

    [
        "WAKEFIELDFAMILY", 
        "ANDERSENFAMILY"
    ]

O concatenar cadenas como en este ejemplo:

**Consultar**

    SELECT Families.id, CONCAT(Families.address.city, ",", Families.address.state) AS location
    FROM Families

**Resultados**

    [{
      "id": "WakefieldFamily",
      "location": "NY,NY"
    },
    {
      "id": "AndersenFamily",
      "location": "seattle,WA"
    }]


Las funciones de cadena también pueden usarse en la cláusula WHERE para filtrar los resultados, al igual que en el ejemplo siguiente:

**Consultar**

    SELECT Families.id, Families.address.city
    FROM Families
    WHERE STARTSWITH(Families.id, "Wakefield")

**Resultados**

    [{
      "id": "WakefieldFamily",
      "city": "NY"
    }]

### <a name="array-functions"></a>Funciones de matriz
Las siguientes funciones escalares realizan una operación en un valor de entrada de matriz y devolver un valor numérico, booleano o de matriz. A continuación se facilita una tabla de funciones de matriz integradas:

| Uso | Descripción |
| --- | --- |
| [ARRAY_LENGTH (arr_expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_length) |Devuelve el número de elementos de la expresión de matriz especificada. |
| [ARRAY_CONCAT (arr_expr, arr_expr [, arr_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_concat) |Devuelve una matriz que es el resultado de concatenar dos o más valores de la matriz. |
| [ARRAY_CONTAINS (arr_expr, expr)](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_contains) |Devuelve un valor booleano que indica si la matriz contiene el valor especificado. |
| [ARRAY_SLICE (arr_expr, num_expr [, num_expr])](https://msdn.microsoft.com/library/azure/dn782250.aspx#bk_array_slice) |Devuelve parte de una expresión de matriz. |

Las funciones de matriz pueden usarse para manipular matrices en JSON. Por ejemplo, a continuación se facilita una consulta que devuelve todos los documentos en los que uno de los elementos primarios es "Robin Wakefield". 

**Consultar**

    SELECT Families.id 
    FROM Families 
    WHERE ARRAY_CONTAINS(Families.parents, { givenName: "Robin", familyName: "Wakefield" })

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Este es otro ejemplo que usa ARRAY_LENGTH para obtener el número de hijos por familia.

**Consultar**

    SELECT Families.id, ARRAY_LENGTH(Families.children) AS numberOfChildren
    FROM Families 

**Resultados**

    [{
      "id": "WakefieldFamily",
      "numberOfChildren": 2
    },
    {
      "id": "AndersenFamily",
      "numberOfChildren": 1
    }]

### <a name="spatial-functions"></a>Funciones espaciales
Cosmos DB admite las siguientes funciones integradas de Open Geospatial Consortium (OGC) para realizar consultas geoespaciales. 

<table>
<tr>
  <td><strong>Uso</strong></td>
  <td><strong>Descripción</strong></td>
</tr>
<tr>
  <td>ST_DISTANCE (point_expr, point_expr)</td>
  <td>Devuelve la distancia entre dos expresiones Point, Polygon o LineString de GeoJSON.</td>
</tr>
<tr>
  <td>ST_WITHIN (point_expr, polygon_expr)</td>
  <td>Devuelve una expresión booleana que indica si el primer objeto de GeoJSON (Point, Polygon o LineString) está en el segundo objeto de GeoJSON (Point, Polygon o LineString).</td>
</tr>
<tr>
  <td>ST_INTERSECTS (spatial_expr, spatial_expr)</td>
  <td>Devuelve una expresión booleana que indica si los dos objetos de GeoJSON especificados (Point, Polygon o LineString) intersecan.</td>
</tr>
<tr>
  <td>ST_ISVALID</td>
  <td>Devuelve un valor booleano que indica si la expresión de Point, Polygon o LineString de GeoJSON especificada es válida.</td>
</tr>
<tr>
  <td>ST_ISVALIDDETAILED</td>
  <td>Devuelve un valor JSON que contiene un valor booleano si la expresión de Point, Polygon o LineString de GeoJSON especificada es válida; si no es válida, devuelve también el motivo como un valor de cadena.</td>
</tr>
</table>

Las funciones espaciales pueden usarse para realizar consultas de proximidad con datos espaciales. Por ejemplo, aquí hay una consulta que devuelve todos los documentos de la familia que estén dentro de un radio de 30 km de la ubicación especificada mediante la función integrada ST_DISTANCE. 

**Consultar**

    SELECT f.id 
    FROM Families f 
    WHERE ST_DISTANCE(f.location, {'type': 'Point', 'coordinates':[31.9, -4.8]}) < 30000

**Resultados**

    [{
      "id": "WakefieldFamily"
    }]

Para más información sobre la compatibilidad geoespacial en Cosmos DB, consulte [Uso de datos geoespaciales en Azure Cosmos DB](documentdb-geospatial.md), que contiene funciones espaciales y sintaxis de SQL para Cosmos DB. Ahora veamos cómo funciona la consulta LINQ y cómo interactúa con la sintaxis que hemos visto hasta ahora.

## <a id="Linq"></a>LINQ para SQL de la API de DocumentDB
LINQ es un modelo de programación de .NET que expresa cálculos como consultas de secuencias de objetos. Cosmos DB proporciona una biblioteca del cliente para interactuar con LINQ facilitando una conversión entre objetos JSON y .NET y una asignación a partir de un subconjunto de consultas de LINQ a consultas de Cosmos DB. 

En la imagen que se muestra a continuación vemos la arquitectura de consultas compatibles con LINQ que usa Cosmos DB.  Con el cliente de Cosmos DB, los desarrolladores pueden crear un objeto **IQueryable** que consulta directamente al proveedor de consultas de Cosmos DB que, a continuación, convierte la consulta de LINQ en una consulta de Cosmos DB. La consulta pasa entonces al servidor de Cosmos DB para recuperar un conjunto de resultados en formato JSON. Los resultados devueltos se deserializan en una secuencia de objetos .NET en el cliente.

![Arquitectura de consultas compatibles con LINQ que usa la API de DocumentDB - sintaxis SQL, lenguaje de consulta JSON, conceptos de base de datos y consultas SQL][1]

### <a name="net-and-json-mapping"></a>Asignación de .NET y JSON
La asignación entre objetos .NET y documentos JSON es natural (cada campo del miembro de datos se asigna a un objeto JSON, donde el nombre del campo se asigna a la parte "clave" del objeto y la parte de "valor" se asigna de forma recursiva a la parte de valor del objeto. Considere el ejemplo siguiente. El objeto Familia creado se asigna al documento JSON como se muestra a continuación. Y viceversa, el documento JSON se reasigna de nuevo a un objeto .NET.

**Clase de C#**

    public class Family
    {
        [JsonProperty(PropertyName="id")]
        public string Id;
        public Parent[] parents;
        public Child[] children;
        public bool isRegistered;
    };

    public struct Parent
    {
        public string familyName;
        public string givenName;
    };

    public class Child
    {
        public string familyName;
        public string givenName;
        public string gender;
        public int grade;
        public List<Pet> pets;
    };

    public class Pet
    {
        public string givenName;
    };

    public class Address
    {
        public string state;
        public string county;
        public string city;
    };

    // Create a Family object.
    Parent mother = new Parent { familyName= "Wakefield", givenName="Robin" };
    Parent father = new Parent { familyName = "Miller", givenName = "Ben" };
    Child child = new Child { familyName="Merriam", givenName="Jesse", gender="female", grade=1 };
    Pet pet = new Pet { givenName = "Fluffy" };
    Address address = new Address { state = "NY", county = "Manhattan", city = "NY" };
    Family family = new Family { Id = "WakefieldFamily", parents = new Parent [] { mother, father}, children = new Child[] { child }, isRegistered = false };


**JSON**  

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
                "gender": "female", 
                "grade": 1,
                "pets": [
                    { "givenName": "Goofy" },
                    { "givenName": "Shadow" }
                ]
            },
            { 
              "familyName": "Miller", 
              "givenName": "Lisa", 
              "gender": "female", 
              "grade": 8 
            }
        ],
        "address": { "state": "NY", "county": "Manhattan", "city": "NY" },
        "isRegistered": false
    };



### <a name="linq-to-sql-translation"></a>LINQ para traducción de lenguaje SQL
El proveedor de consulta de Cosmos DB realiza una mejor opción de asignación desde una consulta de LINQ a una consulta SQL de Cosmos DB. En la siguiente descripción, asumimos la familiaridad básica del lector con LINQ.

En primer lugar, para el sistema de tipos, admitimos todos los tipos primitivos JSON (tipos numéricos, booleanos, de cadena y null). Solo se admiten estos tipos JSON. Se admiten las siguientes expresiones escalares.

* Valores constantes: entre estos se incluyen los valores constantes de los tipos de datos primitivos durante la evaluación de la consulta.
* Expresiones de índice de propiedad o matriz: estas expresiones hacen referencia a la propiedad de un objeto o a un elemento de matriz.
  
     family.Id;    family.children[0].familyName;    family.children[0].grade;    family.children[n].grade; //n es una variable int
* Expresiones aritméticas: entre estas se incluyen expresiones aritméticas comunes o valores numéricos y booleanos. Para ver la lista completa, consulte la especificación de SQL.
  
     2 * family.children[0].grade;    x + y;
* Expresión de comparación de cadenas: entre estas se incluye la comparación de un valor de cadena con algún valor de cadena constante.  
  
     mother.familyName == "Smith";    child.givenName == s; //s es una variable de cadena
* Expresión de creación de objetos o matrices: estas expresiones devuelven un objeto de tipo de valor compuesto o tipo anónimo o una matriz de estos objetos. Estos valores se pueden anidar.
  
     new Parent { familyName = "Smith", givenName = "Joe" }; new { first = 1, second = 2 }; //un tipo anónimo con 2 campos              
     new int[] { 3, child.grade, 5 };

### <a id="SupportedLinqOperators"></a>Lista de los operadores LINQ admitidos
La siguiente es una lista de los operadores LINQ admitidos en el proveedor LINQ incluido en el SDK de .NET de DocumentDB.

* **Select**: Las proyecciones se traducen en la instrucción SQL SELECT, incluida la construcción de objetos.
* **Where**: Los filtros se traducen a la instrucción SQL WHERE y admiten la traducción entre && , || y ! a los operadores SQL.
* **SelectMany**: Permite desenredar las matrices a la cláusula SQL JOIN. Se puede usar para encadenar/anidar expresiones para filtrar los elementos de la matriz.
* **OrderBy y OrderByDescending**: Se traduce a ORDER BY ascendente/descendente
* Los operadores **Count**, **Sum**, **Min**, **Max** y **Average** para la agregación y sus equivalentes asincrónicos **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** y **AverageAsync**.
* **CompareTo**: Se traduce a las comparaciones de intervalos. Se usa frecuentemente para las cadenas, debido a que no son comparables en .NET.
* **Take**: Se traduce a la instrucción SQL TOP para limitar los resultados desde una consulta.
* **Funciones matemáticas**: Admite la traducción desde Abs de .NET, Acos, Asin, Atan, Ceiling, Cos, Exp, Floor, Log, Log10, Pow, Round, Sign, Sin, Sqrt, Tan, Truncate a las funciones SQL integradas equivalentes.
* **Funciones de cadena**: Admite la traducción desde Concat .NET, Contains, EndsWith, IndexOf, Count, ToLower, TrimStart, Replace, Reverse, TrimEnd, StartsWith, SubString, ToUpper a las funciones SQL integradas equivalentes.
* **Funciones de matriz**: Admite la traducción desde Concat .NET, Contains y Count a las funciones SQL integradas equivalentes.
* **Funciones de extensión geoespacial**: Admite la traducción desde los métodos auxiliares Distance, Within, IsValid y IsValidDetailed a las funciones SQL integradas equivalentes.
* **Función de extensión de función definida por el usuario**: Admite la traducción desde el método auxiliar UserDefinedFunctionProvider.Invoke a la correspondiente función definida por el usuario.
* **Varios**: Admite la traducción de los operadores condicionales y de fusión. Puede traducir Contains a String CONTAINS, ARRAY_CONTAINS o SQL IN, según el contexto.

### <a name="sql-query-operators"></a>Operadores de consulta SQL
A continuación, vemos algunos ejemplos que ilustran la traducción de algunos de los operadores de consulta de LINQ estándar a consultas de Cosmos DB.

#### <a name="select-operator"></a>Operador Select
La sintaxis es `input.Select(x => f(x))`, donde `f` es una expresión escalar.

**Expresión lambda de LINQ**

    input.Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f



**Expresión lambda de LINQ**

    input.Select(family => family.children[0].grade + c); // c is an int variable


**SQL** 

    SELECT VALUE f.children[0].grade + c
    FROM Families f 



**Expresión lambda de LINQ**

    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });


**SQL** 

    SELECT VALUE {"name":f.children[0].familyName, 
                  "grade": f.children[0].grade + 3 }
    FROM Families f



#### <a name="selectmany-operator"></a>Operador SelectMany
La sintaxis es `input.SelectMany(x => f(x))`, donde `f` es una expresión escalar que devuelve un tipo de colección.

**Expresión lambda de LINQ**

    input.SelectMany(family => family.children);

**SQL** 

    SELECT VALUE child
    FROM child IN Families.children



#### <a name="where-operator"></a>Operador Where
La sintaxis es `input.Where(x => f(x))`, donde `f` es una expresión escalar que devuelve un valor booleano.

**Expresión lambda de LINQ**

    input.Where(family=> family.parents[0].familyName == "Smith");

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith" 



**Expresión lambda de LINQ**

    input.Where(
        family => family.parents[0].familyName == "Smith" && 
        family.children[0].grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"
    AND f.children[0].grade < 3


### <a name="composite-sql-queries"></a>Composición de consultas SQL
Los operadores anteriores pueden ser compuestos para formar consultas más eficaces. Como Cosmos DB admite recopilaciones anidadas, la composición puede concatenarse o anidarse.

#### <a name="concatenation"></a>Concatenación
La sintaxis es `input(.|.SelectMany())(.Select()|.Where())*`. Una consulta concatenada puede empezar por una consulta `SelectMany` opcional seguida de varios operadores `Select` o `Where`.

**Expresión lambda de LINQ**

    input.Select(family=>family.parents[0])
        .Where(familyName == "Smith");

**SQL**

    SELECT *
    FROM Families f
    WHERE f.parents[0].familyName = "Smith"



**Expresión lambda de LINQ**

    input.Where(family => family.children[0].grade > 3)
        .Select(family => family.parents[0].familyName);

**SQL** 

    SELECT VALUE f.parents[0].familyName
    FROM Families f
    WHERE f.children[0].grade > 3



**Expresión lambda de LINQ**

    input.Select(family => new { grade=family.children[0].grade}).
        Where(anon=> anon.grade < 3);

**SQL** 

    SELECT *
    FROM Families f
    WHERE ({grade: f.children[0].grade}.grade > 3)



**Expresión lambda de LINQ**

    input.SelectMany(family => family.parents)
        .Where(parent => parents.familyName == "Smith");

**SQL** 

    SELECT *
    FROM p IN Families.parents
    WHERE p.familyName = "Smith"



#### <a name="nesting"></a>Anidamiento
La sintaxis es `input.SelectMany(x=>x.Q())`, donde Q es un operador `Select`, `SelectMany` o `Where`.

En una consulta anidada, la consulta interna se aplica a cada uno de los elementos de la recopilación externa. Una característica importante es que la consulta interna puede hacer referencia a los campos de los elementos de la recopilación externa como las autocombinaciones.

**Expresión lambda de LINQ**

    input.SelectMany(family=> 
        family.parents.Select(p => p.familyName));

**SQL** 

    SELECT VALUE p.familyName
    FROM Families f
    JOIN p IN f.parents


**Expresión lambda de LINQ**

    input.SelectMany(family => 
        family.children.Where(child => child.familyName == "Jeff"));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = "Jeff"



**Expresión lambda de LINQ**

    input.SelectMany(family => family.children.Where(
        child => child.familyName == family.parents[0].familyName));

**SQL** 

    SELECT *
    FROM Families f
    JOIN c IN f.children
    WHERE c.familyName = f.parents[0].familyName


## <a id="ExecutingSqlQueries"></a>Ejecución de consultas SQL
Cosmos DB expone recursos mediante la API de REST, que puede invocar cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, Cosmos DB ofrece bibliotecas de programación para varios lenguajes populares como .NET, Node.js, JavaScript y Python. La API de REST y las diversas bibliotecas admiten la realización de consultas a través de SQL. El SDK de .NET admite la realización de consultas de LINQ, además del lenguaje SQL.

En los ejemplos siguientes se muestra cómo crear una consulta y enviarla a una cuenta de la base de datos de Cosmos DB.

### <a id="RestAPI"></a>API DE REST
Cosmos DB ofrece un modelo de programación RESTful sobre HTTP. Las cuentas de la base de datos pueden aprovisionarse usando una suscripción de Azure. El modelo de recursos de Cosmos DB consta de un conjunto de recursos en una cuenta de la base de datos, cada uno de los cuales se puede dirigir mediante un URI lógico y estable. En este documento, se hace referencia a un conjunto de recursos como fuente. Una cuenta de la base de datos consta de un conjunto de bases de datos, cada una incluyendo varias recopilaciones que, a su vez, contienen documentos, UDF y otros tipos de recursos.

El modelo de interacción básico con estos recursos se lleva a cabo a través de los verbos de HTTP GET, PUT, POST y DELETE con su interpretación estándar. El verbo POST se usa para la creación de un nuevo recurso, para ejecutar un procedimiento almacenado o para emitir una consulta de Cosmos DB. Las consultas siempre son operaciones de solo lectura sin efectos secundarios.

En los ejemplos siguientes se muestra POST para una consulta de la API de DocumentDB realizada a una recopilación que incluye los dos documentos de ejemplo que hemos revisado hasta el momento. La consulta tiene un filtro sencillo por la propiedad de nombre JSON. Fíjese en el uso de los encabezados `x-ms-documentdb-isquery` y Content-Type: `application/query+json` para denotar que la operación es una consulta.

**Solicitud**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT * FROM Families f WHERE f.id = @familyId",     
        "parameters": [          
            {"name": "@familyId", "value": "AndersenFamily"}         
        ] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 8b4678fa-a947-47d3-8dd3-549a40da6eed
    x-ms-item-count: 1
    x-ms-request-charge: 0.32

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "id":"AndersenFamily",
             "lastName":"Andersen",
             "parents":[  
                {  
                   "firstName":"Thomas"
                },
                {  
                   "firstName":"Mary Kay"
                }
             ],
             "children":[  
                {  
                   "firstName":"Henriette Thaulow",
                   "gender":"female",
                   "grade":5,
                   "pets":[  
                      {  
                         "givenName":"Fluffy"
                      }
                   ]
                }
             ],
             "address":{  
                "state":"WA",
                "county":"King",
                "city":"seattle"
             },
             "_rid":"u1NXANcKogEcAAAAAAAAAA==",
             "_ts":1407691744,
             "_self":"dbs\/u1NXAA==\/colls\/u1NXANcKogE=\/docs\/u1NXANcKogEcAAAAAAAAAA==\/",
             "_etag":"00002b00-0000-0000-0000-53e7abe00000",
             "_attachments":"_attachments\/"
          }
       ],
       "count":1
    }


En el segundo ejemplo se muestra una consulta más compleja que devuelve varios resultados de la combinación.

**Solicitud**

    POST https://<REST URI>/docs HTTP/1.1
    ...
    x-ms-documentdb-isquery: True
    Content-Type: application/query+json

    {      
        "query": "SELECT 
                     f.id AS familyName, 
                     c.givenName AS childGivenName, 
                     c.firstName AS childFirstName, 
                     p.givenName AS petName 
                  FROM Families f 
                  JOIN c IN f.children 
                  JOIN p in c.pets",     
        "parameters": [] 
    }


**Resultados**

    HTTP/1.1 200 Ok
    x-ms-activity-id: 568f34e3-5695-44d3-9b7d-62f8b83e509d
    x-ms-item-count: 1
    x-ms-request-charge: 7.84

    <indented for readability, results highlighted>

    {  
       "_rid":"u1NXANcKogE=",
       "Documents":[  
          {  
             "familyName":"AndersenFamily",
             "childFirstName":"Henriette Thaulow",
             "petName":"Fluffy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Goofy"
          },
          {  
             "familyName":"WakefieldFamily",
             "childGivenName":"Jesse",
             "petName":"Shadow"
          }
       ],
       "count":3
    }


Si los resultados de una consulta no caben en una sola página, la API de REST devuelve un token de continuación a través del encabezado de respuesta `x-ms-continuation-token` . Los clientes pueden paginar los resultados incluyendo el encabezado en resultados posteriores. El número de resultados por página también se puede controlar a través del encabezado numérico `x-ms-max-item-count` . Si la consulta especificada tiene una función de agregación como `COUNT`, la página de consulta puede devolver un valor parcialmente agregado sobre la página de resultados. Los clientes deben realizar una agregación de segundo nivel con estos resultados para generar los resultados finales, por ejemplo, sumar los números devueltos en las páginas individuales para devolver el número total.

Para administrar la directiva de coherencia de datos para consultas, use el encabezado `x-ms-consistency-level` como todas las solicitudes de la API de REST. Para que la sesión sea coherente, también es necesario enviar el último encabezado de cookie `x-ms-session-token` en la solicitud de la consulta. Tenga en cuenta que la directiva de índices de la recopilación consultada también puede afectar a la coherencia de los resultados de la consulta. En el caso de las recopilaciones, con la configuración de la directiva de índices predeterminada, el índice siempre es actual con el contenido del documento y los resultados de la consulta coincidirán con la coherencia elegida para los datos. Si la directiva de índices se suaviza para los perezosos, las consultas pueden devolver resultados obsoletos. Para más información, consulte [Niveles de coherencia de Azure Cosmos DB][consistency-levels].

Si la directiva de índices configurada de la recopilación no puede admitir la consulta especificada, el servidor de Azure Cosmos DB devuelve el error 400 de "solicitud incorrecta". Esto se devuelve para las consultas por rango en rutas de acceso configuradas para búsquedas hash (igualdad) y rutas de acceso excluidas de forma explícita de los índices. Se puede especificar el encabezado `x-ms-documentdb-query-enable-scan` para permitir que la consulta realice un examen si algún índice no está disponible.

### <a id="DotNetSdk"></a>SDK de C# (.NET)
El SDK de .NET admite la realización de consultas de LINQ y SQL. En el ejemplo siguiente se muestra cómo realizar la consulta de filtro simple incluida anteriormente en este documento.

    foreach (var family in client.CreateDocumentQuery(collectionLink, 
        "SELECT * FROM Families f WHERE f.id = \"AndersenFamily\""))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    SqlQuerySpec query = new SqlQuerySpec("SELECT * FROM Families f WHERE f.id = @familyId");
    query.Parameters = new SqlParameterCollection();
    query.Parameters.Add(new SqlParameter("@familyId", "AndersenFamily"));

    foreach (var family in client.CreateDocumentQuery(collectionLink, query))
    {
        Console.WriteLine("\tRead {0} from parameterized SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery(collectionLink)
        where f.Id == "AndersenFamily"
        select f))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in client.CreateDocumentQuery(collectionLink)
        .Where(f => f.Id == "AndersenFamily")
        .Select(f => f))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


En este ejemplo se comparan dos propiedades para igualdad en cada documento y se usan proyecciones anónimas. 

    foreach (var family in client.CreateDocumentQuery(collectionLink,
        @"SELECT {""Name"": f.id, ""City"":f.address.city} AS Family 
        FROM Families f 
        WHERE f.address.city = f.address.state"))
    {
        Console.WriteLine("\tRead {0} from SQL", family);
    }

    foreach (var family in (
        from f in client.CreateDocumentQuery<Family>(collectionLink)
        where f.address.city == f.address.state
        select new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ query", family);
    }

    foreach (var family in
        client.CreateDocumentQuery<Family>(collectionLink)
        .Where(f => f.address.city == f.address.state)
        .Select(f => new { Name = f.Id, City = f.address.city }))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", family);
    }


En el ejemplo siguiente se muestran combinaciones, expresadas a través de SelectMany de LINQ.

    foreach (var pet in client.CreateDocumentQuery(collectionLink,
          @"SELECT p
            FROM Families f 
                 JOIN c IN f.children 
                 JOIN p in c.pets 
            WHERE p.givenName = ""Shadow"""))
    {
        Console.WriteLine("\tRead {0} from SQL", pet);
    }

    // Equivalent in Lambda expressions
    foreach (var pet in
        client.CreateDocumentQuery<Family>(collectionLink)
        .SelectMany(f => f.children)
        .SelectMany(c => c.pets)
        .Where(p => p.givenName == "Shadow"))
    {
        Console.WriteLine("\tRead {0} from LINQ lambda", pet);
    }



El cliente de .NET se itera automáticamente a través de todas las páginas de resultados de la consulta de los bloques foreach, como se muestra anteriormente. Las opciones de consulta especificadas en la sección de la API de REST también están disponibles en el SDK de .NET mediante las clases `FeedOptions` and `FeedResponse` del método CreateDocumentQuery. El número de páginas se puede controlar con el valor `MaxItemCount` . 

Puede controlar expresamente la paginación creando `IDocumentQueryable` mediante el objeto `IQueryable`; después, leyendo los` ResponseContinuationToken` valores y devolviéndolos como `RequestContinuationToken` en `FeedOptions`. `EnableScanInQuery` para habilitar los exámenes cuando la directiva de indexación configurada no pueda admitir la consulta. Para las colecciones con particiones, puede usar `PartitionKey` para ejecutar la consulta en una sola partición (aunque Cosmos DB puede extraer automáticamente esto a partir del texto de consulta), y `EnableCrossPartitionQuery` para ejecutar consultas que se deben ejecutar en varias particiones. 

Consulte los [ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net) para obtener más casos que contengan consultas. 

> [!NOTE]
> Para realizar consultas de agregación, necesitará el SDK 1.12.0 o superior. No se admite la compatibilidad de LINQ para las funciones de agregación, pero estará disponible en .NET SDK 1.13.0.
>

### <a id="JavaScriptServerSideApi"></a>API del servidor de JavaScript
Cosmos DB proporciona un modelo de programación para ejecutar una lógica de aplicación basada en JavaScript directamente en las recopilaciones usando procedimientos y desencadenadores almacenados. La lógica de JavaScript registrada en un nivel de recopilación puede emitir operaciones de base de datos en las operaciones de los documentos de la recopilación especificada. Estas operaciones se incluyen en transacciones ACID ambientales.

En el ejemplo siguiente se muestra cómo usar queryDocuments en la API del servidor de JavaScript para realizar consultas a partir de procedimientos y desencadenadores almacenados dentro.

    function businessLogic(name, author) {
        var context = getContext();
        var collectionManager = context.getCollection();
        var collectionLink = collectionManager.getSelfLink()

        // create a new document.
        collectionManager.createDocument(collectionLink,
            { name: name, author: author },
            function (err, documentCreated) {
                if (err) throw new Error(err.message);

                // filter documents by author
                var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
                collectionManager.queryDocuments(collectionLink,
                    filterQuery,
                    function (err, matchingDocuments) {
                        if (err) throw new Error(err.message);
    context.getResponse().setBody(matchingDocuments.length);

                        // Replace the author name for all documents that satisfied the query.
                        for (var i = 0; i < matchingDocuments.length; i++) {
                            matchingDocuments[i].author = "George R. R. Martin";
                            // we don't need to execute a callback because they are in parallel
                            collectionManager.replaceDocument(matchingDocuments[i]._self,
                                matchingDocuments[i]);
                        }
                    })
            });
    }

## <a id="References"></a>Referencias
1. [Introducción a Azure Cosmos DB][introduction]
2. [Especificación de SQL de Azure Cosmos DB](http://go.microsoft.com/fwlink/p/?LinkID=510612)
3. [Ejemplos de .NET de Azure Cosmos DB](https://github.com/Azure/azure-documentdb-net)
4. [Niveles de coherencia de Azure Cosmos DB][consistency-levels]
5. SQL ANSI 2011 [http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681](http://www.iso.org/iso/iso_catalogue/catalogue_tc/catalogue_detail.htm?csnumber=53681)
6. JSON [http://json.org/](http://json.org/)
7. Especificación de JavaScript [http://www.ecma-international.org/publications/standards/Ecma-262.htm](http://www.ecma-international.org/publications/standards/Ecma-262.htm) 
8. LINQ [http://msdn.microsoft.com/library/bb308959.aspx](http://msdn.microsoft.com/library/bb308959.aspx) 
9. Técnicas de evaluación de consultas para bases de datos de gran tamaño [http://dl.acm.org/citation.cfm?id=152611](http://dl.acm.org/citation.cfm?id=152611)
10. Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994
11. Lu, Ooi, Tan, Query Processing in Parallel Relational Database Systems, IEEE Computer Society Press, 1994.
12. Christopher Olston, Benjamin Reed, Utkarsh Srivastava, Ravi Kumar, Andrew Tomkins: Pig Latin: A Not-So-Foreign Language for Data Processing, SIGMOD 2008.
13. G. Graefe. El marco de cascadas para la optimización de consultas. IEEE Data Eng. Bull., 18(3): 1995.

[1]: ./media/documentdb-sql-query/sql-query1.png
[introduction]: documentdb-introduction.md
[consistency-levels]: documentdb-consistency-levels.md

