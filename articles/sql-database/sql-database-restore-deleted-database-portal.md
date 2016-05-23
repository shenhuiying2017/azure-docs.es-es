<properties
	pageTitle="Restauración de una base de datos SQL de Azure eliminada (Portal de Azure) | Microsoft Azure"
	description="Restauración de una base de datos SQL de Azure eliminada (Portal de Azure)."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="05/10/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Restaurar una base de datos SQL de Azure eliminada con el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-restore-deleted-database-portal.md)
- [PowerShell](sql-database-restore-deleted-database-powershell.md)

En este artículo se muestra cómo restaurar una Base de datos SQL de Azure eliminada.

En caso de que se elimine una base de datos, Base de datos SQL de Azure le permite restaurar la base de datos eliminada en el momento en que se eliminó. Base de datos SQL de Azure almacena la copia de seguridad de la base de datos eliminada durante el período de retención de la base de datos.

El período de retención de una base de datos eliminada lo determinan el nivel de servicio de la base de datos mientras esta existe, o bien el número de días en que existe la base de datos, el menor de estos dos valores. Para más información acerca de la retención de la base de datos, lea [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md).

## Selección de la base de datos que se va a restaurar 

Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1.	Abra el [Portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **EXAMINAR** > **Servidores SQL Server**.
3.  Vaya al servidor con la base de datos que desea restaurar y selecciónelo.
4.  Desplácese hacia abajo hasta la sección **Operaciones** de la hoja del servidor y seleccione **Bases de datos eliminadas**: ![Restauración de una base de datos SQL de Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted-trashbin.png)
5.  Seleccione la base de datos eliminada que desee restaurar.
6.  Especifique un nombre de base de datos y haga clic en Aceptar:

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-restore-deleted-database-portal/restore-deleted.png)

## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)



## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->