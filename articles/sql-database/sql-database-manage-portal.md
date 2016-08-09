<properties
	pageTitle="Administración de bases de datos SQL de Azure con el Portal de Azure"
	description="Aprenda a usar el Portal de Azure para administrar una base de datos relacional en la nube mediante el Portal de Azure."
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"
	ms.date="05/20/2016"
	ms.author="sstein"/>


# Administración de Bases de datos SQL de Azure con el Portal de Azure


> [AZURE.SELECTOR]
- [Portal de Azure](sql-database-manage-portal.md)
- [SSMS](sql-database-manage-azure-ssms.md)
- [PowerShell](sql-database-command-line-tools.md)

El [Portal de Azure](https://portal.azure.com/) le permite crear, supervisar y administrar servidores lógicos y bases de datos SQL de Azure. En este artículo se resumen algunas de las tareas más comunes.

![Información general de la base de datos](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1\. Acciones de administración de bases de datos

![Acciones de administración de bases de datos](./media/sql-database-manage-portal/sqldatabase_actions.png)

El Portal de Azure proporciona un conjunto de acciones de base de datos comunes a las que se puede acceder en la parte superior de una hoja de la base de datos. Puede restaurar una base de datos a un momento anterior en el tiempo, abra una base de datos en Visual Studio, copiar una base de datos a un nuevo servidor y exportar la base de datos a una cuenta de Almacenamiento de Azure.

- [Restauración de una base de datos SQL](sql-database-recovery-using-backups.md)
- [Apertura de una base de datos SQL en Visual Studio](sql-database-connect-query.md)
- [Exportación de una base de datos SQL](sql-database-export.md)

## 2\. Supervisión de la base de datos

![Supervisión de la base de datos](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

De forma predeterminada, las bases de datos SQL de Azure cuentan con gráficos de supervisión de la unidad de transacción de base de datos (DTU), el tamaño de la base de datos y el estado de conexión. Estos gráficos de supervisión pueden personalizarse y ampliarse para trazar además en el gráfico el porcentaje de CPU, el porcentaje de E/S de datos, los interbloqueos, el porcentaje de E/S de registro o incluso el porcentaje de solicitudes bloqueadas por firewall.

Además, se pueden configurar reglas de alerta para supervisar una métrica especificada y alertar a un administrador y a un coadministrador designados cuando se alcanzan los umbrales predefinidos.

## 3\. Auditoría y seguridad de la base de datos

![Seguridad de la base de datos](./media/sql-database-manage-portal/sqldatabase_security.png)

Se pueden configurar Bases de datos SQL de Azure para que realicen un seguimiento de los eventos de base de datos y escribirlos en un registro de auditoría en la cuenta de Almacenamiento de Azure. Esta característica puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas.

- [Auditoría de Base de datos SQL](sql-database-auditing-get-started.md)

Las bases de datos SQL de Azure también pueden configurarse para enmascarar los datos confidenciales a usuarios sin privilegios.

- [Enmascaramiento de datos dinámicos](sql-database-dynamic-data-masking-get-started.md)


## 4\. Replicación geográfica

![Replicación geográfica](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Se pueden configurar Bases de datos SQL Azure para replicar asincrónicamente las transacciones confirmadas en una base de datos secundaria. La parte de la replicación geográfica del portal permite seleccionar la región de Azure en la que le gustaría que residiera la base de datos secundaria.

- [Replicación geográfica](sql-database-geo-replication-overview.md)



## Recursos adicionales

- [Base de datos SQL](sql-database-technical-overview.md)

<!---HONumber=AcomDC_0727_2016-->