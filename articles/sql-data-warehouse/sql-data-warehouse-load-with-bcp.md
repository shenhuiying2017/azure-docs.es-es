---
title: Uso de bcp para cargar datos en SQL Data Warehouse | Microsoft Docs
description: "Obtenga información sobre qué es bcp y cómo usarlo en escenarios de almacenamiento de datos."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: barbkess
editor: 
ms.assetid: f9467d11-fcd6-4131-a65a-2022d2c32d24
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: get-started-article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 01/22/2018
ms.author: cakarst;barbkess
ms.openlocfilehash: 55211e29149cd334421bd8723d47278a19afbfbb
ms.sourcegitcommit: 9cc3d9b9c36e4c973dd9c9028361af1ec5d29910
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2018
---
# <a name="load-data-with-bcp"></a>Carga de datos con bcp

**[bcp](/sql/tools/bcp-utility.md)** es una utilidad de carga masiva de línea de comandos que permite copiar datos entre SQL Server, archivos de datos y SQL Data Warehouse. Use bcp para importar grandes cantidades de filas en tablas de SQL Data Warehouse o para exportar datos de tablas de SQL Server a archivos de datos. Excepto cuando se usa con la opción queryout, bcp no exige ningún conocimiento de Transact-SQL.

bcp es una forma rápida y sencilla de realizar operaciones de introducción y extracción de datos en una base de datos de SQL Data Warehouse. La cantidad exacta de datos que se recomienda cargar o extracción a través de bcp depende de la conexión de red con Azure.  Las tablas de dimensiones pequeñas se pueden cargar y extraer de inmediato con bcp. Sin embargo, Polybase, no bcp, es la herramienta recomendada para cargar y extraer grandes volúmenes de datos. PolyBase está diseñado para la arquitectura de procesamiento paralelo masivo de SQL Data Warehouse.

Con bcp puede:

* Usar una utilidad de línea de comandos para cargar datos en SQL Data Warehouse.
* Usar una utilidad de línea de comandos para extraer datos de SQL Data Warehouse.

Este tutorial:

* Importa datos en una tabla mediante el comando in de bcp
* Exporta datos de una tabla mediante el comando out de bcp

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="prerequisites"></a>requisitos previos
Para seguir paso a paso este tutorial, necesita:

* Una base de datos de SQL Data Warehouse
* Utilidades de línea de comandos bcp y sqlcmd. Se pueden descargar del [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=36433). 

### <a name="data-in-ascii-or-utf-16-format"></a>Datos en los formatos ASCII o UTF-16
Si va a probar este tutorial con sus propios datos, estos deben utilizar la codificación ASCII o UTF-16, ya que bcp no admite UTF-8. 

PolyBase admite UTF-8, pero aún no es compatible con UTF-16. Para usar bcp para la exportación de datos y, después, PolyBase para su carga, es preciso transformar los datos a UTF-8 después de que se exporten desde SQL Server. 

## <a name="import-data-into-sql-data-warehouse"></a>Importación de datos en SQL Data Warehouse
En este tutorial, creará una tabla en Azure SQL Data Warehouse e importará datos en la tabla.

### <a name="step-1-create-a-table-in-azure-sql-data-warehouse"></a>Paso 1: Crear una tabla en Azure SQL Data Warehouse
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

Para más información acerca de cómo crear una tabla, consulte [Introducción a las tablas](sql-data-warehouse-tables-overview.md) o la sintaxis de [CREATE TABLE](/sql/t-sql/statements/create-table-azure-sql-data-warehouse.md).
 

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
> Es importante recordar que bcp.exe no admite la codificación de archivos UTF-8. Cuando use bcp.exe, utilice archivos ASCII o archivos con codificación UTF-16.
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

La consulta debería devolver los siguientes resultados:

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
Después de cargar los datos, el último paso es crear o actualizar las estadísticas. Esto ayuda a mejorar el rendimiento de las consultas. Para más información, consulte [Estadísticas](sql-data-warehouse-tables-statistics.md). El siguiente ejemplo de sqlcmd crea estadísticas en la tabla que contiene los datos recién cargados.


```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="export-data-from-sql-data-warehouse"></a>Exportación de datos de SQL Data Warehouse
Este tutorial crea un archivo de datos a partir de una tabla de SQL Data Warehouse. Exporta los datos que se importaron en la sección anterior. Los resultados van a un archivo llamado DimDate2_export.txt.

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
> Dada la naturaleza de los sistemas distribuidos, puede que el orden de los datos no sea el mismo en todas las bases de datos de SQL Data Warehouse. Otra opción es utilizar la función **queryout** de bcp para escribir una extracción de consultas en lugar de exportar toda la tabla.
> 
> 

## <a name="next-steps"></a>Pasos siguientes
Para diseñar el proceso de carga, consulte la [Introducción a la carga] (sql-data-warehouse-design-elt-data-loading].  



<!--MSDN references-->



<!--Other Web references-->
[Microsoft Download Center]: https://www.microsoft.com/download/details.aspx?id=36433
