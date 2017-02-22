---
title: "Selección de filas que se van a migrar para Stretch Database: Azure | Microsoft Docs"
description: "Aprenda a seleccionar filas para migrar mediante una función de filtro."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: f5ef79d9-68ef-4394-a057-d7aac5706b72
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/28/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: bcb0a66425439522e0c9a353798ac70505b91e39
ms.openlocfilehash: fc27cd6e25de8e5c3e6b50a0d887755f70d634fd


---
# <a name="select-rows-to-migrate-by-using-a-filter-function-stretch-database"></a>Selección de las filas que se van a migrar mediante una función de filtro (Stretch Database)
Si almacena los datos inactivos en una tabla independiente, puede configurar Stretch Database para migrar toda la tabla. Por otro lado, si la tabla contiene datos activos e inactivos, puede especificar una función de filtro para seleccionar las filas que se migrarán. El predicado de filtro es una función con valores de tabla insertada. Este tema describe cómo escribir una función con valores de tabla insertada para seleccionar las filas que se van a migrar.

> [!NOTE]
> Si proporciona una función de filtro con un rendimiento insuficiente, la migración de datos también lo tendrá. Stretch Database aplica la función de filtro a la tabla con el operador CROSS APPLY.
>
>

Si no especifica ninguna función de filtro, se migrará toda la tabla.

Cuando ejecute el asistente Habilitar la base de datos para Stretch, puede migrar una tabla entera o puede especificar una simple función de filtro basada en el asistente. Si quiere usar otro tipo de función de filtro para seleccionar las filas que se van a migrar, realice una de las siguientes acciones.

* Salga del asistente y ejecute la instrucción ALTER TABLE para habilitar Stretch en la tabla y especificar una función de filtro.
* Ejecute la instrucción ALTER TABLE para especificar una función de filtro después de salir del asistente.

La sintaxis de ALTER TABLE para la adición de una función se describe más adelante en este tema.

## <a name="basic-requirements-for-the-filter-function"></a>Requisitos básicos para la función de filtro
La función con valores de tabla insertada necesaria para un predicado de filtro de Stretch Database es similar al ejemplo siguiente.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datatype1, @column2 datatype2 [, ...n])
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE <predicate>
```
Los parámetros para la función deben tener identificadores para las columnas de la tabla.

La vinculación del esquema es necesaria para evitar que se quiten o modifiquen las columnas que utiliza la función de filtro.

### <a name="return-value"></a>Valor devuelto
Si la función devuelve un resultado no vacío, la fila es apta para la migración. De lo contrario, es decir, si la función no devuelve un resultado, la fila no es apta para migrar.

### <a name="conditions"></a>Condiciones
El &lt;*predicado*&gt; puede constar de una condición o de varias combinadas con el operador lógico AND.

```
<predicate> ::= <condition> [ AND <condition> ] [ ...n ]
```
A su vez, cada condición puede constar de una condición primitiva o de varias condiciones primitivas combinadas con el operador lógico OR.

```
<condition> ::= <primitive_condition> [ OR <primitive_condition> ] [ ...n ]
```

### <a name="primitive-conditions"></a>Condiciones primitivas
Una condición primitiva puede realizar una de las siguientes comparaciones.

```
<primitive_condition> ::=
{
<function_parameter> <comparison_operator> constant
| <function_parameter> { IS NULL | IS NOT NULL }
| <function_parameter> IN ( constant [ ,...n ] )
}
```

* Comparar un parámetro de función con una expresión constante. Por ejemplo: `@column1 < 1000`.

  Este es un ejemplo en el que se comprueba si el valor de una columna *date* es &lt; 1/1/2016.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
  GO

  ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```
