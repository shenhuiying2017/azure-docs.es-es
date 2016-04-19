<properties 
	pageTitle="Patrón de diseño de DocumentDB: Aplicaciones de redes sociales | Microsoft Azure" 
	description="Obtenga información sobre un patrón de diseño para redes sociales con la flexibilidad de almacenamiento de DocumentDB y otros servicios de Azure." 
	keywords="aplicaciones de redes sociales"
	services="documentdb" 
	authors="ealsur" 
	manager="" 
	editor="" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/28/2016" 
	ms.author="mimig"/>

# Redes sociales y DocumentDB

Vivir en una sociedad enormemente interconectada significa que, en algún momento de la vida, uno formará parte de una **red social**. Las redes sociales se usan para mantenerse en contacto con amigos, compañeros de trabajo y  
familiares y, a veces, para compartir intereses comunes con otras personas.

Como ingenieros o desarrolladores, es probable que nos hayamos preguntado cómo almacenan e interconectan nuestros datos estas redes sociales, o incluso puede que nos hayan encargado crear o diseñar la arquitectura de una nueva red social para un segmento de mercado específico. Y ahí surge la gran duda: ¿cómo se almacenan todos estos datos?

Supongamos que queremos crear una red social nueva en la que los usuarios puedan publicar artículos y materiales como imágenes, vídeos o incluso música. En esta red social, los usuarios podrán comentar y valorar las publicaciones con fines de clasificación. Además, en la página de aterrizaje del sitio web, los usuarios podrán ver e interactuar con una fuente de publicaciones. En un principio, esto no parece muy complejo, pero, para mayor simplicidad, lo dejaremos así (podríamos diseñar fuentes de usuario personalizadas en las que se tuvieran en cuenta las relaciones, pero supera el objetivo de este artículo).

Entonces, ¿cómo y dónde almacenamos estos datos?

