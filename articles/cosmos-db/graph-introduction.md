---
title: "Introducción a las API Graph de Azure Cosmos DB | Microsoft Docs"
description: "Aprenda a usar Azure Cosmos DB para almacenar, consultar y recorrer gráficos enormes con latencia baja mediante el lenguaje de consulta Gremlin."
services: cosmosdb
author: arramac
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmosdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 05/10/2017
ms.author: arramac
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 963822f0927afea90c8a03b60b7ee93fe36b4070
ms.contentlocale: es-es
ms.lasthandoff: 05/10/2017


---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Introducción a las API Graph de Azure Cosmos DB

[Azure Cosmos DB](introduction.md) es un servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB proporciona una [distribución global llave en mano](../documentdb/documentdb-distribute-data-globally.md), [escalado elástico de rendimiento y almacenamiento](partition-data.md) en todo el mundo, latencias de pocos milisegundos en el percentil 99, [cinco niveles de coherencia bien definidos](../documentdb/documentdb-consistency-levels.md) y alta disponibilidad garantizada, todo ello respaldado por [Acuerdos de Nivel de Servicio líderes en el sector](https://azure.microsoft.com/support/legal/sla/documentdb/v1_1/). Azure Cosmos DB [indexa automáticamente datos](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin necesidad de que se encargue de la administración del esquema ni del índice. Sigue varios modelos y es compatible con los modelos de datos de documento, de clave-valor, de gráfico y de columnas. 

![Gremlin, gráfico y Azure Cosmos DB](./media/graph-introduction/graph-gremlin.png) 

Azure Cosmos DB proporciona modelado de gráficos y API de recorrido, así como una distribución global llave en mano, escalado elástico de almacenamiento y rendimiento, latencias de lectura <10 ms y <15 ms en el percentil 99, indexación y consulta automáticas, niveles de coherencia ajustables y Acuerdos de Nivel de Servicio completos, incluida la disponibilidad del 99,99 %. Azure Cosmos DB se puede consultar mediante el [estándar Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps), y se integra con otros sistemas de gráficos compatibles con Gremlin.

En este artículo, se proporciona información general sobre la API Graph de Azure Cosmos DB y sobre cómo puede usarla para almacenar gráficos enormes con miles de millones de vértices y bordes, consultarlos con una latencia del orden de milisegundos y modificar con facilidad la estructura y el esquema del gráfico. 

## <a name="graph-databases"></a>Bases de datos de gráficos
Los datos, tal y como aparecen en el mundo real, están conectados naturalmente. El modelado de datos tradicional se centra en las entidades. Sin embargo, para muchas aplicaciones, también existe la necesidad de modelar las ricas relaciones entre entidades. Los gráficos le permiten modelar tanto las entidades como las relaciones de forma natural. 

Un [gráfico](http://mathworld.wolfram.com/Graph.html) es una estructura formada por [vértices](http://mathworld.wolfram.com/GraphVertex.html) y [bordes](http://mathworld.wolfram.com/GraphEdge.html). Tanto los vértices como los bordes pueden tener un número arbitrario de propiedades. Los vértices denotan objetos discretos, como una persona, un lugar o un evento. Los bordes representan las relaciones entre vértices. Por ejemplo, una persona puede conocer a otra persona, que ha participado en un evento o hace poco ha estado en un lugar determinado. Las propiedades expresan información acerca de los bordes y vértices. Algunas propiedades de ejemplo son un vértice con un nombre y una edad, y un borde con una marca de tiempo o un peso. En términos más formales, este modelo se conoce como [gráfico de propiedades](https://github.com/tinkerpop/blueprints/wiki/Property-Graph-Model). Azure Cosmos DB es compatible con el modelo de gráfico de propiedades. 

Por ejemplo, en el siguiente gráfico de ejemplo, se muestra la relación entre personas, dispositivos móviles, intereses y sistemas operativos. 

![Base de datos de ejemplo que muestra personas, dispositivos e intereses](./media/graph-introduction/sample-graph.png) 

Los gráficos son útiles para entender una amplia gama de conjuntos de datos en los sectores de la ciencia, la tecnología y los negocios. Las bases de datos de gráficos permiten modelar y almacenar gráficos de forma natural y eficaz, lo que las hacen atractivas para muchas situaciones. Suelen ser bases de datos NoSQL, ya que estos casos de uso necesitan a menudo también flexibilidad en el esquema y una iteración rápida. 

Los gráficos ofrecen una técnica de modelado de datos novedosa y eficaz. Pero esto, por sí solo, no es razón suficiente para usar una base de datos de gráficos. Para muchos casos de uso y patrones que implican recorridos de gráfico, los gráficos superan el rendimiento de las bases de datos SQL y NoSQL tradicionales en órdenes de magnitud. Esta diferencia en rendimiento se amplía aún más al recorrer relaciones de varios miembros, como los amigos de mis amigos. 

Puede combinar los recorridos rápidos proporcionados por las bases de datos de gráficos con algoritmos de gráfico (como búsqueda en profundidad, búsqueda en amplitud, el algoritmo de Dijkstra, etc.) para resolver problemas en diversos dominios, como las redes sociales, el geoespacial, la administración de contenido y las recomendaciones. 

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Gráficos a escala mundial con Azure Cosmos DB
Azure Cosmos DB es una base de datos de gráficos totalmente administrada que ofrece una distribución global, escalado elástico de almacenamiento y rendimiento, indexación y consulta automáticas, niveles de coherencia ajustables y compatibilidad con el estándar TinkerPop.  

![Arquitectura de gráfico de Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png) 

Azure Cosmos DB ofrece las siguientes funcionalidades diferenciadas en comparación con otras bases de datos de gráficos del mercado: 

* **Rendimiento y almacenamiento escalables de manera elástica**: los gráficos en el mundo real necesitan escalarse más allá de la capacidad de un único servidor. Con Azure Cosmos DB, puede escalar sus gráficos sin problemas en varios servidores. También puede escalar de forma independiente el rendimiento de su gráfico en función de los patrones de acceso. Azure Cosmos DB admite bases de datos de gráficos que pueden escalarse a tamaños de almacenamiento prácticamente ilimitado y rendimiento aprovisionado. 

* **Replicación en varias regiones:** Azure Cosmos DB replica los datos gráficos de forma transparente en todas las regiones que tenga asociadas con su cuenta. Esto permite desarrollar aplicaciones que requieran acceso global a los datos al mismo tiempo que proporciona un equilibrio entre la coherencia, la disponibilidad y el rendimiento, todo ello con las garantías pertinentes. Azure Cosmos DB proporciona conmutación por error regional transparente con las API de hospedaje múltiple, así como la capacidad de escalar de forma elástica el rendimiento y el almacenamiento en todo el mundo.

* **Consultas y recorridos rápidos con la conocida sintaxis de Gremlin**: almacene vértices y bordes heterogéneos, y consulte estos documentos por medio de una sintaxis de Gremlin conocida (pronto, también estará disponible SQL mejorado). Azure Cosmos DB utiliza una tecnología de indexación estructurada mediante registros sin bloqueos y sumamente simultánea para indexar automáticamente todo el contenido. Esto permite realizar consultas y recorridos enriquecidos en tiempo real sin necesidad de especificar sugerencias de esquemas, índices secundarios ni vistas. Aprenda más en [Consulta de gráficos mediante Gremlin](gremlin-support.md).

* **Completamente administrado**: elimine la necesidad de administrar recursos de bases de datos y máquinas. Como servicio de Microsoft Azure completamente administrado, no es necesario que administre las máquinas virtuales, ni que implemente y configure software, administre el escalado ni realice complejas actualizaciones en la capa de datos. Se hacen copias de seguridad de todos los gráficos, además de protegerlos ante errores regionales. Puede agregar fácilmente una cuenta de Azure Cosmos DB y aprovisionar capacidad a medida que la necesite, lo que le permite concentrarse en su aplicación en lugar de en la operación y administración de la base de datos.

* **Indexación automática**: de manera predeterminada, Azure Cosmos DB indexa automáticamente todas las propiedades en nodos y bordes del gráfico y no espera ni requiere ningún esquema, ni tampoco la creación de índices secundarios. 

* **Compatibilidad con Gremlin**: Azure Cosmos DB es compatible de forma nativa con el estándar Gremlin de código abierto y se puede integrar con otros sistemas de gráficos habilitados para Gremlin. Por lo tanto, puede migrar fácilmente desde una base de datos de gráficos diferente, como Titan o Neo4j, o usar Azure Cosmos DB con marcos de análisis de gráficos como Apache Spark GraphX.

* **Niveles de coherencia ajustables**: seleccione entre cinco niveles de coherencia bien definidos para lograr un equilibrio óptimo entre la coherencia y el rendimiento. Para las consultas y las operaciones de lectura, Azure Cosmos DB ofrece cinco niveles de coherencia diferentes: segura, obsolescencia limitada, sesión, prefijo coherente y posible. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios razonables entre la coherencia, la disponibilidad y la latencia. Obtenga más información en [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB](../documentdb/documentdb-consistency-levels.md).

Además, Azure Cosmos DB proporciona la capacidad de usar varios modelos, como documento y gráfico, en los mismos contenedores o bases de datos. Puede usar una colección de documentos para almacenar datos gráficos en paralelo con documentos, y usar consultas SQL a través de JSON y consultas Gremlin para consultar los mismos datos como gráfico. 

## <a name="getting-started"></a>Introducción
Las cuentas de Azure Cosmos DB se pueden crear mediante la CLI de Azure, Azure PowerShell o Azure Portal con compatibilidad con la API Graph. Una vez creada la cuenta, Azure Portal proporciona un punto de conexión de servicio como `https://<youraccount>.graphs.azure.com`, que ofrece un front-end WebSocket para Gremlin. Puede configurar las herramientas compatibles con Gremlin, como [Gremin Console](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), para que se conecten a este punto de conexión y crear aplicaciones en Java, Node.js o cualquier controlador cliente Gremlin.

En la siguiente tabla se muestran los controladores Gremlin populares que puede empezar a usar en Azure Cosmos DB:

| Descargar | Documentación |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript en Github](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin Console](https://tinkerpop.apache.org/downloads.html) |[Documentación de TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB también proporciona una biblioteca de .NET propia con métodos de extensión de Gremlin basada en los [SDK de Azure Cosmos DB](../documentdb/documentdb-sdk-dotnet.md) a través de NuGet. Esta biblioteca proporciona un servidor Gremlin "en proceso" que se puede usar para conectarse directamente a las particiones de datos de DocumenDB. 

| Descargar | Documentación |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Con el [Emulador de Azure Cosmos DB](../documentdb/documentdb-nosql-local-emulator.md), puede llevar a cabo el desarrollo y las pruebas localmente mediante la API Graph, sin crear una suscripción de Azure ni incurrir en ningún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el emulador, puede cambiar al usar una cuenta de almacenamiento de Azure Cosmos DB en la nube.

## <a name="scenarios-for-azure-cosmos-dbs-graph-support"></a>Escenarios de compatibilidad con Graph en Azure Cosmos DB
Estos son algunos escenarios donde se puede utilizar la compatibilidad con gráficos en Azure Cosmos DB: 

* **Redes sociales**: al combinar datos sobre los clientes y sus interacciones con otras personas, puede desarrollar experiencias personalizadas, predecir el comportamiento de los clientes o conectar a personas con otras que tengan intereses similares. Azure Cosmos DB puede utilizarse para administrar redes sociales y realizar un seguimiento de los datos y las preferencias de los usuarios. 

* **Motores de recomendaciones**: se utilizan con frecuencia en el sector minorista. Al combinar información sobre productos, usuarios e interacciones de usuarios, como comprar, examinar o clasificar un artículo, puede generar recomendaciones personalizadas. Azure Cosmos DB, con su latencia baja, escalado elástico y compatibilidad nativa con gráficos, es ideal para el modelado de estas interacciones.

* **Geoespacial**: muchas aplicaciones en telecomunicaciones, logística y programación de viajes necesitan encontrar una ubicación de interés dentro de un área determinada o localizar la mejor ruta entre dos ubicaciones o la más corta. Azure Cosmos DB es una opción natural para estos problemas. 

* **Internet de las cosas**: con la red y las conexiones entre dispositivos IoT modeladas como gráfico, puede lograr una mejor comprensión del estado de los dispositivos y recursos, y de cómo los cambios en una parte de la red pueden afectar a otra parte. 

## <a name="next-steps"></a>Pasos siguientes
Para aprender más sobre la compatibilidad con gráficos en Azure Cosmos DB:

* Empiece con el [tutorial de gráficos en Azure Cosmos DB](create-graph-dotnet.md)
* Aprenda a [consultar gráficos en Azure Cosmos DB mediante Gremlin](gremlin-support.md)

