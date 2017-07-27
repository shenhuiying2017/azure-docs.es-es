---
title: "Guía para el uso de PolyBase en SQL Data Warehouse | Microsoft Docs"
description: Directrices y recomendaciones para el uso de PolyBase en escenarios de Almacenamiento de datos SQL.
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: 4757fce1-96b3-48ea-8a51-be1385705f9f
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.date: 6/5/2016
ms.custom: loading
ms.author: cakarst;barbkess
ms.translationtype: Human Translation
ms.sourcegitcommit: 80be19618bd02895d953f80e5236d1a69d0811af
ms.openlocfilehash: 6938b92d8e5b46d908dc5b2155bdfdc89bb1dc8c
ms.contentlocale: es-es
ms.lasthandoff: 06/07/2017



---
# <a name="guide-for-using-polybase-in-sql-data-warehouse"></a>Guía para el uso de PolyBase en Almacenamiento de datos SQL
Esta guía ofrece información práctica para el uso de PolyBase en el Almacenamiento de datos SQL.

Para comenzar, vea el tutorial [Carga de datos con PolyBase][Load data with PolyBase].

## <a name="rotating-storage-keys"></a>Rotación de claves de almacenamiento
De vez en cuando deseará cambiar la clave de acceso para el almacenamiento de blobs por motivos de seguridad.

La manera más elegante de realizar esta tarea es seguir un proceso conocido como "rotar las claves". Puede que haya observado que dispone de dos claves de almacenamiento para su cuenta de almacenamiento de blobs. Esto es para que puede realizar la transición.

La rotación de las claves de la cuenta de almacenamiento de Azure es un proceso sencillo de tres pasos

1. Crear la segunda credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento secundaria
2. Crear el segundo origen de datos externo basado en esta nueva credencial
3. Quitar y crear las tablas externas señalando al nuevo origen de datos externo

Cuando haya migrado todas las tablas externas al nuevo origen de datos externo, puede realizar las tareas de limpieza:

1. Quitar el primer origen de datos externo
2. Quitar la primera credencial de ámbito de base de datos en función de la clave de acceso de almacenamiento principal
3. Iniciar sesión en Azure y volver a generar la clave de acceso principal lista para la próxima vez

## <a name="query-azure-blob-storage-data"></a>Consulta de datos en el almacenamiento de blobs de Azure
Las consultas en tablas externas simplemente usan el nombre de tabla como si fuese una tabla relacional.

```sql
-- Query Azure storage resident data via external table.
SELECT * FROM [ext].[CarSensor_Data]
;
```

> [!NOTE]
> Una consulta en una tabla externa puede producir el error *"Consulta anulada: se alcanzó el umbral de rechazo máximo al leer desde un origen externo"*. Esto indica que sus datos externos contienen registros *con modificaciones* . Un registro de datos se considera "con modificaciones" si los tipos de datos reales o el número de columnas no coincide con las definiciones de columna de la tabla externa o si los datos no se ajustan al formato de archivo externo especificado. Para corregirlo, asegúrese de que la tabla externa y las definiciones de formato de archivo externos son correctas y que los datos externos se ajustan a estas definiciones. En el caso de que un subconjunto de registros de datos externos estén modificados, puede rechazar estos registros para sus consultas mediante las opciones de rechazo en CREATE EXTERNAL TABLE DDL.
> 
> 

## <a name="load-data-from-azure-blob-storage"></a>Carga de datos del almacenamiento de blobs de Azure
En este ejemplo se cargan datos del almacenamiento de blobs de Azure en la base de datos de Almacenamiento de datos SQL.

El almacenamiento directo de datos quita el tiempo de transferencia de datos de las consultas. El almacenamiento de datos con un índice ColumnStore mejora en hasta 10 veces el rendimiento de las consultas en consultas de análisis.

En este ejemplo se usa la instrucción CREATE TABLE AS SELECT para cargar datos. La nueva tabla hereda las columnas designadas en la consulta. Hereda los tipos de datos de esas columnas de la definición de tabla externa.

CREATE TABLE AS SELECT es una instrucción Transact-SQL de alto rendimiento que carga los datos en paralelo a todos los nodos de proceso de su Almacenamiento de datos SQL.  Se desarrolló originalmente para el motor de procesamiento paralelo masivo (MPP) de Analytics Platform System y se incluye ahora en SQL Data Warehouse.

```sql
-- Load data from Azure blob storage to SQL Data Warehouse

CREATE TABLE [dbo].[Customer_Speed]
WITH
(   
    CLUSTERED COLUMNSTORE INDEX
,    DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS
SELECT *
FROM   [ext].[CarSensor_Data]
;
```

Vea [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)].

## <a name="create-statistics-on-newly-loaded-data"></a>Crear estadísticas de los datos recién cargados
Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas.  Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos.  Si desea ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][Statistics] en el grupo de temas relacionados con el desarrollo.  A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.

```sql
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## <a name="export-data-to-azure-blob-storage"></a>Exportación de datos al almacenamiento de blobs de Azure
Esta sección muestra cómo exportar datos desde el Almacenamiento de datos SQL al almacenamiento de blobs de Azure. En este ejemplo se utiliza CREATE EXTERNAL TABLE AS SELECT, que es una instrucción Transact-SQL de alto rendimiento para exportar los datos en paralelo desde todos los nodos de proceso.

En el ejemplo siguiente se crea una tabla externa Weblogs2014 con definiciones de columna y datos de la tabla dbo.Weblogs. La definición de tabla externa se almacena en Almacenamiento de datos SQL y los resultados de la instrucción SELECT se exportan en el directorio "/archive/log2014/" del contenedor de blobs especificado por el origen de datos. Los datos se exportan en el formato de archivo de texto especificado.

```sql
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```
## <a name="isolate-loading-users"></a>Aislar la carga de usuarios
A menudo, es necesario tener varios usuarios que puedan cargar datos en un almacén de datos de SQL. Dado que [CREATE TABLE AS SELECT (Transact-SQL)][CREATE TABLE AS SELECT (Transact-SQL)] requiere permisos de CONTROL en la base de datos, existirán múltiples usuarios con acceso de control en todos los esquemas. Para limitar esto, puede usar la instrucción DENY CONTROL.

Ejemplo: tenemos los esquemas de base de datos schema_A para el departamento A, schema_B para el departamento B y los usuarios de PolyBase user_A y user_B con cargas en los departamentos A y B respectivamente. A ambos se les ha concedido permiso de CONTROL sobre la base de datos.
Ahora, los creadores de los esquemas A y B bloquean dichos esquemas utilizando DENY:

```sql
   DENY CONTROL ON SCHEMA :: schema_A TO user_B;
   DENY CONTROL ON SCHEMA :: schema_B TO user_A;
```   
 Con esto, user_A y user_B estarán ahora bloqueados en el esquema de los otros departamentos.
 


## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo mover datos a SQL Data Warehouse, vea la [información general sobre la migración de datos][data migration overview].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: ./sql-data-warehouse-load-with-bcp.md
[Load data with PolyBase]: ./sql-data-warehouse-get-started-load-with-polybase.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md
[data migration overview]: ./sql-data-warehouse-overview-migrate.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx

[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022.aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026.aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021.aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[INSERT...SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/ms174335.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189522.aspx
[CREATE DATABASE SCOPED CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/mt270260.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/library/ms189450.aspx

<!-- External Links -->

