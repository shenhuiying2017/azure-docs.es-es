---
title: Transacciones en SQL Data Warehouse | Microsoft Docs
description: Sugerencias para implementar transacciones en el Almacenamiento de datos SQL Azure para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: ae621788-e575-41f5-8bfe-fa04dc4b0b53
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: 29d53e18539f2c24dd64090b2ac6f9dd4c783961
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="transactions-in-sql-data-warehouse"></a>Transacciones en el Almacenamiento de datos SQL
Como cabría esperar, el Almacenamiento de datos SQL admite transacciones como parte de la carga de trabajo de dicho servicio. Sin embargo, para garantizar que se mantiene a escala el rendimiento del Almacenamiento de datos SQL, algunas características están limitadas en comparación con SQL Server. En este artículo se destacan las diferencias entre las características y se enumeran las demás. 

## <a name="transaction-isolation-levels"></a>Niveles de aislamiento de transacciones
El Almacenamiento de datos SQL implementa las transacciones ACID. Sin embargo, el aislamiento de la compatibilidad transaccional está limitado a `READ UNCOMMITTED` y no puede cambiarse. Puede implementar una serie de métodos para evitar lecturas de datos sucios si esto le plantea alguna preocupación. Los métodos más populares utilizan CTAS y la modificación de particiones de tabla (que suele conocerse como un patrón de ventana deslizante) para evitar que los usuarios consulten datos que aún se encuentran en fase de preparación. Las vistas que filtran los datos previamente también constituyen un enfoque popular.  

## <a name="transaction-size"></a>Tamaño de la transacción
Una transacción de modificación de datos única tiene un tamaño limitado. Actualmente, el límite se aplica "por distribución". Por lo tanto, la asignación total puede calcularse multiplicando el límite por el recuento de distribución. Para aproximar el número máximo de filas de la transacción, divida el extremo de la distribución entre el tamaño total de cada fila. Para las columnas de longitud variable, en lugar de utilizar el tamaño máximo, tenga en cuenta la longitud media de la columna.

En la tabla siguiente se han considerado estas hipótesis:

* Se ha producido una distribución uniforme de los datos 
* La longitud media de la fila es de 250 bytes

| [DWU][DWU] | Extremo por distribución (GiB) | Número de distribuciones | Tamaño máximo de la transacción (GiB) | N-º de filas por distribución | Máximo de filas por transacción |
| --- | --- | --- | --- | --- | --- |
| DW100 |1 |60 |60 |4 000 000 |240 000 000 |
| DW200 |1.5 |60 |90 |6.000.000 |360 000 000 |
| DW300 |2.25 |60 |135 |9 000 000 |540 000 000 |
| DW400 |3 |60 |180 |12 000 000 |720 000 000 |
| DW500 |3,75 |60 |225 |15 000 000 |900 000 000 |
| DW600 |4.5. |60 |270 |18 000 000 |1 080 000 000 |
| DW1000 |7.5 |60 |450 |30 000 000 |1 800 000 000 |
| DW1200 |9 |60 |540 |36 000 000 |2 160 000 000 |
| DW1500 |11,25 |60 |675 |45 000 000 |2 700 000 000 |
| DW2000 |15 |60 |900 |60 000 000 |3 600 000 000 |
| DW3000 |22.5 |60 |1,350 |90,000,000 |5,400,000,000 |
| DW6000 |45 |60 |2,700 |180,000,000 |10,800,000,000 |

Se aplica el límite de tamaño de la transacción por transacción u operación. No se aplica en todas las transacciones simultáneas. Por tanto, cada transacción puede escribir esta cantidad de datos en el registro. 

Para optimizar y minimizar la cantidad de datos que se escriben en el registro, consulte el artículo sobre [procedimientos recomendados relacionados con las transacciones][Transactions best practices].

> [!WARNING]
> El tamaño máximo de la transacción solo se puede conseguir para las tablas de distribución HASH o ROUND_ROBIN donde la propagación de los datos es uniforme. Si la transacción está escribiendo datos de forma sesgada en las distribuciones, es posible que el límite se alcance antes de que la transacción llegue al máximo de su tamaño.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Estado de las transacciones
El Almacenamiento de datos SQL usa la función XACT_STATE() para notificar una transacción errónea con el valor -2. Esto significa que se ha producido un error en la transacción y que está marcada para reversión únicamente.

