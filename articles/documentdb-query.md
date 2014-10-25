<properties title="Query with Azure DocumentDB" pageTitle="Query with DocumentDB | Azure" description="DocumentDB's SQL query language supports a subset of ANSI SQL grammar and adds document-oriented support. Queries are served through up-to-date indexes that don't require index management."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Consulta con Base de datos de documentos de Azure

## Motivación

El lenguaje de consulta SQL de Base de datos de documentos admite un subconjunto de gramática SQL ANSI y agrega compatibilidad orientada a documentos en forma de estructuras de datos anidadas, matrices y construcción de objetos. Las consultas se sirven de forma eficiente a través de índices de Base de datos de documentos, que se mantienen automáticamente actualizados en todas las propiedades de documentos sin la sobrecarga de la administración de índices. Los desarrolladores también pueden aprovechar la potencia completa de JavaScript dentro de las consultas a través de funciones definidas por el usuario (UDF).

## ¿Por qué SQL?

Base de datos de documentos admite SQL con el objeto de proporcionar una interfaz sencilla y abordable para la consulta de datos. Como lenguaje, SQL es sencillo desde el punto de vista estructural, pero potente y ampliamente utilizado por los desarrolladores de aplicaciones. Base de datos de documentos está profundamente comprometido con plataformas y estándares abiertos como HTTP, JSON y JavaScript, y ahora SQL.
La consulta SQL se admite a través de la API REST y también de los SDK de cliente para .NET, Node.js y JavaScript. También se admiten consultas SQL en procedimientos almacenados y desencadenadores en la API JavaScript de servidor. Además de consultas SQL, Base de datos de documentos admite también la consulta a través de LINQ en el SDK para .NET.

# Tipo de datos

En Base de datos de documentos, el formato de datos de los documentos y metadatos se expresa en Notación de objetos JavaScript (JSON). JSON se utiliza ampliamente debido al crecimiento de las aplicaciones JavaScript y su evolución a un formato de intercambio de datos independiente de la plataforma. Como JSON es autoexplicativo, es también muy adecuado para bases de datos sin esquemas.

Los tipos de datos primitivos admitidos en consultas de Base de datos de documentos son los mismos que en JSON. JSON presenta un sistema de tipos sencillo que consta de:

-   Cadenas
-   Números (doble precisión de IEEE754)
-   Booleanos: verdadero y falso
-   Valores Null

Tipos de datos más complejos se pueden representar tanto en JSON como en Base de datos de documentos mediante la creación de objetos anidados con el operador { } y matrices con el operador [ ].

# Gramática de consulta

Con el lenguaje SQL de Base de datos de documentos, los usuarios pueden recuperar datos de documentos JSON mediante instrucciones **SELECT**. Aquí se muestra una instrucción SQL de ejemplo en una colección que contiene publicaciones en una red social.

     SELECT p.message, p.user.id AS user_id, p.tags[0] AS first_tag 
     FROM posts p 
     WHERE p.type = "Comment"

