<properties 
	pageTitle="Sugerencias de rendimiento para DocumentDB | Microsoft Azure" 
	description="Conozca las opciones de configuración de cliente para mejorar el rendimiento de las bases de datos de Azure DocumentDB."
	keywords="cómo mejorar el rendimiento de la base de datos"
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/29/2016" 
	ms.author="mimig"/>

# Sugerencias de rendimiento para DocumentDB

Azure DocumentDB Azure es una base de datos distribuida rápida y flexible que se escala sin problemas con una latencia y un rendimiento garantizados. No es necesario realizar cambios de arquitectura importantes ni escribir código complejo para escalar la base de datos con DocumentDB. Escalar o reducir verticalmente es tan sencillo como realizar una única llamada de API o con el [método SDK](documentdb-performance-levels.md#changing-performance-levels-using-the-net-sdk). Sin embargo, como el acceso a DocumentDB se realiza mediante llamadas de red, puede realizar optimizaciones en el lado cliente para conseguir un rendimiento máximo.

De modo que si se está preguntando "¿Cómo puedo mejorar el rendimiento de la base de datos?", tenga en cuenta las siguientes opciones.

## Redes
<a id="direct-connection"></a>

1. **Directiva de conexión: uso del modo de conexión directa**
    
    El modo en que un cliente se conecta a Azure DocumentDB tiene implicaciones importantes sobre el rendimiento, en concreto en términos de la latencia observada en el lado cliente. Existen dos opciones de configuración principales disponibles para configurar la directiva de conexión de cliente: el *modo* de conexión y el *protocolo* de [conexión](#connection-protocol). Los dos modos disponibles son:

    1. Modo de puerta de enlace (predeterminado)
    2. Modo directo

    Como DocumentDB es un sistema de almacenamiento distribuido, sus recursos, como las colecciones, se particionan entre numerosas máquinas y cada partición se replica para obtener una alta disponibilidad. El traslado de la dirección lógica a física se mantiene en una tabla de enrutamiento que también esta disponible de manera interna como un recurso.

    En el modo de puerta de enlace, las máquinas de puerta de enlace de DocumentDB realizan este enrutamiento, lo que permite que el código de cliente sea sencillo y compacto. Una aplicación cliente emite solicitudes a las máquinas de puerta de enlace de DocumentDB, que traducen el identificador URI lógico de la solicitud en la dirección física del nodo de back-end y reenvían la solicitud de la forma adecuada. Por el contrario, en el modo directo, los clientes deben mantener, y actualizar periódicamente, una copia de esta tabla de enrutamiento y luego conectarse directamente a los nodos de DocumentDB del back-end.

    El modo de puerta de enlace se admite en todas las plataformas de SDK y es el valor predeterminado configurado. Si la aplicación se ejecuta dentro de una red corporativa con restricciones de firewall estrictas, el modo de puerta de enlace es la mejor opción, ya que utiliza el puerto HTTPS estándar y un único punto de conexión. La desventaja para el rendimiento, sin embargo, es que el modo de puerta de enlace implica un salto de red adicional cada vez que se leen o escriben datos en DocumentDB. Por este motivo, el modo directo ofrece mejor rendimiento debido al número menor de saltos de red.

2. **Directiva de conexión: uso del protocolo TCP**

    Al aprovechar el modo directo, hay dos opciones de protocolo disponibles:

    - TCP
    - HTTPS

    DocumentDB ofrece un modelo de programación RESTful sencillo y abierto sobre HTTP. Además, ofrece un protocolo TCP eficaz que también es RESTful en su modelo de comunicación y está disponible a través del SDK de cliente de .NET. Para obtener el mejor rendimiento, utilice el protocolo TCP cuando sea posible.

    El modo de conectividad se configura durante la construcción de la instancia de DocumentClient con el parámetro ConnectionPolicy. Si se utiliza el modo directo, el protocolo también puede establecerse en el parámetro ConnectionPolicy.

        var serviceEndpoint = new Uri("https://contoso.documents.net");
        var authKey = new "your authKey from Azure Mngt Portal";
        DocumentClient client = new DocumentClient(serviceEndpoint, authKey, 
        new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        });

    Como TCP solo se admite en modo directo, si se utiliza el modo de puerta de enlace, siempre se usa el protocolo HTTPS para comunicarse con la puerta de enlace y se omite el valor de protocolo en el parámetro ConnectionPolicy.

    ![Ilustración de la directiva de conexión de DocumentDB](./media/documentdb-performance-tips/azure-documentdb-connection-policy.png)

3. **Llamada a OpenAsync para evitar la latencia de inicio en la primera solicitud**

    De forma predeterminada, la primera solicitud tendrá una latencia mayor porque debe capturar la tabla de enrutamiento de direcciones. Para evitar esta latencia de inicio en la primera solicitud, se debe llamar a OpenAsync() una vez durante la inicialización de esta manera.

        await client.OpenAsync();
<a id="same-region"></a>
4. **Colocación de los clientes en la misma región de Azure para aumentar el rendimiento**

    Cuando sea posible, coloque las aplicaciones que llaman a DocumentDB en la misma región que la base de datos de DocumentDB. Para obtener una comparación aproximada, las llamadas a DocumentDB en la misma región se realizan en menos de 1 o 2 ms, pero la latencia entre las costas este y oeste de Estados Unidos es mayor de 50 ms. Esta latencia podría variar de una solicitud a otra, según la ruta tomada por la solicitud cuando pasa del cliente al límite del centro de datos de Azure. Para conseguir la menor latencia posible, asegúrese de que la aplicación que llama se encuentra en la misma región de Azure que el punto de conexión de DocumentDB aprovisionado. Para obtener una lista de regiones disponibles, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustración de la directiva de conexión de DocumentDB](./media/documentdb-performance-tips/azure-documentdb-same-region.png) <a id="increase-threads"></a>
