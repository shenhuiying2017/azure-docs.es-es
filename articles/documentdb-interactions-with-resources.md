<properties title="Interact with DocumentDB resources" pageTitle="Interacción con recursos de Base de datos de documentos | Azure" description="DocumentDB provides client SDKs for .NET, Python, Node.js and JavaScript - all of which are simple wrappers over the underlying REST APIs." metaKeywords="" services="documentdb" solutions="data-management"  authors="bradsev" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/20/2014" ms.author="spelluru" />

#Interacción con recursos de Base de datos de documentos 
La Base de datos de documentos ofrece un modelo de programación RESTful sencillo y abierto en HTTP. En su versión de vista previa, la Base de datos de documentos proporciona SDK de cliente para .NET, Python, Node.js y JavaScript, que son todos contenedores sencillos por sobre las API REST subyacentes. En las versiones futuras, proporcionará también SDK de C++ y Java. Le instamos a crear sus propios SDK para su entorno de programación específico y compartirlo con la comunidad a medida que abrimos nuestras SDK. 

>[AZURE.NOTE] Además, ofrece también un protocolo de TCP sumamente eficaz que también es RESTful en su modelo de comunicación y está disponible a través del SDK de cliente de .NET.  

##Recursos
Las entidades que administra la Base de datos de documentos se consideran **recursos**, que se identifican de manera exclusiva mediante su URI lógico. Los desarrolladores pueden interactuar con los recursos mediante verbos HTTP estándar, encabezados de solicitud/respuesta y códigos de estado. Como se muestra en el siguiente diagrama, el **modelo de recursos** de la Base de datos de documentos consta de un conjunto de recursos en una cuenta de base de datos. Es posible dirigirse a cada uno de ellos mediante una URI lógica y estable. En este documento, al conjunto de recursos se le denomina **fuente**.  

![][1]  

##Modelo de recursos jerárquico en una cuenta de base de datos ##

Como cliente de la Base de datos de documentos, debe empezar por aprovisionar una **cuenta de base de datos** de la Base de datos de documentos mediante su suscripción de Azure. Una cuenta de base de datos consta de un conjunto de **bases de datos**, cada una contiene varias **recopilaciones**, y cada una contiene a la vez **procedimientos almacenados, desencadenadores, UDF, documentos** y **datos adjuntos** relacionados. Una base de datos tiene también **usuarios** asociados, cada uno con un conjunto de **permisos** para tener acceso a diferentes recopilaciones, procedimientos almacenados, desecadenadores, UDF, documentos o datos adjuntos. Aun cuando las bases de datos, los usuarios, los permisos y las recopilaciones son recursos definidos por el sistema con esquemas bien conocidos, los documentos y los datos adjuntos contienen contenido JSON definido por el usuario y arbitrario.  

|Recurso 	|Descripción
|-----------|-----------
|Cuenta de base de datos	|Una cuenta de base de datos está asociada con una o más unidades de capacidad que representan un almacenamiento de documentos aprovisionado y rendimiento, un conjunto de bases de datos y almacenamiento de blobs. Puede crear una o más cuentas de base de datos mediante su suscripción a Azure.
|Base de datos	|Una base de datos es un contenedor lógico de almacenamiento de documentos particionado en recopilaciones. Es también un contenedor de usuarios.
|Usuario	|El espacio de nombres lógico para ámbito/partición de permisos. 
|Permiso	|Un token de autorización asociado con un usuario para el acceso autorizado a un recurso específico.
|Colección	|Una colección es un contenedor de documentos JSON asociado a la lógica de aplicación de JavaScript.
|Procedimiento almacenado	|Lógica de aplicaciones creada en JavaScript que se registra con una recopilación y se ejecuta de manera transaccional en el motor de la base de datos.
|Desencadenador	|Lógica de aplicaciones creada en JavaScript que modela los efectos secundarios asociados con las operaciones de inserción, reemplazo o eliminación.
|UDF	|Una lógica de aplicaciones sin efectos secundarios creada en JavaScript. Las UDF le permiten modelar un operador de consultas personalizado y con ello ampliar el lenguaje de consultas de la Base de datos de documentos central.
|Documento	|Contenido JSON definido por el usuario (arbitrario). De manera predeterminada, no es necesario definir esquemas ni proporcionar índices secundarios para todos los documentos que se agregaron a una recopilación.
|Dato adjunto	|Los datos adjuntos son documentos especiales que contienen referencias y metadatos asociados con blobs/medios externos. El desarrollador puede decidir administrar los blobs a través de la Base de datos de documentos o almacenarlos con un proveedor de servicios de blobs externos como OneDrive, Dropbox, etc. 

