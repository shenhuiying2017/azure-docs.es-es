---
title: Uso de bcp para cargar datos en SQL Data Warehouse | Microsoft Docs
description: "Obtenga información sobre qué es bcp y cómo usarlo en escenarios de almacenamiento de datos."
services: sql-data-warehouse
documentationcenter: NA
author: twounder
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: mausher;barbkess
translationtype: Human Translation
ms.sourcegitcommit: c0e2324a2b2e6294df6e502f2e7a0ae36ff94158
ms.openlocfilehash: e368ae8b249fe3c33371794160440e472b0f35e3
ms.lasthandoff: 01/30/2017



---
# <a name="load-data-with-bcp"></a>Carga de datos con BCP
> [!div class="op_single_selector"]
> * [Redgate](sql-data-warehouse-load-with-redgate.md)  
> * [Factoría de datos](sql-data-warehouse-get-started-load-with-azure-data-factory.md)  
> * [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)  
> * [BCP](sql-data-warehouse-load-with-bcp.md)
> 
> 

**[bcp][bcp]** es una utilidad de carga masiva de línea de comandos que permite copiar datos entre SQL Server, archivos de datos y SQL Data Warehouse. Use bcp para importar grandes cantidades de filas en tablas de Almacenamiento de datos SQL o para exportar datos de tablas de SQL Server a archivos de datos. Excepto cuando se usa con la opción queryout, bcp no exige ningún conocimiento de Transact-SQL.

bcp es una forma rápida y sencilla de realizar operaciones de introducción y extracción de datos en una base de datos de Almacenamiento de datos SQL. La cantidad exacta de datos que se recomienda para la carga o extracción mediante bcp dependerá de la conexión de red con el centro de datos de Azure.  Por lo general, las tablas de dimensiones se pueden cargar y extraer fácilmente con bcp; sin embargo, no se recomienda bcp para cargar o extraer grandes volúmenes de datos.  Polybase es la herramienta recomendada para cargar y extraer grandes volúmenes de datos, ya que funciona mejor al aprovechar la arquitectura de procesamiento masivamente paralela de Almacenamiento de datos SQL.

Con bcp puede:

* Usar una utilidad de línea de comandos sencilla para cargar datos en Almacenamiento de datos SQL.
* Usar una utilidad de línea de comandos sencilla para extraer datos de Almacenamiento de datos SQL.

Este tutorial le mostrará cómo:

* Importar datos en una tabla mediante el comando in de bcp
* Importar datos en una tabla mediante el comando out de bcp

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>Requisitos previos
Para seguir paso a paso este tutorial, necesita:

* Una base de datos de Almacenamiento de datos SQL
* La utilidad de línea de comandos bcp instalada
* La utilidad de línea de comandos SQLCMD instalada

> [!NOTE]
> Puede descargar las utilidades bcp y SQLCMD del [Centro de descarga de Microsoft][Microsoft Download Center].
> 
> 

## <a name="import-data-into-sql-data-warehouse"></a>Importación de datos en Almacenamiento de datos SQL
En este tutorial, creará una tabla en Almacenamiento de datos SQL de Azure e importará datos en la tabla.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Paso 1: Crear una tabla en Almacenamiento de datos SQL de Azure
Desde un símbolo del sistema, use sqlcmd para ejecutar la consulta siguiente para crear una tabla en la instancia:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    CREATE TABLE DimDate2
    (
        DateId INT NOT NULL,
        CalendarQuarter TINYINT NOT NULL,
        FiscalQuarter TINYINT NOT NULL
    )
    WITH
    (
        CLUSTERED COLUMNSTORE INDEX,
        DISTRIBUTION = ROUND_ROBIN
    );
"
```

> [!NOTE]
> Consulte la [información general sobre las tablas][Table Overview] o la [sintaxis de CREATE TABLE][CREATE TABLE syntax] para más información sobre cómo crear una tabla en SQL Data Warehouse y las opciones disponibles en la cláusula WITH.
> 
> 

### <a name="step-2-create-a-source-data-file"></a>Paso 2: Crear un archivo de datos de origen
Abra el Bloc de notas y copie las líneas de datos siguientes en un nuevo archivo de texto y, después, guarde este archivo en el directorio temporal local, C:\Temp\DimDate2.txt.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Es importante recordar que bcp.exe no admite la codificación de archivos UTF-8. Use archivos ASCII o archivos codificados UTF-16 al usar bcp.exe.
> 
> 

### <a name="step-3-connect-and-import-the-data"></a>Paso 3: Conectar e importar los datos
Con bcp, puede conectarse e importar los datos con el comando siguiente, reemplazando los valores según corresponda:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Puede comprobar si los datos se cargaron mediante la ejecución de la siguiente consulta usando sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Esto debe devolver los siguientes resultados:

| DateId | CalendarQuarter | FiscalQuarter |
| --- | --- | --- |
| 20150101 |1 |3 |
| 20150201 |1 |3 |
| 20150301 |1 |3 |
| 20150401 |2 |4 |
| 20150501 |2 |4 |
| 20150601 |2 |4 |
| 20150701 |3 |1 |
| 20150801 |3 |1 |
| 20150801 |3 |1 |
| 20151001 |4 |2 |
| 20151101 |4 |2 |
| 20151201 |4 |2 |

### <a name="step-4-create-statistics-on-your-newly-loaded-data"></a>Paso 4: Crear estadísticas de los datos recién cargados
Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Si desea ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][Statistics] en el grupo de temas relacionados con el desarrollo. A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.

Ejecute las siguientes instrucciones CREATE STATISTICS desde un símbolo del sistema sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportación de datos de Almacenamiento de datos SQL
En este tutorial, creará un archivo de datos a partir de una tabla de Almacenamiento de datos SQL. Se exportarán los datos creados anteriormente a un nuevo archivo de datos denominado DimDate2_export.txt.

### <a name="step-1-export-the-data"></a>Paso 1: Exportar los datos
Con la utilidad bcp, puede conectarse y exportar los datos con el comando siguiente, reemplazando los valores según corresponda:

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Puede comprobar que los datos se exportaron correctamente abriendo el nuevo archivo. Los datos del archivo deben coincidir con el texto siguiente:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

> [!NOTE]
> Dada la naturaleza de los sistemas distribuidos, puede que el orden de los datos no sea el mismo en todas las bases de datos de Almacenamiento de datos SQL. Otra opción es utilizar la función **queryout** de bcp para escribir una extracción de consultas en lugar de exportar toda la tabla.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general sobre la carga, consulte [Carga de datos en SQL Data Warehouse][Load data into SQL Data Warehouse].
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][SQL Data Warehouse development overview].

<!--Image references-->

<!--Article references-->

[Load data into SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[SQL Data Warehouse development overview]: ./sql-data-warehouse-overview-develop.md
[Table Overview]: ./sql-data-warehouse-tables-overview.md
[Statistics]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[CREATE TABLE syntax]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433

