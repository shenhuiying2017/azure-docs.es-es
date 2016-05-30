<properties
	pageTitle="Importar un archivo BACPAC para crear una base de datos SQL de Azure | Microsoft Azure"
	description="Crear una base de datos SQL de Azure importando un archivo BACPAC ya existente."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/09/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Importar un archivo BACPAC para crear una base de datos SQL de Azure


**Base de datos única**

> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-import.md)
- [PowerShell](sql-database-import-powershell.md)
- [SSMS](sql-database-cloud-migrate-compatible-import-bacpac-ssms.md)
- [SqlPackage](sql-database-cloud-migrate-compatible-import-bacpac-sqlpackage.md)

Este artículo proporciona instrucciones para crear una base de datos SQL de Azure a partir de un archivo BACPAC mediante el [Portal de Azure](https://portal.azure.com).

Un BACPAC es un archivo .bacpac que contiene datos y un esquema de base de datos. La base de datos se crea a partir de un BACPAC importado de un contenedor de blobs de almacenamiento de Azure. Si no dispone de un archivo .bacpac en el almacenamiento de Azure, puede crear uno siguiendo los pasos descritos en [Crear y exportar un archivo BACPAC de una base de datos SQL de Azure](sql-database-export.md).


> [AZURE.NOTE] La Base de datos SQL de Azure crea y mantiene automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).


Para importar una base de datos SQL de un .bacpac, necesita lo siguiente:

- Una suscripción de Azure. 
- Servidor V12 de Base de datos SQL de Azure. Si no tiene un servidor V12, siga los pasos de este artículo para crear uno: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
- Un archivo .bacpac de la base de datos que quiere importar en un contenedor de blobs de [cuenta de almacenamiento de Azure (estándar)](../storage/storage-create-storage-account.md).

***Importante***: Al importar un archivo BACPAC a Almacenamiento de blobs de Azure, utilice el almacenamiento estándar. No se admite la importación de un archivo BACPAC de Almacenamiento premium.


## Seleccionar el servidor que contendrá la base de datos

Abra la hoja SQL Server:

1.	Vaya al [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **Servidores SQL Server**.
3.	Haga clic en el servidor en el que restaurará la base de datos.
4.	En la hoja de SQL Server, haga clic en **Importar base de datos** para abrir la hoja **Importar base de datos**:

    ![importar base de datos][1]

1.  Haga clic en **Almacenamiento** y seleccione su cuenta de almacenamiento, el contenedor de blobs y el archivo .bacpac, y haga clic en **Aceptar**.

    ![configurar opciones de almacenamiento][2]

1.  Seleccione el nivel de precios para la nueva base de datos y haga clic en **Seleccionar**. No se admite la importación de una base de datos directamente en un grupo elástico, pero puede importar primero en una base de datos única y luego mover la base de datos a un grupo.

    ![seleccione nivel de precios][3]

1.  Escriba un **NOMBRE DE BASE DE DATOS** para la base de datos que se va a crear desde el archivo BACPAC.
2.  Elija el tipo de autenticación y, luego, proporcione la información de autenticación del servidor. 
3.  Haga clic en **Crear** para crear la base de datos desde el BACPAC.

    ![crear base de datos][4]

Al hacer clic en **Crear** se envía una solicitud de importación de base de datos para el servicio. Según el tamaño de su base de datos, la operación de importación puede tardar algún tiempo en completarse.

## Supervisar el progreso de la operación de importación

1.	Haga clic en **Servidores SQL Server**.
2.	Haga clic en el servidor en el que va a restaurar.
3.	En la hoja del servidor SQL, área Operaciones, haga clic en **Historial de importación y exportación**:

    ![importar historial de exportación][5] ![importar historial de exportación][6]





## Comprobar que la base de datos está disponible en el servidor

1.	Haga clic en **Bases de datos SQL** y compruebe que la nueva base de datos está **En línea**.



## Pasos siguientes

- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)



## Recursos adicionales

- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-import/import-database.png
[2]: ./media/sql-database-import/storage-options.png
[3]: ./media/sql-database-import/pricing-tier.png
[4]: ./media/sql-database-import/create.png
[5]: ./media/sql-database-import/import-history.png
[6]: ./media/sql-database-import/import-status.png

<!---HONumber=AcomDC_0518_2016-->