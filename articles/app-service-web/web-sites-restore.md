<properties 
	pageTitle="Restauración de una aplicación web en el Servicio de aplicaciones de Azure" 
	description="Aprenda a restaurar la aplicación web desde una copia de seguridad." 
	services="app-service\web" 
	documentationCenter="" 
	authors="cephalin" 
	writer="cephalin" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="cephalin"/>

# Restauración de una aplicación web en el Servicio de aplicaciones de Azure

En este artículo se muestra cómo restaurar una aplicación web de la que haya hecho previamente una copia de seguridad usando la característica de copias de seguridad de [Aplicaciones web del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714). Para obtener más información, consulte [Copias de seguridad de Aplicaciones web del Servicio de aplicaciones](web-sites-backup.md).

La característica de restauración de aplicaciones web le permite restaurar, siempre que lo desee, la aplicación web a un estado anterior, o bien crear una nueva aplicación web en función de una de las copias de seguridad de la aplicación web original. La creación de una nueva aplicación web que se ejecuta en paralelo con la versión más reciente puede resultar útil para la prueba A/B.

La característica de restauración de aplicaciones web, disponible en la hoja **Copias de seguridad** del [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), solo está disponible en los modos Estándar y Premium. Para obtener información acerca de cómo escalar el uso de la aplicación al modo Estándar o Premium, consulte [Escalado de una aplicación web en el Servicio de aplicaciones de Azure](web-sites-scale.md). Tenga en cuenta que el modo Premium permite realizar un mayor número de copias de seguridad diarias que el modo Estándar.

<a name="PreviousBackup"></a>
## Para restaurar una aplicación web desde una copia de seguridad realizada anteriormente

1. En la hoja **Configuración** de la aplicación web del Portal de Azure, haga clic en la opción **Copias de seguridad** para mostrar la hoja **Copias de seguridad**. Desplácese a esta hoja y elija uno de los elementos de copia de seguridad en función de la **hora de la copia de seguridad** y el **estado** en la lista de copias de seguridad.
	
	![Elegir origen de copia de seguridad][ChooseBackupSource]
	
2. Elija **Restaurar ahora** en la parte superior de la hoja **Copias de seguridad**.

	![Elegir Restaurar ahora][ChooseRestoreNow]

3. En la hoja **Restaurar**, para restaurar la aplicación web existente, compruebe todos los detalles mostrados y, a continuación, haga clic en **Aceptar**.
	
También puede restaurar la aplicación web en una nueva aplicación web seleccionando la parte **Aplicación web** de la hoja **Restaurar** y la parte **Crear una nueva aplicación web**.
	
<a name="StorageAccount"></a>
## Descarga o eliminación de una copia de seguridad de una cuenta de almacenamiento
	
1. En la hoja principal **Examinar** del Portal de Azure, seleccione **Cuentas de almacenamiento**.
	
	Se mostrará una lista de las cuentas de almacenamiento existentes.
	
2. Seleccione la cuenta de almacenamiento que contiene la copia de seguridad que desea descargar o eliminar.
	
	Aparecerá la hoja **Almacenamiento**.

3. Seleccione la parte **Contenedores** de la hoja **Almacenamiento** para mostrar la hoja **Contenedores**.
	
	Aparecerá una lista de contenedores. Esta lista también mostrará la dirección URL y la fecha de la última modificación de este contenedor.
	
	![Ver contenedores][ViewContainers]

4. En la lista, seleccione el contenedor y muestre la hoja en la que aparece una lista de nombres de archivo, junto con el tamaño de cada archivo.
	
5. Al seleccionar un archivo, puede elegir **Descargar** o **Eliminar el archivo**. Tenga en cuenta que hay dos tipos de archivos principales: los archivos .zip y .xml.

<a name="OperationLogs"></a>
## Visualización de los registros de auditoría
	
1. Para ver detalles sobre si se ha realizado correctamente o no la operación de restauración de la aplicación web, seleccione la parte **Registro de auditoría** de la hoja **Examinar principal**. 
	
	La hoja **Registro de auditoría** muestra todas las operaciones, junto con el nivel, estado, recursos y detalles de hora.
	
2. Desplácese por la hoja para buscar las operaciones relacionadas con la aplicación web.
3. Para ver detalles adicionales sobre una operación, seleccione la operación en la lista.
	
La hoja de detalles mostrará la información disponible relacionada con la operación.
	
>[AZURE.NOTE]Si desea empezar a trabajar con el Servicio de aplicaciones de Azure antes de inscribirse para abrir una cuenta de Azure, vaya a [Prueba del Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde podrá crear inmediatamente una aplicación web de inicio de corta duración en el Servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.
	
## Lo que ha cambiado
* Para obtener una guía del cambio de Sitios web a Servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener una guía del cambio del portal anterior al nuevo, consulte: [Referencia para navegar en el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- IMAGES -->
[ChooseBackupSource]: ./media/web-sites-restore/01ChooseBackupSource.png
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
 

<!---HONumber=62-->