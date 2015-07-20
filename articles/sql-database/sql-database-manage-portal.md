<properties 
	pageTitle="Administración de Bases de datos SQL de Azure con el Portal de administración de Azure" 
	description="Aprenda a usar el Portal de administración de Azure para administrar una base de datos relacional en la nube mediante el Portal de administración de Azure." 
	services="sql-database" 
	documentationCenter="" 
	authors="stevestein" 
	manager="jeffreyg" 
	editor=""/>

<tags 
	ms.service="sql-database" 
	ms.devlang="NA" 
	ms.workload="data-management" 
	ms.topic="article" 
	ms.tgt_pltfrm="NA" 
	ms.date="04/14/2015" 
	ms.author="sstein"/>


# Administración de Bases de datos SQL de Azure con el Portal de administración de Azure

El [Portal de administración de Azure][Management Portal] le permite crear, supervisar y administrar servidores y Bases de datos SQL de Azure. En este artículo se verán las operaciones de base de datos que se puede lograr mediante el Portal de administración. Puede obtener más información acerca de las otras herramientas de administración de Base de datos SQL de Azure [aquí][AzureDb management overview].

>[AZURE.NOTE]Si no está familiarizado con el Portal de administración de Azure, este [recorrido en vídeo proporciona una introducción rápida][Azure Portal Tour] de sus conceptos y características generales.

![Información general de la base de datos](./media/sql-database-manage-portal/sqldatabase_annotated.png)

## 1. Acciones de administración de bases de datos
![Acciones de administración de bases de datos](./media/sql-database-manage-portal/sqldatabase_actions.png)

El Portal de administración de Azure proporciona un conjunto de acciones comunes de base de datos accesibles en la parte superior de una hoja de la base de datos. Puede restaurar una base de datos a un momento anterior en el tiempo, abra una base de datos en Visual Studio, copiar una base de datos a un nuevo servidor y exportar la base de datos a una cuenta de Almacenamiento de Azure.

## 2. Supervisión de la base de datos
![Supervisión de la base de datos](./media/sql-database-manage-portal/sqldatabase_monitoring.png)

De forma predeterminada, Bases de datos SQL de Azure cuentan con gráficos de supervisión para la unidad de rendimiento de base de datos (DTU), el tamaño de la base de datos y el estado de conexión. Estos gráficos de supervisión pueden personalizarse y ampliarse para trazar además en el gráfico el porcentaje de CPU, el porcentaje de E/S de datos, los interbloqueos, el porcentaje de E/S de registro o incluso el porcentaje de solicitudes bloqueadas por firewall. Encontrará más información acerca de cómo personalizar los gráficos de supervisión [aquí][Azure part monitoring].

Además, se pueden configurar reglas de alerta para supervisar una métrica especificada y alertar a un administrador y a un coadministrador designados cuando se alcanzan los umbrales predefinidos. Encontrará más información sobre cómo configurar las reglas de alertas en el Portal de administración de Azure [aquí][Azure part monitoring].

## 3. Auditoría y seguridad de la base de datos
![Seguridad de la base de datos](./media/sql-database-manage-portal/sqldatabase_security.png)

Se pueden configurar Bases de datos SQL de Azure para que realicen un seguimiento de los eventos de base de datos y escribirlos en un registro de auditoría en la cuenta de Almacenamiento de Azure. Esta característica puede ayudarle a mantener el cumplimiento de normativas, comprender la actividad de las bases de datos y conocer las discrepancias que pueden indicar problemas en el negocio o infracciones de seguridad sospechosas. Encontrará más información acerca de la auditoría de Base de datos SQL de Azure [aquí][AzureDb Auditing]

Bases de datos SQL de Azure también pueden configurarse para enmascarar los datos confidenciales a usuarios sin privilegios. Encontrará más información sobre la características de Enmascaramiento dinámico de datos de Bases de datos SQL de Azure [aquí][AzureDb datamasking]

## 4. Replicación geográfica
![Replicación geográfica](./media/sql-database-manage-portal/sqldatabase_georeplication.png)

Se pueden configurar Bases de datos SQL Azure para replicar asincrónicamente las transacciones confirmadas en una base de datos secundaria. La parte de la replicación geográfica en el Portal de administración permite seleccionar la región de Azure en la que le gustaría que resida la base de datos secundaria. Encontrará más información sobre la replicación geográfica de las base de datos en Azure [aquí][Database geo-replication]

##Recursos adicionales
* [Introducción a Base de datos SQL][]   
* [Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio][]   
* [Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica][]   
* [Referencia de Transact-SQL (Base de datos SQL)][]
  
  [Azure Portal Tour]: https://go.microsoft.com/fwlink/?LinkID=522341
  [Management Portal]: https://portal.azure.com
  [Azure part monitoring]: ../documentdb-monitor-accounts.md
  [AzureDb management overview]: http://azure.microsoft.com/blog/2014/12/22/client-tooling-updates-for-azure-sql-database/
  [Introducción a Base de datos SQL]: http://azure.microsoft.com/services/sql-database
  [Database geo-replication]: http://azure.microsoft.com/blog/2014/07/12/spotlight-on-sql-database-active-geo-replication/
  [Administración de Base de datos SQL de Azure con el uso de SQL Server Management Studio]: sql-database-manage-azure-ssms.md
  [Supervisión de Base de datos SQL de Azure mediante vistas de administración dinámica]: http://msdn.microsoft.com/library/windowsazure/ff394114.aspx
  [Referencia de Transact-SQL (Base de datos SQL)]: http://msdn.microsoft.com/library/bb510741(v=sql.120).aspx
  [AzureDb Auditing]: http://azure.microsoft.com/documentation/articles/sql-database-auditing-get-started/
  [AzureDb datamasking]: http://azure.microsoft.com/documentation/articles/sql-database-dynamic-data-masking-get-started/

 
 

<!---HONumber=July15_HO2-->