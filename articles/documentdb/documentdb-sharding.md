---

redirect_url: https://azure.microsoft.com/services/documentdb/
ROBOTS: NOINDEX, NOFOLLOW
translationtype: Human Translation
ms.sourcegitcommit: d59ebef3cda36ba048b844f0cd2326fff66b4aa5
ms.openlocfilehash: d0a616d27c653e8d3749d93f565f5e8616fdf909



---
# <a name="how-to-partition-data-using-client-side-support-in-documentdb"></a>Cómo realizar una partición en los datos mediante la compatibilidad del lado cliente en DocumentDB
Azure DocumentDB es compatible con la [creación de particiones automáticas de colecciones](documentdb-partition-data.md). Sin embargo, hay casos donde resulta ventajoso tener un control específico sobre el comportamiento de la creación de particiones. Para reducir la cantidad de código reutilizable necesario en las tareas de creación de particiones, agregamos una función en el SDK de .NET., Node.js y Java que simplifica la compilación de aplicaciones que se escalan horizontalmente en varias colecciones.

En este artículo, nos fijaremos en las clases e interfaces del SDK de .NET y en su uso para desarrollar aplicaciones con particiones. Otros SDK como Java, Python y Node.js admiten métodos e interfaces similares para creación de particiones del lado cliente.

## <a name="client-side-partitioning-with-the-documentdb-sdk"></a>Creación de particiones del lado del cliente con el SDK de DocumentDB
Antes de profundizar más en la creación de particiones, resumamos algunos conceptos básicos de DocumentDB relacionados con ello. Toda cuenta de base de datos de Azure DocumentDB consta de un conjunto de bases de datos, cada una con varias recopilaciones. Cada recopilación puede contener, a su vez, procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Las colecciones pueden ser de una única partición o se pueden crear particiones automáticamente y tener las siguientes propiedades:

* Las colecciones permiten aislar el rendimiento. Por lo tanto, intercalar documentos similares dentro de la misma colección mejora el rendimiento. Por ejemplo, para los datos de series temporales, puede colocar los datos relativos al último mes, que se consultan con frecuencia, dentro de una colección con un mayor rendimiento aprovisionado, mientras que los datos más antiguos se pueden colocar en colecciones con bajo rendimiento aprovisionado.
* Las transacciones ACID (es decir, los procedimientos almacenados y los desencadenadores) no pueden abarcar una colección. Las transacciones tienen el ámbito de un valor de clave de partición única dentro de una colección.
* Las colecciones no aplican un esquema, por lo que se pueden utilizar para documentos JSON del mismo tipo o de tipos diferentes.

A partir de la versión [1.5.x de los SDK de Azure DocumentDB](documentdb-sdk-dotnet.md), puede realizar operaciones de documento directamente en una base de datos. Internamente, [DocumentClient](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) utiliza el valor de PartitionResolver que ha especificado para la base de datos para enrutar las solicitudes a la colección adecuada.

> [!NOTE]
> La [creación de particiones de lado de servidor](documentdb-partition-data.md) se introdujo en la API de REST 2015-12-16 y la versión 1.6.0+ de los SDK deja de usar el enfoque del solucionador de particiones del lado cliente para casos de uso sencillo. Sin embargo la creación de particiones del lado cliente es más flexible y le permite controlar el aislamiento del rendimiento a través de las claves de partición, controlar el grado de paralelismo mientras se leen los resultados de varias particiones y utilizar enfoques de creación de particiones por rangos y espaciales frente a hash.
> 
> 

Por ejemplo, en .NET, cada clase PartitionResolver es una implementación concreta de una interfaz [IPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.aspx) que tiene tres métodos: [GetPartitionKey](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.getpartitionkey.aspx), [ResolveForCreate](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforcreate.aspx) y [ResolveForRead](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.ipartitionresolver.resolveforread.aspx). Las consultas LINQ y los iteradores ReadFeed utilizan internamente el método ResolveForRead para recorrer en iteración todas las colecciones que coincidan con la clave de partición para la solicitud. Del mismo modo, las operaciones de creación utilizan el método ResolveForCreate para enrutar las creaciones a la partición correcta. No se requieren cambios para las operaciones Replace, Delete y Read, porque utilizan documentos que ya contienen la referencia a la colección correspondiente.

