<properties 
	pageTitle="Ejecutar tareas en segundo plano con trabajos web" 
	description="Obtenga información sobre cómo ejecutar tareas en segundo plano en aplicaciones web de Azure." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	writer="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Ejecutar tareas en segundo plano con trabajos web

## Información general

Puede ejecutar programas o scripts en trabajos web en su aplicación web del [servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) de tres maneras: a petición, de forma continua o según una programación. No hay ningún coste adicional en el uso de trabajos web.

En este artículo se muestra cómo implementar trabajos web con el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715). Para obtener información sobre cómo implementar con Visual Studio o con un proceso de entrega continua, consulte [Cómo implementar trabajos web de Azure en aplicaciones web](websites-dotnet-deploy-webjobs.md).

El SDK de trabajos web de Azure simplifica muchas de las tareas de programación de trabajos web. Para obtener más información, consulte [¿Qué es el SDK de Trabajos web?](websites-dotnet-webjobs-sdk.md).

## <a name="acceptablefiles"></a>Tipos de archivo válidos para scripts o programas

Se aceptan los siguientes tipos de archivo:

* .cmd, .bat, .exe (con windows cmd)
* .ps1 (con powershell)
* .sh (con bash)
* .php (con php)
* .py (con python)
* .js (con node)

## <a name="CreateOnDemand"></a>Creación de un trabajo web a petición en el portal

