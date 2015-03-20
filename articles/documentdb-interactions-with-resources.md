<properties 
	pageTitle="Interacciones de RESTful con recursos de Base de datos de documentos | Azure" 
	description="Obtenga información acerca de cómo realizar interacciones de RESTful con los recursos de Base de datos de documentos de Microsoft Azure mediante verbos HTTP." 
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
	ms.date="02/03/2015" 
	ms.author="mimig"/>

# Interacciones RESTful con recursos de Base de datos de documentos 

Base de datos de documentos admite el uso de métodos HTTP para crear, leer, reemplazar, obtener y eliminar recursos de esa base de datos.

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo funcionan los métodos HTTP estándar con los recursos de Base de datos de documentos?
- ¿Cómo se puede crear un nuevo recurso mediante POST?
- ¿Cómo se puede registrar un procedimiento almacenado mediante POST?
- ¿Cómo Base de datos de documentos admite el control de monedas?
- ¿Cuáles son las opciones de conectividad para HTTPS y TCP?

## Información general de verbos HTTP
Los recursos de Base de datos de documentos admiten los siguientes verbos HTTP con su interpretación estándar:

1.	POST significa crear un nuevo recurso de elementos. 
2.	GET significa leer un elemento existente o un recurso de fuente. 
3.	PUT significa reemplazar un recurso de elementos existentes. 
4.	DELETE significa eliminar un recurso de elementos existentes.
5.	HEAD significa GET sin la carga de respuesta (es decir, solo los encabezados) 

>[AZURE.NOTE] En el futuro, pretendemos agregar compatibilidad para las actualizaciones selectivas a través de PATCH.  

Como se ilustra en el siguiente diagrama, POST solo puede emitirse contra un recurso de fuentes; PUT y DELETE solo pueden emitirse contra un recurso de elementos; GET y HEAD pueden emitirse contra recursos de fuentes o elementos. 

![][1]  

**Modelo de interacción con los métodos HTTP estándar**

## Creación de un nuevo recurso mediante POST 
A fin de obtener una mejor percepción para el modelo de interacción, consideremos el caso de crear un recurso nuevo (alias INSERT). Para crear un recurso nuevo, es necesario que emita una solicitud de HTTP POST con el cuerpo de la solicitud que contiene la representación del recurso contra la URI de la fuente del contenedor al que pertenece el recurso. La única propiedad requerida para la solicitud es el identificador del recurso.  

Como ejemplo, para crear una base de datos nueva, usted ENVÍA un recurso de base de datos (al establecer la propiedad de identificador con un nombre único) contra /dbs, de igual manera, para crear una recopilación nueva, ENVÍA un recurso de recopilación contra /dbs/_rid/colls/ y así sucesivamente. La respuesta contiene el recurso completamente confirmado con las propiedades generadas por el sistema, incluido el vínculo _self del recurso con el que puede navegar a otros recursos. Como un ejemplo del modelo de interacción sencillo basado en HTTP, un cliente puede emitir una solicitud HTTP para crear una base de datos nueva en una cuenta.  

	POST http://fabrikam.documents.azure.net/dbs
	{
	      "id":"MyDb"
	}

El servicio de Base de datos de documentos responde con una respuesta correcta y un código de estado que indica una creación correcta de la base de datos.  

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
  
## Registro de un procedimiento almacenado mediante POST
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

## Ejecución de un procedimiento almacenado mediante POST
Finalmente, para ejecutar el procedimiento almacenado en el ejemplo anterior, se necesita enviar un POST contra la URI del recurso del procedimiento almacenado (/dbs/_rid-db/colls/_rid-coll/sprocs/sproc1). Esto se muestra en el código siguiente.  

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

Tenga en cuenta que el verbo POST se usa para la creación de un recurso nuevo, para ejecutar un procedimiento almacenado y para emitir una consulta SQL. Para ilustrar la ejecución de la consulta SQL, considere lo siguiente.  

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


## Uso de PUT, GET y DELETE
El reemplazo o la lectura de un recurso equivale a emitir los verbos PUT (con un cuerpo de solicitud válido) y GET contra el vínculo _self del recurso, respectivamente. Similarmente, la eliminación de un recurso equivale a emitir un verbo DELETE contra el vínculo _self del recurso. Vale la pena señalar que la organización jerárquica de los recursos en el modelo de recursos de la Base de datos de documentos necesita la compatibilidad con las eliminaciones en cascada, donde la eliminación del recurso del propietario provoca la eliminación de los recursos dependientes. Los recursos dependientes se pueden distribuir a través de otros nodos aparte de los recursos del propietario, de modo que la eliminación puede producirse lentamente. Sin considerar la mecánica de la recopilación de elementos no usados, después de la eliminación de un recurso, la cuota se libera instantáneamente y está disponible para su uso. Tenga en cuenta que el sistema mantiene la integridad referencial. Por ejemplo, no se puede insertar una recopilación en una base de datos que se eliminó ni reemplazar o consultar un documento de una recopilación que ya no existe.  
 
