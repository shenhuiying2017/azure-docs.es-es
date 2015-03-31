<properties 
   pageTitle="Recuperar una base de datos de SQL Azure mediante la restauración geográfica en el portal de Azure" 
   description="Restauración geográfica, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, portal de administración de Azure, portal de Azure" 
   services="sql-database" 
   documentationCenter="" 
   authors="elfisher" 
   manager="jeffreyg" 
   editor="v-romcal"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="storage-backup-recovery" 
   ms.date="02/24/2015"
   ms.author="elfish; v-romcal"/>

# Recuperar una base de datos de SQL Azure mediante la restauración geográfica en el portal de Azure

> [AZURE.SELECTOR]
- [Restauración geográfica: PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-powershell/)
- [Restauración geográfica: API de REST](http://azure.microsoft.com/documentation/articles/sql-database-geo-restore-tutorial-rest/)

## Información general

Este tutorial muestra cómo recuperar una base de datos SQL de Azure mediante la restauración geográfica en el [Portal de Azure](http://manage.windowsazure.com/). La restauración geográfica es la principal protección de recuperación ante desastres incluida para todos los niveles de servicio de bases de datos Basic, Standard y Premium de Azure SQL.

## Restricciones y seguridad

* La restauración geográfica está habilitada para todos los niveles de servicio Basic, Standard y Premium.

* Los períodos de retención de la copia de seguridad son 7 días para Basic, 14 días para Standard y 35 días para Premium.

* Solo el administrador y el coadministrador de la suscripción pueden enviar solicitudes de restauración.

* El inicio de sesión de entidad de seguridad a nivel de servidor será el propietario de la base de datos restaurada.

* Los niveles de servicio Web y Business Edition no admiten la restauración geográfica.
 
	* Si tiene una base de datos Web o Business Edition, puede usar la copia de base de datos para obtener una copia transaccional de la base de datos y, a continuación, exportar la base de datos copiada a una cuenta de almacenamiento de Microsoft Azure. Para obtener más información, consulte [Inserción de Uso de la copia de base de datos (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) y[ Uso de los servicios de importación y exportación en la base de datos de Azure SQL](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* Las ediciones Web y Business Editions se retirarán en septiembre de 2015. Para más información, consulte [Preguntas más frecuentes sobre la retirada de las ediciones Web y Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Cómo: Recuperar una base de datos de SQL Azure mediante la restauración geográfica en el portal de Azure

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Geo-Restore/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

1. Inicie sesión en el portal de Azure con su cuenta de Microsoft y seleccione **Bases de datos SQL**.

2. En el panel izquierdo, haga clic en **BASES DE DATOS SQL**.

3. En la parte superior de la página, haga clic en **SERVIDORES**.

4. En la lista **SERVIDORES**, haga clic en el servidor **Degradado**.

4. En la parte superior de la página, haga clic en **COPIAS DE SEGURIDAD**.

5. Haga clic en la base de datos que desee restaurar.

6. En la parte inferior de la página, en la barra de comandos, haga clic en **Restaurar**. Aparecerá el cuadro de diálogo **Especificar la configuración de restauración**. 

7. Elija el **NOMBRE DE BASE DE DATOS** y el **SERVIDOR DE DESTINO** en el que desea restaurar la base de datos. De forma predeterminada, ya hay un nombre de base de datos elegido, pero puede cambiarlo si lo desea.   

9. Haga clic en la marca de verificación para enviar la solicitud de restauración.

Una restauración puede tardar algún tiempo en completarse. Para supervisar el estado de la restauración, haga clic en el icono de estado en la parte inferior derecha de la página en la barra de comandos y, a continuación, haga clic en **DETALLES**.

## Pasos siguientes

Para obtener más información, consulte los temas siguientes: 

[Restaurar una base de datos de Azure SQL mediante la Restauración a un momento dado en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-management-portal/)

[Restaurar una base de datos de SQL Azure eliminada en el portal de Azure](http://azure.microsoft.com/documentation/articles/sql-database-restore-deleted-database-tutorial-management-portal/)

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauración geográfica de base de datos de Azure SQL (blog)](http://azure.microsoft.com/blog/2014/09/13/azure-sql-database-geo-restore/)
<!--HONumber=47-->
