---
title: Casos de uso y escenarios NoSQL comunes para Azure DocumentDB | Microsoft Docs
description: "Obtenga información sobre los cinco principales casos de uso para DocumentDB: contenido generado por usuarios, registro de eventos, datos del catálogo, datos de las preferencias del usuario e Internet de las cosas (IoT)."
services: documentdb
author: h0n
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: eca68a58-1a8c-4851-8cf8-6e4d2b889905
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2017
ms.author: hawong
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 9a0ba106b4c2aded8aaac673dfba3610bd4ca0fb
ms.lasthandoff: 03/24/2017


---

# <a name="common-documentdb-use-cases"></a>Casos de uso comunes de DocumentDB
En este artículo se proporciona información general acerca de varios casos de uso comunes de DocumentDB.  Las recomendaciones de este artículo sirven como punto de partida para desarrollar su aplicación con DocumentDB.   

Después de leer este artículo, podrá responder a las preguntas siguientes: 

* ¿Cuáles son los casos de uso comunes de DocumentDB?
* ¿Cuáles son las ventajas del uso de DocumentDB para aplicaciones comerciales?
* ¿Cuáles son las ventajas de usar DocumentDB como almacén de datos para los sistemas de Internet de las cosas (IoT)?
* ¿Cuáles son las ventajas del uso de DocumentDB para aplicaciones web y móviles?

## <a name="common-use-cases-for-documentdb"></a>Casos de uso habituales de DocumentDB
Azure DocumentDB es una base de datos NoSQL de ámbito general que se utiliza en un amplio abanico de aplicaciones y casos de uso. Constituye una buena opción para cualquier aplicación que necesite tiempos de respuesta reducidos, de milésimas de segundo, y precise escalarse rápidamente. A continuación se exponen algunos de los atributos de DocumentDB que la convierten en una opción perfecta para aplicaciones de alto rendimiento.

* DocumentDB crea de forma nativa particiones para sus datos a fin de ofrecer una alta disponibilidad y escalabilidad.
* DocumentDB cuenta con almacenamiento respaldado por SSD con tiempos de respuesta con una reducida latencia, de milésimas de segundo.
* La compatibilidad de DocumentDB con niveles de coherencia, como eventual, sesión y obsolescencia entrelazada, permite disfrutar de una excelente relación precio-rendimiento. 
* DocumentDB presenta un modelo de precios flexible e idóneo para los datos que mide el almacenamiento y el rendimiento por separado.
* El modelo de rendimiento reservado de DocumentDB permite que pueda planear en lo relativo a las operaciones de lectura/escritura en lugar de a la CPU, la memoria o las E/S por segundo del hardware subyacente.
* El diseño de DocumentDB le permite adaptarse a enormes volúmenes de solicitudes que pueden ascender a miles de millones al día.

Estos atributos resultan útiles en el caso de aplicaciones web, móviles, de juegos y de IoT que necesitan tiempos de respuesta bajos y deben procesar enormes cantidades de lecturas y escrituras. 

## <a name="iot-and-telematics"></a>IoT y telemáticas
A menudo, los casos de uso de IoT comparten algunos patrones en el modo de ingerir, procesar y almacenar datos.  En primer lugar, estos sistemas necesitan ingerir ráfagas de datos procedentes de sensores de dispositivos de distintas configuraciones regionales. Después, estos sistemas procesan y analizan datos de streaming para obtener perspectivas en tiempo real. A continuación, los datos se archivan en un almacenamiento en frío para el análisis por lotes. Microsoft Azure ofrece sofisticados servicios que pueden aplicarse a los casos de uso de IoT, incluidos Azure Event Hubs, Azure DocumentDB, Azure Stream Analytics, Azure Notification Hub, Azure Machine Learning, Azure HDInsight y PowerBI. 

![Arquitectura de referencia de IoT de Azure DocumentDB](./media/documentdb-use-cases/documentdb-iot.png)

