---
title: "Patrón de diseño de Azure Cosmos DB: aplicaciones de redes sociales | Microsoft Docs"
description: "Obtenga información sobre un patrón de diseño para redes sociales con la flexibilidad de almacenamiento de Azure Cosmos DB y otros servicios de Azure."
keywords: aplicaciones de redes sociales
services: cosmos-db
author: ealsur
manager: jhubbard
editor: 
documentationcenter: 
ms.assetid: 2dbf83a7-512a-4993-bf1b-ea7d72e095d9
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/29/2017
ms.author: mimig
ms.translationtype: Human Translation
ms.sourcegitcommit: a643f139be40b9b11f865d528622bafbe7dec939
ms.openlocfilehash: 0691d6730fddbf9b6145d4a39da94a1762516c2c
ms.contentlocale: es-es
ms.lasthandoff: 05/31/2017


---
# <a name="going-social-with-azure-cosmos-db"></a>Redes sociales y Azure Cosmos DB
Vivir en una sociedad enormemente interconectada significa que, en algún momento de la vida, uno formará parte de una **red social**. Las redes sociales se usan para mantenerse en contacto con amigos, compañeros de trabajo y familiares y, a veces, para compartir intereses comunes con otras personas.

Como ingenieros o desarrolladores, es probable que nos hayamos preguntado cómo almacenan e interconectan nuestros datos estas redes sociales, o incluso puede que nos hayan encargado crear o diseñar la arquitectura de una nueva red social para un segmento de mercado específico. Y ahí surge la gran duda: ¿cómo se almacenan todos estos datos?

Supongamos que queremos crear una red social nueva en la que los usuarios puedan publicar artículos y materiales como imágenes, vídeos o incluso música. En esta red social, los usuarios podrán comentar y valorar las publicaciones con fines de clasificación. Además, en la página de aterrizaje del sitio web, los usuarios podrán ver e interactuar con una fuente de publicaciones. En un principio, esto no parece muy complejo, pero, para mayor simplicidad, lo dejaremos así (podríamos diseñar fuentes de usuario personalizadas en las que se tuvieran en cuenta las relaciones, pero supera el objetivo de este artículo).

Entonces, ¿cómo y dónde almacenamos estos datos?

