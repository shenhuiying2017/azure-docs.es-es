<properties
   pageTitle="Migración del código SQL a Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para migrar el código SQL a Almacenamiento de datos SQL de Azure a fin de desarrollar soluciones."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="jrowlandjones"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="06/25/2015"
   ms.author="JRJ@BigBangData.co.uk;barbkess"/>

# Migración del código SQL a Almacenamiento de datos SQL

Para asegurarse de que el código es compatible con Almacenamiento de datos SQL, es bastante probable que tenga que realizar cambios en el código base. Algunas características de Almacenamiento de datos SQL también pueden mejorar significativamente el rendimiento, ya que están diseñadas para trabajar directamente en un modo distribuido. Sin embargo, para mantener el rendimiento y la escala, también hay algunas características que no están disponibles.

## Cambios de código de Transact-SQL

En la lista siguiente se resumen las principales características que no se admiten en Almacenamiento de datos SQL de Azure:

- ANSI JOINS en UPDATE
- ANSI JOINS en DELETE
- instrucción MERGE
- combinaciones entre bases de datos
- [instrucciones de dinamización y anulación de dinamización][]
- [cursores][]
- [SELECT..INTO][]
- INSERT..EXEC
- cláusula OUTPUT
- funciones insertadas definidas por el usuario
- funciones de múltiples instrucciones
- expresiones de tabla común recursivas (CTE)
- actualizaciones a través de las CTE
- procedimientos y funciones CLR
- función $partition
- variables de tabla
- parámetros de valor de tabla
- transacciones distribuidas
- trabajo con COMMIT/ROLLBACK
- SAVE TRANSACTION
- contextos de ejecución (EXECUTE AS)
- [cláusula GROUP BY con opciones ROLLUP/CUBE/GROUPING SETS][]
- [anidación de niveles más allá de 8][]
- [actualización a través de vistas][]
- [uso de SELECT para la asignación de variables][]
- [No escriba ningún tipo de datos MAX en cadenas de SQL dinámico][]

Afortunadamente la mayoría de estas limitaciones se puede solucionar. Se incluyen explicaciones en los artículos de desarrollo correspondientes antes mencionados.

También hay algunas funciones del sistema que no son compatibles. Algunas de las principales que normalmente se usan en almacenamiento de datos son:

- NEWID()
- NEWSEQUENTIALID()
- @@NESTLEVEL()
- @@IDENTITY()
- @@ROWCOUNT()
- ROWCOUNT_BIG
- ERROR_LINE()

De nuevo, muchos de estos problemas se pueden solucionar.

Por ejemplo, el código siguiente es una solución alternativa para recuperar información de @@ROWCOUNT:

```
SELECT  SUM(row_count) AS row_count 
FROM    sys.dm_pdw_sql_requests 
WHERE   row_count <> -1 
AND     request_id IN 
                    (   SELECT TOP 1    request_id 
                        FROM            sys.dm_pdw_exec_requests 
                        WHERE           session_id = SESSION_ID() 
                        ORDER BY end_time DESC
                    )
;
``` 

## Pasos siguientes
Para obtener consejos sobre el desarrollo del código, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[instrucciones de dinamización y anulación de dinamización]: sql-data-warehouse-develop-pivot-unpivot.md
[cursores]: sql-data-warehouse-develop-loops.md
[SELECT..INTO]: sql-data-warehouse-develop-ctas.md
[cláusula GROUP BY con opciones ROLLUP/CUBE/GROUPING SETS]: sql-data-warehouse-develop-group-by-options.md
[anidación de niveles más allá de 8]: sql-data-warehouse-develop-transactions.md
[actualización a través de vistas]: sql-data-warehouse-develop-views.md
[uso de SELECT para la asignación de variables]: sql-data-warehouse-develop-variable-assignment.md
[No escriba ningún tipo de datos MAX en cadenas de SQL dinámico]: sql-data-warehouse-develop-dynamic-sql.md
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=July15_HO3-->