La salida de una consulta es una matriz de fragmentos de documentos JSON, una para cada documento que coincide con las restricciones especificadas.

    {
      "Documents": [
        {
            "message": "JSON rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        },
        {
            "message": "SQL rocks!"
            "user_id": "@documentdb"
            "first_tag": "#documentstore"
        }
      ]
    }

La gramática formal completa (BNF) del lenguaje de consulta SQL se incluye al final de este documento.

# Cláusula FROM

La cláusula **FROM** es normalmente el nombre de la colección actual donde se ejecuta la consulta. Por ejemplo, esta cláusula selecciona todos los documentos de la colección llamada "publicaciones". Si el nombre es correcto, se devuelve un error al usuario.

    SELECT * 
    FROM posts

La palabra clave **ROOT** se puede usar en lugar de hacer referencia a la colección por nombre, dado que a través del SDK o de la API REST, las consultas se pueden dirigir al ámbito de una colección específica.

    SELECT * 
    FROM ROOT

Se puede establecer el alias de las propiedades de salida de FROM. Esto resulta de utilidad al usar varios orígenes FROM mediante **JOIN**. Este tema se tratará más adelante.

    SELECT P.* 
    FROM posts P

También es posible consultar desde una propiedad anidada. Por ejemplo, si los documentos de publicaciones contienen un subdocumento para un usuario que creó la publicación, es posible realizar la consulta mediante la siguiente sintaxis.

    SELECT U.* 
    FROM posts.user U

El origen de la instrucción FROM puede ser también una matriz escalar (conjunto ordenado).

    SELECT E
    FROM ["documentdb", "json", "sql"] E

También es posible omitir la instrucción FROM completamente mientras se evalúan expresiones escalares.

`SELECT "documentdb"`

# Cláusula WHERE (filtrado)

El lenguaje SQL de Base de datos de documentos admite una cláusula **WHERE** opcional que filtra el conjunto de resultados según las condiciones especificadas que el documento debe satisfacer. La expresión de filtro puede contener comparaciones lógicas con cualquier propiedad o ruta anidada de los documentos.

Por ejemplo, cualquier propiedad como message se puede usar en las consultas. El filtrado se realiza de forma eficiente mediante el índice automático de Base de datos de documentos.

    SELECT * 
    FROM posts p 
    WHERE p.message = "DocumentDB now supports SQL!"

Se pueden usar propiedades anidadas en el filtrado mediante la notación con punto. Las rutas pueden ser arbitrariamente largas dependiendo del esquema de los documentos.

    SELECT * 
    FROM posts p 
    WHERE p.user.name = "Azure DocumentDB"

Se puede hacer referencia a valores individuales dentro de las matrices mediante el operador de eliminación de referencias de matrices. Se admite el desenredo de matrices mediante la palabra clave **IN** (más adelante se comentará).

    SELECT * 
    FROM posts p 
    WHERE p.views[0] = 100

Se admiten comparaciones de rangos (\>, \<. \>=, \<=, !=) para valores numéricos. Los números también se pueden comparar con el resultado de una expresión aritmética como se muestra a continuación.

    SELECT * 
    FROM posts p 
    WHERE p.views[0] > 100 * 2

Los operadores lógicos se pueden combinar mediante los operadores AND, OR y NOT como en SQL normal. Estos se procesan de manera eficiente a través de la intersección de los índices de las propiedades especificadas.

    SELECT * 
    FROM posts p 
    WHERE ((p.user.name = "Azure DocumentDB")
    OR (p.views[0] = 100 AND p.message = "DocumentDB now supports SQL!"))

## Gestión de propiedades que faltan y nulas

NULL se admite como palabra clave, y las propiedades se pueden comparar con valores Null al igual que otros escalares.

    SELECT * 
    FROM posts p 
    WHERE p.message IS NULL

Dado que el esquema de los documentos puede cambiar rápidamente o no conocerse de antemano, el lenguaje admite también operadores especiales para comprobar las propiedades que faltan, a través del operador ISUNDEFINED, que es parecido a **undefined** en JavaScript .

    SELECT * 
    FROM posts p 
    WHERE p.message IS UNDEFINED

> Nota: parecido a JavaScript, = undefined siempre devuelve false.

# Cláusula SELECT (proyección)

La cláusula **SELECT** se puede usar para extraer determinadas propiedades de la consulta. El operador asterisco (\*) devuelve los documentos completos que coinciden con la consulta.

    SELECT * 
    FROM posts

Los campos especificados se pueden extraer en la cláusula SELECT. Los campos pueden ser atómicos (cadenas, números, booleanos) o complejos (matrices, objetos JSON). Los tipos complejos de la cláusula SELECT como "user" en el ejemplo siguiente devuelven el objeto JSON entero bajo la propiedad. Las propiedades de la cláusula SELECT deben tener nombres o alias únicos.

    SELECT posts.id, posts.user
    FROM posts

> Nota: como JSON está sin ordenar, las propiedades individuales del documento no se devuelven en ningún orden específico.

> Nota: los nombres de campos se deben cualificar explícitamente. Puesto que los documentos no tienen esquema fijo, este es necesario en el tiempo de ejecución de la consulta para realizar el enlace correcto.

Otra manera de acceder a las propiedades es usar la sintaxis de búsqueda de diccionario como se muestra a continuación:

    SELECT posts.id, posts["user"], posts["from"]
    FROM posts

> Nota: la sintaxis de búsqueda de diccionario se debe usar para aplicar caracteres de escape en nombres de propiedades que usan una palabra clave reservada como FROM en el ejemplo anterior.

## Evaluación de expresiones

Las expresiones escalares también se pueden usar con expresiones. Cuando no hay ningún nombre especificado, se usa un nombre generado automáticamente como marcador de posición ($1, $2, $3, etc.).

    SELECT ((2 + 11 % 7) – 2)/3
    FROM posts

Las siguientes expresiones se pueden usar dentro de expresiones de consulta SQL. Las operadores están fuertemente tipados como en SQL. Por ejemplo, igual a (=) tiene una igualdad fuerte, lo que significa que 5 != "5".

| Operadores aritméticos    | +, -, \*, /, % (Módulo)                                |
|---------------------------|--------------------------------------------------------|
| Operadores lógicos        | AND, OR, NOT                                           |
| Operadores bit a bit      | & (AND bit a bit), | (OR bit a bit), ^ (XOR bit a bit) |
| Operadores de cadena      | || (Concatenar)                                        |
| Operadores de comparación | =, !=, \>, \<, \>=, \<=                                |

## Transformación de JSON

Las consultas SQL no solo puede devolver documentos completos, también fragmentos JSON; la palabra clave **VALUE** se puede usar para esto. Por ejemplo, el siguiente documento devolverá "2" en lugar de {"id": “2”}.

    SELECT VALUE posts.id
    FROM posts

Se pueden construir expresiones JSON más complejas igual que en JavaScript mediante los operadores {} y []. Esto aporta flexibilidad a las consultas para transformar la forma de los conjuntos de resultados.

    SELECT {"user_name": p.user.name, "recent_views": [p.views[0], p.views[1]]}
    FROM posts p

> Nota: los valores pueden ser cualquier expresión válida, pero las etiquetas deben ser literales (cadenas).

# Combinaciones e iteración

En las bases de datos de documentos, los datos a los que se hace referencia están incrustados como subdocumentos o dentro de las matrices. Es por esto que el lenguaje SQL admite autocombinaciones (dentro de documentos) mediante la palabra clave **JOIN**.

    SELECT p.id, tag 
    FROM posts p 
    JOIN tag IN p.tags

> Nota: las combinaciones son combinaciones cruzadas pero como los datos están incrustados, actúan como combinaciones internas.

En el ejemplo, el operador **IN** se usa para iterar sobre todos los elementos de un origen. El origen de una cláusula IN puede ser una matriz o un objeto. Cuando se usa con un objeto, el iterador pasa por cada propiedad. IN se puede usar también directamente como se muestra a continuación. IN se puede usar también con literales de matriz.

    SELECT tag 
    FROM tag IN posts.tags

Se pueden usar varias combinaciones dentro de una instrucción SQL, como se muestra a continuación:

    SELECT p.id, tag, liked
    FROM posts p 
    JOIN tag IN p.tags
    JOIN liked IN p.likes  

# Funciones definidas por el usuario (UDF)

Las consultas de Base de datos de documentos admiten extensiones programables en forma de funciones definidas por el usuario JavaScript. Cada colección puede tener una o varias **UDF** guardadas que transforman los fragmentos JSON, y hacen referencia a ellos por el nombre directamente en los scripts.

Por ejemplo, mediante las funciones definidas por el usuario de creación, una función "regex\_match" se puede definir como:

    function regex_match(input, pattern) {
        getContext().getResponse().setBody(pattern.test(input));
    }

Luego se puede hacer referencia en las consultas como en este ejemplo:

    SELECT *
    FROM posts p
    WHERE regex_match(p.message, "doc.*db") = true

> Nota: las UDF solo tienen acceso a los parámetros de entrada. No se admite ninguna operación de almacenamiento de Base de datos de documentos (lectura, escritura, consulta, etc.) dentro de las UDF.

> Nota: actualmente, solo se puede usar una UDF dentro de una consulta.

Las UDF no solo pueden procesar y devolver literales, también fragmentos JSON. Por ejemplo, una función "array\_count" se puede definir como:

    function array_count(input) {
        getContext().getResponse().setBody(input.length);
    }

Y luego usarse en las consultas para hallar el tamaño de una matriz dentro de un documento:

    SELECT *
    FROM posts p
    WHERE array_count(p.likes) > 5

Las UDF se pueden especificar también en la cláusula SELECT o en la cláusula FROM de una consulta. Por ejemplo:

    SELECT array_count(p.likes) AS count
    FROM posts p

Para obtener más información sobre la creación y administración de UDF, consulte la documentación de programación de JavaScript.

# Paginación

Cada ejecución de una consulta devuelve un lote de resultados acorde al tamaño de página configurado por el cliente. Para leer todos los resultados de una consulta, las aplicaciones deben paginar cada conjunto de resultados hasta que no queden más datos que leer. Por ejemplo, para leer no más de 10 documentos, los clientes pueden modificar el tamaño de página a 10 a fin de limitar el número máximo de documentos devueltos. Tenga en cuenta que las consultas podrían devolver un número de resultados inferior al tamaño de página o incluso ningún resultado en el caso de algunas consultas. Para leer todos los resultados de una consulta, los clientes deben recuperar el siguiente lote mediante el token de continuación de respuesta hasta que esté vacío.

    // Fetch pages of results up to 10 at a time. FeedOptions is optional
    DocumentServiceQuery<Database> query = (
        from db in client.CreateDatabaseQuery(new FeedOptions { MaxItemCount = 10 })
        where db.Name == dbName
        select db).AsDocumentServiceQuery();

    while (query.HasMoreResults)
    {
        databases.AddRange(await query.ExecuteNextAsync<Database>());
    }

Esto no se implementa en la gramática SQL como una palabra clave TOP o LIMIT, puesto que la funcionalidad ya está disponible a través de los tokens de continuación en la API REST o el SDK de cliente subyacente. Para obtener más información y ejemplos, consulte la documentación de la API REST en los documentos GET o los métodos ReadFeed en los SDK.

# Comportamiento de la coherencia de las consultas

Base de datos de documentos admite cuatro niveles de coherencia ajustables por el desarrollador: alta, de uso vinculado, sesión y ocasional. Las consultas ofrecen la misma garantía que los niveles de coherencia. De manera predeterminada, se tiene la garantía de que los resultados de una consulta coinciden con el nivel de coherencia solicitado por el cliente. El índice de Base de datos de documentos está estructurado en registros, y está diseñado para mantenerse siempre actualizado y coherente con los datos sin importar el volumen. Cuando se insertan o actualizan documentos, inmediatamente están disponibles en los resultados de consulta.

Para las aplicaciones que necesitan coherencia ocasional, la directiva de indexación se puede configurar de manera opcional para usar la indexación diferida. En este caso, el índice se actualiza de una manera coherente de forma ocasional cada vez que la colección está inactiva. Para resumir, esta es la tabla de comportamiento de coherencia de las consultas con diferentes configuraciones de cuentas de base de datos.

| Coherencia de datos | Directiva de indexación | Comportamiento de consultas |
|---------------------|-------------------------|-----------------------------|
| Alta                | Coherente               | Alta                        |
| De uso vinculado    | Coherente               | De uso vinculado            |
| Sesión              | Coherente               | Sesión                      |
| Ocasional           | Coherente               | Ocasional                   |
| Alta                | Diferida                | Ocasional                   |
| De uso vinculado    | Diferida                | Ocasional                   |
| Sesión              | Diferida                | Ocasional                   |
| Ocasional           | Diferida                | Ocasional                   |

Para obtener información adicional, consulte la documentación sobre directivas y configuración de indexación.

# Las API y los SDK

Las consultas se pueden ejecutar con la API REST, los SDK del cliente y la API de JavaScript del servidor mediante la gramática SQL.

## Consulta con la API REST

Las aplicaciones pueden publicar consultas en colecciones mediante la API REST. El siguiente código se debe incluir en las solicitudes de consulta:

-   Encabezado x-ms-documentdb-isquery: true para indicar que es una consulta
-   Encabezado Content-Type: application/sql para indicar que se usa el lenguaje SQL
-   Cuerpo que contiene la instrucción SELECT
    <!-- -->

    POST .../docs/executeQuery HTTP/1.1
    authorization: ...
    x-ms-continuation:
    x-ms-activity-id: 82342881-769e-4113-a662-a85c7617ed5b
    x-ms-date: Fri, 30 May 2014 22:46:13 GMT
    Match:
    x-docdb-resource-id: 9MEKcum9C2g=
    x-docdb-entity-id:
    x-ms-documentdb-isquery: True
    Cache-Control: no-cache
    x-ms-version: 2014-02-25
    User-Agent: Microsoft.Azure.Documents.Client/1.0.0.0
    Content-Type: application/sql
    Host: ...
    Content-Length: 59
    Expect: 100-continue

    SELECT b.title FROM books b WHERE b.title = 'War and Peace'

> Nota: no se admiten consultas con GET o mediante cadenas de consulta.

## Consulta con el SDK para .NET

El SDK para .NET admite consultas con el método CreateDocumentQuery, que admite consultas SQL como cadenas. La salida de la consulta es una interfaz IQueryable que se puede procesar mediante LINQ en el lado del cliente.

    IQueryable<dynamic> results = client.CreateDocumentQuery(collectionId).AsSQL<dynamic>(
       "SELECT b.title FROM books b WHERE b.title = 'War and Peace'");

Para obtener información adicional, consulte la documentación del SDK para .NET

## Consulta dentro de procedimientos almacenados y desencadenadores

La API JavaScript de servidor para procedimientos almacenados y desencadenadores admite también consultas con SQL.

    collection.queryDocuments(collection.GetSelfLink(),
    "SELECT b.title FROM books b WHERE b.title = 'War and Peace'",
    callback);

Para obtener información adicional, consulte la documentación del SDK de servidor para JavaScript.

# Apéndice A: Sintaxis SQL

Los siguientes diagramas muestran la gramática SQL formal del lenguaje de consulta de Base de datos de documentos:

![][]

![][1]

![][2]

![][3]

![][4]

![][5]

![][6]

![][7]

![][8]

![][9]

![][10]

![][11]

![][12]

![][13]

![][14]

![][15]

![][16]

  []: ./media/documentdb-query/query1.png
  [1]: ./media/documentdb-query/query2.png
  [2]: ./media/documentdb-query/query3.png
  [3]: ./media/documentdb-query/query4.png
  [4]: ./media/documentdb-query/query5.png
  [5]: ./media/documentdb-query/query6.png
  [6]: ./media/documentdb-query/query7.png
  [7]: ./media/documentdb-query/query8.png
  [8]: ./media/documentdb-query/query9.png
  [9]: ./media/documentdb-query/query10.png
  [10]: ./media/documentdb-query/query11.png
  [11]: ./media/documentdb-query/query12.png
  [12]: ./media/documentdb-query/query13.png
  [13]: ./media/documentdb-query/query14.png
  [14]: ./media/documentdb-query/query15.png
  [15]: ./media/documentdb-query/query16.png
  [16]: ./media/documentdb-query/query17.png