###Recursos del sistema frente a los definidos por el usuario
Los recursos como cuentas de bases de datos, las bases de datos, las recopilaciones, los usuarios, los permisos, los procedimientos almacenados, los desencadenadores y las UDF tienen todos un esquema fijo y se les denomina *recursos del sistema*. Por el contrario, los recursos como los documentos y los datos adjuntos no tienen restricciones en los esquemas y son ejemplos de *recursos definidos por el usuario*. En la Base de datos de documentos, tanto los recursos definidos por el sistema como los definidos por el usuario se representan y administran como JSON conforme con la norma. Todos los recursos ya sean definidos por el sistema o el usuario tienen las siguientes propiedades en común.

>[AZURE.NOTE] Tenga en cuenta que todas las propiedades generadas por el sistema en un recurso tienen un prefijo con subrayado (_) en su representación de JSON.  


<table width="500"> 
<tbody>
<tr>
<td valign="top" ><p><b>Propiedad </b></p></td>
<td valign="top" ><p><b>¿Configurable por el usuario o generado por el sistema?</b></p></td>
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
<td valign="top" ><p>URI del recurso única y direccionable </p></td>
</tr>

<tr>
<td valign="top" ><p>id</p></td>
<td valign="top" ><p>Configurable por el usuario</p></td>
<td valign="top" ><p>Nombre del recurso único definido por el usuario </p></td>
</tr>

</tbody>
</table>


###Representación de conexión de los recursos
La Base de datos de documentos no exige ninguna extensión propietaria a la norma JSON ni codificaciones especiales; funciona con documentos JSON según la norma.  
 
###Direccionamiento de un recurso
Todos los recursos se pueden diseccionar mediante URI. El valor de la propiedad **_self** de un recurso representa la URI relativa del recurso. El formato de la URI consta de los segmentos de ruta /<feed>/{_rid}:  

|Value of the _self	|Description
|-------------------|-----------
|/dbs	|feed of databases under a database account
|/dbs/{_rid-db}	|Database with the unique id property with the value {_rid-db}
|/dbs/{_rid-db}/colls/	|feed of collections under a database 
|/dbs/{_rid-db}/colls/{_rid-coll}	|Collection with the unique id property with the value {_rid-coll}
|/dbs/{_rid-db}/users/	|feed of users under a database 
|/dbs/{_rid-db}/users/{_rid-user}	|User with the unique id property with the value {_rid-user}
|/dbs/{_rid-db}/users/{_rid-user}/permissions	|feed of permissions under a database 
|/dbs/{_rid-db}/users/{_rid-user}/permissions/{_rid-permission}	|Permission with the unique id property with the value {_rid-permission}  
  

Un recurso también tiene un nombre único definido por el usuario expuesto a través de la propiedad del identificador del recurso. El identificador es una cadena larga definida por el usuario que tiene hasta 256 caracteres de largo que es única dentro del contexto de un recurso principal específico. Por ejemplo, el valor de la propiedad del identificador de todos los documentos en una recopilación dada es único, pero no está garantizado para que sea único en otras recopilaciones. De igual manera, el valor de la propiedad del identificador de todos los permisos de un usuario dado es único, pero no está garantizado para que sea único en todos los usuarios. La propiedad _rid se usa para construir el vínculo direccionable _self de un recurso.   

Cada recurso tiene también un identificador de recursos jerárquico generado por el sistema (llamado también RID) que está disponible a través de la propiedad _rid. El RID codifica toda la jerarquía de un recurso dado y es una representación interna muy conveniente que se usa para imponer una integridad referencial de manera distribuida. El RID es único en una cuenta de base de datos y se usa internamente a través de la Base de datos de documentos para un enrutamiento eficaz, sin necesidad de búsquedas en las particiones. 

