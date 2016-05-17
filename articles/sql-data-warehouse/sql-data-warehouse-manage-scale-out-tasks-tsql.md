<properties
pageTitle="Administración de tareas de escalabilidad de Almacenamiento de datos SQL de Azure (TSQL) | Microsoft Azure"
   description="Tareas de TSQL para escalar horizontalmente el rendimiento de Almacenamiento de datos SQL de Azure Use TSQL para cambiar los recursos de proceso ajustando las DWU."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="04/28/2016"
   ms.author="barbkess;sonyama"/>

# Administración de tareas de escalabilidad de Almacenamiento de datos SQL de Azure (TSQL)

> [AZURE.SELECTOR]
- [Información general](sql-data-warehouse-overview-scalability.md)
- [Portal](sql-data-warehouse-manage-scale-out-tasks.md)
- [PowerShell](sql-data-warehouse-manage-scale-out-tasks-powershell.md)
- [REST](sql-data-warehouse-manage-scale-out-tasks-rest-api.md)
- [TSQL](sql-data-warehouse-manage-scale-out-tasks-tsql.md)


Escale horizontalmente con total flexibilidad memoria y recursos de proceso para satisfacer las demandas de cambio de su carga de trabajo y ahorre costos reduciendo recursos durante las horas de poca actividad.

Esta colección de tareas utiliza TSQL para:

- Ver la configuración de DWU actual
- Cambiar recursos de proceso ajustando DWU

Para pausar o reanudar una base de datos, elija una de las otras opciones de plataforma en la parte superior de este artículo.

Para obtener más información al respecto, consulte [Información general sobre la escalabilidad de rendimiento][].

<a name="current-dwu-bk"></a>

## Ver la configuración de DWU actual

Para ver la configuración actual de DWU para las bases de datos:

1. Abra el Explorador de objetos de SQL Server en Visual Studio 2015.
2. Conéctese a la base de datos maestra asociada al servidor lógico de Base de datos SQL.
2. Seleccione en la vista de administración dinámica sys.database\_service\_objectives. Aquí tiene un ejemplo: 

```
SELECT
 db.name [Database],
 ds.edition [Edition],
 ds.service_objective [Service Objective]
FROM
 sys.database_service_objectives ds
 JOIN sys.databases db ON ds.database_id = db.database_id
```

<a name="scale-dwu-bk"></a>

## Escalado de DWU

[AZURE.INCLUDE [SQL Data Warehouse scale DWUs description (Descripción de escalado de DWU de Almacenamiento de datos SQL)](../../includes/sql-data-warehouse-scale-dwus-description.md)]

Para cambiar las DWU:


1. Conéctese a la base de datos maestra asociada al servidor lógico de Base de datos SQL.
2. Utilice la instrucción TSQL [ALTER DATABAS][]. En el ejemplo siguiente se establece el objetivo de nivel de servicio en DW1000 para la base de datos MySQLDW. 

```Sql
ALTER DATABASE MySQLDW
MODIFY (SERVICE_OBJECTIVE = 'DW1000')
;
```

<a name="next-steps-bk"></a>

## Pasos siguientes

Para otras tareas de administración, consulte [Información general de administración][].

<!--Image references-->

<!--Article references-->
[Service capacity limits]: ./sql-data-warehouse-service-capacity-limits.md
[Información general de administración]: ./sql-data-warehouse-overview-manage.md
[Información general sobre la escalabilidad de rendimiento]: ./sql-data-warehouse-overview-scalability.md

<!--MSDN references-->

[ALTER DATABAS]: https://msdn.microsoft.com/library/mt204042.aspx


<!--Other Web references-->

[Azure portal]: http://portal.azure.com/

<!---HONumber=AcomDC_0504_2016-->