5. **Aumento del número de subprocesos y tareas**

    Dado que las llamadas a DocumentDB se realizan a través de la red, puede que tenga que variar el grado de paralelismo de las solicitudes para reducir todo lo posible el tiempo que la aplicación cliente espera entre una solicitud y otra. Por ejemplo, si utiliza la [biblioteca TPL](https://msdn.microsoft.com//library/dd460717.aspx) de .NET, cree del orden de cien tareas de lectura o escritura en DocumentDB.

## Uso del SDK

1. **Instalación del SDK más reciente**

    Los SDK de DocumentDB se mejoran constantemente para proporcionar el mejor rendimiento. Consulte las páginas del [SDK de DocumentDB](documentdb-sdk-dotnet.md) para determinar las mejoras de los SDK y las revisiones más recientes.

2. **Uso de un cliente de DocumentDB singleton para aumentar la duración de la aplicación**
  
    Tenga en cuenta que cada instancia de DocumentClient está protegida frente amenazas y realiza una administración de conexiones y un almacenamiento en caché de las direcciones de manera eficiente cuando funciona en modo directo. Para permitir la administración eficiente de las conexiones y un rendimiento mejor mediante DocumentClient, se recomienda usar una sola instancia de DocumentClient por AppDomain durante la vigencia de la aplicación. <a id="max-connection"></a>
3. **Aumento de System.Net MaxConnections por host**

    Las solicitudes de DocumentDB se realizan de forma predeterminada a través de HTTPS o REST y están condicionadas por los límites de conexión predeterminados por nombre de host o dirección IP. Puede que deba establecerlo en un valor mayor (100-1000) para que la biblioteca del cliente pueda utilizar varias conexiones simultáneas a DocumentDB. En el SDK 1.8.0 de .NET o versiones superiores, el valor predeterminado de [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) es 50 y para cambiar el valor, debe establecer [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/es-ES/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) en un valor más alto.

4. **Activación de GC del lado servidor**
    
    En algunos casos puede ser de ayuda reducir la frecuencia de recopilación de elementos no utilizados. En .NET, establezca [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) en True.

5. **Implementación del retroceso según intervalos RetryAfter**
 
    Durante las pruebas de rendimiento, debe aumentar la carga hasta que se limite una tasa de solicitudes pequeña. Si se limita, la aplicación del cliente debe retroceder de acuerdo con la limitación para el intervalo de reintento que el servidor especificó. Ello le permite dedicar una cantidad de tiempo de espera mínima entre reintentos. Se incluye compatibilidad con la directiva de reintentos en la versión 1.8.0 y superior de DocumentDB, [.NET](documentdb-sdk-dotnet.md) y [Java](documentdb-sdk-java.md), y en la versión 1.9.0 y superior de [Node.js](documentdb-sdk-nodejs.md) y [Python](documentdb-sdk-python.md). Para más información, consulte [Superación de los límites de rendimiento reservados](documentdb-request-units.md#exceeding-reserved-throughput-limits) y [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).

6. **Escalado horizontal de la carga de trabajo de cliente**

    Si va a realizar pruebas en niveles de alto rendimiento (>50 000 RU/s), la aplicación cliente puede volverse un cuello de botella debido a que la máquina limita el uso de CPU o de la red. Si llega a este punto, puede seguir insertando la cuenta de DocumentDB mediante la escala horizontal de las aplicaciones cliente en varios servidores.

7. **Almacenamiento en caché de los identificadores URI de documentos para una latencia menor en las operaciones de lectura**

    Siempre que sea posible, almacene en caché los identificadores URI para obtener el mejor rendimiento en las operaciones de lectura. <a id="tune-page-size"></a>
8. **Ajuste del tamaño de página en consultas y fuentes de lectura para aumentar el rendimiento**

    Al realizar una lectura masiva de documentos mediante la funcionalidad de fuentes de lectura (es decir, ReadDocumentFeedAsync) o al emitir una consulta SQL de DocumentDB, los resultados se devuelven de forma segmentada si el conjunto de resultados es demasiado grande. De forma predeterminada, se devuelven resultados en fragmentos de 1 MB o de 100 artículos, el límite que se alcance primero.

    Para reducir el número de recorridos de ida y vuelta de red necesarios para recuperar todos los resultados aplicables, puede aumentar el tamaño de página con el encabezado de solicitud x-ms-max-item-count hasta a 1000. En aquellos casos en los que solo sea necesario mostrar unos cuantos resultados, por ejemplo, si la interfaz de usuario o la API de aplicación solo devuelven diez resultados de una vez, también puede reducir el tamaño de página a 10 a fin de reducir el rendimiento consumido en las lecturas y consultas.

    También puede establecer el tamaño de página mediante los SDK de DocumentDB disponibles. Por ejemplo:
    
        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });

