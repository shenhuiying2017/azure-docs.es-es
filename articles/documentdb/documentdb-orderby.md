<properties 
	pageTitle="Ordenación de datos de DocumentDB con Order By | Azure" 
	description="Obtenga información sobre cómo usar ORDER BY en consultas de DocumentDB en LINQ y SQL y cómo especificar una directiva de indexación para las consultas de ORDER BY." 
	services="documentdb" 
	authors="arramac" 
	manager="jhubbard" 
	editor="cgronlun" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="arramac"/>

# Ordenación de datos de DocumentDB con Order By
Microsoft Azure DocumentDB admite la consulta de documentos mediante documentos de SQL sobre JSON. Los resultados de la consulta se pueden ordenar mediante la cláusula ORDER BY en instrucciones de consulta SQL.

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo se consulta con Order By?
- ¿Cómo se configura una directiva de indexación para Order By?
- ¿Qué novedades se esperan?

También se ofrecen [Ejemplos](#samples) y una sección de [Preguntas más frecuentes](#faq).

Para obtener una referencia completa sobre las consultas de SQL, consulte el [tutorial de consultas de DocumentDB](documentdb-sql-query.md).

## Cómo realizar consultas con Order By
Al igual que en ANSI-SQL, ahora puede incluir una cláusula Order By opcional en las instrucciones SQL al consultar DocumentDB. La cláusula puede incluir un argumento ASC o DESC opcional para especificar el orden en que se deben recuperar los resultados.

### Ordenación mediante SQL
Por ejemplo, aquí se muestra una consulta para recuperar libros en orden descendente de PublishTimestamp.

    SELECT * 
    FROM Books 
    ORDER BY Books.PublishTimestamp DESC

### Ordenación mediante SQL con filtrado
Puede ordenar con cualquier propiedad anidada dentro de documentos como Books.ShippingDetails.Weight y puede especificar filtros adicionales en la cláusula WHERE junto con Order By, como en este ejemplo:

    SELECT * 
    FROM Books 
	WHERE Books.SalePrice > 4000
    ORDER BY Books.ShippingDetails.Weight

### Ordenación mediante el proveedor de LINQ para .NET
Mediante la versión 1.2.0 del SDK de .NET y posteriores, también puede utilizar la cláusula OrderBy() u OrderByDescending() en consultas de LINQ, como en este ejemplo:

    foreach (Book book in client.CreateDocumentQuery<Book>(booksCollection.SelfLink)
        .OrderBy(b => b.PublishTimestamp)) 
    {
        // Iterate through books
    }

### Ordenación de la paginación mediante el SDK de .NET
Mediante la compatibilidad nativa con la paginación dentro de los SDK de DocumentDB, puede recuperar resultados de página en página como se muestra en el siguiente fragmento de código .NET. Aquí se capturan hasta 10 resultados a la vez mediante las interfaces FeedOptions.MaxItemCount y IDocumentQuery.

    var booksQuery = client.CreateDocumentQuery<Book>(
        booksCollection.SelfLink,
        "SELECT * FROM Books ORDER BY Books.PublishTimestamp DESC"
        new FeedOptions { MaxItemCount = 10 })
      .AsDocumentQuery();
            
    while (booksQuery.HasMoreResults) 
    {
        foreach(Book book in await booksQuery.ExecuteNextAsync<Book>())
        {
            // Iterate through books
        }
    }

DocumentDB admite la ordenación según tipos numéricos (no cadenas) y solo para una única propiedad Order By por consulta en esta vista previa de la función. Consulte [Qué novedades se esperan](#Whats_coming_next) para obtener más detalles.

## Configurar una directiva de indexación para Order By
Para ejecutar consultas de Order By, debe:

- Indexar rutas de acceso específicas dentro de los documentos con la máxima precisión, (o) 
- Indexar *todas* las rutas de acceso recursivamente para toda la colección con la máxima precisión 

La precisión máxima (representada como una precisión de -1 en el archivo JSON config) usa un número variable de bytes, según el valor que se indexa. Por lo tanto:

- En las propiedades con valores de número mayores (por ejemplo, las marcas de tiempo epoch), la precisión máxima tendrá una alta sobrecarga de índice. 
- Las propiedades con valores de número menores (enumeraciones, ceros, códigos postales, edades, etc.) tendrán una baja sobrecarga de índice.

Para obtener más información, consulte [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md).

### Indexación de Order By con todas las propiedades numéricas
Así es cómo puede crear una colección con la indexación de Order By con cualquier propiedad (numérica).
                   

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range, 
            Path = "/",
            NumericPrecision = -1 });

    await client.CreateDocumentCollectionAsync(databaseLink, 
        booksCollection);  

### Indexación de Order By para una sola propiedad
Así es cómo puede crear una colección con la indexación de Order By solo con la propiedad PublishTimestamp.

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            IndexType = IndexType.Range,
            Path = "/"PublishTimestamp"/?",
            NumericPrecision = -1
        });

    booksCollection.IndexingPolicy.IncludedPaths.Add(
        new IndexingPath {
            Path = "/"
        });


