---
title: Carga de datos de SQL Server en Azure SQL Data Warehouse (bcp) | Microsoft Docs
description: "Para datos de tamaño pequeño, utiliza bcp para exportar datos de SQL Server a archivos planos e importar los datos directamente en Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: f87d8d7c-8276-43c5-90e7-d4ccc0e3a224
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: dae7b5f7456f4ec0daf60d55f9c38b780896ff83
ms.sourcegitcommit: b5c6197f997aa6858f420302d375896360dd7ceb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/21/2017
---
# <a name="load-data-from-sql-server-into-azure-sql-data-warehouse-flat-files"></a>Carga de datos de SQL Server en Azure SQL Data Warehouse (archivos planos)
> [!div class="op_single_selector"]
> * [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
> * [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
> * [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)
> 
> 

Para conjuntos de datos pequeños, se puede utilizar la utilidad de línea de comandos bcp para exportar los datos de SQL Server y, a continuación, cargarlos directamente a Azure SQL Data Warehouse.

En este tutorial, usará bcp para:

* Exportar una tabla de SQL Server mediante el comando bcp out (o crear un archivo de ejemplo simple).
* Importar la tabla de un archivo plano a SQL Data Warehouse.
* Crear estadísticas de los datos cargados.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Loading-data-into-Azure-SQL-Data-Warehouse-with-BCP/player]
> 
> 

## <a name="before-you-begin"></a>Antes de empezar
### <a name="prerequisites"></a>Requisitos previos
Para seguir paso a paso este tutorial, necesita:

* Una base de datos de SQL Data Warehouse
* La utilidad de línea de comandos bcp instalada
* La utilidad de línea de comandos sqlcmd instalada

Puede descargar las utilidades bcp y SQLCMD del [Centro de descarga de Microsoft][Microsoft Download Center].

### <a name="data-in-ascii-or-utf-16-format"></a>Datos en los formatos ASCII o UTF-16
Si va a probar este tutorial con sus propios datos, estos deben utilizar la codificación ASCII o UTF-16, ya que bcp no admite UTF-8. 

PolyBase admite UTF-8, pero aún no es compatible con UTF-16. Tenga en cuenta que si desea combinar bcp con PolyBase, será preciso que transforme los datos al formato UTF-8 después de que se exportan desde SQL Server. 

## <a name="1-create-a-destination-table"></a>1. Creación de una tabla de destino.
Defina en SQL Data Warehouse una tabla que será la tabla de destino de la carga. Las columnas de la tabla deben corresponder con los datos de cada fila del archivo de datos.

Para crear una tabla, abra un símbolo del sistema y use sqlcmd.exe para ejecutar el comando siguiente:

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


## <a name="2-create-a-source-data-file"></a>2. Creación de un archivo de datos de origen
Abra el Bloc de notas y copie las líneas de datos siguientes en un nuevo archivo de texto y, después, guarde este archivo en el directorio temporal local, C:\Temp\DimDate2.txt. Estos datos están en formato ASCII.

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

(Opcional) Para exportar sus propios datos desde una base de datos de SQL Server, abra un símbolo del sistema y ejecute el comando siguiente. Reemplace TableName, ServerName, DatabaseName, Username y Password por su propia información.

```sql
bcp <TableName> out C:\Temp\DimDate2_export.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <Password> -q -c -t ','
```



## <a name="3-load-the-data"></a>3. Carga de los datos
Para cargar los datos, abra un símbolo del sistema y ejecute el comando siguiente, pero reemplace los valores de nombre de servidor, nombre de base de datos, nombre de usuario y contraseña por su propia información.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilice este comando para comprobar que los datos se cargaron correctamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

El resultado debería ser similar a este:

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

## <a name="4-create-statistics"></a>4. Creación de estadísticas
Azure SQL Data Warehouse aún no admite la creación o actualización automáticas de estadísticas. Para obtener el mejor rendimiento de las consultas, es importante crear estadísticas de todas las columnas de todas las tablas después de la primera carga, o bien después de que se realicen cambios importante en los datos. Para ver una explicación detallada de las estadísticas, vea [Estadísticas][Statistics]. 

Ejecute el siguiente comando para crear estadísticas en la tabla recién cargada.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## <a name="5-export-data-from-sql-data-warehouse"></a>5. Exportación de datos de SQL Data Warehouse
Puede exportar los datos que acaba de cargar otra vez al SQL Data Warehouse.  El comando para realizar la exportación es exactamente el mismo que el que se usa para exportar desde SQL Server.

Sin embargo, hay una diferencia en los resultados. Dado que los datos se almacenan en ubicaciones distribuidas en SQL Data Warehouse, al realizar la exportación de datos, cada nodo de ejecución escribe sus datos en el archivo de salida. Es probable que el orden de los datos en el archivo de salida sea diferente del orden de los datos en el archivo de entrada.

### <a name="export-a-table-and-compare-exported-results"></a>Exportación de una tabla y comparación de los resultados exportados
Para ver los datos exportados, abra un símbolo del sistema y ejecute este comando con sus propios parámetros. ServerName es el nombre del servidor SQL Server lógico de Azure.

```sql
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Puede comprobar que los datos se exportaron correctamente abriendo el nuevo archivo. Los datos del archivo deben coincidir con el texto siguiente, pero es probable su orden sea diferente:

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

### <a name="export-the-results-of-a-query"></a>Exportación de los resultados de una consulta
Puede usar la función **queryout** de bcp para exportar los resultados de una consulta, en lugar de exportar toda la tabla. 

## <a name="next-steps"></a>Pasos siguientes
Para obtener información general sobre la carga, vea [Carga de datos en SQL Data Warehouse][Load data into SQL Data Warehouse].
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de SQL Data Warehouse][SQL Data Warehouse development overview].
Consulte la [información general sobre las tablas][Table Overview] o la [sintaxis de CREATE TABLE][CREATE TABLE syntax] para obtener más información sobre cómo crear una tabla en SQL Data Warehouse.

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
