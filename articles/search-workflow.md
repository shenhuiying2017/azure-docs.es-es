<properties title="Search Service: workflow for developers" pageTitle="Servicio de búsqueda: flujo de trabajo para los desarrolladores" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article"  ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Búsqueda de Azure: flujo de trabajo de desarrollo

[WACOM.INCLUDE [This article uses the Azure Preview portal](../includes/preview-portal-note.md)]

En este artículo se proporcionan un mapa de ruta y algunos procedimientos recomendados para crear y mantener el servicio de búsqueda y sus índices. 

Se asume que ya ha aprovisionado el servicio. Si no es así, consulte [Introducción a Búsqueda de Azure](../search-get-started/) para obtener más instrucciones.

+ [Paso 1: creación del índice](#sub-1)
+ [Paso 2: adición de documentos](#sub-2)
+ [Paso 3: consulta de un índice](#sub-3)
+ [Paso 4: actualización o eliminación de índices y documentos](#sub-4)
+ [Consideraciones de diseño de almacenamiento](#sub-5)


<h2 id="sub-1">Paso 1: creación del índice</h2>

Las consultas se destinan a un índice de búsqueda que contiene atributos y datos de búsqueda. Por lo tanto, el primer paso después de aprovisionar el servicio es definir el esquema de índice en formato JSON y ejecutar una solicitud PUT de HTTPS para crear el índice en el servicio. 

Los índices se crean mediante el código de aplicación. No existen herramientas integradas ni editores que ayuden a definir un índice en una interfaz de usuario. Entre los ejemplos que demuestran distintas formas de crear un índice destacan [Creación de la primera solución de búsqueda mediante la Búsqueda de Azure](../search-create-first-solution/), donde el esquema se especifica en el archivo Program.cs y [Introducción a los perfiles de puntuación en la Búsqueda de Azure](../search-get-started-scoring-profiles), que ofrece el índice en un archivo de esquema JSON independiente. Para obtener más información sobre la creación del índice, consulte [Creación de índices (API de Búsqueda de Azure)](http://msdn.microsoft.com/es-es/library/dn798941.aspx) en MSDN.

<h2 id="sub-2">Paso 2: adición de documentos</h2>

Una vez creado el índice de búsqueda, podrá agregar documentos al índice publicándolos en formato JSON. Cada documento debe tener una clave única y una recopilación de campos donde se incluyan datos localizables y no localizables. Los datos de documentos se representan como un conjunto de pares clave-valor para cada campo.

Recomendamos la adición de documentos en lotes para mejorar el rendimiento. Puede procesar por lotes hasta 1.000 documentos, asumiendo un tamaño medio de los documentos de entre 1 y 2 KB.

Hay un código de estado general para la solicitud de POST. Los códigos de estado son HTTP 200 (Éxito) o HTTP 207 (Varios estados) en caso de que haya una combinación de documentos correctos y documentos erróneos. Además del código de estado para la solicitud de POST, Búsqueda de Azure mantiene un campo de estado para cada documento. Una vez cargado un lote, necesita una forma de obtener un estado por documento que indique si la inserción se realizó correctamente o no en cada documento. El campo de estado proporciona esa información. Se establecerá en false si se produjo un error al cargarse el documento.

Bajo una carga elevada, no es extraño que se produzcan algunos errores. En este caso, el código de estado general es 207, lo que indica un éxito parcial, mientras que los documentos con errores de indexación tendrán establecida la propiedad "status" en false.

> [WACOM.NOTE] Cuando el servicio recibe documentos, se ponen en cola para la indexación y es posible que no se incluyan de forma inmediata en los resultados de búsqueda. Si la carga no es elevada, los documentos se suelen indexar en cuestión de segundos.


<h2 id="sub-3">Paso 3: consulta de un índice</h2>

Una vez indexados los documentos, podrá ejecutar consultas de búsqueda. Puede consultar un índice cada vez, usando OData o una sintaxis de la consulta simple:

+	[Sintaxis de la expresión OData para Búsqueda de Azure](http://msdn.microsoft.com/es-es/library/dn798921.aspx)
+	[Sintaxis de la consulta simple en Búsqueda de Azure](http://msdn.microsoft.com/es-es/library/dn798920.aspx)

<h2 id="sub-4">Paso 4: actualización o eliminación de índices y documentos</h2>

Opcionalmente, puede realizar cambios en el esquema del índice de búsqueda, actualizar o eliminar documentos del índice, además de índices.

Al actualizar un índice, puede combinar varias acciones (insertar, combinar y eliminar) en el mismo lote, acabando con la necesidad de varios recorridos de ida y vuelta. Actualmente, Búsqueda de Azure no admite actualizaciones parciales (PATCH de HTTP), de modo que, si tiene que actualizar un índice, debe reenviar la definición de los índices.

<h2 id="sub-5">Consideraciones de diseño de almacenamiento</h2>

Búsqueda de Azure usa almacenamiento interno para los índices y los documentos que se utilizan en operaciones de búsqueda. El análisis de texto y de índice depende de la disponibilidad inmediata de todos los campos localizables y atributos asociados.

No todos los campos de un documento se podrán localizar. Por ejemplo, si su aplicación es un catálogo en línea de música o vídeos, recomendamos almacenar archivos binarios en el servicio BLOB de Azure o con otro formato de almacenamiento. Los propios archivos binarios no se pueden localizar, de modo que no hay necesidad de guardarlos en el almacenamiento de Búsqueda de Azure. Aunque debería almacenar imágenes, vídeos y archivos de audio en otros servicios o ubicaciones, debe incluir un campo que haga referencia a la dirección URL de la ubicación del archivo. De este modo, puede recuperar los datos externos como parte de los resultados de búsqueda. 

Para obtener más información sobre la creación de índices o documentos, consulte la [API de Rest de Búsqueda de Azure](http://msdn.microsoft.com/es-es/library/dn798935.aspx).


<!--Anchors-->
[Paso 1: creación del índice]: #sub-1
[Paso 2: adición de documentos]: #sub-2
[Paso 3: consulta de un índice]: #sub-3
[Paso 4: actualización o eliminación de índices y documentos]: #sub-4
[Elección de un almacén de documentos]: #sub-5


<!--Image references-->

<!--Link references-->
[Introducción a Búsqueda de Azure]: ../search-get-started/
[Administración del servicio de búsqueda en Microsoft Azure]: ../search-manage/
[Creación de la primera solución de búsqueda con Búsqueda de Azure]: ../search-create-first-solution/