## Muestras
Eche un vistazo a los [proyectos de ejemplo de Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) en los que se muestra cómo se usa Order By, incluida la creación de directivas de indexación y la paginación con Order By. Los ejemplos son de código abierto y le animamos a que envíe solicitudes de extracción con las contribuciones que podrían ayudar a otros desarrolladores de DocumentDB. Consulte las [directrices de contribución](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obtener instrucciones acerca de cómo realizar sus aportaciones.

## ¿Qué novedades se esperan?

Las actualizaciones del servicio futuras ampliarán la compatibilidad de Order By que se presenta aquí. Estamos trabajando en las siguientes adiciones y se dará prioridad a la publicación de estas mejoras según sus comentarios:

- Directivas de indexación dinámicas: compatibilidad para modificar la directiva de indexación después de la creación de la colección
- Índices de intervalo de cadena: indexar para admitir consultas de intervalo (>, <, >=, <=) con respecto a valores de cadena. Para admitir esto, se incorporará un nuevo esquema más completo para las directivas de indexación.
- Compatibilidad con Order By de cadena en consultas de DocumentDB.
- Capacidad de actualizar la directiva de indexación mediante el Portal de vista previa de Azure.
- Compatibilidad con los índices compuestos para una operación Order By más eficaz y Order By en varias propiedades.

## P+F

**¿Qué plataformas y versiones del SDK admiten la ordenación?**

Dado que Order By es una actualización del lado servidor, no es necesario descargar una nueva versión del SDK para usar esta característica. Todas las plataformas y versiones del SDK, incluidos los SDK de JavaScript del lado servidor pueden usar Order By con cadenas de consulta SQL. Si está usando LINQ, debe descargar la versión 1.2.0 o una más reciente de Nuget.

**¿Cuál es el consumo de unidades de solicitud (RU) esperado de las consultas de Order By?**

Dado que Order By usa el índice de DocumentDB para las búsquedas, el número de unidades de solicitud que consumen las consultas de Order By será similar a las consultas equivalentes sin Order By. Al igual que con cualquier otra operación en DocumentDB, el número de unidades de solicitud depende de los tamaños y formas de los documentos, así como la complejidad de la consulta.


**¿Cuál es la sobrecarga de indexación esperada de Order By?**

La sobrecarga del almacenamiento de indexación será proporcional a la cantidad de propiedades numéricas. En el peor escenario posible, la sobrecarga del índice será el 100 % de los datos. No hay diferencias en la sobrecarga de procesamiento (unidades de solicitud) entre la indexación de intervalo u Order By y la indexación de Hash predeterminada.

**¿Afecta este cambio a las consultas sin Order By?**

No se han introducido cambios en la forma en que las consultas sin Order By funcionan actualmente. Antes del lanzamiento de esta característica, todas las consultas de DocumentDB devolvían resultados en orden de ResourceId (_rid). Con Order By, las consultas se devolverán naturalmente en el orden de valores especificado. En las consultas de Order By, _rid se usará como un criterio de ordenación secundario cuando haya varios documentos devueltos con el mismo valor.

**¿Cómo se consultan los datos existentes en DocumentDB con Order By?**

Esto se admitirá con la disponibilidad de la mejora de las directivas de indexación dinámicas mencionada en la sección [Qué novedades se esperan](what's-coming-next). Para hacer esto actualmente, debe exportar los datos y volver a importarlos en una nueva colección de DocumentDB creada con un índice de intervalo u Order By. La herramienta de importación de DocumentDB puede usarse para migrar datos entre colecciones.

**¿Cuáles son las limitaciones actuales de Order By?**

Order By solo puede especificarse con una propiedad numérica y solamente cuando se indexa por intervalos con indexación de precisión máxima (-1). Order By solo se admite con colecciones de documentos.

No puede realizar las siguientes operaciones:
 
- Order By con propiedades de cadena (próximamente).
- Order By con propiedades de cadena internas com id, _rid y _self (próximamente). - Order By con propiedades derivadas del resultado de una combinación de dentro de documentos (próximamente).
- Order By con varias propiedades (próximamente).
- Order By con las propiedades calculadas (por ejemplo, el resultado de una expresión o una función UDF o integrada).
- Order By con consultas en bases de datos, colecciones, usuarios, permisos o datos adjuntos.

## Pasos siguientes

Bifurque el [proyecto de ejemplos de Github](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby) y comience a ordenar sus datos.

## Referencias
* [Referencia de las consultas de DocumentDB](documentdb-sql-query.md)
* [Referencia de la directiva de indexación de DocumentDB](documentdb-indexing-policies.md)
* [Referencia SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
* [Ejemplos de Order By de DocumentDB](https://github.com/Azure/azure-documentdb-net/tree/master/samples/orderby)
 

<!---HONumber=62-->