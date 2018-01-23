---
title: "Directivas de indexación de Azure Cosmos DB | Microsoft Docs"
description: "Comprenda cómo funciona la indexación en Azure Cosmos DB. Obtenga información sobre la configuración y cambio de la directiva de indexación para una indexación automática y un mayor rendimiento."
keywords: "funcionamiento de la indexación, indexación automática, indexación de base de datos"
services: cosmos-db
documentationcenter: 
author: arramac
manager: jhubbard
editor: monicar
ms.assetid: d5e8f338-605d-4dff-8a61-7505d5fc46d7
ms.service: cosmos-db
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 08/17/2017
ms.author: arramac
ms.openlocfilehash: b09f5323f0378721412baade9be9926ebd0c171e
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/03/2018
---
# <a name="how-does-azure-cosmos-db-index-data"></a>¿Cómo funcionan los datos del índice de Azure Cosmos DB?

De forma predeterminada, todos los datos de Azure Cosmos DB se indexan. Aunque muchos clientes prefieren dejar que Azure Cosmos DB controle automáticamente todos los aspectos de la indexación, puede especificar una *directiva de indexación* personalizada para las colecciones durante la creación en Azure Cosmos DB. Las directivas de indexación de Azure Cosmos DB son más flexibles y eficaces que los índices secundarios que se ofrecen en otras plataformas de base de datos. En Azure Cosmos DB, puede diseñar y definir la forma del índice sin renunciar a la flexibilidad del esquema. 

Para entender cómo funciona la indexación en Azure Cosmos DB, es importante comprender que, al administrar la directiva de indexación, puede lograr un equilibrio específico entre la sobrecarga de almacenamiento, el rendimiento de escritura y de consulta, y la coherencia de las consultas del índice.  

En este artículo, echamos un vistazo más detenido a las directivas de indexación de Azure Cosmos DB, la personalización de la directiva de indexación, y las ventajas y desventajas asociadas. 

Después de leer este artículo, podrá responder a las preguntas siguientes:

* ¿Cómo puedo reemplazar las propiedades para incluirlas o excluirlas de la indexación?
* ¿Cómo puedo configurar el índice para las actualizaciones finales?
* ¿Cómo puedo configurar la indexación para realizar consultas por ORDER BY o por rango?
* ¿Cómo se pueden realizar cambios en la directiva de indexación de una colección?
* ¿Cómo se puede comparar el almacenamiento y el rendimiento de diferentes directivas de indexación?

## Personalización de la directiva de indexación de una colección <a id="CustomizingIndexingPolicy"></a>  
Para personalizar los equilibrios entre almacenamiento, rendimiento de escritura y consulta, y coherencia de las consultas, puede reemplazar la directiva de indexación predeterminada en una colección de Azure Cosmos DB. Puede configurar los aspectos siguientes:

* **Incluir o excluir documentos y rutas de acceso a y desde el índice**. Puede excluir o incluir documentos específicos en el índice al insertarlos o reemplazarlos en la colección. Asimismo, puede incluir o excluir propiedades JSON específicas, también denominadas *rutas de acceso*, que se vayan a indexar en documentos incluidos en un índice. Las rutas de acceso incluyen patrones de caracteres comodín.
* **Configurar distintos tipos de índice**. Para cada ruta de acceso incluida, puede especificar el tipo de índice que la ruta de acceso requiere para una colección. Puede especificar el tipo de índice en función de los datos de la ruta de acceso, la carga de trabajo de consultas prevista y la "precisión" numérica o de las cadenas.
* **Configurar los modos de actualización del índice**. Azure Cosmos DB admite tres modos de indexación: Coherente, Diferida y Ninguna. Puede configurar los modos de indexación a través de la directiva de indexación en una colección de Azure Cosmos DB. 

En el siguiente fragmento de código de Microsoft .NET se muestra cómo establecer una directiva de indexación personalizada durante la creación de una colección. En este ejemplo, establecemos la directiva con el índice de rango para las cadenas y los números en la precisión máxima. Esta directiva se puede usar para ejecutar consultas por ORDER BY en cadenas.

    DocumentCollection collection = new DocumentCollection { Id = "myCollection" };

    collection.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });
    collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), collection);   


