<properties
   pageTitle="Creación de un Almacenamiento de datos SQL con TSQL | Microsoft Azure"
   description="Aprenda a crear una base de datos de Almacenamiento de datos SQL de Azure con TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/24/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de una base de datos de Almacenamiento de datos SQL mediante Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

En este artículo se explica cómo crear un Almacenamiento de datos SQL mediante T-SQL.

## Requisitos previos

Para empezar, necesitará lo siguiente:

- **Cuenta de Azure**: visite [Evaluación gratuita de Azure][] o [Crédito mensual de Azure para suscriptores de Visual Studio][] para crear una cuenta.
- **SQL Server de Azure**: consulte [Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure][] o [Creación de un servidor lógico de Base de datos SQL de Azure con PowerShell][] para más información.
- **Nombre del grupo de recursos**: use el mismo grupo de recursos que el servidor SQL Server de Azure o consulte [Creación de un grupo de recursos][].
- **Entorno para ejecutar T-SQL**: puede usar [Visual Studio][Installing Visual Studio and SSDT], [sqlcmd][] o [SSMS][] para ejecutar T-SQL.

> [AZURE.NOTE] La creación de una instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable. Consulte [Precios de Almacenamiento de datos SQL][] para más información sobre los precios.

## Creación de una base de datos con Visual Studio

Si no está familiarizado con Visual Studio, consulte el artículo [Consultas en Almacenamiento de datos SQL de Azure (Visual Studio)][]. Para comenzar, abra el Explorador de objetos de SQL Server en Visual Studio y conéctese al servidor que hospedará la base de datos de Almacenamiento de datos SQL. Una vez conectado, puede crear una instancia de Almacenamiento de datos SQL mediante la ejecución del siguiente comando SQL en la base de datos **maestra**. Este comando crea la base de datos MySqlDwDb con un objetivo de servicio de DW400 y permite que crezca hasta un tamaño máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Creación de una base de datos con sqlcmd

Como alternativa, puede ejecutar el mismo comando con sqlcmd mediante la ejecución del siguiente código en el símbolo del sistema.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb COLLATE SQL_Latin1_General_CP1_CI_AS (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

La intercalación predeterminada cuando no se especifica otra es COLLATE SQL\_Latin1\_General\_CP1\_CI\_AS. El valor de `MAXSIZE` puede estar entre 250 GB y 240 TB. El valor de `SERVICE_OBJECTIVE` puede estar entre [DWU][] DW100 y DW2000. Para obtener una lista de todos los valores válidos, consulte la documentación de MSDN para [CREATE DATABASE][]. Se pueden cambiar los valores de MAXSIZE y SERVICE\_OBJECTIVE con un comando [ALTER DATABASE][] de T-SQL. No se puede cambiar la intercalación de una base de datos después de su creación. Debe tener precaución al cambiar el valor de SERVICE\_OBJECTIVE ya que cambiar la DWU provocará un reinicio de los servicios que cancelará todas las consultas en marcha. Con el cambio de MAXSIZE no se reinician servicios, ya que es solo una operación de metadatos sencilla.

## Pasos siguientes

Después de que su Almacenamiento de datos SQL termine el aprovisionamiento, puede [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: sql-data-warehouse-get-started-provision.md
[Consultas en Almacenamiento de datos SQL de Azure (Visual Studio)]: sql-data-warehouse-query-visual-studio.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-overview-load.md
[cargar datos de ejemplo]: sql-data-warehouse-load-sample-databases.md
[Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creación de un servidor lógico de Base de datos SQL de Azure con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creación de un grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group
[Installing Visual Studio and SSDT]: sql-data-warehouse-install-visual-studio.md
[sqlcmd]: sql-data-warehouse-get-started-connect-sqlcmd.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx
[SSMS]: https://msdn.microsoft.com/library/mt238290.aspx

<!--Other Web references-->
[Precios de Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Evaluación gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédito mensual de Azure para suscriptores de Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->