<properties
   pageTitle="Crear Almacenamiento de datos SQL mediante el uso de Powershell | Microsoft Azure"
   description="Creación de Almacenamiento de datos SQL con Powershell"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="08/25/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de Almacenamiento de datos SQL con Powershell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

En este artículo se explica cómo crear un Almacenamiento de datos SQL mediante PowerShell.

## Requisitos previos

Para empezar, necesitará lo siguiente:

- **Cuenta de Azure**: visite [Evaluación gratuita de Azure][] o [Crédito mensual de Azure para suscriptores de Visual Studio][] para crear una cuenta.
- **SQL Server de Azure**: consulte [Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure][] o [Creación de un servidor lógico de Base de datos SQL de Azure con PowerShell][] para más información.
- **Nombre del grupo de recursos**: use el mismo grupo de recursos que el servidor SQL Server de Azure o consulte [Creación de un grupo de recursos][].
- **PowerShell versión 1.0.3 o posterior**: para comprobar la versión, ejecute **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft][]. Para más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

> [AZURE.NOTE] La creación de una instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable. Consulte [Precios de Almacenamiento de datos SQL][] para más información sobre los precios.

## Creación de Almacenamiento de datos SQL

1. Abra Windows PowerShell.
2. Ejecute este cmdlet para iniciar sesión en el Administrador de recursos de Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Seleccione la suscripción que desea utilizar para la sesión actual.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Cree la base de datos. En este ejemplo se crea una base de datos denominada "mynewsqldw", con el nivel de objetivo de servicio "DW400", en el servidor llamado "sqldwserver1" que se encuentra en el grupo de recursos denominado "mywesteuroperesgp1".

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse" -CollationName "SQL_Latin1_General_CP1_CI_AS" -MaxSizeBytes 10995116277760
	```

Los parámetros obligatorios son:

- **RequestedServiceObjectiveName**: la cantidad de [DWU][] solicitada. Los valores admitidos son: DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000, DW3000 y DW6000.
- **DatabaseName**: el nombre del Almacenamiento de datos SQL que está creando.
- **ServerName**: el nombre del servidor que se usa para la creación (tiene que ser V12).
- **ResourceGroupName**: el grupo de recursos que está usando. Para buscar grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
- **Edition**: la edición debe ser "DataWarehouse" para crear un Almacenamiento de datos SQL.

Los parámetros opcionales son:

- **CollationName**: la intercalación predeterminada cuando no se especifica otra es SQL\_Latin1\_General\_CP1\_CI\_AS. No se puede cambiar la intercalación de una base de datos.
- **MaxSizeBytes**: el tamaño máximo predeterminado de una base de datos es 10 GB.


Para más información sobre las opciones de parámetros, consulte [New-AzureRmSqlDatabase][] y [Create Database (Azure SQL Data Warehouse)][] \(Creación de base de datos [Almacenamiento de datos SQL de Azure]).

## Pasos siguientes

Después de que Almacenamiento de datos SQL finalice el aprovisionamiento, puede intentar [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

Si está interesado en más información sobre cómo administrar Almacenamiento de datos SQL mediante programación, consulte nuestro artículo sobre cómo usar [las API de REST y los cmdlets de PowerShell][].

<!--Image references-->

<!--Article references-->
[DWU]: ./sql-data-warehouse-overview-what-is.md#data-warehouse-units
[migrar]: ./sql-data-warehouse-overview-migrate.md
[desarrollar]: ./sql-data-warehouse-overview-develop.md
[cargar]: ./sql-data-warehouse-load-with-bcp.md
[cargar datos de ejemplo]: ./sql-data-warehouse-load-sample-databases.md
[las API de REST y los cmdlets de PowerShell]: ./sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: ../sql-database-configure-firewall-settings.md

[Cómo instalar y configurar Azure PowerShell]: ../powershell/powershell-install-configure.md
[how to create a SQL Data Warehouse from the Azure Portal]: ./sql-data-warehouse-get-started-provision.md
[Creación de un servidor lógico de Base de datos SQL de Azure con el Portal de Azure]: ../sql-database/sql-database-get-started.md#create-an-azure-sql-database-logical-server
[Creación de un servidor lógico de Base de datos SQL de Azure con PowerShell]: ../sql-database/sql-database-get-started-powershell.md#database-setup-create-a-resource-group-server-and-firewall-rule
[Creación de un grupo de recursos]: ../resource-group-template-deploy-portal.md#create-resource-group

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Instalador de plataforma web de Microsoft]: https://aka.ms/webpi-azps
[Precios de Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Evaluación gratuita de Azure]: https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F
[Crédito mensual de Azure para suscriptores de Visual Studio]: https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F

<!---HONumber=AcomDC_0831_2016-->