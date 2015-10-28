<properties
	pageTitle="Crear y exportar un BACPAC de una base de datos SQL de Azure"
	description="Crear y exportar un BACPAC de una base de datos SQL de Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="10/13/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Crear y exportar un BACPAC de una base de datos SQL de Azure

**Base de datos única**

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-database-export.md)
- [PowerShell](sql-database-export-powershell.md)

Este artículo proporciona instrucciones para exportar un BACPAC de la base de datos SQL de Azure con el [portal de vista previa de Azure](https://portal.azure.com).

Un BACPAC es un archivo .bacpac que contiene datos y un esquema de base de datos. Para obtener detalles, vea el paquete de copia de seguridad (.bacpac) en [Aplicaciones del nivel de datos](https://msdn.microsoft.com/library/ee210546.aspx).

> [AZURE.NOTE]La Base de datos SQL de Azure crea automáticamente copias de seguridad para cada base de datos de usuario. Para obtener detalles, vea [Información general sobre la continuidad del negocio](sql-database-business-continuity.md).


El BACPAC se exporta a un contenedor de blobs de almacenamiento de Azure que puede descargar cuando se completa la operación correctamente.

Para completar este artículo, necesitará lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una Base de datos SQL de Azure. Si no tiene una base de datos SQL, siga los pasos de este artículo para crear uno: [Creación de la primera Base de datos SQL de Azure](sql-database-get-started.md).
- Una [cuenta de almacenamiento de Azure](storage-create-storage-account.md) con un contenedor de blobs para almacenar la copia de seguridad de la base de datos. Actualmente la cuenta de almacenamiento debe usar el modelo de implementación clásico; por tanto, elija **Clásica** al crear una cuenta de almacenamiento. 


## Exportar la base de datos

Abra la hoja de la Base de datos SQL para la base de datos que desea exportar como archivo .bacpac:

1.	Vaya al [Portal de vista previa de Azure](https://portal.azure.com).
2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Bases de datos SQL**.
2.	Haga clic en la base de datos que quiere exportar como BACPAC.
3.	En la hoja de la Base de datos SQL, haga clic en **Exportar** para abrir la hoja **Exportar base de datos**:

    ![botón Exportar][1]

1.  Haga clic en **Almacenamiento** y seleccione su cuenta de almacenamiento y contenedor de blobs donde se almacenará el BACPAC:

    ![exportar base de datos][2]

1.  Escriba el **Inicio de sesión de administrador de servidor** y **Contraseña** para el servidor SQL de Azure que contiene la base de datos de la que está realizando la copia de seguridad.
1.  Haga clic en **Crear** para exportar la base de datos.

Al hacer clic en **Crear** se crea una solicitud de base de datos de exportación y se envía al servicio. Según el tamaño de la base de datos, la operación de exportación puede tardar algún tiempo en completarse.

## Supervisar el progreso de la operación de exportación

2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Servidores SQL Server**.
2.	Haga clic en el servidor que contiene la base de datos original (de origen) que acaba de exportar.
3.	En la hoja del servidor SQL, haga clic en **Historial de importación y exportación**:

    ![importar historial de exportación][3] ![importar historial de exportación][4]

## Comprobar que el BACPAC está en su contenedor de almacenamiento

2.	Haga clic en **EXAMINAR TODO**.
3.	Haga clic en **Cuentas de almacenamiento (clásico)**.
2.	Haga clic en la cuenta de almacenamiento donde ha guardado el BACPAC.
3.	Haga clic en **Contenedores** y seleccione el contenedor en el que exportó la base de datos para obtener detalles de la copia de seguridad (puede descargar y guardar el archivo BACPAC desde aquí).

    ![detalles de archivo .bacpac][5]


## Pasos siguientes

- [Importar una base de datos SQL de Azure](sql-database-import.md)



## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Obtención de detalles de la recuperación ante desastres](sql-database-disaster-recovery-drills.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-export/export.png
[2]: ./media/sql-database-export/export-blade.png
[3]: ./media/sql-database-export/export-history.png
[4]: ./media/sql-database-export/export-status.png
[5]: ./media/sql-database-export/bacpac-details.png

<!---HONumber=Oct15_HO3-->