<properties 
	pageTitle="Modelo de recursos y conceptos de la DocumentDB | Azure" 
	description="La DocumentDB de Microsoft Azure es una DocumentDB NoSQL totalmente administrada que usa un modelo jerárquico de cuentas de base de datos, bases de datos, colecciones, procedimientos almacenados, desencadenadores, funciones definidas por el usuario, documentos, datos adjuntos, medios, usuarios y permisos para administrar los recursos."  
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/03/2015" 
	ms.author="mimig"/>

#Modelo de recursos y conceptos de DocumentDB

Las entidades de base de datos que administra DocumentDB se conocen como **recursos**. Cada recurso se identifica mediante un URI lógico. Se puede interactuar con los recursos mediante verbos HTTP estándar, encabezados de solicitud/respuesta y códigos de estado. 

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cuáles son los recursos de DocumentDB?
- ¿Cuál es la jerarquía de los recursos de DocumentDB?
- ¿Qué diferencia existe entre los recursos definidos por el sistema y los recursos definidos por el usuario?
- ¿Cómo se puede dirigir un recurso?
- ¿Cómo se trabaja con las colecciones?
- ¿Cómo se trabaja con procedimientos almacenados, desencadenadores y UDF?

##Modelo jerárquico de recursos
Tal y como muestra el siguiente diagrama, el **modelo de recursos** de DocumentDB consta de conjuntos de recursos en una cuenta de base de datos, cada uno de ellos direccionable mediante un URI lógico y estable. En este artículo, los conjuntos de recursos se denominan **fuentes**. 

>[AZURE.NOTE] DocumentDB ofrece también un protocolo de TCP sumamente eficaz que también es RESTful en su modelo de comunicación, y está disponible a través del [SDK de cliente de .NET](https://msdn.microsoft.com/library/azure/dn781482.aspx).

![][1]  
**Modelo de recursos jerárquico en una cuenta de base de datos**   

Para empezar a trabajar con los recursos, debe [crear una cuenta de base de datos para DocumentDB](documentdb-create-account.md) mediante su suscripción a Azure. Una cuenta de base de datos puede constar de un grupo de **bases de datos**, cada una con varias **colecciones**, que a su vez pueden contener **procedimientos almacenados, desencadenadores, UDF, documentos** y **datos adjuntos** relacionados. Una base de datos también tiene **usuarios** asociados, cada uno con un conjunto de **permisos** para obtener acceso a las colecciones, procedimientos almacenados, desencadenadores, UDF, documentos o datos adjuntos. Mientras las bases de datos, usuarios, permisos y colecciones son recursos definidos por el sistema con esquemas, documentos y datos adjuntos conocidos con contenido arbitrario JSON definido por el usuario.  

|Recurso 	|Descripción
|-----------|-----------
|Cuenta de base de datos	|Una cuenta de base de datos está asociada con una o más unidades de capacidad que representan un almacenamiento de documentos aprovisionado y rendimiento, un conjunto de bases de datos y almacenamiento de blobs. Puede crear una o más cuentas de base de datos mediante su suscripción a Azure.
|Database	|Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones. Es también un contenedor de usuarios.
|Usuario	|El espacio de nombres lógico para ámbito/partición de permisos. 
|Permiso	|Un token de autorización asociado con un usuario para el acceso autorizado a un recurso específico.
|Colección	|Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.
|Procedimiento almacenado	|Lógica de aplicaciones creada en JavaScript que se registra con una recopilación y se ejecuta de manera transaccional en el motor de la base de datos.
|Desencadenador	|Lógica de aplicaciones creada en JavaScript que modela los efectos secundarios asociados con las operaciones de inserción, reemplazo o eliminación.
|UDF	|Una lógica de aplicaciones sin efectos secundarios creada en JavaScript. Las UDF le permiten modelar un operador de consultas personalizado y con ello ampliar el lenguaje de consultas de la DocumentDB central.
|Documento	|Contenido JSON definido por el usuario (arbitrario). De manera predeterminada, no es necesario definir esquemas ni proporcionar índices secundarios para todos los documentos agregados a una recopilación.
|Datos adjuntos	|Los datos adjuntos son documentos especiales que contienen referencias y metadatos asociados con blobs/medios externos. El desarrollador puede decidir administrar los blobs a través de DocumentDB o almacenarlos con un proveedor de servicios de blobs externos como OneDrive, Dropbox, etc. 


##Recursos del sistema frente a recursos definidos por el usuario
Los recursos (por ejemplo, las cuentas de bases de datos, las bases de datos, las colecciones, los usuarios, los permisos, los procedimientos almacenados, los desencadenadores y las UDF) tienen todos un esquema fijo y se les denomina "recursos del sistema". En cambio, los recursos como documentos y datos adjuntos no tienen restricciones de esquema y son ejemplos de recursos definidos por el usuario. En la DocumentDB, tanto los recursos del sistema como los definidos por el usuario se representan y controlan como JSON compatibles con el estándar. Todos los recursos ya sean definidos por el sistema o el usuario tienen las siguientes propiedades en común.

>[AZURE.NOTE] Tenga en cuenta que todas las propiedades generadas por el sistema en un recurso tienen un prefijo con subrayado (_) en su representación de JSON.  


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>Propiedad</b></p></td>
<td valign="top" ><p><b>¿Lo puede configurar el usuario o lo genera el sistema?</b></p></td>
<td valign="top" ><p><b>Propósito</b></p></td>
</tr>

<tr>
<td valign="top" ><p>_rid</p></td>
<td valign="top" ><p>Generado por el sistema</p></td>
<td valign="top" ><p>Identificador del recurso generado por el sistema único y jerárquico</p></td>
</tr>

<tr>
<td valign="top" ><p>_etag</p></td>
<td valign="top" ><p>Generado por el sistema</p></td>
<td valign="top" ><p>etag del recurso necesario para un control optimista de concurrencia</p></td>
</tr>

<tr>
<td valign="top" ><p>_ts</p></td>
<td valign="top" ><p>Generado por el sistema</p></td>
<td valign="top" ><p>Última actualización de la marca de tiempo del recurso</p></td>
</tr>

<tr>
<td valign="top" ><p>_self</p></td>
<td valign="top" ><p>Generado por el sistema</p></td>
<td valign="top" ><p>URI del recurso único y direccionable</p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>Configurable por el usuario</p></td>
<td valign="top" ><p>Nombre del recurso único definido por el usuario</p></td>
</tr>

</tbody>
</table>  

###Representación de conexión de los recursos
La DocumentDB no exige ninguna extensión propietaria a la norma JSON ni codificaciones especiales; funciona con documentos JSON según la norma.  
 
###Direccionamiento de un recurso
Todos los recursos se pueden diseccionar mediante URI. El valor de la propiedad **_self** de un recurso representa la URI relativa del recurso. El formato de la URI consta de los segmentos de ruta /\<feed\>/{_rid}:  

|Valor de _self	|Descripción
|-------------------|-----------
|/dbs	|Fuente de bases de datos en una cuenta de base de datos.
|/dbs/{_rid-db}	|Base de datos con la propiedad de identificador único con el valor {_rid-db}.
|/dbs/{_rid-db}/colls/	|Fuente de colecciones en una base de datos.
|/dbs/{_rid-db}/colls/{_rid-coll}	|Colección con la propiedad de identificador único con el valor {_rid-coll}.
|/dbs/{_rid-db}/users/	|Fuente de usuarios en una base de datos. 
|/dbs/{_rid-db}/users/{_rid-user}	|Usuario con la propiedad de identificador único con el valor {_rid-user}.
|/dbs/{_rid-db}/users/{_rid-user}/permissions	|Fuente de permisos en una base de datos. 
|/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission}	|Permiso con la propiedad de identificador único con el valor {_rid-permission}.  
  
