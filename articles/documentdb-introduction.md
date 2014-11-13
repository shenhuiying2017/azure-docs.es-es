<properties title="Introducci&oacute;n a Base de datos de documentos de Microsoft Azure" pageTitle="Introducci&oacute;n a Base de datos de documentos de Microsoft Azure | Azure" description="Conozca Base de datos de documentos de Azure y su valor para aplicaciones en la nube y m&oacute;viles. Vea tambi&eacute;n c&oacute;mo administra datos y c&oacute;mo puede usarla en el desarrollo de aplicaciones." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

# Introducción a Base de datos de documentos de Microsoft Azure

Este artículo ofrece una introducción a Base de datos de documentos de Microsoft Azure para desarrolladores, profesionales de TI y responsables de la toma de decisiones empresariales. Al leerlo, aprenderá:

-   \<Qué es la Base de datos de documentos de Azure y el valor que proporciona a la nube y las aplicaciones móviles
-   Cómo se administran sus datos en la Base de datos de documentos de Azure
-   Cómo tener acceso a datos y desarrollar aplicaciones usando la Base de datos de documentos de Azure
-   Qué pasos seguir a continuación para crear una primera aplicación de Base de datos de documentos de Azure

## ¿Qué es la Base de datos de documentos de Azure?

Las aplicaciones modernas producen, consumen y responden rápidamente a volúmenes de datos muy grandes. Estas aplicaciones evolucionan con mucha rapidez y lo mismo ocurre con el esquema de datos subyacente. En respuesta a esto, los desarrolladores han elegido cada vez más las bases de datos NoSQL sin esquemas por ser soluciones sencillas, rápidas y flexibles para almacenar y procesar datos, a la vez que conservan la capacidad de iterar rápidamente sobre los modelos de datos de las aplicaciones y las fuentes de datos no estructuradas. Sin embargo, muchas bases de datos libres de esquemas no permiten consultas que no sean triviales y el procesamiento transaccional, lo que dificulta la administración de datos avanzados. Microsoft desarrolló Base de datos de documentos de Azure para ofrecer estas capacidades al administrar datos sin esquemas.

Base de datos de documentos de Microsoft Azure es un servicio de base de datos NoSQL orientado a los documentos y diseñado para las aplicaciones móviles y web modernas. Base de datos de documentos ofrece de manera consistente lecturas y escrituras rápidas, flexibilidad de esquemas y la capacidad de escalar fácilmente una base de datos arriba y abajo a pedido. Base de datos de documentos permite realizar consultas específicas complejas usando el dialecto de SQL, admite niveles de coherencia bien definidos y ofrece lenguaje JavaScript integrado, procesamiento de transacciones de varios documentos usando el modelo de programación conocido de procedimientos almacenados, desencadenadores y UDF.

Base de datos de documentos de Azure admite de manera nativa documentos JSON, lo que permite una iteración fácil de los esquemas de aplicaciones. Abarca la ubicuidad de JSON y JavaScript, lo que elimina la discordancia entre el sistema de tipos de aplicación y los esquemas de las bases de datos. La integración profunda de JavaScript también permite que los desarrolladores ejecuten una lógica de aplicaciones de manera eficaz y directa en el motor de la base de datos dentro de una transacción de base de datos.

Base de datos de documentos de Azure ofrece las siguientes capacidades y ventajas clave:

-   **Consultas específicas con sintaxis SQL conocida:** almacene los diferentes documentos JSON en la Base de datos de documentos y consúltelos a través de una sintaxis SQL conocida. Base de datos de documentos utiliza una tecnología de indexación estructurada mediante registros sin bloqueos y sumamente concurrente para indexar automáticamente el contenido de todo documento. Esto permite consultas enriquecidas en tiempo real sin necesidad de especificar consejos de esquemas, índices secundarios o vistas.

-   **Ejecución de JavaScript en la base de datos:** lógica de aplicaciones expresa como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) con JavaScript estándar. Esto permite que la lógica de su aplicación opere con datos JSON sin preocuparse de la discordancia de impedancia entre la aplicación y el esquema de la base de datos. Base de datos de documentos proporciona una ejecución transaccional completa de la lógica de aplicaciones de JavaScript directamente dentro del motor de la base de datos. La integración profunda de JavaScript permite la ejecución de las operaciones INSERTAR, REEMPLAZAR, ELIMINAR y SELECCIONAR desde un programa JavaScript como una transacción aislada.

