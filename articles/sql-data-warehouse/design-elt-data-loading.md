---
title: "Diseñar ELT para Azure SQL Data Warehouse | Microsoft Docs"
description: "Combine las tecnologías necesarias para mover datos a Azure y cargarlos en SQL Data Warehouse para así poder diseñar un proceso de extracción, carga y transformación (ELT) de Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: 2253bf46-cf72-4de7-85ce-f267494d55fa
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 12/12/2017
ms.author: cakarst;barbkess
ms.openlocfilehash: e94dca69c77c46034e318205279be5188e1371f5
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="designing-extract-load-and-transform-elt-for-azure-sql-data-warehouse"></a>Diseñar un proceso de extracción, carga y transformación (ELT) para Azure SQL Data Warehouse

Combine las tecnologías necesarias para guardar datos en Azure Storage y cargarlos en SQL Data Warehouse para así poder diseñar un proceso de extracción, carga y transformación (ELT) de Azure SQL Data Warehouse. En este artículo se detallan las tecnologías que admiten la carga con Polybase y se centra en el diseño de un proceso ELT que usa PolyBase con T-SQL para cargar datos en SQL Data Warehouse desde Azure Storage.

## <a name="what-is-elt"></a>¿Qué es ELT?

Extraer, cargar y transformar (ETL). Este es un proceso mediante el cual los datos se mueven desde un sistema de origen a un almacén de datos de destino. Este proceso se realiza de forma regular como, por ejemplo, cada hora o diariamente, para así poder llevar datos recién generados al almacén de datos. El modo ideal para llevar datos desde un origen al almacén de datos, es desarrollar un proceso ELT que usa PolyBase para cargar datos en SQL Data Warehouse.

El proceso ELT primero carga y transforma los datos, mientras que el proceso ETL (extraer, transformar y cargar) transforma los datos antes de cargarlos. Si realiza el proceso ELT en vez del proceso ETL, se ahorrará el costo que supone tener que proporcionar sus propios recursos para transformar los datos antes de cargarlos. Cuando se utiliza SQL Data Warehouse, el proceso ELT aprovecha el sistema MPP para llevar a cabo las transformaciones.

Aunque existen muchas variaciones de implementación de ELT para SQL Data Warehouse, estos son los pasos básicos:  

1. Extraer los datos de origen en archivos de texto.
2. Llevar los datos a Azure Blob Storage o Azure Data Lake Store.
3. Preparar los datos para la carga.
2. Cargar los datos en las tablas de almacenamiento provisional de SQL Data Warehouse mediante PolyBase.
3. Transformar los datos.
4. Insertar los datos en tablas de producción.


Si desea un tutorial sobre la carga, consulte [Use PolyBase to load data from Azure blob storage to Azure SQL Data Warehouse](load-data-from-azure-blob-storage-using-polybase.md) (Usar PolyBase para cargar datos de Azure Blob Storage en Azure SQL Data Warehouse).

