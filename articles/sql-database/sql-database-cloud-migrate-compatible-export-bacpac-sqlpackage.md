<properties
   pageTitle="Exportación de una base de datos SQL Server a un archivo BACPAC mediante SqlPackage | Microsoft Azure"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, exportación de bases de datos,exportación de archivo BACPAC, sqlpackage"
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
   ms.workload="sqldb-migrate"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Exportación de una Base de datos SQL Server a un archivo BACPAC mediante SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-export-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-export-bacpac-sqlpackage.md)

En este artículo se muestra cómo exportar una base de datos de SQL Server a un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la utilidad del símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esta utilidad se incluye con Visual Studio y SQL Server. También puede [descargar](https://msdn.microsoft.com/library/mt204009.aspx) la versión más reciente de SQL Server Data Tools para obtener esta utilidad.

1. Abra un símbolo del sistema y cambie a un directorio que contiene la utilidad de línea de comandos sqlpackage.exe; esta utilidad se incluye con Visual Studio y SQL Server. Use la búsqueda en el equipo para encontrar la ruta de acceso en su entorno.
2. Ejecute el siguiente comando sqlpackage.exe con los argumentos siguientes para su entorno:

	'sqlpackage.exe /Action:Export /ssn:< server_name > /sdn:< database_name > /tf:< target_file >

	| Argumento | Descripción |
	|---|---|
	| < server_name > | nombre de servidor de origen |
	| < database_name > | nombre de base de datos de origen |
	| < target_file > | nombre y ubicación del archivo BACPAC |

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01b.png)

## Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)
- [Importar desde BACPAC a la Base de datos SQL con SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Importar un archivo BACPAC para crear una base de datos SQL de Azure](sql-database-import.md)
- [Importar un archivo BACPAC para crear una base de datos SQL de Azure mediante PowerShell](sql-database-import-powershell.md)

## Más información

- [Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
- [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0608_2016-->