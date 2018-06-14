---
title: Uso de transacciones en Azure SQL Data Warehouse | Microsoft Docs
description: Sugerencias para implementar transacciones en Azure SQL Data Warehouse para el desarrollo de soluciones.
services: sql-data-warehouse
author: ckarst
manager: craigg-msft
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.component: implement
ms.date: 04/17/2018
ms.author: cakarst
ms.reviewer: igorstan
ms.openlocfilehash: 7fa3d19cc0fca81616969773a40c3d3dbccc4a26
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/19/2018
ms.locfileid: "31596932"
---
# <a name="using-transactions-in-sql-data-warehouse"></a>Uso de transacciones en SQL Data Warehouse
Sugerencias para implementar transacciones en Azure SQL Data Warehouse para el desarrollo de soluciones.

## <a name="what-to-expect"></a>Qué esperar
Como cabría esperar, SQL Data Warehouse admite transacciones como parte de la carga de trabajo de dicho servicio. Sin embargo, para garantizar que se mantiene a escala el rendimiento de SQL Data Warehouse, algunas características están limitadas en comparación con SQL Server. En este artículo se destacan las diferencias entre las características y se enumeran las demás. 

## <a name="transaction-isolation-levels"></a>Niveles de aislamiento de transacciones
SQL Data Warehouse implementa las transacciones ACID. Pero el nivel de aislamiento de la compatibilidad transaccional se limita a READ UNCOMMITTED; este nivel no se puede cambiar. Si READ UNCOMMITTED supone un problema, puede implementar una serie de métodos de codificación para evitar lecturas de datos sucios. Los métodos más populares utilizan CTAS y la modificación de particiones de tabla (a menudo denominada patrón de ventana deslizante) para evitar que los usuarios consulten datos que aún se encuentran en fase de preparación. Las vistas que filtran los datos previamente también constituyen un enfoque popular.  

## <a name="transaction-size"></a>Tamaño de la transacción
Una transacción de modificación de datos única tiene un tamaño limitado. El límite se aplica por distribución. Por lo tanto, la asignación total puede calcularse multiplicando el límite por el recuento de distribución. Para aproximar el número máximo de filas de la transacción, divida el extremo de la distribución entre el tamaño total de cada fila. Para las columnas de longitud variable, plantéese utilizar la longitud media de la columna en lugar del tamaño máximo.

En la tabla siguiente se han considerado estas hipótesis:

* Se ha producido una distribución uniforme de los datos 
* La longitud media de la fila es de 250 bytes

| [DWU](sql-data-warehouse-overview-what-is.md) | Extremo por distribución (GiB) | Número de distribuciones | Tamaño máximo de la transacción (GiB) | N-º de filas por distribución | Máximo de filas por transacción |
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

Para optimizar y minimizar la cantidad de datos que se escriben en el registro, consulte el artículo [Procedimientos recomendados relacionados con las transacciones](sql-data-warehouse-develop-best-practices-transactions.md).

> [!WARNING]
> El tamaño máximo de la transacción solo se puede conseguir para las tablas de distribución HASH o ROUND_ROBIN donde la propagación de los datos es uniforme. Si la transacción está escribiendo datos de forma sesgada en las distribuciones, es posible que el límite se alcance antes de que la transacción llegue al máximo de su tamaño.
> <!--REPLICATED_TABLE-->
> 
> 

## <a name="transaction-state"></a>Estado de las transacciones
SQL Data Warehouse usa la función XACT_STATE() para notificar una transacción errónea con el valor -2. Este valor indica que la transacción no se ha realizado y que solo se marca para reversión.

> [!NOTE]
> El uso de -2 por la función XACT_STATE para denotar una transacción errónea representa un comportamiento diferente para SQL Server. SQL Server utiliza el valor -1 para representar una transacción no confirmable. SQL Server puede tolerar algunos errores en una transacción sin necesidad de que se marque como no confirmable. Por ejemplo, `SELECT 1/0` produciría un error pero no forzaría una transacción a un estado no confirmable. SQL Server también permite lecturas en la transacción no confirmable. Sin embargo, SQL Data Warehouse no permite hacerlo. Si se produce un error dentro de una transacción de SQL Data Warehouse, especificará automáticamente el estado 2 y no podrá realizar más instrucciones select hasta que la instrucción se haya revertido. Por lo tanto, es importante comprobar el código de aplicación para ver si utiliza XACT_STATE() cuando necesite realizar modificaciones de código.
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

El código anterior muestra el siguiente mensaje de error:

Msg 111233, Level 16, State 1, Line 1 111233; La transacción actual se ha anulado y los cambios pendientes se han revertido. Causa: Una transacción en estado de solo reversión no se ha revertido explícitamente antes de una instrucción DDL, DML o SELECT.

Tampoco obtendrá el resultado de las funciones ERROR_*.

En SQL Data Warehouse debe modificarse ligeramente el código:

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

Lo único que ha cambiado es que la operación ROLLBACK de la transacción tenía que producirse antes de la lectura de la información de error en el bloque CATCH.

## <a name="errorline-function"></a>Función Error_Line()
También cabe destacar que SQL Data Warehouse no implementa o admite la función ERROR_LINE(). Si ha incluido esta función en el código, tendrá que quitarla para que sea compatible con SQL Data Warehouse. En su lugar, utilice etiquetas de consulta en el código para implementar una funcionalidad equivalente. Para obtener más información, vea el artículo sobre [etiquetas](sql-data-warehouse-develop-label.md).

## <a name="using-throw-and-raiserror"></a>Uso de THROW y RAISERROR
THROW es la implementación más moderna para producir excepciones en SQL Data Warehouse, pero también se admite RAISERROR. Sin embargo, hay algunas diferencias a las que se debe prestar atención.

* Los números de mensajes de error definidos por el usuario no pueden encontrarse en el intervalo de 100 000 a 150 000 para THROW.
* Los mensajes de error RAISERROR se fijan en 50.000.
* No se admite el uso de sys.messages.

## <a name="limitations"></a>Limitaciones
SQL Data Warehouse tiene algunas otras restricciones relacionadas con las transacciones.

Los pasos son los siguientes:

* Transacciones no distribuidas
* Transacciones anidadas no permitidas
* Puntos de almacenamiento no admitidos
* Sin transacciones con nombre
* Sin transacciones marcadas
* No existe compatibilidad con DDL como el elemento CREATE TABLE de una transacción definida por el usuario

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre la optimización de transacciones, vea [Procedimientos recomendados relacionados con las transacciones](sql-data-warehouse-develop-best-practices-transactions.md). Para obtener más información sobre otros procedimientos recomendados de SQL Data Warehouse, vea [Procedimientos recomendados para Azure SQL Data Warehouse](sql-data-warehouse-best-practices.md).