9. **Aumento del número de subprocesos y tareas**

	Consulte [Aumento del número de subprocesos y tareas](increase-threads.md) en la sección Redes.

## Directiva de indexación

1. **Uso de la indexación diferida para lograr tasas de ingesta más rápidas a horas punta**

    DocumentDB le permite especificar, en el nivel de colección, una directiva de indexación que le permite elegir si quiere que los documentos de una colección se indexen o no automáticamente. Además, puede elegir entre actualizaciones de índices sincrónicas (coherentes) y asincrónicas (diferidas). De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un documento de la colección. Esto permite que las consultas tengan el mismo [nivel de coherencia](documentdb-consistency-levels.md) que el de las lecturas de los documentos sin demoras en la actualización de los índices.
    
    La indexación diferida se puede considerar en escenarios en los que los datos se escriben en ráfagas y desea amortizar el trabajo necesario para indexar el contenido durante un período de tiempo más prolongado. Esto permite utilizar de forma eficaz el rendimiento aprovisionado y atender a las solicitudes de escritura en las horas punta con una latencia mínima. Sin embargo, es importante advertir que cuando la indexación diferida está habilitada, los resultados de consulta serán a la larga coherentes con independencia del nivel de coherencia configurado para la cuenta de DocumentDB.

    Por lo tanto, el modo de indexación coherente (IndexingPolicy.IndexingMode está establecido en Coherente) genera el mayor gasto de unidad de solicitud por escritura (IndexingPolicy.IndexingMode está establecido en Diferido) y la ausencia de indexación (IndexingPolicy.Automatic está establecido en False) tiene cero costos de indexación en el momento de la escritura.

