<properties title="Frequently asked questions about DocumentDB" pageTitle="Frequently asked questions about DocumentDB | Azure" description="Answers to frequently asked questions about Azure DocumentDB databases. Learn about capacity and request units, and understand how to scale to your application needs." metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, faq"   services="documentdb" solutions="data-management"   authors="bradsev" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="bradsev"></tags>

# Preguntas más frecuentes sobre Base de datos de documentos

## En este artículo

-   [Fundamentos de Base de datos de documentos de Azure][Fundamentos de Base de datos de documentos de Azure]
-   [Configuración de Base de datos de documentos de Azure][Configuración de Base de datos de documentos de Azure]
-   [Desarrollo con Base de datos de documentos de Azure][Desarrollo con Base de datos de documentos de Azure]

## <span id="fundamentals"></span></a>Fundamentos de Base de datos de documentos de Azure

### ¿Qué es Base de datos de documentos de Azure?

Base de datos de documentos de Microsoft Azure es un servicio de base de datos de documentos NoSQL muy escalable que ofrece consultas enriquecidas a través de datos sin esquemas, ayuda a proporcionar un rendimiento configurable y confiable y favorece el desarrollo rápido, y todo gracias a una plataforma administrada respaldada por la potencia y el alcance de Microsoft Azure. Base de datos de documentos es la solución adecuada para aplicaciones web y móviles cuando lo que se busca es un rendimiento predecible, una baja latencia y un modelo de datos sin esquemas. Ofrece flexibilidad de esquemas e indexación enriquecida a través de un modelo de datos JSON nativo, e incluye compatibilidad transaccional de varios documentos con JavaScript integrado.

Para obtener instrucciones sobre la implementación y uso de este servicio, consulte la [página de documentación de Base de datos de documentos][página de documentación de Base de datos de documentos].

### ¿Qué clase de base de datos es Base de datos de documentos?

Base de datos de documentos es una base de datos orientada a documentos NoSQL que almacena datos en formato JSON. Es compatible con estructuras de datos independientes anidados que se pueden consultar mediante una gramática de consultas SQL enriquecida para Base de datos de documentos. Base de datos de documentos ofrece un procesamiento de transacciones de alto rendimiento del servidor JavaScript a través de procedimientos almacenados, desencadenadores y funciones definidas por el usuario. La base de datos admite también niveles de coherencia ajustables por el desarrollador con niveles de rendimiento asociados.

### ¿Tienen las bases de datos de Base de datos de documentos tablas como RDBMS?

No, Base de datos de documentos almacena los datos en colecciones de documentos JSON. Para obtener más información acerca de los recursos de Base de datos de documentos, consulte el artículo sobre modelo de recursos y conceptos de Base de datos de documentos.

### ¿Admiten las bases de datos de Base de datos de documentos datos sin esquemas?

Sí, Base de datos de documentos permite que las aplicaciones almacenen documentos JSON arbitrarios sin definiciones ni sugerencias de esquemas. Los datos están disponibles inmediatamente para consulta a través de la interfaz de consulta SQL de Base de datos de documentos.

### ¿Admite Base de datos de documentos transacciones ACID?

Sí, Base de datos de documentos admite transacciones entre documentos expresadas como procedimientos almacenados y desencadenadores JavaScript. Las transacciones se limitan a una única colección y se ejecutan con semánticas ACID, como todo o nada, aisladas de otras que ejecutan a la vez código y solicitudes de usuario. Si se producen excepciones por la ejecución en el servidor de código de aplicación JavaScript, la transacción entera se revierte.

### ¿Cuáles son los casos de uso típicos de Base de datos de documentos?

Base de datos de documentos es una buena opción para nuevas aplicaciones web y móviles en las que la escala, el rendimiento y la consulta a través de datos sin esquemas son importantes. Se presta muy bien a un desarrollo rápido y admite la iteración continua de modelos de datos de aplicaciones. Casos de uso comunes de Base de datos de documentos son las aplicaciones que administran contenido y datos generados por el usuario.

### ¿Cuáles son la escala y los límites de capacidad?

Cada cuenta de Base de datos de documentos de Azure admite un número máximo de unidades de capacidad que se pueden configurar a través del portal de Azure. Si necesita unidades de capacidad adicionales, póngase en contacto con el soporte técnico para que le aumenten la cuota de la cuenta. Para obtener más información sobre las cuotas de recursos, consulte el artículo sobre los límites de servicio de Base de datos de documentos.

### ¿Cuánto cuesta Base de datos de documentos de Microsoft Azure?

Consulte el artículo de [detalles de precios de Base de datos de documentos][detalles de precios de Base de datos de documentos] para obtener más información.

## <span id="setup"></span></a>Configuración de Base de datos de documentos de Microsoft Azure

### ¿Cómo me registro en Base de datos de documentos de Microsoft Azure?

Base de datos de documentos de Microsoft Azure (vista previa) está disponible en el nuevo Portal de vista previa de Azure. Primero debe registrarse para una suscripción a Microsoft Azure. Una vez hecho esto, puede agregar una cuenta de Base de datos de documentos a su suscripción de Azure a través de la Galería.

### ¿Qué es una clave maestra?

Una clave maestra es un token de seguridad para acceder a todos los recursos de una cuenta. Los individuos con esta clave tienen acceso de lectura y escritura a todos los recursos de la cuenta de base de datos. Tenga cuidado cuando distribuya claves maestras.

### ¿Cómo se crea una base de datos?

