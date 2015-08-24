<properties 
	pageTitle="Interacciones de RESTful con recursos de DocumentDB | Microsoft Azure" 
	description="Obtenga información acerca de cómo realizar interacciones de RESTful con los recursos de DocumentDB de Microsoft Azure mediante verbos HTTP." 
	services="documentdb" 
	authors="h0n" 
	manager="jhubbard" 
	editor="monicar" 
	documentationCenter=""/>

<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/03/2015" 
	ms.author="h0n"/>

# Interacciones RESTful con recursos de DocumentDB 

DocumentDB admite el uso de métodos HTTP para crear, leer, reemplazar, obtener y eliminar recursos de esa base de datos.

Después de leer este artículo, podrá responder a las preguntas siguientes:

- ¿Cómo funcionan los métodos HTTP estándar con los recursos de DocumentDB?
- ¿Cómo se puede crear un nuevo recurso mediante POST?
- ¿Cómo se puede registrar un procedimiento almacenado mediante POST?
- ¿Cómo DocumentDB admite el control de monedas?
- ¿Cuáles son las opciones de conectividad para HTTPS y TCP?

## Información general de verbos HTTP
Los recursos de DocumentDB admiten los siguientes verbos HTTP con su interpretación estándar:

1.	POST significa crear un nuevo recurso de elementos. 
2.	GET significa leer un elemento existente o un recurso de fuente. 
3.	PUT significa reemplazar un recurso de elementos existentes. 
4.	DELETE significa eliminar un recurso de elementos existentes.
5.	HEAD significa GET sin la carga de respuesta (es decir, solo los encabezados) 

>[AZURE.NOTE]En el futuro, pretendemos agregar compatibilidad para las actualizaciones selectivas a través de PATCH.

Como se ilustra en el siguiente diagrama, POST solo puede emitirse contra un recurso de fuentes; PUT y DELETE solo pueden emitirse contra un recurso de elementos; GET y HEAD pueden emitirse contra recursos de fuentes o elementos.

![][1]

**Modelo de interacción con los métodos HTTP estándar**

## Creación de un nuevo recurso mediante POST 
A fin de obtener una mejor percepción para el modelo de interacción, consideremos el caso de crear un recurso nuevo (alias INSERT). Para crear un recurso nuevo, es necesario que emita una solicitud de HTTP POST con el cuerpo de la solicitud que contiene la representación del recurso contra la URI de la fuente del contenedor al que pertenece el recurso. La única propiedad requerida para la solicitud es el identificador del recurso.

Por ejemplo, para crear una nueva base de datos, REGISTRA un recurso de base de datos (estableciendo la propiedad de id. con un nombre único) contra /dbs. De forma similar, para crear una nueva colección, debe registrar mediante POST un recurso de colección en */dbs/\_rid/colls/* y así sucesivamente. La respuesta contiene el recurso completamente confirmado con las propiedades generadas por el sistema, incluido el vínculo *\_self* del recurso con el que puede navegar a otros recursos. Como un ejemplo del modelo de interacción sencillo basado en HTTP, un cliente puede emitir una solicitud HTTP para crear una base de datos nueva en una cuenta.

```
	POST https://fabrikam.documents.azure.com/dbs
	{
	      "id":"MyDb"
	}

The DocumentDB service responds with a successful response and a status code indicating successful creation of the database.  

	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	      "id": "MyDb",
	      "_rid": "UoEi5w==",
	      "_self": "dbs/UoEi5w==/",
	      "_ts": 1407370063,
	      "_etag": "00000100-0000-0000-0000-54b636600000",
	      "_colls": "colls/",
	      "_users": "users/"
	}
```
  
## Registro de un procedimiento almacenado mediante POST
Como otro ejemplo de la creación y ejecución de un recurso, considere un procedimiento almacenado "HelloWorld" simple creado completamente en JavaScript.

```
 	function HelloWorld() {
 	var context = getContext();
 	var response = context.getResponse();
 	
        response.setBody("Hello, World");
     }
```

El procedimiento almacenado se puede registrar en una recopilación de MyDb al emitir un POST en */dbs/\_rid-db/colls/\_rid-coll/sprocs*.

```
	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs HTTP/1.1
	
	{
	  "id": "HelloWorld",
	  "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }"
	}
```

