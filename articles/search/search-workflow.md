<properties
	pageTitle="Flujo de trabajo de desarrollo típico para Búsqueda de Azure | Microsoft Azure"
	description="Un flujo de trabajo o una guía básica para la creación de prototipos y aplicaciones de producción que se integran con Búsqueda de Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Flujo de trabajo de desarrollo típico para Búsqueda de Azure

Este artículo es una guía para incluir Búsqueda de Azure como un componente que proporcione la experiencia de búsqueda en la aplicación personalizada. Según sea su intención, bien de hacer primero algunas pruebas o de pasar directamente a la acción, puede que necesite unas instrucciones preliminares sobre cómo integrar Búsqueda de Azure en su proyecto de desarrollo personalizado.

En las secciones siguientes vamos a desglosar un flujo de trabajo típico para un prototipo inicial, esto le ayudará a evaluar si Búsqueda de Azure reúne los requisitos de búsqueda que necesita para la aplicación. La segunda parte del artículo trata sobre las importantes decisiones de diseño que hay que tener en cuenta a la hora de afrontar un trabajo de desarrollo de aplicaciones más serio.

Antes de iniciar la creación de prototipos, recomendamos que haga un repaso con uno de los tutoriales de introducción, o con este [vídeo de presentación de una hora con información detallada](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/). Los tutoriales de introducción se proporcionan en los siguientes lenguajes: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node.JS](search-get-started-nodejs.md).

## Desarrollo de prototipos

Normalmente, el camino más rápido para la creación de un prototipo correcto incluye los pasos de esta sección. Estos pasos incluyen el aprovisionamiento de un servicio, la definición de un esquema para el índice, la carga del índice con documentos y la consulta del índice.

Para las aplicaciones con datos volátiles (por ejemplo, si el caso común incluye cambios rápidos de inventario o contenido), el prototipo tiene que incluir también un componente para actualizar documentos.

   ![][1]

### Paso 1: aprovisionamiento del servicio

