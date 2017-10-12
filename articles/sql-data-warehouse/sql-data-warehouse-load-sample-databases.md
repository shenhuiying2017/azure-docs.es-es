---
title: Carga de datos de ejemplo en SQL Data Warehouse | Microsoft Docs
description: Carga de datos de ejemplo en Almacenamiento de datos SQL
services: sql-data-warehouse
documentationcenter: NA
author: ckarst
manager: jhubbard
editor: 
ms.assetid: e338ecf8-cfee-419b-b7b6-98108d381c62
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: loading
ms.date: 10/31/2016
ms.author: cakarst;barbkess
ms.openlocfilehash: 1e0df958a2f18fe1e988168918e5cfd293f84e64
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="load-sample-data-into-sql-data-warehouse"></a>Carga de datos de ejemplo en Almacenamiento de datos SQL
Siga estos pasos sencillos para cargar la base de datos de ejemplo de Adventure Works en Almacenamiento de datos SQL. Estos scripts utilizan primero sqlcmd para ejecutar instrucciones SQL que crearán tablas y vistas. Cuando se hayan creado las tablas, los scripts usarán bcp para cargar los datos.  Si todavía no tiene instaladas estas herramientas, siga estos vínculos para [instalar bcp][install bcp] e [instalar sqlcmd][install sqlcmd].

## <a name="load-sample-data"></a>Carga de datos de ejemplo
1. Descargue el archivo ZIP de [scripts de ejemplo de Adventure Works para SQL Data Warehouse][Adventure Works Sample Scripts for SQL Data Warehouse].
2. Extraiga los archivos del zip que descargó en un directorio de la máquina local.
3. Edite el archivo extraído aw_create.bat y defina las siguientes variables que se encuentran en la parte superior del archivo.  No deje espacios en blanco entre "=" y el parámetro.  A continuación, puede ver ejemplos del aspecto que deberían tener las modificaciones.
   
    ```
    server=mylogicalserver.database.windows.net
    user=mydwuser
    password=Mydwpassw0rd
    database=mydwdatabase
    ```
4. En un símbolo del sistema de Windows, ejecute el archivo aw_create.bat editado.  Asegúrese de estar en el directorio en el que guardó la versión editada de aw_create.bat.
   Este script le permitirá hacer lo siguiente:
   
   * Anular cualquier tabla o vista de Adventure Works que ya exista en la base de datos.
   * Crear las vistas y tablas de Adventure Works.
   * Cargar cada tabla de Adventure Works con bcp.
   * Validar los recuentos de fila de cada tabla de Adventure Works.
   * Recopilar estadísticas en cada columna de cada tabla de Adventure Works.

## <a name="query-sample-data"></a>Datos de ejemplo de consultas
Una vez que carga algunos datos de ejemplo en Almacenamiento de datos SQL, puede ejecutar rápidamente algunas consultas.  Para ejecutar una consulta, conéctese a la base de datos de Adventure Works que acaba de crear en SQL Data Warehouse con Visual Studio y SSDT, tal como se describe en el documento de [realización de consultas con Visual Studio][query with Visual Studio].

Un ejemplo de una instrucción select simple para obtener toda la información de los empleados:

```sql
SELECT * FROM DimEmployee;
```

Un ejemplo de una consulta más compleja con construcciones como GROUP BY para ver la cantidad total de todas las ventas de cada día:

```sql
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Ejemplos de una instrucción SELECT con una cláusula WHERE para filtrar pedidos desde antes de una fecha determinada:

```
SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
FROM FactInternetSales
WHERE OrderDateKey > '20020801'
GROUP BY OrderDateKey
ORDER BY OrderDateKey;
```

Almacenamiento de datos SQL admite casi todas las construcciones T-SQL compatibles con SQL Server.  Todas las diferencias existentes se registran en nuestra documentación sobre la [migración del código][migrate code].

## <a name="next-steps"></a>Pasos siguientes
Ahora que tuvo la oportunidad de probar algunas consultas con datos de ejemplo, revise cómo [desarrollar][develop], [cargar][load] o [migrar][migrate] a SQL Data Warehouse.

<!--Image references-->

<!--Article references-->
[migrate]: sql-data-warehouse-overview-migrate.md
[develop]: sql-data-warehouse-overview-develop.md
[load]: sql-data-warehouse-overview-load.md
[query with Visual Studio]: sql-data-warehouse-query-visual-studio.md
[migrate code]: sql-data-warehouse-migrate-code.md
[install bcp]: sql-data-warehouse-load-with-bcp.md
[install sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--Other Web references-->
[Adventure Works Sample Scripts for SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip
