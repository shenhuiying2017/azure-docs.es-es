<properties 
	pageTitle="Preguntas más frecuentes sobre DocumentDB | Azure" 
	description="Respuestas a las preguntas frecuentes acerca del servicio de base de datos de documentos nosql de Azure DocumentDB. Obtenga información acerca de las unidades de solicitudes y capacidad, y comprenda cómo escalar para satisfacer sus necesidades de aplicación." 
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
	ms.date="04/29/2015" 
	ms.author="mimig"/>


#Preguntas más frecuentes sobre DocumentDB

## <a id="fundamentals"></a> Fundamentos de Microsoft Azure DocumentDB

###¿Qué es Azure DocumentDB? 
DocumentDB de Microsoft Azure es un servicio de base de datos de documentos NoSQL muy escalable que ofrece consultas enriquecidas a través de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y confiable y favorece el desarrollo rápido, y todo gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. DocumentDB es la solución adecuada para aplicaciones web y móviles cuando un rendimiento predecible, una baja latencia y un modelo de datos sin esquemas son requisitos clave. Ofrece flexibilidad de esquemas e indexación enriquecida a través de un modelo de datos JSON nativo, e incluye compatibilidad transaccional de varios documentos con JavaScript integrado.
  
Para obtener instrucciones sobre la implementación y uso de este servicio, consulte la [página de documentación de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###¿Qué clase de base de datos es DocumentDB?
DocumentDB es una base de datos orientada a documentos NoSQL que almacena datos en formato JSON. Es compatible con estructuras de datos independientes anidados que se pueden consultar mediante una gramática de consultas SQL enriquecida para DocumentDB. DocumentDB ofrece un procesamiento de transacciones de alto rendimiento del servidor JavaScript a través de procedimientos almacenados, desencadenadores y funciones definidas por el usuario. La base de datos admite también niveles de coherencia ajustables por el desarrollador con niveles de rendimiento asociados.
 
###¿Tienen las bases de datos de DocumentDB tablas como RDBMS?
No, DocumentDB almacena los datos en colecciones de documentos JSON. Para obtener información sobre los recursos de DocumentDB, vea el artículo [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md).

###¿Admiten las bases de datos de DocumentDB datos sin esquemas?
Sí, DocumentDB permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de DocumentDB.

###¿Admite DocumentDB transacciones ACID?
Sí, DocumentDB admite transacciones entre documentos expresadas como procedimientos almacenados y desencadenadores JavaScript. Las transacciones se limitan a una única colección y se ejecutan con semánticas ACID, como todo o nada, aisladas de otras que ejecutan a la vez código y solicitudes de usuario. Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte.

###¿Cuáles son los casos de uso típicos de DocumentDB?  
DocumentDB es una buena opción para nuevas aplicaciones web y móviles en las que la escala, el rendimiento y la capacidad de consulta a través de datos sin esquemas son importantes. Se presta a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones. Casos de uso comunes de DocumentDB son las aplicaciones que administran contenido y datos generados por el usuario.

###¿Cuáles son los límites de escala de DocumentDB?
Las cuentas de DocumentDB se pueden escalar en términos de almacenamiento y rendimiento agregando colecciones. Vea [Límites de DocumentDB](documentdb-limits.md) para las cuotas de servicio para el número de colecciones. Si necesita colecciones adicionales, [póngase en contacto con el soporte técnico](documentdb-increase-limits.md) para que le aumenten la cuota de la cuenta.

###¿Cuánto cuesta DocumentDB de Microsoft Azure?
Consulte la página de [detalles de precios de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402317) para obtener más información.

## <a id="setup"></a> Configuración de Microsoft Azure DocumentDB

###¿Cómo me registro en DocumentDB de Microsoft Azure?
Microsoft Azure DocumentDB está disponible en el nuevo [Portal de vista previa de Azure][azure-portal]. Primero debe registrarse para una suscripción a Microsoft Azure. Una vez hecho esto, puede agregar una cuenta de DocumentDB a su suscripción de Azure a través de Marketplace.

###¿Qué es una clave maestra?
Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los individuos con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras. La clave maestra principal y la secundaria están disponibles en la hoja Claves del [Portal de vista previa de Azure][azure-portal].

###¿Cómo se crea una base de datos?
Puede crear una base de datos mediante uno de los SDK de DocumentDB o a través de las API REST. Vea la sección Desarrollo de la [página de documentación de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319) para obtener información sobre cómo desarrollar aplicaciones.

###¿Qué es una colección?
Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Las consultas y transacciones se limitan a colecciones. Puede almacenar un conjunto de documentos JSON heterogéneos en una sola colección, todos los cuales se indexan automáticamente.

###¿Hay límites en las bases de datos y colecciones?
Cada colección viene con una asignación de almacenamiento de base de datos y rendimiento aprovisionado en uno de los niveles de rendimiento admitidos. Vea [Niveles de rendimiento](documentdb-performance-levels.md) para obtener más información sobre los niveles de rendimiento. También existen cuotas para cada recurso administrado por el servicio. Consulte [Límites de DocumentDB](documentdb-limits.md) para obtener más información.

###¿Cómo se configuran los usuarios y los permisos?
Puede crear usuarios y permisos mediante uno de los SDK de DocumentDB o a través de las API REST. Consulte la sección Desarrollo de la [página de documentación de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319) para obtener información sobre cómo desarrollar aplicaciones.


## <a id="develop"></a>Desarrollo con Microsoft Azure DocumentDB

###¿Cómo se empieza a desarrollar con DocumentDB?
Los SDK están disponibles para NET, Python, Node.js, JavaScript y Java. Los desarrolladores pueden aprovechar también las API RESTful HTTP para interactuar con recursos de DocumentDB desde una variedad de plataformas y lenguajes. Para obtener detalles acerca de cómo usar estos SDK, consulte la sección Desarrollar en la [página de documentación de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###¿Admite DocumentDB SQL?
El lenguaje de consulta SQL de DocumentDB proporciona operadores relacionales y jerárquicos y una extensibilidad a través de JavaScript según las funciones definidas por el usuario (UDF). La gramática JSON permite el modelado de documentos JSON como árboles con etiquetas a modo de nodos de árbol, algo que utilizan las técnicas de indexación automática de DocumentDB y el dialecto de consulta SQL de DocumentDB. Para obtener información detallada sobre el uso de nuestra gramática SQL, consulte el artículo [Consultas mediante SQL de DocumentDB][query].

###¿Qué tipos de datos admite DocumentDB?
Los tipos de datos primitivos admitidos en DocumentDB son los mismos que en JSON. JSON posee un sencillo sistema de tipos que consta de cadenas, números (doble precisión IEEE754), booleanos (verdadero y falso) y valores Null. Tipos de datos más complejos como DateTime, Guid, Int64 y geometría se pueden representar tanto en JSON como en DocumentDB mediante la creación de objetos anidados con el operador { } y matrices con el operador [ ].

###¿De qué modo DocumentDB proporciona simultaneidad?
DocumentDB admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP o ETag. Cada recurso de DocumentDB tiene una ETag, y los clientes de DocumentDB incluyen su última versión de lectura en las solicitudes de escritura. Si la etiqueta ETag es actual, el cambio se confirma. Si el valor ha cambiado externamente, el servidor rechaza la escritura con un código de respuesta de error de condición previa HTTP 412. Los clientes deben leer la última versión del recurso y reintentar la solicitud.

###¿Cómo se realizan transacciones en DocumentDB?
Base de datos de documentos admite transacciones integradas en el lenguaje a través de procedimientos almacenados y desencadenadores JavaScript. Todas las operaciones de base de datos dentro de los scripts se ejecutan bajo el aislamiento de instantáneas limitadas a la colección. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte. Vea la [programación del lado servidor de DocumentDB](documentdb-programming.md) para obtener más detalles.

###¿Cómo se pueden insertar documentos en masa en DocumentDB? 
DocumentDB admite procedimientos almacenados, que proporcionan un medio eficaz para procesar por lotes las inserciones. Mediante el desarrollo de un procedimiento almacenado JavaScript sencillo que acepte e inserte documentos, puede realizar inserciones en masa. Esto tiene el beneficio añadido de que la inserción en masa se realizará como una transacción, lo que dejará a la colección en un estado coherente. Para obtener detalles sobre el modelo de programación, vea la sección Desarrollar en la [página de documentación de DocumentDB](http://go.microsoft.com/fwlink/p/?LinkID=402319).

###¿Admite DocumentDB el almacenamiento en caché de vínculos de recursos?
Sí. Como DocumentDB es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de DocumentDB pueden especificar un encabezado "If-None-Match" para las lecturas con relación a cualquier recurso como documento o colección y actualizar sus copias locales solo cuando la versión del servidor cambie.




[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md
 

<!---HONumber=July15_HO3-->