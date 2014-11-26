<properties linkid="web-sites-create-web-jobs" urlDisplayName="Use WebJobs to run background tasks in Microsoft Azure Websites" pageTitle="Use WebJobs to run background tasks in Microsoft Azure Websites" metaKeywords="Microsoft Azure Web Sites, Web Jobs, background tasks" description="Learn how to run background tasks in Microsoft Azure Websites." metaCanonical="" services="web-sites" documentationCenter="" title="Use WebJobs to run background tasks in Microsoft Azure Websites" authors="cephalin"  solutions="" writer="timamm" manager="wpickett" editor="mollybos"  />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="cephalin" />

# Uso de WebJobs para ejecutar tareas en segundo plano en Sitios web de Microsoft Azure

Sitios web Azure le permite ejecutar programas o scripts en el sitio web de una de las tres formas siguientes: a petición, continuamente o en función de una programación. La utilización de WebJobs de Azure no implica ningún coste adicional, a no ser que desee habilitar la característica Always On descrita más adelante en este artículo.

## Tabla de contenido

-   [Tipos de archivos aceptables para scripts][Tipos de archivos aceptables para scripts]
-   [Creación de una tarea a petición][Creación de una tarea a petición]
-   [Creación de una tarea de ejecución continua][Creación de una tarea de ejecución continua]
-   [Creación de una tarea programada][Creación de una tarea programada]

    -   [Trabajos programados y Programador de Azure][Trabajos programados y Programador de Azure]
-   [Visualización del historial de trabajos][Visualización del historial de trabajos]
-   [Notas][Notas]
-   [Pasos siguientes][Pasos siguientes]

    -   [Hacer más con el SDK de WebJobs de Microsoft Azure][Hacer más con el SDK de WebJobs de Microsoft Azure]
    -   [Métodos alternativos de implementación][Métodos alternativos de implementación]
    -   [Recursos adicionales][Recursos adicionales]

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

    ![Tarea a petición][Tarea a petición]

2.  En **Name**, escriba un nombre para la tarea. El nombre debe comenzar con una letra o un número y no puede contener ningún carácter especial salvo "-" y "\_".

3.  En el cuadro **Content (Zip Files - 100MB Max)**, busque el archivo zip que contiene el script. El archivo zip debe contener el ejecutable (.exe, .cmd, .bat, .sh, .php, .py, .js) así como cualquier archivo complementario necesario para ejecutar el programa o script.

4.  En el cuadro **How to Run**, elija **Run on Demand**.

5.  Active la casilla de la parte inferior derecha del cuadro de diálogo para cargar el script en el sitio web. El nombre especificado para la tarea aparece en la lista:

    ![Lista de tareas][Lista de tareas]

6.  Para ejecutar el script, seleccione su nombre en la lista y haga clic en **Run Once** en la barra de comandos, situada en la parte inferior de la página del portal.

    ![Ejecutar una vez][Ejecutar una vez]

<a name="CreateContinuous"></a>

## Creación de una tarea de ejecución continua

1.  Para crear una tarea de ejecución continua, siga los pasos descritos para crear una tarea que se ejecuta solo una vez, pero en el cuadro **How to Run**, elija **Run continuously**.

    ![Nueva tarea continua][Nueva tarea continua]

2.  Para iniciar o detener una tarea que se ejecuta continuamente, seleccione la tarea en la lista y haga clic en **Iniciar** o **Detener** en la barra de comandos.

> [WACOM.NOTE] Si el sitio web se ejecuta en más de una instancia, la tarea que se ejecuta continuamente se ejecutará en todas las instancias. Las tareas a petición o programadas se ejecutan en una única instancia seleccionada por Microsoft Azure para el equilibrio de carga.

> [WACOM.NOTE]
> En el caso de las tareas continuas, se recomienda habilitar **Always On** en la página de configuración de su sitio web. La característica Always On, disponible en modo básico y estándar, impide que se descarguen los sitios web, aunque hayan estado inactivos durante algún tiempo. Si el sitio web siempre está cargado, la tarea que se ejecuta continuamente puede ejecutarse de forma más confiable.

<a name="CreateScheduled"></a>

## Creación de una tarea programada