Un recurso también tiene un nombre único definido por el usuario expuesto a través de la propiedad del identificador del recurso. El identificador es una cadena definida por el usuario formada por 256 caracteres como máximo y que es única dentro del contexto de un recurso principal específico. Por ejemplo, el valor de la propiedad del identificador de todos los documentos en una recopilación dada es único, pero no está garantizado para que sea único en otras recopilaciones. De igual manera, el valor de la propiedad del identificador de todos los permisos de un usuario dado es único, pero no está garantizado para que sea único en todos los usuarios. La propiedad _rid se usa para construir el vínculo direccionable _self de un recurso.   

Cada recurso tiene también un identificador de recursos jerárquico generado por el sistema (también denominado RID), que está disponible a través de la propiedad _rid. El RID codifica toda la jerarquía de un recurso dado y es una representación interna muy conveniente que se usa para imponer una integridad referencial de manera distribuida. El RID es único en una cuenta de base de datos y se usa internamente a través de la DocumentDB para un enrutamiento eficaz, sin necesidad de búsquedas en las particiones. 

Los valores de las propiedades _self y _rid son representaciones alternativas y canónicas de un recurso.  

##Cuentas de la base de datos
Puede aprovisionar una o más cuentas de la base de datos de DocumentDB mediante su suscripción a Azure. Para su cuenta de base de datos, puede adquirir unidades apilables de almacenamiento de documentos con respaldo SSD y capacidad de proceso en términos de unidades de capacidad (CU), tal y como se muestra en el siguiente diagrama.    

![][2] 

Según su escala de aplicaciones y necesidades de rendimiento, puede agregar o eliminar CU de forma incremental. Cada CU viene con un conjunto de colecciones elásticas, almacenamiento de documentos aprovisionado respaldado de SSD y capacidad de proceso aprovisionada. El almacenamiento y la capacidad de proceso aprovisionados asociados con una CU se distribuyen en todas las colecciones de la DocumentDB que cree en diferentes bases de datos de su cuenta de base de datos. La capacidad aprovisionada con una cuenta de base de datos está disponible para todas las bases de datos y colecciones que existen o se han creado dentro de la cuenta. Prácticamente no hay límite de escala en el tamaño de una cuenta de base de datos; se puede agregar cualquier número de unidades de capacidad con el tiempo en función de las restricciones de la oferta. Los recursos gestionados en una CU se escalan mediante particiones y se replican para obtener una alta disponibilidad. 