> [!NOTE]
> El esquema JSON de la directiva de indexación cambió con el lanzamiento de la API de REST versión 2015-06-03. Con esa versión, el esquema JSON de la directiva de indexación admite los índices de rango en cadenas. El SDK de .NET 1.2.0 y los SDK de Java, Python y Node.js 1.1.0 admiten el nuevo esquema de directiva. Las versiones anteriores del SDK utilizan la API de REST versión 2015-04-08. Admiten el esquema anterior de la directiva de indexación.
> 
> De forma predeterminada, Azure Cosmos DB indexa todas las propiedades de cadena en los documentos de forma coherente con un índice Hash. Indexa todas las propiedades numéricas de los documentos de forma coherente con un índice de rango.  
> 
> 

### <a name="customize-the-indexing-policy-in-the-portal"></a>Personalización de la directiva de indexación en el portal

Puede cambiar la directiva de indexación de una colección en Azure Portal: 

1. En el portal, vaya a la cuenta de Azure Cosmos DB y, a continuación, seleccione su colección. 
2. En el menú de navegación izquierdo, seleccione **Configuración** y, a continuación, elija **Directiva de indexación**. 
3. En **Directiva de indexación**, cambie su directiva de indexación y, a continuación, seleccione **Aceptar**. 

### Modos de indexación de bases de datos <a id="indexing-modes"></a>  
Azure Cosmos DB admite tres modos de indexación que se pueden configurar mediante la directiva de indexación en una colección de Azure Cosmos DB: Coherente, Diferida y Ninguna.

**Coherente**: si la directiva de la colección de Azure Cosmos DB es Coherente, las consultas realizadas en una colección de Azure Cosmos DB determinada siguen el mismo nivel de coherencia que el especificado para las lecturas de punto (es decir, alta, de uso vinculado, sesión o eventual). El índice se actualiza de forma sincrónica como parte de la actualización del documento (inserción, reemplazo, actualización y eliminación de un documento en una colección de Azure Cosmos DB).

La indexación Coherente admite consultas coherentes a costa de una posible reducción en el rendimiento de escritura. Esta reducción depende de las rutas de acceso únicas que se deben indexar y del "nivel de coherencia". El modo de indexación coherente está diseñado para cargas de trabajo de tipo "escribir rápidamente, consultar inmediatamente".

**Diferida**: el índice se actualiza de forma asincrónica cuando una colección de Azure Cosmos DB está inactiva, es decir, cuando la capacidad de rendimiento de la colección no se usa por completo para atender las solicitudes del usuario. El modo de indexación Diferida puede resultar idóneo para cargas de trabajo de tipo "introducir ahora, consultar más adelante" que requieran la ingesta de documentos. Observe que podría obtener resultados incoherentes debido a la ingesta y la indexación lentas de los datos. Esto significa que sus consultas COUNT o los resultados de consultas específicas podrían no ser coherentes o repetirse en un momento dado. 

El índice suele estar en el modo de puesta al día con datos ingeridos. Con la indexación Diferida, los cambios en el período de vida (TTL) hacen que el índice se elimine y se vuelva a crear. Esto provoca incoherencias entre COUNT y los resultados de las consultas durante un período de tiempo. Por este motivo, la mayoría de las cuentas de Azure Cosmos DB deben usar el modo de indexación Coherente.

**Ninguna**: una colección marcada con el modo de indexación Ninguna no tiene ningún índice asociado. Se suele usar si Azure Cosmos DB se emplea como almacenamiento de clave-valor y solo se puede acceder a los documentos mediante su propiedad de identificador. 

> [!NOTE]
> La configuración de la directiva de indexación en Ninguna tiene el efecto secundario de quitar cualquier índice existente. Úsela si los patrones de acceso solo requieren un identificador o la vinculación automática.
> 
> 