Para obtener más información, consulte [el blog de patrones de carga](http://blogs.msdn.microsoft.com/sqlcat/2017/05/17/azure-sql-data-warehouse-loading-patterns-and-strategies/). 

## <a name="options-for-loading-with-polybase"></a>Opciones para cargar datos con PolyBase

PolyBase es una tecnología que usa el lenguaje de T-SQL para obtener acceso a datos que están fuera de la base de datos. Esta es la mejor manera de cargar datos en SQL Data Warehouse. Gracias a PolyBase, los datos se cargan directamente en paralelo desde el origen de datos a los nodos de proceso. 

Para cargar datos con PolyBase, puede utilizar cualquiera de estas opciones de carga.

- [PolyBase con T-SQL](load-data-from-azure-blob-storage-using-polybase.md) funciona bien cuando los datos están en Azure Blob Storage o Azure Data Lake Store. Le proporciona el máximo control sobre el proceso de carga, pero también es necesario definir objetos de datos externos. Los otros métodos definen estos objetos en segundo plano mientras asigna tablas de origen a las tablas de destino.  Para coordinar la carga de T-SQL, puede utilizar Azure Data Factory, SSIS o Azure Functions. 
- [PolyBase con SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md) funciona bien cuando los datos de origen están en SQL Server, ya sea de forma local o en la nube. SSIS define las asignaciones de la tabla de origen a la de destino y también organiza la carga. Si ya dispone de paquetes SSIS, puede modificar los paquetes con los que vaya a trabajar con el nuevo destino del almacenamiento de datos. 
- [PolyBase con Azure Data Factory (ADF)](sql-data-warehouse-load-with-data-factory.md) es otra herramienta de orquestación.  Define una canalización y programa trabajos. 

### <a name="polybase-external-file-formats"></a>Formatos de archivo externos de PolyBase

PolyBase carga los datos de los archivos de texto delimitados que están codificados mediante UTF-8 y UTF-16. Además de los archivos de texto delimitados, también carga datos desde formatos de archivos Hadoop como RC File, ORC y Parquet. Asimismo, PolyBase puede cargar datos desde Gzip y archivos comprimidos de Snappy. Hay que tener en cuenta que actualmente PolyBase no admite el formato ASCII extendido, el formato de ancho fijo y formatos anidados como WinZip, JSON y XML.

### <a name="non-polybase-loading-options"></a>Opciones de carga que no pertenecen a PolyBase
Si los datos no son compatibles con PolyBase, puede usar [bcp](sql-data-warehouse-load-with-bcp.md) o [SQLBulkCopy API](https://msdn.microsoft.com/library/system.data.sqlclient.sqlbulkcopy.aspx). El formato bcp carga datos directamente a SQL Data Warehouse sin tener que pasar por Azure Blob Storage y está diseñado únicamente para cargas pequeñas. Tenga en cuenta que el rendimiento de la carga de estas opciones es mucho más lento que PolyBase. 


## <a name="extract-source-data"></a>Extraer datos de origen

Depende del origen si quiere obtener datos del sistema de origen.  El objetivo consiste en mover los datos a archivos de texto delimitados. Si está utilizando SQL Server, puede usar la [herramienta de línea de comandos bcp](/sql/tools/bcp-utility) para exportar los datos.  

## <a name="land-data-to-azure-storage"></a>Llevar datos a Azure Storage

Para llevar los datos a Azure Storage, puede moverlos a [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) o [Azure Data Lake Store](../data-lake-store/data-lake-store-overview.md). Los datos deben almacenarse en archivos de texto en ambas ubicaciones. PolyBase puede cargar esos datos desde cualquiera de estas ubicaciones.

Se trata de herramientas y servicios que puede usar para mover datos a Azure Storage.

- El servicio [Azure ExpressRoute](../expressroute/expressroute-introduction.md) mejora el rendimiento de la red, el rendimiento en general y la capacidad de predicción. ExpressRoute es un servicio que enruta los datos a Azure a través de una conexión privada dedicada. Las conexiones ExpressRoute no enrutan datos a través de la red pública de Internet. Estas conexiones son más fiables y ofrecen velocidades más altas, menores latencias y mayor seguridad que las conexiones habituales a través de Internet.
- La [utilidad AZCopy](../storage/common/storage-use-azcopy.md) lleva los datos a Azure Storage a través de la red pública de Internet. Esto funciona si el tamaño de los datos es de menos de 10 TB. Para realizar cargas de forma regular con AZCopy, pruebe la velocidad de la red para ver si es aceptable. 
- [Azure Data Factory (ADF)](../data-factory/introduction.md) tiene una puerta de enlace que se puede instalar en el servidor local. A continuación, puede crear una canalización para llevar los datos desde el servidor local hasta Azure Storage.

Consulte el artículo sobre cómo [mover datos desde y hasta Azure Storage](../storage/common/storage-moving-data.md) para obtener más información.


## <a name="prepare-data"></a>Preparación de los datos

Deberá preparar y limpiar los datos de la cuenta de almacenamiento antes de cargarlos en SQL Data Warehouse. Puede preparar los datos mientras están en el origen y a medida que los exporta a archivos de texto o después de que estén en Azure Storage.  Si trabaja con los datos en la parte inicial el proceso, será más fácil manejarlos.  

### <a name="define-external-tables"></a>Definir tablas externas
Antes de poder cargar los datos, debe definir tablas externas en el almacén de datos. PolyBase emplea tablas externas para obtener acceso y definir los datos en Azure Storage. La tabla externa es similar a una tabla normal. La diferencia principal es que la tabla externa apunta a los datos que se almacenan fuera del almacén de datos. 

Si define tablas externas debe especificar el origen de datos, el formato de los archivos de texto y las definiciones de tabla. Estos son los temas de sintaxis de T-SQL que necesitará:
- [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
- [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql)
- [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

Para obtener un ejemplo de creación de objetos externos, consulte el paso [Crear tablas externas](load-data-from-azure-blob-storage-using-polybase.md#create-external-tables-for-the-sample-data) en el tutorial de carga.

### <a name="format-text-files"></a>Formato de los archivos de texto

Una vez que se definen los objetos externos, es necesario alinear las filas de los archivos de texto con la tabla externa y la definición de formato de archivo. Los datos de cada fila del archivo de texto deben alinearse con la definición de tabla.

Para dar formato a los archivos de texto:

- Si los datos provienen de un origen no relacional, debe transformarlos en filas y columnas. Si los datos son de un origen relacional o no relacional, se deben transformar para alinearlos con las definiciones de columna de la tabla en la que va a cargar los datos. 
- Debe dar formato a datos en el archivo de texto que se alineará con los tipos de datos y columnas en la tabla de destino de aSQL Data Warehouse. Si se desalinean los tipos de datos en los archivos de texto externos y la tabla de almacenamiento de datos, las filas se rechazarán durante la carga.
- Debe separar los campos en el archivo de texto con un terminador.  Asegúrese de usar un carácter o una secuencia de caracteres que no se encuentre en los datos de origen. Use el terminador que especificó con la instrucción [CREATE EXTERNAL FILE FORMAT](/sql/t-sql/statements/create-external-file-format-transact-sql).

## <a name="load-to-a-staging-table"></a>Cargar los datos en una tabla de almacenamiento provisional
Para guardar los datos en el almacén de datos, puede cargarlos primero en una tabla de almacenamiento provisional. Al usar una tabla de almacenamiento provisional, puede controlar los errores sin tener que interferir con las tablas de producción y así evitará la ejecución de operaciones de reversión en la tabla de producción. Asimismo, una tabla de almacenamiento provisional también ofrece la posibilidad de usar SQL Data Warehouse para ejecutar transformaciones antes de insertar los datos en tablas de producción.

Para cargarlos con T-SQL, ejecute la instrucción de T-SQL [CREATE TABLE AS SELECT (CTAS)](/sql/t-sql/statements/create-table-as-select-azure-sql-data-warehouse.md). Este comando inserta los resultados de una instrucción "select" en una tabla nueva. Cuando se selecciona la instrucción de una tabla externa, importa los datos externos. 

En el siguiente ejemplo ext.Date es una tabla externa. Todas las filas se importan en una tabla nueva denominada dbo.Date.

```sql
CREATE TABLE [dbo].[Date]
WITH
( 
    CLUSTERED COLUMNSTORE INDEX
)
AS SELECT * FROM [ext].[Date]
;
```

## <a name="transform-the-data"></a>Transformar los datos
Mientras los datos estén en una tabla de almacenamiento provisional, podrá realizar las transformaciones que sean necesarias para la carga de trabajo. A continuación, lleve los datos a una tabla de producción.

## <a name="insert-data-into-production-table"></a>Insertar los datos en la tabla de producción

La instrucción INSERT INTO ... SELECT lleva los datos de la tabla de almacenamiento provisional a la tabla permanente. 

Cuando diseñe un proceso ETL, intente ejecutar el proceso con una pequeña muestra de prueba. Intente extraer 1000 filas de la tabla a un archivo, muévalo a Azure y, a continuación, intente cargarlo en una tabla de almacenamiento provisional. 

## <a name="partner-loading-solutions"></a>Soluciones de carga de asociados
Muchos de nuestros asociados tienen soluciones de carga. Para obtener más información, consulte una lista de nuestros [asociados de soluciones](sql-data-warehouse-partner-business-intelligence.md). 

## <a name="next-steps"></a>Pasos siguientes
Para obtener una guía de carga, consulte la [guía para cargar datos](guidance-for-loading-data.md).


