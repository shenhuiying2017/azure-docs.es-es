---
title: Casos de uso y escenarios comunes para Azure Cosmos DB | Microsoft Docs
description: "Obtenga información sobre los cinco principales casos de uso para Azure Cosmos DB: contenido generado por usuarios, registro de eventos, datos del catálogo, datos de las preferencias del usuario e Internet de las cosas (IoT)."
services: cosmos-db
author: mimig1
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: mimig
ms.openlocfilehash: bcafc999c30d1e72971c8e26e951169ea6b56416
ms.sourcegitcommit: 0e4491b7fdd9ca4408d5f2d41be42a09164db775
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="common-azure-cosmos-db-use-cases"></a>Casos de uso comunes de Azure Cosmos DB
En este artículo se proporciona información general acerca de varios casos de uso comunes de Azure Cosmos DB.  Las recomendaciones de este artículo sirven como punto de partida para desarrollar su aplicación con Cosmos DB.   

Después de leer este artículo, podrá responder a las preguntas siguientes: 

* ¿Cuáles son los casos de uso comunes de Azure Cosmos DB?
* ¿Cuáles son las ventajas del uso de Azure Cosmos DB para aplicaciones comerciales?
* ¿Cuáles son las ventajas de usar Azure Cosmos DB como almacén de datos para los sistemas de Internet de las cosas (IoT)?
* ¿Cuáles son las ventajas del uso de Azure Cosmos DB para aplicaciones web y móviles?