1.  Para crear una tarea programada, siga los mismos pasos indicados más arriba pero, en el cuadro **How to Run**, elija **Run on a schedule**.

    ![Nuevo trabajo programado][Nuevo trabajo programado]

2.  Elija una región en la opción **Scheduler Region** para el trabajo y, a continuación, haga clic en la flecha de la parte inferior derecha del cuadro de diálogo para continuar a la pantalla siguiente.

3.  En el cuadro de diálogo **Create Job**, elija el tipo de periodicidad que desea en la opción **Recurrence**: **One-time job** o **Recurring job**.

    ![Periodicidad de la programación][Periodicidad de la programación]

4.  También, puede elegir una hora de inicio en **Starting**: **Now** o **At a specific time**.

    ![Hora de inicio de la programación][Hora de inicio de la programación]

5.  Si desea comenzar a una hora determinada, elija los valores de la hora de inicio en **Starting On**.

    ![Inicio de la programación a una hora determinada][Inicio de la programación a una hora determinada]

6.  Si elige un trabajo periódico, en la opción **Recur Every** podrá especificar la frecuencia de la periodicidad y en la opción **Ending On** podrá especificar una hora de finalización.

    ![Periodicidad de la programación][1]

7.  Si elige **Weeks**, puede seleccionar la casilla **On a Particular Schedule** y especificar los días de la semana en los que desea que se ejecute el trabajo.

    ![Días de la semana de la programación][Días de la semana de la programación]

8.  Si elige **Months** y selecciona la casilla **On a Particular Schedule**, podrá establecer que el trabajo se ejecute en un número de días determinado del mes seleccionándolos en la opción **Days**.

    ![Fechas determinadas del mes de la programación][Fechas determinadas del mes de la programación]

9.  Si elige **Week Days**, podrá seleccionar el día o días de la semana del mes en los que desea que se ejecute el trabajo.

    ![Programación en días determinados de la semana de un mes][Programación en días determinados de la semana de un mes]

10. Finalmente, también puede utilizar la opción **Occurrences** para elegir en qué semana del mes (primera, segunda, tercera, etc.) desea que se ejecute el trabajo en los días de la semana especificados.

    ![Programación en días de la semana específicos de semanas determinadas][Programación en días de la semana específicos de semanas determinadas]

11. Después de haber creado uno o varios trabajos, sus nombres aparecerán en la pestaña WebJobs con su estado, tipo de programación y otra información. Se conserva la información histórica de las últimas 30 tareas.

    ![Lista de trabajos][Lista de trabajos]

<a name="Scheduler"></a>

### Trabajos programados y Programador de Azure

Los trabajos programados se pueden configurar posteriormente en el portal del Programador de Azure.

1.  En la página WebJobs, haga clic en el vínculo **schedule** para desplazarse a la página del portal del Programador de Azure.

    ![Vínculo al Programador de Azure][Vínculo al Programador de Azure]

2.  En la página del Programador, haga clic en el trabajo.

    ![Trabajo en la página del portal del Programador][Trabajo en la página del portal del Programador]

3.  Se abre la página **Job Action**, donde puede configurar adicionalmente el trabajo.

    ![Página de acción del trabajo en el Programador][Página de acción del trabajo en el Programador]

<!-- ================ ViewJobHistory ================= -->

<a name="ViewJobHistory"></a>

## Visualización del historial de trabajos

1.  Para ver el historial de ejecución de un trabajo, incluidos los trabajos creados con el SDK de WebJobs, haga clic en su vínculo correspondiente en la columna **Logs**. (Puede utilizar el icono del Portapapeles para copiar la dirección URL de la página de archivo de registro en el Portapapeles si lo desea).

    ![Vínculo de registros][Vínculo de registros]

2.  Si hace clic en el vínculo, se abre la página de detalles de los trabajos web para la tarea. En esta página se muestra el nombre de la ejecución del comando, las últimas veces que se ha ejecutado y si se ha ejecutado correctamente o no. En **Recent job runs**, haga clic una vez para ver detalles adicionales.

    ![Detalles del trabajo web][Detalles del trabajo web]

3.  Se muestra la página **WebJob Run Details**. Haga clic en **Toggle Output** para ver el texto del contenido del registro. El registro de resultados se muestra en formato de texto.

    ![Detalles de ejecución del trabajo web][Detalles de ejecución del trabajo web]