* Aplicar el operador IS NULL o IS NOT NULL a un parámetro de función.
* Usar el operador IN para comparar un parámetro de función con una lista de valores constantes.

  En este ejemplo se comprueba si el valor de una columna *shipment\_status* es `IN (N'Completed', N'Returned', N'Cancelled')`.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate(@column1 nvarchar(15))
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 IN (N'Completed', N'Returned', N'Cancelled')
  GO

  ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(shipment_status),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```

### <a name="comparison-operators"></a>Operadores de comparación
Se admiten los siguientes operadores de comparación.

`<, <=, >, >=, =, <>, !=, !<, !>`

```
<comparison_operator> ::= { < | <= | > | >= | = | <> | != | !< | !> }
```

### <a name="constant-expressions"></a>Expresiones constantes
Las constantes que se usan en una función de filtro pueden ser cualquier expresión determinista que se pueda evaluar cuando se defina la función. Las expresiones constantes pueden contener lo siguiente.

* Literales. Por ejemplo: `N’abc’, 123`.
* Expresiones algebraicas. Por ejemplo: `123 + 456`.
* Funciones deterministas. Por ejemplo: `SQRT(900)`.
* Conversiones deterministas que usen CAST o CONVERT. Por ejemplo: `CONVERT(datetime, '1/1/2016', 101)`.

### <a name="other-expressions"></a>Otras expresiones
Puede usar los operadores BETWEEN y NOT BETWEEN si la función resultante se ajusta a las reglas descritas aquí después de reemplazar los operadores BETWEEN y NOT BETWEEN por las expresiones AND y OR equivalentes.

No puede usar subconsultas o funciones no deterministas como RAND() o GETDATE().

## <a name="add-a-filter-function-to-a-table"></a>Incorporación de una función de filtro a una tabla
Agregue una función de filtro a una tabla mediante la ejecución de la instrucción **ALTER TABLE** y la especificación de una función con valores de tabla insertada existente como el valor del parámetro **FILTER\_PREDICATE**. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
) )
```
Después de vincular la función con la tabla como un predicado, se cumple lo siguiente:

* La próxima vez que se produzca la migración de datos, solo se migrarán las filas para las que la función devuelve un valor no vacío.
* Las columnas usadas por la función cuentan con una vinculación de esquema. No puede modificar estas columnas si la tabla usa la función como predicado de filtro.

No puede quitar la función con valores de tabla insertada si la tabla está usando la función como predicado de filtro.

> [!NOTE]
> Para mejorar el rendimiento de la función de filtro, cree un índice en las columnas utilizadas por la función.
>
>

### <a name="passing-column-names-to-the-filter-function"></a>Paso de los nombres de columna a la función de filtro
Al asignar una función de filtro a una tabla, especifique los nombres de columna que se pasan a la función de filtro con un nombre de una parte. Si especifica un nombre de tres partes al pasar los nombres de columna, las posteriores consultas a la tabla habilitada para Stretch darán error.

