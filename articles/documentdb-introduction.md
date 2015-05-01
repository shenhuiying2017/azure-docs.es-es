<properties 
	pageTitle="Introducción a DocumentDB de Microsoft Azure | Azure" 
	description="Obtenga información acerca de Microsoft Azure DocumentDB, una DocumentDB NoSQL y su valor para aplicaciones en la nube y móviles. Aprenda también cómo administra datos y cómo puede usarla en el desarrollo de aplicaciones." 
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
	ms.date="03/20/2015" 
	ms.author="mimig"/>

#Introducción a DocumentDB de Microsoft Azure

Este artículo proporciona una introducción a la DocumentDB de Microsoft Azure, un servicio de DocumentDB NoSQL para desarrolladores, profesionales de TI y responsables de la toma de decisiones empresariales. 

Se recomienda comenzar por ver el vídeo siguiente, donde Ryan CrawCour y Scott Hanselman presentan Microsoft Azure DocumentDB y probar nuestra [Área de consultas](http://www.documentdb.com/sql/demo), donde puede probar DocumentDB y ejecutar consultas SQL en nuestro conjunto de datos.

> [AZURE.VIDEO documentdb-101-with-ryan-crawcour]

A continuación, vuelva a este artículo, donde conocerá las respuestas a las preguntas siguientes:  

-	[¿Qué es la DocumentDB y qué valor ofrece a la nube y a las aplicaciones móviles?](#what-is-docdb)
-	[¿Cómo se administran los datos en la DocumentDB y cómo se tiene acceso al servicio?](#data-management)
-	[¿Cómo puedo desarrollar aplicaciones con la DocumentDB?](#develop)
-	[¿Cuáles son los pasos que debo seguir para crear una aplicación de DocumentDB?](#next-steps)  

##<a name="what-is-docdb"></a>¿Qué es la Microsoft Azure DocumentDB?  

Las aplicaciones modernas producen, consumen y responden rápidamente a volúmenes de datos muy grandes. Estas aplicaciones evolucionan con mucha rapidez y lo mismo ocurre con el esquema de datos subyacente. En respuesta a esto, cada vez más desarrolladores optan por las bases de datos NoSQL libres de esquemas por ser soluciones sencillas, rápidas y flexibles para almacenar y procesar datos, a la vez que permiten conservar la capacidad de iterar rápidamente sobre los modelos de datos de las aplicaciones y las fuentes de datos no estructuradas. Sin embargo, muchas bases de datos libres de esquemas no permiten realizar consultas que no sean triviales ni procesar transacciones, lo que dificulta la administración de datos avanzados. Aquí es donde entra en juego la DocumentDB. Microsoft desarrolló este servicio para satisfacer estos requisitos al permitir administrar los datos sin esquemas.

La DocumentDB es un servicio de DocumentDB NoSQL diseñado para aplicaciones web y móviles modernos.  La DocumentDB ofrece de manera consistente capacidades de lectura y escritura rápidas, flexibilidad de esquemas, así como la capacidad de escalar fácilmente una base de datos en función de las necesidades. La DocumentDB permite realizar consultas específicas complejas mediante SQL. Asimismo, admite niveles de coherencia bien definidos y permite el uso de lenguaje JavaScript, además de procesar transacciones de varios documentos usando el modelo de programación conocido de procedimientos almacenados, desencadenadores y UDF. 

La DocumentDB admite de manera nativa documentos JSON, lo que facilita la iteración de los esquemas de aplicaciones. Abarca la ubicuidad de JSON y JavaScript, lo que elimina la discordancia entre los tipos de aplicación y los esquemas de las bases de datos. La integración de JavaScript también permite a los desarrolladores ejecutar lógica de aplicación de manera eficaz y directa desde el motor de base de datos en una transacción de base de datos. 

Microsoft Azure DocumentDB ofrece las siguientes capacidades y ventajas clave:

-	**Consultas ad hoc con la conocida sintaxis SQL:** almacene documentos JSON heterogéneos en la DocumentDB y realice consultas a través de una sintaxis familiar de SQL. la DocumentDB utiliza una tecnología de indexación estructurada de registros concurrente y libre de bloqueos que permite indexar todo el contenido de los documentos. Esto permite realizar consultas enriquecidas en tiempo real sin necesidad de especificar sugerencias de esquema, índices secundarios o vistas.

-	**Ejecución de JavaScript dentro de la base de datos:** permite expresar la lógica de la aplicación como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) mediante JavaScript estándar. Esto permite que la lógica de aplicación opere con datos JSON sin preocuparse por la discordancia entre la aplicación y el esquema de la base de datos. DocumentDB facilita la ejecución de lógica de la aplicación de JavaScript directamente dentro del motor de la base de datos. La integración profunda de JavaScript permite la ejecución de operaciones INSERT, REPLACE, DELETE y SELECT desde dentro de un programa JavaScript como una transacción aislada. 

-	**Niveles de coherencia ajustables:** seleccione uno entre los cuatro niveles de coherencia definidos con el fin de lograr un equilibrio óptimo entre coherencia y rendimiento. Para las operaciones de consulta y lectura, DocumentDB ofrece cuatro niveles de coherencia distintos: seguro, obsolescencia limitada, sesión y eventual. Estos niveles de coherencia granulares y bien definidos permiten encontrar el equilibrio entre coherencia, disponibilidad y latencia. 

-	**Totalmente administrados:** elimina la necesidad de administrar los recursos del equipo y la base de datos. Como servicio de Microsoft Azure completamente administrado, no es necesario administrar máquinas virtuales, implementar o configurar el software ni tratar con las actualizaciones de la capa de datos complejos. Se realiza una copia de seguridad de cada base de datos y se protege contra errores regionales de forma automática. Puede agregar una cuenta de DocumentDB fácilmente y aprovisionar las capacidades conforme sean necesarias, lo que le permite centrarse en su aplicación en lugar de en la utilización y administración de la base de datos.

-	**Almacenamiento y rendimiento escalables y elásticos:** permite ampliar o reducir fácilmente DocumentDB según las necesidades de la aplicación. El ajuste de escalado se realiza a través de unidades específicas de rendimiento y almacenamiento SSD. DocumentDB se puede escalar de forma elástica con un rendimiento predecible adquiriendo más unidades de capacidad a medida que crezca la aplicación. 

-	**Abrir por diseño:** comience rápidamente con las habilidades y herramientas existentes. La programación con DocumentDB es simple, accesible y no requiere adoptar nuevas herramientas ni adherirse a las extensiones personalizadas en JSON o JavaScript. Puede tener acceso a toda la funcionalidad de base de datos, incluidos CRUD, consulta y procesamiento de JavaScript a través de una interfaz HTTP de RESTful simple. DocumentDB adopta estándares, idiomas y formatos existentes al mismo tiempo que ofrece capacidades de bases de datos de alto valor para ellos.

Puede utilizar la DocumentDB para almacenar conjuntos de datos flexibles que requieren procesamiento transaccional y recuperación de la consulta. Los escenarios de aplicación pueden incluir datos de usuario para las aplicaciones web y móviles interactivas, además del almacenamiento, la recuperación y el procesamiento de datos JSON de la aplicación. Una base de datos puede almacenar cualquier número de documentos JSON, la DocumentDB es ideal para aplicaciones que se ejecutan en Internet.

##<a name="data-management"></a>Recursos de Microsoft Azure DocumentDB
La Microsoft Azure DocumentDB administrar datos a través de recursos de bases de datos bien definidos. Estos recursos se replican para ofrecer elevados niveles de disponibilidad y solo se pueden direccionar a través de su URI lógico. La DocumentDB ofrece un modelo de programación RESTful basado en HTTP sencillo para todos los recursos. 

La cuenta de base de datos de la DocumentDB es un espacio de nombres único que le proporciona acceso a la Microsoft Azure DocumentDB. Para poder crear una cuenta de base de datos, debe disponer de una suscripción de Azure, que proporciona acceso a una variedad de servicios de Azure. 

Todos los recursos de la DocumentDB se modelan y almacenan como documentos JSON. Los recursos se administran como elementos, que son documentos JSON que contienen metadatos, y como fuentes que son recopilaciones de elementos. Las fuentes respectivas contienen los conjuntos de elementos.

La imagen siguiente muestra las relaciones entre los recursos de la DocumentDB:

![][1] 

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias recopilaciones. Cada recopilación puede contener, a su vez, procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos también tiene usuarios asociados, cada uno con un conjunto de permisos que proporciona acceso a diferentes recopilaciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras que las bases de datos, los usuarios, permisos y las recopilaciones son recursos definidos por el sistema con esquemas conocidos, los documentos, procedimientos almacenados, desencadenadores, UDF y datos adjuntos contienen contenido JSON arbitrario y definido por el usuario.  

##<a name="develop"></a>Desarrollo frente a Microsoft Azure DocumentDB
La DocumentDB expone recursos mediante la API de REST, que se puede invocar con cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, la DocumentDB ofrece bibliotecas de programación para varios lenguajes. Estas bibliotecas simplifican muchos aspectos de trabajar con la Microsoft Azure DocumentDB al controlar detalles como el almacenamiento en caché de las direcciones, la administración de excepciones, los intentos automáticos, etc. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas, además de otros previstos:  

- [.NET](http://go.microsoft.com/fwlink/?LinkID=402989)  
-	[Node.js](http://go.microsoft.com/fwlink/?LinkID=402990)
-	[JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991)
-	[Python](http://go.microsoft.com/fwlink/?LinkID=402992)

Además de las operaciones básicas Crear, Leer, Actualizar y Eliminar, la Microsoft Azure DocumentDB proporciona una interfaz de consulta SQL enriquecida para recuperar documentos JSON y soporte del servidor para la ejecución transaccional de la lógica de aplicaciones de JavaScript. Las interfaces de ejecución de consultas y script están disponibles a través de todas las bibliotecas de las plataformas además de las API REST. 

###Consulta SQL
La Microsoft Azure DocumentDB permite la realización de consultas de documentos mediante lenguaje SQL, que se basa en el sistema de tipos de JavaScript, y con expresiones que admiten las consultas jerárquicas avanzadas. El lenguaje de consulta de la DocumentDB es una interfaz sencilla pero poderosa para consultar documentos JSON. El lenguaje admite un subconjunto de gramática ANSI SQL e incorpora una integración profunda del objeto de JavaScript, matrices, construcción de objetos e invocación de funciones. La DocumentDB proporciona su modelo de consulta sin ningún esquema explícito o consejo de indexación por parte del desarrollador.

Las funciones definidas por el usuario (UDF) se pueden registrar con la DocumentDB a las que se hace referencia como parte de una consulta SQL, con lo que se extiende la gramática a una lógica de aplicación personalizada compatible. Estas UDF se crean como programas de JavaScript y se ejecutan en la base de datos. 

Para desarrolladores. NET, la DocumentDB también ofrece un proveedor de consultas LINQ como parte del SDK de. NET. 

###Transacciones y ejecución de JavaScript
La Microsoft Azure DocumentDB permite crear lógica de aplicaciones como programas con nombres escritos completamente en JavaScript. Estos programas se registran para una recopilación y pueden emitir operaciones de base de datos en los documentos dentro de una recopilación determinada. JavaScript se puede registrar para la ejecución como desencadenador, procedimiento almacenado o función definida por el usuario. Los desencadenadores y procedimientos almacenados pueden crear, leer, actualizar y eliminar documentos; mientras que las funciones definidas por el usuario se ejecutan como parte de la lógica de ejecución de consulta sin acceso de escritura a la colección.

La ejecución de JavaScript dentro de la DocumentDB se modela después de que los conceptos admitidos por los sistemas de base de datos relacionales, con JavaScript como un reemplazo moderno de T-SQL. Toda la lógica de JavaScript se ejecuta en un entorno de transacciones ACID con aislamiento de la instantánea. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción.

##<a name="next-steps"></a>Pasos siguientes
Para comenzar con la Microsoft Azure DocumentDB, explore los siguientes recursos:

-   [Probar DocumentDB ahora](https://portal.azure.com/#gallery/Microsoft.DocumentDB)
-   [Área de consultas](http://www.documentdb.com/sql/demo)
-	[Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md)
-	[Interacción con recursos de DocumentDB](documentdb-interactions-with-resources.md)
-	[Creación de una cuenta de base de datos de la DocumentDB](documentdb-create-account.md)
-	[Introducción al SDK de .NET de DocumentDB](documentdb-get-started.md)

[1]: ./media/documentdb-introduction/intro.png

<!--HONumber=49-->