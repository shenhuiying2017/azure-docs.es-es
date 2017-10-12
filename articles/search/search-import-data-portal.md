---
title: "Importación de datos en Azure Search en el portal | Microsoft Docs"
description: "Utilice el Asistente para la importación de datos de Azure Search en Azure Portal para rastrear datos de Azure desde Azure Cosmos DB NoSQL, Blob Storage, Table Storage, SQL Database y SQL Server en máquinas virtuales de Azure."
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: Azure Portal
ms.assetid: f40fe07a-0536-485d-8dfa-8226eb72e2cd
ms.service: search
ms.devlang: na
ms.workload: search
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.date: 05/01/2017
ms.author: heidist
ms.openlocfilehash: a3e6dd66197a17bfdc80c04130e198b787692a58
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="import-data-to-azure-search-using-the-portal"></a>Importación de datos en Búsqueda de Azure con el Portal
Azure Portal incluye un asistente para **importar datos** en el panel de Azure Search que le permite cargar datos en un índice. 

  ![Importación de datos en la barra de comandos][1]

Internamente, el asistente configura e invoca un *indexador*, mediante la automatización de varios pasos del proceso de indexación: 

* Conectarse a un origen de datos externo en la misma suscripción de Azure
* Generar un esquema de índice modificable basado en la estructura de los datos de origen
* Cargar documentos JSON en un índice mediante un conjunto de filas recuperado del origen de datos

Puede probar este flujo de trabajo con datos de ejemplo en Azure Cosmos DB. Visite [Introducción a Azure Search en Azure Portal](search-get-started-portal.md) para obtener instrucciones.

> [!NOTE]
> Puede iniciar el asistente para **importar datos** desde el panel de Azure Cosmos DB para simplificar la indexación para ese origen de datos. En el panel de navegación de la izquierda, vaya a **Colecciones** > **Add Azure Search** (Añadir búsqueda de Azure) para empezar.

## <a name="data-sources-supported-by-the-import-data-wizard"></a>Orígenes de datos admitidos por el Asistente para la importación de datos
El Asistente para importación de datos admite los siguientes orígenes de datos: 

* Base de datos SQL de Azure
* Datos relacionales de SQL Server en una máquina virtual de Azure
* Azure Cosmos DB
* Almacenamiento de blobs de Azure
* Almacenamiento de tablas de Azure

Un conjunto de datos plano es una entrada necesaria. Solo se puede importar desde una única tabla, vista de base de datos o estructura de datos equivalente. Debe crear esta estructura de datos antes de ejecutar al asistente.

