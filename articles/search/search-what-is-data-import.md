<properties
	pageTitle="Importación de datos a Búsqueda de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Carga de datos en un índice en Búsqueda de Azure"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importación de datos a Búsqueda de Azure
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST API](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Búsqueda de Azure es un servicio de búsqueda hospedado en la nube que proporciona un motor de búsqueda, características de búsqueda y almacenamiento de datos. El servicio funciona con datos persistentes (un índice) que proporciona documentos e información que se usa para procesar un índice, ejecutar consultas o formular los resultados de la búsqueda. Entre estas operaciones de alto nivel h operaciones específicas de bajo nivel para analizar texto, clasificar, filtrar, ordenar, creación de facetas y otras funciones que se usan para calcular o mostrar resultados de la búsqueda.

La relevancia de la experiencia de búsqueda depende de la calidad de los datos que se cargan y la frecuencia con deben actualizarse. En este artículo, presentaremos los enfoques de inserción y extracción (rastreo) para importar y actualizar un índice.

Para poder importar datos, el índice debe existir, y los datos que se cargan deben cumplir el esquema. Consulte [Índices de Búsqueda de Azure](search-what-is-an-index.md) para obtener más información.

##Consideraciones sobre el conjunto de datos

No existen restricciones sobre el tipo de datos que se cargan, siempre que el esquema y los conjuntos de datos se formulen como estructuras JSON.

Los datos que se cargan deben originarse desde cualquier base de datos u origen de datos que su aplicación personalizada cree o consuma. Por ejemplo, si la aplicación es un catálogo de venta minorista en línea, el índice que se crea para Búsqueda de Azure debe dibujar datos del inventario de productos o las bases de datos de ventas admitidos por la aplicación.

Un índice de Búsqueda de Azure obtiene datos de una sola tabla, vista, contenedor de blobs o equivalente. Puede ser necesario crear una estructura de datos en la base de datos o aplicación no SQL que proporciona los datos en Búsqueda de Azure. O bien, para algunos orígenes de datos como Base de datos de SQL Azure o DocumentDB, puede crear un indexador que rastrea datos en una tabla externa, una vista o un contenedor de blob para cargarlos en Búsqueda de Azure.

##Requisitos de sincronización de datos y latencia

La tabla siguiente es un resumen de los requisitos y recomendaciones más comunes.

|Requisitos|Recomendaciones|
|------------|---------------|
|Sincronización de datos casi en tiempo real|Código .NET o API de REST para insertar las actualizaciones en un índice. Un enfoque de extracción para la ingesta de datos es una operación programada, que no se puede ejecutar lo suficientemente rápido para mantener los cambios rápidos en un origen de datos principal.|
|Base de datos SQL de Azure, DocumentDB o SQL Server en máquinas virtuales de Azure|Los indexadores están unidos a tipos de orígenes de datos específicos. Si los orígenes de datos principales están en un origen de datos admitido, puede usar un indexador para rastrear el origen de datos y programar la actualización de datos tan a menudo como intervalos de horas. Puede configurar un indexador en el portal o en código.|
|Actualización de datos programada|Use un indexador (consulte más arriba).|
|Rastreador (crawler)|Use un indexador (consulte más arriba).|
|Creación de prototipos sin código ni edición|El portal incluye a un Asistente para la importación de datos que configura un indexador; para ello, a veces genera un esquema preliminar si hay suficiente información en la base de datos principal para hacerlo. El asistente incluye opciones para configurar la actualización de datos programada. Si lo desea, puede agregar los analizadores de lenguaje u opciones de CORS. Existen algunos aspectos negativos: no se puede agregar perfiles de puntuación, ni tampoco puede exportar un esquema creado en el portal a un archivo JSON para su uso en el código.| 

<!---HONumber=Nov15_HO3-->