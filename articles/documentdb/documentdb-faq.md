<properties 
	pageTitle="Preguntas de base de datos DocumentDB - Preguntas más frecuentes| Microsoft Azure" 
	description="Obtenga respuestas a las preguntas frecuentes sobre Azure DocumentDB, un servicio de base de datos de documentos NoSQL para JSON. Responda a preguntas de base de datos acerca de la capacidad, los niveles de rendimiento y escalado." 
	keywords="Database questions, frequently asked questions, documentdb, azure, Microsoft azure"
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
	ms.date="03/30/2016" 
	ms.author="mimig"/>


#Preguntas más frecuentes sobre DocumentDB

## Preguntas de bases de datos sobre los Fundamentos de Microsoft Azure DocumentDB

### ¿Qué es Microsoft Azure DocumentDB? 
Microsoft Azure DocumentDB es un servicio de base de datos de documentos NoSQL muy escalable que ofrece consultas enriquecidas a través de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y confiable y favorece el desarrollo rápido, y todo gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. DocumentDB es la solución adecuada para aplicaciones web, móviles, de juego e IoT cuando un rendimiento predecible, una baja latencia y un modelo de datos sin esquemas son requisitos clave. Ofrece flexibilidad de esquemas e indexación enriquecida a través de un modelo de datos JSON nativo, e incluye compatibilidad transaccional de varios documentos con JavaScript integrado.
  
Para obtener más preguntas, respuestas e instrucciones de bases de datos sobre la implementación y el uso de este servicio, consulte la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### ¿Qué clase de base de datos es DocumentDB?
DocumentDB es una base de datos orientada a documentos NoSQL que almacena datos en formato JSON. Es compatible con estructuras de datos independientes anidados que se pueden consultar mediante una [gramática de consultas SQL](documentdb-sql-query.md) enriquecida para DocumentDB. DocumentDB ofrece un procesamiento de transacciones de alto rendimiento del servidor JavaScript a través de [ procedimientos almacenados, desencadenadores y funciones definidas por el usuario](documentdb-programming.md). La base de datos admite también niveles de coherencia ajustables por el desarrollador con [niveles de rendimiento](documentdb-performance-levels.md) asociados.
 
### ¿Las bases de datos de DocumentDB tiene tablas como una base de datos relacional (RDBMS)?
No, DocumentDB almacena los datos en colecciones de documentos JSON. Para obtener información sobre los recursos de DocumentDB, consulte [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md).

### ¿Admiten las bases de datos de DocumentDB datos sin esquemas?
Sí, DocumentDB permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de DocumentDB.

### ¿Admite DocumentDB transacciones ACID?
Sí, DocumentDB admite transacciones entre documentos expresadas como procedimientos almacenados y desencadenadores JavaScript. Las transacciones se limitan a una única partición dentro de cada colección y se ejecutan con semánticas ACID, como todo o nada, aisladas de otras que ejecutan a la vez solicitudes de usuario y código. Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte.

### ¿Cuáles son los casos de uso típicos de DocumentDB?  
DocumentDB es una buena elección para las nuevas aplicaciones web, móviles, de juego e IoT donde es importante la escala automática, el rendimiento predecible, la orden rápida de tiempos de respuesta en milisegundos y la capacidad para consultar datos sin esquemas. Se presta a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones. [Casos de uso comunes de DocumentDB](documentdb-use-cases.md) son las aplicaciones que administran contenido y datos generados por el usuario.

### ¿Cómo ofrece DocumentDB un rendimiento predecible?
Una unidad de solicitud (RU) es la medida de rendimiento en DocumentDB. 1 RU corresponde al rendimiento de una solicitud GET de un documento de 1 KB. Cada operación en DocumentDB, incluidas las lecturas, escrituras, consultas SQL y ejecuciones de procedimiento almacenado, tiene un valor de unidad de solicitud determinista basado en el rendimiento necesario para completar la operación. En vez de pensar en la CPU, E/S y en la memoria y en qué impacto provoca cada una en el rendimiento de la aplicación, puede pensar en términos de una medida única de unidad de solicitud.