## <a name="introduction"></a>Introducción
[Azure Cosmos DB](../cosmos-db/introduction.md) es un servicio de base de datos de distribución global de Microsoft. El servicio está diseñado para permitir que los clientes escalen de manera elástica e independiente el rendimiento y el almacenamiento en cualquier número de regiones geográficas. Azure Cosmos DB es el primer servicio de bases de datos distribuidas de forma global que ofrece [acuerdos de nivel de servicio](https://azure.microsoft.com/support/legal/sla/cosmos-db/) integrales que abarcan rendimiento, latencia, disponibilidad y coherencia. 

Azure Cosmos DB es una base de datos con varios modelos y de distribución global que se usa en una amplia variedad de aplicaciones y casos de uso. Constituye una buena opción para cualquier aplicación [sin servidor](http://azure.com/serverless) que necesite tiempos de respuesta reducidos, de milésimas de segundo, y precise escalarse rápida y globalmente. Admite varios modelos de datos (par clave-valor, documentos, grafos y columnas) y varias API para el acceso de datos, entre las que se incluyen la [API de MongoDB](mongodb-introduction.md), la [API de SQL](documentdb-introduction.md), [Graph API (Gremlin)](graph-introduction.md) y [Tables API](table-introduction.md) de forma nativa y de forma extensible. 

A continuación se exponen algunos atributos de Azure Cosmos DB que la convierten en una opción perfecta para aplicaciones de alto rendimiento con una ambición mundial.

* Azure Cosmos DB crea de forma nativa particiones para sus datos a fin de ofrecer una alta disponibilidad y escalabilidad. Azure Cosmos DB garantiza un 99,99 % de disponibilidad, rendimiento, latencia baja y coherencia para todas las cuentas de una sola región y todas las cuentas de varias regiones con coherencia moderada, y disponibilidad de lectura del 99,999 % para todas las cuentas de base de datos de varias regiones.
* Azure Cosmos DB cuenta con almacenamiento respaldado por SSD con tiempos de respuesta con una reducida latencia, de milisegundos.
* La compatibilidad de Azure Cosmos DB con niveles de coherencia, como eventual, prefijo coherente, sesión y obsolescencia entrelazada, permite disfrutar de una flexibilidad completa y una excelente relación precio-rendimiento. Ningún servicio de base de datos ofrece la flexibilidad que ofrece Azure Cosmos DB en la coherencia de niveles. 
* Azure Cosmos DB presenta un modelo de precios flexible e idóneo para los datos que mide el almacenamiento y el rendimiento por separado.
* El modelo de rendimiento reservado de Azure Cosmos DB permite que pueda planear en lo relativo a las operaciones de lectura/escritura en lugar de a la CPU, la memoria o las E/S por segundo del hardware subyacente.
* El diseño de Azure Cosmos DB le permite adaptarse a enormes volúmenes de solicitudes que pueden ascender a billones al día.

Estos atributos resultan útiles en el caso de aplicaciones web, móviles, de juegos y de IoT que necesitan tiempos de respuesta bajos y deben procesar enormes cantidades de lecturas y escrituras.

## <a name="iot-and-telematics"></a>IoT y telemáticas
A menudo, los casos de uso de IoT comparten algunos patrones en el modo de ingerir, procesar y almacenar datos.  En primer lugar, estos sistemas necesitan ingerir ráfagas de datos procedentes de sensores de dispositivos de distintas configuraciones regionales. Después, estos sistemas procesan y analizan datos de streaming para obtener perspectivas en tiempo real. A continuación, los datos se archivan en un almacenamiento en frío para el análisis por lotes. Microsoft Azure ofrece sofisticados servicios que pueden aplicarse a los casos de uso de IoT, incluidos Azure Cosmos DB, Azure Event Hubs, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight y PowerBI. 

![Arquitectura de referencia de IoT de Azure Cosmos DB](./media/use-cases/iot.png)

Las ráfagas de datos pueden ser asumidas por Azure Event Hubs, ya que ofrecen una ingesta de datos de alto rendimiento con baja latencia. Los datos ingeridos que requieran procesamiento para obtener información en tiempo real se pueden dirigir a Azure Stream Analytics para realizar el análisis en tiempo real. Los datos pueden cargarse en Azure Cosmos DB para realizar consultas ad hoc. Una vez que los datos se carguen en Azure Cosmos DB, estarán listos para recibir consultas. Además, los nuevos datos y los cambios en los datos existentes se pueden leer en la fuente de cambios. La fuente de cambios es un registro persistente solo para anexar que almacena en orden secuencial los cambios en las colecciones de Cosmos DB. Todos los datos o solo los cambios en los datos de Azure Cosmos DB pueden usarse como referencia como parte del proceso de análisis en tiempo real. Además, los datos pueden afinarse y procesarse aún más conectando los datos de Azure Cosmos DB a HDInsight para trabajos de Pig, Hive o Map/Reduce.  Los datos afinados se cargan luego de nuevo en Azure Cosmos DB para la creación de informes.   

Para ver una solución de IoT de ejemplo con Azure Cosmos DB, EventHubs y Storm, consulte el [repositorio de ejemplos de hdinsight-storm en GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para más información sobre las posibilidades de Azure para IoT, consulte [Cree el Internet de sus cosas](http://www.microsoft.com/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Minoristas y marketing
Azure Cosmos DB se usa habitualmente en las plataformas de comercio electrónico de Microsoft, que ejecutan la Tienda Windows y XBox Live. También se utiliza en el sector minorista para almacenar los datos del catálogo y el abastecimiento de eventos en las canalizaciones de procesamiento de pedidos.

Entre los escenarios de uso de los datos de catálogo se encuentra el almacenamiento y la consulta de un conjunto de atributos para entidades como personas, lugares y productos. Algunos ejemplos de datos de catálogo son las cuentas de usuario, los catálogos de productos, los registros de dispositivo IoT y los sistemas de lista de materiales. Los atributos de estos datos pueden variar y cambiar con el tiempo para satisfacer los requisitos de la aplicación.

Piense por ejemplo en un catálogo de productos para un proveedor de piezas para vehículos. Cada pieza puede tener sus propios atributos además de los atributos comunes que comparten todas las piezas. Además, los atributos de una pieza específica pueden cambiar al año siguiente, cuando se lance un nuevo modelo. Azure Cosmos DB admite esquemas flexibles y datos jerárquicos, y por ello es adecuado para almacenar los datos del catálogo de productos.

![Arquitectura de referencia del catálogo de minoristas de Azure Cosmos DB](./media/use-cases/product-catalog.png)

Azure Cosmos DB se suele utilizar en el abastecimiento de eventos para mejorar las arquitecturas orientadas a eventos mediante su funcionalidad de [fuente de cambios](change-feed.md). La fuente de cambios proporciona a los microservicios de nivel inferior la capacidad de leer de forma confiable e incremental las inserciones y actualizaciones (por ejemplo, los eventos desordenados) realizadas en una instancia de Azure Cosmos DB. Esta funcionalidad se puede aprovechar para proporcionar un almacén de eventos persistente como un agente de mensajes para los eventos de cambio de estado y dirigir el orden del flujo de trabajo de procesamiento entre muchos microservicios (lo que se puede implementar como [funciones de Azure sin servidor](http://azure.com/serverless)).

![Arquitectura de referencia para la ordenación de las canalizaciones de Azure Cosmos DB](./media/use-cases/event-sourcing.png)

Además, los datos almacenados en Azure Cosmos DB pueden integrarse con HDInsight para el análisis de macrodatos a través de trabajos de Apache Spark. Para detalles sobre el conector de Spark para Azure Cosmos DB, consulte [Ejecución de un trabajo de Spark con Cosmos DB y HDInsight](spark-connector.md).

## <a name="gaming"></a>Juegos
El nivel de base de datos es un componente fundamental de las aplicaciones de juegos. Los juegos modernos realizan el procesamiento de los elementos grafos en los clientes de consola o dispositivos móviles, pero utilizan la nube para ofrecer contenido personalizado y a medida, como estadísticas dentro del juego, integración con las redes sociales y los marcadores de puntuaciones. A menudo, los juegos requieren latencias de un solo milisegundo para que las lecturas y escrituras proporcionen una experiencia de juego atractiva. Una base de datos de un juego debe ser rápida y capaz de manejar los picos masivos en la velocidad de las solicitudes cuando se inicia un nuevo juego y se actualizan las características.

Juegos como [The Walking Dead: No Man's Land](https://azure.microsoft.com/blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/), de [Next Games](http://www.nextgames.com/), y [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) usan Azure Cosmos DB. Azure Cosmos DB proporciona las siguientes ventajas a los desarrolladores de juegos:

* Azure Cosmos DB permite que el rendimiento se escale o reduzca verticalmente de manera flexible. Esto permite que los juegos puedan actualizar los perfiles y las estadísticas de docenas de millones de jugadores simultáneos realizando una simple llamada de API.
* Azure Cosmos DB admite lecturas y escrituras en milisegundos para contribuir a evitar cualquier retardo mientras el usuario esté disfrutando del juego.
* La indexación automática de Azure Cosmos DB permite filtrar por varias propiedades distintas en tiempo real (por ejemplo, ubicar a jugadores por sus identificadores internos de jugador o de Game Center, Facebook o Google, o bien consultar según la pertenencia del jugador a un gremio). Esto es posible sin crear una infraestructura compleja de indexación o particionamiento.
* Las características sociales, como los mensajes de chat dentro del juego, las pertenencias a gremios de los jugadores, los retos completados, los marcadores de puntuaciones se implementan con más facilidad gracias a un esquema flexible.
* Azure Cosmos DB, como una plataforma como servicio (PaaS) administrada, requiere tareas de configuración y administración mínimas para facilitar la rápida creación de iteraciones y reducir el tiempo necesario para llevar los productos al mercado.

![Arquitectura de referencia de juegos de Azure Cosmos DB](./media/use-cases/gaming.png)

## <a name="web-and-mobile-applications"></a>Aplicaciones web y móviles
Azure Cosmos DB se usa normalmente en aplicaciones web y móviles y sirve para modelar interacciones sociales, para la integración con servicios de terceros y para la creación de experiencias personalizadas enriquecidas. Los SDK de Cosmos DB pueden utilizarse para generar completas aplicaciones de iOS y Android mediante el conocido [marco Xamarin](mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplicaciones sociales
Un caso de uso común para Azure Cosmos DB es almacenar y consultar el contenido generado por usuarios para aplicaciones web, móviles y de medios sociales. Algunos ejemplos de contenido generado por usuarios son las sesiones de chat, los tweets, las entradas de blog, las valoraciones y los comentarios. A menudo, el contenido generado por usuarios en las aplicaciones de medios sociales es una mezcla de texto sin formato, propiedades, etiquetas y relaciones que no están limitadas por una estructura rígida. Los contenidos como chats, comentarios y publicaciones pueden almacenarse en Cosmos DB sin requerir transformaciones u objetos complejos en las capas de asignación relacional.  Se pueden agregar o modificar fácilmente las propiedades de datos para satisfacer los requisitos a medida que los desarrolladores recorran en iteración el código de la aplicación, permitiendo así un rápido desarrollo.  

Las aplicaciones que se integran con redes sociales de terceros deben responder a los esquemas cambiantes de estas redes. Como los datos se indexan automáticamente de forma predeterminada en Cosmos DB, los datos están listos para ser consultados en cualquier momento. Por lo tanto, estas aplicaciones tienen la flexibilidad para recuperar las proyecciones en función de sus necesidades respectivas.

Muchas de las aplicaciones sociales se ejecutan a escala global y pueden presentar patrones de uso impredecibles. La flexibilidad en el escalado del almacén de datos es esencial, ya que el nivel de la aplicación se escala para satisfacer la demanda de uso.  Es posible escalar horizontalmente mediante la adición de particiones de datos adicionales a una cuenta de Cosmos DB.  Además, también puede crear cuentas adicionales de Cosmos DB en varias regiones. Para ver la disponibilidad del servicio de Cosmos DB por regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services).

![Arquitectura de referencia de aplicaciones web de Azure Cosmos DB](./media/use-cases/apps-with-global-reach.png)

### <a name="personalization"></a>Personalización
En la actualidad, las aplicaciones modernas incluyen experiencias y vistas complejas. Normalmente son dinámicas, encargándose de las preferencias o los estados de ánimo del usuario y las necesidades de personalización de marca. Por lo tanto, las aplicaciones requieren la capacidad de recuperar la configuración personalizada eficazmente para representar elementos de interfaz de usuario y experiencias rápidamente. 

JSON, un formato compatible con Cosmos DB, es eficaz para representar datos de diseño de la interfaz de usuario que no solo es ligero, sino que también se puede interpretar fácilmente mediante JavaScript. Cosmos DB ofrece niveles de coherencia ajustables que permiten lecturas rápidas con escrituras de baja latencia. Por lo tanto, el almacenamiento de datos de diseño de la interfaz de usuario, incluida la configuración personalizada como documentos JSON en Cosmos DB, es un medio eficaz para obtener estos datos a través de la red.

![Arquitectura de referencia de aplicaciones web de Azure Cosmos DB](./media/use-cases/personalization.png)

## <a name="next-steps"></a>pasos siguientes
Para empezar a trabajar con Azure Cosmos DB, siga nuestras [guías de inicio rápido](create-sql-api-dotnet.md), que le indican el proceso de creación de una cuenta y cómo comenzar a usar Cosmos DB. 

O bien, si desea leer más acerca de los usuarios que emplean Cosmos DB, tiene a su disposición los siguientes testimonios de clientes:

* [Jet.com](https://jet.com). Una empresa de comercio electrónico que aspira a ser la número uno ejecuta su solución en Microsoft Cloud y usa Cosmos DB a una escala global.
* [Asos.com](http://www.asos.com/). Asos.com es una tienda británica en línea de moda y artículos de belleza. Dirigida principalmente a adultos jóvenes, Asos vende más de 850 marcas, además de su propia gama de vestuario y accesorios.
* [Toyota](https://www.toyota.com/). Toyota Motor Corporation es un fabricante de automóviles japonés. Toyota usó Cosmos DB para una aplicación de IoT a nivel mundial.
* [Citrix](https://customers.microsoft.com/story/citrix). Citrix desarrolla una solución de inicio de sesión único con Azure Service Fabric y Azure Cosmos DB
* [TEXA](https://customers.microsoft.com/story/texaspa) La revolucionaria solución IoT de TEXA para propietarios de vehículos permite ahorrar tiempo, dinero y combustible... y posiblemente salvar vidas.
* [Domino's Pizza](https://www.dominos.com). Domino's Pizza Inc. es una cadena de pizzerías de Estados Unidos.
* [Johnson Controls](http://www.johnsoncontrols.com). Johnson Controls es un líder en tecnología diversificada y de varios sectores a nivel mundial, con una gran variedad de clientes en más de 150 países.
* [Microsoft Windows, Universal Store, Azure IoT Hub, Xbox Live y otros servicios a escala de Internet](https://azure.microsoft.com/blog/how-azure-documentdb-planet-scale-nosql-helps-run-microsoft-s-own-businesses/). Cómo Microsoft compila de forma masiva servicios escalables con Azure Cosmos DB.
* [Equipo de Datos y análisis de Microsoft](https://customers.microsoft.com/story/microsoftdataandanalytics). El equipo de Datos y análisis de Microsoft alcanza una colección de macrodatos a escala mundial con Azure Cosmos DB
* [Sulekha.com](https://customers.microsoft.com/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha usa Azure Cosmos DB para conectar a clientes y empresa en la India.
* [NewOrbit](https://customers.microsoft.com/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit alza el vuelo con Azure Cosmos DB.
* [Affinio](https://customers.microsoft.com/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio cambia de AWS a Azure Cosmos DB para aprovechar datos sociales a escala.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). The Walking Dead: No Man's Land game soars to #1 supported by Azure Cosmos DB (El juego The Walking Dead: No Man's Land se hace con el primer puesto con la ayuda de Azure Cosmos DB).
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). How Halo 5 implemented social gameplay using Azure Cosmos DB (Halo 5 usa Azure Cosmos DB para implementar el juego social).
* [Galería de Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galería de Cortana Analytics: un sitio de la comunidad escalable basado en Azure Cosmos DB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Integrador líder del sector ofrece a empresas multinacionales perspectivas globales en minutos con tecnologías en la nube flexibles.
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Incorporación de inteligencia a las noticias para proporcionar información con propósito a ciudadanos comprometidos. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Para obtener un color uniforme en todo el mundo, las principales marcas recurren a SGS. Y SGS recurre a Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Telenor, empresa líder a nivel global, usa la nube para moverse con la velocidad de una startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). El almacén del futuro se ejecuta con la rapidez en la búsqueda y la sencillez en el flujo de datos.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Esta plataforma de software basada en Azure rompe las barreras que existen entre las empresas y los clientes.
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Smart-Fridge de Weka mejora la administración de vacunas de forma que más gente puede protegerse contra enfermedades.
* [Orange Tribes](https://customers.microsoft.com/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Hay más para esa aplicación de alimentos de lo que parece.
* [Real Madrid](https://customers.microsoft.com/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid acerca el estadio a 450 millones de aficionados de todo el mundo con Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU hace que comprar un vehículo sea una experiencia divertida gracias a los servicios de Azure.
