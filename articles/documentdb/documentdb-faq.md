---
title: "Preguntas de base de datos DocumentDB - Preguntas más frecuentes| Microsoft Docs"
description: Obtenga respuestas a las preguntas frecuentes sobre Azure DocumentDB, un servicio de base de datos de documentos NoSQL para JSON. Responda a preguntas de base de datos acerca de la capacidad, los niveles de rendimiento y escalado.
keywords: Database questions, frequently asked questions, documentdb, azure, Microsoft azure
services: documentdb
author: mimig1
manager: jhubbard
editor: monicar
documentationcenter: 
ms.assetid: b68d1831-35f9-443d-a0ac-dad0c89f245b
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 97acd09d223e59fbf4109bc8a20a25a2ed8ea366
ms.openlocfilehash: 8ebc1aa663f298d1f3f495523d85bda8777d5d29
ms.lasthandoff: 03/10/2017


---
# <a name="frequently-asked-questions-about-documentdb"></a>Preguntas más frecuentes sobre DocumentDB
## <a name="database-questions-about-microsoft-azure-documentdb-fundamentals"></a>Preguntas de bases de datos  sobre los Fundamentos de Microsoft Azure DocumentDB
### <a name="what-is-microsoft-azure-documentdb"></a>¿Qué es Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB es un servicio de base de datos de documentos NoSQL ultrarrápido y de escala planetaria que ofrece consultas enriquecidas a través de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y confiable, y posibilita un desarrollo rápido. Todo ello gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. DocumentDB es la solución adecuada para aplicaciones web, móviles, de juego e IoT cuando los requisitos clave son un rendimiento predecible, una alta disponibilidad, una baja latencia y un modelo de datos sin esquemas. Ofrece flexibilidad de esquemas e indexación enriquecida a través de un modelo de datos JSON nativo, e incluye compatibilidad transaccional de varios documentos con JavaScript integrado.  

