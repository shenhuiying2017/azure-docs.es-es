---
title: "Habilitación de Stretch Database para una tabla | Microsoft Docs"
description: "Obtenga información sobre cómo configurar una tabla para Stretch Database."
services: sql-server-stretch-database
documentationcenter: 
author: douglaslMS
manager: jhubbard
editor: 
ms.assetid: 63724114-82c1-4b00-ac50-c3ade6a69d47
ms.service: sql-server-stretch-database
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/05/2016
ms.author: douglasl
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 3c9aa9a1abb4f436cb44e67d236682ff58a92531


---
# <a name="enable-stretch-database-for-a-table"></a>Habilitación de Stretch Database para una tabla
Para configurar una tabla para Stretch Database, seleccione **Stretch | Habilitar** en una tabla de SQL Server Management Studio a fin de abrir el asistente para **habilitar una tabla para Stretch**. También puede utilizar Transact\-SQL para habilitar Stretch Database en una tabla existente o para crear una nueva tabla con Stretch Database habilitado.

* Si almacena los datos inactivos en una tabla independiente, puede migrar toda la tabla.
* Si la tabla contiene datos activos e inactivos, puede especificar una función de filtro para seleccionar las filas que se deben migrar.

**Requisitos previos**. Si selecciona **Stretch | Habilitar** para una tabla y no ha habilitado todavía Stretch Database para la base de datos, el asistente configura primero la base de datos para Stretch Database. Siga los pasos que se describen en [Ejecución del asistente para habilitar la base de datos para Stretch](sql-server-stretch-database-wizard.md) en lugar de los pasos de este tema.

**Permisos**. La habilitación de Stretch Database en una base de datos o una tabla requiere permisos db\_owner. La habilitación de Stretch Database en una tabla también requiere permisos ALTER en la tabla.

> [!NOTE]
> Posteriormente, al deshabilitar Stretch Database, no olvide que si lo hace en una tabla o una base de datos, no se elimina el objeto remoto. Si quiere eliminar la tabla remota o la base de datos remota, debe hacerlo mediante el Portal de administración de Azure. Los objetos remotos seguirán generando costos de Azure hasta que se eliminan manualmente.
> 
> 

## <a name="a-nameenablewizardtableause-the-wizard-to-enable-stretch-database-on-a-table"></a><a name="EnableWizardTable"></a>Uso del asistente para habilitar Stretch Database en una tabla
**Inicio del asistente**

1. En SQL Server Management Studio, en el Explorador de objetos, seleccione la tabla en la que desee habilitar Stretch.
2. Haga clic con el botón derecho y seleccione **Stretch** y **Habilitar** para iniciar el asistente.

**Introducción**

Consulte el propósito del asistente y los requisitos previos.

**Selección de tablas de base de datos**

Confirme que se muestra y se selecciona la tabla que desea habilitar.

Puede migrar una tabla entera o especificar una función de filtro sencillo en el Asistente. Si quiere usar un tipo diferente de función de filtro para seleccionar las filas que se migrarán, realice una de las siguientes acciones.

* Salga del asistente y ejecute la instrucción ALTER TABLE para habilitar Stretch en la tabla y especificar una función de filtro.
* Ejecute la instrucción ALTER TABLE para especificar una función tras salir del Asistente. Para conocer los pasos necesarios, consulte [Add a filter function after running the Wizard](sql-server-stretch-database-predicate-function.md#addafterwiz)(Adición de una función de filtro después de ejecutar el asistente).

La sintaxis de ALTER TABLE se describe más adelante en este tema.

**Resumen**

Consulte los valores que escribió y las opciones que seleccionó en el asistente. Después, seleccione **Finalizar** para habilitar Stretch.

**Resultados**

Consulte los resultados.

## <a name="a-nameenabletsqltableause-transact-sql-to-enable-stretch-database-on-a-table"></a><a name="EnableTSQLTable"></a>Uso de Transact\-SQL para habilitar Stretch Database en una tabla
Puede habilitar Stretch Database para una tabla existente o crear una nueva tabla con habilitación de Stretch Database mediante Transact-SQL.

### <a name="options"></a>Opciones
Utilice las siguientes opciones al ejecutar CREATE TABLE o ALTER TABLE para habilitar Stretch Database en una tabla.

* Opcionalmente, use la cláusula `FILTER_PREDICATE = <function>` para especificar una función y seleccionar las filas que se migrarán si la tabla contiene datos activos e inactivos. El predicado debe llamar a una función con valores de tabla insertada. Para obtener más información, consulte [Select rows to migrate by using a filter function](sql-server-stretch-database-predicate-function.md)(Uso de una función de filtro para seleccionar las filas que se migrarán). Si no especifica una función de filtro, se migrará toda la tabla.
  
  > [!NOTE]
  > Si proporciona una función de filtro con un rendimiento insuficiente, la migración de datos también lo tendrá. Stretch Database aplica la función de filtro a la tabla con el operador CROSS APPLY.
  > 
  > 
* Especifique `MIGRATION_STATE = OUTBOUND` para iniciar la migración de datos inmediatamente o `MIGRATION_STATE = PAUSED` para posponer el inicio de la migración de datos.

### <a name="enable-stretch-database-for-an-existing-table"></a>Habilitación de Stretch Database para una tabla existente
Para configurar una tabla existente para Stretch Database, ejecute el comando ALTER TABLE.

Este es un ejemplo en el que se migra toda la tabla y en el que la migración de datos se inicia de inmediato.

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```
En el ejemplo siguiente se migran solo las filas identificadas mediante la función con valores de tabla insertada `dbo.fn_stretchpredicate` y se pospone la migración de datos. Para más información sobre la función de filtro, consulte [Select rows to migrate by using a filter function](sql-server-stretch-database-predicate-function.md)(Uso de una función de filtro para seleccionar las filas que se migran).

```tsql
USE <Stretch-enabled database name>;
GO
ALTER TABLE <table name>  
    SET ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
 GO
```

Para obtener más información, consulte [ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx).

### <a name="create-a-new-table-with-stretch-database-enabled"></a>Creación de una nueva tabla con Stretch Database habilitado
Para crear una nueva tabla con Stretch Database habilitado, ejecute el comando CREATE TABLE.

Este es un ejemplo en el que se migra toda la tabla y en el que la migración de datos se inicia de inmediato.

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON ( MIGRATION_STATE = OUTBOUND ) ) ;  
GO
```

En el ejemplo siguiente se migran solo las filas identificadas mediante la función con valores de tabla insertada `dbo.fn_stretchpredicate` y se pospone la migración de datos. Para más información sobre la función de filtro, consulte [Select rows to migrate by using a filter function](sql-server-stretch-database-predicate-function.md)(Uso de una función de filtro para seleccionar las filas que se migran).

```tsql
USE <Stretch-enabled database name>;
GO
CREATE TABLE <table name>
    ( ... )  
    WITH ( REMOTE_DATA_ARCHIVE = ON (  
        FILTER_PREDICATE = dbo.fn_stretchpredicate(),  
        MIGRATION_STATE = PAUSED ) ) ;  
GO  
```

Para obtener más información, consulte [CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx).

## <a name="see-also"></a>Otras referencias
[ALTER TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms190273.aspx)

[CREATE TABLE (Transact-SQL)](https://msdn.microsoft.com/library/ms174979.aspx)




<!--HONumber=Nov16_HO3-->


