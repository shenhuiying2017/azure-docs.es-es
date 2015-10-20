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
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="10/07/2015"
   ms.author="lodipalm"/>

#Creación de Almacenamiento de datos SQL con TSQL 

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-TSQL.md)
- [PowerShell](sql-data-warehouse-get-started-create-powershell.md)

En este artículo se mostrará cómo crear un Almacenamiento de datos SQL mediante Transact-SQL. Necesitará lo siguiente para completar los pasos de este artículo:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Visual Studio. Para obtener una copia gratis de Visual Studio, consulte la página [Descargas de Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Un servidor de SQL Server V12. Necesitará un servidor SQL Server V12 para crear el Almacenamiento de datos SQL. Si no tiene un servidor de SQL Server V12 disponible, se recomienda crearlo en el Portal de vista previa para que pueda crear el Almacenamiento de datos SQL en un servidor nuevo.

En este artículo no se aborda cómo configurar correctamente Visual Studio ni cómo conectarse con él. Para obtener una descripción completa de cómo llevarlo a cabo, consulte la documentación de [Conexión y consultas][]. Para comenzar, tendrá que abrir el Explorador de objetos de SQL Server en Visual Studio y conectarse al servidor que va a usar para crear un Almacenamiento de datos SQL. Una vez hecho esto, podrá crear un Almacenamiento de datos SQL ejecutando el comando siguiente en la base de datos maestra:

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

También puede crear un Almacenamiento de datos SQL; para ello, abra la línea de comandos y ejecute lo siguiente:

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Cuando se ejecutan las instrucciones TSQL anteriores, tenga en cuenta que los parámetros MAXSIZE y SERVICE\_OBJECTIVE determinarán el tamaño de almacenamiento inicial y el proceso asignado a la instancia de Almacenamiento de datos. MAXSIZE aceptará los tamaños siguientes y se recomienda elegir un tamaño grande para tener espacio para el futuro:

+ 250 GB
+ 500 GB
+ 750 GB
+ 1\.024 GB
+ 5\.120 GB
+ 10\.240 GB
+ 20\.480 GB
+ 30\.720 GB
+ 40\.960 GB
+ 51\.200 GB

SERVICE\_OBJECTIVE indica el número de DWU con la que se iniciará la instancia y aceptará los valores siguientes:

+ DW100
+ DW200
+ DW300
+ DW400
+ DW500
+ DW600
+ DW1000
+ DW1200
+ DW1500
+ DW2000

Para obtener información sobre el impacto de la facturación de estos parámetros, consulte nuestra [página de precios][].

## Pasos siguientes
Después de que su Almacenamiento de datos SQL termine el aprovisionamiento, puede [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

[Conexión y consultas]: ./sql-data-warehouse-get-started-connect-query.md
[migrar]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-migrate/
[desarrollar]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-develop/
[cargar]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-overview-load/
[cargar datos de ejemplo]: https://azure.microsoft.com/es-ES/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[página de precios]: https://azure.microsoft.com/es-ES/pricing/details/sql-data-warehouse/

<!---HONumber=Oct15_HO3-->