Puede [crear y administrar cuentas de base de datos de DocumentDB](documentdb-create-account.md) mediante el portal de Azure en [http://portal.azure.com/](http://portal.azure.com/). Crear y controlar una cuenta de base de datos requiere acceso administrativo y solo se puede realizar con su suscripción a Azure. 

###Propiedades de la cuenta de base de datos
Como parte del aprovisionamiento y control de una cuenta de base de datos, puede configurar y leer las siguientes propiedades:  

<table border="1" cellspacing="0" cellpadding="0" > 
<tbody>
<tr>
<td valign="top" ><p><b>Nombre de la propiedad</b></p></td>
<td valign="top" ><p><b>Descripción</b></p></td>
</tr>

<tr>
<td valign="top" ><p>Directiva de coherencia</p></td>
<td valign="top" ><p>Establezca esta propiedad para configurar el nivel de coherencia predeterminado para todas las colecciones con su cuenta de base de datos. Puede reemplazar el nivel de coherencia en función de la solicitud mediante el encabezado de solicitud [x-ms-consistency-level]. En el futuro, es posible que pueda reemplazar el nivel de coherencia en función de la colección. </p>

<p>Tenga en cuenta que esta propiedad solo se aplica a los <i>recursos definidos por el usuario</i>. Todos los recursos definidos por el sistema se configuran para que sean compatibles con lecturas/consultas con alta coherencia.</p></td>
</tr>

<tr>
<td valign="top" ><p>Clave principal y clave secundaria</p></td>
<td valign="top" ><p>Las claves principales y secundarias proporcionan acceso administrativo a todos los recursos de la cuenta de base de datos.</p></td>
</tr>

<tr>
<td valign="top" ><p>MaxMediaStorageUsageInMB (LECTURA)</p></td>
<td valign="top" ><p>Cantidad máxima de almacenamiento multimedia disponible para la cuenta de base de datos</p></td>
</tr>

<tr>
<td valign="top" ><p>CurrentMediaStorageUsageInMB (LECTURA)</p></td>
<td valign="top" ><p>Uso actual de almacenamiento multimedia para la cuenta de base de datos</p></td>
</tr>

<tr>
<td valign="top" ><p>CapacityUnitsProvisioned</p></td>
<td valign="top" ><p>El número total de unidades de capacidad aprovisionadas en la cuenta de base de datos</p></td>
</tr>

<tr>
<td valign="top" ><p>CapacityUnitsConsumed (LECTURA)</p></td>
<td valign="top" ><p>El número total de unidades de capacidad que se están consumiendo actualmente en la cuenta de base de datos</p></td>
</tr>

<tr>
<td valign="top" ><p>ProvisionedDocumentStorageInMB (LECTURA)</p></td>
<td valign="top" ><p>La cantidad total de almacenamiento de documentos (en MB) aprovisionado en todas las unidades de capacidad de la cuenta de base de datos</p></td>
</tr>

<tr>
<td valign="top" ><p>ReservedDocumentStorageInMB (LECTURA)</p></td>
<td valign="top" ><p>La cantidad total de almacenamiento de documentos (en MB) reservado en todas las unidades de capacidad de la cuenta de base de datos. El almacenamiento de documentos se supone que se reserva cuando una colección contiene documentos. </p></td>
</tr>

<tr>
<td valign="top" ><p>ConsumedDocumentStorageInMB (LECTURA)</p></td>
<td valign="top" ><p>La cantidad total de almacenamiento de documentos (en MB) que se consume actualmente en todas las unidades de capacidad de la cuenta de base de datos. Este es el tamaño de sus documentos e índice reales combinados en las diferentes colecciones en una cuenta de base de datos.</p></td>
</tr>

</tbody>
</table>

Tenga en cuenta que, además del aprovisionamiento, la configuración y la administración de su cuenta de base de datos desde el portal de Azure, también puede crear y administrar mediante programación cuentas de base de datos de DocumentDB mediante las [API REST de Microsoft Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx), así como los [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx).  

##Bases de datos
Una base de datos de DocumentDB es un contenedor lógico de una o varias colecciones o usuarios, como se muestra en el diagrama siguiente. Puede crear cualquier número de bases de datos en la cuenta de base de datos de DocumentDB en función de los límites de la oferta.  

![][3]  
**Una base de datos es un contenedor lógico de usuarios y recopilaciones**

Una base de datos puede contener almacenamiento de documentos prácticamente ilimitado particionado por colecciones, que forman los dominios de transacción para los documentos que las contienen. 

###Escala elástica de una base de datos de DocumentDB
Una base de datos de la DocumentDB es elástica de forma predeterminada, desde unos pocos GB hasta petabytes de almacenamiento de documentos respaldado y capacidad de proceso aprovisionada de SSD. 

A diferencia de una base de datos en RDBMS tradicional, una base de datos en la DocumentDB no está dirigida a un único equipo. Con la DocumentDB, a medida que aumenten sus necesidades de escala de la aplicación, podrá crear más colecciones, bases de datos o ambas. De hecho, varias aplicaciones propias dentro de Microsoft han utilizado la DocumentDB a nivel de consumidor creando bases de datos de la DocumentDB extremadamente grandes conteniendo cada una miles de colecciones con terabytes de almacenamiento de documentos. Puede ampliar o reducir una base de datos agregando o eliminando colecciones para cumplir con los requisitos de escala de su aplicación. 

Puede crear cualquier número de colecciones dentro de una base de datos en función de la oferta y de la cantidad de unidades de capacidad que compre. El almacenamiento respaldado y la capacidad de proceso aprovisionada de SSD para usted mediante la compra de unidades de capacidad, se puede propagar en todas las colecciones de las bases de datos de su cuenta de base de datos.   

Una base de datos de la DocumentDB también es un contenedor de usuarios. Un usuario, a su vez, es un espacio de nombres lógico para un conjunto de permisos que proporciona autorización y acceso detallado a colecciones, documentos y datos adjuntos.  
 
Como con otros recursos en el modelo de recursos de DocumentDB, las bases de datos se pueden crear, reemplazar, eliminar, leer o enumerar fácilmente mediante las [API REST de Microsoft Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquier [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). La DocumentDB garantiza una gran coherencia para leer o consultar los metadatos de un recurso de base de datos. Eliminar una base de datos automáticamente garantiza que no puede obtener acceso a ninguna colección o usuario contenidos en la misma. Incluso aunque la DocumentDB reclame el almacenamiento y capacidad de proceso aprovisionada como parte de la base de datos eliminada en segundo plano, tanto el almacenamiento como la capacidad de proceso aprovisionada de la base de datos eliminada estarán disponibles inmediatamente para su uso.   

##Colecciones
Una colección de la DocumentDB es un contenedor de sus documentos JSON. Una colección también es una unidad de escala para transacciones y consultas. Puede escalar horizontalmente una base de datos de la DocumentDB agregando más colecciones. Si su aplicación necesita escalar más, puede aprovisionar más almacenamiento de documentos respaldado por SSD (y capacidad de proceso) y distribuirlo por todas las colecciones de una o más bases de datos de su cuenta de base de datos.
 
###Almacenamiento de documentos respaldado con SSD elástico
Una colección es elástica de forma intrínseca; crece y se reduce automáticamente a medida que agrega o elimina documentos. Aunque el uso propio de la DocumentDB se ha probado con miles de colecciones de una base de datos, variando entre ellas desde unos pocos gigabytes a terabytes de tamaño, la oferta de Versión preliminar estándar de la DocumentDB actualmente limita la elasticidad de una colección determinada a 10 GB. 

###Indexación automática de las colecciones
La DocumentDB es un sistema de bases de datos verdadero libre de esquemas. No asume ni requiere ningún esquema para los documentos JSON. A medida que agrega documentos a una colección, la DocumentDB los indexa automáticamente y están disponibles para que los consulte. La indexación automática de documentos sin requerir esquema o índices secundarios es una capacidad clave de la DocumentDB y se habilita mediante técnicas de mantenimiento de índices de escritura optimizada, sin bloqueo y estructuradas por registros. La DocumentDB es compatible con un volumen constante de operaciones de escritura muy rápidas mientras sigue dando servicio a consultas consistentes. Tanto el almacenamiento de documentos como de índices se utilizan para calcular el almacenamiento consumido por cada colección. Puede controlar el equilibrio entre almacenamiento y rendimiento asociado con la indexación configurando la directiva de indexación de una colección. 

###Configuración de la directiva de indexación de una colección
La directiva de indexación de cada colección le permite realizar equilibrios entre rendimiento y almacenamiento relacionados con la indexación. Tiene las siguientes opciones disponibles como parte de la configuración de indexación:  

-	Seleccione si la colección indexa o no todos los documentos automáticamente. De forma predeterminada, se indexan todos los documentos automáticamente. Puede seleccionar desactivar la indexación automática y agregar únicamente documentos de forma selectiva al índice. Al contrario, puede seleccionar de forma selectiva excluir únicamente documentos específicos. Puede lograr esto estableciendo la propiedad automática en verdadera o falsa en indexingPolicy de una colección y utilizando el encabezado de solicitud [x-ms-indexingdirective] al insertar, reemplazar o eliminar un documento.  
-	Seleccione si incluir o excluir rutas o patrones específicos del índice en sus documentos. Puede lograr esto configurando includedPaths y excludedPaths en el indexingPolicy de una colección, respectivamente. También puede configurar los equilibrios de almacenamiento y rendimiento para un intervalo y hash de consultas de patrones de ruta específicos. 
-	Seleccione entre actualizaciones de índice sincrónica (consistente) y asincrónica (diferida). De forma predeterminada, el índice se actualiza de forma sincrónica con cada inserción, reemplazo o eliminación de un documento de la colección. Esto permite a las consultas respetar el mismo nivel de coherencia que el de las lecturas de documentos. Aunque DocumentDB está optimizada para escrituras y admite volúmenes sostenidos de escrituras de documentos junto con un mantenimiento sincrónico de índices y un servicio de consultas coherentes, se pueden configurar determinadas colecciones para actualizar el índice de manera diferida. La indexación diferida aumenta más el rendimiento de escritura y es ideal para casos de ingesta en bloque de colecciones principales con mucha carga de lectura.

En la versión preliminar de la DocumentDB, la directiva de indexación solo se puede configurar durante la creación de una colección. Una vez que se haya creado la colección, no se podrá actualizar la directiva.

###Consulta de una colección
Los documentos de una colección pueden tener esquemas arbitrarios y puede consultar documentos de una colección sin proporcionar por adelantando ningún esquema o índice secundario. Puede consultar la colección mediante el [lenguaje de consulta SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx), que proporciona grandes operadores jerárquicos y relacionales y extensibilidad mediante las UDF basadas en JavaScript. La gramática de JSON permite el modelado de documentos JSON como árboles con etiquetas como los nodos del árbol. Esto lo aprovechan tanto las técnicas de indexación automática de DocumentDB, así como el dialecto de consultas SQL de la DocumentDB. El lenguaje de consulta de DocumentDB consta de tres aspectos principales:   

1.	Un pequeño conjunto de operaciones de consulta que se asignan de forma natural a la estructura de árbol, incluiidas las consultas jerárquicas y las proyecciones. 
2.	Un subconjunto de operaciones relacionales incluyendo composición, filtro, proyecciones, agregados y autocombinaciones. 
3.	UDF basadas puramente en JavaScript que se funcionan con (1) y (2)  

El modelo de consulta de la DocumentDB intenta lograr un equilibrio entre funcionalidad, eficacia y simplicidad. El motor de la base de datos de DocumentDB compila y ejecuta instrucciones de consultas SQL de forma nativa. Puede consultar una colección mediante las [API REST de Microsoft Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquier [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). El SDK de .NET viene con un proveedor LINQ. En versiones futuras, se proporcionarán asignaciones con subrayado nativas que se podrán utilizar desde el [SDK de JavaScript de DocumentDB](https://github.com/Azure/azure-documentdb-js), así como con los procedimientos almacenados y los desencadenadores del servidor.   

###Transacciones de documentos múltiples
Las transacciones de la base de datos proporcionan un modelo de programación seguro y predecible para tratar cambios concurrentes en los datos. En RDBMS, la forma tradicional de escribir lógica empresarial es escribir **procedimientos almacenados** o **desencadenadores** y enviarlos al servidor de la base de datos para la ejecución transaccional. En RDBMS, es necesario el programador de aplicaciones para tratar con dos lenguajes de programación dispares: 

- El lenguaje de programación de aplicaciones (no transaccional), por ejemplo, JavaScript, Python, C#, Java, etc. 
- T-SQL, el lenguaje de programación transaccional que se ejecuta la base de datos de forma nativa.

Debido a su profundo compromiso con JavaScript y JSON directamente con el motor de la base de datos, la DocumentDB proporciona un modelo de programación intuitivo para ejecutar lógica de aplicaciones basada en JavaScript directamente en las colecciones en términos de procedimientos almacenados y desencadenadores. Esto permite realizar las dos acciones siguientes:

- Implementación eficaz del control simultáneo, la recuperación, la indexación automática de los gráficos de objeto JSON directamente en el motor de la base de datos. 
- Expresión de forma natural del flujo de control, el ámbito de las variables, la asignación y la integración de primitivos de control de excepciones con transacciones de bases de datos directamente en términos del lenguaje de programación de JavaScript.   

La lógica de JavaScript registrada a nivel de colección puede entonces emitir operaciones de base de datos en los documentos de la colección dada. La DocumentDB ajusta los procedimientos almacenados y desencadenadores basados en JavaScript dentro de transacciones ACID ambientales con un aislamiento de instantáneas en los documentos de una colección. Durante el transcurso de esta ejecución, si JavaScript lanza una excepción, entonces se cancela toda la transacción. El modelo de programación resultante es muy sencillo aunque eficaz. Los desarrolladores de JavaScript obtienen un modelo de programación "duradero" mientras siguen utilizando sus construcciones de lenguaje y primitivos de biblioteca familiares.   

La capacidad de ejecutar JavaScript directamente en el motor de la base de datos en el mismo espacio de dirección que el grupo de búferes, permite la ejecución transaccional y con mayor rendimiento de las operaciones de base de datos en los documentos de una colección. Además, debido a que el motor de base de datos de la DocumentDB tiene un fuerte compromiso con JSON y JavaScript, elimina cualquier error de coincidencia de impedancia entre los sistemas tipo de la aplicación y la base de datos.   

Tras crear una colección, puede registrar procedimientos almacenados, desencadenadores y UDF con una colección mediante las [API REST de Microsoft Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o cualquier [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). Tras el registro, puede hacer referencia a los mismos y ejecutarlos. Tenga en cuenta el siguiente procedimiento almacenado escrito completamente en JavaScript, toma dos argumentos (nombre del libro y nombre del autor) y crea un nuevo documento, consulta un documento y lo actualiza; todo bajo los auspicios de una transacción ACID implícita. En cualquier punto de la ejecución, si se lanza una excepción de JavaScript, se cancelará toda la transacción.

	function businessLogic(name, author) {
	    var context = getContext();
	    var collectionManager = context.getCollection();        
	    var collectionLink = collectionManager.getSelfLink()
	        
	    // create a new document.
	    collectionManager.createDocument(collectionLink,
	        {id: name, author: author},
	        function(err, documentCreated) {
	            if(err) throw new Error(err.message);
	            
	            // filter documents by author
	            var filterQuery = "SELECT * from root r WHERE r.author = 'George R.'";
	            collectionManager.queryDocuments(collectionLink,
	                filterQuery,
	                function(err, matchingDocuments) {
	                    if(err) throw new Error(err.message);
	                    
	                    context.getResponse().setBody(matchingDocuments.length);
	                   
	                    // Replace the author name for all documents that satisfied the query.
	                    for (var i = 0; i < matchingDocuments.length; i++) {
	                        matchingDocuments[i].author = "George R. R. Martin";
	                        // we don't need to execute a callback because they are in parallel
	                        collectionManager.replaceDocument(matchingDocuments[i]._self,
	                            matchingDocuments[i]);   
	                    }
	                })
	        })
	};

El cliente puede "enviar" la lógica de JavaScript anterior a la base de datos para la ejecución transaccional mediante POST HTTP. Para obtener más información acerca del uso de los métodos HTTP, consulte [Interacciones RESTful con recursos de DocumentDB](documentdb-interactions-with-resources.md). 

	client.createStoredProcedureAsync(collection._self, {id: "CRUDProc", body: businessLogic})
	   .then(function(createdStoredProcedure) {
	        return client.executeStoredProcedureAsync(createdStoredProcedure.resource._self,
	            "NoSQL Distilled",
	            "Martin Fowler");
	    })
	    .then(function(result) {
	        console.log(result);
	    },
	    function(error) {
	        console.log(error);
	    });


Tenga en cuenta que debido a que la base de datos comprende JSON y JavaScript de forma nativa, no existe ningún de error de coincidencia del sistema de tipo, no es necesaria ninguna "asignación OR" ni magia de generación de códigos.   

Los procedimientos almacenados y desencadenadores interactúan con una colección y los documentos de la misma a través de un modelo de objetos bien definido que se expone al contexto de colección actual.  

Las colecciones de DocumentDB se pueden crear, eliminar, leer o enumerar fácilmente con las [API REST de Microsoft Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) o con cualquier [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). La DocumentDB siempre proporciona una alta coherencia para leer o consultar los metadatos de una colección. Eliminar una colección automáticamente garantiza que no puede obtener acceso a ningún documento, dato adjunto, procedimiento almacenado, desencadenador o UDF contenido en la misma. Incluso aunque la DocumentDB reclame el almacenamiento y la capacidad de proceso aprovisionada como parte de la colección eliminada en segundo plano, tanto el almacenamiento como la capacidad de proceso aprovisionada para la colección eliminada estarán de nuevo disponibles al instante para su uso.   
 
##Procedimientos almacenados, desencadenadores y UDF
Como se describe en la sección anterior, puede escribir lógica de aplicación para ejecutarla directamente en una transacción dentro del motor de base de datos. La lógica de la aplicación se puede escribir totalmente en JavaScript y se puede modelar como un procedimiento almacenado, desencadenador o UDF. El código de JavaScript de un procedimiento almacenado o desencadenador puede insertar, reemplazar, eliminar, leer o consultar documentos de una colección. Por otro lado, el JavaScript dentro de una UDF solo puede realizar computación sin efectos secundarios enumerando los documentos del conjunto de resultados de la consulta y producir otro conjunto de resultados. Para los servicios multiinquilino, DocumentDB impone una estricta reserva basada en la gobernanza de los recursos. Cada procedimiento almacenado, desencadenador o UDF obtiene un cuanto de recursos del sistema operativo para realizar su trabajo. Además, los procedimientos almacenados, desencadenadores o UDF no se pueden vincular con bibliotecas externas de JavaScript y están en la lista negra si superan el presupuesto de recursos que se les ha asignado. Puede registrar y anular el registro de los procedimientos almacenados, los desencadenadores o las UDF de una colección mediante las API REST.  Tras el registro de un procedimiento almacenado, desencadenador o UDF, se compila de forma previa y almacena como código byte que se ejecutará más tarde. La siguiente sección ilustra cómo puede utilizar el SDK de JavaScript de DocumentDB para registrar, ejecutar o anular el registro de un procedimiento almacenado, un desencadenador o una UDF. El SDK de JavaScript es un contenedor sencillo de las [API REST de DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx). 

###Registro de un procedimiento almacenado
El registro de un procedimiento almacenado crea un nuevo recurso de procedimiento almacenado en una colección mediante POST HTTP.  

	var storedProc = {
	    id: "validateAndCreate",
	    body: function (documentToCreate) {
	        documentToCreate.id = documentToCreate.id.toUpperCase();
	        
	        var collectionManager = getContext().getCollection();
	        collectionManager.createDocument(collectionManager.getSelfLink(),
	            documentToCreate,
	            function(err, documentCreated) {
	                if(err) throw new Error('Error while creating document: ' + err.message;
	                getContext().getResponse().setBody('success - created ' + 
	                        documentCreated.name);
	            });
	    }
	};
	
	client.createStoredProcedureAsync(collection._self, storedProc)
	    .then(function (createdStoredProcedure) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

###Ejecución de un procedimiento almacenado
La ejecución de un procedimiento almacenado se realiza emitiendo un POST HTTP en un recurso de procedimiento almacenado existente pasando parámetros al procedimiento en el cuerpo de la solicitud.

	var inputDocument = {id : "document1", author: "G. G. Marquez"};
	client.executeStoredProcedureAsync(createdStoredProcedure.resource._self, inputDocument)
	    .then(function(executionResult) {
	        assert.equal(executionResult, "success - created DOCUMENT1");
	    }, function(error) {
	        console.log("Error");
	    });

###Unregistering a stored procedure
Anular el registro de un procedimiento almacenado es sencillo mediante la emisión de un DELETE HTTP en un recurso de procedimiento almacenado existente.   

	client.deleteStoredProcedureAsync(createdStoredProcedure.resource._self)
	    .then(function (response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });


###Registro de un desencadenador previo
El registro de un desencadenador se realiza creando un nuevo recurso de desencadenador en una colección mediante POST HTTP. Puede especificar si el desencadenador es previo o posterior y el tipo de operación con la que se puede asociar; por ejemplo, Crear, Reemplazar, Eliminar o Todas.   

	var preTrigger = {
	    id: "upperCaseId",
	    body: function() {
	            var item = getContext().getRequest().getBody();
	            item.id = item.id.toUpperCase();
	            getContext().getRequest().setBody(item);
	    },
	    triggerType: TriggerType.Pre,
	    triggerOperation: TriggerOperation.All
	}
	
	client.createTriggerAsync(collection._self, preTrigger)
	    .then(function (createdPreTrigger) {
	        console.log("Successfully created trigger");
	    }, function(error) {
	        console.log("Error");
	    });

###Ejecución de un desencadenador previo
La ejecución de un desencadenador se realiza especificando el nombre de un desencadenador existente en el momento de emitir la solicitud POST/PUT/DELETE de un recurso de documento mediante el encabezado de solicitud.  
 
	client.createDocumentAsync(collection._self, { id: "doc1", key: "Love in the Time of Cholera" }, { preTriggerInclude: "upperCaseId" })
	    .then(function(createdDocument) {
	        assert.equal(createdDocument.resource.id, "DOC1");
	    }, function(error) {
	        console.log("Error");
	    });

###Anulación del registro de un desencadenador previo
Anular el registro de un desencadenador es sencillo mediante la emisión de un DELETE HTTP en un recurso de desencadenador existente.  

	client.deleteTriggerAsync(createdPreTrigger._self);
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

###Registro de una UDF
El registro de una UDF se realiza creando un nuevo recurso de UDF en una colección mediante POST HTTP.  

	var udf = { 
	    id: "mathSqrt",
	    body: function(number) {
	            return Math.sqrt(number);
	    },
	};
	client.createUserDefinedFunctionAsync(collection._self, udf)
	    .then(function (createdUdf) {
	        console.log("Successfully created stored procedure");
	    }, function(error) {
	        console.log("Error");
	    });

###Ejecución de una UDF como parte de la consulta
Una UDF se puede especificar como parte de la consulta SQL y se utiliza como una forma de ampliar el núcleo del [lenguaje de consulta SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx).   

	var filterQuery = "SELECT mathSqrt(r.Age) AS sqrtAge FROM root r WHERE r.FirstName='John'";
	client.queryDocuments(collection._self, filterQuery).toArrayAsync();
	    .then(function(queryResponse) {
	        var queryResponseDocuments = queryResponse.feed;
	    }, function(error) {
	        console.log("Error");
	    });

###Anulación del registro de una UDF 
Anular el registro de una UDF es sencillo mediante la emisión de un DELETE HTTP en un recurso de UDF existente.  

	client.deleteUserDefinedFunctionAsync(createdUdf._self)
	    .then(function(response) {
	        return;
	    }, function(error) {
	        console.log("Error");
	    });

Aunque los fragmentos de código anteriores mostraban el registro (POST), anulación del registro (PUT), lectura/enumeración (GET) y ejecución (POST) mediante el [SDK de JavaScript de DocumentDB](https://github.com/Azure/azure-documentdb-js), también puede utilizar las [API REST](https://msdn.microsoft.com/library/azure/dn781481.aspx) u otro [SDK de cliente](https://msdn.microsoft.com/library/azure/dn781482.aspx). 

##Documentos
Puede insertar, reemplazar, eliminar, leer, enumerar y consultar documentos JSON arbitrarios de una colección. La DocumentDB no impone ningún esquema y no requiere índices secundarios para ser compatible con las consultas de documentos en una colección.   

Al ser un servicio de base de datos totalmente abierto, la DocumentDB no crea ningún tipo de datos especializados, como fecha y hora, o codificaciones específicas para documentos JSON. Tenga en cuenta que la DocumentDB no necesita ninguna convención JSON especial para codificar las relaciones entre varios documentos; el lenguaje de consulta SQL de la DocumentDB proporciona operadores de consulta jerárquica y relacional muy eficaz para consultar y proyectar documentos sin ninguna anotación especial o necesidad de codificar relaciones entre documentos utilizando propiedades importantes.  
 
Como con el resto de recursos, se pueden crear, reemplazar, eliminar, leer, enumerar y consultar documentos fácilmente mediante las API REST o con cualquier SDK de cliente. Al eliminar un documento se libera inmediatamente la cuota correspondiente a todos los datos adjuntos anidados. El nivel de coherencia de lectura de documentos sigue la Directiva de coherencia de la cuenta de base de datos. Esta directiva se puede reemplazar en función de la solicitud dependiendo de los requisitos de coherencia de datos de su aplicación. Al consultar documentos, la coherencia de lectura sigue el modo de indexación establecido en la colección. Para ser "coherente", sigue la Directiva de coherencia de la cuenta. 

##Datos adjuntos y multimedia
La DocumentDB le permite almacenar blobs binarios/multimedia en la misma DocumentDB o en su almacén multimedia remoto. También le permite representar los metadatos de un archivo multimedia en términos de un documento especial llamado dato adjunto. Un dato adjunto en la DocumentDB es un documento especial (JSON) que hace referencia al archivo multimedia/blob almacenado en cualquier parte. Un dato adjunto es sencillamente un documento especial que captura los metadatos (por ejemplo, ubicación, autor, etc.) de un archivo multimedia almacenado en un almacenamiento multimedia remoto. 

Considere una aplicación de lectura social que utilice la DocumentDB para almacenar anotaciones manuscritas y metadatos como comentarios, texto resaltado, marcadores, clasificaciones, gustos y preferencias, etc., asociados a un libro electrónico o usuario determinado.   

-	El contenido del libro mismo se almacena en el almacenamiento multimedia disponible como parte de la cuenta de la DocumentDB o de un almacén multimedia remoto. 
-	Una aplicación puede almacenar los metadatos de cada usuario como un documento distinto; por ejemplo, los metadatos de Joe para book1 se almacenan en un documento con la referencia /colls/joe/docs/book1. 
-	Los datos adjuntos que señalan páginas de contenido de un libro de un usuario se almacenan en el documento correspondiente; por ejemplo, /colls/joe/docs/book1/chapter1, /colls/joe/docs/book1/chapter2 etc. 

Tenga en cuenta que los ejemplos usan identificadores sencillos para transmitir la jerarquía de recursos. Se obtiene acceso a los recursos mediante las API REST a través de los identificadores de recurso únicos. 

Para los archivos multimedia gestionados por la DocumentDB, la propiedad _media del dato adjunto hará referencia al archivo multimedia por su URI. La DocumentDB garantizará la recolección de archivos multimedia no utilizados cuando se anulen todas las referencias pendientes. La DocumentDB genera automáticamente el dato adjunto cuando carga nuevos archivos multimedia y completa _media para señalar al nuevo archivo multimedia agregado. Si selecciona almacenar el archivo multimedia en un almacén blob remoto que lo gestiona usted (por ejemplo, OneDrive, Azure Storage, DropBox etc), puede seguir utilizando los datos adjuntos para hacer referencia a los archivos multimedia. En este caso, creará los datos adjuntos usted mismo y rellenará la propiedad _media.   

Como con el resto de recursos, se pueden crear, reemplazar, eliminar, leer o enumerar datos adjuntos fácilmente mediante las API REST o con cualquier SDK de cliente. Como con los documentos, el nivel de coherencia de lectura de datos adjuntos sigue la Directiva de coherencia en la cuenta de base de datos. Esta directiva se puede reemplazar en función de la solicitud dependiendo de los requisitos de coherencia de datos de su aplicación. Al consultar datos adjuntos, la coherencia de lectura sigue el modo de indexación establecido en la colección. Para ser "coherente", sigue la Directiva de coherencia de la cuenta. 
 
##Usuarios
Un usuario de DocumentDB representa un espacio de nombres lógico para agrupar permisos. Un usuario de DocumentDB puede corresponder a un usuario en un sistema administración de identidades o a un rol de aplicaciones predefinido. Para la DocumentDB, un usuario simplemente representa una abstracción para agrupar un conjunto de permisos de una base de datos.   

Para implementar servicios multiinquilino en su aplicación, puede crear usuarios en la DocumentDB que correspondan a sus usuarios o inquilinos de la aplicación. Entonces puede crear permisos para un usuario determinado que se corresponda con el control de acceso de varias colecciones, documentos, datos adjuntos, etc.   

A medida que sus aplicaciones necesiten escalar con el crecimiento de su usuario, puede adoptar varias formas de particionar sus datos. Puede modelar cada usuario de la siguiente forma:   

-	Cada usuario se asigna a una base de datos.
-	Cada usuario se asigna a una colección. 
-	Los documentos que corresponden a varios usuarios van a una colección dedicada. 
-	Los documentos que corresponden a varios usuarios van a un conjunto de colecciones.   

Independientemente de la estrategia de partición específica que seleccione, puede modelar a los usuarios reales como usuarios de base de datos de la DocumentDB y asociar permisos detallados a cada usuario.  

![][4]  
**Estrategias de partición y modelado de usuarios**

Como con el resto de recursos, los usuarios de la DocumentDB se pueden crear, reemplazar, eliminar, leer o enumerar fácilmente mediante las API REST o con cualquier SDK de cliente. La DocumentDB siempre proporciona una alta coherencia para leer o consultar los metadatos de un recurso de usuario. Es importante señalar que eliminar un usuario garantiza automáticamente que no podrá obtener acceso a ningún permiso contenido en el mismo. Incluso aunque la DocumentDB reclame la cuota de permisos como parte del usuario eliminado en segundo plano, los permisos eliminados estarán de nuevo disponibles al instante para su uso.  

##Permisos
Desde la perspectiva de control de acceso, los recursos como las cuentas de bases de datos, las bases de datos, los usuarios y los permisos se consideran recursos *administrative* dado que estos requieren permisos administrativos. Por otro lado, los recursos que incluyen colecciones, documentos, datos adjuntos, procedimientos almacenados, desencadenadores y UDF se dirigen a una base de datos dada y se consideran *application resources*. Conforme a los dos tipos de recursos y a los roles que obtienen acceso a estos (a saber, el administrador y usuario), el modelo de autorización define dos tipos de *access keys*: *master key* y *resource key*. La clave maestra forma parte de la cuenta de base de datos y se proporciona al desarrollador (o administrador) que está aprovisionando la cuenta de base de datos. Esta clave maestra tiene semánticas de administrador y se pueden utilizar para autorizar acceso a recursos tanto administrativos como de aplicación. Al contrario, una clave de recurso es una clave de acceso granular que permite el acceso a recursos de aplicación *specific*. Por lo tanto, captura la relación entre el usuario de una base de datos y los permisos que tiene el usuario para un recurso específico (por ejemplo, colección, documento, dato adjunto, procedimiento almacenado, desencadenador o UDF).   

La única forma de obtener una clave de recurso es creando un recurso de permiso para un usuario determinado. Tenga en cuenta que para poder crear o recuperar un permiso, se debe presentar una clave maestra en el encabezado de autorización. Un recurso de permiso está relacionado con el recurso, su acceso y el usuario. Tras crear un recurso de permiso, el usuario solo necesita presentar la clave de recurso asociada para obtener acceso al recurso relevante. Así, una clave de recurso se puede visualizar como una representación lógica y compacta del recurso de permiso.  

Como con el resto de recursos, los permisos de la DocumentDB se pueden crear, reemplazar, eliminar, leer o enumerar fácilmente mediante las API REST o con cualquier SDK de cliente. La DocumentDB siempre proporciona una coherencia para leer o consultar los metadatos de un permiso. 

##Pasos siguientes
Obtenga más información sobre cómo trabajar con recursos usando comandos HTTP en [Interacciones RESTful con recursos de DocumentDB](documentdb-interactions-with-resources.md).


[1]: ./media/documentdb-resources/resources1.png
[2]: ./media/documentdb-resources/resources2.png
[3]: ./media/documentdb-resources/resources3.png
[4]: ./media/documentdb-resources/resources4.png

<!--HONumber=49-->