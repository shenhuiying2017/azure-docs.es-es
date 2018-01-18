---
title: "Introducción a las API Graph de Azure Cosmos DB | Microsoft Docs"
description: Aprenda a usar Azure Cosmos DB para almacenar, realizar consultas y recorrer grafos enormes con latencia baja mediante el lenguaje de consulta de grafos Gremlin de Apache TinkerPop.
services: cosmos-db
author: luisbosquez
documentationcenter: 
ms.assetid: b916644c-4f28-4964-95fe-681faa6d6e08
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/05/2017
ms.author: lbosq
ms.openlocfilehash: 14921dbeb0b670e4ec29a224caca07da12bfb82b
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2018
---
# <a name="introduction-to-azure-cosmos-db-graph-api"></a>Introducción a las API Graph de Azure Cosmos DB

[Azure Cosmos DB](introduction.md) es el servicio de base de datos con varios modelos y distribución global de Microsoft para aplicaciones críticas. Azure Cosmos DB proporciona las siguientes características, que cuentan con el respaldo de unos [contratos de nivel de servicio líderes del sector](https://azure.microsoft.com/support/legal/sla/cosmos-db/):

* [Distribución global inmediata](distribute-data-globally.md)
* [Escalado elástico del rendimiento y almacenamiento](partition-data.md) en todo el mundo
* Latencias de menos de 10 ms en el percentil 99
* [Cinco niveles de coherencia bien definidos](consistency-levels.md)
* Alta disponibilidad garantizada 

Azure Cosmos DB [indexa datos automáticamente](http://www.vldb.org/pvldb/vol8/p1668-shukla.pdf) sin que haya que ocuparse de la administración de esquemas ni de índices. Sigue varios modelos y es compatible con los modelos de datos de documento, de clave-valor, de grafo y de columnas.

Se recomienda ver el siguiente vídeo, en el que Kirill Gavrylyuk explica cómo empezar a trabajar con grafos en Azure Cosmos DB:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Graphs-with-Azure-Cosmos-DB-Gremlin-API/player]
> 
> 

La API Graph de Azure Cosmos DB proporciona:

- Modelado de grafos.
- API de cruce seguro.
- Distribución global llave en mano.
- Escalado elástico de rendimiento y almacenamiento con latencias de lectura inferiores a 10 milisegundos, y a 15 milisegundos en el percentil 99.
- Indexación automática con disponibilidad de consulta instantánea.
- Niveles de coherencia ajustables.
- Acuerdos de Nivel de Servicio completos que incluyen una disponibilidad del 99,99 % para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones.

Para consultar Azure Cosmos DB, puede usar el lenguaje de recorrido de gráficos [Apache TinkerPop](http://tinkerpop.apache.org), [Gremlin](http://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) u otro sistema de gráfico compatible con TinkerPop como [Apache Spark GraphX](spark-connector-graph.md).

En este artículo se proporciona una introducción de la API Graph de Azure Cosmos DB y se explica cómo se puede usar para almacenar grafos grandes con miles de millones de vértices y bordes. Puede consultar los gráficos con una latencia de milisegundos y evolucionar la estructura y el esquema de los gráficos con facilidad.

## <a name="graph-database"></a>Base de datos de gráfico
Los datos, tal y como aparecen en el mundo real, están conectados naturalmente. El modelado de datos tradicional se centra en las entidades. En el caso de muchas aplicaciones, también existe la necesidad de modelar las entidades y las relaciones de manera natural.

Un [gráfico](http://mathworld.wolfram.com/Graph.html) es una estructura que está formada por [vértices](http://mathworld.wolfram.com/GraphVertex.html) y [bordes](http://mathworld.wolfram.com/GraphEdge.html). Tanto los vértices como los bordes pueden tener un número arbitrario de propiedades. Los vértices denotan objetos discretos, como una persona, un lugar o un evento. Los bordes representan las relaciones entre vértices. Por ejemplo, es posible que una persona conozca a otra, haya participado en un evento y recientemente haya estado en una ubicación. Las propiedades expresan información acerca de los bordes y vértices. Algunas propiedades de ejemplo incluyen un vértice con un nombre, una edad y un borde, que tiene una marca de tiempo o un peso. En términos más formales, este modelo se conoce como [grafo de propiedades](http://tinkerpop.apache.org/docs/current/reference/#intro). Azure Cosmos DB es compatible con el modelo de grafo de propiedades.

Por ejemplo, en el siguiente grafo de ejemplo, se muestran las relaciones entre personas, dispositivos móviles, intereses y sistemas operativos:

![Base de datos de ejemplo que muestra personas, dispositivos e intereses](./media/graph-introduction/sample-graph.png)

Los gráficos son útiles para entender una amplia gama de conjuntos de datos en los sectores de la ciencia, la tecnología y los negocios. Las bases de datos de gráficos permiten modelar y almacenar gráficos de forma natural y eficaz, lo que las hace útiles para muchos escenarios. Suelen ser bases de datos NoSQL, ya que estos casos de uso también necesitan a menudo flexibilidad en el esquema y una iteración rápida.

Los grafos ofrecen una técnica de modelado de datos novedosa y eficaz. Pero este hecho, por sí solo, no es razón suficiente para usar una base de datos de gráficos. Para muchos casos de uso y patrones que implican recorridos de gráfico, los gráficos superan el rendimiento de las bases de datos SQL y NoSQL tradicionales en órdenes de magnitud. Esta diferencia en rendimiento se amplía aún más al recorrer varias relaciones, como los amigos de mis amigos.

Puede combinar los cruces seguros rápidos que proporcionan las bases de datos de grafos con algoritmos de grafo (como búsqueda en profundidad, búsqueda en amplitud y el algoritmo de Dijkstra) para resolver problemas en diversos dominios, como las redes sociales, la administración de contenido, el geoespacial y las recomendaciones.

## <a name="planet-scale-graphs-with-azure-cosmos-db"></a>Grafos a escala mundial con Azure Cosmos DB
Azure Cosmos DB es una base de datos de gráficos totalmente administrada que ofrece una distribución global, escalado elástico de almacenamiento y rendimiento, indexación y consulta automáticas, niveles de coherencia ajustables y compatibilidad con el estándar TinkerPop.

![Arquitectura de grafo de Azure Cosmos DB](./media/graph-introduction/cosmosdb-graph-architecture.png)

Azure Cosmos DB ofrece las siguientes funcionalidades diferenciadas en comparación con otras bases de datos de gráficos del mercado:

* Rendimiento y almacenamiento escalables de manera elástica

 Los gráficos en el mundo real necesitan escalarse más allá de la capacidad de un único servidor. Con Azure Cosmos DB, puede escalar sus grafos sin problemas en varios servidores. También puede escalar el rendimiento de su gráfico de forma independiente en función de los patrones de acceso. Azure Cosmos DB admite bases de datos de grafos que pueden escalarse a tamaños de almacenamiento prácticamente ilimitado y rendimiento aprovisionado.

* Replicación de varias regiones

 Azure Cosmos DB replica con transparencia los datos de gráfico en todas las regiones que se han asociado a la cuenta. La replicación permite desarrollar aplicaciones que requieren acceso global a los datos. Hay equilibrios en los ámbitos de la coherencia, disponibilidad y rendimiento, y las garantías correspondientes. Azure Cosmos DB proporciona conmutación por error regional transparente con las API de hospedaje múltiple. Puede escalar el rendimiento y el almacenamiento de forma elástica en todo el mundo.

* Consultas rápidas y recorridos con la sintaxis conocida de Gremlin

 Almacene vértices heterogéneos y bordes, y consulte estos documentos por medio de la sintaxis conocida de Gremlin. Azure Cosmos DB usa una tecnología de indexación estructurada mediante registros sin bloqueos y sumamente simultánea para indexar automáticamente todo el contenido. Esta capacidad permite realizar consultas y recorridos enriquecidos en tiempo real sin necesidad de especificar sugerencias de esquemas, índices secundarios ni vistas. Aprenda más en [Compatibilidad de Azure Cosmos DB con grafos Gremlin](gremlin-support.md).

* Completamente administrada

 Azure Cosmos DB elimina la necesidad de administrar recursos de bases de datos y máquinas. Al ser un servicio de Microsoft Azure completamente administrado, no es preciso que administre las máquinas virtuales, que implemente y configure software, que administre el escalado ni que realice complejas actualizaciones en la capa de datos. Se hacen copias de seguridad de todos los grafos, además de protegerlos ante errores regionales. Puede agregar fácilmente una cuenta de Azure Cosmos DB y aprovisionar capacidad a medida que la necesite, para poder concentrarse en su aplicación en lugar de en la operación y administración de la base de datos.

* Indexación automática

 De manera predeterminada, Azure Cosmos DB indexa automáticamente todas las propiedades en los nodos y bordes del grafo, y no espera ni requiere ningún esquema ni la creación de índices secundarios.

* Compatibilidad con Apache TinkerPop

 Azure Cosmos DB es compatible de forma nativa con el estándar Apache TinkerPop de código abierto y se puede integrar con otros sistemas de gráficos habilitados para TinkerPop. Por tanto, puede migrar fácilmente desde una base de datos de gráficos diferente, como Titan o Neo4j, o usar Azure Cosmos DB con marcos de análisis de gráficos como [Apache Spark GraphX](spark-connector-graph.md).

* Niveles de coherencia ajustables

 Seleccione entre cinco niveles de coherencia bien definidos para lograr un equilibrio óptimo entre coherencia y rendimiento. Para las consultas y las operaciones de lectura, Azure Cosmos DB ofrece cinco niveles de coherencia diferentes: segura, obsolescencia limitada, sesión, prefijo coherente y posible. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios razonables entre la coherencia, la disponibilidad y la latencia. Encontrará más información en [Niveles de coherencia de datos optimizables en Azure Cosmos DB](consistency-levels.md).

Además, Azure Cosmos DB puede usar varios modelos, como documento y gráfico, en los mismos contenedores o bases de datos. Puede usar una colección de documentos para almacenar los datos de gráfico en paralelo con los documentos. Puede usar las consultas SQL sobre JSON y las de Gremlin para consultar los mismos datos como un gráfico.

## <a name="get-started"></a>Introducción
Puede usar la interfaz de línea de comandos (CLI) de Azure, Azure PowerShell o Azure Portal con compatibilidad con la API Graph para crear cuentas de Azure Cosmos DB. Después de crear las cuentas, Azure Portal proporciona un punto de conexión de servicio como `https://<youraccount>.gremlin.cosmosdb.azure.com`, que ofrece un front-end WebSocket para Gremlin. Puede configurar las herramientas compatibles con TinkerPop, como [Gremlin Console](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console), para que se conecten a este punto de conexión y compilar aplicaciones en Java, Node.js o cualquier controlador cliente de Gremlin.

En la siguiente tabla se muestran los controladores Gremlin populares que puede usar en Azure Cosmos DB:

| Descargar | Documentación |
| --- | --- |
| [Java](https://mvnrepository.com/artifact/com.tinkerpop.gremlin/gremlin-java) |[Gremlin JavaDoc](http://tinkerpop.apache.org/javadocs/current/full/) |
| [Node.js](https://www.npmjs.com/package/gremlin) |[Gremlin-JavaScript en Github](https://github.com/jbmusso/gremlin-javascript) |
| [Gremlin Console](https://tinkerpop.apache.org/downloads.html) |[Documentación de TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#gremlin-console) |

Azure Cosmos DB también proporciona una biblioteca de .NET con métodos de extensión de Gremlin basada en los [SDK de Azure Cosmos DB](sql-api-sdk-dotnet.md) a través de NuGet. Esta biblioteca proporciona un servidor Gremlin "en proceso" que se puede usar para conectarse directamente a las particiones de datos.

| Descargar | Documentación |
| --- | --- |
| [.NET](https://www.nuget.org/packages/Microsoft.Azure.Graphs/) |[Microsoft.Azure.Graphs](https://msdn.microsoft.com/library/azure/dn948556.aspx) |

Mediante el [Emulador de Azure Cosmos DB ](local-emulator.md) puede llevar a cabo el desarrollo y las pruebas localmente mediante la API Graph de .NET anterior sin necesidad de crear una suscripción de Azure ni incurrir en ningún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el emulador, puede cambiar al usar una cuenta de almacenamiento de Azure Cosmos DB en la nube.

> [!NOTE]
> El soporte técnico para validar consultas de Gremlin en el [Emulador de Azure Cosmos DB](local-emulator.md) solo está disponible a través de la API Graph de .NET.

## <a name="scenarios-for-graph-support-of-azure-cosmos-db"></a>Escenarios de compatibilidad con gráficos de Azure Cosmos DB
Estos son algunos escenarios donde se puede usar la compatibilidad con gráficos de Azure Cosmos DB:

* Redes sociales

 Al combinar datos sobre los clientes y sus interacciones con otras personas, puede desarrollar experiencias personalizadas, predecir el comportamiento de los clientes o conectar a personas con otras que tengan intereses similares. Azure Cosmos DB puede utilizarse para administrar redes sociales y realizar un seguimiento de los datos y las preferencias de los usuarios.

* Motores de recomendaciones

 Este escenario se usa con frecuencia en el sector minorista. Al combinar información sobre productos, usuarios e interacciones de usuario, como comprar, examinar o clasificar un artículo, puede generar recomendaciones personalizadas. Azure Cosmos DB, con su latencia baja, escalado elástico y compatibilidad nativa con grafos, es ideal para el modelado de estas interacciones.

* Geoespaciales

 Muchas aplicaciones en telecomunicaciones, logística y programación de viajes necesitan encontrar una ubicación de interés dentro de un área o localizar la mejor ruta entre dos ubicaciones o la más corta. Azure Cosmos DB es una opción natural para estos problemas.

* Internet de las cosas

 Con la red y las conexiones entre dispositivos IoT modeladas como un grafo, se puede conocer mejor el estado de los dispositivos y recursos. También se puede ver de qué manera los cambios en una parte de la red pueden afectar a otra.

## <a name="next-steps"></a>pasos siguientes
Para aprender más sobre la compatibilidad con grafos en Azure Cosmos DB:

* Empiece con el [tutorial de gráficos en Azure Cosmos DB](create-graph-dotnet.md).
* Obtenga más información sobre cómo [consultar grafos en Azure Cosmos DB mediante Gremlin](gremlin-support.md).