En caso de tener experiencia en bases de datos SQL o al menos tener nociones de [modelado de datos relacional](https://en.wikipedia.org/wiki/Relational_model) , podría verse tentado a empezar a dibujar algo parecido a esto:

![Diagrama que ilustra un modelo relacional relativo](./media/social-media-apps/social-media-apps-sql.png) 

Sin embargo, a pesar de ser una estructura de datos perfectamente normalizada, no sería posible escalarla. 

No me malinterpreten, he trabajado con bases de datos SQL toda mi vida y me parecen excelentes, pero, al igual que todos los modelos, prácticas y plataformas de software, no son perfectas para todos los escenarios.

¿Por qué SQL no es la mejor opción en este escenario? Echemos un vistazo a la estructura de una entrada única, si quisiéramos esa publicación en un sitio web o una aplicación, tendríamos que realizar una consulta con 8 combinaciones de tablas para mostrar una sola publicación. Ahora, cree una secuencia de entradas que se cargue dinámicamente y aparezcan en la pantalla.

Por supuesto, podríamos usar una instancia de SQL enorme con capacidad suficiente para resolver miles de consultas con las combinaciones necesarias para servir el contenido, pero ¿por qué habríamos de hacerlo cuando existe una solución más sencilla?

## <a name="the-nosql-road"></a>La vía NoSQL
Este artículo lo ayudará a modelar los datos de su plataforma social con una base de datos NoSQL de Azure, [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/), de una manera rentable y aprovechando otras funciones de Azure Cosmos DB, como la [API Graph de Gremlin](../cosmos-db/graph-introduction.md). Con un enfoque [NoSQL](https://en.wikipedia.org/wiki/NoSQL), almacenamiento de datos en formato JSON y la aplicación de [desnormalización](https://en.wikipedia.org/wiki/Denormalization), nuestra publicación, que antes era complicada, ahora puede transformarse en un único [documento](https://en.wikipedia.org/wiki/Document-oriented_database):


    {
        "id":"ew12-res2-234e-544f",
        "title":"post title",
        "date":"2016-01-01",
        "body":"this is an awesome post stored on NoSQL",
        "createdBy":User,
        "images":["http://myfirstimage.png","http://mysecondimage.png"],
        "videos":[
            {"url":"http://myfirstvideo.mp4", "title":"The first video"},
            {"url":"http://mysecondvideo.mp4", "title":"The second video"}
        ],
        "audios":[
            {"url":"http://myfirstaudio.mp3", "title":"The first audio"},
            {"url":"http://mysecondaudio.mp3", "title":"The second audio"}
        ]
    }

Además, puede obtenerse con una sola consulta y sin combinaciones. Esto es mucho más sencillo, directo, económico y requiere menos recursos para conseguir un mejor resultado.

Azure Cosmos DB se asegura de que todas las propiedades se indexen con su indexación automática, que incluso se puede [personalizar](indexing-policies.md). El enfoque sin esquema nos permite almacenar documentos con estructuras dinámicas y diferentes. Así, por ejemplo, si en el futuro queremos que las publicaciones tengan una lista de categorías o hashtags asociados, Cosmos DB gestionará los nuevos documentos con los atributos agregados y sin ningún esfuerzo adicional requerido por nuestra parte.

Los comentarios en una publicación pueden tratarse del mismo modo que otras publicaciones con una propiedad primaria (esto simplifica la asignación de objetos). 

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":User2,
        "parent":"ew12-res2-234e-544f"
    }

    {
        "id":"asd2-fee4-23gc-jh67",
        "title":"Ditto!",
        "date":"2016-01-03",
        "createdBy":User3,
        "parent":"ew12-res2-234e-544f"
    }

Por otro lado, todas las interacciones sociales pueden almacenarse en un objeto independiente como contadores:

    {
        "id":"dfe3-thf5-232s-dse4",
        "post":"ew12-res2-234e-544f",
        "comments":2,
        "likes":10,
        "points":200
    }

Para la creación de fuentes solo es necesario crear documentos que puedan contener una lista de identificadores de publicación con un orden de importancia determinado:

    [
        {"relevance":9, "post":"ew12-res2-234e-544f"},
        {"relevance":8, "post":"fer7-mnb6-fgh9-2344"},
        {"relevance":7, "post":"w34r-qeg6-ref6-8565"}
    ]

Podríamos tener un flujo "más reciente" con publicaciones ordenadas por fecha de creación o un flujo "favoritos" con las publicaciones que más han gustado en las últimas 24 horas. Incluso se puede implementar un flujo personalizado para cada usuario basado en lógica como seguidores e intereses, y seguiría siendo una lista de publicaciones. La cuestión es cómo crear estas listas, pero el rendimiento de lectura no se verá afectado. Una vez que se adquiere una de estas listas, se emite una consulta única a Cosmos DB con el [operador IN](documentdb-sql-query.md#WhereClause) para obtener páginas de publicaciones a la vez.

Los flujos de fuente se pueden generar mediante procesos en segundo plano de [Azure App Service](https://azure.microsoft.com/services/app-service/): [Webjobs](../app-service-web/web-sites-create-web-jobs.md). Una vez que se crea una publicación, el procesamiento en segundo plano puede activarse mediante el uso de [Azure Storage](https://azure.microsoft.com/services/storage/) [Queues](../storage/storage-dotnet-how-to-use-queues.md) y Webjobs desencadenados mediante el [SDK Azure Webjobs](../app-service-web/websites-dotnet-webjobs-sdk.md), implementando la propagación de publicaciones dentro de los flujos en función de nuestra lógica personalizada. 

La puntuación y los "me gusta" de una publicación se pueden procesar de manera aplazada usando esta misma técnica para crear un entorno coherente.

Con los seguidores es más complicado. Cosmos DB tiene un límite de tamaño máximo del documento y los documentos de gran tamaño de lectura/escritura pueden afectar a la escalabilidad de la aplicación. Por esta razón, debería plantearse almacenar los seguidores como un documento con esta estructura:

    {
        "id":"234d-sd23-rrf2-552d",
        "followersOf": "dse4-qwe2-ert4-aad2",
        "followers":[
            "ewr5-232d-tyrg-iuo2",
            "qejh-2345-sdf1-ytg5",
            //...
            "uie0-4tyg-3456-rwjh"
        ]
    }

Este enfoque podría funcionar con un usuario que tenga unos miles de seguidores, pero si se trata de una persona famosa este enfoque produciría un tamaño de documento grande y terminaría superándose el límite de tamaño de documentos.

Para solucionar esto, podemos adoptar un enfoque mixto. Como parte del documento Estadísticas de usuario, podemos almacenar el número de seguidores:

    {
        "id":"234d-sd23-rrf2-552d",
        "user": "dse4-qwe2-ert4-aad2",
        "followers":55230,
        "totalPosts":452,
        "totalPoints":11342
    }

Y el gráfico real de los seguidores puede almacenarse con [API Graph de Gremlin](../cosmos-db/graph-introduction.md) de Azure Cosmos DB, para crear [vértices](http://mathworld.wolfram.com/GraphVertex.html) para cada usuario y [bordes](http://mathworld.wolfram.com/GraphEdge.html) que mantienen relaciones del tipo "A sigue a B". La API Graph le permite no solo obtener los seguidores de un determinado usuario, sino también crear consultas más complejas que incluso permiten sugerir personas en común. Si se agregan al gráfico de las categorías de contenido que gustan o encantan a los individuos, podemos comenzar a componer escenarios que incluyen detección inteligente de contenido, sugerencias de contenido que gustan a las personas a las que seguimos o encontrar personas con quienes puede que tengamos mucho en común.

El documento de Estadísticas de usuario se sigue pudiendo usar para crear tarjetas en la interfaz de usuario o vistas previas rápidas de los perfiles.

## <a name="the-ladder-pattern-and-data-duplication"></a>El modelo "Escalera" y la duplicación de datos
Como habrá observado en el documento JSON que hace referencia a una publicación, hay varias apariciones de un usuario. Y, como ya habrá imaginado, esto significa que la información que representa a un usuario, dada esta desnormalización, puede existir en más de un lugar.

Para permitir consultas más rápidas, incurrimos en duplicación de datos. El problema con este efecto secundario es que si, por alguna acción, cambian los datos de un usuario, necesitaremos buscar todas las actividades que ha hecho y actualizarlas. Lo cierto es que no parece muy práctico.

Nosotros vamos a resolverlo mediante la identificación de los atributos clave de un usuario que mostramos en nuestra aplicación para cada actividad. Si en nuestra aplicación mostramos una publicación tan solo con el nombre y la imagen del creador, ¿por qué almacenar todos los datos del usuario en el atributo "createdBy"? Si en cada comentario solo se muestra la imagen del usuario, no es necesario el resto de su información. Y aquí es donde entra en juego lo que yo llamo el modelo "escalera".

Tomemos como ejemplo información de usuario:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "address":"742 Evergreen Terrace",
        "birthday":"1983-05-07",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "username":"johndoe",
        "password":"some_encrypted_phrase",
        "totalPoints":100,
        "totalPosts":24
    }

Al examinar esta información, podemos detectar rápidamente cuál es información importante y cuál no lo es, creando así una "escalera":

![Diagrama de un modelo de escalera](./media/social-media-apps/social-media-apps-ladder.png)

El paso más pequeño se denomina UserChunk: es el fragmento mínimo de información que identifica a un usuario y se usa para la duplicación de datos. Al reducir el tamaño de los datos duplicados a la información que "mostraremos", se reduce también la posibilidad de actualizaciones masivas.

El paso intermedio se denomina usuario: son todos los datos que se usarán en la mayoría de las consultas dependientes del rendimiento en Cosmos DB, a los que más se accede y los más críticos. Incluye la información representada por un UserChunk.

El mayor es el usuario extendido. Incluye toda la información crítica del usuario además de otros datos que no es necesario leer rápidamente o cuyo uso es eventual (como el proceso de inicio de sesión). Estos datos pueden almacenarse fuera de Cosmos DB, en Azure SQL Database o en tablas de Azure Storage.

¿Por qué habríamos de dividir el usuario e incluso almacenar esta información en diferentes lugares? Porque desde el punto de vista del rendimiento, cuanto mayores sean los documentos, más costosas serán las consultas. No sobrecargue los documentos; que tengan la información adecuada para realizar todas las consultas dependientes del rendimiento de la red social, y almacene el resto de la información adicional para escenarios eventuales, como modificaciones del perfil completo, inicios de sesión e incluso minería de datos para análisis de uso e iniciativas de macrodatos. No nos importa que la recopilación de información para minería de datos sea lenta, ya que se ejecuta en la Base de datos SQL de Microsoft Azure. Lo que sí nos importa es que los usuarios tengan una experiencia rápida y ligera. La apariencia de un usuario almacenado en Cosmos DB sería la siguiente:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "username":"johndoe"
        "email":"john@doe.com",
        "twitterHandle":"@john"
    }

Asimismo, una solicitud Post tendría el aspecto siguiente:

    {
        "id":"1234-asd3-54ts-199a",
        "title":"Awesome post!",
        "date":"2016-01-02",
        "createdBy":{
            "id":"dse4-qwe2-ert4-aad2",
            "username":"johndoe"
        }
    }

Si se produce una modificación que afecte a uno de los atributos del fragmento, es fácil encontrar los documentos afectados mediante consultas que señalen a los atributos indexados (SELECT * FROM posts p WHERE p.createdBy.id == “edited_user_id”) y, a continuación, actualizando los fragmentos.

## <a name="the-search-box"></a>El cuadro de búsqueda
Los usuarios generarán, con suerte, una gran cantidad de contenido. Debemos proporcionar la capacidad de buscar y encontrar contenido que podría no estar directamente en los flujos de contenido de los usuarios, quizás porque no siguen a los creadores o quizás porque están buscando una publicación antigua de hace seis meses.

Afortunadamente, y gracias a que estamos usando Azure DocumentDB, podemos implementar fácilmente un motor de búsqueda con [Búsqueda de Azure](https://azure.microsoft.com/services/search/) en un par de minutos, y sin escribir una sola línea de código (aparte de, obviamente, el proceso de búsqueda y la interfaz de usuario).

¿Por qué es tan fácil?

Azure Search implementa lo que llaman "[indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx)"; es decir, procesos en segundo plano que se enlazan en los repositorios de datos y automáticamente agregan, actualizan o quitan objetos en los índices. Son compatibles con [indexadores de Azure SQL Database](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexadores de Blobs de Azure](../search/search-howto-indexing-azure-blob-storage.md) y, afortunadamente, [indexadores de Cosmos DB](../search/search-howto-index-documentdb.md). La transición de información de Cosmos DB a Azure Search es sencilla porque ambos almacenan la información en formato JSON; tan solo debemos [crear nuestro índice](../search/search-create-index-portal.md) y asignar los atributos de los documentos que deseamos indexar. En cuestión de minutos (según el tamaño de los datos), todo el contenido estará disponible para buscarse con la mejor solución de búsqueda como servicio en la infraestructura de nube. 

Para obtener más información sobre Búsqueda de Azure, puede consultar la guía [Hitchhiker’s Guide to Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/)(Guía de búsqueda de Hitchhiker).

## <a name="the-underlying-knowledge"></a>La información subyacente
Después de almacenar todo este contenido que crece y crece diariamente, podríamos pensar: ¿qué puedo hacer con todo este flujo de información de mis usuarios?

La respuesta es sencilla: póngala a trabajar y aprenda de ella.

Pero, ¿qué podemos aprender? Por ejemplo, [análisis de opinión](https://en.wikipedia.org/wiki/Sentiment_analysis), recomendaciones de contenido según las preferencias de un usuario o, incluso, un moderador automatizado que garantiza que todo el contenido que publique nuestra red social sea adecuado para todos los públicos.

Ahora que ya está interesado, probablemente pensará que necesita un doctorado en ciencias matemáticas para extraer estos patrones y la información de los archivos y las bases de datos, pero no es así.

[Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/), que forma parte de [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), es un servicio en la nube totalmente administrado que permite crear flujos de trabajo mediante algoritmos en una sencilla interfaz de arrastrar y colocar, programar sus propios algoritmos en [R](https://en.wikipedia.org/wiki/R_\(programming_language\)) o usar algunas de las API integradas y listas para usar, como [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator](https://www.microsoft.com/moderator) o [Recommendations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

Para posibilitar cualquiera de estos escenarios de Machine Learning, podemos usar [Azure Data Lake](https://azure.microsoft.com/services/data-lake-store/) para introducir la información de distintos orígenes, así como [U-SQL](https://azure.microsoft.com/documentation/videos/data-lake-u-sql-query-execution/) para procesar dicha información y generar una salida que pueda tratar Azure Machine Learning.

Otra opción disponible es usar [Microsoft Cognitive Services](https://www.microsoft.com/cognitive-services) para analizar el contenido de nuestros usuarios; no solo podemos comprenderlos mejor (mediante el análisis de lo que escriben con [Text Analytics API](https://www.microsoft.com/cognitive-services/en-us/text-analytics-api)), sino que también podemos detectar contenido no deseado o contenido para adultos y actuar en consecuencia con [Computer Vision API](https://www.microsoft.com/cognitive-services/en-us/computer-vision-api). Cognitive Services incluye una gran cantidad de soluciones listas para usar que no requieren ningún conocimiento de Machine Learning para usarlas.

## <a name="a-planet-scale-social-experience"></a>Una experiencia social a escala mundial
Hay un último, pero no por ello menos importante, tema que abordaremos: la **escalabilidad**. Cuando se diseña una arquitectura, resulta fundamental que cada componente pueda escalar por sí mismo, ya sea porque es necesario procesar más datos o porque se desea tener una mayor cobertura geográfica (o ambas opciones). Afortunadamente, llevar a cabo una tarea así de compleja es una **experiencia inmediata** con Cosmos DB.

Cosmos DB admite la [creación de particiones dinámica](https://azure.microsoft.com/blog/10-things-to-know-about-documentdb-partitioned-collections/) inmediata mediante la creación automática de particiones según una **clave de partición** determinada (que se define como uno de los atributos en los documentos). Definir la clave de partición correcta se debe realizar en el momento del diseño, teniendo en cuenta los [procedimientos recomendados](../cosmos-db/partition-data.md#designing-for-partitioning) disponibles; en caso de una experiencia social, la estrategia de creación de particiones debe alinearse con la forma en que consulta (se prefieren las lecturas dentro de la misma partición) y escribe (evite las "zonas activas" distribuyendo las escrituras en varias particiones). Algunas opciones son: particiones basadas en una clave temporal (día/mes/semana), por categoría de contenido, por región geográfica, por usuario; en realidad, todo depende de cómo consultará los datos y cómo los mostrará en la experiencia social. 

Un punto interesante que vale la pena mencionar es que Cosmos DB ejecutará las consultas (incluidas las [agregaciones](https://azure.microsoft.com/blog/planet-scale-aggregates-with-azure-documentdb/)) en todas las particiones de forma transparente, por lo que no necesita agregar ninguna lógico a medida que crecen los datos.

Con el tiempo, a la larga el tráfico crecerá y su consumo de recursos (que se mide en [RU](request-units.md) o unidades de solicitud) aumentará. Leerá y escribirá con más frecuencia a medida que crece la base de usuarios y ellos comenzarán a crear y leer más contenido; la capacidad de **escalar el rendimiento** resulta vital. Aumentar las unidades de solicitud es muy simple: podemos hacerlo con algunos clics en Azure Portal o a través de la [emisión de comandos mediante la API](https://docs.microsoft.com/rest/api/documentdb/replace-an-offer).

![Escalado vertical y definición de una clave de partición](./media/social-media-apps/social-media-apps-scaling.png)

¿Qué ocurre si las cosas siguen mejorando y los usuarios de otra región, otro país u otro continente descubren su plataforma y comienzan a usarla? ¡Una gran sorpresa!

Pero... momento: pronto se da cuenta de que su experiencia con la plataforma no es óptima, porque están tan lejos de la región de operaciones que la latencia es enorme y, por supuesto, no quiere que abandonen la plataforma. ¡Si tan solo hubiese una forma sencilla de **extender su alcance global**! Y la hay.

Cosmos DB le permite [replicar los datos global](../cosmos-db/tutorial-global-distribution-documentdb.md) y transparentemente con un par de clics y seleccionar de forma automática entre las regiones disponibles del [código de cliente](../cosmos-db/tutorial-global-distribution-documentdb.md). Esto también significa que tiene [varias regiones de conmutación por error](regional-failover.md). 

Cuando replica globalmente los datos, debe asegurarse de que los clientes puedan aprovecharlos. Si usa un front-end web o tiene acceso a las API desde clientes para dispositivos móviles, puede implementar [Azure Traffic Manager](https://azure.microsoft.com/services/traffic-manager/) y clonar la instancia de Azure App Service en todas las regiones deseadas mediante una [configuración de rendimiento](../app-service-web/web-sites-traffic-manager.md) para admitir la cobertura global extendida. Cuando los clientes tienen acceso al front-end o a las API, se enrutarán a la instancia de App Service más cercana, que, a su vez, se conectará a la réplica local de Cosmos DB.

![Incorporación de la cobertura global en la plataforma social](./media/social-media-apps/social-media-apps-global-replicate.png)

## <a name="conclusion"></a>Conclusión
Este artículo trata de ofrecer alternativas de bajo costo y excelentes resultados en el proceso integral de creación de redes sociales en Azure. Con este objetivo, se fomenta el uso de una solución de almacenamiento en varias capas y distribución de datos denominada "Escalera".

![Diagrama de interacción entre los servicios de Azure para redes sociales](./media/social-media-apps/social-media-apps-azure-solution.png)

La verdad es que no hay ninguna fórmula milagrosa para este tipo de escenarios, sino que es la sinergia creada mediante la combinación de excelentes servicios lo que nos permite crear grandes experiencias: la velocidad y la libertad de Azure Cosmos DB para proporcionar una gran aplicación social; la inteligencia de una solución de búsqueda de primera clase como Azure Search; la flexibilidad de Azure App Services para hospedar aplicaciones independientes del lenguaje y eficaces procesos en segundo plano; los ampliables Azure Storage y Azure SQL Database para guardar enormes cantidades de datos; y la potencia analítica de Azure Machine Learning para crear conocimiento e inteligencia que proporcionen información a nuestros procesos y nos ayuden a suministrar el contenido correcto a los usuarios adecuados.

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre los casos de uso de Cosmos DB, consulte [Casos de uso comunes de Cosmos DB](use-cases.md).
