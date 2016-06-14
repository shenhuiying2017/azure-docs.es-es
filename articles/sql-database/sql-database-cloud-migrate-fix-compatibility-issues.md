<properties
   pageTitle="Solucionar problemas de compatibilidad de bases de datos de SQL Server antes de la migración a la Base de datos SQL"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, compatibilidad, Asistente para migración de Microsoft Azure"
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management"
   ms.date="05/31/2016"
   ms.author="carlrab"/>

# Uso del Asistente para migración de SQL Azure para corregir problemas de compatibilidad con bases de datos SQL Server antes de realizar la migración a Base de datos SQL de Azure

> [AZURE.SELECTOR]
- Uso del [Asistente para migración a SQL Azure](sql-database-cloud-migrate-fix-compatibility-issues.md)
- Uso de [SSDT](sql-database-cloud-migrate-fix-compatibility-issues-ssdt.md)
- Uso de [SSMS](sql-database-cloud-migrate-fix-compatibility-issues-ssms.md)

En este artículo, aprenderá a detectar y corregir problemas de compatibilidad con bases de datos de SQL Server mediante el Asistente para migración de SQL Azure antes de la migración a Base de datos SQL de Azure.

## Uso del Asistente para migración a SQL de Azure

Use la herramienta de CodePlex del [Asistente para migración de SQL Azure](http://sqlazuremw.codeplex.com/) para generar un script T-SQL desde una base de datos de origen que luego se transforma por el Asistente para que sea compatible con la Base de datos SQL y después se conecte a la Base de datos SQL de Azure para ejecutar el script. Esta herramienta también analizará los archivos de seguimiento para determinar problemas de compatibilidad. El script puede generarse solo con el esquema o puede incluir datos en formato BCP. Hay documentación adicional, incluidas instrucciones paso a paso, disponible en CodePlex en el [Asistente para migración de SQL de Azure](http://sqlazuremw.codeplex.com/).

 ![Diagrama de migración de SAMW](./media/sql-database-cloud-migrate/02SAMWDiagram.png)

  > [AZURE.NOTE] Tenga en cuenta que no todos los esquemas incompatibles que se pueden detectar por el asistente se pueden corregir por sus transformaciones integradas. El script incompatible que no se pueda resolver se notificará como un error, con comentarios insertados en el script generado. Si se detectan numerosos errores, use Visual Studio o SQL Server Management Studio para analizar y corregir cada error que no se pudo corregir con el Asistente para migración de SQL Server.

## Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Migración de una Base de datos SQL Server compatible con la Base de datos SQL](sql-database-cloud-migrate.md#migrate-a-compatible-sql-server-database-to-sql-database)

## Más información

- [Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
- [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0601_2016-->