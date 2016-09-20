<properties
	pageTitle="Importación de datos en Búsqueda de Azure mediante indexadores en el Portal de Azure | Microsoft Azure | Servicio de búsqueda hospedado en la nube"
	description="Utilice el Asistente para la importación de datos de Azure Search en Azure Portal para rastrear datos desde Azure Blob Storage, Table Storage, SQL Database y SQL Server en las máquinas virtuales de Azure."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="jhubbard"
	editor=""
    tags="Azure Portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="08/29/2016"
	ms.author="heidist"/>

# Importación de datos en Búsqueda de Azure con el Portal

El portal de Azure incluye un comando **Importar datos** en el panel de Azure Search que le permite cargar datos en un índice.

  ![Importación de datos en la barra de comandos][1]

Internamente, el asistente configura e invoca un *indexador*, mediante la automatización de varios pasos del proceso de indexación:

- Conectarse a un origen de datos externo en la suscripción actual de Azure
- Generar automáticamente un esquema de índice basado en la estructura de los datos de origen
- Crear documentos basados en un conjunto de filas recuperadas del origen de datos
- Cargar documentos en el índice en el servicio de búsqueda

Puede probar este flujo de trabajo con datos de ejemplo en DocumentDB. Visite [Introducción a Azure Search en Azure Portal](search-get-started-portal.md) para obtener instrucciones.

## Orígenes de datos admitidos por el Asistente para la importación de datos

La automatización de la indexación y las herramientas están disponible para los orígenes de datos siguientes:

- Base de datos SQL de Azure
- Datos relacionales de SQL Server en una máquina virtual de Azure
- DocumentDB de Azure
- Azure Blob Storage (en versión preliminar)
- Azure Table Storage (en versión preliminar)

Un conjunto de datos plano es una entrada necesaria. Solo se puede importar desde una única tabla, vista de base de datos o estructura de datos equivalente. Debe crear esta estructura de datos antes de ejecutar al asistente.

Tenga en cuenta que algunos de los indexadores están todavía en versión preliminar, lo que significa que la definición del indexador está respaldada por la versión en versión preliminar de la API. Consulte la [información general sobre indexadores](search-indexer-overview.md) para más información y vínculos.

## Conexión a los datos

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y abra el panel de servicio. Puede hacer clic en **Servicios de búsqueda** en la barra de acceso rápido para mostrar los servicios existentes en la suscripción actual.

2. Haga clic en **Importar datos** en la barra de comandos para abrir la hoja Importar datos.

3. Haga clic en **Conectar a los datos** para especificar una definición de origen de datos usada por un indexador. Para los orígenes de datos que se encuentran en la suscripción, el Asistente normalmente puede detectar y leer la información de conexión, así como minimizar los requisitos de configuración general.

| | |
|--------|------------|
|**Origen de datos existente** | Si ya tiene indexadores definidos en el servicio de búsqueda, puede seleccionar una definición de origen de datos existente para otra importación.|
|**Base de datos SQL de Azure** | Se puede especificar el nombre del servicio, las credenciales de un usuario de base de datos con permiso de lectura y un nombre de base de datos en la página o a través de una cadena de conexión de ADO.NET. Elija la opción de cadena de conexión para ver o personalizar las propiedades. <br/><br/>En la página debe especificarse la tabla o vista que proporciona el conjunto de filas. Esta opción aparece una vez realizada correctamente la conexión, lo que proporciona una lista desplegable para que pueda realizar una selección.|
|**SQL Server en máquinas virtuales de Azure** | Especifique un nombre de servicio completo, el identificador de usuario y la contraseña, y la base de datos como una cadena de conexión. Para utilizar este origen de datos, debe haber instalado un certificado en el almacén local que cifra la conexión. <br/><br/>En la página debe especificarse la tabla o vista que proporciona el conjunto de filas. Esta opción aparece una vez realizada correctamente la conexión, lo que proporciona una lista desplegable para que pueda realizar una selección.
|**DocumentDB** |Los requisitos incluyen la cuenta, la base de datos y la colección. Todos los documentos de la colección se incluirán en el índice. Puede definir una consulta para aplanar o filtrar el conjunto de filas, o para detectar los documentos modificados para las siguientes operaciones de actualización de datos.|
|**Azure Blob Storage** | Los requisitos incluyen la cuenta de almacenamiento y un contenedor. Opcionalmente, si los nombres de blobs siguen una convención de nomenclatura virtual con fines de agrupación, puede especificar la parte del directorio virtual del nombre como una carpeta en el contenedor. Consulte [Indexación de Blob Storage (versión preliminar)](search-howto-indexing-azure-blob-storage.md) para más información. |
|**Almacenamiento de tablas de Azure** | Los requisitos incluyen la cuenta de almacenamiento y un nombre de tabla. Opcionalmente, puede especificar una consulta para recuperar un subconjunto de las tablas. Consulte [Indexación de Table Storage (versión preliminar)](search-howto-indexing-azure-tables.md) para más información. |