Las ráfagas de datos pueden ser asumidas por los Centros de eventos de Azure, ya que ofrecen una ingesta de datos de alto rendimiento con baja latencia. Los datos ingeridos que requieran procesamiento para obtener información en tiempo real se pueden dirigir a Azure Stream Analytics para realizar el análisis en tiempo real. Los datos pueden cargarse en DocumentDB para realizar consultas ad hoc. Una vez que los datos se carguen en DocumentDB, estarán listos para recibir consultas.  Los datos de DocumentDB pueden utilizarse como datos de referencia en el proceso de análisis en tiempo real. Además, los datos pueden afinarse y procesarse aún más conectando los datos de DocumentDB a HDInsight para trabajos de Pig, Hive o Map/Reduce.  Los datos afinados se cargan luego de nuevo en DocumentDB para la creación de informes.   

Para ver una solución de IoT de ejemplo utilizando DocumentDB, EventHubs y Storm, consulte el [repositorio de ejemplos de hdinsight-storm en GitHub](https://github.com/hdinsight/hdinsight-storm-examples/).

Para más información sobre las posibilidades de Azure para IoT, consulte [Cree el Internet de sus cosas](http://www.microsoft.com/en-us/server-cloud/internet-of-things.aspx). 

## <a name="retail-and-marketing"></a>Minoristas y marketing
DocumentDB se utilizan habitualmente en el sector minorista para almacenar los datos del catálogo. Entre los escenarios de uso de los datos de catálogo se encuentra el almacenamiento y la consulta de un conjunto de atributos para entidades como personas, lugares y productos.  Algunos ejemplos de datos de catálogo son cuentas de usuario, catálogos de productos, registros de dispositivo para Internet de las cosas y sistemas de lista de materiales.  Los atributos de estos datos pueden variar y cambiar con el tiempo para satisfacer los requisitos de la aplicación.  

Piense por ejemplo en un catálogo de productos para un proveedor de piezas para vehículos. Cada pieza puede tener sus propios atributos además de los atributos comunes que comparten todas las piezas.  Además, los atributos de una pieza específica pueden cambiar al año siguiente, cuando se lance un nuevo modelo.  Como almacén de documentos JSON, DocumentDB admite esquemas flexibles y datos jerárquicos, y por ello es adecuado para almacenar los datos del catálogo de productos.

![Arquitectura de referencia del catálogo de minoristas de Azure DocumentDB](./media/documentdb-use-cases/documentdb-retail-catalog.png)

 Además, los datos almacenados en DocumentDB pueden integrarse con HDInsight para el análisis de macrodatos a través de trabajos de Pig, Hive o asignación y reducción. Para obtener detalles sobre el conector de Hadoop para DocumentDB, consulte [Ejecución de un trabajo de Hadoop con DocumentDB y HDInsight](documentdb-run-hadoop-with-hdinsight.md).

 ![Arquitectura de referencia de pedidos de minoristas de Azure DocumentDB](./media/documentdb-use-cases/documentdb-retail-orders.png)
## <a name="gaming"></a>Juegos
El nivel de base de datos es un componente fundamental de las aplicaciones de juegos. Los juegos modernos realizan el procesamiento de los elementos gráficos en los clientes de consola o dispositivos móviles, pero utilizan la nube para ofrecer contenido personalizado y a medida, como estadísticas dentro del juego, integración con las redes sociales y los marcadores de puntuaciones. A menudo, los juegos requieren latencias de un solo milisegundo para que las lecturas y escrituras proporcionen una experiencia de juego atractiva. Una base de datos de un juego debe ser rápida y capaz de manejar los picos masivos en la velocidad de las solicitudes cuando se inicia un nuevo juego y se actualizan las características.

Juegos como [The Walking Dead: No Man's Land](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/), de [Next Games](http://www.nextgames.com/), y [Halo 5: Guardians](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/) utilizan DocumentDB. DocumentDB proporciona las siguientes ventajas a los desarrolladores de juegos:

* DocumentDB permite que el rendimiento se escale o reduzca verticalmente de manera flexible. Esto permite que los juegos puedan actualizar los perfiles y las estadísticas de docenas de millones de jugadores simultáneos realizando una simple llamada de API.
* DocumentDB admite lecturas y escrituras en milisegundos para contribuir a evitar cualquier retardo mientras el usuario esté disfrutando del juego.
* La indexación automática de DocumentDB permite filtrar por varias propiedades distintas en tiempo real (p. ej., ubicar a jugadores por sus id. de jugador internos o de Game Center, Facebook o Google, o bien una consulta según la pertenencia del jugador a un gremio). Esto es posible sin crear una infraestructura compleja de indexación o particionamiento.
* Las características sociales, como los mensajes de chat dentro del juego, las pertenencias a gremios de los jugadores, los retos completados, los marcadores de puntuaciones se implementan con más facilidad gracias a un esquema flexible.
* DocumentDB, como una plataforma como servicio (PaaS) administrada, requiere tareas de configuración y administración mínimas para facilitar la rápida creación de iteraciones y reducir el tiempo necesario para llevar los productos al mercado.

![Arquitectura de referencia de juegos de Azure DocumentDB](./media/documentdb-use-cases/documentdb-gaming-architecture.png)

## <a name="web-and-mobile-applications"></a>Aplicaciones web y móviles
DocumentDB se usa normalmente en aplicaciones web y móviles y sirve, sobre todo, para modelar interacciones sociales, para la integración con servicios de terceros y para la creación de experiencias personalizadas enriquecidas. Los SDK de DocumentDB pueden utilizarse para generar completas aplicaciones de iOS y Android mediante el conocido [marco Xamarin](documentdb-mobile-apps-with-xamarin.md).  

### <a name="social-applications"></a>Aplicaciones sociales
Un caso de uso común para DocumentDB es almacenar y consultar el contenido generado por usuarios para aplicaciones web y móviles, especialmente aplicaciones de medios sociales. Algunos ejemplos de contenido generado por usuarios son las sesiones de chat, los tweets, las entradas de blog, las valoraciones y los comentarios. A menudo, el contenido generado por usuarios en las aplicaciones de medios sociales es una mezcla de texto sin formato, propiedades, etiquetas y relaciones que no están limitadas por una estructura rígida. Los contenidos como chats, comentarios y publicaciones pueden almacenarse en DocumentDB sin requerir transformaciones u objetos complejos en las capas de asignación relacional.  Se pueden agregar o modificar fácilmente las propiedades de datos para satisfacer los requisitos a medida que los desarrolladores recorran en iteración el código de la aplicación, permitiendo así un rápido desarrollo.  

Las aplicaciones que se integran con redes sociales de terceros deben responder a los esquemas cambiantes de estas redes. Como los datos se indizan automáticamente de forma predeterminada en DocumentDB, los datos están listos para ser consultados en cualquier momento. Por lo tanto, estas aplicaciones tienen la flexibilidad para recuperar las proyecciones en función de sus necesidades respectivas.

Muchas de las aplicaciones sociales se ejecutan a escala global y pueden presentar patrones de uso impredecibles. La flexibilidad en el escalado del almacén de datos es esencial, ya que el nivel de la aplicación se escala para satisfacer la demanda de uso.  Es posible escalar horizontalmente mediante la adición de particiones de datos adicionales a una cuenta de DocumentDB.  Además, también puede crear cuentas adicionales de DocumentDB en varias regiones. Para ver la disponibilidad del servicio de DocumentDB por regiones, consulte [Regiones de Azure](https://azure.microsoft.com/regions/#services).

![Arquitectura de referencia de aplicaciones web de Azure DocumentDB](./media/documentdb-use-cases/documentdb-web.png)

### <a name="personalization"></a>Personalización
En la actualidad, las aplicaciones modernas incluyen experiencias y vistas complejas. Normalmente son dinámicas, encargándose de las preferencias o los estados de ánimo del usuario y las necesidades de personalización de marca. Por lo tanto, las aplicaciones requieren la capacidad de recuperar la configuración personalizada eficazmente para representar elementos de interfaz de usuario y experiencias rápidamente. 

JSON es un formato eficaz para representar datos de diseño de la interfaz de usuario que no solo es ligero, sino que también se puede interpretar fácilmente mediante JavaScript. DocumentDB ofrece niveles de coherencia ajustables que permiten lecturas rápidas con escrituras de baja latencia. Por lo tanto, el almacenamiento de datos de diseño de la interfaz de usuario, incluida la configuración personalizada como documentos JSON en DocumentDB, es un medio eficaz para obtener estos datos a través de la red.

## <a name="next-steps"></a>Pasos siguientes
Para empezar a usar DocumentDB, puede crear una [cuenta](https://azure.microsoft.com/pricing/free-trial/) y, a continuación, seguir nuestra [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para obtener información sobre DocumentDB y buscar la información que necesita. 

O bien, si desea leer más acerca de los usuarios que emplean DocumentDB, tiene a su disposición los siguientes testimonios de clientes:

* [Sulekha.com](https://customers.microsoft.com/en-US/story/sulekha-uses-azure-documentdb-to-connect-customers-and-businesses-across-india). Sulekha utiliza Azure DocumentDB para conectar a clientes y empresas en la India.
* [NewOrbit](https://customers.microsoft.com/en-US/story/neworbit-takes-flight-with-azure-documentdb). NewOrbit alza el vuelo con Azure DocumentDB.
* [Affinio](https://customers.microsoft.com/en-US/doclink/affinio-switches-from-aws-to-azure-documentdb-to-harness-social-data-at-scale). Affinio cambia de AWS a Azure DocumentDB para aprovechar datos sociales a escala.
* [Next Games](https://azure.microsoft.com//blog/the-walking-dead-no-mans-land-game-soars-to-1-with-azure-documentdb/). The Walking Dead: No Man's Land game soars to #1 supported by Azure DocumentDB (El juego The Walking Dead: No Man's Land se hace con el primer puesto con la ayuda de Azure DocumentDB).
* [Halo](https://azure.microsoft.com/blog/how-halo-5-guardians-implemented-social-gameplay-using-azure-documentdb/). How Halo 5 implemented social gameplay using Azure DocumentDB (Halo 5 utiliza Azure DocumentDB para implementar el juego social).
* [Galería de Cortana Analytics](https://azure.microsoft.com/blog/cortana-analytics-gallery-a-scalable-community-site-built-on-azure-documentdb/). Galería de Cortana Analytics: un sitio de la comunidad escalable basado en Azure DocumentDB.
* [Breeze](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18602). Integrador líder del sector ofrece a empresas multinacionales perspectivas globales en minutos con tecnologías en la nube flexibles.
* [News Republic](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18639). Incorporación de inteligencia a las noticias para proporcionar información con propósito a ciudadanos comprometidos. 
* [SGS International](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18653). Para obtener un color uniforme en todo el mundo, las principales marcas recurren a SGS. Y SGS recurre a Azure.
* [Telenor](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18608). Telenor, empresa líder a nivel global, usa la nube para moverse con la velocidad de una startup. 
* [XOMNI](https://customers.microsoft.com/Pages/CustomerStory.aspx?recid=18667). El almacén del futuro se ejecuta con la rapidez en la búsqueda y la sencillez en el flujo de datos.
* [Nucleo](https://customers.microsoft.com/story/azure-based-software-platform-breaks-down-barriers-bet). Esta plataforma de software basada en Azure rompe las barreras que existen entre las empresas y los clientes.
* [Weka](https://customers.microsoft.com/story/weka-smart-fridge-improves-vaccine-management-so-more-people-can-be-protected-against-diseases). Smart-Fridge de Weka mejora la administración de vacunas de forma que más gente puede protegerse contra enfermedades.
* [Orange Tribes](https://customers.microsoft.com/en-US/story/theres-more-to-that-food-app-than-meets-the-eye-or-the-mouth). Hay más para esa aplicación de alimentos de lo que parece.
* [Real Madrid](https://customers.microsoft.com/en-US/story/real-madrid-brings-the-stadium-closer-to-450-million-f). Real Madrid acerca el estadio a 450 millones de aficionados de todo el mundo con Microsoft Cloud.
* [Tuku](https://customers.microsoft.com/en-US/story/tuku-makes-car-buying-fun-with-help-from-azure-services). TUKU hace que comprar un vehículo sea una experiencia divertida gracias a los servicios de Azure. 

