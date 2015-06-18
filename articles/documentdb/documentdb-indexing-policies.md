<properties 
	pageTitle="Directivas de indexación de la DocumentDB | Azure" 
	description="Obtenga información acerca de cómo funciona la indexación en DocumentDB y sobre cómo configurar la directiva de indexación." 
	services="documentdb" 
	documentationCenter="" 
	authors="mimig1" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="documentdb" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="03/13/2015" 
	ms.author="mimig"/>


Directivas de indexación de la DocumentDB
============================

La DocumentDB es un sistema de bases de datos libre de esquemas. No asume ni requiere ningún esquema para los documentos JSON que indexa. Esto permite definir y establecer iteraciones en los modelos de datos de aplicación rápidamente. A medida que se agregan documentos a una colección, la DocumentDB indexará automáticamente las propiedades de los documentos para que estén disponibles para su consulta. La indexación automática también permite almacenar los tipos de documentos heterogéneos.

La indexación automática de documentos es posible gracias a las técnicas de escritura optimizada y mantenimiento del índice estructurado de registros y libre de bloqueos. DocumentDB admite un volumen sostenido de escrituras rápidas mientras se sigue atendiendo consultas coherentes.

El subsistema de indexación de la DocumentDB está diseñado para ofrecer lo siguiente:

·         Consultas jerárquicas y relacionales eficaces y enriquecidas sin necesidad de definición de esquema o índice.

·         Resultados de consulta coherente mientras se controla un volumen sostenido de escrituras. Para cargas de trabajo de rendimiento de escritura alto con consultas coherentes, el índice se actualiza incrementalmente, eficaz y en línea mientras se controla un volumen sostenido de escrituras.

·         Eficacia de almacenamiento. para obtener rentabilidad, se enlaza la sobrecarga de almacenamiento en el disco del índice y es predecible.

·         Multiempresa. Las actualizaciones del índice se realizan según la asignación de recursos de sistema asignados por la recopilación de la DocumentDB.

Es posible utilizar la directiva de indexación automática predeterminada para la mayoría de las aplicaciones, ya que ofrece la máxima flexibilidad y proporciona un justo equilibrio entre rendimiento y eficacia en el almacenamiento. Por otro lado, especificar una directiva de indexación personalizada permite lograr un equilibrio granular entre el rendimiento de las consultas, el rendimiento en la escritura y la sobrecarga de almacenamiento del índice.

Por ejemplo, si se excluye determinados documentos o rutas de acceso de los documentos de la indexación, es posible reducir el espacio de almacenamiento utilizado para la indexación, así como el tiempo de inserción para el mantenimiento del índice. Es posible cambiar el tipo de índice para adaptarlo a las consultas, o bien incrementar la precisión del índice en bytes para mejorar el rendimiento de las consultas. Este artículo describe las distintas opciones de indexación disponibles en la DocumentDB, así como la personalización de las directivas de indexación a sus cargas de trabajo.

<a id="HowWorks"></a>Funcionamiento de la indización de la DocumentDB
-----------------------------

La indexación en DocumentDB aprovecha el hecho de que la gramática de JSON permite que los documentos se **representen como árboles**. Para representar un documento JSON como un árbol, es necesario crear un nodo raíz ficticio que actúe como elemento principal para el resto de nodos reales del documento que dependen de dicho nodo ficticio. Cada etiqueta, que incluye los índices de la matriz en un documento JSON, se convierte en un nodo del árbol. La ilustración siguiente muestra un ejemplo de documento JSON y de su representación de árbol correspondiente.

![Indexing Policies](media/documentdb-indexing-policies/image001.png)


