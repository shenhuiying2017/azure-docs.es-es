<properties
	pageTitle="Índices en Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="¿Qué es un índice de Búsqueda de Azure y cómo se usa?"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Índices en Búsqueda de Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Búsqueda de Azure es un servicio de búsqueda hospedado en la nube que proporciona un motor de búsqueda, características de búsqueda que permiten una experiencia similar a Google y Bing en la aplicación personalizada, API de .NET y de REST para integrar Búsqueda de Azure con la aplicación web o aplicación de escritorio y almacenamiento de los datos que se pueden buscar como *índices*.

##¿Qué es un índice?

Un *índice* es un almacenamiento persistente de documentos y otras construcciones que usa el servicio Búsqueda de Azure.

Más específicamente, contiene todos los datos que se pueden buscar usados en el procesamiento de un índice, en la ejecución de una consulta o en la devolución de una lista de resultados de búsqueda, una estructura de navegación con facetas o una página de detalles de la aplicación. En Búsqueda de Azure, un índice también contendrá datos que no admiten búsquedas para usarlos en expresiones de filtro o en perfiles de puntuación (que se usan para depurar una consulta o mejorar una puntuación de clasificación de la búsqueda), así como estructuras de ámbito de la aplicación personalizada que se integra con Búsqueda de Azure.

Si está familiarizado con los conceptos de base de datos, un índice es conceptualmente similar a una tabla y los documentos son más o menos equivalentes a las filas de una tabla.

Las secciones principales de un índice, que se describen con más detalle en este artículo, son las siguientes:- Campos, que definen el cuerpo de un *documento*, con atributos para comportamientos específicos - Perfiles de puntuación - Sugerencias - Opciones CORS - Analizador predeterminado

Como parte del aprovisionamiento de un servicio de búsqueda para su uso por la aplicación, deberá crear un índice. La creación de un índice es una tarea que se centra en la definición de su esquema. Como mínimo, consta de la definición de los campos y del establecimiento de los atributos. Opcionalmente, puede definir perfiles de puntuación y sugerencias para mejorar la experiencia de búsqueda.

Para crear un esquema, puede usar el Portal o escribir código que llama al SDK de .NET o a la API de REST.

Cuando ya está definido el esquema y creado el índice, rellenarlo es una operación independiente. Para obtener más información sobre la ingesta de datos después de la creación del índice, consulte [Importación de datos a Búsqueda de Azure](search-what-is-data-import.md).

##¿Cómo se usan los índices en Búsqueda de Azure?

Todas las operaciones relacionadas con datos realizadas con Búsqueda de Azure consumen o devuelven datos de un índice. No hay excepciones: el servicio no puede apuntar a otros orígenes de datos además del índice para devolver datos externos en una respuesta.

Para las aplicaciones que acumulan datos y operan en ellos, como las transacciones de ventas de una aplicación comercial en línea que almacena datos en una base de datos OLTP, un índice de Búsqueda de Azure será un origen de datos adicional en la solución general. El índice es un almacenamiento de datos dedicado usado únicamente por el servicio de búsqueda. El contar con un origen de datos dedicado y próximo al servicio garantiza la coherencia en la búsqueda de resultados, reduce la volatilidad, reduce el número idas y vueltas entre la aplicación y Búsqueda de Azure y mejora el rendimiento general de las operaciones de búsqueda porque no hay competencia por los datos o los recursos.

##Guía para definir un esquema

Los esquemas se crean como estructuras JSON. Debe crear un índice para cada solución personalizada que se integra con Búsqueda de Azure. No se pueden vincular o combinar varios índices, pero puede crear índices complejos que adapten una variedad de escenarios de búsqueda necesarios para la solución. Por ejemplo, si las aplicaciones admiten varios idiomas, la colección de campos del índice podría tener variantes específicas del idioma para cada campo.

Al diseñar el índice, tómese su tiempo en la fase de planeación y reflexione cada decisión. El cambio de un índice después de la implementación implica volver a generar y volver a cargar los datos. Si crea el índice en el código, este paso será mucho más fácil que si lo crea manualmente en el portal.

Es esencial contar con una comprensión sólida de los datos de origen originales para crear un buen esquema. Desea hacer coincidir los tipos de datos, saber qué campo usar como *clave* para que identifique de forma única cada documento en el índice y qué campos se deben exponer en una lista de resultados de búsqueda o página de detalles.

