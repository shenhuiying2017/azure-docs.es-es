<properties
	pageTitle="Copia de una instancia de Base de datos SQL de Azure mediante el Portal de Azure | Microsoft Azure"
	description="Creación de una copia de una base de datos SQL de Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="09/19/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>



# Copia de una instancia de Base de datos SQL de Azure mediante el Portal de Azure

> [AZURE.SELECTOR]
- [Información general](sql-database-copy.md)
- [Portal de Azure](sql-database-copy-portal.md)
- [PowerShell](sql-database-copy-powershell.md)
- [T-SQL](sql-database-copy-transact-sql.md)

En los siguientes pasos se muestra cómo copiar una base de datos SQL con [Azure Portal](https://portal.azure.com) en el mismo servidor o en otro distinto.

Para copiar una base de datos SQL, necesita lo siguiente:

- Una suscripción de Azure. Si necesita una suscripción a Azure, haga clic en la opción **PRUEBA GRATUITA** situada en la parte superior de esta página y, a continuación, vuelva para finalizar este artículo.
- Una base de datos SQL para copiar. Si no tiene ninguna base de datos SQL, siga los pasos de este artículo para crear una: [Creación de la primera base de datos SQL de Azure](sql-database-get-started.md).


## Copiar la base de datos SQL

Abra la página de base de datos SQL correspondiente a la base de datos que desea copiar:

1.	Vaya al [Portal de Azure](https://portal.azure.com).
2.	Haga clic en **More Services** (Más servicios) > **Bases de datos SQL** y, luego, en la base de datos que quiera.
3.	En la página de base de datos SQL, haga clic en **Copiar**:

    ![Base de datos SQL](./media/sql-database-copy-portal/sql-database-copy.png)

1.  En la página **Copiar**, se proporciona un nombre predeterminado para la base de datos. Escriba un nombre diferente si quiere (todas las bases de datos de un servidor deben tener nombres únicos).
2.  Seleccione un **Servidor de destino**. El servidor de destino es donde se creará la copia de la base de datos. Puede crear la copia de la base de datos en el mismo servidor o en otro distinto. Puede crear un nuevo servidor o seleccionar un servidor existente en la lista.
3.  Después de seleccionar el **servidor de destino**, se habilitarán las opciones **Grupo de bases de datos elásticas** y **Plan de tarifa**. Si el servidor tiene un grupo, puede copiar la base de datos en él.
3.  Haga clic en **Aceptar** para iniciar el proceso de copia.

    ![Base de datos SQL](./media/sql-database-copy-portal/copy-page.png)


## Supervisar el progreso de la operación de copia

- Después de iniciar la copia, haga clic en la notificación de portal para obtener información.

    ![notificación][3]
 
    ![monitor][4]


## Comprobar que la base de datos está disponible en el servidor

- Haga clic en **More Services** (Más servicios) > **Bases de datos SQL** y compruebe que la nueva base de datos está **en línea**.


## Resolución de inicios de sesión

Para resolver los inicios de sesión una vez completada la operación de copia, consulte [Resolución de inicios de sesión](sql-database-copy-transact-sql.md#resolve-logins-after-the-copy-operation-completes).


## Pasos siguientes

- Consulte [Copiar una base de datos SQL de Azure](sql-database-copy.md) para obtener información sobre cómo copiar una base de datos SQL de Azure.
- Consulte [Copia de una Base de datos SQL de Azure con PowerShell](sql-database-copy-powershell.md) para copiar una base de datos mediante PowerShell.
- Consulte [Copia de una Base de datos SQL de Azure con Transact-SQL](sql-database-copy-transact-sql.md) para copiar una base de datos mediante Transact-SQL.
- Consulte [Administración de la seguridad de Base de datos SQL de Azure después de la recuperación ante desastres](sql-database-geo-replication-security-config.md) para obtener información sobre cómo administrar usuarios e inicios de sesión al copiar una base de datos a un servidor lógico diferente.



## Recursos adicionales

- [Administración de inicios de sesión](sql-database-manage-logins.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)
- [Exportar la base de datos a un BACPAC](sql-database-export.md)
- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de la base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)




<!--Image references-->
[1]: ./media/sql-database-copy-portal/copy.png
[2]: ./media/sql-database-copy-portal/copy-ok.png
[3]: ./media/sql-database-copy-portal/copy-notification.png
[4]: ./media/sql-database-copy-portal/monitor-copy.png

<!---HONumber=AcomDC_0921_2016-->