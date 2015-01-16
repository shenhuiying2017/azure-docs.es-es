<properties urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Uso de trabajos web para ejecutar tareas en segundo plano en Sitios web Microsoft Azure"metaKeywords ="sitios web Microsoft Azure, trabajos Web, tareas en segundo plano" description="Obtenga información acerca de cómo ejecutar tareas en segundo plano en sitios web de Microsoft Azure." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/12/2014" ms.author="cephalin" />

#Uso de trabajos web para ejecutar tareas en segundo plano en Sitios web Microsoft Azure

Sitios web Azure le permite ejecutar programas o scripts en el sitio web de una de las tres formas siguientes: a petición, continuamente o en función de una programación. No hay ningún coste adicional por usar trabajos web de Microsoft Azure.

En este artículo se muestra cómo implementar trabajos web mediante el Portal de administración de Azure. Para obtener información sobre cómo implementar mediante Visual Studio o un proceso de entrega continua, consulte [Implementación de trabajos web de Azure en Sitios web Azure](http://azure.microsoft.com/es-es/documentation/articles/websites-dotnet-deploy-webjobs).

El SDK de trabajos web de Azure simplifica muchas de las tareas de programación de trabajos web. Para obtener más información, consulte [¿Qué es el SDK de trabajos web?](../websites-dotnet-webjobs-sdk).

## Tabla de contenido ##
- [Tipos de archivos aceptables para scripts](#acceptablefiles)
- [Creación de una tarea a petición](#CreateOnDemand)
- [Creación de una tarea de ejecución continua](#CreateContinuous)
- [Creación de una tarea programada](#CreateScheduled)
	- [Trabajos programados y Programador de Azure](#Scheduler)
- [Visualización del historial de trabajos](#ViewJobHistory)
- [Notas](#WHPNotes)
- [Pasos siguientes](#NextSteps)

## <a name="acceptablefiles"></a>Tipos de archivo aceptables para scripts o programas

Se aceptan los siguientes tipos de archivo:

* .cmd, .bat, .exe (con windows cmd)
* .ps1 (con powershell)
* .sh (con bash)
* .php (con php)
* .py (con python)
* .js (con node)

## <a name="CreateOnDemand"></a>Creación de una tarea a petición

1. En la barra de comandos de la página **Trabajos web**, haga clic en **Agregar**. Se muestra el cuadro de diálogo **Nuevo trabajo**.
	
	![On Demand Task][OnDemandWebJob]
	
2. En **Nombre**, proporcione un nombre para la tarea. El nombre debe comenzar con una letra o un número y no puede contener ningún carácter especial salvo "-" y "_".
	
3. En el cuadro **Contenido (archivos zip - 100 MB como máximo)**, busque el archivo zip que contiene el script. El archivo zip debe contener el ejecutable (.exe, .cmd, .bat, .sh, .php, .py, .js) así como cualquier archivo complementario necesario para ejecutar el programa o script.
	
4. En el cuadro **Cómo ejecutar**, elija **Ejecutar a petición**.
	
5. Active la casilla de la parte inferior derecha del cuadro de diálogo para cargar el script en el sitio web. El nombre especificado para la tarea aparece en la lista:
	
	![Task List][WebJobsList]
	
6. Para ejecutar el script, seleccione su nombre en la lista y haga clic en **Ejecutar una vez** en la barra de comandos en la parte inferior de la página del portal.
	
	![Run Once][RunOnce]

## <a name="CreateContinuous"></a>Creación de una tarea de ejecución continua

1. Para crear una tarea de ejecución continua, siga los pasos descritos para crear una tarea que se ejecuta solo una vez, pero en el cuadro **Cómo ejecutar**, elija **Ejecutar continuamente**.
	
	![New Continuous Task][NewContinuousJob]
	
2. Para iniciar o detener una tarea que se ejecuta continuamente, seleccione la tarea en la lista y haga clic en **Iniciar** o en **Detener** en la barra de comandos.

> [WACOM.NOTE] Si el sitio web se ejecuta en más de una instancia, la tarea que se ejecuta continuamente se ejecutará en todas las instancias. Las tareas a petición o programadas se ejecutan en una única instancia seleccionada por Microsoft Azure para el equilibrio de carga.

> [WACOM.NOTE]
> En el caso de las tareas continuas, se recomienda habilitar **Siempre visible** en la página de configuración del sitio web. La característica Always On, disponible en modo básico y estándar, impide que se descarguen los sitios web, aunque hayan estado inactivos durante algún tiempo. Si el sitio web siempre está cargado, la tarea que se ejecuta continuamente puede ejecutarse de forma más confiable. 

## <a name="CreateScheduled"></a>Creación de una tarea programada

1. Para crear una tarea programada, siga los mismos pasos indicados más arriba pero, en el cuadro **Cómo ejecutar**, elija **Ejecutar de acuerdo con una programación**.
	
	![New Scheduled Job][NewScheduledJob]
	
2. Elija una región en la opción **Región del programador** para el trabajo y, a continuación, haga clic en la flecha de la parte inferior derecha del cuadro de diálogo para continuar a la pantalla siguiente.

3. En el cuadro de diálogo **Crear trabajo**, elija el tipo de periodicidad que desea en la opción **Repetición**: **Trabajo único** o **Trabajo recurrente**.
	
	![Schedule Recurrence][SchdRecurrence]
	
4. También, puede elegir una hora de **inicio**: **Ahora** o **A una hora específica**.
	
	![Schedule Start Time][SchdStart]
	
5. Si desea comenzar a una hora determinada, elija los valores de la hora de inicio en **Comienza el**.
	
	![Schedule Start at a Specific Time][SchdStartOn]
	
6. Si elige un trabajo periódico, en la opción **Repetir cada** podrá especificar la frecuencia de la periodicidad y en la opción **Termina el** podrá especificar una hora de finalización.
	
	![Schedule Recurrence][SchdRecurEvery]
	
7. Si elige **Semanas**, puede seleccionar el cuadro **En una determinada programación** y especificar los días de la semana en los que desea que se ejecute el trabajo.
	
	![Schedule Days of the Week][SchdWeeksOnParticular]
	
8. Si elige **Meses** y selecciona **En una determinada programación**, podrá establecer que el trabajo se ejecute en un número de días determinado del mes seleccionándolos en la opción **Días** del mes. 
	
	![Schedule Particular Dates in the Month][SchdMonthsOnPartDays]
	
9. Si elige **Días de la semana**, podrá seleccionar el día o días de la semana del mes en los que desea que se ejecute el trabajo.
	
	![Schedule Particular Week Days in a Month][SchdMonthsOnPartWeekDays]
	
10. Finalmente, también puede utilizar la opción **Repeticiones** para elegir en qué semana del mes (primera, segunda, tercera, etc.) desea que se ejecute el trabajo en los días de la semana especificados.
	
	![Schedule Particular Week Days on Particular Weeks in a Month][SchdMonthsOnPartWeekDaysOccurences]
	
11. Después de haber creado uno o varios trabajos, sus nombres aparecerán en la pestaña WebJobs con su estado, tipo de programación y otra información. Se conserva la información histórica de las últimas 30 tareas.
	
	![Jobs list][WebJobsListWithSeveralJobs]
	
### <a name="Scheduler"></a>Trabajos programados y Programador de Azure

Los trabajos programados se pueden configurar posteriormente en el portal del Programador de Azure.

1.	En la página Trabajos web, haga clic en el vínculo **schedule** para desplazarse a la página del portal del Programador de Azure. 
	
	![Link to Azure Scheduler][LinkToScheduler]
	
2. En la página del Programador, haga clic en el trabajo.
	
	![Job on the Scheduler portal page][SchedulerPortal]
	
3. Se abre la página **Acción del trabajo**, donde puede configurar adicionalmente el trabajo. 
	
	![Job Action PageInScheduler][JobActionPageInScheduler]
	
## <a name="ViewJobHistory"></a>Visualización del historial de trabajos

1. Para ver el historial de ejecución de un trabajo, incluidos los trabajos creados con el SDK de trabajos web, haga clic en su vínculo correspondiente en la columna **Registros**. (Puede utilizar el icono del Portapapeles para copiar la dirección URL de la página de archivo de registro en el Portapapeles si lo desea).
	
	![Logs Link][WebJobLogs]
		
2. Si hace clic en el vínculo, se abre la página de detalles de los trabajos web para la tarea. En esta página se muestra el nombre de la ejecución del comando, las últimas veces que se ha ejecutado y si se ha ejecutado correctamente o no. En **Recent job runs** (Ejecuciones de trabajos recientes), haga clic en una hora para ver detalles adicionales.
	
	![WebJobDetails][WebJobDetails]
	
3. Se muestra la página **WebJob Run Details** (Detalles de ejecuciones de trabajos web). Haga clic en **Toggle Output** para ver el texto del contenido del registro. El registro de resultados se muestra en formato de texto. 
	
	![Web job run details][WebJobRunDetails]
	
4. Para ver el texto de salida en otra ventana del explorador, haga clic en el vínculo **download**. Para descargar el texto propiamente dicho, haga clic con el botón secundario en el vínculo y utilice las opciones del explorador para guardar el contenido del archivo.
	
	![Download log output][DownloadLogOutput]
	
5. El vínculo **WebJobs** situado en la parte superior de la página ofrece una forma cómoda de obtener una lista de los trabajos web en el panel del historial.
	
	![Link to web jobs list][WebJobsLinkToDashboardList]
	
	![List of jobs in history dashboard][WebJobsListInJobsDashboard]
	
	Al hacer clic en uno de estos vínculos, obtendrá acceso a la página de detalles de WebJob para el trabajo que ha seleccionado.


## <a name="WHPNotes"></a>Notas
	
- Desde marzo de 2014, los sitios web en modo libre pueden agotar su tiempo de espera tras 20 minutos si no se realizan solicitudes al sitio de scm (implementación) y el portal del sitio web no está abierto en Azure. Las solicitudes al sitio real no restablecerán esto.
- Debe escribirse el código para un trabajo continuo para que se ejecute en un bucle infinito.
- Los trabajos continuos solo se ejecutan continuamente cuando el sitio está activo.
- Los modos básico y estándar ofrecen la característica Always On que, cuando está habilitada, impide que los sitios se vuelvan inactivos.

## <a name="NextSteps"></a>Pasos siguientes
 
Para obtener más información, consulte [Recursos recomendados de trabajos web de Azure][WebJobsRecommendedResources].

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
