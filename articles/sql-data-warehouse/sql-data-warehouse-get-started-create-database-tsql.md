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
   ms.date="07/11/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de una base de datos de Almacenamiento de datos SQL mediante Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

En este artículo se mostrará cómo crear una base de datos de Almacenamiento de datos SQL con Transact-SQL (T-SQL).

## Requisitos previos
Antes de empezar, asegúrese de que cumple los siguientes requisitos previos:

- **Cuenta de Azure**: consulte [Evaluación gratuita de Azure][] o [Crédito mensual de Azure para suscriptores de Visual Studio][] para crear una cuenta.
- **SQL Server V12 de Azure**: consulte [Creación de un servidor lógico de Base de datos SQL de Azure][] o [Configuración de la base de datos: creación de un grupo de recursos, un servidor y una regla de firewall][].
- **Nombre del grupo de recursos**: use el mismo grupo de recursos que el servidor SQL Server V12 de Azure o consulte [Creación de un grupo de recursos][] para crear uno.
- **Visual Studio con SQL Server Data Tools**: para ver instrucciones de instalación, consulte [Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL][].

> [AZURE.NOTE] La creación de una nueva instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable. Consulte [Precios de Almacenamiento de datos SQL][] para más información sobre los precios.

## Creación de una base de datos con Visual Studio

Si no está familiarizado con Visual Studio, consulte el artículo [Conexión a Almacenamiento de datos SQL con Visual Studio][]. Para comenzar, abra el Explorador de objetos de SQL Server en Visual Studio y conéctese al servidor que hospedará la base de datos de Almacenamiento de datos SQL. Una vez conectado, puede crear una instancia de Almacenamiento de datos SQL mediante la ejecución del siguiente comando SQL en la base de datos **maestra**. Este comando crea la base de datos MySqlDwDb con un objetivo de servicio de DW400 y permite que crezca hasta un tamaño máximo de 10 TB.

```sql
CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB);
```

## Creación de una base de datos con sqlcmd

Como alternativa, puede ejecutar el mismo comando con sqlcmd mediante la ejecución del siguiente código en el símbolo del sistema.

```sql
sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE MySqlDwDb (EDITION='datawarehouse', SERVICE_OBJECTIVE = 'DW400', MAXSIZE= 10240 GB)"
```

El valor de `MAXSIZE` puede estar entre 250 GB y 240 TB. El valor de `SERVICE_OBJECTIVE` puede estar entre [DWU][] DW100 y DW2000. Para obtener una lista de todos los valores válidos, consulte la documentación de MSDN para [CREATE DATABASE][]. También se pueden cambiar los valores de MAXSIZE y SERVICE\_OBJECTIVE con un comando [ALTER DATABASE][] de T-SQL. Debe tener precaución al cambiar el valor de SERVICE\_OBJECTIVE ya que provocará un reinicio de los servicios que cancelará todas las consultas en marcha. Con el cambio de MAXSIZE no se reinician servicios, ya que es solo una operación de metadatos sencilla.

## Pasos siguientes
Después de que su Almacenamiento de datos SQL termine el aprovisionamiento, puede [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[how to create a SQL Data Warehouse from the Azure portal]: ./sql-data-warehouse-get-started-provision.md
[Conexión a Almacenamiento de datos SQL con Visual Studio]: ./sql-data-warehouse-get-started-connect.md
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-overview-load.md
[cargar datos de ejemplo]: ./sql-data-warehouse-get-started-load-sample-databases.md
[Creación de un servidor lógico de Base de datos SQL de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Configuración de la base de datos: creación de un grupo de recursos, un servidor y una regla de firewall]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creación de un grupo de recursos]: ../resource-group-template-deploy-portal.md
[Instalación de Visual Studio 2015 y SSDT para Almacenamiento de datos SQL]: ./sql-data-warehouse-install-visual-studio.md


<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Precios de Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Evaluación gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédito mensual de Azure para suscriptores de Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0720_2016-->