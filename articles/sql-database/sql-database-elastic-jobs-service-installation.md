<properties 
	pageTitle="Instalación de trabajos de bases de datos elásticas" 
	description="Pasos de instalación de la característica de trabajo elástico." 
	services="sql-database" 
	documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/25/2015" 
	ms.author="sidneyh"/>

# Instalación del componente de trabajo de base de datos elástica

El [grupo de bases de datos elásticas (versión preliminar)](sql-database-elastic-pool-portal.md) proporciona un modelo predecible para la implementación de un gran número de bases de datos. Una vez creado el grupo de bases de datos elásticas, podrá usar los **trabajos de bases de datos elásticas** para ejecutar tareas administrativas en cada base de datos del grupo de bases de datos elásticas. Por ejemplo, puede implementar un nuevo esquema como, por ejemplo, establecer una directiva de RLS en cada base de datos, para restringir la visualización de datos confidenciales solo a las personas con las credenciales correctas. A continuación se muestra cómo instalar los **trabajos de bases de datos elásticas**.

**Tiempo estimado para completar el tutorial:** 10 minutos.

## Requisitos previos
* Una suscripción de Azure. Para obtener una prueba gratuita, vea [Prueba gratuita de un mes](http://azure.microsoft.com/pricing/free-trial/).
* Grupo de bases de datos elásticas. Consulte [Creación de un grupo de bases de datos elásticas de Base de datos SQL de Azure (versión preliminar)](sql-database-elastic-pool-portal.md).

## Instalación de componentes de servicio
En primer lugar, diríjase al [Portal de vista previa de Azure](https://ms.portal.azure.com/#).


1. Desde la vista del panel del grupo de bases de datos elásticas, haga clic en **Crear trabajo**.
2. Si va a crear un trabajo por primera vez, deberá instalar los **trabajos de bases de datos elásticas** haciendo clic en **TÉRMINOS DE VISTA PREVIA**. 
3. Acepte los términos haciendo clic en la casilla de verificación.
4. En la vista "Instalar servicios", haga clic en **CREDENCIALES DEL TRABAJO**.

	![Instalación de los servicios][1]

5. Escriba un nombre de usuario y una contraseña de administración de la base de datos. Si ya existe un usuario común para la ejecución de secuencias de comandos en el grupo de bases de datos elásticas, considere la opción de usar este mismo usuario para eliminar la necesidad de agregar un nuevo usuario en cada base de datos para la ejecución de secuencias de comandos. Como parte de la instalación, se creará un nuevo servidor de base de datos SQL de Azure. En este nuevo servidor, se creará una nueva base de datos, conocida como la base de datos de control, que se usa para contener los metadatos para los trabajos de bases de datos elásticas. El nombre de usuario y la contraseña creados se crean para dos propósitos: (1) para iniciar sesión en base de datos de control y (2) como las credenciales usadas para iniciar sesión en cada base de datos del grupo elástico siempre que se ejecute un trabajo para de ejecución de secuencias de comandos.
 
	![Creación del nombre de usuario y la contraseña][2]
6. Haga clic en el botón Aceptar. Los componentes se crean en pocos minutos en un nuevo [Grupo de recursos](../resource-group-portal.md). El nuevo grupo de recursos se ancla al panel de inicio, tal como se muestra a continuación. Una vez creados, los trabajos de bases de datos elásticas (servicio de nube, base de datos SQL, bus de servicio y almacenamiento) se crearán en el grupo.

	![Grupo de recursos en el panel de inicio][3]


7. Si intenta crear o administrar un trabajo mientras se está instalando trabajos de bases de datos elásticas, se mostrará el mensaje siguiente al proporcionar las **Credenciales**.

	![Implementación en curso][4]

8. Si se produce un error en la instalación por alguna razón, elimine el grupo de recursos. Vea [Desinstalación de componentes de trabajo de bases de datos elásticas](sql-database-elastic-jobs-uninstall.md).


## Pasos siguientes

Si se proporcionaron nuevas credenciales al instalar trabajos de bases de datos elásticas que ya no existen en cada base de datos del grupo de bases de datos elásticas con los derechos adecuados para ejecutar secuencias de comandos, deberá crear la credencial en cada base de datos. Vea [Cómo agregar usuarios a un grupo de bases de datos elásticas](sql-database-elastic-jobs-add-logins-to-dbs.md). Para comprender la creación de trabajos, vea [Creación y administración de un trabajo de base de datos elástica](sql-database-elastic-jobs-create-and-manage.md).

<!--Image references-->
[1]: ./media/sql-database-elastic-jobs-service-installation/screen-1.png
[2]: ./media/sql-database-elastic-jobs-service-installation/credentials.png
[3]: ./media/sql-database-elastic-jobs-service-installation/start-board.png
[4]: ./media/sql-database-elastic-jobs-service-installation/incomplete.png
 

<!---HONumber=July15_HO3-->