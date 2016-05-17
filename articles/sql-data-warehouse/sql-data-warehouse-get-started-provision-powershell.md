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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de Almacenamiento de datos SQL con Powershell

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

### Requisitos previos
Antes de empezar, asegúrese de que cumple los siguientes requisitos previos:

- Azure SQL Server v 12 para hospedar la base de datos.
- Conocer el nombre del grupo de recursos de SQL Server

Para más detalles sobre los requisitos previos mencionados, consulte el paso **Configurar y crear un servidor** en el artículo [Creación de Almacenamiento de datos SQL][].

> [AZURE.NOTE]  Para usar Azure Powershell con Almacenamiento de datos SQL, se necesita instalar Azure PowerShell versión 1.0.3 o superior. Puede comprobar la versión ejecutando **Get-Module -ListAvailable -Name Azure**. Se puede instalar la versión más reciente desde el [Instalador de plataforma web de Microsoft ][]. Para más información sobre cómo instalar la versión más reciente, consulte [Cómo instalar y configurar Azure PowerShell][].

## Creación de una base de datos de Almacenamiento de datos SQL.
1. Abra Windows PowerShell.
2. Ejecute este cmdlet para iniciar sesión en el Administrador de recursos de Azure.

	```Powershell
	Login-AzureRmAccount
	```
	
3. Seleccione la suscripción que desea utilizar para la sesión actual.

	```Powershell
	Get-AzureRmSubscription	-SubscriptionName "MySubscription" | Select-AzureRmSubscription
	```

4.  Cree la base de datos. En este ejemplo se crea una nueva base de datos denominada "mynewsqldw", con el nivel de objetivo de servicio "DW400", en el servidor llamado "sqldwserver1" que se encuentra en el grupo de recursos denominado "mywesteuroperesgp1". **NOTA: Crear una nueva base de datos de Almacenamiento de datos SQL podría resultar en nuevos cargos facturables. Consulte [Precios Almacenamiento de datos SQL][] para más información sobre los precios.**

	```Powershell
	New-AzureRmSqlDatabase -RequestedServiceObjectiveName "DW400" -DatabaseName "mynewsqldw" -ServerName "sqldwserver1" -ResourceGroupName "mywesteuroperesgp1" -Edition "DataWarehouse"
	```

Los parámetros necesarios para este cmdlet son los siguientes:

- **RequestedServiceObjectiveName**: la cantidad de DWU solicitada y con formato "DWXXX". DWU representa una asignación de CPU y memoria. Cada valor de DWU representa un aumento lineal en estos recursos. Los valores admitidos son: 100, 200, 300, 400, 500, 600, 1000, 1200, 1500, 2000.
- **DatabaseName**: el nombre del Almacenamiento de datos SQL que está creando.
- **ServerName**: el nombre del servidor que se usa para la creación (tiene que ser V12).
- **ResourceGroupName**: el grupo de recursos que está usando. Para obtener los grupos de recursos que estén disponibles en su suscripción, use Get-AzureResource.
- **Edition**: tiene que establecer la edición como "DataWarehouse" para crear un Almacenamiento de datos SQL.

Para más información sobre las opciones de parámetros, consulte [Create Database (Azure SQL Data Warehouse)][] (Creación de base de datos [Almacenamiento de datos SQL de Azure]). Para la referencia de comandos, consulte [New-AzureRmSqlDatabase][].

## Pasos siguientes
Después de que Almacenamiento de datos SQL termine el aprovisionamiento, puede intentar [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

Si está interesado en más información sobre cómo administrar Almacenamiento de datos SQL mediante programación, consulte nuestro artículo sobre cómo usar [las API de REST y cmdlets de Powershell][].

<!--Image references-->

<!--Article references-->
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-load-with-bcp.md
[cargar datos de ejemplo]: sql-data-warehouse-get-started-manually-load-samples.md
[las API de REST y cmdlets de Powershell]: sql-data-warehouse-reference-powershell-cmdlets.md
[firewall rules]: sql-database-configure-firewall-settings.md
[Cómo instalar y configurar Azure PowerShell]: ../powershell/powershell-install-configure.md
[Creación de Almacenamiento de datos SQL]: sql-data-warehouse-get-started-provision.md

<!--MSDN references--> 
[MSDN]: https://msdn.microsoft.com/library/azure/dn546722.aspx
[New-AzureRmSqlDatabase]: https://msdn.microsoft.com/library/mt619339.aspx
[Create Database (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204021.aspx

<!--Other Web references-->
[Instalador de plataforma web de Microsoft ]: https://aka.ms/webpi-azps
[Precios Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
 

<!---HONumber=AcomDC_0511_2016-->