1. En la hoja **Aplicación web** del [Portal de Azure](http://portal.azure.com), haga clic en **Toda la configuración > Trabajos web** para mostrar la hoja **Trabajos web**.
	
	![WebJob blade](./media/web-sites-create-web-jobs/wjblade.png)
	
5. Haga clic en **Add**. Aparecerá el cuadro de diálogo **Agregar trabajo web**.
	
	![Add WebJob blade](./media/web-sites-create-web-jobs/addwjblade.png)
	
2. En **Nombre**, escriba un nombre para el trabajo web. El nombre debe comenzar con una letra o un número y no puede contener ningún carácter especial salvo "-" y "_".
	
4. En el cuadro **Cómo ejecutar**, elija **Ejecutar a petición**.
	
3. En el cuadro **Carga de archivos**, haga clic en el icono de la carpeta y busque el archivo zip que contiene el script. El archivo zip debe contener el ejecutable (.exe, .cmd, .bat, .sh, .php, .py, .js) así como cualquier archivo complementario necesario para ejecutar el programa o script.
	
5. Active **Crear** para cargar el script de su aplicación web. 
	
	El nombre especificado para el trabajo web aparece en la lista de la hoja **Trabajos web**.
	
6. Para ejecutar el trabajo web, haga clic en su nombre en la lista y haga clic en **Ejecutar**.
	
	![Run WebJob](./media/web-sites-create-web-jobs/runondemand.png)
	
## <a name="CreateContinuous"></a>Creación de un trabajo web de ejecución continua

1. Para crear un trabajo web de ejecución continua, siga los pasos descritos para crear un trabajo web que se ejecuta solo una vez, pero en el cuadro **Cómo ejecutar**, elija **Continua**.

2. Para iniciar o detener un trabajo web continuo, haga clic en el trabajo web en la lista y haga clic en **Iniciar** o **Detener**.
	
> [AZURE.NOTE] Si la aplicación web se ejecuta en más de una instancia, se ejecutará un trabajo web continuamente en todas las instancias. Los Trabajos web a petición o programados se ejecutan en una única instancia seleccionada por Microsoft Azure para el equilibrio de carga.
	
> [AZURE.NOTE] En trabajos web continuos, se recomienda que habilite **Siempre disponible** en su aplicación web. La característica Siempre disponible, disponible en modo básico y estándar, impide que se descarguen las aplicaciones web, aunque hayan estado inactivos durante algún tiempo. Si la aplicación web siempre está cargada, el trabajo web que se ejecuta continuamente puede ejecutarse de forma más confiable. 

## <a name="CreateScheduled"></a>Creación de un trabajo web programado

El portal de administración de Azure aún no tiene la capacidad para crear un trabajo web programado, pero hasta que se agregue esa característica, puede hacerlo con el [portal antiguo](http://manage.windowsazure.com).

1. En el [portal antiguo](http://manage.windowsazure.com), vaya a la página del trabajo web y haga clic en **Agregar**.

1. En el cuadro **Cómo ejecutar**, seleccione **Ejecutar según una programación**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Elija una región en la opción **Región del programador** para el trabajo y luego haga clic en la flecha de la parte inferior derecha del cuadro de diálogo para continuar a la pantalla siguiente.

3. En el cuadro de diálogo **Crear trabajo**, elija el tipo de **periodicidad** que quiere: **Trabajo único** o **Trabajo periódico**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. También, puede elegir una hora de **inicio**: **Ahora** o **A una hora específica**.
	
	![Schedule Start Time][SchdStart]
	
5. Si desea comenzar a una hora determinada, elija los valores de la hora de inicio en **Iniciar a las**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Si elige un trabajo periódico, en la opción **Repetir cada** podrá especificar la frecuencia de la periodicidad y en la opción **Finalizar a las** podrá especificar una hora de finalización.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. Si elige **Semanas**, puede seleccionar la casilla **Según un programa determinado** y especificar los días de la semana en los que quiere que se ejecute el trabajo.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. Si elige **Meses** y selecciona la casilla **Según un programa determinado**, podrá establecer que el trabajo se ejecute en un número de días determinado del mes seleccionándolos en la opción **Días**. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. Si elige **Días de la semana**, podrá seleccionar el día o días de la semana del mes en los que desea que se ejecute el trabajo.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Finalmente, también puede utilizar la opción **Repeticiones** para elegir en qué semana del mes (primera, segunda, tercera, etc.) desea que se ejecute el trabajo en los días de la semana especificados.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Después de haber creado uno o varios trabajos, sus nombres aparecerán en la pestaña WebJobs con su estado, tipo de programación y otra información. Se conserva la información histórica de los últimos 30 trabajos web.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Trabajos programados y Programador de Azure

Los trabajos programados se puede configurar adicionalmente en las páginas del Programador de Azure de la [portal antiguo](http://manage.windowsazure.com).

1.	En la página Trabajos web, haga clic en el vínculo **Programar** para desplazarse a la página del portal del Programador de Azure. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. En la página del Programador, haga clic en el trabajo.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. Se abre la página **Acción del trabajo**, donde puede configurar adicionalmente el trabajo. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Ver el historial de trabajos

1. Para ver el historial de ejecución de un trabajo, incluidos los trabajos creados con el SDK de Trabajos web, haga clic en su vínculo correspondiente en la columna **Registros** de la hoja Trabajos web. (Puede utilizar el icono del Portapapeles para copiar la dirección URL de la página de archivo de registro en el Portapapeles si lo desea).
	
	![vínculo a Registros](./media/web-sites-create-web-jobs/wjbladelogslink.png)
		
2. Al hacer clic en el vínculo, se abre la página de detalles del trabajo web. En esta página se muestra el nombre de la ejecución del comando, las últimas veces que se ha ejecutado y si se ha ejecutado correctamente o no. En **Ejecuciones del trabajo recientes**, haga clic una vez para ver más detalles.
	
	![WebJobDetails][WebJobDetails]
	
3. Se muestra la página **Detalles de ejecución del trabajo web**. Haga clic en **Alternar salida** para ver el texto del contenido del registro. El registro de resultados se muestra en formato de texto. 
	
	![Web job run details][WebJobRunDetails]
	
4. Para ver el texto de salida en otra ventana del explorador, haga clic en el vínculo **descargar**. Para descargar el texto propiamente dicho, haga clic con el botón secundario en el vínculo y utilice las opciones del explorador para guardar el contenido del archivo.
	
	![Download log output][DownloadLogOutput]
	
5. El vínculo **Trabajos web** situado en la parte superior de la página ofrece una forma cómoda de obtener una lista de los trabajos web en el panel del historial.
	
	![Link to WebJobs list][WebJobsLinkToDashboardList]
	
	![List of WebJobs in history dashboard][WebJobsListInJobsDashboard]
	
	Al hacer clic en uno de estos vínculos, obtendrá acceso a la página de detalles de WebJob para el trabajo que ha seleccionado.


## <a name="WHPNotes"></a>Notas
	
- Desde marzo de 2014, las aplicaciones web en modo libre pueden agotar su tiempo de espera tras 20 minutos si no se realizan solicitudes al sitio de scm (implementación) y el portal de la aplicación web no está abierto en Azure. Las solicitudes al sitio real no restablecerán esto.
- Debe escribirse el código para un trabajo continuo para que se ejecute en un bucle infinito.
- Los trabajos continuos solo se ejecutan continuamente cuando la aplicación web está activa.
- Los modos básico y estándar ofrecen la característica Siempre disponible que, cuando está habilitada, impide que las aplicaciones web se vuelvan inactivas.

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de inicio de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a name="NextSteps"></a>Pasos siguientes
 
Para obtener más información, consulte [Recursos recomendados de trabajos web de Azure][WebJobsRecommendedResources].

## Lo que ha cambiado
* Para obtener instrucciones sobre el cambio de sitios web al servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener instrucciones sobre el cambio del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx
[WebJobsRecommendedResources]:http://go.microsoft.com/fwlink/?LinkId=390226

[OnDemandWebJob]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
[WebJobsList]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
[NewContinuousJob]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
[NewScheduledJob]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
[SchdRecurrence]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
[SchdStart]: ./media/web-sites-create-web-jobs/06SchdStart.png
[SchdStartOn]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
[SchdRecurEvery]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
[SchdWeeksOnParticular]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
[SchdMonthsOnPartDays]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
[SchdMonthsOnPartWeekDays]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
[SchdMonthsOnPartWeekDaysOccurences]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
[RunOnce]: ./media/web-sites-create-web-jobs/13RunOnce.png
[WebJobsListWithSeveralJobs]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
[WebJobLogs]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
[WebJobDetails]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
[WebJobRunDetails]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
[DownloadLogOutput]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
[WebJobsLinkToDashboardList]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
[WebJobsListInJobsDashboard]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
[LinkToScheduler]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
[SchedulerPortal]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
[JobActionPageInScheduler]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png

<!--HONumber=49-->