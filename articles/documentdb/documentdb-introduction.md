---
title: "Introducción a DocumentDB, una base de datos JSON | Microsoft Docs"
description: "Obtenga información sobre Azure DocumentDB, una base de datos JSON NoSQL. Esta base de datos de documentos se ha creado para macrodatos, escalabilidad elástica y alta disponibilidad."
keywords: base de datos json, base de datos de documentos
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: 686cdd2b-704a-4488-921e-8eefb70d5c63
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/14/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 4f6ae0c3c40a10f75b46c6e44b0aa9e8ce440b4d
ms.lasthandoff: 03/15/2017


---
# <a name="introduction-to-documentdb-a-nosql-json-database"></a>Introducción a DocumentDB: una base de datos de JSON NoSQL
## <a name="what-is-documentdb"></a>¿Qué es DocumentDB?
DocumentDB es un servicio totalmente administrado de base de datos NoSQL compilado para obtener un rendimiento rápido y predecible, alta disponibilidad, escalado elástico, distribución global y facilidad de desarrollo. Como base de datos NoSQL sin esquemas, DocumentDB proporciona funcionalidades de consulta SQL avanzadas y conocidas con bajas latencias constantes en datos JSON, lo cual garantiza que el 99 % de las lecturas se proporcionan en menos de 10 milisegundos y el 99 % de las escrituras en menos de 15 milisegundos. Estas ventajas exclusivas hacen que DocumentDB resulte idóneo para web, móvil, juegos, IoT y muchas otras aplicaciones que necesitan un escalado impecable y una replicación global.

## <a name="how-can-i-learn-about-documentdb"></a>¿Cómo puedo obtener información acerca de DocumentDB?
Una forma rápida de obtener información sobre DocumentDB y verlo en acción es seguir estos tres pasos: 