La siguiente tabla muestra la coherencia de las consultas basadas en el modo de indexación (Coherente y Diferida) que se configure para la colección y el nivel de coherencia especificado para la solicitud de consulta. Esto se aplica a las consultas realizadas con cualquier interfaz: API de REST, SDK o desde procedimientos almacenados y desencadenadores. 

|Coherencia|Modo de indexación: Coherente|Modo de indexación: diferido|
|---|---|---|
|Alta|Alta|Ocasional|
|Uso vinculado|Uso vinculado|Ocasional|
|Sesión|Sesión|Ocasional|
|Ocasional|Ocasional|Ocasional|

Azure Cosmos DB devuelve un error para las consultas realizadas en colecciones con el modo de indexación Ninguna. Las consultas se pueden seguir ejecutando como exámenes a través del encabezado **x-ms-documentdb-enable-scan** en la API de REST o con la opción de solicitud **EnableScanInQuery** mediante el SDK de .NET. Algunas características de consulta, como ORDER BY, no se admiten como exámenes con **EnableScanInQuery**.

La siguiente tabla muestra la coherencia de las consultas basadas en el modo de indexación (Coherente, Diferida y Ninguna) cuando se especifica **EnableScanInQuery**.

|Coherencia|Modo de indexación: coherente|Modo de indexación: diferido|Modo de indexación: ninguno|
|---|---|---|---|
|Alta|Alta|Ocasional|Alta|
|Uso vinculado|Uso vinculado|Ocasional|Uso vinculado|
|Sesión|Sesión|Ocasional|Sesión|
|Ocasional|Ocasional|Ocasional|Ocasional|

El siguiente ejemplo de código muestra cómo crear una colección de Azure Cosmos DB mediante el SDK de .NET con indexación Coherente en todas las inserciones de documentos.

     // Default collection creates a Hash index for all string fields and a Range index for all numeric    
     // fields. Hash indexes are compact and offer efficient performance for equality queries.

     var collection = new DocumentCollection { Id ="defaultCollection" };

     collection.IndexingPolicy.IndexingMode = IndexingMode.Consistent;

     collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("mydb"), collection);


### <a name="index-paths"></a>Rutas de acceso del índice
Azure Cosmos DB modela los documentos JSON y el índice como árboles. Puede ajustar las rutas de acceso a las directivas en el árbol. En los documentos, puede elegir las rutas de acceso que quiere incluir o excluir de la indexación. Esto puede mejorar el rendimiento de escritura y reducir el almacenamiento necesario para índice en escenarios en los que se conocen de antemano los patrones de consulta.

Las rutas de acceso de índice comienzan con la raíz (/) y suelen terminar con el operador de comodín ?, lo que indica que hay varios valores posibles para el prefijo. Por ejemplo, para atender la consulta SELECT * FROM Families F WHERE F.familyName = "Andersen", debe incluir una ruta de acceso de índice para /familyName/? en la directiva de índice de la colección.