2. **Exclusión de rutas de acceso sin utilizar de la indexación para acelerar las escrituras**

    La directiva de indexación de DocumentDB también le permite especificar las rutas de acceso de documentos que se incluirán en la indexación o se excluirán de esta mediante el aprovechamiento de las rutas de acceso de indexación (IndexingPolicy.IncludedPaths y IndexingPolicy.ExcludedPaths). El uso de rutas de acceso de indexación puede ofrecer un rendimiento de escritura mejorado y un almacenamiento de índices reducido en escenarios en los que los patrones de consulta se conocen de antemano, dado que los costos de indexación están directamente correlacionados con el número de rutas de acceso únicas indexadas. Por ejemplo, el siguiente código muestra cómo excluir una sección entera del documento (lo que se conoce también como subárbol) de la indexación mediante el comodín "*".

        var collection = new DocumentCollection { Id = "excludedPathCollection" };
        collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
        collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
        collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);

    Para más información, consulte [Directivas de indexación de DocumentDB](documentdb-indexing-policies.md).

## Rendimiento
<a id="measure-rus"></a>

1. **Medición y optimización del uso menor de unidades de solicitud por segundo**

    DocumentDB ofrece un amplio conjunto de operaciones de base de datos, incluidas consultas relacionales y jerárquicas con UDF, procedimientos almacenados y desencadenadores. Todo funciona con los documentos dentro de una colección de base de datos. El coste asociado a cada una de estas operaciones variará en función de la CPU, E/S y memoria necesarios para completar la operación. En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud de la aplicación.

    Las [unidades de solicitud](documentdb-request-units.md) se aprovisionan para cada cuenta de base de datos según el número de unidades de capacidad que adquiera. El consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para su cuenta se limitarán hasta que la frecuencia caiga por debajo del nivel reservado para la cuenta. Si su aplicación necesita un nivel mayor de capacidad de proceso, puede adquirir unidades de capacidad adicionales.

    La complejidad de una consulta afecta a la cantidad de unidades de solicitud consumidas para una operación. El número de predicados, la naturaleza de los predicados, el número de UDF y el tamaño del conjunto de datos de origen influyen en el costo de operaciones de consulta.

    Para medir la sobrecarga de cualquier operación (crear, actualizar o eliminar), inspeccione el encabezado x-ms-request-charge (o la propiedad RequestCharge equivalente en ResourceResponse<T> o FeedResponse<T> del SDK de .NET) para medir el número de unidades de solicitudes usadas por estas operaciones.

        // Measure the performance (request units) of writes
        ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
        Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
        // Measure the performance (request units) of queries
        IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
        while (queryable.HasMoreResults)
             {
                  FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
                  Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
             }
        
    El cargo de solicitud devuelto en este encabezado es una fracción de la capacidad de proceso aprovisionada (es decir, 2000 RU/segundo). Por ejemplo, si la consulta anterior devuelve 1000 documentos de 1 KB, el costo de la operación será 1000. Por lo tanto, al cabo de un segundo, el servidor respetará solo dos de estas solicitudes antes de limitar las que vengan a continuación. Para más información, consulte [Unidades de solicitud](documentdb-request-units.md) y la [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner).

