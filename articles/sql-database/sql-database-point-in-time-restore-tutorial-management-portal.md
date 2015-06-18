<properties 
   pageTitle="Restaurar una base de datos de Azure SQL mediante la Restauración a un momento dado en el portal de Azure" 
   description="Restauración a un momento dado, base de datos de Microsoft Azure SQL, restaurar base de datos, recuperar base de datos, portal de administración de Azure, portal de Azure" 
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

# Restaurar una base de datos de Azure SQL mediante la Restauración a un momento dado en el portal de Azure

> [AZURE.SELECTOR]
- [Restauración a un momento dado: PowerShell](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-powershell/)
- [Restauración a un momento dado: API de REST](http://azure.microsoft.com/documentation/articles/sql-database-point-in-time-restore-tutorial-rest/) 

## Información general

Este tutorial muestra cómo restaurar una base de datos SQL de Azure usando la restauración a un momento dado en el [portal de Azure](http://manage.windowsazure.com/). La base de datos SQL de Azure tiene copias de seguridad integradas para admitir sin intervención la restauración a un momento dado para los niveles de servicio Basic, Standard y Premium.

La restauración a un momento dado crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

* La restauración a un momento dado está habilitada para todos los niveles de servicio Basic, Standard y Premium.

* Los períodos de retención de la copia de seguridad son 7 días para Basic, 14 días para Standard y 35 días para Premium.
 
* Solo el administrador y el coadministrador de la suscripción pueden enviar solicitudes de restauración.

* El inicio de sesión de entidad de seguridad a nivel de servidor será el propietario de la base de datos restaurada.

* Los niveles de servicio Web y Business Edition no admiten la restauración a un momento dado.
 
	* Si tiene una base de datos Web o Business Edition, puede usar la copia de base de datos para obtener una copia transaccional de la base de datos y, a continuación, exportar la base de datos copiada a una cuenta de almacenamiento de Microsoft Azure. Para obtener más información, consulte [Inserción de Uso de la copia de base de datos (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) y[ Uso de los servicios de importación y exportación en la base de datos de Azure SQL](http://msdn.microsoft.com/library/azure/hh335292.aspx).

	* Las ediciones Web y Business Editions se retirarán en septiembre de 2015. Para más información, consulte [Preguntas más frecuentes sobre la retirada de las ediciones Web y Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Cómo: Restaurar una base de datos de Azure SQL mediante la Restauración a un momento dado en el portal de Azure

<iframe src="http://channel9.msdn.com/Blogs/Windows-Azure/Restore-a-SQL-Database-Using-Point-in-Time-Restore/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

1. Inicie sesión en el portal de Azure con su cuenta de Microsoft.

2. En el panel izquierdo, haga clic en **BASES DE DATOS SQL**.
  
3. En la lista **BASES DE DATOS**, haga clic en la base de datos que desea restaurar. 

4. En la parte inferior de la página, en la barra de comandos, haga clic en **RESTAURAR**. Aparecerá el cuadro de diálogo **Especificar la configuración de restauración**.

5. Elija un **NOMBRE DE BASE DE DATOS**. De forma predeterminada, ya hay un nombre de base de datos elegido, pero puede cambiarlo si lo desea.

6. Elija el momento al que se debe restaurar la base de datos. Este momento en el tiempo no debe superar el período de retención de la base de datos.
	
7. Haga clic en la marca de verificación para enviar la solicitud de restauración.

Una restauración puede tardar algún tiempo en completarse. Para supervisar el estado de la restauración, haga clic en el icono de estado en la parte inferior derecha de la página en la barra de comandos y, a continuación, haga clic en **DETALLES**.

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

[Restauración a un momento dado de base de datos SQL de Azure (blog)](http://azure.microsoft.com/blog/2014/10/01/azure-sql-database-point-in-time-restore/)
<!--HONumber=47-->
 