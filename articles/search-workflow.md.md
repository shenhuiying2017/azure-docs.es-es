<properties title="Search Service: workflow for developers" pageTitle="Search Service: workflow for developers" description="Search Service: workflow for developers" metaKeywords="" services="" solutions="" documentationCenter="" authors="heidist" videoId="" scriptId="" />

# Búsqueda de Azure: flujo de trabajo de desarrollo

[WACOM.INCLUDE [Este artículo usa el portal de Azure en vista previa][]]

En este artículo se proporcionan un mapa de ruta y algunos procedimientos recomendados para crear y mantener el servicio de búsqueda y sus índices.

Se asume que ya ha aprovisionado el servicio. Si no lo ha hecho aún, consulte [Configuración de Búsqueda en el portal de vista previa de Azure][] para comenzar.

-   [Paso 1: creación del índice][]
-   [Paso 2: adición de documentos][]
-   [Paso 3: consulta de un índice][]
-   [Paso 4: actualización o eliminación de índices y documentos][]
-   [Consideraciones de diseño de almacenamiento][]

## Paso 1: creación del índice

Las consultas (al menos las ajenas al sistema) se dirigen a un índice de búsqueda donde se incluyen datos de búsqueda y atributos. En este paso, define el esquema de índices en formato JSON y ejecuta una solicitud PUT de HTTPS para la creación de este índice en el servicio.

Los índices se suelen codificar en su entorno de desarrollo local. No existen herramientas ni editores integrados para la definición de los índices. Para obtener más información sobre la creación del índice, consulte [Creación de índices (API de Búsqueda de Azure)][] en MSDN.

## Paso 2: adición de documentos

Una vez creado el índice de búsqueda, podrá agregar documentos al índice publicándolos en formato JSON. Cada documento debe tener una clave única y una recopilación de campos donde se incluyan datos localizables y no localizables. Los datos de documentos se representan como un conjunto de pares clave-valor.

Recomendamos la adición de documentos en lotes para mejorar el rendimiento. Puede procesar por lotes hasta 10.000 documentos, asumiendo un tamaño medio de los documentos de entre 1 y 2 KB.

Hay un código de estado general para la solicitud de POST. Los códigos de estado son HTTP 200 (Éxito) o HTTP 207 (Varios estados) en caso de que haya una combinación de documentos correctos y documentos erróneos. Además del código de estado para la solicitud de POST, Búsqueda de Azure mantiene un campo de estado para cada documento. Una vez cargado un lote, necesita una forma de obtener un estado por documento que indique si la inserción se realizó correctamente o no en cada documento. El campo de estado proporciona esa información. Se establecerá en false si se produjo un error al cargarse el documento.

Bajo una carga elevada, no es extraño que se produzcan algunos errores. En este caso, el código de estado general es 207, lo que indica un éxito parcial, mientras que los documentos con errores de indexación tendrán establecida la propiedad "status" en false.

> [WACOM.NOTE] Cuando el servicio recibe documentos, se ponen en cola para la indexación y es posible que no se incluyan de forma inmediata en los resultados de búsqueda. Si la carga no es elevada, los documentos se suelen indexar en cuestión de segundos.

## Paso 3: consulta de un índice

Una vez indexados los documentos, podrá ejecutar consultas de búsqueda. Puede consultar un índice cada vez, usando OData o una sintaxis de la consulta simple:

-   [Sintaxis de la expresión OData para Búsqueda de Azure][]
-   [Sintaxis de la consulta simple en Búsqueda de Azure][]

## Paso 4: actualización o eliminación de índices y documentos

Opcionalmente, puede realizar cambios en el esquema del índice de búsqueda, actualizar o eliminar documentos del índice, además de índices.

Al actualizar un índice, puede combinar varias acciones (insertar, combinar y eliminar) en el mismo lote, acabando con la necesidad de varios recorridos de ida y vuelta. Actualmente, Búsqueda de Azure no admite actualizaciones parciales (PATCH de HTTP), de modo que, si tiene que actualizar un índice, debe reenviar la definición de los índices.

## Consideraciones de diseño de almacenamiento

Muchas aplicaciones de búsqueda usan varios formatos de almacenamiento para diversas necesidades de las aplicaciones. El almacenamiento interno que viene con Búsqueda de Azure debe usarse para almacenar índices y documentos. El análisis de texto depende de la disponibilidad inmediata de todos los campos localizables y atributos asociados.

No todos los campos de un documento se podrán localizar. Por ejemplo, si su aplicación es un catálogo en línea de música o vídeos, recomendamos almacenar archivos binarios en BLOB o de otra forma. Los propios archivos binarios no se pueden localizar, de modo que no hay necesidad de guardarlos en el almacenamiento de Búsqueda de Azure. Debe almacenar imágenes, vídeos, archivos de audio en otros servicios o ubicaciones, con un campo en el documento donde se almacena la URL en la ubicación de archivo.

Para obtener más información sobre la creación de índices o documentos, consulte la [API de Rest de Búsqueda de Azure][].

<!--Anchors-->  

  [Este artículo usa el portal de Azure en vista previa]: ../includes/preview-portal-note.md
  [Configuración de Búsqueda en el portal de vista previa de Azure]: ../search-configure/
  [Paso 1: creación del índice]: #sub-1
  [Paso 2: adición de documentos]: #sub-2
  [Paso 3: consulta de un índice]: #sub-3
  [Paso 4: actualización o eliminación de índices y documentos]: #sub-4
  [Consideraciones de diseño de almacenamiento]: #sub-5
<!--Link references-->
  [Creación de índices (API de Búsqueda de Azure)]: http://msdn.microsoft.com/en-us/library/dn798941.aspx
  [Sintaxis de la expresión OData para Búsqueda de Azure]: http://msdn.microsoft.com/en-us/library/dn798921.aspx
  [Sintaxis de la consulta simple en Búsqueda de Azure]: http://msdn.microsoft.com/en-us/library/dn798920.aspx
  [API de Rest de Búsqueda de Azure]: http://msdn.microsoft.com/en-us/library/dn798935.aspx
