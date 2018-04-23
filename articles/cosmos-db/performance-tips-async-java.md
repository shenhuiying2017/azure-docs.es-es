---
title: Sugerencias de rendimiento de Azure Cosmos DB para Async Java | Microsoft Docs
description: Conozca las opciones de configuración de cliente para mejorar el rendimiento de las bases de datos de Azure Cosmos DB.
keywords: cómo mejorar el rendimiento de la base de datos
services: cosmos-db
author: SnehaGunda
manager: kfile
documentationcenter: ''
ms.assetid: dfe8f426-3c98-4edc-8094-092d41f2795e
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2018
ms.author: sngun
ms.openlocfilehash: 95f6e3d6d9db5a88b5b974daf6e36573b60878a5
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/20/2018
---
> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 
> 

# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Sugerencias de rendimiento para Azure Cosmos DB y Async Java
Azure Cosmos DB es una base de datos distribuida rápida y flexible que se escala sin problemas con una latencia y un rendimiento garantizados. No es necesario realizar cambios de arquitectura importantes ni escribir código complejo para escalar la base de datos con Azure Cosmos DB. Escalar y reducir verticalmente es tan sencillo como realizar una única llamada API o una llamada al método SDK. Sin embargo, como el acceso a Azure Cosmos DB se realiza mediante llamadas de red, puede realizar optimizaciones en el lado cliente para conseguir un rendimiento máximo al usar el [SDK de Async Java para SQL](sql-api-sdk-async-java.md).

Así que si se está preguntando "¿Cómo puedo mejorar el rendimiento de la base de datos?", tenga en cuenta las opciones siguientes:

## <a name="networking"></a>Redes
   <a id="same-region"></a>
