<properties 
    pageTitle="Directivas de indexación de DocumentDB | Azure" 
    description="Obtenga información acerca de cómo funciona la indexación en DocumentDB y sobre cómo configurar y cambiar la directiva de indexación." 
    services="documentdb" 
    documentationCenter="" 
    authors="mimig1" 
    manager="jhubbard" 
    editor="monicar"/>

<tags 
    ms.service="documentdb" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="data-services" 
    ms.date="07/19/2015" 
    ms.author="mimig"/>


# Directivas de indexación de DocumentDB

DocumentDB es un sistema de bases de datos libre de esquemas. No asume ni requiere ningún esquema para los documentos de JSON que indexa. Esto permite definir y establecer iteraciones en los modelos de datos de aplicación rápidamente. A medida que se agregan documentos a una colección, DocumentDB indexará automáticamente las propiedades de los documentos para que estén disponibles para su consulta. La indexación automática también permite almacenar los tipos de documentos heterogéneos.

La indexación automática de documentos es posible gracias a las técnicas de escritura optimizada y de mantenimiento del índice estructurado de registros y sin bloqueos. DocumentDB admite un volumen sostenido de escrituras rápidas mientras se sigue atendiendo consultas coherentes.

El subsistema de indexación de DocumentDB está diseñado para ofrecer lo siguiente:

-  Consultas jerárquicas y relacionales eficaces y sofisticadas sin necesidad de ninguna definición de esquema o índice.
-  Resultados de consulta coherente mientras se controla un volumen sostenido de escrituras. Para cargas de trabajo de rendimiento de escritura alto con consultas coherentes, el índice se actualiza de forma incremental, eficaz y en línea mientras se controla un volumen sostenido de escrituras.
- Eficacia de almacenamiento. Para obtener rentabilidad, se enlaza la sobrecarga de almacenamiento en el disco del índice y es predecible.
- Multiempresa. Las actualizaciones del índice se realizan según la asignación de recursos de sistema asignados por la recopilación de DocumentDB. 

Es posible utilizar la directiva de indexación automática predeterminada para la mayoría de las aplicaciones, ya que ofrece la máxima flexibilidad y proporciona un equilibrio adecuado entre rendimiento y eficacia en el almacenamiento. Por otro lado, especificar una directiva de indexación personalizada permite lograr un equilibrio granular entre el rendimiento de las consultas, el rendimiento en la escritura y la sobrecarga de almacenamiento del índice.

Por ejemplo, si se excluyen determinados documentos o rutas de acceso de los documentos de la indexación, es posible reducir el espacio de almacenamiento utilizado para la indexación, así como el tiempo de inserción para el mantenimiento del índice. Es posible cambiar el tipo de índice para adaptarlo a las consultas, o bien incrementar la precisión del índice en bytes para mejorar el rendimiento de las consultas. Este artículo describe las distintas opciones de indexación disponibles en DocumentDB, así como la personalización de las directivas de indexación a sus cargas de trabajo.

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo admite DocumentDB la indexación de todas las propiedades de forma predeterminada?
- ¿Cómo puedo reemplazar las propiedades para incluirlas o excluirlas de la indexación?
- ¿Cómo puedo configurar el índice para las actualizaciones finales?
- ¿Cómo puedo configurar la indexación para realizar consultas Order By o intervalo?

## Funcionamiento de la indexación de DocumentDB

La indexación en DocumentDB aprovecha el hecho de que la gramática de JSON permite que los documentos se **representen como árboles**. Para representar un documento JSON como un árbol, es necesario crear un nodo raíz ficticio que actúe como elemento primario para el resto de nodos reales del documento que dependen de dicho nodo ficticio. Cada etiqueta, que incluye los índices de la matriz en un documento JSON, se convierte en un nodo del árbol. La ilustración siguiente muestra un ejemplo de documento JSON y de su representación de árbol correspondiente.

![Directivas de indexación](media/documentdb-indexing-policies/image001.png)

