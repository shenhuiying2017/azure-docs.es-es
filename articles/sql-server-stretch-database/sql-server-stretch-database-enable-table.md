<properties
	pageTitle="Habilitación de Stretch Database para una tabla | Microsoft Azure"
	description="Obtenga información sobre cómo configurar una tabla para Stretch Database."
	services="sql-server-stretch-database"
	documentationCenter=""
	authors="douglaslMS"
	manager=""
	editor=""/>

<tags
	ms.service="sql-server-stretch-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/17/2016"
	ms.author="douglasl"/>

# Habilitación de Stretch Database para una tabla

Para configurar una tabla para Stretch Database, seleccione **Stretch | Habilitar** en una tabla en SQL Server Management Studio para abrir el asistente para **habilitar una tabla para Stretch**. También puede utilizar Transact-SQL para habilitar Stretch Database en una tabla existente o para crear una nueva tabla con Stretch Database habilitado.

-   Si almacena los datos históricos en una tabla independiente, puede migrar toda la tabla.

-   Si la tabla contiene datos históricos y actuales, puede especificar un predicado de filtro para seleccionar las filas que migrar.

**Requisitos previos**. Si selecciona **Stretch | Habilitar** para una tabla y no ha habilitado todavía Stretch Database para la base de datos, el asistente configura primero la base de datos para Stretch Database. Siga los pasos de [Enable Database for Stretch Wizard](sql-server-stretch-database-wizard.md) (Asistente para habilitar Stretch Database) en lugar de los pasos de este tema.

**Permisos**. La habilitación de Stretch Database en una base de datos o una tabla requiere permisos db\_owner. La habilitación de Stretch Database en una tabla también requiere permisos ALTER en la tabla.

## <a name="EnableWizardTable"></a>Uso del asistente para habilitar Stretch Database en una tabla
**Inicio del asistente**

1.  En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla en la que desee habilitar Stretch.

2.  Haga clic con el botón derecho y seleccione **Stretch** y **Habilitar** para iniciar el asistente.

**Introducción**

Consulte el propósito del asistente y los requisitos previos.

**Selección de tablas de base de datos**

Confirme que se muestra y se selecciona la tabla que desea habilitar.

Puede migrar una tabla entera o puede especificar un simple predicado de filtro basado en la fecha en el asistente. Si desea usar un predicado de filtro diferente para seleccionar filas que migrar, realice una de las siguientes acciones.

-   Salga del asistente y ejecute la instrucción ALTER TABLE para habilitar Stretch para la tabla y especificar un predicado.

-   Ejecute la instrucción ALTER TABLE para especificar un predicado después de salir del asistente.

La sintaxis de ALTER TABLE se describe más adelante en este tema.

**Resumen**

Consulte los valores que escribió y las opciones que seleccionó en el asistente. Después, seleccione **Finalizar** para habilitar Stretch.

**Resultados**

Consulte los resultados.

## <a name="EnableTSQLTable"></a>Uso de Transact-SQL para habilitar Stretch Database en una tabla
Puede habilitar Stretch Database para una tabla existente o crear una nueva tabla con habilitación de Stretch Database mediante Transact-SQL.

### Opciones
Utilice las siguientes opciones al ejecutar CREATE TABLE o ALTER TABLE para habilitar Stretch Database en una tabla.

-   Opcionalmente, use la cláusula `FILTER_PREDICATE = <predicate>` para especificar un predicado y seleccionar las filas que migrar si la tabla contiene tanto datos históricos como actuales. El predicado debe llamar a una función con valores de tabla insertada. Para obtener más información, consulte [Uso de un predicado de filtro para seleccionar filas para migrar (Stretch Database)](sql-server-stretch-database-predicate-function.md). Si no especifica un predicado de filtro, se migra toda la tabla.

    >   [AZURE.NOTE] Si proporciona un predicado de filtro que tiene un rendimiento insuficiente, la migración de datos también lo tendrá. Stretch Database aplica el predicado de filtro a la tabla con el operador CROSS APPLY.

-   Especifique `MIGRATION_STATE = OUTBOUND` para iniciar la migración de datos inmediatamente o `MIGRATION_STATE = PAUSED` para posponer el inicio de la migración de datos.

### Habilitación de Stretch Database para una tabla existente
Para configurar una tabla existente para Stretch Database, ejecute el comando ALTER TABLE.

Este es un ejemplo en el que se migra toda la tabla y en el que la migración de datos se inicia de inmediato.

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
En el ejemplo siguiente se migran solo las filas identificadas mediante la función con valores de tabla insertada `dbo.fn_stretchpredicate` y pospone la migración de datos. Para obtener más información sobre el predicado de filtro, consulte [Uso de un predicado de filtro para seleccionar filas para migrar (Stretch Database)](sql-server-stretch-database-predicate-function.md).

```tsql
ALTER TABLE <table name>
    SET ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

Para obtener más información, consulte [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### Creación de una nueva tabla con Stretch Database habilitado
Para crear una nueva tabla con Stretch Database habilitado, ejecute el comando CREATE TABLE.

Este es un ejemplo en el que se migra toda la tabla y en el que la migración de datos se inicia de inmediato.

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;
```
En el ejemplo siguiente se migran solo las filas identificadas mediante la función con valores de tabla insertada `dbo.fn_stretchpredicate` y pospone la migración de datos. Para obtener más información sobre el predicado de filtro, consulte [Uso de un predicado de filtro para seleccionar filas para migrar (Stretch Database)](sql-server-stretch-database-predicate-function.md).

```tsql
CREATE TABLE <table name> ...
    WITH ( REMOTE_DATA_ARCHIVE = ON (
        FILTER_PREDICATE = dbo.fn_stretchpredicate(date),
        MIGRATION_STATE = PAUSED ) );
```

Para obtener más información, consulte [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).


## Consulte también

[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)

<!---HONumber=AcomDC_0518_2016-->