## <a name="connect-to-your-data"></a>Conexión a los datos
1. Inicie sesión en [Azure Portal](https://portal.azure.com) y abra el panel de servicio. Puede hacer clic en **Más servicios** en la barra de acceso rápido para buscar los "servicios de búsqueda" existentes en la suscripción actual. 
2. Haga clic en **Importar datos** en la barra de comandos para abrir la hoja Importar datos.  
3. Haga clic en **Conectar a los datos** para especificar una definición de origen de datos usada por un indexador. Para los orígenes de datos que se encuentran en la suscripción, el Asistente normalmente puede detectar y leer la información de conexión, así como minimizar los requisitos de configuración general.

|  |  |
| --- | --- |
| **Origen de datos existente** |Si ya tiene indexadores definidos en el servicio de búsqueda, puede seleccionar una definición de origen de datos existente para otra importación. |
| **Base de datos SQL de Azure** |Se puede especificar el nombre del servicio, las credenciales de un usuario de base de datos con permiso de lectura y un nombre de base de datos en la página o a través de una cadena de conexión de ADO.NET. Elija la opción de cadena de conexión para ver o personalizar las propiedades. <br/><br/>En la página debe especificarse la tabla o vista que proporciona el conjunto de filas. Esta opción aparece una vez realizada correctamente la conexión, lo que proporciona una lista desplegable para que pueda realizar una selección. |
| **SQL Server en máquinas virtuales de Azure** |Especifique un nombre de servicio completo, el identificador de usuario y la contraseña, y la base de datos como una cadena de conexión. Para utilizar este origen de datos, debe haber instalado un certificado en el almacén local que cifra la conexión. Para obtener instrucciones, consulte [Conexión de máquina virtual de SQL a Azure Search](search-howto-connecting-azure-sql-iaas-to-azure-search-using-indexers.md). <br/><br/>En la página debe especificarse la tabla o vista que proporciona el conjunto de filas. Esta opción aparece una vez realizada correctamente la conexión, lo que proporciona una lista desplegable para que pueda realizar una selección. |
| **Azure Cosmos DB** |Los requisitos incluyen la cuenta, la base de datos y la colección. Todos los documentos de la colección se incluirán en el índice. Puede definir una consulta para aplanar o filtrar el conjunto de filas, o para detectar los documentos modificados para las siguientes operaciones de actualización de datos. |
| **Azure Blob Storage** |Los requisitos incluyen la cuenta de almacenamiento y un contenedor. Opcionalmente, si los nombres de blobs siguen una convención de nomenclatura virtual con fines de agrupación, puede especificar la parte del directorio virtual del nombre como una carpeta en el contenedor. Consulte [Indexación de Blob Storage](search-howto-indexing-azure-blob-storage.md) para más información. |
| **Almacenamiento de tablas de Azure** |Los requisitos incluyen la cuenta de almacenamiento y un nombre de tabla. Opcionalmente, puede especificar una consulta para recuperar un subconjunto de las tablas. Consulte [Indexación de Table Storage](search-howto-indexing-azure-tables.md) para más información. |

## <a name="customize-target-index"></a>Personalización del índice de destino
Normalmente, un índice preliminar se deduce del conjunto de datos. Agregue, modifique o elimine campos para completar el esquema. Además, establezca atributos en el nivel de campo para determinar sus comportamientos de búsqueda subsiguientes.

1. En **Personalizar índice de destino**, especifique el nombre y una **clave** utilizados para identificar de forma única cada documento. La clave debe ser una cadena. Si los valores de campo son espacios o guiones, asegúrese de establecer las opciones avanzadas en **Importar sus datos** para suprimir la comprobación de validación para estos caracteres.
2. Revise y modifique los campos restantes. El tipo y el nombre de campo suelen rellenarse automáticamente. Puede cambiar el tipo de datos hasta que se cree el índice. Para cambiarlo posteriormente, será necesario recompilar.
3. Establezca los atributos de índice para cada campo:
   
   * Retrievable devuelve el campo en los resultados de búsqueda.
   * Filterable permite que se haga referencia al campo en las expresiones de filtro.
   * Sortable permite que el campo se use en una ordenación.
   * Facetable permite que el campo se use en la navegación con facetas.
   * Searchable permite la búsqueda de texto completo.
4. Haga clic en la pestaña **Analizador** si desea especificar un analizador de lenguaje en el nivel de campo. Solo se pueden especificar en este momento los analizadores de lenguaje. Si se usa un analizador personalizado o un analizador que no sea de lenguaje, como palabra clave o patrón, se requerirá código.
   
   * Haga clic en **Buscable** para designar la búsqueda de texto completo en el campo y habilitar la lista desplegable del analizador.
   * Elija el analizador que desea usar. Para más información, consulte [Creación de un índice para documentos en varios idiomas en Azure Search](search-language-support.md).
5. Haga clic en **Proveedor de sugerencias** para habilitar las sugerencias de consulta anticipadas en los campos seleccionados.

## <a name="import-your-data"></a>Importar sus datos
1. En **Importar sus datos**, dé un nombre al indexador. Recuerde que el producto del Asistente para la importación de datos es un indexador. Más adelante, si desea verlo o modificarlo, puede seleccionarlo desde el portal en lugar de volver a ejecutar al Asistente. 
2. Especifique la programación, que se basa en la zona horaria regional en la que se aprovisiona el servicio.
3. Establezca opciones avanzadas para especificar los umbrales sobre si la indexación puede continuar si se coloca un documento. Además, puede especificar si los campos de **clave** pueden contener espacios y barras diagonales.  
4. Haga clic en **Aceptar** para crear el índice e importar los datos.

Puede supervisar la indexación en el portal. A medida que se carguen documentos, aumentará el recuento de documentos para el índice que se ha definido. A veces, el portal tarda unos minutos en actualizarse.

El índice está listo para que se realicen consultas en cuanto se cargan todos los documentos.

## <a name="query-an-index-using-search-explorer"></a>Realización de consultas en un índice mediante el Explorador de búsqueda

El portal incluye el **Explorador de búsqueda**, con el fin de que pueda consultar un índice sin tener que escribir código. El [Explorador de búsqueda](search-explorer.md) se puede usar en todos los índices.

La búsqueda se basa en los valores predeterminados, como la [sintaxis simple](https://docs.microsoft.com/rest/api/searchservice/simple-query-syntax-in-azure-search) y el parámetro de consulta searchMode predeterminado (https://docs.microsoft.com/rest/api/searchservice/search-documents). 

Los resultados se devuelven en formato JSON detallado, con el fin de que pueda revisar todo el documento.

## <a name="edit-an-existing-indexer"></a>Edición de un indexador existente
Como se indica, el asistente para la importación de datos crea un **indexador** que se puede modificar como una construcción independiente en el portal.

En el panel de servicios, haga doble clic en el icono del indexador para mostrar una lista de todos los indexadores creados para su suscripción. Haga doble clic en uno de ellos para ejecutarlo, editarlo o eliminarlo. Puede reemplazar el índice con otro existente, cambiar el origen de datos y establecer las opciones de los umbrales de error durante la indexación.

## <a name="edit-an-existing-index"></a>Edición de un índice existente
El asistente también ha creado un **índice**. En Azure Search, las actualizaciones estructurales de un índice requerirán que se recompile el índice. Una recompilación conlleva eliminar el índice, volver a crearlo utilizando un esquema revisado que incluya los cambios deseados y volver a cargar los datos. Las actualizaciones estructurales incluyen el cambio de un tipo de datos y el cambio de nombre o eliminación de un campo.

Entre las modificaciones que no requieren una recompilación se incluyen la incorporación de un nuevo campo, el cambio los perfiles de puntuación, el cambio de los proveedores de sugerencias o el cambio de los analizadores de lenguaje. Consulte [Actualización de un índice](https://msdn.microsoft.com/library/azure/dn800964.aspx) para más información.


## <a name="next-steps"></a>Pasos siguientes
Siga estos vínculos para más información sobre los indexadores:

* [Indexación de Azure SQL Database](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md)
* [Indexación de Azure Cosmos DB](search-howto-index-documentdb.md)
* [Indexación de Blob Storage](search-howto-indexing-azure-blob-storage.md)
* [Indexación de Table Storage](search-howto-indexing-azure-tables.md)

<!--Image references-->
[1]: ./media/search-import-data-portal/search-import-data-command.png