Por ejemplo, la propiedad JSON`{"headquarters": "Belgium"}` en el ejemplo anterior corresponde a la ruta de acceso `/headquarters/Belgium`. La matriz JSON `{"exports": [{"city": “Moscow"}, {"city": Athens"}]}`corresponde a las rutas de acceso `/exports/[]/city/Moscow` y `/exports/[]/city/Athens`.

>[AZURE.NOTE]La representación de la ruta de acceso difumina el límite entre el esquema/la estructura y los valores de instancia de los documentos, lo que permite a DocumentDB usar un modelo libre de esquemas.

En DocumentDB, los documentos se organizan en recopilaciones que se pueden consultar mediante SQL o que se procesan en el ámbito de una sola transacción. Cada recopilación puede configurarse con su propia directiva de indexación expresada en términos de rutas de acceso. En la siguiente sección, analizaremos cómo configurar el comportamiento de la indexación de una recopilación de DocumentDB.

## Configuración de la directiva de indexación de una colección

Para cada colección de DocumentDB, puede configurar las siguientes opciones:

- Modo de indexación: **Homogéneas**, **Diferidas** (para actualizaciones asincrónicas) o **Ninguna** (acceso solo en función de "id")
- Rutas incluidas y excluidas: elija qué rutas de JSON se incluyen y excluyen
- Tipo de índice: **Hash** (para consultas de igualdad), **intervalo** (para la igualdad, intervalo y consultas Order By con mayor almacenamiento)
- Precisión de índice: 1-8 o Máximo (-1) para un equilibrio entre el rendimiento y el almacenamiento
- Automático: **true** o **false** para habilitar o **manual** (participara con cada inserción)

En el siguiente fragmento de código de .NET se muestra cómo establecer una directiva de indexación personalizada durante la creación de una colección. A continuación establecemos la directiva con el índice de intervalo para las cadenas y números en la precisión máxima. Esta directiva nos permite ejecutar consultas Order By en cadenas.

    var collection = new DocumentCollection { Id = "myCollection" };
    
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, collection);   


>[AZURE.NOTE]El esquema JSON de la directiva de indexación ha cambiado con el lanzamiento de la API de REST versión 2015-06-03 para admitir índices de intervalo en cadenas. El SDK de .NET 1.2.0 y los SDK de Java, Python y Node.js 1.1.0 admiten el nuevo esquema de directiva. Los SDK anteriores usan la API de REST versión 2015-04-08 y admiten el esquema anterior de la directiva de indexación.
>
>De forma predeterminada, DocumentDB indexa todas las propiedades de cadena en los documentos de forma coherente con un índice Hash y las propiedades numéricas con un índice de intervalo.

### Modos de indexación

Puede elegir entre las actualizaciones de índices sincrónicas (**Homogéneas**), asincrónicas (**Diferida**) y sin actualización (**Ninguna**). De forma predeterminada, el índice se actualiza de forma sincrónica en cada acción de inserción, sustitución o eliminación realizada en un documento de la colección. Esto permite que las consultas tengan el mismo nivel de homogeneidad que el de las lecturas de los documentos sin demoras en la actualización de los índices.

Aunque DocumentDB está optimizada para escritura y admite volúmenes constantes de escrituras de documentos junto con capacidades sincrónicas de mantenimiento del índice, es posible configurar determinadas recopilaciones para que su índice se actualice de forma diferida. La indexación diferida es perfecta para escenarios en los que los datos se escriben en ráfagas y desea amortizar el trabajo necesario para indexar el contenido durante un período de tiempo más prolongado. Esto permite utilizar de forma eficaz el rendimiento aprovisionado y atender a las solicitudes de escritura en las horas punta con una latencia mínima. Con la indexación diferida activada, los resultados de la consulta serán coherentes con el tiempo independientemente del nivel de coherencia configurado para la cuenta de base de datos.

