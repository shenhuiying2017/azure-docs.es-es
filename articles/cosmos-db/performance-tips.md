---
title: Sugerencias de rendimiento de Azure Cosmos DB para .NET | Microsoft Docs
description: "Conozca las opciones de configuración de cliente para mejorar el rendimiento de las bases de datos de Azure Cosmos DB."
keywords: "cómo mejorar el rendimiento de la base de datos"
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 94ff155e-f9bc-488f-8c7a-5e7037091bb9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2018
ms.author: mimig
ms.openlocfilehash: 2b822794cbafa037d4ab3cb23d2a885914c83607
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2018
---
> [!div class="op_single_selector"]
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Sugerencias de rendimiento para Azure Cosmos DB y .NET

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

Azure Cosmos DB es una base de datos distribuida rápida y flexible que se escala sin problemas con una latencia y un rendimiento garantizados. No es necesario realizar cambios de arquitectura importantes ni escribir código complejo para escalar la base de datos con Azure Cosmos DB. Escalar o reducir verticalmente es tan sencillo como realizar una única llamada de API o con el [método SDK](set-throughput.md#set-throughput-sdk). Sin embargo, como el acceso a Azure Cosmos DB se realiza mediante llamadas de red, puede realizar optimizaciones en el lado cliente para conseguir un rendimiento máximo al usar el [SDK de .NET para SQL](documentdb-sdk-dotnet.md).

Así que si se está preguntando "¿Cómo puedo mejorar el rendimiento de la base de datos?", tenga en cuenta las opciones siguientes:

## <a name="networking"></a>Redes
<a id="direct-connection"></a>

1. **Directiva de conexión: uso del modo de conexión directa**

    El modo en que un cliente se conecta a Azure Cosmos DB tiene implicaciones importantes sobre el rendimiento, especialmente en los términos de la latencia observada en el lado cliente. Existen dos opciones de configuración principales disponibles para configurar la directiva de conexión de cliente: el *modo* de conexión y el [protocolo *de* conexión](#connection-protocol).  Los dos modos disponibles son:

   1. Modo de puerta de enlace (predeterminado)
   2. Modo directo

      El modo de puerta de enlace se admite en todas las plataformas de SDK y es el valor predeterminado configurado.  Si la aplicación se ejecuta dentro de una red corporativa con restricciones de firewall estrictas, el modo de puerta de enlace es la mejor opción, ya que utiliza el puerto HTTPS estándar y un único punto de conexión. La desventaja para el rendimiento, sin embargo, es que el modo de puerta de enlace implica un salto de red adicional cada vez que se leen o escriben datos en Azure Cosmos DB. Por este motivo, el modo directo ofrece mejor rendimiento debido al número menor de saltos de red.
<a id="use-tcp"></a>
2. **Directiva de conexión: uso del protocolo TCP**

    Al usar el modo directo, hay dos opciones de protocolo disponibles:

   * TCP
   * HTTPS

     Azure Cosmos DB ofrece un modelo de programación RESTful sencillo y abierto sobre HTTPS. Además, ofrece un protocolo TCP eficaz que también es RESTful en su modelo de comunicación y está disponible a través del SDK de cliente de .NET. Tanto HTTPS como TCP directo usan SSL para la autenticación inicial y cifrar el tráfico. Para obtener el mejor rendimiento, utilice el protocolo TCP cuando sea posible.

     Al utilizar TCP en modo de puerta de enlace, el puerto TCP 443 es el de Azure Cosmos DB y el puerto 10255 es el de la API de MongoDB. Al utilizar TCP en modo directo, además de los puertos de la puerta de enlace, debe garantizar que los puertos entre el 10000 y el 20000 estén abiertos, ya que Azure Cosmos DB utiliza puertos TCP dinámicos. Si estos puertos no están abiertos y trata de utilizar TCP, recibirá un error 503 de servicio no disponible.

     El modo de conectividad se configura durante la construcción de la instancia de DocumentClient con el parámetro ConnectionPolicy. Si se utiliza el modo directo, el protocolo también puede establecerse en el parámetro ConnectionPolicy.

    ```csharp
    var serviceEndpoint = new Uri("https://contoso.documents.net");
    var authKey = new "your authKey from the Azure portal";
    DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
    new ConnectionPolicy
    {
        ConnectionMode = ConnectionMode.Direct,
        ConnectionProtocol = Protocol.Tcp
    });
    ```

    Como TCP solo se admite en modo directo, si se utiliza el modo de puerta de enlace, siempre se usa el protocolo HTTPS para comunicarse con la puerta de enlace y se omite el valor de protocolo en el parámetro ConnectionPolicy.

    ![Ilustración de la directiva de conexión de Azure Cosmos DB](./media/performance-tips/connection-policy.png)

3. **Llamada a OpenAsync para evitar la latencia de inicio en la primera solicitud**

    De forma predeterminada, la primera solicitud tendrá una latencia mayor porque debe capturar la tabla de enrutamiento de direcciones. Para evitar esta latencia de inicio en la primera solicitud, se debe llamar a OpenAsync() una vez durante la inicialización de esta manera.

        await client.OpenAsync();
   <a id="same-region"></a>
4. **Colocación de los clientes en la misma región de Azure para aumentar el rendimiento**

    Cuando sea posible, coloque las aplicaciones que llaman a Azure Cosmos DB en la misma región que la base de datos de Azure Cosmos DB. Para obtener una comparación aproximada, las llamadas a Azure Cosmos DB en la misma región se realizan en menos de 1 o 2 ms, pero la latencia entre las costas este y oeste de Estados Unidos es mayor de 50 ms. Esta latencia podría variar de una solicitud a otra, según la ruta tomada por la solicitud cuando pasa del cliente al límite del centro de datos de Azure. Para conseguir la menor latencia posible, asegúrese de que la aplicación que llama se encuentra en la misma región de Azure que el punto de conexión de Azure Cosmos DB aprovisionado. Para obtener una lista de regiones disponibles, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustración de la directiva de conexión de Azure Cosmos DB](./media/performance-tips/same-region.png)
   <a id="increase-threads"></a>
5. **Aumento del número de subprocesos y tareas**

    Dado que las llamadas a Azure Cosmos DB se realizan a través de la red, puede que tenga que variar el grado de paralelismo de las solicitudes para reducir todo lo posible el tiempo que la aplicación cliente espera entre una solicitud y otra. Por ejemplo, si usa la [biblioteca TPL](https://msdn.microsoft.com//library/dd460717.aspx) de .NET, cree del orden de 100 tareas de lectura o escritura en Azure Cosmos DB.

## <a name="sdk-usage"></a>Uso del SDK
1. **Instalación del SDK más reciente**

    Los SDK de Azure Cosmos DB se mejoran constantemente para proporcionar el mejor rendimiento. Consulte las páginas del [SDK de Azure Cosmos DB](documentdb-sdk-dotnet.md) para determinar las mejoras de los SDK y las revisiones más recientes.
2. **Uso de un cliente de Azure Cosmos DB singleton para aumentar la duración de la aplicación**

    Cada instancia de DocumentClient está protegida frente a amenazas y realiza una administración de conexiones y un almacenamiento en caché de las direcciones de manera eficiente cuando funciona en modo directo. Para permitir la administración eficiente de las conexiones y un rendimiento mejor mediante DocumentClient, se recomienda usar una sola instancia de DocumentClient por AppDomain durante la vigencia de la aplicación.

   <a id="max-connection"></a>
3. **Aumento de System.Net MaxConnections por host al usar el modo de puerta de enlace**

    Las solicitudes de Azure Cosmos DB se realizan a través de HTTPS o REST cuando se usa el modo de puerta de enlace, y están condicionadas por los límites de conexión predeterminados por nombre de host o dirección IP. Puede que deba establecer MaxConnections en un valor mayor (100-1000) para que la biblioteca del cliente pueda utilizar varias conexiones simultáneas para Azure Cosmos DB. En el SDK 1.8.0 de .NET o versiones superiores, el valor predeterminado de [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) es 50 y para cambiar el valor, debe establecer [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) en un valor más alto.   
4. **Ajuste de consultas paralelas en colecciones particionadas**

     A partir de la versión 1.9.0 del SDK de .NET para SQL se admiten consultas paralelas que permiten consultar una colección con particiones en paralelo (consulte [Trabajar con los SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) y los [ejemplos de código](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) relacionados para más información). Las consultas paralelas están diseñadas para mejorar la latencia y el rendimiento de la consulta en todos sus homólogos seriales. Las consultas paralelas proporcionan dos parámetros que los usuarios pueden adaptar según sus necesidades: (a) MaxDegreeOfParallelism, para controlar el número máximo de particiones que se pueden consultar en paralelo, y (b) MaxBufferedItemCount, para controlar el número de resultados recuperados previamente.

    (a) La consulta paralela ***Tuning MaxDegreeOfParallelism\:*** realiza consultas en varias particiones en paralelo. Sin embargo, los datos de una recopilación con particiones individual se capturan en serie con respecto a la consulta. Por lo tanto, establecer el parámetro MaxDegreeOfParallelism en el número de particiones tiene la máxima probabilidad de conseguir el mejor rendimiento de consulta, siempre y cuando el resto de las demás condiciones del sistema permanezcan invariables. Si no conoce el número de particiones, puede establecer MaxDegreeOfParallelism en un número alto y el sistema elegirá el mínimo (número de particiones, entrada proporcionada por el usuario) como el valor de MaxDegreeOfParallelism.

    Es importante tener en cuenta que las consultas en paralelo producen los mejores beneficios si los datos se distribuyen uniformemente entre todas las particiones con respecto a la consulta. Si la colección con particiones está dividida de tal forma que todos, o la mayoría de los datos, devueltos por una consulta se concentran en algunas particiones (una partición en el peor de los casos), entonces el rendimiento de la consulta se vería afectada por cuellos de botella debido a esas particiones.

    (b) La consulta paralela ***Tuning MaxBufferedItemCount\:*** está diseñada para capturar previamente los resultados mientras el cliente procesa el lote actual de resultados. La captura previa ayuda a mejorar la latencia general de una consulta. MaxBufferedItemCount es el parámetro para limitar el número de resultados capturados previamente. Establecer MaxBufferedItemCount en el número esperado de resultados devueltos (o un número más alto) permite que la consulta reciba el máximo beneficio de la captura previa.

    La captura previa funciona de la misma manera con independencia de MaxDegreeOfParallelism, y hay un único búfer para los datos de todas las particiones.  
5. **Activación de GC del lado servidor**

    En algunos casos puede ser de ayuda reducir la frecuencia de recopilación de elementos no utilizados. En .NET, establezca [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) en True.
6. **Implementación del retroceso según intervalos RetryAfter**

    Durante las pruebas de rendimiento, debe aumentar la carga hasta que se limite una tasa de solicitudes pequeña. Si se limita, la aplicación del cliente debe retroceder de acuerdo con la limitación para el intervalo de reintento que el servidor especificó. Respetar el retroceso garantiza que dedica una cantidad de tiempo mínima de espera entre reintentos. Se incluye compatibilidad con la directiva de reintentos en la versión 1.8.0 y superior de [.NET](sql-api-sdk-dotnet.md) y [Java](sql-api-sdk-java.md) de SQL, y en la versión 1.9.0 y superior de [Node.js](sql-api-sdk-node.md) y [Python](sql-api-sdk-python.md), así como en todas las versiones compatibles de los SDK de [.NET Core](sql-api-sdk-dotnet-core.md). Para más información, consulte [Superación de los límites de rendimiento reservados](request-units.md#RequestRateTooLarge) y [RetryAfter](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx).
    
    Con la versión 1.19 y versiones posterior del SDK de. NET, hay un mecanismo para registrar información adicional de diagnóstico y soluciones de problemas de latencia como se muestra en el ejemplo siguiente. Puede registrar la cadena de diagnóstico para las solicitudes que tienen una mayor latencia de lectura. La cadena de diagnóstico capturada le ayudará a comprender el número de veces que se ha observado la aparición de códigos de estado 429 para una solicitud determinada.
    ```csharp
    ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
    readDocument.RequestDiagnosticsString 
    ```
    
7. **Escalado horizontal de la carga de trabajo de cliente**

    Si va a realizar pruebas en niveles de alto rendimiento (>50 000 RU/s), la aplicación cliente puede volverse un cuello de botella debido a que la máquina limita el uso de CPU o de la red. Si llega a este punto, puede seguir insertando la cuenta de Azure Cosmos DB mediante la escala horizontal de las aplicaciones cliente en varios servidores.
8. **Almacenamiento en caché de los identificadores URI de documentos para una latencia menor en las operaciones de lectura**

    Siempre que sea posible, almacene en caché los identificadores URI para obtener el mejor rendimiento en las operaciones de lectura.
   <a id="tune-page-size"></a>
9. **Ajuste del tamaño de página en consultas y fuentes de lectura para aumentar el rendimiento**

    Al realizar una lectura masiva de documentos mediante la funcionalidad de fuentes de lectura (por ejemplo, ReadDocumentFeedAsync) o al emitir una consulta SQL, los resultados se devuelven de forma segmentada si el conjunto de resultados es demasiado grande. De forma predeterminada, se devuelven resultados en fragmentos de 1 MB o de 100 artículos, el límite que se alcance primero.

    Para reducir el número de recorridos de ida y vuelta de red necesarios para recuperar todos los resultados aplicables, puede aumentar el tamaño de página con el encabezado de solicitud [x-ms-max-item-count](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-request-headers) hasta a 1000. En aquellos casos en los que solo sea necesario mostrar unos cuantos resultados, por ejemplo, si la interfaz de usuario o la API de aplicación solo devuelven 10 resultados de una vez, también puede reducir el tamaño de página a 10 a fin de reducir el rendimiento consumido en las lecturas y consultas.

    También puede establecer el tamaño de página mediante los SDK de Azure Cosmos DB disponibles.  Por ejemplo: 

        IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
10. **Aumento del número de subprocesos y tareas**

    Consulte [Aumento del número de subprocesos y tareas](#increase-threads) en la sección Redes.

11. **Uso del proceso de host de 64 bits**

    El SDK de SQL funciona en un proceso de host de 32 bits cuando se usa la versión 1.11.4 y superiores del SDK de .NET de SQL. Sin embargo, si utiliza consultas entre particiones, se recomienda el proceso de host de 64 bits para obtener un mejor rendimiento. De forma predeterminada, los siguientes tipos de aplicaciones tienen un proceso de host de 32 bits; para cambiar a 64 bits, siga estos pasos según el tipo de aplicación:

    - Para aplicaciones ejecutables, esto puede hacerse desactivando la opción **Preferencia de 32 bits** de la ventana **Propiedades del proyecto**, en la pestaña **Generar**.

    - Para proyectos de prueba basados en VSTest, puede hacerlo seleccionando **Prueba**->**Configuración de pruebas**->**Arquitectura del procesador predeterminada como X64**, en la opción de menú **Prueba de Visual Studio**.

    - Para aplicaciones web de ASP.NET implementadas localmente, se puede hacer seleccionando **Usar la versión de 64 bits de IIS Express para proyectos y sitios web**, en **Herramientas**->**Opciones**->**Proyectos y soluciones**->**Proyectos Web**.

    - Para aplicaciones web de ASP.NET implementadas en Azure, se puede hacer eligiendo la **Plataforma de 64 bits** en **Configuración de la aplicación** en Azure Portal.

## <a name="indexing-policy"></a>Directiva de indexación
 
1. **Exclusión de rutas de acceso sin utilizar de la indexación para acelerar las escrituras**

    La directiva de indexación de Cosmos DB también le permite especificar las rutas de acceso de documentos que se incluirán en la indexación o se excluirán de esta mediante el aprovechamiento de las rutas de acceso de indexación (IndexingPolicy.IncludedPaths y IndexingPolicy.ExcludedPaths). El uso de rutas de acceso de indexación puede ofrecer un rendimiento de escritura mejorado y un almacenamiento de índices reducido en escenarios en los que los patrones de consulta se conocen de antemano, dado que los costos de indexación están directamente correlacionados con el número de rutas de acceso únicas indexadas.  Por ejemplo, el código siguiente muestra cómo excluir una sección completa de los documentos (también conocido como subárbol) de la indexación usando el comodín "*".

    ```csharp
    var collection = new DocumentCollection { Id = "excludedPathCollection" };
    collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
    collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
    collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
    ```

    Para más información, consulte [Directivas de indexación de Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **Medición y optimización del uso menor de unidades de solicitud por segundo**

    Azure Cosmos DB ofrece un amplio conjunto de operaciones de base de datos, incluidas consultas relacionales y jerárquicas con funciones definidas por el usuario, procedimientos almacenados y desencadenadores. Todo funciona con los documentos dentro de una colección de base de datos. El costo asociado a cada una de estas operaciones variará en función de la CPU, la E/S y la memoria necesarias para completar la operación. En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud de la aplicación.

    El rendimiento se aprovisiona en función del número de [unidades de solicitud](request-units.md) establecido para cada contenedor. El consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para su contenedor están limitadas hasta que la frecuencia cae por debajo del nivel aprovisionado del contenedor. Si la aplicación requiere un mayor nivel de rendimiento, puede aumentar el rendimiento mediante el aprovisionamiento de unidades de solicitud adicionales. 

    La complejidad de una consulta afecta a la cantidad de unidades de solicitud consumidas para una operación. El número de predicados, la naturaleza de los predicados, el número de UDF y el tamaño del conjunto de datos de origen influyen en el costo de operaciones de consulta.

    Para medir la sobrecarga de cualquier operación (crear, actualizar o eliminar), inspeccione el encabezado [x-ms-request-charge](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) (o la propiedad RequestCharge equivalente en ResourceResponse o <T>FeedResponse<T> del SDK de .NET) para medir el número de unidades de solicitud que usan estas operaciones.

    ```csharp
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
    ```             

    El cargo de solicitud devuelto en este encabezado es una fracción de la capacidad de proceso aprovisionada (es decir, 2000 RU/segundo). Por ejemplo, si la consulta anterior devuelve 1000 documentos de 1 KB, el coste de la operación será 1000. Por lo tanto, al cabo de un segundo, el servidor respetará solo dos de estas solicitudes antes de limitar las que vengan a continuación. Para más información, consulte [Unidades de solicitud](request-units.md) y la [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Administración de la limitación de velocidad y la tasa de solicitudes demasiado grande**

    Cuando un cliente intenta superar la capacidad de proceso reservada para una cuenta, no habrá ninguna degradación del rendimiento en el servidor y no se utilizará ninguna capacidad de proceso más allá del nivel reservado. El servidor finalizará de forma preventiva la solicitud con RequestRateTooLarge (código de estado HTTP 429) y devolverá el encabezado [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/documentdb/common-documentdb-rest-response-headers) para indicar la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar realizar la solicitud.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Los SDK capturan implícitamente esta respuesta, respetan el encabezado retry-after especificado por el servidor y reintentan la solicitud. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

    Si tiene más de un cliente que funciona acumulativamente de forma coherente por encima de la tasa de solicitudes, puede que el número de reintentos predeterminado establecido actualmente en 9 de manera interna por el cliente no sea suficiente; en este caso, el cliente producirá una excepción DocumentClientException con el código de estado 429 para la aplicación. El número de reintentos predeterminado se puede cambiar estableciendo RetryOptions en la instancia ConnectionPolicy. De forma predeterminada, la excepción DocumentClientException con el código de estado 429 se devuelve tras un tiempo de espera acumulativo de 30 segundos si la solicitud sigue funcionando por encima de la tasa de solicitudes. Esto sucede incluso cuando el número de reintentos actual es inferior al número de reintentos máximo de 9, el valor predeterminado, o un valor definido por el usuario.

    Aunque el comportamiento de reintento automático ayuda a mejorar la resistencia y la usabilidad en la mayoría de las aplicaciones, podría no resultar ventajoso al realizar comparativas de rendimiento, en especial al medir la latencia.  La latencia observada del cliente aumentará si el experimento llega a la limitación del servidor y hace que el SDK del cliente realice reintentos de forma silenciosa. Para evitar aumentos de latencia durante los experimentos de rendimiento, mida el gasto devuelto por cada operación y asegúrese de que las solicitudes funcionan por debajo de la tasa de solicitudes observada. Para más información, consulte [Unidades de solicitud](request-units.md).
3. **Diseño de documentos más pequeños para un mayor rendimiento**

    El gasto de solicitud (es decir, el costo de procesamiento de solicitudes) de una operación dada está directamente correlacionado con el tamaño del documento. Las operaciones con documentos grandes cuestan más que las operaciones con documentos pequeños.

## <a name="next-steps"></a>pasos siguientes
Si quiere ver una aplicación de ejemplo usada para evaluar Azure Cosmos DB en escenarios de alto rendimiento en un pequeño número de máquinas cliente, consulte [Pruebas de escala y rendimiento con Azure Cosmos DB](performance-testing.md).

Para más información sobre cómo diseñar la aplicación para escalarla y obtener un alto rendimiento, consulte [Partición y escalado en Azure Cosmos DB](partition-data.md).
