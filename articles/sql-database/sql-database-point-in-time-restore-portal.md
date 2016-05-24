<properties
	pageTitle="Restauración de una base de datos SQL de Azure a un momento dado anterior (Portal de Azure) | Microsoft Azure"
	description="Restauración de una base de datos SQL de Azure a un momento dado anterior."
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


# Restauración de una base de datos SQL de Azure a un momento dado anterior con el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-point-in-time-restore-portal.md)
- [PowerShell](sql-database-point-in-time-restore-powershell.md)

En este artículo se muestra cómo restaurar la base de datos a un momento anterior a través del Portal de Azure.

[**Restauración a un momento dado**](sql-database-point-in-time-restore.md) es una funcionalidad autoservicio que le permite restaurar una base de datos a partir de las copias de seguridad automáticas que realizamos de todas ellas a cualquier momento de su período de retención. Más información sobre las copias de seguridad automáticas y los períodos de retención de las bases de datos, consulte [Información general: continuidad del negocio en la nube y recuperación ante desastres con la Base de datos SQL](sql-database-business-continuity.md).

## Selección de una base de datos para restaurarla a un momento dado anterior

Para restaurar una base de datos en el Portal de Azure, haga lo siguiente:

1.	Abra el [Portal de Azure](https://portal.azure.com).
2.  En el lado izquierdo de la pantalla, seleccione **EXAMINAR** > **Bases de datos SQL**.
3.  Vaya a la base de datos que quiera restaurar y selecciónela.
4.  En la parte superior de la hoja de la base de datos, seleccione **Restaurar**:

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore.png)

5.  Especifique el nombre de base de datos y un momento dado y haga clic en Aceptar:

    ![Restauración de una base de datos SQL de Azure](./media/sql-database-point-in-time-restore-portal/restore-details.png)


## Pasos siguientes

- [Finalización de una base de datos SQL de Azure recuperada](sql-database-recovered-finalize.md)
- [Conexión a la Base de datos SQL con SQL Server Management Studio y realización de una consulta de T-SQL de ejemplo](sql-database-connect-query-ssms.md)



## Recursos adicionales

- [Información general acerca de la continuidad del negocio](sql-database-business-continuity.md)
- [Documentación de Base de datos SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!---HONumber=AcomDC_0511_2016-->