Por ejemplo, si especifica un nombre de columna de tres partes como se muestra en el ejemplo siguiente, la instrucción se ejecutará correctamente, pero las posteriores consultas a la tabla darán error.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(dbo.SensorTelemetry.ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

En su lugar, especifique la función de filtro con un nombre de columna de una parte, tal como se muestra en el ejemplo siguiente.

```tsql
ALTER TABLE SensorTelemetry
  SET ( REMOTE_DATA_ARCHIVE = ON  (
    FILTER_PREDICATE=dbo.fn_stretchpredicate(ScanDate),
    MIGRATION_STATE = OUTBOUND )
  )
```

## <a name="a-nameaddafterwizaadd-a-filter-function-after-running-the-wizard"></a><a name="addafterwiz"></a>Incorporación de una función de filtro después de ejecutar el asistente
Si desea usar una función que no se puede crear en el asistente **Habilitar la base de datos para Stretch** , puede ejecutar la instrucción ALTER TABLE para especificar una función después de salir del asistente. Para poder aplicar una función, sin embargo, tendrá que detener la migración de datos que ya está en curso y recuperar los datos migrados. (Para más información sobre por qué esto es necesario, consulte [Reemplazo de una función de filtro existente](#replacePredicate).  

1. Invierta la dirección de la migración y recupere los datos ya migrados. Una vez iniciada esta operación, no puede cancelarse. También se le pueden aplicar cargos en Azure por las transferencias de datos de salida \(salida\). Para más información, consulte [cómo funcionan los precios de Azure](https://azure.microsoft.com/pricing/details/data-transfers/).  

    ```tsql  
    ALTER TABLE <table name>  
         SET ( REMOTE_DATA_ARCHIVE ( MIGRATION_STATE = INBOUND ) ) ;   
    ```  
2. Espere a que finalice la migración. Puede comprobar el estado de **Stretch Database Monitor** desde SQL Server Management Studio o bien puede consultar la vista **sys.dm_db_rda_migration_status**. Para más información, consulte [Supervisión y solución de problemas de migración de datos (Stretch Database)](sql-server-stretch-database-monitor.md) o [sys.dm_db_rda_migration_status](https://msdn.microsoft.com/library/dn935017.aspx).  
3. Cree la función de filtro que quiera aplicar a la tabla.  
4. Agregue la función a la tabla y reinicie la migración de datos a Azure.  

    ```tsql  
    ALTER TABLE <table name>  
        SET ( REMOTE_DATA_ARCHIVE  
            (           
                FILTER_PREDICATE = <predicate>,  
                MIGRATION_STATE = OUTBOUND  
            )  
        );   
    ```  

## <a name="filter-rows-by-date"></a>Filtrar filas por fecha
En el ejemplo siguiente se migran las filas cuya columna **date** contiene un valor anterior al 1 de enero de 2016.

```tsql
-- Filter by date
--
CREATE FUNCTION dbo.fn_stretch_by_date(@date datetime2)
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @date < CONVERT(datetime2, '1/1/2016', 101)
GO
```

## <a name="filter-rows-by-the-value-in-a-status-column"></a>Filtrar filas por el valor en una columna de estado
En el ejemplo siguiente se migran las filas cuya columna **status** contiene uno de los valores especificados.

```tsql
-- Filter by status column
--
CREATE FUNCTION dbo.fn_stretch_by_status(@status nvarchar(128))
RETURNS TABLE
WITH SCHEMABINDING
AS
       RETURN SELECT 1 AS is_eligible WHERE @status IN (N'Completed', N'Returned', N'Cancelled')
GO
```

## <a name="filter-rows-by-using-a-sliding-window"></a>Filtrar filas mediante una ventana deslizante
Para filtrar filas mediante una ventana deslizante, tenga en cuenta los siguientes requisitos de la función de filtro.

* La función debe ser determinista. Por lo tanto, no se puede crear una función que recalcule automáticamente la ventana deslizante a medida que pasa el tiempo.
* La función usa enlace de esquema. Por tanto, no puede simplemente actualizar la función "en contexto" cada día mediante una llamada a **ALTER FUNCTION** para mover la ventana deslizante.

Comience con una función de filtro similar al del ejemplo siguiente, que migra las filas cuya columna **systemEndTime** contiene un valor anterior al 1 de enero de 2016.

```tsql
CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160101(@systemEndTime datetime2)
RETURNS TABLE
WITH SCHEMABINDING  
AS  
RETURN SELECT 1 AS is_eligible
  WHERE @systemEndTime < CONVERT(datetime2, '2016-01-01T00:00:00', 101) ;
```

Aplique la función de filtro a la tabla.

```tsql
ALTER TABLE <table name>
SET (
        REMOTE_DATA_ARCHIVE = ON
                (
                        FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160101 (SysEndTime)
                                , MIGRATION_STATE = OUTBOUND
                )
        )
;
```

Si quiere actualizar la ventana deslizante, haga lo siguiente.

1. Cree una nueva función que especifique la nueva ventana deslizante. En el ejemplo siguiente se seleccionan las fechas anteriores al 2 de enero de 2016, en lugar del 1 de enero de 2016.
2. Reemplace la función de filtro anterior por la nueva mediante una llamada a **ALTER TABLE**, tal como se muestra en el ejemplo siguiente.
3. Si quiere, quite la función de filtro anterior que ya no se está usando mediante una llamada a **DROP FUNCTION**. (Este paso no se muestra en el ejemplo).

```tsql
BEGIN TRAN
GO
        /*(1) Create new predicate function definition */
        CREATE FUNCTION dbo.fn_StretchBySystemEndTime20160102(@systemEndTime datetime2)
        RETURNS TABLE
        WITH SCHEMABINDING
        AS
        RETURN SELECT 1 AS is_eligible
               WHERE @systemEndTime < CONVERT(datetime2,'2016-01-02T00:00:00', 101)
        GO

        /*(2) Set the new function as the filter predicate */
        ALTER TABLE <table name>
        SET
        (
               REMOTE_DATA_ARCHIVE = ON
               (
                       FILTER_PREDICATE = dbo.fn_StretchBySystemEndTime20160102(SysEndTime),
                       MIGRATION_STATE = OUTBOUND
               )
        )
COMMIT ;
```

## <a name="more-examples-of-valid-filter-functions"></a>Más ejemplos de funciones de filtro válidas
* El ejemplo siguiente combina dos condiciones primitivas con el operador lógico AND.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate((@column1 datetime, @column2 nvarchar(15))
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
    WHERE @column1 < N'20150101' AND @column2 IN (N'Completed', N'Returned', N'Cancelled')
  GO

  ALTER TABLE table1 SET ( REMOTE_DATA_ARCHIVE = ON (
      FILTER_PREDICATE = dbo.fn_stretchpredicate(date, shipment_status),
      MIGRATION_STATE = OUTBOUND
  ) )
  ```
* En el ejemplo siguiente se usa una conversión determinista y varias condiciones con CONVERT.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example1(@column1 datetime, @column2 int, @column3 nvarchar)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
      WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)AND (@column2 < -100 OR @column2 > 100 OR @column2 IS NULL)AND @column3 IN (N'Completed', N'Returned', N'Cancelled')
  GO
  ```
* El ejemplo siguiente usa las funciones y operadores matemáticos.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example2(@column1 float)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < SQRT(400) + 10
  GO
  ```
* En el ejemplo siguiente se usan los operadores BETWEEN y NOT BETWEEN. Este uso es válido porque la función resultante se ajusta a las reglas descritas aquí después de reemplazar los operadores BETWEEN y NOT BETWEEN por las expresiones AND y OR equivalentes.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example3(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 BETWEEN 0 AND 100
              AND (@column2 NOT BETWEEN 200 AND 300 OR @column1 = 50)
  GO
  ```
  La función anterior es equivalente a la función siguiente después de reemplazar los operadores BETWEEN y NOT BETWEEN por expresiones AND y OR equivalentes.

  ```tsql
  CREATE FUNCTION dbo.fn_stretchpredicate_example4(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 >= 0 AND @column1 <= 100AND (@column2 < 200 OR @column2 > 300 OR @column1 = 50)
  GO
  ```

## <a name="examples-of-filter-functions-that-arent-valid"></a>Ejemplos de funciones de filtro que no son válidas
* La siguiente función no es válida porque contiene una conversión no determinista.

  ```tsql
  CREATE FUNCTION dbo.fn_example5(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < CONVERT(datetime, '1/1/2016')
  GO
  ```
* La siguiente función no es válida porque contiene una llamada de función no determinista.

  ```tsql
  CREATE FUNCTION dbo.fn_example6(@column1 datetime)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 < DATEADD(day, -60, GETDATE())
  GO
  ```
* La siguiente función no es válida porque contiene una subconsulta.

  ```tsql
  CREATE FUNCTION dbo.fn_example7(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 IN (SELECT SupplierID FROM Supplier WHERE Status = 'Defunct'))
  GO
  ```
* Las siguientes funciones no son válidas porque las expresiones que usan operadores algebraicos o funciones incorporadas deben evaluarse en una constante cuando define la función. No puede incluir referencias de columna en expresiones algebraicas o llamadas de función.

  ```tsql
  CREATE FUNCTION dbo.fn_example8(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE @column1 % 2 =  0
  GO

  CREATE FUNCTION dbo.fn_example9(@column1 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE SQRT(@column1) = 30
  GO
  ```
* La siguiente función no es válida porque infringe las reglas descritas aquí después de reemplazar el operador BETWEEN por la expresión AND equivalente.

  ```tsql
  CREATE FUNCTION dbo.fn_example10(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE (@column1 BETWEEN 1 AND 200 OR @column1 = 300) AND @column2 > 1000
  GO
  ```
  La función anterior es equivalente a la función siguiente después de reemplazar el operador BETWEEN por la expresión AND equivalente. Esta función no es válida porque las condiciones primitivas solo pueden usar el operador lógico OR.

  ```tsql
  CREATE FUNCTION dbo.fn_example11(@column1 int, @column2 int)
  RETURNS TABLE
  WITH SCHEMABINDING
  AS
  RETURN    SELECT 1 AS is_eligible
          WHERE (@column1 >= 1 AND @column1 <= 200 OR @column1 = 300) AND @column2 > 1000
  GO
  ```

## <a name="how-stretch-database-applies-the-filter-function"></a>Aplicación de Stretch Database a la función de filtro
Stretch Database aplica la función de filtro a la tabla y determina filas aptas mediante el operador CROSS APPLY. Por ejemplo:

```tsql
SELECT * FROM stretch_table_name CROSS APPLY fn_stretchpredicate(column1, column2)
```
Si la función devuelve un resultado no vacío para la fila, la fila es apta para la migración.

## <a name="a-namereplacepredicateareplace-an-existing-filter-function"></a><a name="replacePredicate"></a>Reemplazo de una función de filtro existente
Puede reemplazar una función de filtro especificada anteriormente ejecutando de nuevo la instrucción **ALTER TABLE** y especificando un nuevo valor para el parámetro **FILTER\_PREDICATE**. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = dbo.fn_stretchpredicate2(column1, column2),
    MIGRATION_STATE = <desired_migration_state>
```
La nueva función con valores de tabla insertada tiene los siguientes requisitos.

* La nueva función tiene que ser menos restrictiva que la función anterior.
* Todos los operadores que existían en la función antigua deben existir en la nueva función.
* La nueva función no puede contener operadores que no existan en la función anterior.
* No se puede cambiar el orden de los argumentos del operador.
* Solo los valores constantes que forman parte de una comparación `<, <=, >, >=` se pueden cambiar de manera que la función sea menos restrictiva.

### <a name="example-of-a-valid-replacement"></a>Ejemplo de un reemplazo válido
Suponga que la función siguiente es el predicado de filtro actual.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_old (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La siguiente función es un reemplazo válido porque la constante de fecha nueva (que especifica una fecha límite posterior) hace que la función sea menos restrictiva.

```tsql
CREATE FUNCTION dbo.fn_stretchpredicate_new (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '2/1/2016', 101)
            AND (@column2 < -50 OR @column2 > 50)
GO
```

### <a name="examples-of-replacements-that-arent-valid"></a>Ejemplos de reemplazos que no son válidos
La siguiente función no es un reemplazo válido porque la constante de fecha nueva (que especifica una fecha límite anterior) no hace que la función sea menos restrictiva.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_1 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2015', 101)
            AND (@column2 < -100 OR @column2 > 100)
GO
```
La siguiente función no es un reemplazo válido porque uno de los operadores de comparación se ha eliminado.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_2 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -50)
GO
```
La siguiente función no es un reemplazo válido porque se ha agregado una nueva condición con el operador lógico AND.

```tsql
CREATE FUNCTION dbo.fn_notvalidreplacement_3 (@column1 datetime, @column2 int)
RETURNS TABLE
WITH SCHEMABINDING
AS
RETURN    SELECT 1 AS is_eligible
        WHERE @column1 < CONVERT(datetime, '1/1/2016', 101)
            AND (@column2 < -100 OR @column2 > 100)
            AND (@column2 <> 0)
GO
```

## <a name="remove-a-filter-function-from-a-table"></a>Eliminación de una función de filtro de una tabla
Para migrar toda la tabla en lugar de las filas seleccionadas, quite la función existente estableciendo **FILTER\_PREDICATE** en null. Por ejemplo:

```tsql
ALTER TABLE stretch_table_name SET ( REMOTE_DATA_ARCHIVE = ON (
    FILTER_PREDICATE = NULL,
    MIGRATION_STATE = <desired_migration_state>
) )
```
Después de quitar la función de filtro, todas las filas de la tabla son aptas para la migración. Como resultado, no puede especificar una función de filtro para la misma tabla más adelante, a menos que primero vuelva a traer todos los datos remotos de la tabla de Azure. Esta restricción existe para evitar la situación en que las filas que no son aptas para la migración, al proporcionar una función de filtro nueva, ya se hayan migrado a Azure.

## <a name="check-the-filter-function-applied-to-a-table"></a>Comprobación de la función de filtro aplicada a una tabla
Para comprobar la función de filtro aplicada a una tabla, abra la vista de catálogo **sys.remote\_data\_archive\_tables** y confirme el valor de la columna **filter\_predicate**. Si el valor es null, la tabla completa es elegible para el archivado. Para más información, consulte [sys.remote_data_archive_tables (Transact-SQL)](https://msdn.microsoft.com/library/dn935003.aspx).

## <a name="security-notes-for-filter-functions"></a>Notas de seguridad para las funciones de filtro
Una cuenta comprometida con privilegios db_owner puede realizar las siguientes cosas.  

* Crear y aplicar una función con valores de tabla que consume gran cantidad de recursos del servidor o que espera durante un tiempo prolongado, lo que da lugar a una denegación de servicio.  
* Crear y aplicar una función con valores de tabla que hace posible deducir el contenido de una tabla para la que el usuario tiene denegado explícitamente el acceso de lectura.  

## <a name="see-also"></a>Consulte también
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)



<!--HONumber=Jan17_HO4-->


