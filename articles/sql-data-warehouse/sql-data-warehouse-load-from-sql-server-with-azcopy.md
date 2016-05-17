<properties
   pageTitle="Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (PolyBase) | Microsoft Azure"
   description="Usa bcp para exportar datos de SQL Server a archivos planos, AZCopy para importar datos al Almacenamiento de blobs de Azure y PolyBase para introducir los datos en Almacenamiento de datos SQL de Azure."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/08/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (AZCopy)

Use las utilidades de la línea de comandos bcp y AZCopy para cargar datos de SQL Server en el Almacenamiento de blobs de Azure. Después, use PolyBase o Data Factory de Azure para cargar los datos en Almacenamiento de datos SQL de Azure.


## Requisitos previos

Para seguir paso a paso este tutorial, necesita:

- Una base de datos de Almacenamiento de datos SQL
- La utilidad de línea de comandos bcp instalada
- La utilidad de línea de comandos SQLCMD instalada

>[AZURE.NOTE] Puede descargar las utilidades bcp y SQLCMD del [Centro de descarga de Microsoft][].

## Importación de datos en Almacenamiento de datos SQL

En este tutorial, creará una tabla en Almacenamiento de datos SQL de Azure e importará datos en la tabla.

### Paso 1: Crear una tabla en Almacenamiento de datos SQL de Azure

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

>[AZURE.NOTE] Consulte [Diseño de tablas en el Almacenamiento de datos SQL][] o la [sintaxis de CREATE TABLE][] para más información sobre cómo crear una tabla en Almacenamiento de datos SQL y las opciones disponibles en la cláusula WITH.

### Paso 2: Crear un archivo de datos de origen

Abra el Bloc de notas y copie las líneas de datos siguientes en un nuevo archivo de texto y, después, guarde este archivo en el directorio temporal local, C:\\Temp\\DimDate2.txt.

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

> [AZURE.NOTE] Es importante recordar que bcp.exe no admite la codificación de archivos UTF-8. Use archivos ASCII o archivos codificados UTF-16 al usar bcp.exe.

### Paso 3: Conectar e importar los datos
Con bcp, puede conectarse e importar los datos con el comando siguiente, reemplazando los valores según corresponda:

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Puede comprobar si los datos se cargaron mediante la ejecución de la siguiente consulta usando sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

Esto debe devolver los siguientes resultados:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### Paso 4: Crear estadísticas de los datos recién cargados

Almacenamiento de datos SQL de Azure todavía no permite crear ni actualizar automáticamente las estadísticas. Con la finalidad de obtener el mejor rendimiento a partir de las consultas, es importante crear estadísticas en todas las columnas de todas las tablas después de la primera carga o después de que se realiza cualquier cambio importante en los datos. Si desea ver una explicación detallada de las estadísticas, consulte el tema [Estadísticas][] en el grupo de temas relacionados con el desarrollo. A continuación, puede ver un ejemplo rápido de cómo crear estadísticas sobre los datos cargados y organizados en tablas que aparecen en este ejemplo.

Ejecute las siguientes instrucciones CREATE STATISTICS desde un símbolo del sistema sqlcmd:

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## Exportación de datos de Almacenamiento de datos SQL
En este tutorial, creará un archivo de datos a partir de una tabla de Almacenamiento de datos SQL. Se exportarán los datos creados anteriormente a un nuevo archivo de datos denominado DimDate2\_export.txt.

### Paso 1: Exportar los datos

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

>[AZURE.NOTE] Dada la naturaleza de los sistemas distribuidos, puede que el orden de los datos no sea el mismo en todas las bases de datos de Almacenamiento de datos SQL. Otra opción es usar la función **queryout** de bcp para escribir una extracción de consultas en lugar de exportar toda la tabla.

## Pasos siguientes
Para obtener información general sobre la carga, vea [Carga de datos en Almacenamiento de datos SQL][]. Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][].

<!--Image references-->

<!--Article references-->

[Carga de datos en Almacenamiento de datos SQL]: sql-data-warehouse-overview-load.md
[información general sobre desarrollo de Almacenamiento de datos SQL]: sql-data-warehouse-overview-develop.md
[Diseño de tablas en el Almacenamiento de datos SQL]: sql-data-warehouse-develop-table-design.md
[Estadísticas]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[sintaxis de CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de descarga de Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0511_2016-->