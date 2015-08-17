<properties 
	pageTitle="Introducción a Microsoft Azure DocumentDB | Microsoft Azure" 
	description="Obtenga información acerca de Azure DocumentDB, una base de datos de documentos NoSQL y su valor para aplicaciones en la nube y móviles. Aprenda también cómo administra datos y cómo puede usarla en el desarrollo de aplicaciones." 
	services="documentdb" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/20/2015" 
	ms.author="mimig"/>

#Introducción a DocumentDB de Microsoft Azure

Este artículo proporciona una introducción a la Base de datos de documentos de Microsoft Azure, un servicio de base de datos de documentos NoSQL para desarrolladores, profesionales de TI y responsables de la toma de decisiones empresariales.

Se recomienda comenzar por ver el siguiente vídeo de presentación de dos minutos y probar nuestra [Área de consultas](http://www.documentdb.com/sql/demo), donde puede probar DocumentDB y ejecutar consultas SQL en nuestro conjunto de datos.

> [AZURE.VIDEO what-is-azure-documentdb]

A continuación, vuelva a este artículo, donde conocerá las respuestas a las preguntas siguientes:

-	[¿Qué es DocumentDB y qué valor ofrece a la nube y a las aplicaciones móviles?](#what-is-docdb)
-	[¿Cómo se administran los datos en DocumentDB y cómo se tiene acceso al servicio?](#data-management)
-	[¿Cómo puedo desarrollar aplicaciones con DocumentDB?](#develop)
-	[¿Cuáles son los pasos que debo seguir para crear una aplicación de DocumentDB?](#next-steps)  

##<a name="what-is-docdb"></a>¿Qué es Azure DocumentDB?  

Las aplicaciones modernas producen, consumen y responden rápidamente a volúmenes de datos muy grandes. Estas aplicaciones evolucionan con mucha rapidez y lo mismo ocurre con el esquema de datos subyacente. En respuesta a esto, cada vez más desarrolladores optan por las bases de datos de documentos NoSQL libres de esquemas por ser soluciones sencillas, rápidas y escalables para almacenar y procesar datos, a la vez que permiten conservar la capacidad de iterar rápidamente sobre los modelos de datos de las aplicaciones y las fuentes de datos no estructuradas. Sin embargo, muchas bases de datos libres de esquemas no permiten realizar consultas complejas ni procesar transacciones, lo que dificulta la administración de datos avanzados. Esto es donde entra en juego la DocumentDB. Microsoft desarrolló DocumentDB para que cumpliera estos requisitos al administrar los datos de las aplicaciones actuales.

DocumentDB es un servicio de base de datos de documentos NoSQL diseñado para aplicaciones web y móviles modernos. La Base de datos de documentos ofrece de manera consistente capacidades de lectura y escritura rápidas, flexibilidad de esquemas, así como la capacidad de escalar fácilmente una base de datos en función de las necesidades. DocumentDB permite realizar consultas específicas complejas mediante lenguaje SQL. Asimismo, admite niveles de coherencia bien definidos y permite el uso de lenguaje JavaScript, además de procesar transacciones de varios documentos usando el modelo de programación conocido de procedimientos almacenados, desencadenadores y UDF.

DocumentDB admite de manera nativa documentos JSON, lo que facilita la iteración de los esquemas de aplicaciones. Abarca la ubicuidad de JSON y JavaScript, lo que elimina la discordancia entre el esquema de base de datos y los objetos definidos de aplicación. La integración de JavaScript también permite a los desarrolladores ejecutar lógica de aplicación de manera eficaz y directa desde el motor de base de datos en una transacción de base de datos.

Azure DocumentDB ofrece las siguientes capacidades y ventajas clave:

-	**Consultas ad hoc con sintaxis SQL familiares:** almacene los diferentes documentos JSON en DocumentDB y consúltelos a través de una sintaxis SQL conocida. DocumentDB utiliza una tecnología de indexación estructurada mediante registros sin bloqueos y sumamente concurrente para indexar automáticamente el contenido de todo documento. Esto permite consultas enriquecidas en tiempo real sin necesidad de especificar consejos de esquemas, índices secundarios o vistas.

-	**Ejecución de JavaScript dentro de la base de datos:** lógica de aplicaciones expresa como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) con JavaScript estándar. Esto permite que la lógica de su aplicación opere con datos JSON sin preocuparse de la discordancia entre la aplicación y el esquema de la base de datos. DocumentDB proporciona una ejecución transaccional completa de la lógica de aplicaciones de JavaScript directamente dentro del motor de la base de datos. La integración profunda de JavaScript permite la ejecución de las operaciones INSERTAR, REEMPLAZAR, ELIMINAR y SELECCIONAR desde un programa JavaScript como una transacción aislada.

-	**Niveles de coherencia ajustables:** seleccione entre cuatro niveles de coherencia bien definidos para lograr un equilibrio óptimo entre la coherencia y el rendimiento. Para las consultas y las operaciones de lectura, DocumentDB ofrece cuatro niveles de coherencia diferentes: fuerte, obsolescencia entrelazada, sesión y eventual. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia.

-	**Completamente administrados:** elimine la necesidad de administrar recursos de bases de datos y máquinas. Como un servicio de Microsoft Azure completamente administrado, no es necesario que administre las máquinas virtuales, administrar y configurar software o realizar complejas actualizaciones a nivel de datos. Se realiza una copia de seguridad de cada base de datos además de protegerlas contra fallas regionales. Puede agregar fácilmente una cuenta de DocumentDB y aprovisionar capacidad a medida que la necesita, lo que le permite concentrarse en su aplicación en lugar de la operación y administración de la base de datos.

-	**Rendimiento y almacenamiento escalable de manera flexible:** escale o reduzca verticalmente DocumentDB para satisfacer las necesidades de la aplicación. El escalado se realiza a través de unidades detalladas (colecciones) del almacenamiento y rendimiento reservados en copia de seguridad en SSD. Puede escalar de manera flexible DocumentDB con un rendimiento predecible al crear más unidades, a medida que su aplicación crece.

-	**Diseño abierto:** comience rápidamente usando las destrezas y herramientas existentes. La programación de DocumentDB es sencilla, asequible y no requiere la adopción de herramientas nuevas o de extensiones personalizadas de JSON o JavaScript. Puede tener acceso a todas las funciones de la base de datos incluido el procesamiento de CRUD, las consultas y JavaScript en una interfaz RESTful HTTP sencilla. DocumentDB abarca formatos, lenguajes y estándares actuales y, además, ofrece capacidades de base de datos de alto valor.

Puede utilizar DocumentDB para almacenar conjuntos de datos flexibles que requieren procesamiento transaccional y recuperación de la consulta. Los escenarios de aplicación pueden incluir datos de usuario para las aplicaciones web y móviles interactivas, además del almacenamiento, la recuperación y el procesamiento de datos JSON de la aplicación. Una base de datos puede almacenar cualquier número de documentos JSON, DocumentDB es ideal para aplicaciones que se ejecutan en Internet.

##<a name="data-management"></a>Recursos de Azure DocumentDB
Azure DocumentDB administra datos a través de recursos de bases de datos bien definidos. Estos recursos se replican para ofrecer elevados niveles de disponibilidad y solo se pueden direccionar a través de su URI lógico. DocumentDB ofrece un modelo de programación RESTful basado en HTTP sencillo para todos los recursos.

La cuenta de base de datos de DocumentDB es un espacio de nombres único que le proporciona acceso a Azure DocumentDB. Para poder crear una cuenta de base de datos, debe disponer de una suscripción de Azure, que proporciona acceso a una variedad de servicios de Azure.

Todos los recursos de DocumentDB se modelan y almacenan como documentos JSON. Los recursos se administran como elementos, que son documentos JSON que contienen metadatos, y como fuentes que son recopilaciones de elementos. Las fuentes respectivas contienen los conjuntos de elementos.

La imagen siguiente muestra las relaciones entre los recursos de DocumentDB:

![][1]

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias recopilaciones. Cada recopilación puede contener, a su vez, procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos también tiene usuarios asociados, cada uno con un conjunto de permisos que proporciona acceso a diferentes recopilaciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras que las bases de datos, los usuarios, permisos y las recopilaciones son recursos definidos por el sistema con esquemas conocidos, los documentos, procedimientos almacenados, desencadenadores, UDF y datos adjuntos contienen contenido JSON arbitrario y definido por el usuario.

##<a name="develop"></a>Desarrollo frente a Azure DocumentDB
DocumentDB expone recursos mediante la API de REST, que se puede invocar con cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, la Base de datos de documentos ofrece bibliotecas de programación para varios lenguajes. Estas bibliotecas simplifican muchos aspectos de trabajar con la Base de datos de documentos de Azure al controlar detalles como el almacenamiento en caché de las direcciones, la administración de excepciones, los intentos automáticos, etc. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas, además de otros previstos:

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
- [Node.js](http://go.microsoft.com/fwlink/?LinkID=402990)
- [Java](http://go.microsoft.com/fwlink/?LinkID=402380)
- [JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
- [Python](http://go.microsoft.com/fwlink/?LinkID=402992)

Además de las operaciones básicas Crear, Leer, Actualizar y Eliminar, Azure DocumentDB proporciona una interfaz de consulta SQL enriquecida para recuperar documentos JSON y soporte del servidor para la ejecución transaccional de la lógica de aplicaciones de JavaScript. Las interfaces de ejecución de consultas y script están disponibles a través de todas las bibliotecas de las plataformas además de las API REST.

###Consulta SQL
Azure DocumentDB permite la realización de consultas de documentos mediante lenguaje SQL, que se basa en el sistema de tipos de JavaScript, y con expresiones que admiten las consultas jerárquicas avanzadas. El lenguaje de consulta de DocumentDB es una interfaz sencilla pero poderosa para consultar documentos JSON. El lenguaje admite un subconjunto de gramática ANSI SQL e incorpora una integración profunda del objeto de JavaScript, matrices, construcción de objetos e invocación de funciones. DocumentDB proporciona su modelo de consulta sin ningún esquema explícito o consejo de indexación por parte del desarrollador.

Las funciones definidas por el usuario (UDF) se pueden registrar con DocumentDB a las que se hace referencia como parte de una consulta SQL, con lo que se extiende la gramática a una lógica de aplicación personalizada compatible. Estas UDF se crean como programas de JavaScript y se ejecutan en la base de datos.

Para desarrolladores. NET, la Base de datos de documentos también ofrece un proveedor de consultas LINQ como parte del SDK de. NET.

###Transacciones y ejecución de JavaScript
Azure DocumentDB permite crear lógica de aplicaciones como programas con nombres escritos completamente en JavaScript. Estos programas se registran para una recopilación y pueden emitir operaciones de base de datos en los documentos dentro de una recopilación determinada. JavaScript se puede registrar para la ejecución como desencadenador, procedimiento almacenado o función definida por el usuario. Los desencadenadores y procedimientos almacenados pueden crear, leer, actualizar y eliminar documentos; mientras que las funciones definidas por el usuario se ejecutan como parte de la lógica de ejecución de consulta sin acceso de escritura a la colección.

La ejecución de JavaScript dentro de la Base de datos de documentos se modela después de que los conceptos admitidos por los sistemas de base de datos relacionales, con JavaScript como un reemplazo moderno de T-SQL. Toda la lógica de JavaScript se ejecuta en un entorno de transacciones ACID con aislamiento de la instantánea. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción.

##<a name="next-steps"></a>Pasos siguientes
Para comenzar con Azure DocumentDB, explore los siguientes recursos:

-   [Probar DocumentDB ahora](https://portal.azure.com/#gallery/Microsoft.DocumentDB)
-   [Área de consultas](http://www.documentdb.com/sql/demo)
-	[Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md)
-	[Interacción con recursos de DocumentDB](documentdb-interactions-with-resources.md)
-	[Creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md)
-	[Introducción al SDK de .NET de DocumentDB](documentdb-get-started.md)

[1]: ./media/documentdb-introduction/intro.png
 

<!---HONumber=August15_HO6-->