> [!NOTE]
> El uso de -2 por la función XACT_STATE para denotar una transacción errónea representa un comportamiento diferente para SQL Server. SQL Server utiliza el valor -1 para representar una transacción no confirmable. SQL Server puede tolerar errores dentro de una transacción sin necesidad de que se marque como no confirmable. Por ejemplo, `SELECT 1/0` producirá un error pero no fuerza una transacción en un estado no confirmable. SQL Server también permite lecturas en la transacción no confirmable. Sin embargo, Almacenamiento de datos SQL no permite hacerlo. Si se produce un error dentro de una transacción de Almacenamiento de datos SQL, especificará automáticamente el estado 2 y no podrá realizar más instrucciones select hasta que la instrucción se haya revertido. Por lo tanto, es importante comprobar el código de aplicación para ver si utiliza XACT_STATE() cuando necesite realizar modificaciones de código.
> 
> 

Por ejemplo, puede que vea una transacción con el siguiente aspecto en SQL Server:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Si deja el código como aparecía anteriormente, obtendrá el siguiente mensaje de error:

Msg 111233, Level 16, State 1, Line 1 111233; La transacción actual se ha anulado y se han revertido los cambios pendientes. Causa: una transacción en estado de solo reversión no se ha revertido explícitamente antes de la instrucción DDL, DML o SELECT.

Tampoco obtendrá el resultado de las funciones ERROR_*.

En el Almacenamiento de datos SQL debe modificarse ligeramente el código:

```sql
SET NOCOUNT ON;
DECLARE @xact_state smallint = 0;

BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(INT,'ABC');
    END TRY
    BEGIN CATCH
        SET @xact_state = XACT_STATE();

        IF @@TRANCOUNT > 0
        BEGIN
            PRINT 'ROLLBACK';
            ROLLBACK TRAN;
        END

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

IF @@TRANCOUNT >0
BEGIN
    PRINT 'COMMIT';
    COMMIT TRAN;
END

SELECT @xact_state AS TransactionState;
```

Ahora se observa el comportamiento esperado. Se administra el error en la transacción y las funciones ERROR_* proporcionan los valores esperados.

Todo lo que ha cambiado es que se tuvo que aplicar `ROLLBACK` de la transacción para leer la información de error en el bloque `CATCH`.

## <a name="errorline-function"></a>Función Error_Line()
También cabe destacar que el Almacenamiento de datos SQL no implementa o admite la función ERROR_LINE(). Si tiene esto en el código, tendrá que eliminarlo para que sea compatible con el Almacenamiento de datos SQL. En su lugar, utilice etiquetas de consulta en el código para implementar una funcionalidad equivalente. Consulte el artículo sobre [USO DE ETIQUETAS][LABEL] para más información sobre esta característica.

## <a name="using-throw-and-raiserror"></a>Uso de THROW y RAISERROR
THROW es la implementación más moderna para producir excepciones en el Almacenamiento de datos SQL, pero también se admite RAISERROR. Sin embargo, hay algunas diferencias a las que se debe prestar atención.

* Los números de mensajes de error definidos no pueden encontrarse en el intervalo 100.000 a 150.000 para THROW.
* Los mensajes de error RAISERROR se fijan en 50.000.
* No se admite el uso de sys.messages.

## <a name="limitiations"></a>Limitaciones
El Almacenamiento de datos SQL tiene algunas otras restricciones relacionadas con las transacciones.

Los pasos son los siguientes:

* Transacciones no distribuidas
* Transacciones anidadas no permitidas
* Puntos de almacenamiento no admitidos
* Sin transacciones con nombre
* Sin transacciones marcadas
* No existe compatibilidad con DDL como el elemento `CREATE TABLE` de una transacción definida por el usuario

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de la optimización de transacciones, consulte [Procedimientos recomendados relacionados con las transacciones][Transactions best practices].  Para más información sobre otros procedimientos recomendados de SQL Data Warehouse, consulte [Procedimientos recomendados para SQL Data Warehouse][SQL Data Warehouse best practices].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md
[development overview]: ./sql-data-warehouse-overview-develop.md
[Transactions best practices]: ./sql-data-warehouse-develop-best-practices-transactions.md
[SQL Data Warehouse best practices]: ./sql-data-warehouse-best-practices.md
[LABEL]: ./sql-data-warehouse-develop-label.md

<!--MSDN references-->

<!--Other Web references-->