Para obtener más preguntas, respuestas e instrucciones de bases de datos sobre la implementación y el uso de este servicio, consulte la [página de documentación de DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

### <a name="what-kind-of-database-is-documentdb"></a>¿Qué clase de base de datos es DocumentDB?
DocumentDB es una base de datos orientada a documentos NoSQL que almacena datos en formato JSON.  Es compatible con estructuras de datos independientes anidados que se pueden consultar mediante una [gramática de consultas SQL](documentdb-sql-query.md)enriquecida para DocumentDB. DocumentDB ofrece un procesamiento de transacciones de alto rendimiento del servidor JavaScript a través de [procedimientos almacenados, desencadenadores y funciones definidas por el usuario](documentdb-programming.md). La base de datos admite también niveles de coherencia ajustables por el desarrollador con [niveles de rendimiento](documentdb-performance-levels.md)asociados.

### <a name="do-documentdb-databases-have-tables-like-a-relational-database-rdbms"></a>¿Las bases de datos de DocumentDB tiene tablas como una base de datos relacional (RDBMS)?
No, DocumentDB almacena los datos en colecciones de documentos JSON.  Para obtener información sobre los recursos de DocumentDB, consulte [Modelo de recursos y conceptos de DocumentDB](documentdb-resources.md). Para obtener más información sobre en qué se diferencian las soluciones NoSQL, como DocumentDB, de las relacionales, consulte [NoSQL frente a SQL](documentdb-nosql-vs-sql.md).

### <a name="do-documentdb-databases-support-schema-free-data"></a>¿Admiten las bases de datos de DocumentDB datos sin esquemas?
Sí, DocumentDB permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de DocumentDB.   

### <a name="does-documentdb-support-acid-transactions"></a>¿Admite DocumentDB transacciones ACID?
Sí, DocumentDB admite transacciones entre documentos expresadas como procedimientos almacenados y desencadenadores JavaScript. Las transacciones se limitan a una única partición dentro de cada colección y se ejecutan con semánticas ACID, como todo o nada, aisladas de otras que ejecutan a la vez solicitudes de usuario y código.  Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte. Para obtener más información sobre las transacciones, consulte [Transacciones del programa de base de datos](documentdb-programming.md#database-program-transactions).

### <a name="what-are-the-typical-use-cases-for-documentdb"></a>¿Cuáles son los casos de uso típicos de DocumentDB?
DocumentDB es una buena elección para las nuevas aplicaciones web, móviles, de juego e IoT donde  es importante la escala automática, el rendimiento predecible, la orden rápida de tiempos de respuesta en milisegundos y la capacidad para consultar datos sin esquemas. Se presta a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones. [Casos de uso comunes de DocumentDB](documentdb-use-cases.md)son las aplicaciones que administran contenido y datos generados por el usuario.  

### <a name="how-does-documentdb-offer-predictable-performance"></a>¿Cómo ofrece DocumentDB un rendimiento predecible?
Una [unidad de solicitud](documentdb-request-units.md) es la medida de rendimiento de DocumentDB. 1 RU corresponde al rendimiento de una solicitud GET de un documento de 1 KB. Cada operación en DocumentDB, incluidas las lecturas, escrituras, consultas SQL y ejecuciones de procedimiento almacenado, tiene un valor de unidad de solicitud determinista basado en el rendimiento necesario para completar la operación. En vez de pensar en la CPU, la E/S, la memoria y en cómo afecta cada una al rendimiento de la aplicación, puede hacerlo en términos de una medida única de unidad de solicitud.

Cada colección de DocumentDB puede reservarse con rendimiento aprovisionado en términos de unidades de solicitud del rendimiento por segundo. Para las aplicaciones de cualquier escala, puede realizar pruebas comparativas de las solicitudes individuales para medir los valores de unidad de solicitud y aprovisionar colecciones para controlar la suma total de las unidades de solicitud en todas las solicitudes. También puede escalar o reducir verticalmente el rendimiento de su colección a medida que evolucionen las necesidades de la aplicación. Para más información acerca de las unidades de solicitud y ayuda para determinar las necesidades de su colección, consulte [Estimación de necesidades de rendimiento](documentdb-request-units.md#estimating-throughput-needs) y pruebe la [calculadora de rendimiento](https://www.documentdb.com/capacityplanner).

### <a name="is-documentdb-hipaa-compliant"></a>¿Es DocumentDB compatible con HIPAA?
Sí, DocumentDB es compatible con HIPAA. HIPAA establece los requisitos para el uso, la divulgación y la protección de información de mantenimiento identificable individualmente. Para más información, consulte el [Centro de confianza de Microsoft](https://www.microsoft.com/en-us/TrustCenter/Compliance/HIPAA).

### <a name="what-are-the-storage-limits-of-documentdb"></a>¿Cuáles son los límites de almacenamiento de DocumentDB?
No existe ningún límite en la cantidad total de datos que puede almacenar una colección en DocumentDB.

### <a name="what-are-the-throughput-limits-of-documentdb"></a>¿Cuáles son los límites de rendimiento de DocumentDB?
No hay ningún límite en la cantidad total de rendimiento que puede admitir una colección en DocumentDB, si la carga de trabajo se puede distribuir aproximadamente de manera uniforme entre un número suficientemente elevado de claves de partición.

### <a name="how-much-does-microsoft-azure-documentdb-cost"></a>¿Cuánto cuesta Microsoft Azure DocumentDB?
Para más información, consulte la página [Detalles de precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/). Los cargos por uso de DocumentDB se determinan según la cantidad de colecciones aprovisionadas, el número de horas que las colecciones estuvieron conectadas y el rendimiento aprovisionado de cada colección.

### <a name="is-there-a-free-account-available"></a>¿Existe una cuenta gratuita disponible?
Si es la primera vez que usa Azure, regístrese para obtener una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), que ofrece 30 días y 200 USD para que pruebe todos los servicios de Azure. Si tiene una suscripción a Visual Studio, puede recibir [150 USD en créditos gratis de Azure al mes](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) para usarlos en cualquier servicio de Azure.  

También puede usar el [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) para desarrollar y probar su aplicación localmente, de forma gratuita, sin necesidad de crear una suscripción de Azure. Cuando esté satisfecho con el funcionamiento de la aplicación en el Emulador de DocumentDB, puede cambiar al uso de una cuenta de Azure DocumentDB en la nube.

### <a name="how-can-i-get-additional-help-with-documentdb"></a>¿Cómo puedo obtener ayuda adicional con DocumentDB?
Si necesita ayuda, póngase en contacto con nosotros en [Stack Overflow](http://stackoverflow.com/questions/tagged/azure-documentdb) o programe un chat 1:1 con el equipo de ingeniería de DocumentDB, para lo que debe enviar un correo a [askdocdb@microsoft.com](mailto:askdocdb@microsoft.com). Para mantenerse al día en las novedades y características más recientes de DocumentDB, síganos en [Twitter](https://twitter.com/DocumentDB).

## <a name="set-up-microsoft-azure-documentdb"></a>Configuración de Microsoft Azure DocumentDB
### <a name="how-do-i-sign-up-for-microsoft-azure-documentdb"></a>¿Cómo me registro en Microsoft Azure DocumentDB?
Microsoft Azure DocumentDB está disponible en el nuevo portal [Azure Portal][azure-portal].  Primero debe registrarse para una suscripción a Microsoft Azure.  Una vez hecho esto, puede agregar una cuenta de DocumentDB a su suscripción a Azure. Para instrucciones sobre cómo agregar una cuenta de DocumentDB, vea [Creación de una cuenta de base de datos de DocumentDB](documentdb-create-account.md).   

### <a name="what-is-a-master-key"></a>¿Qué es una clave maestra?
Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los individuos con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras. La clave maestra principal y la secundaria están disponibles en la hoja **Claves** de [Azure Portal][azure-portal]. Para más información sobre las claves, vea [Visualización, copia y regeneración de las claves de acceso](documentdb-manage-account.md#keys).

### <a name="how-do-i-create-a-database"></a>¿Cómo se crea una base de datos?
Puede crear bases de datos mediante [Azure Portal]() como se describe en [Creación de una colección y una base de datos de DocumentDB](documentdb-create-collection.md), uno de los [SDK de DocumentDB](documentdb-sdk-dotnet.md) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).  

### <a name="what-is-a-collection"></a>¿Qué es una colección?
Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Una colección es una entidad facturable, en la que el [costo](documentdb-performance-levels.md) lo determinan el rendimiento y el almacenamiento que se usen. Las colecciones pueden abarcar una o varias particiones o uno o varios servidores y se pueden escalar para administrar volúmenes prácticamente ilimitados de almacenamiento o rendimiento.

Las colecciones también son las entidades de facturación de DocumentDB. Cada colección se factura por hora basándose en el rendimiento aprovisionado y en el espacio de almacenamiento usado. Para obtener más información, consulte [Precios de DocumentDB](https://azure.microsoft.com/pricing/details/documentdb/).  

### <a name="how-do-i-set-up-users-and-permissions"></a>¿Cómo se configuran los usuarios y los permisos?
Puede crear usuarios y permisos mediante uno de los [SDK de DocumentDB](documentdb-sdk-dotnet.md) o a través de las [API de REST](https://msdn.microsoft.com/library/azure/dn781481.aspx).   

## <a name="database-questions-about-developing-against-microsoft-azure-documentdb"></a>Preguntas de bases de datos sobre el desarrollo con Microsoft Azure DocumentDB
### <a name="how-to-do-i-start-developing-against-documentdb"></a>¿Cómo se empieza a desarrollar con DocumentDB?
[SDK](documentdb-sdk-dotnet.md) están disponibles para NET, Python, Node.js, JavaScript y Java.  Los desarrolladores también pueden usar las [API de HTTP RESTful](https://msdn.microsoft.com/library/azure/dn781481.aspx) para interactuar con recursos de DocumentDB desde varias plataformas y lenguajes.

Ejemplos de los SDK de DocumentDB [.NET](documentdb-dotnet-samples.md), [Java](https://github.com/Azure/azure-documentdb-java), [Node.js](documentdb-nodejs-samples.md) y [Python](documentdb-python-samples.md) se encuentran disponibles en GitHub.

### <a name="does-documentdb-support-sql"></a>¿Admite DocumentDB SQL?
El lenguaje de consulta de DocumentDB SQL es un subconjunto mejorado de la funcionalidad de consulta admitida por SQL. El lenguaje de consulta SQL de DocumentDB proporciona operadores relacionales y jerárquicos y una extensibilidad a través de JavaScript según las funciones definidas por el usuario (UDF). La gramática de JSON permite el modelado de documentos JSON como árboles con etiquetas como los nodos del árbol, algo que usan las técnicas de indexación automática de DocumentDB y el dialecto de consulta SQL de DocumentDB.  Para obtener información detallada acerca del uso de la gramática de SQL, consulte el artículo [Consulta SQL y sintaxis SQL en DocumentDB][query].

### <a name="does-documentdb-support-sql-aggregation-functions"></a>¿Admite DocumentDB las funciones de agregación de SQL?
DocumentDB admite la agregación de baja latencia a cualquiera de escala a través de las funciones de agregado `COUNT`, `MIN`, `MAX`, `AVG` y `SUM` a través de la gramática SQL. Para más información, consulte [Funciones de agregado](documentdb-sql-query.md#Aggregates).

### <a name="what-are-the-data-types-supported-by-documentdb"></a>¿Qué tipos de datos admite DocumentDB?
Los tipos de datos primitivos admitidos en DocumentDB son los mismos que en JSON. JSON posee un sencillo sistema de tipos que consta de cadenas, números (doble precisión IEEE754) y valores booleanos (verdadero, falso y nulos). DocumentDB admite de forma nativa los tipos espaciales Point, Polygon y LineString expresados como GeoJSON. Tipos de datos más complejos como DateTime, Guid, Int64 y geometría se pueden representar tanto en JSON como en DocumentDB mediante la creación de objetos anidados con el operador { } y matrices con el operador [ ].

### <a name="how-does-documentdb-provide-concurrency"></a>¿De qué modo DocumentDB proporciona simultaneidad?
DocumentDB admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP o ETag. Cada recurso de DocumentDB tiene una ETag, que se establece en el servidor cada vez que se actualiza un documento. El encabezado de la ETag y el valor actual se incluyen en todos los mensajes de respuesta. Las ETag pueden utilizarse con el encabezado If-Match para permitir que el servidor decida si debe actualizarse un recurso. El valor de If-Match es el valor de ETag que somete al proceso de comprobación. Si el valor de ETag coincide con el valor de ETag del servidor, se actualizará el recurso. Si el valor de ETag ya no es el actual, el servidor rechaza la operación con un código de respuesta HTTP 412 Precondition failure (HTTP 412: error de condición previa). Después, el cliente tendrá que volver a recuperar el recurso para obtener el valor de ETag actual del recurso. Además, las ETag pueden emplearse con el encabezado If-None-Match para determinar si hay que volver a recuperar un recurso.

Para usar la simultaneidad optimista en .NET, utilice la clase [AccessCondition](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.accesscondition.aspx) . Para ver un ejemplo de .NET, consulte la sección [Program.cs](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/DocumentManagement/Program.cs) en el ejemplo del artículo DocumentManagement de GitHub.

### <a name="how-do-i-perform-transactions-in-documentdb"></a>¿Cómo se realizan transacciones en DocumentDB?
DocumentDB admite transacciones integradas en el lenguaje a través de procedimientos almacenados y desencadenadores JavaScript. Todas las operaciones de base de datos dentro de scripts se ejecutan en un aislamiento de instantánea limitado a la colección si es una colección de partición única, o en documentos con el mismo valor de la clave de partición dentro de una colección, si la colección está particionada. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte. Vea [Programación de servidor DocumentDB](documentdb-programming.md) para más detalles.

### <a name="how-can-i-bulk-insert-documents-into-documentdb"></a>¿Cómo se pueden insertar documentos en masa en DocumentDB?
Existen tres maneras de insertar documentos de forma masiva en DocumentDB:

* La herramienta de migración de datos, como se describe en [Importación de datos en DocumentDB](documentdb-import-data.md).
* El Explorador de documentos en Azure Portal, como se describe en [Adición en masa de documentos con el Explorador de documentos](documentdb-view-json-document-explorer.md#bulk-add-documents).
* Procedimientos almacenados, como se describe en [Programación de servidor DocumentDB](documentdb-programming.md).

### <a name="does-documentdb-support-resource-link-caching"></a>¿Admite DocumentDB el almacenamiento en caché de vínculos de recursos?
Sí. Como DocumentDB es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de DocumentDB pueden especificar un encabezado "If-None-Match" para las lecturas con relación a cualquier recurso como documento o colección y actualizar sus copias locales solo cuando la versión del servidor cambie.

### <a name="is-a-local-instance-of-documentdb-available"></a>¿Hay una instancia local de DocumentDB disponible?
Sí. El [Emulador de Azure DocumentDB](documentdb-nosql-local-emulator.md) proporciona una emulación de alta fidelidad del servicio DocumentDB. Admite una funcionalidad idéntica a la de Azure DocumentDB, incluida la compatibilidad para crear y consultar documentos JSON, aprovisionar y escalar colecciones y ejecutar procedimientos y desencadenadores almacenados. Puede desarrollar y probar aplicaciones mediante el Emulador de DocumentDB e implementarlas en Azure a escala global realizando simplemente un solo cambio de configuración en el punto de conexión de la conexión de DocumentDB.

## <a name="database-questions-about-developing-against-api-for-mongodb"></a>Preguntas de bases de datos sobre el desarrollo con la API de MongoDB
### <a name="what-is-documentdbs-api-for-mongodb"></a>¿Qué es la API de MongoDB de DocumentDB?
La API de MongoDB de Microsoft Azure DocumentDB es una capa de compatibilidad que permite a las aplicaciones comunicarse de manera sencilla y transparente con el motor de base de datos de DocumentDB mediante los controladores y las API de Apache MongoDB compatibles con la comunidad. Los desarrolladores ahora pueden aprovechar las habilidades y las cadenas de herramienta de MongoDB para crear aplicaciones que emplean DocumentDB, con lo que se benefician de las funcionalidades únicas de esta solución, entre las que se encuentran la indización automática, el mantenimiento de copias de seguridad, los Acuerdos de Nivel de Servicio con respaldo financiero.

### <a name="how-to-do-i-connect-to-my-api-for-mongodb-database"></a>¿Cómo puedo conectarme a la base de datos de mi API de MongoDB?
La forma más rápida de conectarse la API de MongoDB de DocumentDB es a través de [Azure Portal](https://portal.azure.com). Acceda a su cuenta. En el panel de *navegación izquierda* de la cuenta, haga clic en *Inicio rápido*. *Inicio rápido* es la mejor manera de obtener fragmentos de código para conectarse a su base de datos. 

DocumentDB aplica estrictos estándares y requisitos de seguridad. Las cuentas de DocumentDB requieren autenticación y comunicación segura a través de *SSL* para garantizar el uso de la versión&1;.2 del protocolo TLS.

Para obtener más información, consulte el artículo sobre [conectarse a la base de datos de la API de MongoDB](documentdb-connect-mongodb-account.md).

### <a name="are-there-additional-error-codes-for-an-api-for-mongodb-database"></a>¿Existen otros códigos de error para una base de datos de la API de MongoDB?
La API de MongoDB tiene sus propios códigos de error, además de los comunes de MongoDB.


| Error               | Código  | Descripción  | Solución  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | El número total de unidades de solicitud consumido superó la tasa de unidad de solicitud de aprovisionamiento de la colección y se ha limitado. | Considere la posibilidad de escalar el rendimiento de la colección desde Azure Portal o vuelva a intentarlo. |
| ExceededMemoryLimit | 16501 | Como se trata de un servicio de varios inquilinos, la operación ha superado la asignación de memoria del cliente. | Reduzca el ámbito de la operación a través de un criterio de consulta más restrictivo o póngase en contacto con el equipo de asistencia técnica desde [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). <br><br>*Ejemplo: &nbsp;&nbsp;&nbsp;&nbsp;db.getCollection('users').aggregate([<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$match: {name: "Andy"}}, <br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{$sort: {age: -1}}<br>&nbsp;&nbsp;&nbsp;&nbsp;])*) |

[azure-portal]: https://portal.azure.com
[query]: documentdb-sql-query.md