4.  Para ver el texto de salida en otra ventana del explorador, haga clic en el vínculo **download**. Para descargar el texto propiamente dicho, haga clic con el botón secundario en el vínculo y utilice las opciones del explorador para guardar el contenido del archivo.

    ![Resultado del registro de descarga][Resultado del registro de descarga]

5.  El vínculo **WebJobs** situado en la parte superior de la página ofrece una forma cómoda de obtener una lista de los trabajos web en el panel del historial.

    ![Vínculo a la lista de trabajos web][Vínculo a la lista de trabajos web]

    ![Lista de trabajos en el panel del historial][Lista de trabajos en el panel del historial]

    Al hacer clic en uno de estos vínculos, obtendrá acceso a la página de detalles de WebJob para el trabajo que ha seleccionado.

<a name="WHPNotes"></a>

## Notas

-   Desde marzo de 2014, los sitios web en modo libre pueden agotar su tiempo de espera tras 20 minutos si no se realizan solicitudes al sitio de scm (implementación) y el portal del sitio web no está abierto en Azure. Las solicitudes al sitio real no restablecerán esto.

-   Debe escribirse el código para un trabajo continuo para que se ejecute en un bucle infinito.

-   Los trabajos continuos solo se ejecutan continuamente cuando el sitio está activo.

-   Los modos básico y estándar ofrecen la característica Always On que, cuando está habilitada, impide que los sitios se vuelvan inactivos.

<a name="NextSteps"></a>

## Pasos siguientes

<!-- ======= Do More with the Microsoft Azure WebJobs SDK ======== -->

<a name="WebJobsSDK"></a>

### Hacer más con el SDK de WebJobs de Microsoft Azure

El SDK de WebJobs de Microsoft Azure simplifica la tarea de programar un WebJob que funciona con colas, blobs o tablas de Almacenamiento de Azure o con colas de Azure Service Bus. El panel, ahora integrado en el portal de Azure, ofrece una supervisión y un diagnóstico completos para los programas que escriba usando el SDK. Las características de supervisión y diagnóstico están incluidas en el SDK y no requieren que agregue ningún código especial en el programa.

Para obtener más información, consulte el tutorial [Getting Started with Microsoft Azure WebJobs SDK][Getting Started with Microsoft Azure WebJobs SDK].

<!-- =========== Alternative Methods of Deployment ============= -->

<a name="AlternateDeployments"></a>

### Métodos alternativos de implementación

Visual Studio 2013 incluye características que automatizan la implementación de los proyectos de la aplicación de consola como WebJobs. Para obtener más información, consulte [Implementación de WebJobs de Azure a Sitios web de Azure][Implementación de WebJobs de Azure a Sitios web de Azure].

También puede usar FTP, Git o Web Deploy para implementar WebJobs. Para obtener más información, consulte[Git que implementa una aplicación de consola de .NET a Azure mediante WebJobs][Git que implementa una aplicación de consola de .NET a Azure mediante WebJobs] e [Implementación de WebJobs de Windows Azure][Implementación de WebJobs de Windows Azure].

<a name="AdditionalResources"></a>

### Recursos adicionales

-   Para obtener una lista anotada de vínculos en la característica WebJobs, consulte [WebJobs Azure: recursos recomendados][WebJobs Azure: recursos recomendados].

-   Vídeos relacionados con WebJobs:

    [Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa][Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa]

    [Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with Jamie Espinosa][Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with Jamie Espinosa]

    [Azure Scheduler 101 - Kevin Lam explains how to schedule stuff][Azure Scheduler 101 - Kevin Lam explains how to schedule stuff]

### Comience ahora