-   **Niveles de coherencia ajustables:** seleccione entre cuatro niveles de coherencia bien definidos para lograr un equilibrio óptimo entre la coherencia y el rendimiento. Para las consultas y las operaciones de lectura, Base de datos de documentos ofrece cuatro niveles de coherencia diferentes: Fuerte, Obsolescencia entrelazada, Sesión y Eventual. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia.

-   **Completamente administrados:** elimine la necesidad de administrar recursos de bases de datos y máquinas. Como un servicio de Microsoft Azure completamente administrado, no es necesario que administre las máquinas virtuales, administrar y configurar software o realizar complejas actualizaciones a nivel de datos. Se realiza una copia de seguridad de cada base de datos además de protegerlas contra fallas regionales. Puede agregar fácilmente una cuenta de Base de datos de documentos y aprovisionar capacidad a medida que la necesita, lo que le permite concentrarse en su aplicación en cuanto a la operación y administración de la base de datos.

-   **Rendimiento y almacenamiento escalable de manera flexible:** escale o reduzca verticalmente la Base de datos de documentos fácilmente para satisfacer las necesidades de su aplicación. El escalado se realiza a través de unidades detalladas del almacenamiento y rendimiento reservados en copia de seguridad en SSD. Puede escalar de manera flexible la Base de datos de documentos con un rendimiento predecible al comprar más unidades de capacidad, a medida que su aplicación crece.

-   **Abierto por diseño:** comience rápidamente usando las destrezas y herramientas existentes. La programación de una Base de datos de documentos es sencilla, abordable y no requiere que adopte herramientas nuevas o respete extensiones personalizadas de JSON o JavaScript. Puede tener acceso a todas las funciones de la base de datos incluido procesamiento de CRUD, consultas y JavaScript en una interfaz RESTful HTTP simple. La Base de datos de documentos abarca los formatos, lenguajes y estándares actuales mientras, además, ofrece capacidades de base de datos de alto valor.

Puede usar Base de datos de documentos de Azure para almacenar conjuntos de datos flexibles que requieren la recuperación de consultas y un procesamiento transaccional. Los escenarios de aplicación pueden incluir datos de usuario para las aplicaciones web y móviles interactivas, además del almacenamiento, la recuperación y el procesamiento de datos JSON de la aplicación. Una base de datos puede almacenar cualquier número de documentos JSON, puesto que dicha Base de datos de documentos es adecuada para aplicaciones que se ejecutan en Internet.

## Recursos de la Base de datos de documentos de Azure

La Base de datos de documentos de Azure administrar datos a través de recursos de bases de datos bien definidos. Estos recursos se replican para una mayor disponibilidad y pueden abordarse de manera única a través de la URI lógica. La Base de datos de documentos ofrece un modelo de programación RESTful basado en HTTP sencillo para todos los recursos.

La cuenta de base de datos de la Base de datos de documentos es un espacio de nombres único que le proporciona acceso a la Base de datos de documentos de Azure. Para poder crear una cuenta de base de datos, debe tener una suscripción de Azure, que es un plan que le proporciona acceso a diversos servicios de Azure.

Todos los recursos en la Base de datos de documentos de Azure están modelados y almacenados como documentos JSON. Los recursos se administran como elementos, que son documentos JSON que contienen metadatos y como fuentes que son recopilaciones de elementos. Las fuentes respectivas contienen los conjuntos de elementos.

La siguiente imagen muestra las relaciones entre los recursos de la Base de datos de Azure:

![][0]

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una contiene varias recopilaciones y cada una de estas puede contener procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos tiene también usuarios asociados, cada uno con un conjunto de permisos para tener acceso a diferentes recopilaciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Aun cuando las bases de datos, los usuarios, los permisos y las recopilaciones son recursos definidos por el sistema con esquemas bien conocidos, los documentos, los procedimientos almacenados, los desencadenadores, las UDF y los datos adjuntos contienen contenido JSON definido por el usuario y arbitrario.

## Desarrollo frente a la Base de datos de documentos de Azure