Búsqueda de Azure es un servicio en línea totalmente administrado disponible a través de la suscripción de Azure. [Una vez que se suscriba a Azure](http://azure.microsoft.com/pricing/free-trial/), Búsqueda se puede agregar con rapidez. Visite [Creación de un servicio de Búsqueda de Azure en el portal](search-create-service-portal.md) para obtener instrucciones sobre cómo agregar un servicio de Búsqueda a su suscripción.

Hay dos niveles de precios para elegir. Se recomienda el servicio compartido (gratuito) para crear prototipos, con la advertencia de que tendrá que trabajar con un pequeño subconjunto de los datos. El servicio compartido es gratuito para los suscriptores existentes (a través de la suscripción normal o de evaluación) y se configura con rapidez, pero limita el número de índices y documentos que puede utilizar a 3 índices y hasta 10.000 documentos por índice, o a 50 MB de almacenamiento total, lo que se alcance primero.

### Paso 2: creación del índice

Después de crear el servicio, está listo para crear un índice, empezando con su definición de esquema.

La manera más rápida y fácil de crear un índice es a través del Portal de Azure. Como mínimo, cada documento tiene que tener una clave única y al menos un campo que contenga datos que se puedan buscar. Para comenzar, consulte [Creación de un índice de Búsqueda de Azure en el portal](search-create-index-portal.md).

> [AZURE.NOTE]**Dentro de un índice de Búsqueda de Azure**
>
> Un *índice* se compone de datos conservados y organizados que actúan como *corpus de búsqueda* para todas las operaciones de búsqueda subsiguientes. El corpus de búsqueda se almacena en la nube como parte de la suscripción al servicio de Búsqueda, lo que permite realizar operaciones de búsqueda de forma rápida y coherente. En terminología de búsqueda, se denomina *documento* a cada elemento en el corpus de búsqueda, y la suma total de todos los documentos es la *colección de documentos*.
>
>Un *esquema de índice* define todos los campos dentro de un documento por nombre, tipo de datos y atributos que especifican si el campo se puede buscar, filtrar, utilizar con facetas, etc.
>
> Además de la estructura del documento, un esquema de índice también especifica perfiles de puntuación que proporcionan criterios para aumentar una puntuación de búsqueda y opciones de configuración que permiten que las consultas de Autocompletar (proveedores de sugerencias) y CORS para las solicitudes de consultas entre dominios. *Para los prototipos, se recomienda iniciar especificando solo los campos en un documento*, para continuar agregando otras características de forma incremental (vea el Paso 5 para obtener una lista de funcionalidades adicionales que se pueden agregar más adelante).
>
> Para poner un ejemplo del mundo real, piense en una aplicación de comercio electrónico. El índice de búsqueda contendrá todos los productos o servicios que se pueden buscar en la aplicación (cualquier cosa que los resultados de búsqueda puedan devolver). Habrá un documento para cada SKU. Cada documento incluirá el nombre del producto, marca, tamaños, precio, colores e incluso referencias a imágenes u otros archivos de recursos que se desee que aparezcan en los resultados de la búsqueda.

### Paso 3: carga de documentos

Después de guardar el índice de Búsqueda de Azure, el siguiente paso es rellenar el índice con documentos. En este paso, los datos se cargan, analizan, acortan y almacenan en estructuras de datos (por ejemplo, índices invertidos) diseñadas para cargas de trabajo de búsqueda.

Los datos que se cargan en un índice deben ajustarse al esquema definido en el paso anterior. Los datos de documentos se representan como un conjunto de pares clave-valor para cada campo, en formato JSON. Si el esquema especifica un campo identificador (clave), un campo de nombre, un campo de número y un campo de dirección URL (lo que podría hacer si las imágenes externas forman parte de los resultados de búsqueda), todos los documentos que introduzca en el índice tienen que tener valores (o null) para cada campo.

Hay varias formas de cargar documentos, pero ahora mismo, todas ellas requieren una API. Para la mayoría de los prototipos, este paso puede resultar el más lento debido a un requisito de codificación. Las opciones se describen más adelante en este artículo.

> [AZURE.NOTE]Recuerde que el servicio compartido tiene un límite de 10.000 documentos por índice. Asegúrese de reducir el conjunto de datos para que permanezca dentro de los límites. Consulte [Límites y restricciones](search-limits-quotas-capacity.md) para obtener detalles.

#### Cómo cargar datos en un índice

Un enfoque consiste en utilizar un indizador. Para orígenes de datos relacionales de SQL Server o DocumentDB de Azure en Azure (de forma específica la base de datos de SQL de Azure o SQL Server en una máquina virtual de Azure), puede usar [indizadores](https://msdn.microsoft.com/library/dn946891.aspx) para recuperar documentos desde un origen de datos admitido. Se pueden encontrar ejemplos de código que utilizan indizadores para cargar documentos en cualquiera de estos tutoriales de introducción: [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md), [Node.JS](search-get-started-nodejs.md).

Una segunda opción es escribir un programa sencillo mediante la API de REST o la biblioteca de .NET que cargue los documentos:

- [Agregar, actualizar o eliminar documentos (API de REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [DocumentOperationsExtensions Class](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

Una tercera opción que funciona para los conjuntos de datos muy pequeños es usar [Fiddler](search-fiddler.md) o [Chrome Postman](search-chrome-postman.md) para cargar los documentos.

Una cuarta opción, posiblemente la más sencilla, consiste en tomar prestado el código bien del [ejemplo de la API de REST con C# de Adventure Works ](https://azuresearchadventureworksdemo.codeplex.com/) que carga los documentos desde una base de datos incrustada (.mdf) en la solución, o del [ejemplo de la API de REST con C# de perfiles de puntuación](https://azuresearchscoringprofiles.codeplex.com/) que carga los datos de archivos de datos JSON incluidos en la solución.

> [AZURE.TIP]Puede modificar y ejecutar el [ejemplo de perfiles de puntuación](https://azuresearchscoringprofiles.codeplex.com/), reemplazando los archivos de datos de JSON y el archivo schema.json con datos válidos para la aplicación.

### Paso 4: documentos de la consulta

Una vez que se cargan los documentos en el índice, puede escribir la primera consulta.

La forma más rápida de obtener los primeros resultados de búsqueda en el servicio de Búsqueda es con el uso de [Fiddler](search-fiddler.md) o [Chrome Postman](search-chrome-postman.md) para ver una respuesta, aunque de forma realista, lo mejor es escribir un código de interfaz de usuario simple para ver los resultados en un formato legible.

Las API para las operaciones de búsqueda incluyen:

- [La operación Buscar documentos](https://msdn.microsoft.com/library/dn798927.aspx)
- [Clase SearchIndexClient](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

Las consultas de Búsqueda de Azure pueden ser muy simples. Al incluir `search=*` en el URI devolverá los 50 primeros elementos en el corpus de búsqueda; si se especifica `search=<some phrase>` se realizará una búsqueda de texto completo de la frase, que devolverá hasta 50 documentos, suponiendo que hay al menos 50 documentos que contengan una coincidencia con el término de entrada.

50 documentos es el valor predeterminado. Puede cambiar el número de elementos que se devuelven mediante el parámetro de consulta `$Count`. Este parámetro se documenta en [Buscar documentos](https://msdn.microsoft.com/library/dn798927.aspx).

> [AZURE.TIP]La lista más completa de ejemplos de consultas puede encontrarse en [Buscar documentos](https://msdn.microsoft.com/library/dn798927.aspx), también puede revisar la [referencia sobre la sintaxis](https://msdn.microsoft.com/library/dn798920.aspx) para ver la lista de operadores compatibles.

### Paso 5: explorar más características

Ahora que tiene un servicio y un índice, puede experimentar con otras características para desarrollar aún más la experiencia de búsqueda. A continuación se enumera una breve lista de características para investigar.

**Páginas de búsqueda:** a menudo incluyen recuentos de documento en un conjunto de resultados o utilizan la paginación para subdividir los resultados en un número más fácil de administrar. Consulte [paginación](search-pagination-page-layout.md) para obtener más información.

**searchMode = all** es un parámetro de consulta que cambia la forma en la que Búsqueda de Azure evalúa el operador NOT. De forma predeterminada, las consultas que incluyen NOT (-), expanden en lugar de limitar los resultados. Puede establecer este parámetro para cambiar la forma en la que se evalúa el operador. Está documentado en [Buscar documentos](https://msdn.microsoft.com/library/dn798927.aspx) o en [SearchMode Enumeration](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx).

**Perfiles de puntuación:** se utilizan para aumentar las puntuaciones de búsqueda, con ellos los elementos que cumplen unos criterios predefinidos aparecerán en un lugar más alto en los resultados de búsqueda. Consulte [Introducción a los perfiles de puntuación](search-get-started-scoring-profiles.md) para hacer un recorrido por esta característica.

**Filtros:** se utilizan para limitar los resultados de búsqueda al proporcionar criterios adicionales en la selección. Las expresiones de filtro se colocan dentro de la consulta. Consulte [Buscar documentos](https://msdn.microsoft.com/library/dn798927.aspx) para obtener más información.

**Navegación por facetas:** se usa para realizar un filtrado autodirigido. Búsqueda de Azure genera y devuelve la estructura y el código presenta la estructura de navegación por facetas en una página de resultados de búsqueda. Consulte [Procedimiento para implementar la navegación por facetas en Búsqueda de Azure](search-faceted-navigation.md) para obtener más información.

**Proveedores de sugerencias:** se refiere a la característica que autocompleta o anticipa la escritura de las consultas, devolviendo sugerencias para los términos de búsqueda a medida que el usuario escribe los primeros caracteres de una frase de búsqueda. Consulte [Sugerencias](https://msdn.microsoft.com/library/dn798936.aspx) o [Suggesters Class](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx) para obtener más información.

**Analizadores de lenguaje:** proporcionan las reglas lingüísticas utilizadas durante el análisis de texto. El analizador de lenguaje predeterminado para Búsqueda de Azure es Lucene para inglés, pero se pueden utilizar otros o incluso varios analizadores especificándolos en el índice. Los analizadores de Lucene están disponibles en todas las API. Los procesadores de lenguaje natural de Microsoft solo están disponibles en [Versión de API de REST 2015-02-28 Preview](search-api-2015-02-28-preview.md). Consulte [￼Compatibilidad de idioma￼ ](https://msdn.microsoft.com/library/dn879793.aspx) para obtener más información.

### Paso 6: actualización de índices y documentos

Algunas de las características que se vayan a evaluar pueden requerir una actualización del índice, lo que a menudo hace que sea necesario actualizar sus documentos.

Si tiene que actualizar el índice o los documentos, por ejemplo para agregar proveedores de sugerencias o especificar analizadores de lenguaje en los campos que ha agregado para ese propósito, vea los siguientes vínculos para obtener instrucciones:

- [Actualización de índice (API de REST)](https://msdn.microsoft.com/library/dn800964.aspx)
- [Actualización de indizador (API de REST)](https://msdn.microsoft.com/library/dn946892.aspx)
- [Adición, actualización o eliminación de documentos (API de REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Index Class (.NET library)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [Document Class (.NET library)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

Una vez que se ha creado un prototipo que establece la prueba de concepto, puede aprovechar lo que ha aprendido para pasar al siguiente nivel mediante el diseño de un proyecto de desarrollo que puede admitir cargas de trabajo de producción.

## Desarrollo de aplicaciones

Avanzar a la siguiente fase requiere decisiones acerca de qué API usar, cómo gestionar los documentos y cargar la frecuencia y si se desea incluir recursos externos en los resultados de búsqueda.

El diseño de solución que realice tendrá que incluir todos los pasos descritos para los prototipos, pero en lugar de dar prioridad a la ruta de acceso más expedita, tendrá que considerar cuáles son los métodos más compatibles con la solución general.

### Elija una API

Búsqueda de Azure proporciona dos modelos de programación: la biblioteca de .NET para código administrado y una API de REST para los lenguajes de programación como Java, JavaScript u otros lenguajes no destinados a Microsoft .NET Framework.

Actualmente, un pequeño subconjunto de características no están aún en la biblioteca. NET, por lo que incluso si prefiere escribir código administrado, tendrá que usar la API de REST para obtener las características que desee. Características que solo están disponibles en la API de REST incluyen:

- [Procesadores de lenguaje natural de Microsoft - solo vista previa](../search-api-2015-02-28-preview/)
- [característica moreLikeThis - solo vista previa](../search-api-2015-02-28-preview/)
- [API de REST de administración de Búsqueda de Azure](https://msdn.microsoft.com/library/dn832684.aspx)

Puede comprobar periódicamente el artículo [Novedades](search-latest-updates.md) para ver los cambios en el estado de las características.

### Determinación de los métodos de sincronización de datos: inserción o extracción

Los modelos de inserción y extracción hacen referencia a la forma en la que se actualizan los documentos en el índice. A menudo, el escenario mismo dicta cuál de los modelos es el adecuado.

Si su empresa es un minorista en línea, probablemente necesitará un modelo de inserción para poder insertar o reescribir cualquier cambio en el inventario tanto en la base de datos OLTP como en el índice de Búsqueda de Azure. Cuando se haya agotado un SKU específico, o un color o un tamaño no estén disponibles, es necesario que el índice se actualice lo antes posible para evitar la frustración de los clientes. Solo los modelos de inserción pueden proporcionar actualizaciones prácticamente en tiempo real para el índice de búsqueda.

No hay ningún mecanismo concreto en Búsqueda de Azure para implementar un modelo de inserción. El código de aplicación, en la capa de datos, debe controlar la operación de actualización de documentos mediante la [API de REST](https://msdn.microsoft.com/library/dn798935.aspx) o la [biblioteca .NET](https://msdn.microsoft.com/library/dn951165.aspx) para actualizar los documentos de la colección. Como detalle de implementación, el uso de una SKU del producto para la clave de documento puede ayudarle con esta tarea.

Los modelos de extracción son generalmente operaciones programadas que recuperan datos a partir de fuentes de datos externas. En Búsqueda de Azure, está disponible un modelo de extracción a través de [indizadores](https://msdn.microsoft.com/library/azure/dn946891.aspx), que se encuentran a su vez disponibles para orígenes de datos específicos: DocumentDB de Azure o Base de datos de SQL Azure (y también SQL Server en máquinas virtuales de Azure).

### Carga de documentos en lotes

Recomendamos la adición de documentos en lotes para mejorar el rendimiento. Puede procesar por lotes hasta 1.000 documentos, asumiendo un tamaño medio de los documentos de entre 1 y 2 KB.

Hay un código de estado general para la solicitud de POST. Los códigos de estado son HTTP 200 (Éxito) o HTTP 207 (Varios estados) en caso de que haya una combinación de documentos correctos y documentos erróneos. Además del código de estado para la solicitud de POST, Búsqueda de Azure mantiene un campo de estado para cada documento. Una vez cargado un lote, necesita una forma de obtener un estado por documento que indique si la inserción se realizó correctamente o no en cada documento. El campo de estado proporciona esa información. Se establecerá en false si se produjo un error al cargarse el documento.

Bajo una carga elevada, no es extraño que se produzcan algunos errores. En este caso, el código de estado general es 207, lo que indica un éxito parcial, mientras que los documentos con errores de indexación tendrán establecida la propiedad "status" en false.

> [AZURE.NOTE]Cuando el servicio recibe documentos, se ponen en cola para la indexación y es posible que no se incluyan de forma inmediata en los resultados de búsqueda. Si la carga no es elevada, los documentos se suelen indexar en cuestión de segundos.

Al actualizar un índice, puede combinar varias acciones (insertar, combinar y eliminar) en el mismo lote, acabando con la necesidad de varios recorridos de ida y vuelta. Actualmente, Búsqueda de Azure no admite actualizaciones parciales (PATCH de HTTP), de modo que, si tiene que actualizar un índice, debe reenviar la definición de los índices.

### Integración de datos externos en los resultados de la búsqueda

Búsqueda de Azure usa almacenamiento interno para los índices y los documentos que se utilizan en operaciones de búsqueda. El análisis de texto y de índice depende de la disponibilidad inmediata de todos los campos localizables y atributos asociados.

De todas formas, no todos los campos de un documento se podrán localizar. Por ejemplo, si su aplicación es un catálogo en línea de música o vídeos, recomendamos almacenar archivos binarios en el servicio BLOB de Azure o con otro formato de almacenamiento. Los propios archivos binarios no se pueden localizar, de modo que no hay necesidad de guardarlos en el almacenamiento de Búsqueda de Azure. Aunque debería almacenar imágenes, vídeos y archivos de audio en otros servicios o ubicaciones, debe incluir un campo que haga referencia a la dirección URL de la ubicación del archivo. De este modo, puede recuperar los datos externos como parte de los resultados de búsqueda.

Para utilizar datos externos, tiene que definir un campo en el índice que almacena un puntero de dirección URL al archivo de datos externo. Si emite una solicitud de [búsqueda de documento](https://msdn.microsoft.com/library/dn798929.aspx) o incluye el campo en los resultados de búsqueda, el archivo binario aparece en el contexto de un documento.

### Planificación de capacidad

Una de la características más atractivas en Búsqueda de Azure es la facilidad con la que se puede ampliar o reducir los recursos en respuesta a la demanda. Aunque esta funcionalidad no elimina la necesidad de una planificación de capacidad, minimiza la mayoría de los riesgos. No estará limitado por el hardware adicional o el hardware equivocado para ejecutar las cargas de trabajo de búsqueda.

Como último paso, revise los niveles de recursos existentes para réplicas y particiones, y determine si se necesitan ajustes. La manera más fácil de ajustar la capacidad está en el [Portal de Azure](https://ms.portal.azure.com/).

Recuerde que solo el nivel de precios estándar se pueden escalar o reducir. Además, según el grado de ajuste, puede tardar desde unos minutos hasta varias horas para implementar clústeres adicionales para el servicio.

> [AZURE.NOTE]La capacidad se puede ajustar mediante programación usando la API de REST de administración. Para obtener más información, consulte [la API de REST de administración](https://msdn.microsoft.com/library/azure/dn832684.aspx).


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png

<!---HONumber=Sept15_HO2-->