Cada colección de DocumentDB puede reservarse con rendimiento aprovisionado en términos de unidades de solicitud del rendimiento por segundo. Para las aplicaciones de cualquier escala, puede realizar pruebas comparativas de las solicitudes individuales para medir los valores de unidad de solicitud y aprovisionar colecciones para controlar la suma total de las unidades de solicitud en todas las solicitudes. También puede escalar o reducir verticalmente el rendimiento de su colección a medida que evolucionen las necesidades de la aplicación. Para obtener más información acerca de las unidades de solicitud y ayuda para determinar las necesidades de su colección, lea [Administrar el rendimiento y la funcionalidad](documentdb-manage.md).

### ¿Es DocumentDB compatible con HIPAA?
Sí, DocumentDB es compatible con HIPAA. HIPAA establece los requisitos para el uso, la divulgación y la protección de información de mantenimiento identificable individualmente. Para obtener más información, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/es-ES/TrustCenter/Compliance/HIPAA).

### ¿Cuáles son los límites de almacenamiento de DocumentDB? 
No existe ningún límite teórico en la cantidad total de datos que puede almacenar una colección en DocumentDB. Si desea almacenar más de 250 GB de datos en una sola colección, [póngase en contacto con el soporte técnico](documentdb-increase-limits.md) para aumentar la cuota de su cuenta.

### ¿Cuáles son los límites de rendimiento de DocumentDB? 
No hay ningún límite teórico en la cantidad total de rendimiento que puede admitir una colección en DocumentDB, si la carga de trabajo se puede distribuir aproximadamente de manera uniforme entre un número suficientemente elevado de claves de partición. Si desea superar las 250 000 unidades de solicitud/segundo por colección o cuenta, [póngase en contacto con el soporte técnico](documentdb-increase-limits.md) para que aumenten la cuota de su cuenta.

### ¿Cuánto cuesta Microsoft Azure DocumentDB?
Consulte la página [Detalles de precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/) para detalles. Los cargos por uso de DocumentDB están determinados por la cantidad de colecciones en uso, el número de horas en que las colecciones estuvieron en línea, el almacenamiento consumido y el rendimiento aprovisionado de cada colección.

