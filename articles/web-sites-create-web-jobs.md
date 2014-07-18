<properties  linkid="web-sites-create-web-jobs" urlDisplayName="How to create web jobs in Microsoft Azure" pageTitle="How to Create Web Jobs in Microsoft Azure Web Sites" metaKeywords="Microsoft Azure Web Sites, Web Jobs" description="Learn how to create web jobs in Microsoft Azure Web Sites." metaCanonical="" services="web-sites" documentationCenter="" title="How to Create Web Jobs in Microsoft Azure Web Sites" authors="timamm" solutions="" writer="timamm" manager="paulettm" editor="mollybos" />

# Utilización de la característica WebJobs en Sitios web Azure

Sitios web Azure le permite ejecutar programas o scripts en el sitio web de una de las tres formas siguientes: a petición, continuamente o en función de una programación. La utilización de WebJobs de Azure no implica ningún coste adicional, a no ser que desee habilitar la característica Always On descrita más adelante en este artículo.

## Tabla de contenido

* [Tipos de archivos aceptables para scripts](#acceptablefiles)
* [Creación de una tarea a petición](#CreateOnDemand)
* [Creación de una tarea de ejecución continua](#CreateContinuous)
* [Creación de una tarea programada](#CreateScheduled)
  * [Trabajos programados y Programador de Azure](#Scheduler)

* [Visualización del historial de trabajos](#ViewJobHistory)
* [Notas](#WHPNotes)
* [Pasos siguientes](#NextSteps)
  * [Hacer más con el SDK de WebJobs de Microsoft Azure](#WebJobsSDK)
  * [Métodos alternativos de implementación](#AlternateDeployments)
  * [Recursos adicionales](#AdditionalResources)

<a name="acceptablefiles"></a>

## Tipos de archivos aceptables para scripts

Los siguientes tipos de archivos se aceptan como scripts ejecutables:

.cmd, .bat, .exe (con windows cmd)

.ps1 (con powershell)

.sh (con bash)

.php (con php)

.py (con python)

.js (con node)

<a name="CreateOnDemand"></a> 

## Creación de una tarea a petición

1.  En la barra de comandos de la página **WebJobs**, haga clic en **Add**. Se muestra el cuadro de diálogo **New Job**.
    
	![Tarea a petición](./media/web-sites-create-web-jobs/01aOnDemandWebJob.png)

2.  En **Name**, escriba un nombre para la tarea. El nombre debe comenzar con una letra o un número y no puede contener ningún carácter especial salvo "-" y "\_".

3.  En el cuadro **Content (Zip Files - 100MB Max)**, busque el archivo zip que contiene el script. El archivo zip debe contener el ejecutable (.exe, .cmd, .bat, .sh, .php, .py, .js) así como cualquier archivo complementario necesario para ejecutar el programa o script.

4.  En el cuadro **How to Run**, elija **Run on Demand**.

5.  Active la casilla de la parte inferior derecha del cuadro de diálogo para cargar el script en el sitio web. El nombre especificado para la tarea aparece en la lista:
    
    ![Lista de tareas](./media/web-sites-create-web-jobs/02aWebJobsList.png)

6.  Para ejecutar el script, seleccione su nombre en la lista y haga clic en **Run Once** en la barra de comandos, situada en la parte inferior de la página del portal.
    
    ![Ejecutar una vez](./media/web-sites-create-web-jobs/13RunOnce.png)

<a name="CreateContinuous"></a>

## Creación de una tarea de ejecución continua

1.  Para crear una tarea de ejecución continua, siga los pasos descritos para crear una tarea que se ejecuta solo una vez, pero en el cuadro  **How to Run**, elija **Run continuously**.
    
    ![Nueva tarea
    continua](./media/web-sites-create-web-jobs/03aNewContinuousJob.png)

2.  Para iniciar o detener una tarea que se ejecuta continuamente, seleccione la tarea en la lista y haga clic en **Iniciar** o **Detener** en la barra de comandos.

> [WACOM.NOTE] Si el sitio web se ejecuta en más de una instancia, la
> tarea que se ejecuta continuamente se ejecutará en todas las
> instancias. Las tareas a petición o programadas se ejecutan en una
> única instancia seleccionada por Microsoft Azure para el equilibrio de
> carga.

> [WACOM.NOTE] En el caso de las tareas continuas, se recomienda
> habilitar **Always On** en la página de configuración de su sitio web.
> La característica Always On, disponible en modo básico y estándar,
> impide que se descarguen los sitios web, aunque hayan estado inactivos
> durante algún tiempo. Si el sitio web siempre está cargado, la tarea
> que se ejecuta continuamente puede ejecutarse de forma más confiable.

<a name="CreateScheduled"></a>

## Creación de una tarea programada

1. Para crear una tarea programada, siga los mismospasos indicados más arriba pero, en el cuadro **How to Run**, elija **Run on a schedule**.

	![Nueva tarea programada][NewScheduledJob]

1.  Elija una región en la opción **Scheduler Region** para el trabajo y, a continuación, haga clic en la flecha de la parte inferior derecha del cuadro de diálogo para continuar a la pantalla siguiente.

2.  En el cuadro de diálogo **Create Job**, elija el tipo de periodicidad que desea en la opción **Recurrence**: **One-time job** o **Recurring job**.
    
    ![Periodicidad de la
    programación](./media/web-sites-create-web-jobs/05SchdRecurrence.png)

3.  También, puede elegir una hora de inicio en **Starting**: **Now** o **At a specific time**.
    
    ![Hora de inicio de la
    programación](./media/web-sites-create-web-jobs/06SchdStart.png)

4.  Si desea comenzar a una hora determinada, elija los valores de la hora de inicio en **Starting On**.
    
    ![Inicio de la programación a una hora
    determinada](./media/web-sites-create-web-jobs/07SchdStartOn.png)

5.  Si elige un trabajo periódico, en la opción **Recur Every** podrá especificar la frecuencia de la periodicidad y en la opción **Ending On** podrá especificar una hora de finalización.
    
    ![Periodicidad de la
    programación](./media/web-sites-create-web-jobs/08SchdRecurEvery.png)

6.  Si elige **Weeks**, puede seleccionar la casilla **On a Particular Schedule** y especificar los días de la semana en los que desea que se ejecute el trabajo.
    
    ![Días de la semana de la
    programación](./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png)

7.  Si elige **Months** y selecciona la casilla **On a Particular Schedule**, podrá establecer que el trabajo se ejecute en un número de días determinado del mes seleccionándolos en la opción **Days**.
    
    ![Fechas determinadas del mes de la
    programación](./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png)

8.  Si elige **Week Days**, podrá seleccionar el día o días de la semana del mes en los que desea que se ejecute el trabajo.
    
    ![Programación en días determinados de la semana de un
    mes](./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png)

9.  Finalmente, también puede utilizar la opción **Occurrences** para elegir en qué semana del mes (primera, segunda, tercera, etc.) desea que se ejecute el trabajo en los días de la semana especificados.
    
    ![Programación en días de la semana específicos
    de semanas
    determinadas](./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png)

10. Después de haber creado uno o varios trabajos, sus nombres aparecerán en la pestaña WebJobs con su estado, tipo de programación y otra información. Se conserva la información histórica de las últimas 30 tareas.
    
    ![Lista de
    trabajos](./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png)

<a name="Scheduler"></a>

### Trabajos programados y Programador de Azure. 

Los trabajos programados se pueden configurar posteriormente en el portal del Programador de Azure.

1.  En la página WebJobs, haga clic en el vínculo **schedule** para desplazarse a la página del portal del Programador de Azure.
    
    ![Vínculo al Programador de
    Azure](./media/web-sites-create-web-jobs/31LinkToScheduler.png)

2.  En la página del Programador, haga clic en el trabajo.
    
    ![Trabajo en la página del portal del
    Programador](./media/web-sites-create-web-jobs/32SchedulerPortal.png)

3.  Se abre la página **Job Action**, donde puede configurar adicionalmente el trabajo.
    
    ![Página de acción del trabajo en el
    Programador](./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png)

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## Visualización del historial de trabajos
 
1. Para ver el historial de ejecución de un trabajo, incluidos los trabajos creados con el SDK de WebJobs, haga clic en su vínculo correspondiente en la columna **Logs**. (Puede utilizar el icono del Portapapeles para copiar la dirección URL de la página de archivo de registro en el Portapapeles si lo desea).

    ![Vínculo a Logs][WebJobLogs]

1.  Si hace clic en el vínculo, se abre la página de detalles de los trabajos web para la tarea. En esta página se muestra el nombre de la ejecución del comando, las últimas veces que se ha ejecutado y si se ha ejecutado correctamente o no. En **Recent job runs**, haga clic una vez para ver detalles adicionales.
    
    ![Detalles del trabajo
    web](./media/web-sites-create-web-jobs/15WebJobDetails.png)

2.  Se muestra la página **WebJob Run Details**. Haga clic en **Toggle Output** para ver el texto del contenido del registro. El registro de resultados se muestra en formato de texto.
    
    ![Detalles de ejecución del trabajo
    web](./media/web-sites-create-web-jobs/16WebJobRunDetails.png)

3.  Para ver el texto de salida en otra ventana del explorador, haga clic en el vínculo **download**. Para descargar el texto propiamente dicho, haga clic con el botón secundario en el vínculo y utilice las opciones del explorador para guardar el contenido del archivo.
    
    ![Resultado del registro de
    descarga](./media/web-sites-create-web-jobs/17DownloadLogOutput.png)

4.  El vínculo **WebJobs** situado en la parte superior de la página ofrece una forma cómoda de obtener una lista de los trabajos web en el panel del historial.
    
    ![Vínculo a la lista de trabajos
    web](./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png)
    
    ![Lista de trabajos en el panel del
    historial](./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png)
    
    Al hacer clic en uno de estos vínculos, obtendrá acceso a la página
    de detalles de WebJob para el trabajo que ha seleccionado.

<a name="WHPNotes"></a>

## Notas

* Desde marzo de 2014, los sitios web en modo libre pueden agotar su tiempo de espera tras 20 minutos si no se realizan solicitudes al sitio de scm (implementación) y el portal del sitio web no está abierto en Azure. Las solicitudes al sitio real no restablecerán esto.

* Debe escribirse el código para un trabajo continuo para que se ejecute en un bucle infinito.

* Los trabajos continuos solo se ejecutan continuamente cuando el sitio está activo.

* Los modos básico y estándar ofrecen la característica Always On que, cuando está habilitada, impide que los sitios se vuelvan inactivos.

<a name="NextSteps"></a>

## Pasos siguientes

<a name="WebJobsSDK"></a>

### Hacer más con el SDK de WebJobs de Microsoft Azure

El SDK de Microsoft Azure WebJobs simplifica la tarea de agregar procesamiento en segundo plano a sus sitios web de Azure. El SDK integra el almacenamiento de Microsoft Azure, desencadenando una función en el programa cuando se agregan los elementos a Colas, Blobs o Tablas. El panel, ahora integrado en el portal de Azure, ofrece una supervisión y un diagnóstico completos para los programas que escriba usando el SDK. Las características de supervisión y diagnóstico están incluidas en el SDK y no requieren que agregue ningún código especial en el programa.

Para obtener más información, consulte el tutorial [Getting Started with Microsoft Azure WebJobs SDK][1]. El tutorial ofrece una introducción general a las características del SDK de WebJobs y le lleva por los pasos para crear y ejecutar un proceso simple en segundo plano de Hello World.

Para ver un tutorial de una aplicación de línea de comandos de ejemplo con el SDK de WebJobs de Microsoft Azure, consulte [Introducing Windows Azure WebJobs][2].

<a name="AlternateDeployments"></a>

### Métodos alternativos de implementación

Si no desea utilizar la página del portal de WebJobs para cargar sus scripts, puede utilizar FTP, .git o Web Deploy. Para obtener más información, consulte [How to deploy Azure WebJobs][3] y [Git deploying a .NET console app to Azure using WebJobs][4].

<a name="AdditionalResources"></a>

### Recursos adicionales

Para obtener una lista anotada de vínculos en la característica WebJobs, consulte [Using the WebJobs feature of Azure Web Sites][5].

* Vídeos relacionados con WebJobs:
  
  [Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa][6]
  
  [Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with
  Jamie Espinosa][7]
  
  [Azure Scheduler 101 - Kevin Lam explains how to schedule stuff][8]

<!-- LINKS -->


[PSonWebJobs]:http://blogs.msdn.com/b/nicktrog/archive/2014/01/22/running-powershell-web-jobs-on-azure-websites.aspx

[HanselIntro]:http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx

[AmitDeploy]:http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs

[AmitConsole]:http://blog.amitapple.com/post/73574681678/git-deploy-console-app

[RickWebJobsCurah]:http://go.microsoft.com/fwlink/?LinkId=390226
<!-- IMAGES -->

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


[1]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
[2]: http://www.hanselman.com/blog/IntroducingWindowsAzureWebJobs.aspx
[3]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
[4]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
[5]: http://go.microsoft.com/fwlink/?LinkId=390226
[6]: http://www.windowsazure.com/en-us/documentation/videos/azure-webjobs-basics/
[7]: http://www.windowsazure.com/en-us/documentation/videos/azure-webjobs-schedule-and-dashboard/
[8]: http://www.windowsazure.com/en-us/documentation/videos/azure-scheduler-how-to/