En caso de tener experiencia en bases de datos SQL o al menos tener nociones de [modelado de datos relacional](https://en.wikipedia.org/wiki/Relational_model), podría verse tentado a empezar a dibujar algo parecido a esto:

![Diagrama que ilustra un modelo relacional relativo](./media/documentdb-social-media-apps/social-media-apps-sql.png)

Sin embargo, a pesar de ser una estructura de datos perfectamente normalizada, no sería posible escalarla.

No me malinterpreten, he trabajado con bases de datos SQL toda mi vida y me parecen excelentes, pero, al igual que todos los modelos, prácticas y plataformas de software, no son perfectas para todos los escenarios.

¿Por qué SQL no es la mejor opción en este escenario? Echemos un vistazo a la estructura de una publicación única; si quisiera mostrarla en un sitio web o en una aplicación, tendría que crear una consulta con... ¡8 combinaciones de tablas para mostrar una sola publicación! Imagine ahora un flujo de publicaciones que se cargan dinámicamente y aparecen en la pantalla y entenderá a dónde quiero llegar.

Por supuesto, podríamos usar una instancia de SQL enorme con capacidad suficiente para resolver miles de consultas con las combinaciones necesarias para servir el contenido, pero ¿por qué habríamos de hacerlo cuando existe una solución más sencilla?

## La vía NoSQL

Existen bases de datos de gráficos especiales que pueden [ejecutarse en Azure](http://neo4j.com/developer/guide-cloud-deployment/#_windows_azure), pero no son económicas y requieren servicios IaaS (infraestructura como servicio, máquinas virtuales principalmente) y mantenimiento. En este artículo me centraré en una solución de menor costo que funcionará en la mayoría de los escenarios y se ejecuta en la base de datos NoSQL [DocumentDB](https://azure.microsoft.com/services/documentdb/) de Azure. Con un enfoque [NoSQL](https://en.wikipedia.org/wiki/NoSQL), almacenamiento de datos en formato JSON y la aplicación de [desnormalización](https://en.wikipedia.org/wiki/Denormalization), nuestra publicación, que antes era complicada, ahora puede transformarse en un único [documento](https://en.wikipedia.org/wiki/Document-oriented_database):

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

Azure DocumentDB se asegura de que todas las propiedades se indexen con su [indexación automática](documentdb-indexing.md), que puede ser incluso [personalizada](documentdb-indexing-policies.md). El enfoque sin esquema nos permite almacenar documentos con estructuras dinámicas y diferentes. Así, por ejemplo, si en el futuro queremos que las publicaciones tengan una lista de categorías o hashtags asociados, DocumentDB gestionará los nuevos documentos con los atributos agregados y sin ningún esfuerzo adicional requerido por nuestra parte.

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

Podríamos tener un flujo "más reciente" con publicaciones ordenadas por fecha de creación o un flujo "favoritos" con las publicaciones que más han gustado en las últimas 24 horas. Incluso se puede implementar un flujo personalizado para cada usuario basado en lógica como seguidores e intereses, y seguiría siendo una lista de publicaciones. La cuestión es cómo crear estas listas, pero el rendimiento de lectura no se verá afectado. Una vez que se adquiere una de estas listas, se emite una consulta única a DocumentDB con el [operador IN](documentdb-sql-query.md#where-clause) para obtener páginas de publicaciones a la vez.

Los flujos de fuente se pueden generar mediante procesos en segundo plano del [Servicio de aplicaciones de Azure](https://azure.microsoft.com/services/app-service/): [Webjobs](../app-service-web/web-sites-create-web-jobs.md). Una vez que se crea una publicación, el procesamiento en segundo plano puede activarse mediante el uso de [colas](../storage/storage-dotnet-how-to-use-queues.md) del [almacenamiento de Azure](https://azure.microsoft.com/services/storage/) y trabajos web (Webjobs) desencadenados mediante el [SDK Azure Webjobs](../app-service-web/websites-dotnet-webjobs-sdk.md), implementando la propagación de publicaciones dentro de los flujos en función de nuestra lógica personalizada.

La puntuación y los "me gusta" de una publicación se pueden procesar de manera aplazada usando esta misma técnica para crear un entorno coherente.

## El modelo "Escalera" y la duplicación de datos

Como habrá observado en el documento JSON que hace referencia a una publicación, hay varias apariciones de un usuario. Y, como ya habrá imaginado, esto significa que la información que representa a un usuario, dada esta desnormalización, puede existir en más de un lugar.

Para permitir consultas más rápidas, incurrimos en duplicación de datos. El problema con este efecto secundario es que si, por alguna acción, cambian los datos de un usuario, necesitaremos buscar todas las actividades que ha hecho y actualizarlas. Lo cierto es que no parece muy práctico.

Las bases de datos de gráficos lo resuelven a su manera, y nosotros vamos a resolverlo mediante la identificación de los atributos de clave de un usuario que mostramos en nuestra aplicación para cada actividad. Si en nuestra aplicación mostramos una publicación tan solo con el nombre y la imagen del creador, ¿por qué almacenar todos los datos del usuario en el atributo "createdBy"? Si en cada comentario solo se muestra la imagen del usuario, no es necesario el resto de su información. Y aquí es donde entra en juego lo que yo llamo el modelo "escalera".

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

![Diagrama de un modelo de escalera](./media/documentdb-social-media-apps/social-media-apps-ladder.png)

El paso más pequeño se denomina UserChunk: es el fragmento mínimo de información que identifica a un usuario y se usa para la duplicación de datos. Al reducir el tamaño de los datos duplicados a la información que "mostraremos", se reduce también la posibilidad de actualizaciones masivas.

El paso intermedio se denomina usuario: son todos los datos que se usarán en la mayoría de las consultas dependientes del rendimiento en DocumentDB, a los que más se accede y los más críticos. Incluye la información representada por un UserChunk.

El mayor es el usuario extendido. Incluye toda la información crítica del usuario además de otros datos que no es necesario leer rápidamente o cuyo uso es eventual (como el proceso de inicio de sesión). Estos datos pueden almacenarse fuera de DocumentDB, en la base de datos SQL de Microsoft Azure o en tablas de almacenamiento de Azure.

¿Por qué habríamos de dividir el usuario e incluso almacenar esta información en diferentes lugares? Porque el espacio de almacenamiento en DocumentDB no es infinito y, desde el punto de vista del rendimiento, cuanto mayores sean los documentos, más costosas serán las consultas. No sobrecargue los documentos; que tengan la información adecuada para realizar todas las consultas dependientes del rendimiento de la red social, y almacene el resto de la información adicional para escenarios eventuales, como modificaciones del perfil completo, inicios de sesión e incluso minería de datos para análisis de uso e iniciativas de macrodatos. No nos importa que la recopilación de información para minería de datos sea lenta, ya que se ejecuta en la Base de datos SQL de Microsoft Azure. Lo que sí nos importa es que los usuarios tengan una experiencia rápida y ligera. La apariencia de un usuario almacenado en DocumentDB sería la siguiente:

    {
        "id":"dse4-qwe2-ert4-aad2",
        "name":"John",
        "surname":"Doe",
        "email":"john@doe.com",
        "twitterHandle":"@john",
        "totalPoints":100,
        "totalPosts":24,
        "following":{
            "count":2,
            "list":[
                UserChunk1, UserChunk2
            ]
        }
    }

Si se produce una modificación que afecte a uno de los atributos del fragmento, es fácil encontrar los documentos afectados mediante consultas que señalen a los atributos indexados (SELECT * FROM posts p WHERE p.createdBy.id == “edited\_user\_id”) y, a continuación, actualizando los fragmentos.

## El cuadro de búsqueda

Los usuarios generarán, con suerte, una gran cantidad de contenido. Debemos proporcionar la capacidad de buscar y encontrar contenido que podría no estar directamente en los flujos de contenido de los usuarios, quizás porque no siguen a los creadores o quizás porque están buscando una publicación antigua de hace seis meses.

Afortunadamente, y gracias a que estamos usando Azure DocumentDB, podemos implementar fácilmente un motor de búsqueda con [Búsqueda de Azure](https://azure.microsoft.com/services/search/) en un par de minutos y sin escribir una sola línea de código (aparte de, obviamente, el proceso de búsqueda y la interfaz de usuario).

¿Por qué es tan fácil?

Búsqueda de Azure implementa lo que llaman [indexadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), procesos en segundo plano que se enlazan en los repositorios de datos y automáticamente agregan, actualizan o quitan objetos en los índices. Son compatibles con [indexadores de Base de datos SQL de Azure](https://blogs.msdn.microsoft.com/kaevans/2015/03/06/indexing-azure-sql-database-with-azure-search/), [indexadores de Blobs de Azure](../search/search-howto-indexing-azure-blob-storage.md) y, afortunadamente, [indexadores de Azure DocumentDB](../documentdb/documentdb-search-indexer.md). La transición de información de DocumentDB a Búsqueda de Azure es sencilla porque ambos almacenan la información en formato JSON; tan solo debemos [crear nuestro índice](../search/search-create-index-portal.md) y asignar los atributos de los documentos que deseamos indexar. Y ya está, en cuestión de minutos (según el tamaño de los datos), todo el contenido estará disponible para buscarse con la mejor solución de búsqueda como servicio en la infraestructura de nube.

Para obtener más información sobre Búsqueda de Azure, puede visitar la guía [Hitchhiker’s Guide to Search](https://blogs.msdn.microsoft.com/mvpawardprogram/2016/02/02/a-hitchhikers-guide-to-search/).

## La información subyacente

Después de almacenar todo este contenido que crece y crece diariamente, podríamos pensar: ¿qué puedo hacer con todo este flujo de información de mis usuarios?

La respuesta es sencilla: póngala a trabajar y aprenda de ella.

Pero, ¿qué podemos aprender? Por ejemplo, [análisis de opinión](https://en.wikipedia.org/wiki/Sentiment_analysis), recomendaciones de contenido según las preferencias de un usuario o incluso un moderador automatizado que garantiza que todo el contenido publicado por nuestra red social sea adecuado para la familia.

Ahora que ya está interesado, probablemente pensará que necesita un doctorado en ciencias matemáticas para extraer estos patrones y la información de los archivos y las bases de datos, pero no es así.

[Aprendizaje automático de Azure](https://azure.microsoft.com/services/machine-learning/), parte del [Cortana Intelligence Suite](https://www.microsoft.com/en/server-cloud/cortana-analytics-suite/overview.aspx), es un servicio en la nube totalmente administrado que permite crear flujos de trabajo mediante algoritmos en una sencilla interfaz de arrastrar y colocar, programar sus propios algoritmos en [R] (https://en.wikipedia.org/wiki/R_(programming_language)) o usar algunas de las API integradas y listas para usar, como [Text Analytics](https://gallery.cortanaanalytics.com/MachineLearningAPI/Text-Analytics-2), [Content Moderator](https://www.microsoft.com/moderator) o [Recommendations](https://gallery.cortanaanalytics.com/MachineLearningAPI/Recommendations-2).

## Conclusión

Este artículo trata de ofrecer alternativas de bajo costo y excelentes resultados en el proceso integral de creación de redes sociales en Azure. Con este objetivo, se fomenta el uso de una solución de almacenamiento en varias capas y distribución de datos denominada "Escalera".

![Diagrama de interacción entre los servicios de Azure para redes sociales](./media/documentdb-social-media-apps/social-media-apps-azure-solution.png)

La verdad es que no hay ninguna fórmula milagrosa para este tipo de escenarios, sino que es la sinergia creada mediante la combinación de excelentes servicios lo que nos permite crear grandes experiencias: la velocidad y la libertad de Azure DocumentDB para proporcionar una gran aplicación social; la inteligencia de una solución de búsqueda de primera clase como Búsqueda de Azure; la flexibilidad de Servicios de aplicaciones de Azure para hospedar aplicaciones independientes del lenguaje y eficaces procesos en segundo plano; los ampliables Almacenamiento de Azure y Base de datos SQL de Azure para guardar ingentes cantidades de datos; y la potencia analítica de Aprendizaje automático de Azure para crear conocimiento e inteligencia que proporcionen información a nuestros procesos y nos ayuden a suministrar el contenido correcto a los usuarios adecuados.

## Pasos siguientes

Obtenga más información sobre el modelado de datos en el artículo [Modelado de datos en DocumentDB](documentdb-modeling-data.md). Si está interesado en otros casos de uso de DocumentDB, consulte [Casos de uso comunes de DocumentDB](documentdb-use-cases.md).

U obtenga más información acerca de DocumentDB siguiendo la [ruta de aprendizaje de DocumentDB](https://azure.microsoft.com/documentation/learning-paths/documentdb/).

<!---HONumber=AcomDC_0406_2016-->