Puede crear una base de datos mediante uno de los SDK de Base de datos de documentos o a través de las API REST. Consulte la sección Desarrollar de la [página de documentación de Base de datos de documentos][página de documentación de Base de datos de documentos] para obtener información sobre cómo desarrollar aplicaciones.

### ¿Qué es una colección?

Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript. Las consultas y transacciones se limitan a colecciones. Puede almacenar un conjunto de documentos JSON heterogéneos en una sola colección, todos los cuales se indexan automáticamente.

### ¿Hay límites en las bases de datos y colecciones?

Cada unidad de capacidad adquirida viene con una asignación de almacenamiento de base de datos y rendimiento aprovisionado. También existen cuotas para cada recurso administrado por el servicio. Consulte la sección sobre los límites de servicio de Base de datos de documentos para obtener más información.

### ¿Cómo se configuran los usuarios y los permisos?

Puede crear usuarios y permisos mediante uno de los SDK de Base de datos de documentos o a través de las API REST. Consulte la sección Desarrollar de la [página de documentación de Base de datos de documentos][página de documentación de Base de datos de documentos] para obtener información sobre cómo desarrollar aplicaciones. .

## <span id="develop"></span></a>Desarrollo con Base de datos de documentos de Microsoft Azure

### ¿Cómo se empieza a desarrollar con Base de datos de documentos?

En vista previa, dispone de SDK para .NET, Python, Node.js y JavaScript. Los desarrolladores pueden aprovechar también las API RESTful HTTP para interactuar con recursos de Base de datos de documentos desde una variedad de plataformas y lenguajes. Para obtener detalles acerca de cómo usar estos SDK, consulte la sección Desarrollar en la [página de documentación de Base de datos de documentos][página de documentación de Base de datos de documentos].

### ¿Admite Base de datos de documentos SQL?

El lenguaje de consultas SQL de Base de datos de documentos ofrece operadores relacionales y jerárquicos enriquecidos, junto con extensibilidad a través de funciones definidas por el usuario (UDF) basadas en JavaScript. La gramática JSON permite el modelado de documentos JSON como árboles con etiquetas a modo de nodos de árbol, hecho que aprovechan también las técnicas de indexación automática de Base de datos de documentos, que es su dialecto de consulta SQL. Para obtener información detallada sobre el uso de nuestra gramática SQL, consulte el artículo sobre la [realización de consultas mediante SQL de Base de datos de documentos][realización de consultas mediante SQL de Base de datos de documentos].

### ¿Qué tipos de datos admite Base de datos de documentos?

Los tipos de datos primitivos admitidos en Base de datos de documentos son los mismos que en JSON. JSON posee un sencillo sistema de tipos que consta de cadenas, números (doble precisión IEEE754), booleanos (verdadero y falso) y valores Null. Tipos de datos más complejos como DateTime, Guid, Int64 y geometría se pueden representar tanto en JSON como en Base de datos de documentos mediante la creación de objetos anidados con el operador { } y matrices con el operador [ ].

### ¿De qué modo Base de datos de documentos proporciona simultaneidad?

Base de datos de documentos admite control de simultaneidad optimista (OCC) a través de etiquetas de entidad HTTP o ETag. Cada recurso de Base de datos de documentos tiene una ETag, y los clientes de Base de datos de documentos incluyen su última versión de lectura en las solicitudes de escritura. Si la etiqueta ETag es actual, el cambio se confirma. Si el valor ha cambiado externamente, el servidor rechaza la escritura con un código de respuesta de error de condición previa HTTP 412. Los clientes deben leer la última versión del recurso y reintentar la solicitud.

### ¿Cómo se realizan transacciones en Base de datos de documentos?

Base de datos de documentos admite transacciones integradas en el lenguaje a través de procedimientos almacenados y desencadenadores JavaScript. Todas las operaciones de base de datos dentro de los scripts se ejecutan bajo el aislamiento de instantáneas limitadas a la colección. Una instantánea de las versiones de documento (etiquetas ETag) se toma al comienzo de la transacción y solo se confirma si el script se ejecuta correctamente. Si el JavaScript produce un error, la transacción se revierte.

### ¿Cómo se pueden insertar documentos en masa en Base de datos de documentos?

La compatibilidad de Base de datos de documentos con procedimientos almacenados ofrece un medio eficiente de realizar inserciones por lotes. Mediante el desarrollo de un procedimiento almacenado JavaScript sencillo que acepte e inserte documentos, puede realizar inserciones en masa. Esto tiene el beneficio añadido de que la inserción en masa se realizará como una transacción, lo que dejará a la colección en un estado coherente. Para obtener detalles sobre el modelo de programación, consulte la sección Desarrollar en la [página de documentación de Base de datos de documentos][página de documentación de Base de datos de documentos].

### ¿Admite Base de datos de documentos el almacenamiento en caché de vínculos de recursos?

Sí. Como Base de datos de documentos es un servicio RESTful, los vínculos de recursos son inmutables y se pueden almacenar en caché. Los clientes de Base de datos de documentos pueden especificar un encabezado "If-None-Match" para las lecturas con relación a cualquier recurso como documento o colección y actualizar sus copias locales solo cuando la versión del servidor cambie.

  [Fundamentos de Base de datos de documentos de Azure]: #fundamentals
  [Configuración de Base de datos de documentos de Azure]: #setup
  [Desarrollo con Base de datos de documentos de Azure]: #develop
  [página de documentación de Base de datos de documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402319
  [detalles de precios de Base de datos de documentos]: http://go.microsoft.com/fwlink/p/?LinkID=402317
  [realización de consultas mediante SQL de Base de datos de documentos]: ../documentdb-sql-query/