Los SDK también incluyen dos clases compatibles con las dos técnicas de creación de particiones canónicas (la creación de hash y las búsquedas de intervalos) a través de [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) y [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx). Puede utilizar estas clases para agregar fácilmente la lógica de creación de particiones a la aplicación.  

## <a name="add-partitioning-logic-and-register-the-partitionresolver"></a>Adición de lógica de creación de particiones y registro de PartitionResolver
Este es un fragmento de código que muestra cómo crear un [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) y registrarlo en DocumentClient para una base de datos.

```cs
// Create some collections to partition data.
DocumentCollection collection1 = await client.CreateDocumentCollectionAsync(...);
DocumentCollection collection2 = await client.CreateDocumentCollectionAsync(...);

// Initialize a HashPartitionResolver using the "UserId" property and the two collection self-links.
HashPartitionResolver hashResolver = new HashPartitionResolver(
    u => ((UserProfile)u).UserId, 
    new string[] { collection1.SelfLink, collection2.SelfLink });

// Register the PartitionResolver with the database.
this.client.PartitionResolvers[database.SelfLink] = hashResolver;

```

## <a name="create-documents-in-a-partition"></a>Creación de documentos en una partición
Una vez registrado el PartitionResolver, puede realizar creaciones y consultas directamente en la base de datos, tal como se muestra a continuación. En este ejemplo, el SDK utiliza el PartitionResolver para extraer el valor UserId, aplicarle el hash y, a continuación, usar ese valor para enrutar la operación de creación a la colección correcta.

```cs
Document johnDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("J1", "@John", Region.UnitedStatesEast));
Document ryanDocument = await this.client.CreateDocumentAsync(
    database.SelfLink, new UserProfile("U4", "@Ryan", Region.AsiaPacific, UserStatus.AppearAway));
```

## <a name="create-queries-against-partitions"></a>Creación de consultas en particiones
Puede consultar con el método [CreateDocumentQuery](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.documentqueryable.createdocumentquery.aspx) pasando la base de datos y una clave de partición. La consulta devuelve un único conjunto de resultados de todas las colecciones de la base de datos que se asignan a la clave de partición.  

```cs
// Query for John's document by ID - uses PartitionResolver to restrict the query to the partitions 
// containing @John. Again the query uses the database self link, and relies on the hash resolver 
// to route the appropriate collection.
var query = this.client.CreateDocumentQuery<UserProfile>(
    database.SelfLink, null, partitionResolver.GetPartitionKey(johnProfile))
    .Where(u => u.UserName == "@John");
johnProfile = query.AsEnumerable().FirstOrDefault();
```

## <a name="create-queries-against-all-collections-in-the-database"></a>Creación de consultas en todas las colecciones de la base de datos
También puede consultar todas las colecciones de la base de datos y enumerar los resultados como se muestra a continuación, omitiendo el argumento de la clave de partición.

```cs
// Query for all "Available" users. Here since there is no partition key, the query is serially executed 
// across each partition/collection and returns a single result-set. 
query = this.client.CreateDocumentQuery<UserProfile>(database.SelfLink)
    .Where(u => u.Status == UserStatus.Available);
foreach (UserProfile activeUser in query)
{
    Console.WriteLine(activeUser);
}
```

## <a name="hash-partition-resolver"></a>Resolución de partición por hash
Con la creación de particiones por hash, las particiones se asignan en función del valor de una función hash, lo que permite distribuir uniformemente las solicitudes y los datos entre una cantidad determinada de particiones. Este enfoque se utiliza normalmente para la creación de particiones de datos producidos o consumidos desde un gran número de clientes distintos, y es útil para almacenar perfiles de usuario, elementos de catálogos y datos de telemetría de IoT ("Internet de las cosas"). La compatibilidad con creación de particiones de lado servidor de DocumentDB también usa creación de particiones por hash dentro de una colección.

**Creación de particiones por hash:**
![Diagrama que ilustra cómo la creación de particiones por hash distribuye uniformemente las solicitudes entre las particiones](media/documentdb-sharding/partition-hash.png)