El siguiente programa de ejemplo muestra cómo crear una colección de DocumentDB mediante el SDK de .NET con la indización automática coherente en todas las inserciones de documentos.


     // Default collection creates a hash index for all string and numeric    
     // fields. Hash indexes are compact and offer efficient
     // performance for equality queries.
     
     var collection = new DocumentCollection { Id ="defaultCollection" };
     
     // Optional. Override Automatic to false for opt-in indexing of documents.
     collection.IndexingPolicy.Automatic = true;
     
     // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
     // collections. Queries might return stale results with Lazy indexing.
     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;
     
     collection = await client.CreateDocumentCollectionAsync(database.SelfLink, collection);

### Rutas de acceso del índice

En los documentos, puede elegir qué rutas de acceso se deben incluir o excluir del índice. Esto puede mejorar el rendimiento de escritura y reducir el almacenamiento necesario para índice en escenarios en los que se conocen de antemano los patrones de consulta.

Las rutas de acceso del índice comenzar con la raíz (/) y normalmente finalizan con el operador comodín ?, que indica que hay varios posibles valores para el prefijo. Por ejemplo, para atender a la consulta SELECT * FROM Families F WHERE F.familyName = "Andersen", debe incluir una ruta de acceso del índice para /familyName/? en la directiva de índice de la recopilación.