Emitir un GET contra una fuente de recursos o consultar una recopilación puede tener como resultado millones de elementos, lo que dificulta que ambos servidores los materialicen y que los clientes los consuman como parte de una sola solicitud roundtrip/ e intercambio de respuestas. Para abordar esto, Base de datos de documentos permite que los clientes paginen en una fuente grande una página a la vez. Los clientes pueden usar el encabezado de respuesta [x-ms-continuationToken] como un cursor para navegar a la página siguiente.   

## Control de concurrencia optimista
La mayoría de las aplicaciones web dependen de una etiqueta de identidad basada en el control de concurrencia optimista para evitar los desastrosos problemas de "Actualización perdida" y "Eliminación perdida". La etiqueta de entidad es una marca de tiempo HTTP lógica y fácil de usar asociada con un recurso. La Base de datos de documentos admite de manera nativa el control de concurrencia optimista al asegurar que toda respuesta HTTP contenga la versión (de manera durable) asociada con el recurso específico. Los conflictos del control de concurrencia se detectan correctamente para los siguientes casos:  

1.	Si los clientes emiten simultáneamente solicitudes mutantes (a través de los verbos PUT/DELETE) en un recurso con la última versión del recurso (especificado a través del encabezado de solicitud [if-match]), el motor de la base de datos de Base de datos de documentos los somete al control de concurrencia transaccional.
2.	Si un cliente se presenta con una versión más antigua del recurso (especificado mediante el encabezado de solicitud [if-match]), la solicitud se rechaza.  

## Opciones de conectividad
La Base de datos de documentos expone un modelo de direccionamiento lógico donde cada recurso tiene una URI lógica y estable identificada por su vínculo _self. Como un sistema de almacenamiento distribuido desplegado en regiones, los recursos de varias cuentas de bases de datos en la Base de datos de documentos se dividen en varias máquinas y cada partición se replica para una mayor disponibilidad. Las réplicas que administran los recursos de una partición dada registran las direcciones físicas. Aun cuando las direcciones físicas cambian en el tiempo debido a errores, sus direcciones lógicas se mantienen estables y constantes. El traslado de la dirección lógica a física se mantiene en una tabla de enrutamiento que también esta disponible de manera interna como un recurso. La Base de datos de documentos expone dos modos de conectividad:  

1.	**Modo de puerta de enlace:** los clientes no conocen la traducción entre direcciones lógicas a físicas o los detalles de la ruta; simplemente tratan con los valores URI y RESTfully lógicos para navegar por el modelo de recursos. Los clientes emiten las solicitudes mediante el URI lógico y los equipos perimetrales traducen el valor URI lógico a la dirección física de la réplica que administra el recurso y reenvía la solicitud. Con los equipos perimetrales almacenando en caché (y actualizando periódicamente) la tabla de enrutamiento, el enrutamiento es bastante eficaz.
2.	**Modo de conectividad directa:** los clientes administran directamente la tabla de enrutamiento de su espacio de proceso y la actualizan periódicamente. El cliente puede conectase directamente con las réplicas y omitir los equipos perimetrales.


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
                    <strong>Protocolo</strong>
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

## Pasos siguientes
Consulte la [referencia de la API de REST de Base de datos de documentos de Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx) para obtener más información sobre cómo trabajar con recursos mediante la API de REST.

## Referencias
-   [Referencia de API de REST de Base de datos de documentos de Azure](https://msdn.microsoft.com/library/azure/dn781481.aspx)
-	REST [http://en.wikipedia.org/wiki/Representational_state_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
-	Especificación de JSON  [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
-	Especificación de HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
-	Etiquetas de entidad [http://en.wikipedia.org/wiki/HTTP_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)
-	[Base de datos de documentos de consulta](../documentdb-sql-query/)
-	[Referencia SQL de Base de datos de documentos](https://msdn.microsoft.com/library/azure/dn782250.aspx)
-	[Programación de Base de datos de documentos: Procedimientos almacenados, desencadenadores y UDF](../documentdb-programming/)
-	[Documentación de referencia de Base de datos de documentos](https://msdn.microsoft.com/library/azure/dn781482.aspx)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png

<!--HONumber=47-->