Los valores de las propiedades _self y   _rid son representaciones alternativas y canónicas de un recurso.  
 
##Modelo de interacción básica
Los recursos admiten los siguientes verbos HTTP  con su interpretación estándar:

>[AZURE.NOTE] En el futuro, pretendemos agregar compatibilidad para las actualizaciones selectivas a través de PATCH.  

1.	POST significa crear un nuevo recurso de elementos. 
2.	GET significa leer un elemento existente o un recurso de fuente. 
3.	PUT significa reemplazar un recurso de elementos existentes. 
4.	DELETE significa eliminar un recurso   de elementos existentes.
5.	HEAD significa GET sin la carga de respuesta (es decir, solo los encabezados)

En las versiones futuras, los recursos de elementos admitirán también el verbo PATCH.  

Como se ilustra en la figura a continuación, POST solo puede emitirse contra un recurso de fuentes; PUT, DELETE solo pueden emitirse contra un recurso de elementos; GET y HEAD pueden emitirse contra recursos de fuentes o elementos. 

![][2]  

**Modelo de interacción con los verbos HTTP estándar**

A fin de obtener una mejor percepción para el modelo de interacción, consideremos el caso de crear un recurso nuevo (alias INSERT). Para crear un recurso nuevo, es necesario que emita una solicitud de HTTP POST con el cuerpo de la solicitud que contiene la representación del recurso contra la URI de la fuente del contenedor al que pertenece el recurso. La única propiedad requerida para la solicitud es el identificador del recurso.  

Como ejemplo, para crear una base de datos nueva, usted ENVÍA un recurso de base de datos (al establecer la propiedad de identificador con un nombre único) contra /dbs, de igual manera, para crear una recopilación nueva, ENVÍA un recurso de recopilación contra /dbs/_rid/colls/ y así sucesivamente. La respuesta contendrá el recurso completamente confirmado con las propiedades generadas por el sistema, incluido el vínculo _self del recurso con el que puede navegar a otros recursos.   Como un ejemplo del modelo de interacción sencillo basado en HTTP, un cliente puede emitir una solicitud HTTP para crear una base de datos nueva en una cuenta:

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}
Base de datos de documentos responde con una respuesta correcta y un código de estado que indica una creación correcta de la base de datos.  

	[201 Created]
	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/MyDb/",
	      "_ts": 1407370063,
	      "_etag": "\"00002100-0000-0000-0000-50e71fda0000\"",
	      "_colls": "colls/",
	      "_users": "users/"
	}

El servicio de Base de datos de documentos responde con una respuesta correcta y un código de estado que indica una creación correcta de la base de datos.  

Como otro ejemplo de la creación y ejecución de un recurso, considere el siguiente procedimiento almacenado creado completamente en JavaScript.   

	function sproc(docToCreate, addedPropertyName, addedPropertyValue) {
	    var collectionManager = getContext().getCollection();
	    collectionManager.createDocument(collectionManager.getSelfLink(), docToCreate,   
	    function(err, docCreated) {
	        if(err) throw new Error('Error while creating document: ' + err.message);
	        
	        docCreated.addedPropertyName = addedPropertyValue;
	        getContext().getResponse().setBody(docCreated);
	    });
	}

El procedimiento almacenado se puede registrar en una recopilación de MyDb al emitir un POST contra /dbs/_rid-db/colls/_rid-coll/sprocs. 

	POST /dbs/MyDb/colls/MyColl/sprocs HTTP/1.1
	
	{
	  "id": "sproc1",
	  "body": "function (docToCreate, addedPropertyName, addedPropertyValue) {
	                var collectionManager = getContext().getCollection();
	                collectionManager.createDocument(collectionManager.getSelfLink(), 
	                            docToCreate, function(err, docCreated) {
	                    if(err) throw new Error('Error while creating document: ' + 
	                                                     err.message);
	                    
	                    docCreated.addedPropertyName = addedPropertyValue;
	                    getContext().getResponse().setBody(docCreated);
	                });
	            }"
	}
