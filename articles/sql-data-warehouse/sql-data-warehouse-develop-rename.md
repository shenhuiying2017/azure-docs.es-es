<properties
   pageTitle="Cambio de nombre en el Almacenamiento de datos SQL | Microsoft Azure"
   description="Sugerencias para cambiar el nombre de tablas en el Almacenamiento de datos SQL de Azure para desarrollar soluciones."
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
   ms.date="05/28/2016"
   ms.author="mausher;jrj;barbkess;sonyama"/>

# Cambio de nombre en el Almacenamiento de datos SQL
Almacenamiento de datos SQL usa la instrucción [RENAME][] para cambiar el nombre de las tablas. En esto se diferencia de SQL Server, que utiliza `sp_rename`. Actualmente, solo puede cambiarse el nombre de las tablas de usuario. No se puede cambiar el nombre de bases de datos y tablas externas.

## Cambio de nombre de una tabla

Al cambiar el nombre de una tabla, se actualizan todos los objetos y las propiedades asociados a la tabla para hacer referencia al nuevo nombre de la tabla. Por ejemplo, se actualizan las definiciones, los índices, las restricciones y los permisos de la tabla. Las vistas no se actualizan.

La sintaxis para cambiar el nombre de una tabla es:

```sql
RENAME OBJECT dbo.Customer TO NewCustomer;
```

## Cambio del esquema de una tabla

Para cambiar el esquema al que pertenece un objeto, utilice ALTER SCHEMA:

```sql
ALTER SCHEMA dbo TRANSFER OBJECT::product.item;
```

## El cambio de nombre de una tabla exige el bloqueo exclusivo de la tabla.

No se puede cambiar el nombre de una tabla mientras está en uso. El cambio de nombre de una tabla requiere un bloqueo exclusivo en la tabla. Si la tabla se está usando, puede que tenga que terminar la sesión en que se usa la tabla. Utilice el comando [KILL][] para terminar una sesión. Por ejemplo: `KILL 'SID1234'`. Tenga cuidado cuando utilice KILL, dado que cualquier trabajo transaccional no confirmado se revertirá cuando se termine una sesión. Consulte el artículo sobre conexiones, para más información sobre [sesiones y solicitudes][]. Consulte [Optimización de transacciones para Almacenamiento de datos SQL][] para más información sobre el impacto de eliminar una consulta transaccional y el efecto de una reversión.


## Pasos siguientes
Para más información, consulte las [referencias sobre T-SQL][].

<!--Image references-->

<!--Article references-->
[development overview]: ./sql-data-warehouse-overview-develop.md
[sesiones y solicitudes]: ./sql-data-warehouse-develop-connections.md#sessions-and-requests
[referencias sobre T-SQL]: ./sql-data-warehouse-reference-tsql-statements.md
[Optimización de transacciones para Almacenamiento de datos SQL]: ./sql-data-warehouse-develop-best-practices-transactions.md

<!--MSDN references-->
[KILL]: https://msdn.microsoft.com/library/ms173730.aspx
[RENAME]: https://msdn.microsoft.com/library/mt631611.aspx

<!---HONumber=AcomDC_0601_2016-->