El contenido de imágenes, audio o vídeo debe almacenarse en otro lugar ya el índice incluye un campo representativo con una dirección URL al recurso. Cualquier contenido, especialmente contenido binario, que no se pueda buscar debe almacenarse en otra parte y después hacerse referencia por el identificador URI de un campo en el índice.

Como se puede imaginar, el índice se debe sincronizar periódicamente con otros orígenes de datos usados en la solución. En un catálogo minorista en línea, la base de datos de inventario que captura las transacciones de ventas debe tener la misma SKU, precios y disponibilidad que los datos mostrados a través de los resultados de la búsqueda. Dependiendo de cuánta latencia sea aceptable para su solución, es posible que la sincronización de datos pueda realizarse una vez por semana, una vez al día o en casi en tiempo real mediante escrituras simultáneas tanto en la base de datos de inventario como en un índice de Búsqueda de Azure. [Importación de datos en Búsqueda de Azure](search-what-is-data-import.md) explica las opciones disponibles con detalle.

##Elementos del esquema

Los índices constan de campos de atributos, valores de datos y otras construcciones que conforman cómo se construyen las consultas y los resultados de búsqueda.

- Los campos definen el cuerpo de un documento. Un comerciante en línea deseará documentos para cada SKU, una organización de noticias querrá documentos para cada artículo y un portal infomedia deseará documentos para cada proveedor o escaparate electrónico.
- Los atributos (o propiedades) son anotaciones en el campo que especifican el tipo de datos, la longitud, el valor y los comportamientos permitidos para ese campo. Puede especificar si el campo es un campo de búsqueda, si se puede recuperar u ordenar, campo por campo. También puede especificar qué analizador de lenguaje usar en el nivel de campo. Una *clave* es un campo reservado en el esquema que proporciona un identificador único para cada documento en la colección de campos.
- Los perfiles de puntuación son criterios que se usan para mejorar la clasificación de un resultado de búsqueda que tiene más características de las establecidas en el perfil. Por ejemplo, supongamos que un término de búsqueda coincide con un nombre de producto y con la descripción del producto, quizá quiera que las coincidencias del nombre del producto se clasifiquen en una posición superior que las que se encuentran en la descripción.
- Las sugerencias, también conocidas como autocompletar o consultas de escritura anticipada, se definen como una sección en el índice.
- Pueden especificarse analizadores de lenguaje predeterminados en el nivel de índice de manera global y que se aplican a todos los campos.
- Las opciones de CORS habilitan el scripting entre dominios y también forman parte de una definición de índice.

## Atributos de índice

|**Propiedad**|Descripción|
|------------|-----------|
|**Retrievable**|Establece si el campo se puede devolver en un resultado de búsqueda.|
|**Filterable**|Permite que el campo se use en consultas **$filter**.|
|**Sortable**|Permite que el campo se use como una opción de ordenación.|
|**Facetable**|Permite que un campo se use en una estructura de navegación con facetas para el filtrado autodirigido. Normalmente los campos que contienen valores repetitivos que se pueden usar para agrupar varios documentos (por ejemplo, varios documentos que forman parte de una categoría de servicio o producto único) funcionan mejor como facetas.|
|**Key**|Es el identificador único de cada documento, que se usa para buscar documentos. Todos los índices deben tener una clave. Solo un campo puede ser la clave y se debe establecer en Edm.String.|
|**Searchable**|Marca el campo como campo de búsqueda de texto completo.|

## Almacenamiento en la nube

Dado que Búsqueda de Azure es un servicio de búsqueda totalmente administrado, el almacenamiento de datos se trata como una operación interna. Como programador, es importante entender que no se pueden omitir las API de Búsqueda de Azure para conectarse directamente al índice, ni se puede supervisar el tamaño del índice o el mantenimiento excepto a través de notificaciones del portal o llamadas a API. No hay ninguna funcionalidad para realizar copias de seguridad o restaurar un índice, ni para optimizar o ajustar el rendimiento en el nivel de almacenamiento. En segundo plano, los datos se almacenan como almacenamiento de blobs, pero la mejor manera de pensar en el almacenamiento físico y en la administración de un índice es considerarlos un detalle de implementación que podría cambiar en el futuro. Una de las principales ventajas del servicio es el enfoque sin intervención humana a la administración de datos.

Si los requisitos de almacenamiento de datos o el volumen de consultas cambian con el tiempo, puede aumentar o disminuir la capacidad agregando o moviendo particiones y réplicas. Para obtener más información, consulte [Administración del servicio de Búsqueda en Azure](search-manage.md) o [Límites de servicio](search-limits-quotas-capacity.md).

<!---HONumber=Nov15_HO3-->