El servicio de Base de datos de documentos responde con una respuesta correcta y un código de estado que indica el registro correcto del procedimiento almacenado.  

	[201 Created]
	{
	      "id": "sproc1",
	      "_rid": "EoEi5w==",
	      "_self": "dbs/MyDb/colls/MyColl/sprocs/sproc1",
	      "_etag": "\"00002100-0000-0000-0000-50f71fda0000\"",
	       ...
	}

Finalmente, para ejecutar el procedimiento almacenado en el ejemplo anterior, se necesita enviar un POST contra la URI del recurso del procedimiento almacenado (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Esto se ilustra a continuación:  

	POST /dbs/MyDb/colls/MyColl/sprocs/sproc1 HTTP/1.1
	 [ { "id": "TestDocument", "book": "Autumn of the Patriarch"}, "Price", 200 ]
 

El servicio Base de datos de documentos responde con la siguiente respuesta:  

	HTTP/1.1 200 OK
	 { 
	  "id": "TestDocument",  
	  "_rid": "ZTlcANiwqwIBAAAAAAAAAA==",
	  "_ts": 1407370063,
	  "_self": "dbs/ZTlcAA==/colls/ZTlcANiwqwI=/docs/ZTlcANiwqwIBAAAAAAAAAA==/",
	  "_etag": "00000900-0000-0000-0000-53e2c34f0000",
	  "_attachments": "attachments/",
	  "book": "Autumn of the Patriarch", 
	  "price": 200
	}

Tenga el tenga en cuenta que el verbo POST se usa para la creación de un recurso nuevo, para ejecutar un procedimiento almacenado o para emitir una consulta SQL. Para ilustrar la ejecución de la consulta SQL, considere lo siguiente:  

	POST /dbs/MyDb/colls/MyColl/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	Content-Type: application/sql
	
	SELECT * FROM root.children

El servicio responde con los resultados de la consulta SQL.   

	HTTP/1.1 200 Ok
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 81
	x-ms-request-charge: 1.43
	Content-Length: 287
	
	{"_rid":"sehcAIE2Qy4=","Documents":[[{"firstName":"Henriette Thaulow","gender":"female","grade":5,"pets":[{"givenName":"Fluffy"}]}],[{"familyName":"Merriam","givenName":"Jesse","gender":"female","grade":1},{"familyName":"Miller","givenName":"Lisa","gender":"female","grade":8}]],"count":2}


El reemplazo o la lectura de un recurso equivale a emitir los verbos PUT (con un cuerpo de solicitud válido) y GET contra el vínculo _self del recurso, respectivamente. Similarmente, la eliminación de un recurso equivale a emitir un verbo DELETE contra el vínculo _self del recurso. Vale la pena señalar que la organización jerárquica de los recursos en el modelo de recursos de la Base de datos de documentos necesita la compatibilidad con las eliminaciones en cascada, donde la eliminación del recurso del propietario provoca la eliminación de los recursos dependientes. Los recursos dependientes se pueden distribuir a través de otros nodos aparte de los recursos del propietario, de modo que la eliminación puede producirse lentamente. Sin considerar la mecánica de la recopilación de elementos no usados, después de la eliminación de un recurso, la cuota se libera instantáneamente y está disponible para su uso. Tenga en cuenta que el sistema mantiene la integridad referencial. Por ejemplo, no se puede insertar una recopilación en una base de datos que se eliminó ni reemplazar o consultar un documento de una recopilación que ya no existe.  
 
Emitir un GET contra una fuente (de) recursos o consultar una recopilación puede tener como resultado millones de elementos, lo que dificulta que ambos servidores los materialicen y que los clientes los consuman como parte de una sola solicitud roundtrip/ e intercambio de respuestas. Para abordar esto, Base de datos de documentos permite que los clientes paginen en una fuente grande una página a la vez. Los clientes pueden usar el encabezado de respuesta [x-ms-continuationToken] como un cursor para navegar a la página siguiente.   

##Control de concurrencia optimista
La mayoría de las aplicaciones web dependen de una etiqueta de identidad basada en el control de concurrencia optimista para evitar los desastrosos problemas de "Actualización perdida" y "Eliminación perdida". La etiqueta de entidad es una marca de tiempo HTTP lógica y fácil de usar asociada con un recurso. La Base de datos de documentos admite de manera nativa el control de concurrencia optimista al asegurar que toda respuesta HTTP contenga la versión (de manera durable) asociada con el recurso específico. Los conflictos del control de concurrencia se detectan correctamente para los siguientes casos:  

1.	Si los clientes emiten simultáneamente solicitudes mutantes (a través de los verbos PUT/DELETE) en un recurso con la última versión del recurso (especificado a través del encabezado de solicitud [if-match]), el motor de la base de datos de Base de datos de documentos los somete al control de concurrencia transaccional.
2.	Si un cliente se presenta con una versión más antigua del recurso (especificado mediante el encabezado de solicitud [if-match]), la solicitud se rechaza.  

##Opciones de conectividad
La Base de datos de documentos expone un modelo de direccionamiento lógico donde cada recurso tiene una URI lógica y estable identificada por su vínculo _self. Como un sistema de almacenamiento distribuido desplegado en regiones, los recursos de varias cuentas de bases de datos en la Base de datos de documentos se dividen en varias máquinas y cada partición se replica para una mayor disponibilidad. Las réplicas que administran los recursos de una partición dada registran las direcciones físicas. Aun cuando las direcciones físicas cambian en el tiempo debido a errores, sus direcciones lógicas se mantienen estables y constantes. El traslado de la dirección lógica a física se mantiene en una tabla de enrutamiento que también esta disponible de manera interna como un recurso. La Base de datos de documentos expone dos modos de conectividad:  

1.	**Modo de puerta de enlace:** los clientes están protegidos del traslado entre las direcciones lógicas a físicas o los detalles del enrutamiento; simplemente tratan las URI lógicas y navegan de modo RESTful en el modelo de recursos. Los clientes envían las solicitudes mediante la URI lógica y las máquinas perimetrales traducen la URI lógica a la dirección física de la réplica que administra el recurso y lo reenvía a la solicitud. Con el almacenamiento (y la actualización periódica) en caché de las máquinas perimetrales, el enrutamiento es sumamente eficaz. 
2.	**Modo de conectividad directa:** los clientes administran directamente la tabla de enrutamiento en su espacio de proceso y la actualizan periódicamente. El cliente puede conectarse directamente con las réplicas y omitir las máquinas perimetrales.   



<table width="300">
    <tbody>
        <tr>
            <td width="120" valign="top">
                <p>
                    <strong>Modo de conectividad</strong>
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    <strong>Protocol</strong>
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    <strong>Detalles</strong>
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    <strong>SDK de Base de datos de documentos</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Puerta de enlace
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Las aplicaciones se conectan directamente con los nodos perimetrales usando las URI lógicas. Esto significa un salto adicional de la red.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    API de REST
                </p>
                <p>
                    .NET, JavaScript, Node.js, Python
                </p>
            </td>
        </tr>
        <tr>
            <td width="120" valign="top">
                <p>
                    Conectividad directa
                </p>
            </td>
            <td width="66" valign="top">
                <p>
                    HTTPS y
                </p>
                <p>
                    TCP
                </p>
            </td>
            <td width="264" valign="top">
                <p>
                    Las aplicaciones pueden tener acceso de manera directa a la tabla de enrutamiento y realizar el enrutamiento del cliente para que se conecte directamente con las réplicas.
                </p>
            </td>
            <td width="150" valign="top">
                <p>
                    .NET
                </p>
            </td>
        </tr>
    </tbody>
</table>

##Referencias
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Especificación JSON  [http://-www.ietf.org/rfc/rfc4627.txt](http://-www.ietf.org/rfc/rfc4627.txt)
-	Especificación HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Etiquetas de entidad [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Base de datos de documentos de consulta](/documentation/articles/documentdb-sql-query/)
-	[Referencia SQL de Base de datos de documentos](http://go.microsoft.com/fwlink/p/?LinkID=510612)
-	[Programación de la Base de datos de documentos: procedimientos almacenados, desencadenadores y UDF](/documentation/articles/documentdb-programming/)
-	[Documentación de referencia de la Base de datos de documentos](http://go.microsoft.com/fwlink/p/?LinkID=402384)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources1.png
[2]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