1. Ver el vídeo de dos minutos [What is DocumentDB?](https://azure.microsoft.com/documentation/videos/what-is-azure-documentdb/) (¿Qué es DocumentDB?), que presenta las ventajas de usar DocumentDB.
2. Ver el vídeo de tres minutos [Create DocumentDB on Azure](https://azure.microsoft.com/documentation/videos/create-documentdb-on-azure/) (Creación de DocumentDB en Azure), que destaca cómo empezar a trabajar con DocumentDB mediante el Portal de Azure.
3. Visite [Query Playground](http://www.documentdb.com/sql/demo)(Área de juegos de consultas), donde puede recorrer distintas actividades para obtener información sobre la sofisticada funcionalidad de consulta disponible en DocumentDB. A continuación, diríjase a la pestaña Sandbox (Espacio aislado) y ejecute consultas SQL personalizadas y experimente con DocumentDB.

A continuación, vuelva a este artículo, para conocer más detalles.  

## <a name="what-capabilities-and-key-features-does-documentdb-offer"></a>¿Qué funcionalidades y características clave ofrece DocumentDB?
Base de datos de documentos de Azure ofrece las siguientes capacidades y ventajas clave:

* **Rendimiento y almacenamiento escalable de manera flexible** : escale o reduzca verticalmente su base de datos de JSON DocumentDB para cubrir sus necesidades de aplicaciones. Los datos se almacenan en discos de estado sólido (SSD) para las bajas latencias predecibles. DocumentDB admite contenedores para almacenar datos JSON denominados colecciones que se pueden escalar a tamaños de almacenamiento prácticamente ilimitados y de rendimiento aprovisionado. Puede escalar de manera flexible DocumentDB con un rendimiento predecible impecable a medida que su aplicación crece. 
* **Replicación en varias regiones:** DocumentDB replica los datos de forma transparente en todas las regiones que tenga asociadas con su cuenta de DocumentDB. Esto permite desarrollar aplicaciones que requieran acceso global a los datos al mismo tiempo que proporciona un equilibrio entre la coherencia, la disponibilidad y el rendimiento, todo ello con las garantías pertinentes. DocumentDB proporciona conmutación por error regional transparente con las API de hospedaje múltiple, así como la capacidad de escalar de forma elástica el rendimiento y el almacenamiento en todo el mundo. Más información en [Distribución de datos global con DocumentDB](documentdb-distribute-data-globally.md).
* **Consultas ad hoc con sintaxis SQL familiares:** almacene los diferentes documentos JSON en DocumentDB y consúltelos a través de una sintaxis SQL conocida. Base de datos de documentos utiliza una tecnología de indexación estructurada mediante registros sin bloqueos y sumamente concurrente para indexar automáticamente el contenido de todo documento. Esto permite consultas enriquecidas en tiempo real sin necesidad de especificar consejos de esquemas, índices secundarios o vistas. Obtenga más información en [DocumentDB de consulta](documentdb-sql-query.md). 
* **Ejecución de JavaScript en la base de datos** : expresa la lógica de las aplicaciones como procedimientos almacenados, desencadenadores y funciones definidas por el usuario (UDF) con JavaScript estándar. Esto permite que la lógica de su aplicación opere con datos sin preocuparse de la discordancia entre la aplicación y el esquema de la base de datos. Base de datos de documentos proporciona una ejecución transaccional completa de la lógica de aplicaciones de JavaScript directamente dentro del motor de la base de datos. La integración profunda de JavaScript permite la ejecución de las operaciones INSERTAR, REEMPLAZAR, ELIMINAR y SELECCIONAR desde un programa JavaScript como una transacción aislada. Obtenga más información en [Programación de servidor DocumentDB](documentdb-programming.md).
* **Niveles de coherencia ajustables** : seleccione entre cuatro niveles de coherencia bien definidos para lograr un equilibrio óptimo entre la coherencia y el rendimiento. Para las consultas y las operaciones de lectura, DocumentDB ofrece cuatro niveles de coherencia diferentes: fuerte, obsolescencia entrelazada, sesión y eventual. Estos niveles de coherencia bien definidos y pormenorizados le permiten realizar equilibrios profundos entre la coherencia, la disponibilidad y la latencia. Obtenga más información en [Uso de los niveles de coherencia para maximizar la disponibilidad y el rendimiento en DocumentDB](documentdb-consistency-levels.md).
* **Completamente administrados** : elimine la necesidad de administrar recursos de bases de datos y máquinas. Como un servicio de Microsoft Azure completamente administrado, no es necesario que administre las máquinas virtuales, ni que implemente y configure software, administre escalado o realice complejas actualizaciones a nivel de datos. Se realiza una copia de seguridad de cada base de datos además de protegerlas contra fallas regionales. Puede agregar fácilmente una cuenta de DocumentDB y aprovisionar capacidad a medida que la necesita, lo que le permite concentrarse en su aplicación en lugar de la operación y administración de la base de datos. 
* **Diseño abierto** : comience rápidamente con las destrezas y herramientas existentes. La programación de DocumentDB es sencilla, asequible y no requiere la adopción de herramientas nuevas o de extensiones personalizadas de JSON o JavaScript. Puede tener acceso a todas las funciones de la base de datos incluido el procesamiento de CRUD, las consultas y JavaScript en una interfaz RESTful HTTP sencilla. DocumentDB abarca formatos, lenguajes y estándares actuales y, además, ofrece capacidades de base de datos de alto valor.
* **Indexación automática:** de manera predeterminada, DocumentDB indexa automáticamente todos los documentos de la base de datos y no espera ni requiere ningún esquema, ni tampoco la creación de índices secundarios. ¿No desea tener que indexar todo? No se preocupe, también puede [renunciar a las rutas de acceso en los archivos JSON](documentdb-indexing-policies.md) .
* **Compatibilidad con aplicaciones de MongoDB:** con DocumentDB: API para MongoDB, puede usar bases de datos de DocumentDB como almacén de datos para aplicaciones escritas para MongoDB. Esto significa que mediante controladores existentes para bases de datos de MongoDB, la aplicación escrita para MongoDB se puede comunicar ahora con DocumentDB y usar bases de datos de DocumentDB en lugar de bases de datos de MongoDB. En muchos casos, puede cambiar del uso de MongoDB a DocumentDB con solo cambiar una cadena de conexión. Más información en [¿Qué es DocumentDB: API para MongoDB?](documentdb-protocol-mongodb.md)

## <a name="data-management"></a>¿Cómo administra los datos DocumentDB?
Azure DocumentDB administra los datos JSON a través de recursos de bases de datos bien definidos. Estos recursos se replican para ofrecer elevados niveles de disponibilidad y solo se pueden direccionar a través de su URI lógico. La Base de datos de documentos ofrece un modelo de programación RESTful basado en HTTP sencillo para todos los recursos. 

La cuenta de base de datos de la Base de datos de documentos es un espacio de nombres único que le proporciona acceso a la Base de datos de documentos de Azure. Para poder crear una cuenta de base de datos, debe disponer de una suscripción de Azure que le proporcione acceso a una variedad de servicios de Azure. 

Todos los recursos de DocumentDB se modelan y almacenan como documentos JSON. Los recursos se administran como elementos, que son documentos JSON que contienen metadatos, y como fuentes que son recopilaciones de elementos. Las fuentes respectivas contienen los conjuntos de elementos.

La imagen siguiente muestra las relaciones entre los recursos de DocumentDB:

![La relación jerárquica entre los recursos de DocumentDB, una base de datos JSON NoSQL][1] 

Una cuenta de base de datos consta de un conjunto de bases de datos, cada una con varias recopilaciones. Cada recopilación puede contener, a su vez, procedimientos almacenados, desencadenadores, UDF, documentos y datos adjuntos relacionados. Una base de datos también tiene usuarios asociados, cada uno con un conjunto de permisos que proporciona acceso a diferentes recopilaciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras que las bases de datos, los usuarios, permisos y las recopilaciones son recursos definidos por el sistema con esquemas conocidos, los documentos, procedimientos almacenados, desencadenadores, UDF y datos adjuntos contienen contenido JSON arbitrario y definido por el usuario.  

## <a name="develop"></a> ¿Cómo puedo desarrollar aplicaciones con DocumentDB?
DocumentDB expone recursos mediante la API de REST, que se puede invocar con cualquier lenguaje capaz de realizar solicitudes de HTTP/HTTPS. Además, DocumentDB ofrece bibliotecas de programación para varios lenguajes conocidos y es compatible con las API de MongoDB. Estas bibliotecas de cliente simplifican muchos aspectos del trabajo con Azure DocumentDB al controlar detalles como el almacenamiento en caché de las direcciones, la administración de excepciones, los reintentos automáticos, etc. Actualmente hay bibliotecas disponibles para los siguientes lenguajes y plataformas:  

| Descargar | Documentación |
| --- | --- |
| [.NET SDK](http://go.microsoft.com/fwlink/?LinkID=402989) |[Biblioteca de .NET](https://msdn.microsoft.com/library/azure/dn948556.aspx) |
| [SDK de Node.js](http://go.microsoft.com/fwlink/?LinkID=402990) |[Biblioteca de Node.js](http://azure.github.io/azure-documentdb-node/) |
| [SDK de Java](http://go.microsoft.com/fwlink/?LinkID=402380) |[Biblioteca de Java](http://azure.github.io/azure-documentdb-java/) |
| [SDK de JavaScript](http://go.microsoft.com/fwlink/?LinkID=402991) |[Biblioteca de JavaScript](http://azure.github.io/azure-documentdb-js/) |
| N/D |[SDK del servidor de JavaScript](http://azure.github.io/azure-documentdb-js-server/) |
| [SDK de Python](https://pypi.python.org/pypi/pydocumentdb) |[Biblioteca de Python](http://azure.github.io/azure-documentdb-python/) |
| N/D | [API para MongoDB](documentdb-protocol-mongodb.md)

Mediante el [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md), puede desarrollar y probar su aplicación localmente, sin crear una suscripción de Azure o realizar algún gasto. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador de DocumentDB, puede cambiar al uso de una cuenta de Azure DocumentDB en la nube.

Además de las operaciones básicas Crear, Leer, Actualizar y Eliminar, DocumentDB proporciona una sofisticada interfaz de consulta SQL para recuperar documentos JSON y soporte del servidor para la ejecución transaccional de la lógica de aplicaciones de JavaScript. Las interfaces de ejecución de consultas y script están disponibles a través de todas las bibliotecas de las plataformas además de las API REST. 

### <a name="sql-query"></a>Consulta SQL
Azure DocumentDB es compatible con la realización de consultas de documentos mediante lenguaje SQL, que se basa en el sistema de tipos de JavaScript, y con expresiones que admiten consultas relacionales, jerárquicas y espaciales. El lenguaje de consulta de DocumentDB es una interfaz sencilla pero poderosa para consultar documentos JSON. El lenguaje admite un subconjunto de gramática ANSI SQL e incorpora una integración profunda del objeto de JavaScript, matrices, construcción de objetos e invocación de funciones. La Base de datos de documentos proporciona su modelo de consulta sin ningún esquema explícito o consejo de indexación por parte del desarrollador.

Las funciones definidas por el usuario (UDF) se pueden registrar con DocumentDB a las que se hace referencia como parte de una consulta SQL, con lo que se extiende la gramática a una lógica de aplicación personalizada compatible. Estas UDF se crean como programas de JavaScript y se ejecutan en la base de datos. 

Para desarrolladores. NET, DocumentDB también ofrece un proveedor de consultas LINQ como parte del [SDK de. NET](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.linq.aspx). 

### <a name="transactions-and-javascript-execution"></a>Transacciones y ejecución de JavaScript
Azure DocumentDB permite crear lógica de aplicaciones como programas con nombres escritos completamente en JavaScript. Estos programas se registran para una recopilación y pueden emitir operaciones de base de datos en los documentos dentro de una recopilación determinada. JavaScript se puede registrar para la ejecución como desencadenador, procedimiento almacenado o función definida por el usuario. Los desencadenadores y procedimientos almacenados pueden crear, leer, actualizar y eliminar documentos; mientras que las funciones definidas por el usuario se ejecutan como parte de la lógica de ejecución de consulta sin acceso de escritura a la colección.

La ejecución de JavaScript en DocumentDB se modela de acuerdo con los conceptos admitidos por los sistemas de base de datos relacionales, con JavaScript como un reemplazo moderno de Transact-SQL. Toda la lógica de JavaScript se ejecuta en un entorno de transacciones ACID con aislamiento de la instantánea. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción.

## <a name="are-there-any-online-courses-on-documentdb"></a>¿Hay cursos en línea de DocumentDB?

Sí, en la [Microsoft Virtual Academy](https://mva.microsoft.com/en-US/training-courses/azure-documentdb-planetscale-nosql-16847) hay un curso de Azure DocumentDB. 

>[!VIDEO https://mva.microsoft.com/en-US/training-courses-embed/azure-documentdb-planetscale-nosql-16847]
>
>

## <a name="next-steps"></a>Pasos siguientes
¿Ya tiene una cuenta de Azure? Entonces, puede empezar a trabajar con DocumentDB en [Azure Portal](https://portal.azure.com/#gallery/Microsoft.DocumentDB) mediante la [creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md).

¿No tiene una cuenta de Azure? Puede:

* Si es la primera vez que usa Azure, regístrese para una [prueba gratuita de Azure](https://azure.microsoft.com/free/), que le proporciona 30 días y 200 USD para que pruebe todos los servicios de Azure. 
* Si tiene una suscripción a MSDN, puede recibir [USD 150 en créditos gratis de Azure al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure. 
* Descargue el [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) para desarrollar su aplicación localmente.

A continuación, cuando esté listo para obtener más información, visite nuestra [ruta de aprendizaje](https://azure.microsoft.com/documentation/learning-paths/documentdb/) para navegar por todos los recursos de aprendizaje disponibles. 

[1]: ./media/documentdb-introduction/json-database-resources1.png


