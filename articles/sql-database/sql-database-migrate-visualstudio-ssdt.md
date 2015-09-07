<properties 
   pageTitle="Migración con Visual Studio y SSDT"
	description="Base de datos SQL de Microsoft Azure, migración de base de datos, importación de base de datos, exportación de base de datos, asistente para migración"
	services="sql-database"
	documentationCenter=""
	authors="carlrabeler"
	manager="jeffreyg"
	editor=""/>

<tags
   ms.service="sql-database"
	ms.devlang="NA"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.workload="data-management"
	ms.date="08/24/2015"
	ms.author="carlrab"/>

#Actualización de la base de datos in situ para su posterior implementación en Base de datos SQL de Azure

![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/01VSSSDTDiagram.png)

Use esta opción cuando la migración de una base de datos local a Base de datos SQL de Azure V12 requiere cambios de esquema porque la base de datos usa características de SQL Server que no se admiten en la Base de datos SQL de Azure o para comprobar si las características no compatibles están presentes en una base de datos local.

Use las [SQL Server Data Tools para Visual Studio más recientes](https://msdn.microsoft.com/library/mt204009.aspx) con Visual Studio 2013 Update 4 o versiones posteriores.

Con esta opción:

 - SQL Server Data Tools para Visual Studio ("SSDT") se usa en primer lugar para crear un proyecto de base de datos a partir de la base de datos de origen. 
 - La plataforma de destino del proyecto se establece en Base de datos SQL de Azure V12 y se compila el proyecto para identificar todos los problemas de compatibilidad. 
 - Cuando el proyecto se compila correctamente, el esquema se publica en una copia de la base de datos de origen (no se sobrescribe la base de datos de origen).
 - La característica de comparación de datos de SSDT se usa a continuación para comparar la base de datos de origen con la base de datos compatible con SQL Azure recién creada y, a continuación, actualiza la nueva base de datos con datos de la base de datos de origen. 
 - La base de datos actualizada se implementa a continuación en Azure mediante SSMS, ya sea directamente o bien mediante la exportación e importación de un archivo BACPAC.
 
>[AZURE.NOTE]Nota: Si solo es necesario la implementación del esquema, el esquema actualizado se puede publicar directamente desde Visual Studio a Base de datos SQL de Azure.

## Pasos de migración

1.	Abra el **Explorador de objetos de SQL Server** en Visual Studio. Use **Agregar SQL Server** para conectarse a la instancia de SQL Server que contiene la base de datos que se está migrando. Busque la base de datos en el explorador, haga clic con el botón derecho en ella y seleccione **Crear nuevo proyecto...** 

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/02MigrateSSDT.png)

2.	Configure las opciones de importación para **solo los objetos con ámbito de aplicación de importación**. Desmarque las opciones para importar los inicios de sesión de referencia, permisos y configuraciones de base de datos.

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/03MigrateSSDT.png)

3.	Haga clic en **Iniciar** para importar la base de datos y crear el proyecto, que contiene un archivo de scripts T-SQL para cada objeto de la base de datos. Los archivos de scripts se anidan en las carpetas del proyecto.

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/04MigrateSSDT.png)

4.	En el Explorador de soluciones de Visual Studio, haga clic con el botón derecho y seleccione Propiedades. Se abrirá la página **Configuración del proyecto** en donde va a configurar la plataforma de destino para Base de datos de SQL de Microsoft Azure V12.

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/05MigrateSSDT.png)

5.	Haga clic con el botón derecho en el proyecto y seleccione **Crear** para crear el proyecto.

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/06MigrateSSDT.png)

6.	La **lista de errores** muestra cada incompatibilidad. En este caso, el nombre de usuario NT AUTHORITY\\NETWORK SERVICE es incompatible. Puesto que es incompatible, puede comentarlo o quitarlo (y tratar las implicaciones de eliminar este inicio de sesión y rol de la solución de base de datos).

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/07MigrateSSDT.png)
7.	Haga doble clic en el primer script para abrirlo en una ventana de consulta, coméntelo y, a continuación, ejecute el script. ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/08MigrateSSDT.png)

8.	Repita este proceso para cada script que contenga incompatibilidades hasta que no quede ningún error. ![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/09MigrateSSDT.png)
9.	Cuando la base de datos está libre de errores, haga clic con el botón derecho en el proyecto y seleccione **Publicar** para generar y publicar la base de datos en una copia de la base de datos de origen (se recomienda usar una copia, al menos inicialmente). 
 - Antes de realizar la publicación, dependiendo de la versión de SQL Server de origen (anterior a SQL Server 2014), es posible que necesite restablecer la plataforma de destino del proyecto para habilitar la implementación. 
 - Si va a migrar una base de datos de SQL Server anterior, no debe introducir ninguna característica en el proyecto que no se admita en SQL Server de origen a menos que migre primero la base de datos a una versión más reciente de SQL Server. 

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/10MigrateSSDT.png)

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/11MigrateSSDT.png)

10.	En el Explorador de objetos de SQL Server, haga clic con el botón derecho en la base de datos de origen y haga clic en **Comparación de datos** para comparar el proyecto con la base de datos original para comprender qué cambios ha realizado el asistente. Seleccione la versión Azure SQL V12 de la base de datos y, a continuación, haga clic en **Finalizar**.

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/12MigrateSSDT.png)

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/13MigrateSSDT.png)

12.	Revise las diferencias detectadas y, a continuación, haga clic en **Actualizar destino** para migrar datos de la base de datos de origen a la base de datos Azure SQL V12.

	![texto alternativo](./media/sql-database-migrate-visualstudio-ssdt/14MigrateSSDT.png)

14.	Implemente el esquema de base de datos compatible con Azure SQL V12 y los datos a Base de datos SQL de Azure mediante SSMS. Consulte [Migración de una base de datos compatible con SSMS](sql-database-migrate-ssms.md).

<!---HONumber=August15_HO9-->