El servicio de DocumentDB responde con una respuesta correcta y un código de estado que indica el registro correcto del procedimiento almacenado.

```
	HTTP/1.1 201 Created
	Content-Type: application/json
	x-ms-request-charge: 4.95
	...

	{
	       "body": "function HelloWorld() {
	           var context = getContext();
 	           var response = context.getResponse();
 	           
 	           response.setBody("Hello, World");
        	   }",
	      "id": "HelloWorld"
	      "_rid": "UoEi5w+upwABAAAAAAAAgA==",
	      "_ts" :  1421227641
	      "_self": "dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/",
	      "_etag": "00002100-0000-0000-0000-50f71fda0000"
	}
```

## Ejecución de un procedimiento almacenado mediante POST
Por último, para ejecutar el procedimiento almacenado en el ejemplo anterior, se necesita emitir un POST contra el URI del recurso del procedimiento almacenado (/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA==/) como se muestra a continuación.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/sprocs/UoEi5w+upwABAAAAAAAAgA== HTTP/1.1
	
El servicio DocumentDB responde con la siguiente respuesta:

	HTTP/1.1 200 OK
	
	"Hello World"

Tenga en cuenta que el verbo POST se usa para la creación de un recurso nuevo, para ejecutar un procedimiento almacenado y para emitir una consulta SQL. Para ilustrar la ejecución de la consulta SQL, considere lo siguiente.

	POST https://fabrikam.documents.azure.com/dbs/UoEi5w==/colls/UoEi5w+upwA=/docs HTTP/1.1
	...
	x-ms-documentdb-isquery: True
	x-ms-documentdb-query-enable-scan: True
	Content-Type: application/query+json
	...
	
	{"query":"SELECT f.LastName AS Name, f.Address.City AS City FROM Families f WHERE f.id='AndersenFamily' OR f.Address.City='NY'"}

El servicio responde con los resultados de la consulta SQL.

```
	HTTP/1.1 200 Ok
	...
	x-ms-activity-id: 83f9992c-abae-4eb1-b8f0-9f2420c520d2
	x-ms-item-count: 2
	x-ms-session-token: 4
	x-ms-request-charge: 3.1
	Content-Type: application/json1
	...
	{"_rid":"UoEi5w+upwA=","Documents":[{"Name":"Andersen","City":"Seattle"},{"Name":"Wakefield","City":"NY"}],"_count":2}
```


## Uso de PUT, GET y DELETE
Reemplazar o leer un recurso equivale a emitir los verbos PUT (con un cuerpo de solicitud válido) y GET contra el vínculo *\_self* del recurso, respectivamente. De forma similar, eliminar un recurso equivale a emitir un verbo DELETE contra el vínculo *\_self* del recurso. Vale la pena señalar que la organización jerárquica de los recursos en el modelo de recursos de la Base de datos de documentos necesita la compatibilidad con las eliminaciones en cascada, donde la eliminación del recurso del propietario provoca la eliminación de los recursos dependientes. Los recursos dependientes se pueden distribuir a través de otros nodos aparte de los recursos del propietario, de modo que la eliminación puede producirse lentamente. Sin considerar la mecánica de la recopilación de elementos no usados, después de la eliminación de un recurso, la cuota se libera instantáneamente y está disponible para su uso. Tenga en cuenta que el sistema mantiene la integridad referencial. Por ejemplo, no se puede insertar una recopilación en una base de datos que se eliminó ni reemplazar o consultar un documento de una recopilación que ya no existe.
 
Emitir un GET contra una fuente de recursos o consultar una recopilación puede tener como resultado millones de elementos, lo que dificulta que ambos servidores los materialicen y que los clientes los consuman como parte de una sola solicitud roundtrip/ e intercambio de respuestas. Para abordar esto, DocumentDB permite que los clientes paginen en una fuente grande una página a la vez. Los clientes pueden usar el encabezado de respuesta [x-ms-continuation] como un cursor para navegar a la página siguiente.

## Control de concurrencia optimista
La mayoría de las aplicaciones web dependen de una etiqueta de identidad basada en el control de concurrencia optimista para evitar los desastrosos problemas de "Actualización perdida" y "Eliminación perdida". La etiqueta de entidad es una marca de tiempo HTTP lógica y fácil de usar asociada con un recurso. La Base de datos de documentos admite de manera nativa el control de concurrencia optimista al asegurar que toda respuesta HTTP contenga la versión (de manera durable) asociada con el recurso específico. Los conflictos del control de concurrencia se detectan correctamente para los siguientes casos:

1.	Si los clientes emiten simultáneamente solicitudes mutantes (a través de los verbos PUT/DELETE) en un recurso con la última versión del recurso (especificado a través del encabezado de solicitud [if-match]), el motor de la base de datos de Base de datos de documentos los somete al control de concurrencia transaccional.
2.	Si un cliente se presenta con una versión más antigua del recurso (especificado mediante el encabezado de solicitud [if-match]), la solicitud se rechaza.  

## Opciones de conectividad
DocumentDB expone un modelo de direccionamiento lógico donde cada recurso tiene un URI lógico y estable identificado por su vínculo *\_self*. Como un sistema de almacenamiento distribuido desplegado en regiones, los recursos de varias cuentas de bases de datos en la Base de datos de documentos se dividen en varias máquinas y cada partición se replica para una mayor disponibilidad. Las réplicas que administran los recursos de una partición dada registran las direcciones físicas. Aun cuando las direcciones físicas cambian en el tiempo debido a errores, sus direcciones lógicas se mantienen estables y constantes. El traslado de la dirección lógica a física se mantiene en una tabla de enrutamiento que también esta disponible de manera interna como un recurso. La Base de datos de documentos expone dos modos de conectividad:

1.	**Modo de puerta de enlace:** los clientes están protegidos del traslado entre las direcciones lógicas a físicas o los detalles del enrutamiento; simplemente tratan las URI lógicas y navegan de modo RESTful en el modelo de recursos. Los clientes emiten las solicitudes usando una URI lógica y las máquinas perimetrales trasladan la URI a la dirección física de la réplica que administra el recurso y reenvía la solicitud. Con las máquinas perimetrales que ponen en la memoria caché (y periódicamente actualizan) la tabla de enrutamiento, el enrutamiento es sumamente eficaz. 
2.	**Modo de conectividad directa:** los clientes administran directamente la tabla de enrutamiento en su espacio de proceso y la actualizan periódicamente. El cliente puede conectarse directamente con las réplicas y omitir las máquinas perimetrales.   


Modo de conectividad|Protocol|Detalles|SDK de Base de datos de documentos
---|---|---|---
Puerta de enlace|HTTPS|Las aplicaciones se conectan directamente con los nodos perimetrales usando las URI lógicas. Esto significa un salto adicional de la red.|API de REST,. NET, Node.js, Java, Python, JavaScript
Conectividad directa|HTTPS y TCP|Las aplicaciones pueden tener acceso de manera directa a la tabla de enrutamiento y realizar el enrutamiento del cliente para que se conecte directamente con las réplicas.|.NET


## Pasos siguientes
Consulte la [Referencia de la API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) para obtener más información sobre cómo trabajar con recursos mediante la API de REST.

## Referencias
- [Referencia de API de REST de Azure DocumentDB](https://msdn.microsoft.com/library/azure/dn781481.aspx) 
- [DocumentDB de consulta](../documentdb-sql-query/)
- [Referencia SQL de DocumentDB](https://msdn.microsoft.com/library/azure/dn782250.aspx)
- [Programación de DocumentDB: procedimientos almacenados, desencadenadores y UDF](../documentdb-programming/)
- [Documentación de referencia de DocumentDB](https://msdn.microsoft.com/library/azure/dn781482.aspx)
- REST [http://en.wikipedia.org/wiki/Representational\_state\_transfer](http://en.wikipedia.org/wiki/Representational_state_transfer)
- Especificación JSON [http://www.ietf.org/rfc/rfc4627.txt](http://www.ietf.org/rfc/rfc4627.txt)
- Especificación HTTP [http://www.w3.org/Protocols/rfc2616/rfc2616.html](http://www.w3.org/Protocols/rfc2616/rfc2616.html)
- Etiquetas de entidad [http://en.wikipedia.org/wiki/HTTP\_ETag](http://en.wikipedia.org/wiki/HTTP_ETag)


[1]: ./media/documentdb-interactions-with-resources/interactions-with-resources2.png
 

<!---HONumber=August15_HO7-->