1. **Colocación de los clientes en la misma región de Azure para aumentar el rendimiento**

    Cuando sea posible, coloque las aplicaciones que llaman a Azure Cosmos DB en la misma región que la base de datos de Azure Cosmos DB. Para obtener una comparación aproximada, las llamadas a Azure Cosmos DB en la misma región se realizan en menos de 1 o 2 ms, pero la latencia entre las costas este y oeste de Estados Unidos es mayor de 50 ms. Esta latencia podría variar de una solicitud a otra, según la ruta tomada por la solicitud cuando pasa del cliente al límite del centro de datos de Azure. Para conseguir la menor latencia posible, asegúrese de que la aplicación que llama se encuentra en la misma región de Azure que el punto de conexión de Azure Cosmos DB aprovisionado. Para obtener una lista de regiones disponibles, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services).

    ![Ilustración de la directiva de conexión de Azure Cosmos DB](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>Uso del SDK
1. **Instalación del SDK más reciente**

    Los SDK de Azure Cosmos DB se mejoran constantemente para proporcionar el mejor rendimiento. Consulte las páginas del [SDK de Azure Cosmos DB](sql-api-sdk-async-java.md) para determinar las mejoras de los SDK y las revisiones más recientes.
2. **Uso de un cliente de Azure Cosmos DB singleton para aumentar la duración de la aplicación**

    Cada instancia de AsyncDocumentClient está protegida frente a amenazas y realiza de manera eficiente la administración de las conexiones y el almacenamiento en caché de las direcciones. Para permitir la administración eficiente de las conexiones y un rendimiento mejor mediante AsyncDocumentClient, se recomienda usar una única instancia de AsyncDocumentClient por AppDomain durante la vigencia de la aplicación.

   <a id="max-connection"></a>

3. **Optimización de ConnectionPolicy**

    Las solicitudes de Azure Cosmos DB se realizan a través de HTTPS o REST cuando se usa el SDK de Async Java, y están condicionadas por el tamaño máximo predeterminado del grupo de conexiones (1000). Este valor predeterminado debe ser conveniente para la mayoría de los casos de uso. Sin embargo, en caso de que tenga una colección muy grande con muchas particiones, puede establecer el tamaño máximo del grupo de conexiones en un número mayor (pongamos, 1500) mediante setMaxPoolSize.

4. **Ajuste de consultas paralelas en colecciones particionadas**

    El SDK de Async Java para Azure Cosmos DB admite consultas paralelas, que permiten consultar una colección con particiones en paralelo (para más información, consulte [Trabajar con los SDK](sql-api-partition-data.md#working-with-the-azure-cosmos-db-sdks) y los [ejemplos de código](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) relacionados). Las consultas paralelas están diseñadas para mejorar la latencia y el rendimiento de la consulta en todos sus homólogos seriales.

    (a) Las consultas paralelas ***Tuning setMaxDegreeOfParallelism\:*** realizan consultas en varias particiones en paralelo. Sin embargo, los datos de una recopilación con particiones individual se capturan en serie con respecto a la consulta. Por lo tanto, use el parámetro setMaxDegreeOfParallelism para establecer el número de particiones que tienen la máxima probabilidad de conseguir el mejor rendimiento de consulta, siempre y cuando el resto de las demás condiciones del sistema permanezcan invariables. Si no conoce el número de particiones, puede usar setMaxDegreeOfParallelism para establecer un número alto y el sistema elegirá el mínimo (número de particiones, entrada proporcionada por el usuario) como el grado máximo de paralelismo. 

    Es importante tener en cuenta que las consultas en paralelo producen los mejores beneficios si los datos se distribuyen uniformemente entre todas las particiones con respecto a la consulta. Si la colección con particiones está dividida de tal forma que todos, o la mayoría de los datos, devueltos por una consulta se concentran en algunas particiones (una partición en el peor de los casos), entonces el rendimiento de la consulta se vería afectada por cuellos de botella debido a esas particiones.

    (b) La consulta paralela ***Tuning setMaxBufferedItemCount\:*** está diseñada para capturar previamente los resultados mientras el cliente procesa el lote actual de resultados. La captura previa ayuda a mejorar la latencia general de una consulta. setMaxBufferedItemCount limita el número de resultados capturados previamente. Establece setMaxBufferedItemCount en el número esperado de resultados devueltos (o un número más alto) permite que la consulta reciba el máximo beneficio de la captura previa.

    La captura previa funciona de la misma manera con independencia de MaxDegreeOfParallelism, y hay un único búfer para los datos de todas las particiones.  

5. **Implementación del retroceso según intervalos de getRetryAfterInMilliseconds**

    Durante las pruebas de rendimiento, debe aumentar la carga hasta que se limite una tasa de solicitudes pequeña. Si se limita, la aplicación cliente debe retroceder de acuerdo con el intervalo de reintento que el servidor especificó. Respetar el retroceso garantiza que dedica una cantidad de tiempo mínima de espera entre reintentos. Para más información, consulte [Superación de los límites de rendimiento reservados en Azure Cosmos DB](request-units.md#RequestRateTooLarge) y DocumentClientException.getRetryAfterInMilliseconds.
6. **Escalado horizontal de la carga de trabajo de cliente**

    Si va a realizar pruebas en niveles de alto rendimiento (>50 000 RU/s), la aplicación cliente puede volverse un cuello de botella debido a que la máquina limita el uso de CPU o de la red. Si llega a este punto, puede seguir insertando la cuenta de Azure Cosmos DB mediante la escala horizontal de las aplicaciones cliente en varios servidores.

7. **Uso del direccionamiento basado en nombres**

    Use el direccionamiento basado en nombres, en el que los vínculos tienen el formato `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`, en lugar de SelfLinks (\_self) que tiene el formato `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>`, para evitar recuperar los valores de ResourceIds de todos los recursos empleados para la construcción del vínculo. Además, a medida que se recrean estos recursos (posiblemente con el mismo nombre), almacenarlos en la caché puede que no sea útil.

   <a id="tune-page-size"></a>
8. **Ajuste del tamaño de página en consultas y fuentes de lectura para aumentar el rendimiento**

    Al realizar una lectura masiva de documentos mediante la funcionalidad de fuentes de lectura (por ejemplo, readDocuments) o al emitir una consulta SQL, los resultados se devuelven de forma segmentada si el conjunto de resultados es demasiado grande. De forma predeterminada, se devuelven resultados en fragmentos de 1 MB o de 100 artículos, el límite que se alcance primero.

    Para reducir el número de recorridos de ida y vuelta de red necesarios para recuperar todos los resultados aplicables, puede aumentar el tamaño de página con el encabezado de solicitud [x-ms-max-item-count](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) hasta 1000. En aquellos casos en los que solo sea necesario mostrar unos cuantos resultados, por ejemplo, si la interfaz de usuario o la API de aplicación solo devuelven 10 resultados de una vez, también puede reducir el tamaño de página a 10 a fin de reducir el rendimiento consumido en las lecturas y consultas.

    También puede establecer el tamaño de página con el método setMaxItemCount.
    
9. **Use el programador adecuado (evite el robo de subprocesos de E/S eventloop de Netty)**

    El SDK de Async Java usa [Netty](https://netty.io/) para la E/S sin bloqueo. El SDK usa un número fijo de subprocesos de E/S eventloop de Netty (tantos como núcleos de CPU tenga su máquina) para ejecutar operaciones de E/S. El observable que devuelve la API emite el resultado en uno de los subprocesos de E/S eventloop de Netty compartidos. Así que es importante no bloquear dichos subprocesos. Realizar trabajos que hacen un uso elevado de CPU o bloquear operaciones en el subproceso de E/S eventloop de Netty puede provocar un interbloqueo o reducir el rendimiento del SDK de manera considerable.

    Por ejemplo, el código siguiente ejecuta un trabajo que hace un uso elevado de CPU en el subproceso de E/S eventloop de Netty:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Tras recibir el resultado, si quiere realizar sobre él un trabajo que hace un uso elevado de CPU, debe evitar hacerlo sobre un subproceso de E/S eventloop de Netty. En su lugar, proporcione su propio programador para suministrar su propio subproceso para ejecutar su trabajo.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    Según el tipo del trabajo, debe usar el programador de RxJava existente adecuado para su trabajo. Obtenga más información aquí [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html).

    Para más información, busque el SDK de Async Java en la [página de Github](https://github.com/Azure/azure-cosmosdb-java).

10. **Deshabilite el registro de Netty** El registro de la biblioteca Netty es muy activo y debe desactivarse (puede que no baste con suprimir el registro de la configuración) para evitar costos adicionales de CPU. Si no está en modo de depuración, deshabilite por completo el registro de Netty. De modo que, si va a usar log4j para eliminar los costos de CPU adicionales que se producen debido al uso de ``org.apache.log4j.Category.callAppenders()`` desde Netty, agregue la siguiente línea al código base:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

11. **Límite de recursos de archivos abiertos del SO** Algunos sistemas Linux (como Redhat) tienen un límite superior sobre el número de archivos abiertos y, por tanto, sobre el número total de conexiones. Ejecute el siguiente código para ver los límites actuales:

    ```bash
    ulimit -a
    ```

    El número de archivos abiertos (nofile) debe ser lo bastante grande como para que haya espacio suficiente para el tamaño configurado del grupo de conexiones y otros archivos abiertos por el sistema operativo. Se puede modificar para permitir un tamaño mayor del grupo de conexiones.

    Abra el archivo limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    Agregue o modifique las siguientes líneas:

    ```
    * - nofile 100000
    ```

12. **Use implementación de SSL nativa para Netty** Netty puede usar OpenSSL directamente en la pila de implementación de SSL para conseguir un mejor rendimiento. En ausencia de esta configuración, Netty revertirá a la implementación de SSL predeterminada de Java.

    En Ubuntu, ejecute:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    y agregue las siguientes dependencias a las dependencias de Maven del proyecto:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.7.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Para otras plataformas (Redhat, Windows, Mac, etc.), consulte estas instrucciones https://netty.io/wiki/forked-tomcat-native.html.

## <a name="indexing-policy"></a>Directiva de indexación
 
1. **Exclusión de rutas de acceso sin utilizar de la indexación para acelerar las escrituras**

    La directiva de indexación de Azure Cosmos DB le permite especificar las rutas de acceso de documentos que se incluirán en la indexación o se excluirán de esta mediante el aprovechamiento de las rutas de acceso de indexación (setIncludedPaths y setExcludedPaths). El uso de rutas de acceso de indexación puede ofrecer un rendimiento de escritura mejorado y un almacenamiento de índices reducido en escenarios en los que los patrones de consulta se conocen de antemano, dado que los costos de indexación están directamente correlacionados con el número de rutas de acceso únicas indexadas.  Por ejemplo, el código siguiente muestra cómo excluir una sección completa de los documentos (también conocido como subárbol) de la indexación usando el comodín "*".

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Para más información, consulte [Directivas de indexación de Azure Cosmos DB](indexing-policies.md).

## <a name="throughput"></a>Throughput
<a id="measure-rus"></a>

1. **Medición y optimización del uso menor de unidades de solicitud por segundo**

    Azure Cosmos DB ofrece un amplio conjunto de operaciones de base de datos, incluidas consultas relacionales y jerárquicas con funciones definidas por el usuario, procedimientos almacenados y desencadenadores. Todo funciona con los documentos dentro de una colección de base de datos. El costo asociado a cada una de estas operaciones variará en función de la CPU, la E/S y la memoria necesarias para completar la operación. En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud de la aplicación.

    El rendimiento se aprovisiona en función del número de [unidades de solicitud](request-units.md) establecido para cada contenedor. El consumo de la unidad de solicitud se evalúa como frecuencia por segundo. Las aplicaciones que superan la frecuencia de unidad de solicitud aprovisionada para su contenedor están limitadas hasta que la frecuencia cae por debajo del nivel aprovisionado del contenedor. Si la aplicación requiere un mayor nivel de rendimiento, puede aumentar el rendimiento mediante el aprovisionamiento de unidades de solicitud adicionales. 

    La complejidad de una consulta afecta a la cantidad de unidades de solicitud consumidas para una operación. El número de predicados, la naturaleza de los predicados, el número de UDF y el tamaño del conjunto de datos de origen influyen en el costo de operaciones de consulta.

    Para medir la sobrecarga de cualquier operación (crear, actualizar o eliminar), inspeccione el encabezado [x-ms-request-charge](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para medir el número de unidades de solicitud usadas por estas operaciones. También puede examinar la propiedad RequestCharge equivalente en ResourceResponse<T> o FeedResponse<T>.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```             

    El cargo de solicitud devuelto en este encabezado es una fracción de la capacidad de proceso aprovisionada. Por ejemplo, si tiene 2000 RU/segundo aprovisionadas, y si la consulta anterior devuelve 1000 documentos de 1 KB, el costo de la operación será 1000. Por lo tanto, al cabo de un segundo, el servidor respetará solo dos de estas solicitudes antes de limitar las que vengan a continuación. Para más información, consulte [Unidades de solicitud](request-units.md) y la [calculadora de unidades de solicitud](https://www.documentdb.com/capacityplanner).
<a id="429"></a>
2. **Administración de la limitación de velocidad y la tasa de solicitudes demasiado grande**

    Cuando un cliente intenta superar la capacidad de proceso reservada para una cuenta, no habrá ninguna degradación del rendimiento en el servidor y no se utilizará ninguna capacidad de proceso más allá del nivel reservado. El servidor finalizará de forma preventiva la solicitud con RequestRateTooLarge (código de estado HTTP 429) y devolverá el encabezado [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) para indicar la cantidad de tiempo, en milisegundos, que el usuario debe esperar antes de volver a intentar realizar la solicitud.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    Los SDK capturan implícitamente esta respuesta, respetan el encabezado retry-after especificado por el servidor y reintentan la solicitud. A menos que varios clientes obtengan acceso a la cuenta al mismo tiempo, el siguiente reintento se realizará correctamente.

    Si tiene más de un cliente que funciona acumulativamente de forma coherente por encima de la tasa de solicitudes, puede que el número de reintentos predeterminado establecido actualmente en 9 de manera interna por el cliente no sea suficiente; en este caso, el cliente producirá una excepción DocumentClientException con el código de estado 429 para la aplicación. El número de reintentos predeterminado se puede cambiar mediante setRetryOptions en la instancia ConnectionPolicy. De forma predeterminada, la excepción DocumentClientException con el código de estado 429 se devuelve tras un tiempo de espera acumulativo de 30 segundos si la solicitud sigue funcionando por encima de la tasa de solicitudes. Esto sucede incluso cuando el número de reintentos actual es inferior al número de reintentos máximo de 9, el valor predeterminado, o un valor definido por el usuario.

    Aunque el comportamiento de reintento automático ayuda a mejorar la resistencia y la usabilidad en la mayoría de las aplicaciones, podría no resultar ventajoso al realizar comparativas de rendimiento, en especial al medir la latencia.  La latencia observada del cliente aumentará si el experimento llega a la limitación del servidor y hace que el SDK del cliente realice reintentos de forma silenciosa. Para evitar aumentos de latencia durante los experimentos de rendimiento, mida el gasto devuelto por cada operación y asegúrese de que las solicitudes funcionan por debajo de la tasa de solicitudes observada. Para más información, consulte [Unidades de solicitud](request-units.md).
3. **Diseño de documentos más pequeños para un mayor rendimiento**

    El gasto de solicitud (es decir, el costo de procesamiento de solicitudes) de una operación dada está directamente correlacionado con el tamaño del documento. Las operaciones con documentos grandes cuestan más que las operaciones con documentos pequeños.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre cómo diseñar la aplicación para escalarla y obtener un alto rendimiento, consulte [Partición y escalado en Azure Cosmos DB](partition-data.md).