Base de datos de documentos de Azure expone recursos mediante la API de REST, que puede invocar cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, la Base de datos de documentos de Azure ofrece bibliotecas de programación para varios lenguajes populares. Estas bibliotecas simplifican muchos aspectos de trabajar con la Base de datos de documentos de Azure al controlar detalles como el almacenamiento en caché de las direcciones, la administración de excepciones, los intentos automáticos, etc. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas, además de otros previstos:

-   [.NET][.NET]
-   [Node.js][Node.js]
-   [JavaScript][JavaScript]
-   [Python][Python]

Además de las operaciones básicas Crear, Leer, Actualizar y Eliminar, la Base de datos de documentos de Azure proporciona una interfaz de consulta SQL enriquecida para recuperar documentos JSON y soporte del servidor para la ejecución transaccional de la lógica de aplicaciones de JavaScript. Las interfaces de ejecución de consultas y script están disponibles a través de todas las bibliotecas de las plataformas además de las API REST.

### Consulta SQL

La Base de datos de documentos de Azure admite la consulta de documentos usando un lenguaje SQL que está enraizado en un sistema tipo JavaScript y expresiones compatibles con consultas jerárquicas enriquecidas. El lenguaje de consulta de la Base de datos de documentos es una interfaz sencilla pero poderosa para consultar documentos JSON. El lenguaje admite un subconjunto de gramática ANSI SQL y agrega una integración profunda del objeto de JavaScript, matrices, construcción de objetos e invocación de funciones. La Base de datos de documentos proporciona su modelo de consulta sin ningún esquema explícito o consejo de indexación por parte del desarrollador.

Las funciones definidas por el usuario (UDF) se pueden registrar con la Base de datos de documentos de Azure a las que se hace referencia como parte de una consulta SQL, con lo que se extiende la gramática a una lógica de aplicación personalizada compatible. Estas UDF se crean como programas de JavaScript y se ejecutan en la base de datos.

Para los desarrolladores de .NET, la Base de datos de documentos de Azure ofrece también un proveedor de consultas LINQ como parte del SDK de .NET.

### Transacciones y ejecución de JavaScript

La Base de datos de documentos de Azure le permite crear lógica de aplicaciones como programas con nombres escritos completamente en JavaScript. Estos programas se registran para una recopilación y admiten la emisión de operaciones de bases de datos en los documentos dentro de una recopilación dada. JavaScript de aplicaciones se puede registrar para la ejecución como Desencadenador, Procedimiento almacenado o Función definida por el usuario (UDF). Los desencadenadores y los procedimientos almacenados pueden crear, leer, actualizar y eliminar documentos mientras que las UDF se ejecutan como parte de una ejecución de consultas sin acceso de escritura para la recopilación.

La ejecución de JavaScript dentro de la Base de datos de documentos se modela después de que los conceptos admitidos por los sistemas de base de datos relacionales, con JavaScript como un reemplazo moderno de T-SQL. Toda la lógica de JavaScript se ejecuta en un entorno de transacciones ACID con aislamiento de la instantánea. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción.

## Pasos siguientes

Para comenzar con la Base de datos de documentos de Azure, explore los siguientes recursos:

-   [Probar Base de datos de documentos ahora][Probar Base de datos de documentos ahora]
-   [Comprensión de los conceptos de la Base de datos de documentos][Comprensión de los conceptos de la Base de datos de documentos]
-   [Interacción con la Base de datos de documentos][Interacción con la Base de datos de documentos]
-   [Creación de una cuenta de base de datos][Creación de una cuenta de base de datos]
-   [Introducción a la Base de datos de documentos][Introducción a la Base de datos de documentos]

  [0]: ./media/documentdb-introduction/intro.png
  [.NET]: http://go.microsoft.com/fwlink/?LinkID=402989
  [Node.js]: http://go.microsoft.com/fwlink/?LinkID=402990
  [JavaScript]: http://go.microsoft.com/fwlink/?LinkID=402991
  [Python]: http://go.microsoft.com/fwlink/?LinkID=402992
  [Probar Base de datos de documentos ahora]: https://portal.azure.com/#gallery/Microsoft.DocumentDB
  [Comprensión de los conceptos de la Base de datos de documentos]: /documentation/articles/documentdb-resources/
  [Interacción con la Base de datos de documentos]: /documentation/articles/documentdb-interactions-with-resources/
  [Creación de una cuenta de base de datos]: /documentation/articles/documentdb-create-account/
  [Introducción a la Base de datos de documentos]: /documentation/articles/documentdb-get-started/