2. **Administración de la limitación de velocidad y la tasa de solicitudes demasiado grande**

    Cuando un cliente intenta superar la capacidad de proceso reservada para una cuenta, no habrá ninguna degradación del rendimiento en el servidor y no se utilizará capacidad de proceso más allá del nivel reservado. El servidor finalizará de forma preventiva la solicitud con RequestRateTooLarge (código de estado HTTP 429) y devolverá el encabezado x-ms-retry-after-ms para indicar la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar realizar la solicitud.
 
        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Los SDK capturan implícitamente esta respuesta, respetan el encabezado retry-after especificado por el servidor y reintentan la solicitud. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

    Si tiene más de un cliente que funciona acumulativamente de forma coherente por encima de la tasa de solicitudes, puede que el número de reintentos predeterminado establecido actualmente en 9 de manera interna por el cliente no sea suficiente; en este caso, el cliente producirá una excepción DocumentClientException con el código de estado 429 para la aplicación. El número de reintentos predeterminado se puede cambiar estableciendo RetryOptions en la instancia ConnectionPolicy. De forma predeterminada, la excepción DocumentClientException con el código de estado 429 se devuelve tras un tiempo de espera acumulativo de 30 segundos si la solicitud sigue funcionando por encima de la tasa de solicitudes. Esto se produce incluso cuando el número de reintentos actual es inferior al número de reintentos máximo de 9, el valor predeterminado, o un valor definido por el usuario.

    Aunque el comportamiento de reintento automático ayuda a mejorar la resistencia y la usabilidad en la mayoría de las aplicaciones, podría no resultar ventajoso al realizar comparativas de rendimiento, en especial al medir la latencia. La latencia observada del cliente aumentará si el experimento llega a la limitación del servidor y hace que el SDK del cliente realice reintentos de forma silenciosa. Para evitar aumentos de latencia durante los experimentos de rendimiento, mida el gasto devuelto por cada operación y asegúrese de que las solicitudes funcionan por debajo de la tasa de solicitudes observada. Para más información, consulte [Unidades de solicitud](documentdb-request-units.md).
   
3. **Diseño de documentos más pequeños para un mayor rendimiento**

    El gasto de solicitud (es decir, el costo de procesamiento de solicitudes) de una operación dada está directamente correlacionado con el tamaño del documento. Las operaciones con documentos grandes cuestan más que las operaciones con documentos pequeños.

## Niveles de coherencia

1. **Uso de niveles de coherencia más débiles para mejor las latencias de lectura**

    Otro factor importante a tener en cuenta al optimizar el rendimiento de una aplicación de DocumentDB es el nivel de coherencia. La elección del nivel de coherencia tiene implicaciones para el rendimiento tanto de las operaciones de lectura como de escritura. Puede configurar el nivel de coherencia predeterminado en la cuenta de base de datos y el nivel de coherencia elegido se aplica entonces a todas las colecciones (en todas las bases de datos) dentro de la cuenta de DocumentDB. En cuanto a las operaciones de escritura, el impacto de cambiar el nivel de coherencia se observa como latencia de la solicitud. A medida que se utilizan niveles de coherencia más fuertes, aumentan las latencias de escritura. Por otro lado, el impacto del nivel de coherencia en las operaciones de lectura se observa en términos de rendimiento. Niveles de coherencia más débiles permiten una mayor capacidad de proceso de lectura por parte del cliente.

    De manera predeterminada todas las lecturas y consultas enviadas a los recursos definidos por el usuario utilizarán el nivel de coherencia predeterminado especificado en la cuenta de la base de datos. Sin embargo, puede bajar el nivel de coherencia de una solicitud de lectura/consulta determinada especificando x-ms-consistency-level en el encabezado de la solicitud. Para más información, consulte [Niveles de coherencia en DocumentDB](documentdb-consistency-levels.md).

## Pasos siguientes

Si desea ver una aplicación de ejemplo usada para evaluar DocumentDB en escenarios de alto rendimiento en un pequeño número de máquinas cliente, consulte [Pruebas de escala y rendimiento con Azure DocumentDB](documentdb-performance-testing.md).

Para más información sobre cómo diseñar la aplicación para escalarla y obtener un alto rendimiento, consulte [Partición y escalado en Azure DocumentDB](documentdb-partition-data.md).

<!---HONumber=AcomDC_0803_2016-->