Las rutas de acceso del índice también pueden usar el operador comodín \* para especificar el comportamiento de las rutas de acceso de forma recursiva en el prefijo. Por ejemplo, /payload/* puede usarse para excluir de la indexación a todo el contenido de la propiedad payload.

Estos son los patrones comunes para especificar las rutas de acceso del índice:

| Ruta de acceso                | Descripción/caso de uso                                                                                                                                                                                                                                                                                         |
| ------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| /                   | Ruta de acceso predeterminada de la recopilación. Recursiva. Se aplica a la totalidad del árbol de documentos.                                                                                                                                                                                                                                   |
| /prop/?             | Ruta de acceso de índice necesaria para atender consultas como la siguiente (con tipos hash o de intervalo respectivamente):<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                       |
| /"prop"/*             | Ruta de acceso del índice para todas las rutas de acceso situadas en la etiqueta especificada. Funciona con las siguientes consultas<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5<br><br>SELECT FROM collection c WHERE c.prop.subprop.nextprop = "value"<br><br>SELECT FROM collection c ORDER BY c.prop         |
| /props/[]/?         | Ruta de acceso de índice necesaria para servir a la iteración y a las consultas JOIN frente a las matrices de valores escalares como ["a", "b", "c"]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag > 5                                                                         |
| /props/[]/subprop/? | Ruta de acceso de índice necesaria para servir a la iteración y a las consultas JOIN frente a las matrices de objetos como [{subprop: "a"}, {subprop: "b"}]:<br><br>SELECT tag FROM tag IN collection.props WHERE tag.subprop = "value"<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag.subprop = "value"                                  |
| /prop/subprop/?     | Ruta de acceso de índice necesaria para atender consultas (con tipos hash o de intervalo respectivamente):<br><br>SELECT FROM collection c WHERE c.prop.subprop = "value"<br><br>SELECT FROM collection c WHERE c.prop.subprop > 5                                                                                                                    |

> [!NOTE]
> Al establecer las rutas de acceso de índice personalizado, es necesario especificar la regla de indexación predeterminada para el árbol de todo el documento, que se indica mediante la ruta de acceso especial "/*". 
> 
> 

En el ejemplo siguiente se configura una ruta de acceso específica con indexación de rango y un valor de precisión personalizado de 20 bytes:

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

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), pathRange);


### <a name="index-data-types-kinds-and-precisions"></a>Tipos de datos de índice, variantes y precisiones
Dispone de varias opciones para configurar la directiva de indexación de una ruta de acceso. Puede especificar una o más definiciones de indexación para cada ruta de acceso:

* **Tipo de datos**: Cadena, Número, Punto, Polígono o LineString (solo puede contener una entrada por tipo de datos y ruta de acceso).
* **Variante de índice**: Hash (consultas de igualdad), Rango (consultas de igualdad, por rango o por ORDER BY) o Espacial (consultas espaciales).
* **Precisión**: para el índice de hash, varía de 1 a 8, tanto para cadenas como para números. El valor predeterminado es 3. Para un índice de rango, este valor puede ser -1 (precisión máxima). Puede variar entre 1 y 100 (precisión máxima) para valores numéricos o de cadena.

#### <a name="index-kind"></a>Tipo de índice
Azure Cosmos DB admite los tipos de índice Hash y Rango para cada ruta de acceso que pueda configurar para los tipos de dato Cadena, Número o ambos.

* **Hash** admite consultas de igualdad y JOIN eficientes. Para la mayoría de los casos de uso, los índices hash no requieren una precisión mayor que el valor predeterminado de 3 bytes. El tipo de datos puede ser Cadena o Número.
* **Rango** admite consultas de igualdad, consultas por rango (con >, <, >=, <=, !=) y consultas por ORDER BY eficientes. De forma predeterminada, las consultas por ORDER BY también requieren una precisión de índice máxima (-1). El tipo de datos puede ser Cadena o Número.

Azure Cosmos DB también admite la clase de índice Espacial para cada ruta de acceso, que se puede especificar para el tipo de datos Punto, Polígono o LineString. El valor en la ruta especificada debe ser un fragmento de GeoJSON válido como `{"type": "Point", "coordinates": [0.0, 10.0]}`.

* **Espacial** admite consultas espaciales eficaces (internas y a distancia). El tipo de datos puede ser Punto, Polígono o LineString.

> [!NOTE]
> Azure Cosmos DB admite la indexación automática de los tipos de datos Punto, Polígono y LineString.
> 
> 

Estos son las variantes de índice admitidas, con ejemplos de consultas que se pueden usar para atenderlas:

| Tipo de índice | Descripción/caso de uso                                                                                                                                                                                                                                                                                                                                                                                                              |
| ---------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Hash       | Hash over /prop/? (o /) puede utilizarse para servir de forma eficaz las siguientes consultas:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>Hash over /props/[]/? (o / o /props/) puede utilizarse para servir de forma eficaz las siguientes consultas:<br><br>SELECT tag FROM collection c JOIN tag IN c.props WHERE tag = 5                                                                                                                       |
| Intervalo      | Range over /prop/? (o /) puede utilizarse para servir de forma eficaz las siguientes consultas:<br><br>SELECT FROM collection c WHERE c.prop = "value"<br><br>SELECT FROM collection c WHERE c.prop > 5<br><br>SELECT FROM collection c ORDER BY c.prop                                                                                                                                                                                                              |
| Espacial     | Range over /prop/? (o /) puede utilizarse para servir de forma eficaz las siguientes consultas:<br><br>SELECT FROM collection c<br><br>WHERE ST_DISTANCE(c.prop, {"type": "Point", "coordinates": [0.0, 10.0]}) < 40<br><br>SELECT FROM collection c WHERE ST_WITHIN(c.prop, {"type": "Polygon", ... }) --with indexing on points enabled<br><br>SELECT FROM collection c WHERE ST_WITHIN({"type": "Point", ... }, c.prop) --with indexing on polygons enabled              |

De forma predeterminada, se devuelve un error para las consultas con operadores de rango como >= si no hay ningún índice de rango (de ninguna precisión) para indicar que podría ser necesario realizar un examen para atender la consulta. Las consultas por rango se pueden realizar sin un índice de rango mediante el encabezado **x-ms-documentdb-enable-scan** en la API de REST o con la opción de solicitud **EnableScanInQuery** mediante el SDK de .NET. Si hay otros filtros en la consulta en los que Azure Cosmos DB puede usar el índice para realizar el filtrado, no se devuelve ningún error.

Se aplican las mismas reglas para las consultas espaciales. De forma predeterminada, se devuelve un error para las consultas espaciales si no hay ningún índice espacial y no hay ningún otro filtro que pueda obtenerse del índice. Se pueden realizar como un examen mediante **x-ms-documentdb-enable-scan** o **EnableScanInQuery**.

#### <a name="index-precision"></a>Índice de precisión
La precisión de índice se puede usar para lograr el equilibrio entre la sobrecarga de almacenamiento de índices y el rendimiento de las consultas. Para los números, se recomienda usar la configuración de precisión predeterminada de -1 (máxima). Puesto que los números son 8 bytes en JSON, equivale a una configuración de 8 bytes. La selección de un valor inferior para la precisión (p. ej., de 1 a 7) significa que los valores de algunos rangos se asignan a la misma entrada de índice. Por lo tanto, se reduce el espacio de almacenamiento del índice, pero la ejecución de la consulta podría tener que procesar más documentos. Por lo tanto, consume más rendimiento en las unidades de solicitud.

La configuración de la precisión del índice tiene una aplicación más práctica con intervalos de cadena. Dado que las cadenas pueden ser de cualquier longitud arbitraria, la elección de la precisión del índice puede afectar al rendimiento de las consultas de rango de cadena. También puede influir en la cantidad de espacio de almacenamiento del índice necesario. Los índices de rango de cadena pueden configurarse con 1 a 100 o -1 (máximo). Si desea realizar consultas por ORDER BY en las propiedades de cadena, debe especificar una precisión de -1 para las rutas de acceso correspondientes.

Los índices espaciales siempre usan la precisión de índice predeterminada para todos los tipos (Punto, LineString y Polígono). La precisión de índice predeterminada de los índices espaciales no se puede invalidar. 

En el ejemplo siguiente se muestra cómo aumentar la precisión de los índices de rango en una colección mediante el SDK de .NET. 

**Creación de una colección con una precisión de índice personalizada**

    var rangeDefault = new DocumentCollection { Id = "rangeCollection" };

    // Override the default policy for strings to Range indexing and "max" (-1) precision
    rangeDefault.IndexingPolicy = new IndexingPolicy(new RangeIndex(DataType.String) { Precision = -1 });

    await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), rangeDefault);   


> [!NOTE]
> Azure Cosmos DB devuelve un error cuando una consulta usa Order By, pero no tiene un índice de rango en la ruta de acceso consultada con la precisión máxima. 
> 
> 

Del mismo modo, puede excluir por completo las rutas de acceso de la indexación. En el ejemplo siguiente se muestra cómo excluir una sección completa de los documentos (un *subárbol*) de la indexación mediante el operador de comodín \*.

    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*" });

    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);



## <a name="opt-in-and-opt-out-of-indexing"></a>Participar y no participar en la indexación
Puede elegir si desea que la recopilación de todos los documentos se indexe automáticamente. De forma predeterminada, todos los documentos se indexan automáticamente, pero puede desactivar la indexación automática. Cuando se desactiva la indexación, solo se puede acceder a los documentos a través de sus propios vínculos o mediante su identificador.

Cuando se desactiva la indexación automática, podrá agregar al índice de manera selectiva solo algunos documentos específicos. Por el contrario, puede dejar activada la indexación automática y excluir de forma selectiva documentos específicos. Las configuraciones de indexación activada/desactivada son útiles cuando solo tiene un subconjunto de los documentos que necesita consultar.

En el ejemplo siguiente se muestra cómo incluir un documento explícitamente mediante el [SDK de .NET para SQL API](https://docs.microsoft.com/azure/cosmos-db/sql-api-sdk-dotnet) y la propiedad [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

    // If you want to override the default collection behavior to either
    // exclude (or include) a document in indexing,
    // use the RequestOptions.IndexingDirective property.
    client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"),
        new { id = "AndersenFamily", isRegistered = true },
        new RequestOptions { IndexingDirective = IndexingDirective.Include });

## <a name="modify-the-indexing-policy-of-a-collection"></a>Modificación de la directiva de indexación de una colección
En Azure Cosmos DB, puede realizar cambios sobre la marcha en la directiva de indexación de una colección. Un cambio en la directiva de indexación de una colección de Azure Cosmos DB puede conducir a un cambio en la forma del índice. El cambio afecta a las rutas de acceso que se pueden indexar, a su precisión y al modelo de coherencia del propio índice. Un cambio en la directiva de indexación requiere efectivamente una transformación del índice original en uno nuevo. 

**Transformaciones de índice en línea**

![Cómo funciona la indexación: transformaciones de índice en línea de Azure Cosmos DB](./media/indexing-policies/index-transformations.png)

Las transformaciones de índice se realizan en línea. Esto significa que los documentos indexados por la directiva antigua se transforman eficazmente según la nueva directiva *sin que ello afecte a la disponibilidad de escritura ni al rendimiento aprovisionado* de la colección. La coherencia de las operaciones de lectura y escritura realizadas con la interfaz API de REST, SDK o desde procedimientos almacenados y desencadenadores no se ve afectada durante la transformación de índice. No se produce ninguna degradación del rendimiento ni ningún tiempo de inactividad en las aplicaciones al realizar un cambio de directiva de indexación.

Sin embargo, durante el tiempo en que la transformación de índice está en curso, las consultas son coherentes finalmente, con independencia de la configuración del modo de indexación (Coherente o Diferida). Esto se aplica a las consultas realizadas con todas las interfaces: API de REST, SDK o desde procedimientos almacenados y desencadenadores. Al igual que con la indexación Diferida, la transformación de índice se realiza asincrónicamente en segundo plano en las réplicas mediante los recursos de reserva disponibles para una réplica determinada. 

Las transformaciones de índice también se realizan in situ. Azure Cosmos DB no mantiene dos copias del índice e intercambia el índice antiguo por el nuevo. Esto significa que no se requiere ni se usa espacio en disco adicional en las colecciones mientras se realizan transformaciones de índice.

Cuando se cambia la directiva de indexación, se aplican los cambios para pasar del índice antiguo al nuevo en base, principalmente, a las configuraciones de modo de indexación. Las configuraciones de modo de indexación desempeñan un rol más importante que otros valores, como las rutas de acceso incluidas y excluidas, los tipos de índice y las precisiones. 

Si la directiva antigua y la nueva usan la indexación Coherente, Azure Cosmos DB realiza una transformación de índice en línea. No se puede aplicar otro cambio de directiva de indexación con el modo de indexación Coherente mientras la transformación está en curso. Sin embargo, puede cambiar al modo de indexación Diferida o Ninguna mientras una transformación está en curso: 

* Si cambia a Diferida, el cambio de directiva de indexación surte efecto de inmediato. Azure Cosmos DB comienza a crear nuevamente el índice de forma asincrónica. 
* Si cambia a Ninguna, el índice se elimina inmediatamente. El cambio a Ninguna resulta útil cuando se quiere cancelar una transformación en curso y empezar de nuevo con una directiva de indexación distinta. 

El siguiente fragmento de código muestra cómo modificar la directiva de indexación de una colección pasando del modo Coherente al modo Diferida. Si usa el SDK. de NET, puede iniciar un cambio de la directiva de indexación mediante el nuevo método **ReplaceDocumentCollectionAsync**.

**Modificación de directiva de indexación de Coherente a Diferida**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Changing from Default to Lazy IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.Lazy;

    await client.ReplaceDocumentCollectionAsync(collection);

**Seguimiento del progreso de transformación de índice**

Puede realizar un seguimiento del progreso porcentual de la transformación de índice en un índice Coherente mediante la propiedad de respuesta **IndexTransformationProgress** con una llamada a **ReadDocumentCollectionAsync**. Otros SDK y la API de REST admiten métodos y propiedades equivalentes para realizar cambios de directiva de indexación. Puede comprobar el progreso de una transformación de índice en un índice Coherente mediante una llamada a **ReadDocumentCollectionAsync**: 

    long smallWaitTimeMilliseconds = 1000;
    long progress = 0;

    while (progress < 100)
    {
        ResourceResponse<DocumentCollection> collectionReadResponse = await client.ReadDocumentCollectionAsync(
            UriFactory.CreateDocumentCollectionUri("db", "coll"));

        progress = collectionReadResponse.IndexTransformationProgress;

        await Task.Delay(TimeSpan.FromMilliseconds(smallWaitTimeMilliseconds));
    }

> [!NOTE]
> * La propiedad **IndexTransformationProgress** solo es aplicable cuando se realiza la transformación a un índice Coherente. Use la propiedad **ResourceResponse.LazyIndexingProgress** para realizar el seguimiento de las transformaciones en un índice Diferido.
> * Las propiedades **IndexTransformationProgress** y **LazyIndexingProgress** se rellenan solo para una colección sin particiones, es decir, una colección que se crea sin una clave de partición.
>

Puede quitar el índice de una colección moviendo al modo de indexación Ninguna. Puede ser una herramienta operativa útil si quiere cancelar una transformación en curso y comenzar inmediatamente una nueva.

**Eliminación del índice de una colección**

    // Switch to Lazy indexing mode.
    Console.WriteLine("Dropping index by changing to to the None IndexingMode.");

    collection.IndexingPolicy.IndexingMode = IndexingMode.None;

    await client.ReplaceDocumentCollectionAsync(collection);

¿Cuando realizaría cambios de directiva de indexación en las colecciones de Azure Cosmos DB? Los siguientes son los casos de uso más comunes:

* Servicio de resultados coherentes durante el funcionamiento normal, pero reversión al modo de indexación Diferida durante las importaciones de conjuntos masivos de datos.
* Empezar a usar nuevas características de indexación en sus colecciones actuales de Azure Cosmos DB. Por ejemplo, puede usar consultas geoespaciales, que requieren el tipo de índice espacial, o bien consultas por ORDER BY o por rango de cadena, que requieren el tipo de índice por rango de cadena.
* Seleccionar manualmente las propiedades que se van a indexar y cambiarlas con el tiempo.
* Optimizar la precisión de indexación para mejorar el rendimiento de las consultas o reducir el almacenamiento usado.

> [!NOTE]
> Para modificar la directiva de indexación con **ReplaceDocumentCollectionAsync**, debe usar la versión 1.3.0 o una versión posterior del SDK de .NET.
> 
> Para que la transformación del índice se complete correctamente, asegúrese de que haya suficiente espacio libre de almacenamiento disponible en la colección. Si la colección alcanza su cuota de almacenamiento, se pausa la transformación del índice. La transformación del índice se reanuda automáticamente cuando hay espacio de almacenamiento disponible, por ejemplo, si elimina algunos documentos.
> 
> 

## <a name="performance-tuning"></a>Optimización del rendimiento
Las API de SQL proporcionan información acerca de las métricas de rendimiento, como el almacenamiento de índice usado y el costo del rendimiento (unidades de solicitud) para cada operación. Esta información puede usarse para comparar varias directivas de indexación y para optimizar el rendimiento.

Para comprobar la cuota de almacenamiento y el uso de una colección, ejecute una solicitud **HEAD** o **GET** en el recurso de la colección. A continuación, inspeccione los encabezados **x-ms-request-quota** y **x-ms-request-usage**. En el SDK de .NET, las propiedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) y [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) contienen los valores correspondientes.

     // Measure the document size usage (which includes the index size) against   
     // different policies.
     ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"));  
     Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentQuota, collectionInfo.DocumentUsage);


Para medir la sobrecarga de la indexación en cada operación de escritura (crear, actualizar o eliminar), inspeccione el encabezado **x-ms-request-charge** (o la propiedad [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) equivalente en [ResourceResponse<T\>](http://msdn.microsoft.com/library/dn799209.aspx) en el SDK de .NET) para medir el número de unidades de solicitud que usan estas operaciones.

     // Measure the performance (request units) of writes.     
     ResourceResponse<Document> response = await client.CreateDocumentAsync(UriFactory.CreateDocumentCollectionUri("db", "coll"), myDocument);              
     Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);

     // Measure the performance (request units) of queries.    
     IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(UriFactory.CreateDocumentCollectionUri("db", "coll"), queryString).AsDocumentQuery();

     double totalRequestCharge = 0;
     while (queryable.HasMoreResults)
     {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
        Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
        totalRequestCharge += queryResponse.RequestCharge;
     }

     Console.WriteLine("Query consumed {0} request units in total", totalRequestCharge);

## <a name="changes-to-the-indexing-policy-specification"></a>Cambios en la especificación de la directiva de indexación
Se incorporó un cambio en el esquema de la directiva de indexación el 7 de julio de 2015 con la API de REST versión 2015-06-03. Las clases correspondientes de las versiones del SDK tienen implementaciones nuevas para que coincidan con el esquema. 

Se han implementado los siguientes cambios en la especificación de JSON:

* La directiva de indexación admite índices de rango para las cadenas.
* Cada ruta de acceso puede tener varias definiciones de índice. Puede tener una para cada tipo de datos.
* La precisión de la indexación admite de 1 a 8 para números, de 1 a 100 para cadenas y -1 (precisión máxima).
* Los segmentos de rutas de acceso no requieren comillas dobles para escapar de cada ruta de acceso. Por ejemplo, puede agregar una ruta de acceso para **/title/?** en lugar de **/"title"/?**.
* La ruta de acceso raíz que representa "todas las rutas de acceso" se puede representar como **/\*** (además de **/**).

Si tiene código que aprovisiona colecciones con una directiva de indexación personalizada escrita con la versión 1.1.0 del SDK de .NET o una versión anterior, debe cambiar el código de la aplicación para controlar estos cambios para pasar al SDK versión 1.2.0. Si no tiene código que configure la directiva de indexación o tiene previsto continuar usando una versión anterior del SDK, no es necesario ningún cambio.

Para obtener una comparación práctica, se incluye a continuación un ejemplo de una directiva de indexación personalizada escrita con la API de REST versión 2015-06-03, seguida de la misma directiva de indexación escrita con la API de REST anterior de la versión 2015-04-08.

**JSON de directiva de indexación actual (API de REST versión 2015-06-03)**

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


**JSON de directiva de indexación anterior (API de REST versión 2015-04-08)**

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
          "/\"nonIndexedContent\"/*"
       ]
    }


## <a name="next-steps"></a>pasos siguientes
Para obtener ejemplos de administración de directivas de índice y más información acerca del lenguaje de consulta de Azure Cosmos DB, visite los vínculos siguientes (pueden estar en inglés):

* [Ejemplos de código de administración de índices de .NET de SQL API](https://github.com/Azure/azure-documentdb-net/blob/master/samples/code-samples/IndexManagement/Program.cs)
* [Operaciones de colección de REST de SQL API](https://msdn.microsoft.com/library/azure/dn782195.aspx)
* [Consulta con SQL](sql-api-sql-query.md)

