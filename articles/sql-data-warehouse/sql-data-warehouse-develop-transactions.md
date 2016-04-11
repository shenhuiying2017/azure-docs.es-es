<properties
   pageTitle="Transacciones en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para implementar transacciones en el Almacenamiento de datos SQL Azure para el desarrollo de soluciones."
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
   ms.date="03/23/2016"
   ms.author="jrj;barbkess;sonyama"/>

# Transacciones en el Almacenamiento de datos SQL

Como cabe esperar, el Almacenamiento de datos SQL ofrece soporte para todas las propiedades transaccionales. Sin embargo, para garantizar que se mantiene a escala el rendimiento del Almacenamiento de datos SQL, algunas características están limitadas en comparación con SQL Server. En este artículo se destacan las diferencias entre las características y se enumeran las demás.

## Niveles de aislamiento de transacciones
El Almacenamiento de datos SQL implementa las transacciones ACID. Sin embargo, el aislamiento de la compatibilidad transaccional está limitado a `READ UNCOMMITTED` y no puede cambiarse. Puede implementar una serie de métodos para evitar lecturas de datos sucios si esto le plantea alguna preocupación. Los métodos más populares utilizan CTAS y la modificación de particiones de tabla (que suele conocerse como un patrón de ventana deslizante) para evitar que los usuarios consulten datos que aún se encuentran en fase de preparación. Las vistas que filtran los datos previamente también constituyen un enfoque popular.

## Tamaño de la transacción
Una transacción de modificación de datos única tiene un tamaño limitado. Actualmente, el límite se aplica "por distribución". Por tanto, para obtener la cifra total debemos multiplicar el límite por el recuento de distribución. Para aproximar el número máximo de filas de la transacción, divida el extremo de la distribución entre el tamaño total de cada columna. Para las columnas de longitud variable, en lugar de utilizar el tamaño máximo, tenga en cuenta la longitud media de la columna.

En la tabla siguiente se han considerado estas hipótesis:
* Se ha producido una distribución uniforme de los datos 
* La longitud media de la fila es de 250 bytes

| DWU | Extremo por distribución (GiB) | Número de distribuciones | Tamaño máximo de la transacción (GiB) | Número de filas por distribución | Máximo de filas por transacción |
| ------ | -------------------------- | ----------------------- | -------------------------- | ----------------------- | ------------------------ |
| DW100 | 1 | 60 | 60 | 4 000 000 | 240 000 000 |
| DW200 | 1\.5 | 60 | 90 | 6\.000.000 | 360 000 000 |
| DW300 | 2\.25 | 60 | 135 | 9 000 000 | 540 000 000 |
| DW400 | 3 | 60 | 180 | 12 000 000 | 720 000 000 |
| DW500 | 3,75 | 60 | 225 | 15 000 000 | 900 000 000 |
| DW600 | 4\.5. | 60 | 270 | 18 000 000 | 1 080 000 000 |
| DW1000 | 7\.5 | 60 | 450 | 30 000 000 | 1 800 000 000 |
| DW1200 | 9 | 60 | 540 | 36 000 000 | 2 160 000 000 |
| DW1500 | 11,25 | 60 | 675 | 45 000 000 | 2 700 000 000 |
| DW2000 | 15 | 60 | 900 | 60 000 000 | 3 600 000 000 |

Se aplica el límite de tamaño de la transacción por transacción u operación. No se aplica en todas las transacciones simultáneas. Por tanto, cada transacción puede escribir esta cantidad de datos en el registro.

Para optimizar y minimizar la cantidad de datos que se escriben en el registro, consulte el artículo sobre [prácticas recomendadas relacionadas con las transacciones][].

> [AZURE.WARNING] El tamaño máximo de la transacción solo se puede conseguir para las tablas de distribución HASH o ROUND\_ROBIN donde la propagación de los datos es uniforme. Si la transacción está escribiendo datos de forma sesgada en las distribuciones, es posible que el límite se alcance antes de que la transacción llegue al máximo de su tamaño.
<!--REPLICATED_TABLE-->

## Estado de las transacciones
El Almacenamiento de datos SQL usa la función XACT\_STATE() para notificar una transacción errónea con el valor -2. Esto significa que se ha producido un error en la transacción y que está marcada para reversión únicamente.

> [AZURE.NOTE] El uso de -2 por la función XACT\_STATE para denotar una transacción errónea representa un comportamiento diferente para SQL Server. SQL Server utiliza el valor -1 para representar una transacción no confirmable. SQL Server puede tolerar errores dentro de una transacción sin necesidad de que se marque como no confirmable. Por ejemplo, SELECT 1/0 producirá un error pero no fuerza una transacción en un estado no confirmable. SQL Server también permite lecturas en la transacción no confirmable. Sin embargo, en SQLDW esto no es así. Si se produce un error dentro de una transacción SQLDW, introducirá automáticamente el estado -2: incluidos los errores SELECT 1/0. Por lo tanto, es importante comprobar el código de aplicación para ver si utiliza XACT\_STATE().

En SQL Server, puede que vea un fragmento de código con el siguiente aspecto:

```sql
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;

        ROLLBACK TRAN;

    END CATCH;
```

Tenga en cuenta que la instrucción `SELECT` se produce antes de la instrucción `ROLLBACK`. Tenga en cuenta también que la configuración de la variable `@xact` utiliza DECLARE y no `SELECT`.

En el Almacenamiento de datos SQL, el código tendría este aspecto:

```sql
BEGIN TRAN
    BEGIN TRY
        DECLARE @i INT;
        SET     @i = CONVERT(int,'ABC');
    END TRY
    BEGIN CATCH

        ROLLBACK TRAN;

        DECLARE @xact smallint = XACT_STATE();

        SELECT  ERROR_NUMBER()    AS ErrNumber
        ,       ERROR_SEVERITY()  AS ErrSeverity
        ,       ERROR_STATE()     AS ErrState
        ,       ERROR_PROCEDURE() AS ErrProcedure
        ,       ERROR_MESSAGE()   AS ErrMessage
        ;
    END CATCH;

SELECT @xact;
```

Observe que la reversión de la transacción debe ocurrir antes de la lectura de la información de error en el bloque `CATCH`.

## Función Error\_Line()
También cabe destacar que el Almacenamiento de datos SQL no implementa o admite la función ERROR\_LINE(). Si tiene esto en el código, tendrá que eliminarlo para que sea compatible con el Almacenamiento de datos SQL. En su lugar, utilice etiquetas de consulta en el código para implementar una funcionalidad equivalente. Consulte el artículo [etiquetas de consulta] para obtener más detalles sobre esta característica.

## Uso de THROW y RAISERROR
THROW es la implementación más moderna para producir excepciones en el Almacenamiento de datos SQL, pero también se admite RAISERROR. Sin embargo, hay algunas diferencias a las que se debe prestar atención.

- Los números de mensajes de error definidos no pueden encontrarse en el intervalo 100.000 a 150.000 para THROW.
- Los mensajes de error RAISERROR se fijan en 50.000.
- No se admite el uso de sys.messages.

## Limitaciones
El Almacenamiento de datos SQL tiene algunas otras restricciones relacionadas con las transacciones.

Los pasos son los siguientes:

- Transacciones no distribuidas
- Transacciones anidadas no permitidas
- Puntos de almacenamiento no admitidos

## Pasos siguientes
Para obtener más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][].

<!--Image references-->

<!--Article references-->
[información general sobre desarrollo]: sql-data-warehouse-overview-develop.md
[prácticas recomendadas relacionadas con las transacciones]: sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->

<!--Other Web references-->

<!---HONumber=AcomDC_0330_2016-->