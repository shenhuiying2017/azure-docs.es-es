<properties 
   pageTitle="Restaurar una base de datos SQL de Azure eliminada en el portal de Azure" 
   description="Base de datos SQL de Microsoft Azure, restaurar base de datos eliminada, recuperar base de datos eliminada, Portal de administración de Azure, portal de Azure" 
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
   ms.date="07/15/2015"
   ms.author="elfish; v-romcal"/>

# Restaurar una base de datos SQL de Azure eliminada en el portal de Azure

> [AZURE.SELECTOR]
- [Restore deleted database - PowerShell](sql-database-restore-deleted-database-tutorial-powershell.md)
- [Restore deleted database - REST API](sql-database-restore-deleted-database-tutorial-rest.md)

## Información general

Este tutorial muestra cómo restaurar una base de datos SQL de Azure eliminada en el [portal de Azure](http://manage.windowsazure.com). Puede restaurar una base de datos que se eliminó durante su período de retención en el punto en que se eliminó. El período de retención determina el nivel de servicio de la base de datos.

Al restaurar una base de datos SQL de Azure eliminada, se crea una nueva base de datos. El servicio selecciona automáticamente el nivel de servicio en función de la copia de seguridad utilizada en el punto de restauración. Asegúrese de contar con cuota disponible en el servidor lógico para crear otra base de datos. Si desea solicitar una cuota mayor, póngase en contacto con el [servicio de soporte técnico de Azure](http://azure.microsoft.com/support/options/).

## Restricciones y seguridad

* La restauración de una base de datos eliminada SQL de Azure está habilitada para los todos los niveles de los servicios Basic, Standard y Premium. 

* Los períodos de retención de la copia de seguridad son 7 días para Basic, 14 días para Standard y 35 días para Premium.

* Solo el administrador y el coadministrador de la suscripción pueden enviar solicitudes de restauración.

* El inicio de sesión de entidad de seguridad a nivel de servidor será el propietario de la base de datos restaurada.
 
* Los niveles de servicio Web y Business Edition no admiten la restauración de una base de datos SQL eliminada.
 
	* Si tiene una base de datos Web o Business Edition, puede usar la copia de base de datos para obtener una copia transaccional de la base de datos y, a continuación, exportar la base de datos copiada a una cuenta de almacenamiento de Microsoft Azure. Para obtener más información, consulte [Procedimiento: Uso de la copia de base de datos (Base de datos SQL de Azure)](http://msdn.microsoft.com/library/azure/ff951631.aspx) y [Procedimiento: uso del servicio de importación y exportación en Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh335292.aspx).
	* Las ediciones Web y Business Editions se retirarán en septiembre de 2015. Para obtener más información, consulte [Preguntas más frecuentes sobre la retirada de las ediciones Web y Business](http://msdn.microsoft.com/library/azure/dn741330.aspx).

## Cómo: Restaurar una base de datos SQL de Azure eliminada en el portal de Azure

> [AZURE.VIDEO restore-a-deleted-sql-database]

1. Inicie sesión en el [portal de Azure](http://manage.windowsazure.com) con su cuenta de Microsoft.

2. En el panel izquierdo, haga clic en **BASES DE DATOS SQL**.

3. En la parte superior de la página, haga clic en **BASES DE DATOS ELIMINADAS**. Aparecerá una lista de **BASES DE DATOS ELIMINADAS RESTAURABLES**.

4. Haga clic en la base de datos que desee restaurar.

6. En la parte inferior de la página, en la barra de comandos, haga clic en **Restaurar**. Aparecerá el cuadro de diálogo **Especificar configuración de restauración**.

7. Elija un **NOMBRE DE BASE DE DATOS**. De forma predeterminada, ya hay un nombre de base de datos elegido, pero puede cambiarlo si lo desea.

	* Tenga en cuenta que una base de datos eliminada solo puede restaurarse al servidor desde el que se eliminó y al punto en el tiempo en el que se eliminó.   

8. Haga clic en la marca de verificación para enviar la solicitud de restauración.

Una restauración puede tardar algún tiempo en completarse. Para supervisar el estado de la restauración, haga clic en el icono de estado en la parte inferior derecha de la página en la barra de comandos y, a continuación, haga clic en **DETALLES**.

## Pasos siguientes

Para obtener más información, consulte los temas siguientes:

[Continuidad de negocio de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/hh852669.aspx)

[Copia de seguridad y restauración de Base de datos SQL de Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)

<!---HONumber=July15_HO3-->