Un esquema sencillo de la creación de particiones por hash entre *N* colecciones sería tomar cualquier documento y calcular *hash(d) mod N* para determinar en qué colección se coloca. Sin embargo, esta sencilla técnica presenta un problema, y es que no funciona bien cuando agrega nuevas colecciones o quita colecciones, ya que esto requeriría la reestructuración de la práctica totalidad de los datos. [La aplicación coherente de hash](http://citeseerx.ist.psu.edu/viewdoc/summary?doi=10.1.1.23.3738) es un algoritmo muy conocido que soluciona este problema mediante la implementación de un esquema de hash que minimiza la cantidad de movimiento de datos necesario durante la adición o la eliminación de las colecciones.

La clase [HashPartitionResolver](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.hashpartitionresolver.aspx) implementa la lógica para crear un anillo de hash coherente a través de la función de hash especificada en la interfaz [IHashGenerator](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.partitioning.ihashgenerator.aspx). De forma predeterminada, HashPartitionResolver utiliza una función de hash MD5, pero esto se puede cambiar por su propia implementación de hash. El HashPartitionResolver crea internamente 16 valores hash o "nodos virtuales" en el anillo de hash para cada colección a fin de lograr una distribución más uniforme de documentos entre las colecciones, pero puede cambiar este número para compensar el sesgo de datos con la cantidad de cálculo del lado cliente.

**Aplicación coherente de hash con HashPartitionResolver:**
![Diagrama que ilustra cómo HashPartitionResolver crea un anillo de hash](media/documentdb-sharding/HashPartitionResolver.JPG)

## <a name="range-partition-resolver"></a>Resolución de partición por rangos
En la creación de particiones por rangos, las particiones se asignan en función de si la clave de partición está dentro de un rango determinado. Esto se utiliza normalmente para la creación de particiones con propiedades de marca de tiempo; por ejemplo, la hora de un evento (eventTime) entre el 1 y el 14 de abril de 2015. La clase [RangePartitionResolver](https://msdn.microsoft.com/library/azure/mt126047.aspx) ayuda a mantener una asignación entre un valor de Range\<T\> y el vínculo a la propia colección. 

[Range\<T\>](https://msdn.microsoft.com/library/azure/mt126048.aspx) es una clase sencilla que administra los rangos de los tipos que implementan IComparable\<T\> y IEquatable\<T\> como cadenas o números. En el caso de lecturas y creaciones, puede pasar cualquier rango arbitrario y la resolución identifica todas las colecciones candidatas mediante la identificación de los rangos de las particiones que forman una intersección con el rango solicitado. Esta funcionalidad puede ser útil al realizar consultas de rangos en datos de series temporales.

**Creación de particiones por rangos:**  

![ Diagrama que ilustra cómo la creación de particiones por rangos distribuye uniformemente las solicitudes entre las particiones](media/documentdb-sharding/partition-range.png)  

Un caso especial de la creación de particiones por rangos es cuando el rango es simplemente un único valor discreto, a veces denominado "creación de particiones por búsquedas". Esto se utiliza normalmente para crear particiones por región (por ejemplo, la partición de Escandinavia contiene Noruega, Dinamarca y Suecia) o para crear particiones de los inquilinos en una aplicación de varios inquilinos.

## <a name="samples"></a>Muestras
Eche un vistazo al [proyecto de Github de ejemplos de creación de particiones de DocumentDB](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning), que contiene fragmentos de código sobre cómo utilizar estos PartitionResolvers y ampliarlos para implementar sus propias resoluciones a fin de ajustarse a casos de uso específicos, como los siguientes: 

* Cómo especificar una expresión lambda arbitraria para GetPartitionKey y usarla para implementar las claves de creación de particiones compuestas o para crear particiones de diferentes tipos de objetos de forma distinta.
* Cómo crear un valor [LookupPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/LookupPartitionResolver.cs) simple que usa una tabla de búsqueda manual para la creación de particiones. Este patrón se utiliza normalmente para la creación de particiones basadas en valores discretos como región, identificador de inquilino o nombre de aplicación.
* Cómo crear un [ManagedPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/ManagedHashPartitionResolver.cs) que crea colecciones automáticamente basadas en una plantilla que define un esquema de nomenclatura, una directiva IndexingPolicy y procedimientos almacenados que deben registrarse en nuevas colecciones.
* Cómo crear un [SpilloverPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/SpilloverPartitionResolver.cs) sin esquema que simplemente crea nuevas colecciones a medida que se completan las anteriores.
* Cómo serializar y deserializar el estado de PartitionResolver como JSON, de manera que se pueda compartir entre procesos y a través de los apagados. Puede conservarlos en archivos de configuración, o incluso en una colección de DocumentDB.
* Una clase [DocumentClientHashPartitioningManager](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Util/DocumentClientHashPartitioningManager.cs) para agregar y eliminar dinámicamente particiones en una base de datos cuyas particiones se han creado con base en una aplicación coherente de hash. Utiliza internamente un [TransitionHashPartitionResolver](https://github.com/Azure/azure-documentdb-dotnet/blob/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning/Partitioners/TransitionHashPartitionResolver.cs) para enrutar lecturas y escrituras durante la migración mediante uno de los cuatro modos: leer desde el esquema de creación de particiones antiguo (ReadCurrent), el nuevo (ReadNext), combinar los resultados de ambos (ReadBoth) o no estar disponible durante la migración (None).

Los ejemplos son de código abierto y le animamos a que envíe solicitudes de extracción con las contribuciones que podrían ayudar a otros desarrolladores de DocumentDB. Consulte las [directrices de contribución](https://github.com/Azure/azure-documentdb-net/blob/master/Contributing.md) para obtener instrucciones acerca de cómo realizar sus aportaciones.  

> [!NOTE]
> Las creaciones de colección tienen la velocidad limitada por DocumentDB, por lo que algunos de los métodos de ejemplo que se muestran a continuación pueden tardar unos minutos en completarse.
> 
> 

## <a name="faq"></a>P+F
**¿Admite DocumentDB la creación de particiones en el lado servidor?**

Sí, DocumentDB admite [creación de particiones en el lado servidor](documentdb-partition-data.md). En los casos de uso más avanzados, DocumentDB también permite la creación de particiones en el lado de cliente a través de solucionadores de partición del lado de cliente.

**¿Cuándo debería usar la creación de particiones del lado del cliente o del lado del servidor?**
En la mayoría de los casos, le recomendamos que use la creación de particiones del lado de servidor, que gestiona las tareas administrativas de creación de particiones de datos y de enrutamiento de solicitudes. Sin embargo, si necesita crear particiones por rangos o tiene un caso de uso especializado para aislar el rendimiento entre valores distintos de claves de partición, entonces sería mejor la creación de particiones del lado de cliente.

**¿Cómo se puede agregar una colección a mi esquema de creación de particiones o quitarla del mismo?**

Eche un vistazo a la implementación de DocumentClientHashPartitioningManager en el proyecto de ejemplos para obtener un ejemplo de cómo puede implementar la nueva creación de particiones.

**¿Cómo se puede conservar o compartir mi configuración de creación de particiones con otros clientes?**

Puede serializar el estado particionador como JSON y almacenarlo en archivos de configuración, o incluso en colecciones de DocumentDB. Eche un vistazo al método RunSerializeDeserializeSample en el proyecto de ejemplos para ver un caso de este tipo.

**¿Cómo se pueden encadenar varias técnicas de creación de particiones?**

Puede encadenar PartitionResolvers implementando su propio valor de IPartitionResolver que utilice internamente uno o varios solucionadores existentes. Eche un vistazo a TransitionHashPartitionResolver en el proyecto de ejemplos para ver un caso de este tipo.

## <a name="references"></a>Referencias
* [Creación de particiones en el lado servidor en DocumentDB](documentdb-partition-data.md)
* [Colecciones y niveles de rendimiento de DocumentDB](documentdb-performance-levels.md)
* [Creación de particiones en ejemplos de código en Github](https://github.com/Azure/azure-documentdb-dotnet/tree/287acafef76ad223577759b0170c8f08adb45755/samples/code-samples/Partitioning)
* [Documentación del SDK de .NET de DocumentDB en MSDN](https://msdn.microsoft.com/library/azure/dn948556.aspx)
* [Ejemplos de .NET de DocumentDB](https://github.com/Azure/azure-documentdb-net)
* [Blog de DocumentDB sobre sugerencias de rendimiento](https://azure.microsoft.com/blog/2015/01/20/performance-tips-for-azure-documentdb-part-1-2/)




<!--HONumber=Jan17_HO3-->