Las rutas de acceso del índice también pueden usar el operador comodín * para especificar el comportamiento de las rutas de acceso de forma recursiva en el prefijo. Por ejemplo, /payload/* puede usarse para excluir de la indexación de todo el contenido de la propiedad payload.

Estos son los patrones comunes para especificar las rutas de acceso del índice:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Ruta de acceso</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descripción/caso de uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /*
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso predeterminada de la recopilación. Recursiva. Se aplica a la totalidad del árbol de documentos.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /prop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para atender las consultas como la siguiente (con tipos hash o de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>                
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso del índice para todas las rutas de acceso situadas en la etiqueta especificada. Funciona con las siguientes consultas
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop.nextprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para servir a la iteración y a las consultas JOIN frente a las matrices de valores escalares como ["a", "b", "c"]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /props/[]/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para servir a la iteración y a las consultas JOIN frente a las matrices de objetos como [{subprop: "a"}, {subprop: "b"}]:
                </p>
                <p>
                    SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"
                </p>
                <p>
                    SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"
                </p>
            </td>
        </tr>        
        <tr>
            <td valign="top">
                <p>
                    /prop/subprop/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para atender consultas (con las de tipo hash o de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop > 5
                </p>
                <p>
                    SELECT * FROM collection c ORDER BY c.prop.subprop
                </p>                
            </td>
        </tr>
    </tbody>
</table>

>[AZURE.NOTE]Al configurar las rutas de acceso de índice personalizado, es necesario especificar la regla de indexación predeterminada para el árbol de todo el documento indicada mediante la ruta de acceso especial "/".

En el ejemplo siguiente se configura una ruta de acceso específica con indexación de intervalo y un valor de precisión personalizado de 20 bytes:

    var collection = new DocumentCollection { Id = "rangeSinglePathCollection" };    
    
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/Title/?", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = 20 } } 
            });

    // Default for everything else
    collection.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*" ,
            Indexes = new Collection<Index> {
                new HashIndex(DataType.String) { Precision = 3 }, 
                new RangeIndex(DataType.Number) { Precision = -1 } 
            }
        });
        
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);

### Tipos de datos de índice, variantes y precisiones

Ahora que hemos echado un vistazo a cómo especificar las rutas de acceso, echemos un vistazo a las opciones que podemos usar para configurar la directiva de indexación en una ruta de acceso. Puede especificar una o más definiciones de indexación para cada ruta de acceso:

- Tipo de datos: **Cadena** o **Número** (solo puede contener una entrada por tipo de datos y por ruta de acceso)
- Tipo de índice: **Hash** (consultas de igualdad) o **intervalo** (igualdad, intervalo o consultas Order By)
- Precisión: 1-8 o -1 (precisión máxima) para números, 1-100 (precisión máxima) para cadenas

#### Tipo de índice

DocumentDB admite dos variantes de índice por cada par de tipo de datos y ruta de acceso.

- **Hash** admite consultas de igualdad eficaces. Para la mayoría de los casos de uso, los índices hash no requieren una precisión mayor que el valor predeterminado de 3 bytes.
- **Intervalo** admite consultas de igualdad eficientes, las consultas por intervalo (con >, <, >=, <=, !=) y las consultas Order By. De forma predeterminada, las consultas Order By también requieren una precisión índice máximo (-1).

#### Índice de precisión

La precisión de índice permite lograr un equilibrio entre la sobrecarga de almacenamiento de índices y el rendimiento de las consultas. Para números, se recomienda usar la configuración de precisión predeterminada de -1. Puesto que los números son 8 bytes en JSON, esto es equivalente a una configuración de 8 bytes. La selección de un valor inferior para la precisión, como 1-7, significa que los valores de algunos intervalos se asignan a la misma entrada de índice. Por lo tanto, se reducirá el espacio de almacenamiento del índice, pero la ejecución de la consulta podría tener que procesar más documentos y, por tanto, consumir más rendimiento, es decir, solicitar unidades.

La configuración de la precisión del índice es más útil con intervalos de cadena. Dado que las cadenas pueden ser de cualquier longitud arbitraria, la elección de la precisión del índice puede afectar al rendimiento de las consultas de intervalo de cadena, así como a la cantidad de espacio de almacenamiento del índice requerido. Los índices de intervalo de cadena pueden configurarse con 1-100 o el valor de precisión máxima (-1). Si necesita Order By en cadenas, debe especificarlo en la ruta de acceso especificada (-1).

En el ejemplo siguiente se muestra cómo aumentar la precisión de los índices de intervalo en una recopilación mediante el SDK de .NET. Tenga en cuenta que esto usa la ruta predeterminada "/*".

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };
    
    rangeDefault.IndexingPolicy.IncludedPaths.Add(
        new IncludedPath { 
            Path = "/*", 
            Indexes = new Collection<Index> { 
                new RangeIndex(DataType.String) { Precision = -1 }, 
                new RangeIndex(DataType.Number) { Precision = -1 }
            }
        });

    await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


> [AZURE.NOTE]DocumentDB devuelve un error cuando una consulta usa Order By, pero no tiene un índice de intervalo en la ruta de acceso consultada con la precisión máxima.
>
> Se devuelve un error para las consultas con operadores de intervalo como >= si no hay ningún índice de intervalo (de cualquier precisión), pero pueden obtenerse si hay otros filtros que pueden obtenerse a partir del índice.
> 
> Las consultas de intervalo se pueden realizar sin un índice de intervalo mediante el uso del encabezado x-ms-documentdb-allow-scans en la API de REST o con la opción de solicitud EnableScanInQuery mediante el SDK de .NET.

De forma similar las rutas de acceso se pueden excluir completamente de la indexación. En el ejemplo siguiente se muestra cómo excluir una sección completa de los documentos (también conocida como subárbol) de la indexación usando el comodín "*".

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    
    collection = await client.CreateDocumentCollectionAsync(database.SelfLink, excluded);


### Indexación automática

Puede elegir si desea que la recopilación de todos los documentos se indexe automáticamente. De forma predeterminada, todos los documentos se indexan automáticamente, pero puede desactivarla. Cuando se desactiva la indexación, solo se puede tener acceso a documentos a través de sus propios vínculos o mediante consultas con Id.

Cuando se desactiva la indexación automática, podrá agregar al índice de manera selectiva solo algunos documentos específicos. Por el contrario, puede dejar activada la indexación automática y excluir de forma selectiva solo algunos documentos específicos. Las configuraciones de indexación activada/desactivada son útiles cuando solo tiene un subconjunto de los documentos que necesita consultar.

Por ejemplo, en el ejemplo siguiente se muestra cómo incluir un documento de forma explícita mediante la propiedad [SDK de .NET de DocumentDB](https://github.com/Azure/azure-documentdb-java) y [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a Document from indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(defaultCollection.SelfLink,
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## Optimización del rendimiento

Las API de DocumentDB proporcionan información acerca de las métricas de rendimiento, como el almacenamiento de índice usado y el costo del rendimiento (unidades de solicitud) para cada operación. Esta información puede usarse para comparar varias directivas de indexación y para optimizar el rendimiento.

Para comprobar la cuota de almacenamiento y el uso de una colección, ejecute una solicitud HEAD o GET en el recurso de colección e inspeccione la cuota x-ms-request y los encabezados x-ms-request-usage. En el SDK de .NET, las propiedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) y [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contienen los valores correspondientes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.        
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir la sobrecarga de la indexación en cada operación de escritura (crear, actualizar o eliminar), inspeccione el encabezado x-ms-request-charge (o la propiedad [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) equivalente en [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) en el SDK de .NET) para medir el número de unidades de solicitudes usadas por estas operaciones.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
     
     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                  
     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }
     
     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## Cambios en la especificación de la directiva de indexación
Se incorporó un cambio en el esquema de la directiva de indexación el 7 de julio de 2015 con la API de REST versión 2015-06-03. Las clases correspondientes de las versiones del SDK tienen implementaciones nuevas para que coincidan con el esquema.

Se han implementado los siguientes cambios en la especificación de JSON:

- La directiva de indexación admite índices de intervalo para las cadenas
- Cada ruta de acceso puede tener varias definiciones de índice, una para cada tipo de datos
- La precisión de la indexación admite 1-8 para números, 1-100 para las cadenas y -1 (precisión máxima)
- Los segmentos de rutas de acceso no requieren comillas dobles para escapar de cada ruta de acceso. Por ejemplo, ¿puede agregar una ruta de acceso para /title/? en lugar de /"title"/?
- La ruta de acceso raíz que representa "todas las rutas de acceso" se puede representar como /* (además de /)

Si tiene código que aprovisiona colecciones con una directiva de indexación personalizada escrita con la versión 1.1.0 del SDK de .NET o anteriores, será necesario cambiar el código de la aplicación para controlar estos cambios para pasar al SDK versión 1.2.0. Si no tiene código que configure la directiva de indexación o va a continuar usando una versión anterior del SDK, no se requerirá ningún cambio.

Para ver una comparación práctica, presentamos una directiva de indexación personalizada de ejemplo escrita con la API de REST versión 2015-06-03, así como la versión anterior, 08-04-2015.

**JSON de directiva de indexación anterior**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "IncludedPaths":[
          {
             "IndexType":"Hash",
             "Path":"/",
             "NumericPrecision":7,
             "StringPrecision":3
          }
       ],
       "ExcludedPaths":[
          "/"nonIndexedContent"/*"
       ]
    }

**JSON de directiva de indexación actual**

    {
       "automatic":true,
       "indexingMode":"Consistent",
       "includedPaths":[
          {
             "path":"/*",
             "indexes":[
                {
                   "kind":"Hash",
                   "dataType":"String",
                   "precision":3
                },
                {
                   "kind":"Hash",
                   "dataType":"Number",
                   "precision":7
                }
             ]
          }
       ],
       "ExcludedPaths":[
          {
             "path":"/nonIndexedContent/*"
          }
       ]
    }

## Pasos siguientes

Siga los vínculos que aparecen a continuación para obtener ejemplos de administración de directivas de índice y para obtener más información acerca del lenguaje de consulta de DocumentDB.

1.	[Ejemplos de código de administración de índices de .NET DocumentDB](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
2.	[Operaciones de recopilación de la API de REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn782195.aspx)
3.	[Consulta con SQL de Base de datos de documentos](documentdb-sql-query.md)

 

<!---HONumber=July15_HO4-->