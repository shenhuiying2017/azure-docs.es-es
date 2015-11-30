<properties 
	pageTitle="Cómo agregar usuarios a un grupo de bases de datos elásticas" 
	description="Debe agregar un usuario con privilegios para cada base de datos del grupo." 
	metaKeywords="azure sql database elastic databases credentials" 
	services="sql-database" documentationCenter=""  
	manager="jeffreyg" 
	authors="ddove"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/03/2015" 
	ms.author="ddove; sidneyh" />

# Cómo agregar usuarios a un grupo de bases de datos elásticas

La característica **Trabajos de base de datos elástica** (vista previa) le permite ejecutar un script de Transact-SQL transversalmente en un grupo de bases de datos, que puede incluir una colección de bases de datos personalizada definida por el usuario, un **grupo de bases de datos elásticas** o un **conjunto de particiones de bases de datos elásticas** en Base de datos de SQL de Azure. Para ejecutar el script, deberá agregar un usuario con los permisos adecuados en cada base de datos en la que se ejecute el trabajo. Para obtener más información, vea [Administración de bases de datos e inicios de sesión en su base de datos SQL de Azure](sql-database-manage-logins.md) o [Adición de usuarios a la base de datos SQL de Azure](http://azure.microsoft.com/blog/2010/06/21/adding-users-to-your-sql-azure-database/).

## Requisitos previos
* Instale los [componentes de trabajo elástico](sql-database-elastic-jobs-service-installation.md). 

## Cómo agregar los usuarios a las bases de datos

1.	En primer lugar, conéctese al equipo **maestro** del servidor de la base de datos SQL de Azure en el que reside el grupo de bases de datos elásticas y cree un nuevo inicio de sesión con las mismas credenciales que proporcionó al instalar los **trabajos de la base de datos elástica**.

		CREATE LOGIN login1 WITH password='<ProvidePassword>';

2. Inicie sesión en cada base de datos del grupo y cree un usuario con el mismo nombre y contraseña. El usuario debe tener permisos suficientes para ejecutar el trabajo. Este código se debe ejecutar en cada base de datos.

		CREATE USER admin1 FROM LOGIN login1;
		
3. El usuario también debe tener permisos suficientes para ejecutar la secuencia de comandos especificada para el trabajo. Use [ALTER ROLE](https://msdn.microsoft.com/library/ms189775.aspx) para proporcionar al usuario los permisos mínimos necesarios para ejecutar correctamente el script.

## Pasos siguientes

Para crear y administrar trabajos mediante el Portal de Azure, consulte [Crear y administrar trabajos de bases de datos elásticas](sql-database-elastic-jobs-create-and-manage.md). Para crear trabajos mediante PowerShell, consulte [Crear y administrar trabajos de bases de datos elásticas de la Base de datos SQL mediante PowerShell (vista previa)](sql-database-elastic-jobs-powershell.md).

[AZURE.INCLUDE [elastic-scale-include](../../includes/elastic-scale-include.md)]

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-overview/elastic-jobs.png
<!--anchors-->

<!---HONumber=Nov15_HO4-->