Para comenzar con Azure, puede usar una [evaluación gratuita de Microsoft Azure][evaluación gratuita de Microsoft Azure].


  [Tipos de archivos aceptables para scripts]: #acceptablefiles
  [Creación de una tarea a petición]: #CreateOnDemand
  [Creación de una tarea de ejecución continua]: #CreateContinuous
  [Creación de una tarea programada]: #CreateScheduled
  [Trabajos programados y Programador de Azure]: #Scheduler
  [Visualización del historial de trabajos]: #ViewJobHistory
  [Notas]: #WHPNotes
  [Pasos siguientes]: #NextSteps
  [Hacer más con el SDK de WebJobs de Microsoft Azure]: #WebJobsSDK
  [Métodos alternativos de implementación]: #AlternateDeployments
  [Recursos adicionales]: #AdditionalResources
  [Tarea a petición]: ./media/web-sites-create-web-jobs/01aOnDemandWebJob.png
  [Lista de tareas]: ./media/web-sites-create-web-jobs/02aWebJobsList.png
  [Ejecutar una vez]: ./media/web-sites-create-web-jobs/13RunOnce.png
  [Nueva tarea continua]: ./media/web-sites-create-web-jobs/03aNewContinuousJob.png
  [Nuevo trabajo programado]: ./media/web-sites-create-web-jobs/04aNewScheduledJob.png
  [Periodicidad de la programación]: ./media/web-sites-create-web-jobs/05SchdRecurrence.png
  [Hora de inicio de la programación]: ./media/web-sites-create-web-jobs/06SchdStart.png
  [Inicio de la programación a una hora determinada]: ./media/web-sites-create-web-jobs/07SchdStartOn.png
  [1]: ./media/web-sites-create-web-jobs/08SchdRecurEvery.png
  [Días de la semana de la programación]: ./media/web-sites-create-web-jobs/09SchdWeeksOnParticular.png
  [Fechas determinadas del mes de la programación]: ./media/web-sites-create-web-jobs/10SchdMonthsOnPartDays.png
  [Programación en días determinados de la semana de un mes]: ./media/web-sites-create-web-jobs/11SchdMonthsOnPartWeekDays.png
  [Programación en días de la semana específicos de semanas determinadas]: ./media/web-sites-create-web-jobs/12SchdMonthsOnPartWeekDaysOccurences.png
  [Lista de trabajos]: ./media/web-sites-create-web-jobs/13WebJobsListWithSeveralJobs.png
  [Vínculo al Programador de Azure]: ./media/web-sites-create-web-jobs/31LinkToScheduler.png
  [Trabajo en la página del portal del Programador]: ./media/web-sites-create-web-jobs/32SchedulerPortal.png
  [Página de acción del trabajo en el Programador]: ./media/web-sites-create-web-jobs/33JobActionPageInScheduler.png
  [Vínculo de registros]: ./media/web-sites-create-web-jobs/14WebJobLogs.png
  [Detalles del trabajo web]: ./media/web-sites-create-web-jobs/15WebJobDetails.png
  [Detalles de ejecución del trabajo web]: ./media/web-sites-create-web-jobs/16WebJobRunDetails.png
  [Resultado del registro de descarga]: ./media/web-sites-create-web-jobs/17DownloadLogOutput.png
  [Vínculo a la lista de trabajos web]: ./media/web-sites-create-web-jobs/18WebJobsLinkToDashboardList.png
  [Lista de trabajos en el panel del historial]: ./media/web-sites-create-web-jobs/19WebJobsListInJobsDashboard.png
  [Getting Started with Microsoft Azure WebJobs SDK]: http://asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [Implementación de WebJobs de Azure a Sitios web de Azure]: http://azure.microsoft.com/es-es/documentation/articles/websites-dotnet-deploy-webjobs
  [Git que implementa una aplicación de consola de .NET a Azure mediante WebJobs]: http://blog.amitapple.com/post/73574681678/git-deploy-console-app
  [Implementación de WebJobs de Windows Azure]: http://blog.amitapple.com/post/74215124623/deploy-azure-webjobs
  [WebJobs Azure: recursos recomendados]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Azure WebJobs 101 - Basic WebJobs with Jamie Espinosa]: http://www.windowsazure.com/es-es/documentation/videos/azure-webjobs-basics/
  [Azure WebJobs 102 - Scheduled WebJobs and the WebJobs Dashboard with Jamie Espinosa]: http://www.windowsazure.com/es-es/documentation/videos/azure-webjobs-schedule-and-dashboard/
  [Azure Scheduler 101 - Kevin Lam explains how to schedule stuff]: http://www.windowsazure.com/es-es/documentation/videos/azure-scheduler-how-to/
  [evaluación gratuita de Microsoft Azure]: http://azure.microsoft.com/es-es/pricing/free-trial/