Por ejemplo, la propiedad JSON {"headquarters": "Belgium"} en el ejemplo anterior corresponde con la ruta de acceso /"headquarters"/"Belgium". La matriz JSON {"exports": [{"city": "Moscow"}, {"city": Athens"}]} corresponden a las rutas de acceso /"exports"/0/"city"/"Moscow" y /"exports"/1/"city"/"Athens".

**Nota** La representación de la ruta de acceso difumina el límite entre el esquema/la estructura y los valores de instancia de los documentos, lo que permite a la DocumentDB utilizar un modelo libre de esquemas.

En la DocumentDB, los documentos se organizan en recopilaciones que se pueden consultar mediante SQL o que procesan en el ámbito de una sola transacción. Cada recopilación puede configurarse con su propia directiva de indexación expresada en términos de rutas de acceso. En la siguiente sección, analizaremos cómo configurar el comportamiento de la indexación de una recopilación de la DocumentDB.

<a id="ConfigPolicy"></a>Configuración de la directiva de indexación de una colección
-------------------------------------------

En el ejemplo siguiente se muestra cómo establecer una directiva de indización personalizada durante la creación de una colección, mediante la API de REST de DocumentDB. El ejemplo muestra la directiva de indización expresada en rutas de acceso, tipos de índices y precisiones.


	POST https://<REST URI>/colls HTTP/1.1                                                  
 	...                                                             
 	Accept: application/json 
                                                                                                                         
 	{                                                                     
	 "id":"customIndexCollection",                                     
	 "indexingPolicy":{                                                 
     "automatic":true,                                            
	 "indexingMode":"Consistent",                                     
	 "IncludedPaths":[                                       
	           {                                                             
	              "IndexType":"Hash",                                        
	              "Path":"/"                                                 
	           }                                                  
	        ],                                                               
	        "ExcludedPaths":[                                                
	           "/"nonIndexedContent"/*"                                 
	        ]                                                               
	     }                                                                 
	 }                                                                                                                                                
 	...                                                                      
                  
                                                        
	 HTTP/1.1 201 Created                                                     


**Nota:** la directiva de indización de una colección debe especificarse en el momento de la creación. No se permite la modificación de la directiva de indización después de la creación de la colección, pero se admitirá en una versión futura de DocumentDB.

**Nota:** de forma predeterminada, la DocumentDB indiza todas las rutas de los documentos de forma homogénea con un índice hash. La ruta de acceso interna de marca de tiempo (_ts) se almacena con un índice de intervalo.

### Indexación automática

Puede elegir si desea que la recopilación de todos los documentos se indexe automáticamente o no. De forma predeterminada, todos los documentos se indexan automáticamente, pero puede desactivarla. Cuando se desactiva la indexación, solo se puede tener acceso a documentos a través de sus propios vínculos o mediante consultas con Id.

Cuando se desactiva la indexación automática, podrá agregar al índice de manera selectiva solo algunos documentos específicos. Por el contrario, puede dejar activada la indexación automática y excluir de forma selectiva solo algunos documentos específicos. Las configuraciones de indexación activada/desactivada son útiles cuando solo tiene un subconjunto de los documentos que necesita consultar.

Puede configurar la directiva predeterminada especificando el valor de la propiedad automática como verdadero/falso. Para anular la operación de un único documento, puede establecer el encabezado de solicitud x-ms-indexingdirective durante la inserción o la sustitución de un documento.

Por ejemplo, en el ejemplo siguiente muestra cómo incluir un documento de forma explícita mediante la propiedad [DocumentDB .NET SDK](https://github.com/Azure/azure-documentdb-java) y [RequestOptions.IndexingDirective](http://msdn.microsoft.com/library/microsoft.azure.documents.client.requestoptions.indexingdirective.aspx).

	// If you want to override the default collection behavior to either     
	// exclude (or include) a Document from indexing,                                                                                           
	// use the RequestOptions.IndexingDirective property.                                  
	                                                                         
	client.CreateDocumentAsync(defaultCollection.SelfLink,  
	    new { Id = "AndersenFamily", isRegistered = true },                            
		new RequestOptions                               
		    {                                                                    
			    IndexingDirective = IndexingDirective.Include                                                                                      
			}
		);                                                                  


### Modos de indexación

Puede elegir entre las actualizaciones de índices sincrónicas (**Homogéneas**) y asincrónicas (**Diferida**). De forma predeterminada, el índice se actualiza de forma sincrónica en cada acción de inserción, sustitución o eliminación realizada en un documento de la colección. Esto permite que las consultas tengan el mismo nivel de homogeneidad que el de las lecturas de los documentos sin demoras en la actualización de los índices.

Aunque la DocumentDB es de escritura optimizada y admite volúmenes constantes de escrituras de documentos junto con capacidades sincrónicas de mantenimiento del índice, es posible configurar determinadas recopilaciones para que su índice se actualice de forma diferida. La indexación diferida es perfecta para escenarios en los que los datos se escriben en ráfagas y desea amortizar el trabajo necesario para indexar el contenido durante un período de tiempo prolongado. Esto permite utilizar de forma eficaz el rendimiento aprovisionado y atender a las solicitudes de escritura en las horas punta con una latencia mínima. Con la indexación diferida activada, los resultados de la consulta serán coherentes con el tiempo independientemente del nivel de coherencia configurado para la cuenta de base de datos.

El siguiente programa de ejemplo muestra cómo crear una colección de DocumentDB mediante el SDK de .NET con la indización automática coherente en todas las inserciones de documentos.


	 // Default collection creates a hash index for all string and numeric    
	 // fields. Hash indexes are compact and offer efficient                                                                                           
	 // performance for equality queries.                                     
	                                                                          
	 var defaultCollection = new DocumentCollection { Id ="defaultCollection" };                                                   
	                                                                          
	 // Optional. Override Automatic to false for opt-in indexing of documents.                                                                
	                                                                          
	 defaultCollection.IndexingPolicy.Automatic = true;                       
	                                                                          
	 // Optional. Set IndexingMode to Lazy for bulk import/read heavy        
	 // collections. Queries might return stale results with Lazy indexing.       
	                                                                          
	 defaultCollection.IndexingPolicy.IndexingMode = IndexingMode.Consistent; 
	                                                                          
	 defaultCollection = await client.CreateDocumentCollectionAsync(database.SelfLink,defaultCollection);                                                      


### Tipos de índices y precisión

El tipo o el esquema utilizado para las entradas de índice repercute directamente en el rendimiento y almacenamiento del índice. En esquemas que utilizan mayor precisión, las consultas suelen ser más rápidas. Sin embargo, la sobrecarga en el almacenamiento también es mayor para el índice. Elegir una precisión menor implica la posibilidad de procesar más documentos durante la ejecución de la consulta, aunque la sobrecarga del almacenamiento será menor.

La precisión del índice de los valores en cualquier ruta de acceso puede ser de entre 3 y 7 bytes.
Puesto que la misma ruta de acceso puede tener valores numéricos y de cadena en distintos documentos, estos se pueden controlar por separado. En el SDK. NET, estos valores corresponden a las propiedades [NumericPrecision](http://msdn.microsoft.com/library/microsoft.azure.documents.indexingpath.numericprecision.aspx) y [StringPrecision](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.indexingpath.stringprecision.aspx).

Hay dos tipos de índices admitidos: De hash e intervalo. El tipo de índice **Hash** permite realizar consultas de igualdad eficaz. Para la mayoría de los casos de uso, los índices hash no requieren una precisión mayor que el valor predeterminado de 3 bytes.

El tipo de índice de **intervalo** permite variar las consultas (mediante >, <, >=, <=, !=). Para rutas de acceso que tienen grandes intervalos de valores, se recomienda usar una precisión mayor como, por ejemplo, 6 bytes. Un caso de uso común que requiere un mayor índice de intervalo de precisión es el del almacenamiento de las marcas de tiempo como tiempo de época.

Si el caso de uso no requiere consultas de intervalos eficaces, el valor predeterminado de los índices hash es el que ofrece el mejor equilibrio entre almacenamiento y rendimiento. Tenga en cuenta que para admitir consultas por rango, debe especificar una directiva de índice personalizado.

> [AZURE.NOTE] Los índices de intervalo solo admiten valores numéricos.
  
En el ejemplo siguiente se muestra cómo aumentar la precisión de los índices de intervalo en una recopilación mediante el SDK de .NET. Tenga en cuenta que esto usa una ruta de acceso especial "/", que se explica en la sección siguiente.


	 // If your collection has numeric fields that need to be queried    
	 // against ranges (>,>=,<=,<), then you can configure the collection to 
	 // use range queries for all numeric values.                                                                                                      
 
	var rangeDefault = new DocumentCollection { Id = "rangeCollection" };                                                              
	rangeDefault.IndexingPolicy.IncludedPaths.Add(                                                             
												 new IndexingPath {   
													IndexType = IndexType.Range, Path = "/", 
													NumericPrecision = 7 }
												 ); 
	 rangeDefault = await client.CreateDocumentCollectionAsync(database.SelfLink, rangeDefault);   


### Rutas de acceso del índice

En los documentos, puede elegir qué rutas de acceso se deben incluir o excluir del índice. Esto puede mejorar el rendimiento de escritura y reducir el almacenamiento necesario para índice en escenarios en los que se conocen de antemano los patrones de consulta.

Las rutas de acceso del índice comenzar con la raíz (/) y normalmente finalizan con el operador comodín ?, que indica que hay varios posibles valores para el prefijo. Por ejemplo, para atender a la consulta SELECT * FROM Families F WHERE F.familyName = "Andersen", debe incluir una ruta de acceso del índice para /"familyName"/? en la directiva de índice de la recopilación.

Las rutas de acceso del índice también pueden utilizar el operador comodín * para especificar el comportamiento de las rutas de acceso de forma recursiva en el prefijo. Por ejemplo, /"payload"/* puede usarse para excluir de la indexación todo el contenido de la propiedad payload.

Estos son los patrones comunes para especificar las rutas de acceso del índice:

<table border="0" cellspacing="0" cellpadding="0">
    <tbody>
        <tr>
            <td valign="top">
                <p>
                    <strong>Ruta de acceso</strong>
                </p>
            </td>
            <td valign="top">
                <p>
                    <strong>Descripción/caso de uso</strong>
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso predeterminada de la recopilación. Recursiva. Se aplica a la totalidad del árbol de documentos.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para atender las consultas como la siguiente (con tipos hash o de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop &gt; 5
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/*
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso del índice para todas las rutas de acceso situadas en la etiqueta especificada. Especificada solo para la exclusión de la indexación.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso del índice utilizada durante la ejecución de la consulta para eliminar los documentos que no tienen la ruta de acceso especificada.
                </p>
            </td>
        </tr>
        <tr>
            <td valign="top">
                <p>
                    /"prop"/"subprop"/?
                </p>
            </td>
            <td valign="top">
                <p>
                    Ruta de acceso de índice necesaria para atender consultas (con las de tipo hash o de intervalo respectivamente):
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop = "value"
                </p>
                <p>
                    SELECT * FROM collection c WHERE c.prop.subprop &gt; 5
                </p>
            </td>
        </tr>
    </tbody>
</table>

> [AZURE.NOTE] Al configurar las rutas de acceso de índice personalizado, es necesario especificar la regla de indexación predeterminada para el árbol de todo el documento indicada mediante la ruta de acceso especial "/".

En el ejemplo siguiente se configura una ruta de acceso específica con indexación de intervalo y un valor de precisión personalizado de 7 bytes:


 	// If you only want to specify range queries against a specific field,   
 	// then use a range index against that field. Doing so reduces the   
	// amount of storage required for indexes for the collection. In this    
 	// case, the query creates an index against the JSON path                   
 	// /"CreatedTimestamp"/?    
 	// allowing queries of the form WHERE CreatedTimestamp [>] X            
	
	var pathRange = new DocumentCollection { Id = "rangeSinglePathCollection" };    
	
	pathRange.IndexingPolicy.IncludedPaths.Add(
								new IndexingPath { 
										IndexType = IndexType.Range, 
										Path = "/"CreatedTimestamp"/?",   
										NumericPrecision = 7   
							 			}
									);   
	
	pathRange.IndexingPolicy.IncludedPaths.Add(   
											 new IndexingPath {  
											  	 Path = "/"  
											 });                                                                      
                                                   
	 pathRange = await client.CreateDocumentCollectionAsync(database.SelfLink, pathRange);      


La DocumentDB devuelve un error cuando una consulta usa un operador de intervalo pero no tiene ningún índice de intervalo en la ruta de acceso consultada y no dispone de todos los filtros que pueden obtenerse desde el índice. Sin embargo, estas consultas se pueden realizar sin un índice de intervalo mediante el uso del encabezado x-ms-documentdb-allow-scans en la API REST o con la opción AllowScanInQueryrequest mediante el SDK de .NET.

En el ejemplo siguiente se excluye un árbol secundario de rutas de acceso de la indexación mediante el comodín
"*".

	var excluded = new DocumentCollection { Id = "excludedPathCollection" };                                                                       
  	excluded.IndexingPolicy.IncludedPaths.Add(
	newIndexingPath {  Path = "/" });  

	excluded.IndexingPolicy.ExcludedPaths.Add("/" nonIndexedContent"/*");    
	excluded = await client.CreateDocumentCollectionAsync(database.SelfLink,excluded);                                                               


Optimización del rendimiento
------------------

A medida que evalúe distintas configuraciones de directiva de indexación, debe medir las implicaciones de rendimiento y almacenamiento de la directiva a través de las API de DocumentDB.

Para comprobar la cuota de almacenamiento y el uso de una colección, ejecute una solicitud HEAD o GET en el recurso de colección e inspeccione la cuota x-ms-request y los encabezados x-ms-request-usage. En el SDK de .NET, las propiedades [DocumentSizeQuota](http://msdn.microsoft.com/library/dn850325.aspx) y [DocumentSizeUsage](http://msdn.microsoft.com/library/azure/dn850324.aspx) de [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) contienen los valores correspondientes.


 	// Measure the document size usage (which includes the index size) against   
 	// different policies.        
	 ResourceResponse<DocumentCollection> collectionInfo = await client.ReadDocumentCollectionAsync(collectionSelfLink);  
	 Console.WriteLine("Document size quota: {0}, usage: {1}", collectionInfo.DocumentSizeQuota, collectionInfo.DocumentSizeUsage);                                       


Para medir la sobrecarga de la indización en cada operación de escritura (crear, actualizar o eliminar), inspeccione el encabezado x-ms-request-charge (o la propiedad [RequestCharge](http://msdn.microsoft.com/library/dn799099.aspx) equivalente en [ResourceResponse<T>](http://msdn.microsoft.com/library/dn799209.aspx) en el SDK de .NET para medir el número de unidades de solicitudes utilizadas por estas operaciones.


 	// Measure the performance (request units) of writes.     
 	ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);              
                                                                    
 	Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);                                                 
                                                                          
 	// Measure the performance (request units) of queries.    
	 IDocumentQuery<dynamic> queryable =  client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();                                          
                                                                          
 	while (queryable.HasMoreResults)                                                                            
 	{                                                                         
 	   FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>(); 
	   Console.WriteLine("Query batch consumed {0} request units",queryResponse.RequestCharge);
	}                                                                        




<!--HONumber=49--> 