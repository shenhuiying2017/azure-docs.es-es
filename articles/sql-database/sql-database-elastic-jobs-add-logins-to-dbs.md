<properties 
	title="How to add a users to an elastic database pool" 
	pageTitle="Cómo agregar usuarios a un grupo de bases de datos elásticas" 
	description="Debe agregar un usuario con privilegios para cada base de datos del grupo." 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="sidneyh"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/20/2015" 
	ms.author="sidneyh" />

# Cómo agregar usuarios a un grupo de bases de datos elásticas

Los trabajos de bases de datos elásticas permiten ejecutar la misma secuencia de comandos en cada base de datos de un [grupo de bases de datos elásticas](sql-database-elastic-pool.md). Para ejecutar la secuencia de comandos, deberá agregar un usuario con los permisos adecuados para cada base de datos del grupo. Para obtener más información, vea [Administración de bases de datos e inicios de sesión en su base de datos SQL de Azure](https://msdn.microsoft.com/library/azure/ee336235.aspx?f=255&MSPPError=-2147217396) o [Adición de usuarios a la base de datos SQL de Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/).

## Requisitos previos
* [Creación de un grupo de bases de datos elásticas (vista previa)](sql-database-elastic-pool-portal.md)
* Instale los [componentes de trabajo elástico](sql-database-elastic-jobs-service-installation.md). 

## Cómo agregar los usuarios a las bases de datos

1.	En primer lugar, conéctese al equipo **maestro** del servidor de la base de datos SQL de Azure en el que reside el grupo de bases de datos elásticas y cree un nuevo inicio de sesión con las mismas credenciales que proporcionó al instalar los **trabajos de la base de datos elástica**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Inicie sesión en cada base de datos del grupo y cree un usuario con el mismo nombre y contraseña. El usuario debe tener permisos suficientes para ejecutar el trabajo. Este código se debe ejecutar en cada base de datos.

		CREATE USER admin1 FROM LOGIN login1;
		
3. El usuario también debe tener permisos suficientes para ejecutar la secuencia de comandos especificada para el trabajo. Use procedimiento **sp_addrolemember** para proporcionar al usuario los permisos mínimos necesarios para ejecutar correctamente la secuencia de comandos.

## Pasos siguientes

Ejecute el trabajo en el grupo de bases de datos elásticas. Vea [Creación y administración de trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=58_postMigration-->