## Personalización del índice de destino

Normalmente, un índice preliminar se deduce del conjunto de datos. Agregue, modifique o elimine campos para completar el esquema. Además, establezca atributos en el nivel de campo para determinar sus comportamientos de búsqueda subsiguientes.

1. En **Personalizar índice de destino**, especifique el nombre y una **clave** utilizados para identificar de forma única cada documento. La clave debe ser una cadena. Si los valores de campo son espacios o guiones, asegúrese de establecer las opciones avanzadas en **Importar sus datos** para suprimir la comprobación de validación para estos caracteres.

2. Revise y modifique los campos restantes. El tipo y el nombre de campo suelen rellenarse automáticamente. Puede cambiar el tipo de datos.

3. Establezca los atributos de índice para cada campo:

 - Retrievable devuelve el campo en los resultados de búsqueda.
 - Filterable permite que se haga referencia al campo en las expresiones de filtro.
 - Sortable permite que el campo se use en una ordenación.
 - Facetable permite que el campo se use en la navegación con facetas.
 - Searchable permite la búsqueda de texto completo.
  
4. Haga clic en la pestaña **Analizador** si desea especificar un analizador de lenguaje en el nivel de campo. Solo se pueden especificar en este momento los analizadores de lenguaje. Si se usa un analizador personalizado o un analizador que no sea de lenguaje, como palabra clave o patrón, se requerirá código.

 - Haga clic en **Buscable** para designar la búsqueda de texto completo en el campo y habilitar la lista desplegable del analizador.
 - Elija el analizador que desea usar. Consulte [Creación de un índice para documentos en varios idiomas](search-language-support.md) para obtener más información.

5. Haga clic en **Proveedor de sugerencias** para habilitar las sugerencias de consulta anticipadas en los campos seleccionados.


## Importación de los datos

1. En **Importar sus datos**, dé un nombre al indexador. Recuerde que el producto del Asistente para la importación de datos es un indexador. Más adelante, si desea verlo o modificarlo, puede seleccionarlo desde el portal en lugar de volver a ejecutar al Asistente.

2. Especifique la programación, que se basa en la zona horaria regional en la que se aprovisiona el servicio.

3. Establezca opciones avanzadas para especificar los umbrales sobre si la indexación puede continuar si se coloca un documento. Además, puede especificar si los campos de **clave** pueden contener espacios y barras diagonales.

## Edición de un indexador existente

En el panel de servicios, haga doble clic en el icono del indexador para mostrar una lista de todos los indexadores creados para su suscripción. Haga doble clic en uno de ellos para ejecutarlo, editarlo o eliminarlo. Puede reemplazar el índice con otro existente, cambiar el origen de datos y establecer las opciones de los umbrales de error durante la indexación.

## Edición de un índice existente

En Búsqueda de Azure, las actualizaciones estructurales de un índice requieren que se realice una recompilación de ese índice, que consiste en eliminar el índice, volver a crearlo y volver a cargar los datos. Las actualizaciones estructurales incluyen el cambio de un tipo de datos y el cambio de nombre o eliminación de un campo.

Entre las modificaciones que no requieren una recompilación se incluyen la incorporación de un nuevo campo, el cambio los perfiles de puntuación, el cambio de los proveedores de sugerencias o el cambio de los analizadores de lenguaje. Consulte [Actualización de un índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) para más información.

## Paso siguiente

Siga estos vínculos para más información sobre los indexadores:

- [Indexación de Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)
- [Indexación de DocumentDB](../documentdb/documentdb-search-indexer.md)
- [Indexación de Blob Storage (versión preliminar)](search-howto-indexing-azure-blob-storage.md)
- [Indexación de Table Storage (versión preliminar)](search-howto-indexing-azure-tables.md)



<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

<!---HONumber=AcomDC_0914_2016-->