### ¿Existe una cuenta gratuita disponible?
Si es la primera vez que usa Azure, regístrese para obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/pricing/free-trial/), que le ofrece 30 días y 200 USD para que pruebe todos los servicios de Azure. Si tiene una suscripción a Visual Studio, puede recibir [150 USD en créditos gratis de Azure al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure.

### ¿Cómo puedo obtener ayuda adicional con DocumentDB?
En caso de que necesite ayuda, póngase rápidamente en contacto con nosotros en [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb), los [foros para desarrolladores de MSDN de Azure DocumentDB](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureDocumentDB) o programe un [chat 1:1 con el equipo de ingeniería de DocumentDB](http://www.askdocdb.com/). Para mantenerse al día en las novedades y características más recientes de DocumentDB, síganos en [Twitter](https://twitter.com/DocumentDB).

## Configuración de Microsoft Azure DocumentDB

### ¿Cómo me registro en Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB está disponible en el nuevo [Portal de Azure][azure-portal]. Primero debe registrarse para una suscripción a Microsoft Azure. Una vez hecho esto, puede agregar una cuenta de DocumentDB a su suscripción a Azure. Para instrucciones sobre cómo agregar una cuenta de DocumentDB, vea [Creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md).

### ¿Qué es una clave maestra?
Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los individuos con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras. La clave maestra principal y la secundaria están disponibles en la hoja **Claves ** del [Portal de Azure][azure-portal]. Para más información sobre las claves, vea [Visualización, copia y regeneración de las claves de acceso](documentdb-manage-account.md#keys).

### ¿Cómo se crea una base de datos?
Puede crear bases de datos mediante el [Portal de Azure]() como se describe en [Creación de una base de datos de DocumentDB](documentdb-create-database.md), uno de los [SDK de DocumentDB](documentdb-sdk-dotnet.md) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

### ¿Qué es una colección?
Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Una colección es una entidad facturable, donde el [costo](documentdb-performance-levels.md) está determinado por el nivel de rendimiento asociado a la colección. Las colecciones pueden abarcar una o varias particiones o servidores y se pueden escalar para administrar volúmenes prácticamente ilimitados de almacenamiento o rendimiento.

Las colecciones también son las entidades de facturación de DocumentDB. Cada colección se factura por hora basándose en el rendimiento aprovisionado y en el espacio de almacenamiento usado. Para obtener más información, consulte [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).

### ¿Cómo se configuran los usuarios y los permisos?
Puede crear usuarios y permisos mediante uno de los [SDK de DocumentDB](documentdb-sdk-dotnet.md) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).

## Preguntas de bases de datos sobre el desarrollo con Microsoft Azure DocumentDB

### ¿Cómo se empieza a desarrollar con DocumentDB?
Los [SDK](documentdb-sdk-dotnet.md) están disponibles para NET, Python, Node.js, JavaScript y Java. Los desarrolladores pueden aprovechar también las [API de HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con recursos de DocumentDB desde una variedad de plataformas y lenguajes.

Ejemplos de los SDK de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) y [Python](documentdb-python-samples.md) se encuentran disponibles en GitHub.

### ¿Admite DocumentDB SQL?
El lenguaje de consulta de DocumentDB SQL es un subconjunto mejorado de la funcionalidad de consulta admitida por SQL. El lenguaje de consulta SQL de DocumentDB proporciona operadores relacionales y jerárquicos y una extensibilidad a través de JavaScript según las funciones definidas por el usuario (UDF). La gramática JSON permite el modelado de documentos JSON como árboles con etiquetas a modo de nodos de árbol, algo que usan las técnicas de indexación automática de DocumentDB y el dialecto de consulta SQL de DocumentDB. Para obtener información detallada sobre el uso de la gramática SQL, consulte el artículo [DocumentDB de consulta][query].

### ¿Qué tipos de datos admite DocumentDB?
Los tipos de datos primitivos admitidos en DocumentDB son los mismos que en JSON. JSON posee un sencillo sistema de tipos que consta de cadenas, números (doble precisión IEEE754) y valores booleanos (verdadero, falso y nulos). Tipos de datos más complejos como DateTime, Guid, Int64 y geometría se pueden representar tanto en JSON como en DocumentDB mediante la creación de objetos anidados con el operador { } y matrices con el operador [ ].

### ¿De qué modo DocumentDB proporciona simultaneidad?
DocumentDB admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP o ETag. Cada recurso de DocumentDB tiene una ETag, y los clientes de DocumentDB incluyen su última versión de lectura en las solicitudes de escritura. Si la etiqueta ETag es actual, el cambio se confirma. Si el valor ha cambiado externamente, el servidor rechaza la escritura con un código de respuesta de error de condición previa HTTP 412. Los clientes deben leer la última versión del recurso y reintentar la solicitud.

### ¿Cómo se realizan transacciones en DocumentDB?
DocumentDB admite transacciones integradas en el lenguaje a través de procedimientos almacenados y desencadenadores JavaScript. Todas las operaciones de base de datos dentro de scripts se ejecutan en un aislamiento de instantánea limitado a la colección si es una colección de partición única, o en documentos con el mismo valor de la clave de partición dentro de una colección, si la colección está particionada. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte. Vea [Programación de servidor DocumentDB](documentdb-programming.md) para más detalles.

### ¿Cómo se pueden insertar documentos en masa en DocumentDB? 
Existen tres maneras de insertar documentos de forma masiva en DocumentDB:

- La herramienta de migración de datos, como se describe en [Importación de datos en DocumentDB](documentdb-import-data.md).
- El Explorador de documentos en el Portal de Azure, como se describe en [Adición en masa de documentos con el Explorador de documentos](documentdb-view-json-document-explorer.md#BulkAdd).
- Procedimientos almacenados, como se describe en [Programación de servidor DocumentDB](documentdb-programming.md).

### ¿Admite DocumentDB el almacenamiento en caché de vínculos de recursos?
Sí. Como DocumentDB es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de DocumentDB pueden especificar un encabezado "If-None-Match" para las lecturas con relación a cualquier recurso como documento o colección y actualizar sus copias locales solo cuando la versión del servidor cambie.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=AcomDC_0615_2016-->