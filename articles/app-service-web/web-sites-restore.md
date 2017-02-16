---
title: "Restaurar una aplicación en Azure"
description: "Obtenga información sobre cómo restaurar la aplicación desde una copia de seguridad."
services: app-service
documentationcenter: 
author: cephalin
manager: wpickett
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: af79132b5830b1782850c8738f1d8bd5ca0b38d9
ms.openlocfilehash: b60009a93bd2c857acfc4d1856d8f1afb7fff595


---
# <a name="restore-an-app-in-azure"></a>Restaurar una aplicación en Azure
En este artículo se muestra cómo restaurar una aplicación de [Azure App Service](../app-service/app-service-value-prop-what-is.md) de la que ha realizado previamente una copia de seguridad (consulte [Realizar una copia de seguridad de la aplicación en Azure](web-sites-backup.md)). Puede restaurar la aplicación con sus bases de datos vinculadas (Base de datos SQL o MySQL) a petición a un estado anterior o crear una nueva aplicación basada en una copia de seguridad de la aplicación original. La creación de una nueva aplicación que se ejecuta en paralelo con la versión más reciente puede resultar útil para la prueba A/B.

La restauración de las copias de seguridad está disponible para las aplicaciones que se ejecutan en el nivel **Estándar** y **Premium**. Para obtener más información sobre la escalación de su aplicación, vea [Escalación de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-scale.md). El nivel **premium** permite realizar un mayor número de copias de seguridad diarias que se van a realizar en el nivel **estándar**.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Restaurar una aplicación de una copia de seguridad existente
1. En la hoja **Configuración** de la aplicación en Azure Portal, haga clic en la opción **Copias de seguridad** para mostrar la hoja **Copias de seguridad**. A continuación, haga clic en **Restaurar ahora** en la barra de comandos.
   
    ![Elegir Restaurar ahora][ChooseRestoreNow]
2. En la hoja **Restaurar** , seleccione primero el origen de la copia de seguridad.
   
    ![](./media/web-sites-restore/021ChooseSource.png)
   
    La opción **Copia de seguridad de aplicación** le muestra todas las copias de seguridad existentes de la aplicación actual y puede seleccionar una fácilmente.
    La opción **Almacenamiento** le permite seleccionar cualquier archivo ZIP de copia de seguridad de cualquier cuenta de almacenamiento de Azure y contenedor existente de su suscripción.
    Si está intentando restaurar una copia de seguridad de otra aplicación, use la opción **Almacenamiento** .
3. A continuación, especifique el destino de la restauración de la aplicación en **Destino de restauración**.
   
    ![](./media/web-sites-restore/022ChooseDestination.png)
   
   > [!WARNING]
   > Si elige **Sobrescribir**, se borrarán todos los datos existentes de la aplicación actual. Antes de hacer clic en **Aceptar**, asegúrese de que es exactamente lo que desea.
   > 
   > 
   
    Puede seleccionar **Aplicación existente** para restaurar la copia de seguridad de la aplicación a otra aplicación en el mismo grupo de recursos. Antes de utilizar esta opción, debe haber creado primero otra aplicación en el grupo de recursos con una configuración de base de datos reflejada en la definida en la copia de seguridad de la aplicación.
4. Haga clic en **Aceptar**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Descarga o eliminación de una copia de seguridad de una cuenta de almacenamiento
1. En la hoja principal **Examinar** de Azure Portal, seleccione **Cuentas de almacenamiento**.
   
    Se mostrará una lista de las cuentas de almacenamiento existentes.
2. Seleccione la cuenta de almacenamiento que contiene la copia de seguridad que desea descargar o eliminar.
   
    Se mostrará la hoja de la cuenta de almacenamiento.
3. En la hoja de la cuenta de almacenamiento, seleccione el contenedor que quiere.
   
    ![Ver contenedores][ViewContainers]
4. Seleccione el archivo de copia de seguridad que quiere descargar o eliminar.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Haga clic en **Descargar** o **Eliminar**, dependiendo de lo que quiera hacer.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Supervisar una operación de restauración
1. Para detalles sobre si se ha realizado correctamente o no la operación de restauración de la aplicación, vaya a la hoja **Registro de actividades** de Azure Portal.
   
    La hoja **Registro de actividades** muestra todas las operaciones, junto con el nivel, estado, recursos y detalles de hora.
2. Desplácese hacia abajo para buscar la operación de restauración deseada y haga clic para seleccionarla.

La hoja de detalles mostrará la información disponible relacionada con la operación de restauración.

## <a name="next-steps"></a>Pasos siguientes
También puede crear una copia de seguridad y restaurar las aplicaciones del Servicio de aplicaciones mediante la API de REST (vea [Uso de REST para copia de seguridad y restauración de aplicaciones del Servicio de aplicaciones](websites-csm-backup.md)).

> [!NOTE]
> Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
> 
> 

<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
[StorageAccountFile]: ./media/web-sites-restore/02StorageAccountFile.png
[BrowseCloudStorage]: ./media/web-sites-restore/03BrowseCloudStorage.png
[StorageAccountFileSelected]: ./media/web-sites-restore/04StorageAccountFileSelected.png
[ChooseRestoreSettings]: ./media/web-sites-restore/05ChooseRestoreSettings.png
[ChooseDBServer]: ./media/web-sites-restore/06ChooseDBServer.png
[RestoreToNewSQLDB]: ./media/web-sites-restore/07RestoreToNewSQLDB.png
[NewSQLDBConfig]: ./media/web-sites-restore/08NewSQLDBConfig.png
[RestoredContosoWebSite]: ./media/web-sites-restore/09RestoredContosoWebSite.png
[DashboardOperationLogsLink]: ./media/web-sites-restore/10DashboardOperationLogsLink.png
[ManagementServicesOperationLogsList]: ./media/web-sites-restore/11ManagementServicesOperationLogsList.png
[DetailsButton]: ./media/web-sites-restore/12DetailsButton.png
[OperationDetails]: ./media/web-sites-restore/13OperationDetails.png



<!--HONumber=Nov16_HO5-->


