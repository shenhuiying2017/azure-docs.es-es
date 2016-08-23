<properties
   pageTitle="Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage"
   description="Base de datos SQL de Microsoft Azure, migración de bases de datos, importación de bases de datos, importación de archivo BACPAC, sqlpackage"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
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

# Importar a la Base de datos SQL desde un archivo BACPAC mediante SqlPackage

> [AZURE.SELECTOR]
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)

En este artículo se muestra cómo importar a una base de datos de SQL Server desde un archivo [BACPAC](https://msdn.microsoft.com/library/ee210546.aspx#Anchor_4) mediante la utilidad del símbolo del sistema [SqlPackage](https://msdn.microsoft.com/library/hh550080.aspx). Esta utilidad se incluye con Visual Studio y SQL Server. También puede [descargar](https://msdn.microsoft.com/library/mt204009.aspx) la versión más reciente de SQL Server Data Tools para obtener esta utilidad.

> [AZURE.NOTE] En los pasos siguientes se da por supuesto que un servidor de la Base de datos SQL está ya aprovisionado, que tiene a mano la información de conexión y ha comprobado que la base de datos de origen es compatible.

## Importación de un archivo BACPAC a la Base de datos SQL de Azure mediante SqlPackage

Use los pasos siguientes para que la utilidad de línea de comandos [SqlPackage.exe](https://msdn.microsoft.com/library/hh550080.aspx) importe una Base de datos SQL Server compatible (o una Base de datos SQL de Azure) desde un archivo BACPAC.

> [AZURE.NOTE] En los pasos siguientes se da por supuesto que un servidor de la Base de datos SQL de Azure está ya aprovisionado y que tiene a mano la información de conexión.

1. Abra un símbolo del sistema y cambie a un directorio que contiene la utilidad de línea de comandos sqlpackage.exe; esta utilidad se incluye con Visual Studio y SQL Server.
2. Ejecute el siguiente comando sqlpackage.exe con los argumentos siguientes para su entorno:

	'sqlpackage.exe /Action:Import /tsn:< nombre\_servidor > /tdn:< nombre\_basededatos > /tu:< nombre\_usuario > /tp:< contraseña > /sf:< archivo\_origen >

	| Argumento | Descripción |
	|---|---|
	| < nombre\_servidor > | nombre de servidor de destino |
	| < nombre\_basedatos > | nombre de base de datos de destino |
	| < nombre\_usuario > | nombre de usuario en el servidor de destino |
	| < contraseña > | contraseña del usuario |
	| < archivo\_origen > | nombre y ubicación del archivo BACPAC que se importa |

	![Exportar una aplicación de capa de datos desde el menú Tareas](./media/sql-database-cloud-migrate/TestForCompatibilityUsingSQLPackage01c.png)

## Pasos siguientes

- [Versión más reciente de SSDT](https://msdn.microsoft.com/library/mt204009.aspx)
- [Versión más reciente de SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx)

## Recursos adicionales

- [Base de datos SQL V12](sql-database-v12-whats-new.md)
- [Diferencias de Transact-SQL de Base de datos SQL de Azure](sql-database-transact-sql-information.md)
- [Migración de bases de datos no SQL Server mediante SQL Server Migration Assistant](http://blogs.msdn.com/b/ssma/)

<!---HONumber=AcomDC_0803_2016-->
