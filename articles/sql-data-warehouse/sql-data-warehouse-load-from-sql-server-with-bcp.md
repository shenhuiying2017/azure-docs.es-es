<properties
   pageTitle="Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (bcp) | Microsoft Azure"
   description="Para datos de tamaño pequeño, utiliza bcp para exportar datos de SQL Server a archivos planos e importar los datos directamente en Almacenamiento de datos SQL de Azure."
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
   ms.date="06/30/2016"
   ms.author="lodipalm;barbkess;sonyama"/>


# Carga de datos de SQL Server en Almacenamiento de datos SQL de Azure (archivos planos)

> [AZURE.SELECTOR]
- [SSIS](sql-data-warehouse-load-from-sql-server-with-integration-services.md)
- [PolyBase](sql-data-warehouse-load-from-sql-server-with-polybase.md)
- [bcp](sql-data-warehouse-load-from-sql-server-with-bcp.md)

Para conjuntos de datos pequeños, se puede utilizar la utilidad de línea de comandos bcp para exportar los datos de SQL Server y, a continuación, cargarlos directamente al Almacenamiento de datos SQL Azure.

En este tutorial, usará bcp para:

- Exportar una tabla de SQL Server mediante el comando bcp out (o crear un archivo de ejemplo simple).
- Importar la tabla de un archivo plano a Almacenamiento de datos SQL.
- Crear estadísticas de los datos cargados.

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Antes de empezar

### Requisitos previos

Para seguir paso a paso este tutorial, necesita:

- Una base de datos de Almacenamiento de datos SQL
- La utilidad de línea de comandos bcp instalada
- La utilidad de línea de comandos sqlcmd instalada

Puede descargar las utilidades bcp y SQLCMD del [Centro de descarga de Microsoft][].

### Datos en los formatos ASCII o UTF-16

Si va a probar este tutorial con sus propios datos, estos deben utilizar la codificación ASCII o UTF-16, ya que bcp no admite UTF-8.

PolyBase admite UTF-8, pero aún no es compatible con UTF-16. Tenga en cuenta que si desea combinar bcp con PolyBase, será preciso que transforme los datos al formato UTF-8 después de que se exportan desde SQL Server.


## 1\. Creación de una tabla de destino.

Defina en Almacenamiento de datos SQL una tabla que será la tabla de destino de la carga. Las columnas de la tabla deben corresponder con los datos de cada fila del archivo de datos.

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


## 2\. Creación de un archivo de datos de origen

Abra el Bloc de notas y copie las líneas de datos siguientes en un nuevo archivo de texto y, después, guarde este archivo en el directorio temporal local, C:\\Temp\\DimDate2.txt. Estos datos están en formato ASCII.

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



## 3\. Carga de los datos
Para cargar los datos, abra un símbolo del sistema y ejecute el comando siguiente, pero reemplace los valores de nombre de servidor, nombre de base de datos, nombre de usuario y contraseña por su propia información.

```sql
bcp DimDate2 in C:\Temp\DimDate2.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t  ','
```

Utilice este comando para comprobar que los datos se cargaron correctamente

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "SELECT * FROM DimDate2 ORDER BY 1;"
```

El resultado debería ser similar a este:

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

## 4\. Creación de estadísticas

Almacenamiento de datos SQL de Azure aún no admite la creación o actualización automáticas de estadísticas. Para obtener el mejor rendimiento de las consultas, es importante crear estadísticas de todas las columnas de todas las tablas después de la primera carga, o bien después de que se realicen cambios importante en los datos. Para ver una explicación detallada de las estadísticas, consulte [Estadísticas][].

Ejecute el siguiente comando para crear estadísticas en la tabla recién cargada.

```sql
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I -Q "
    create statistics [DateId] on [DimDate2] ([DateId]);
    create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
    create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
"
```

## 5\. Exportación de datos de Almacenamiento de datos SQL
Puede exportar los datos que acaba de cargar otra vez al Almacenamiento de datos SQL. El comando para realizar la exportación es exactamente el mismo que el que se usa para exportar desde SQL Server.

Sin embargo, hay una diferencia en los resultados. Dado que los datos se almacenan en ubicaciones distribuidas en Almacenamiento de datos SQL, al realizar la exportación de datos, cada nodo de ejecución escribe sus datos en el archivo de salida. Es probable que el orden de los datos en el archivo de salida sea diferente del orden de los datos en el archivo de entrada.

### Exportación de una tabla y comparación de los resultados exportados

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

### Exportación de los resultados de una consulta

Puede usar la función **queryout** de bcp para exportar los resultados de una consulta, en lugar de exportar toda la tabla.

## Pasos siguientes
Para obtener información general sobre la carga, vea [Carga de datos en Almacenamiento de datos SQL][]. Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo de Almacenamiento de datos SQL][]. Consulte la [información general sobre las tablas][] o la [sintaxis CREATE TABLE][] para más información sobre cómo crear una tabla en Almacenamiento de datos SQL.

<!--Image references-->

<!--Article references-->

[Carga de datos en Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-load.md
[información general sobre desarrollo de Almacenamiento de datos SQL]: ./sql-data-warehouse-overview-develop.md
[información general sobre las tablas]: ./sql-data-warehouse-tables-overview.md
[Estadísticas]: ./sql-data-warehouse-tables-statistics.md

<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx
[sintaxis CREATE TABLE]: https://msdn.microsoft.com/library/mt203953.aspx

<!--Other Web references-->
[Centro de descarga de Microsoft]: https://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0706_2016-->