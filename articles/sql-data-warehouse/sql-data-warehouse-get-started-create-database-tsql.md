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
   ms.date="04/20/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Creación de una base de datos de Almacenamiento de datos SQL mediante Transact-SQL (TSQL)

> [AZURE.SELECTOR]
- [Portal de Azure](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

En este artículo se mostrará cómo crear una base de datos de Almacenamiento de datos SQL con Transact-SQL (TSQL).

## Antes de empezar

Necesitará lo siguiente para completar los pasos de este artículo:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Un servidor SQL lógico V12. Necesitará un servidor SQL Server V12 para crear el Almacenamiento de datos SQL. Si no tiene un servidor SQL lógico V12, consulte **Configurar y crear un servidor** en el artículo [Creación de Almacenamiento de datos SQL][].
- Visual Studio. Para obtener una copia gratis de Visual Studio, consulte la página [Descargas de Visual Studio][].


> [AZURE.NOTE] La creación de una nueva instancia de Almacenamiento de datos SQL puede dar lugar a un nuevo servicio facturable. Consulte [Precios Almacenamiento de datos SQL][] para más información sobre los precios.

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

Los parámetros **MAXSIZE** y **SERVICE\_OBJECTIVE** especifican el espacio máximo que puede usar la base de datos en el disco y los recursos de proceso asignados a la instancia de Almacenamiento de datos. El objetivo de servicio es esencialmente una asignación de CPU y memoria que se escala linealmente con el tamaño de DWU.

El valor MAXSIZE puede estar entre 250 GB y 60 TB. El objetivo de servicio puede estar entre DW100 y DW2000. Para ver una lista completa de todos los valores válidos de MAXSIZE y SERVICE\_OBJECTIVE, consulte la documentación de MSDN para [CREATE DATABASE][]. También se pueden cambiar los valores de MAXSIZE y SERVICE\_OBJECTIVE con un comando [ALTER DATABASE][] de T-SQL. Debe tener precaución al cambiar el valor de SERVICE\_OBJECTIVE ya que provocará un reinicio de los servicios que cancelará todas las consultas en marcha. El cambio de MAXSIZE no tiene esta precaución, ya que es solo una operación de metadatos sencilla.

## Pasos siguientes
Después de que su Almacenamiento de datos SQL termine el aprovisionamiento, puede [cargar datos de ejemplo][] o averiguar cómo [desarrollar][], [cargar][] o [migrar][].

<!--Article references-->
[Creación de Almacenamiento de datos SQL]: sql-data-warehouse-get-started-provision.md
[Conexión a Almacenamiento de datos SQL con Visual Studio]: sql-data-warehouse-get-started-connect.md
[migrar]: sql-data-warehouse-overview-migrate.md
[desarrollar]: sql-data-warehouse-overview-develop.md
[cargar]: sql-data-warehouse-overview-load.md
[cargar datos de ejemplo]: sql-data-warehouse-get-started-manually-load-samples.md

<!--MSDN references--> 
[CREATE DATABASE]: https://msdn.microsoft.com/library/mt204021.aspx
[ALTER DATABASE]: https://msdn.microsoft.com/library/mt204042.aspx

<!--Other Web references-->
[Precios Almacenamiento de datos SQL]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/
[Descargas de Visual Studio]: https://www.visualstudio.com/downloads/download-visual-studio-vs

<!---HONumber=AcomDC_0511_2016-->