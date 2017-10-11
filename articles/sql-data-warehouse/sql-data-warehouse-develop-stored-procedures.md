---
title: Procedimientos almacenados en SQL Data Warehouse | Microsoft Docs
description: Sugerencias para implementar procedimientos almacenados en el Almacenamiento de datos SQL Azure para el desarrollo de soluciones.
services: sql-data-warehouse
documentationcenter: NA
author: jrowlandjones
manager: jhubbard
editor: 
ms.assetid: 9b238789-6efe-4820-bf77-5a5da2afa0e8
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: t-sql
ms.date: 10/31/2016
ms.author: jrj;barbkess
ms.openlocfilehash: e42d80f0ca35f3fbb67389c66d072bc40d8a8d2c
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 07/11/2017
---
# <a name="stored-procedures-in-sql-data-warehouse"></a>Procedimientos almacenados en el Almacenamiento de datos SQL
El Almacenamiento de datos SQL admite muchas de las características de Transact-SQL que se encuentran en SQL Server. Más importante aún, hay características específicas de escalado horizontal que deseamos utilizar para maximizar el rendimiento de la solución.

Sin embargo, para mantener la escala y el rendimiento del Almacenamiento de datos SQL también hay algunas características y funcionalidades que tienen diferencias de comportamiento y otras que no son compatibles.

En este artículo se explica cómo implementar procedimientos almacenados en el Almacenamiento de datos SQL.

## <a name="introducing-stored-procedures"></a>Introducción a los procedimientos almacenados
Los procedimientos almacenados son una manera excelente para encapsular el código SQL y almacenarlo cerca de los datos en el almacenamiento de datos. Al encapsular el código en unidades administrables, los procedimientos almacenados ayudan a los programadores a modularizar sus soluciones, de tal manera que facilitan una mayor reutilización del código. Cada procedimiento almacenado también puede aceptar parámetros para que sean todavía más flexibles.

El Almacenamiento de datos SQL proporciona una implementación optimizada y simplificada de procedimientos almacenados. La diferencia más importante en comparación con SQL Server es que el procedimiento almacenado no es código compilado previamente. En los almacenamientos de datos, en general, nos preocupa menos el tiempo de compilación. Es más importante que el código del procedimiento almacenado esté bien optimizado al operar con grandes volúmenes de datos. El objetivo es ahorrar horas, minutos y segundos, no milisegundos. Por lo tanto, resulta más útil pensar en los procedimientos almacenados como contenedores para la lógica SQL.     

Si el Almacenamiento de datos SQL ejecuta el procedimiento almacenado, las instrucciones SQL se distribuyen, traducen y optimizan en el momento de la ejecución. Durante este proceso, cada instrucción se convierte en consultas distribuidas. El código SQL que se ejecuta realmente en los datos es diferente de la consulta enviada.

## <a name="nesting-stored-procedures"></a>Anidamiento de los procedimientos almacenados
Cuando los procedimientos almacenados llaman a otros procedimientos almacenados o ejecutan SQL dinámico, se dice que la invocación interna de código o de procedimientos almacenados se anida.

El Almacenamiento de datos SQL admite un máximo de ocho niveles de anidamiento. Esto difiere ligeramente de SQL Server. El nivel de anidamiento en SQL Server es 32.

La llamada al procedimiento almacenado de nivel superior es igual al nivel de anidamiento 1.

```sql
EXEC prc_nesting
```
Si el procedimiento almacenado también realiza otra llamada EXEC, aumentará el nivel de anidación a 2.

```sql
CREATE PROCEDURE prc_nesting
AS
EXEC prc_nesting_2  -- This call is nest level 2
GO
EXEC prc_nesting
```
Si el segundo procedimiento ejecuta luego SQL dinámico, aumentará el nivel de anidación a 3.

```sql
CREATE PROCEDURE prc_nesting_2
AS
EXEC sp_executesql 'SELECT 'another nest level'  -- This call is nest level 2
GO
EXEC prc_nesting
```

Tenga en cuenta que SQL Data Warehouse no admite actualmente @@NESTLEVEL. Debe poder realizar usted mismo un seguimiento de su nivel de anidamiento. Es probable que alcance el límite del nivel 8 de anidamiento, pero, en su caso, deberá reprocesar el código y "acoplarlo" para adecuarlo a dicho límite.

## <a name="insertexecute"></a>INSERT..EXECUTE
El Almacenamiento de datos SQL no permite utilizar el conjunto de resultados de un procedimiento almacenado con una instrucción INSERT. Sin embargo, puede utilizar un método alternativo.

Consulte el siguiente artículo sobre las [tablas temporales] para obtener un ejemplo sobre cómo hacerlo.

## <a name="limitations"></a>Limitaciones
Existen algunos aspectos de los procedimientos almacenados de Transact-SQL que no se implementan en el Almacenamiento de datos SQL.

Son las siguientes:

* Procedimientos almacenados temporales
* Procedimientos almacenados numerados
* Procedimientos almacenados extendidos
* Procedimientos almacenados CLR
* Opción de cifrado
* Opción de replicación
* Parámetros con valores de tabla
* Parámetros de solo lectura
* Parámetros predeterminados
* Contextos de ejecución
* Instrucción de devolución

## <a name="next-steps"></a>Pasos siguientes
Para más sugerencias sobre desarrollo, consulte la [información general sobre desarrollo][development overview].

<!--Image references-->

<!--Article references-->
[tablas temporales]: ./sql-data-warehouse-tables-temporary.md#modularizing-code
[development overview]: ./sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[nest level]: https://msdn.microsoft.com/library/ms187371.aspx

<!--Other Web references-->
