<properties 
	pageTitle="Implementar trabajos web con Visual Studio" 
	description="Obtenga información sobre cómo implementar trabajos web de Azure en aplicaciones web del servicio de aplicaciones de Azure con Visual Studio." 
	services="app-service\web" 
	documentationCenter="" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Implementar trabajos web con Visual Studio

## Información general

En este tema se explica cómo utilizar Visual Studio para implementar un proyecto de Aplicación de consola en una aplicación web en el [Servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=529714) como un [Trabajo web de Azure](http://go.microsoft.com/fwlink/?LinkId=390226). Para obtener información sobre cómo implementar trabajos web con el [Portal de Azure](http://go.microsoft.com/fwlink/?LinkId=529715), consulte [Ejecución de tareas en segundo plano con trabajos web](web-sites-create-web-jobs.md).

Cuando Visual Studio implementa un proyecto de aplicación de consola con funcionalidad WebJobs, realiza dos tareas:

* Copia los archivos de tiempo de ejecución a la carpeta correspondiente en la aplicación web (*App_Data/jobs/continuous* para trabajos web continuos, *App_Data/jobs/triggered* de trabajos web programada y a petición).
* Configura los [trabajos del programador de Azure](#scheduler) para trabajos web que están programados para ejecutarse en momentos concretos. (Esto no se necesita para WebJobs continuos.)

Un proyecto con funcionalidad WebJobs tiene los siguientes elementos agregados:

* El paquete de NuGet [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/).
* Un archivo [webjob-publish-settings.json](#publishsettings) que contiene la configuración de la implementación y el programador. 

![Diagram showing what is added to a Console App to enable deployment as a WebJob](./media/websites-dotnet-deploy-webjobs/convert.png)

Puede agregar estos elementos a un proyecto de aplicación de consola existente o usar una plantilla para crear un nuevo proyecto de aplicación de consola con funcionalidad WebJobs. 

Puede implementar un proyecto como un WebJob por sí mismo o vincularlo a un proyecto web que se implemente automáticamente siempre que implemente el proyecto web. Para vincular proyectos, Visual Studio incluye el nombre del proyecto habilitado de trabajos web en un archivo [webjobs list.json](#webjobslist) en el proyecto web.

![Diagram showing WebJob project linking to web project](./media/websites-dotnet-deploy-webjobs/link.png)
 


## Requisitos previos

Las características de implementación de WebJobs están disponibles en Visual Studio 2013 al instalar la versión 2.4, o posterior, del SDK de Azure:

* [SDK de Azure para Visual Studio 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409).

Las características de implementación de trabajos web también se incluyen en [Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409) y en actualizaciones posteriores.

## <a id="convert"></a>Activación de la implementación de trabajos web para un proyecto de Aplicación de consola existente

Dispone de dos opciones:

* [Activación de la implementación automática con un proyecto web](#convertlink).

	Configure un proyecto de aplicación de consola existente de forma que se implemente automáticamente como un WebJob cuando implemente un proyecto web. Utilice esta opción cuando desee ejecutar su trabajo web en la misma aplicación web en la que ejecuta la aplicación web relacionada.

* [Activación de la implementación sin un proyecto web](#convertnolink).

	Configure un proyecto de aplicación de consola existente para implementar como WebJob por sí mismo, sin vínculo a un proyecto web. Use esta opción cuando desee ejecutar un trabajo web en una aplicación web por sí mismo, sin ninguna aplicación web ejecutándose en dicha aplicación web. Puede que desee hacer esto para poder escalar los recursos de su WebJob independientemente de los recursos de la aplicación web.

### <a id="convertlink"></a>Activación de la implementación de trabajos web automática con un proyecto web
  
1. Haga clic con el botón secundario en el proyecto web en el **Explorador de soluciones** y luego haga clic en **Agregar** > **Proyecto existente como trabajo web de Azure**.

	![Existing Project as Azure WebJob](./media/websites-dotnet-deploy-webjobs/eawj.png)
	
	Aparece el cuadro de diálogo [Agregar trabajo web de Azure](#configure) .

1. En la lista desplegable **Nombre de proyecto**, seleccione el proyecto de aplicación de consola para agregar como trabajo web.

	![Selecting project in Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw1.png)

2. Complete el cuadro de diálogo [Agregar trabajo web de Azure](#configure) y haga clic en **Aceptar**. 

### <a id="convertnolink"></a>Activación de la implementación de trabajos web sin un proyecto web
  	
1. Haga clic con el botón secundario en el proyecto de Aplicación de consola en el **Explorador de soluciones** y haga clic en **Publicar como trabajo web de Azure**. 

	![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
	Aparece el cuadro de diálogo [Agregar trabajo web de Azure](#configure) con el proyecto seleccionado en el cuadro **Nombre de proyecto**.

2.  Complete el cuadro de diálogo [Agregar trabajo web de Azure](#configure) y haga clic en **Aceptar**.

	Aparece el asistente de **Publicación web**.  Si no quiere realizar la publicación inmediatamente, cierre el asistente. La configuración que ha escrito se guarda para cuando desee [implementar el proyecto](#deploy).

## <a id="create"></a>Creación de un nuevo proyecto con funcionalidad Trabajos web

Para crear un nuevo proyecto con funcionalidad Trabajos web, puede usar la plantilla de proyecto de aplicación de consola y habilitar la implementación de trabajos web tal y como se explicó en [la sección anterior](#convert). Como alternativa, puede usar la plantilla para nuevos proyectos WebJobs:

* [Uso de la plantilla para nuevos proyectos de trabajos web para un trabajo web independiente](#createnolink)

	Cree un proyecto y configúrelo para implementarse por sí mismo como WebJob, sin vínculo a un proyecto web. Use esta opción cuando desee ejecutar un trabajo web en una aplicación web por sí mismo, sin ninguna aplicación web ejecutándose en dicha aplicación web. Puede que desee hacer esto para poder escalar los recursos de su WebJob independientemente de los recursos de la aplicación web.

* [Uso de la plantilla para nuevos proyectos de trabajos web para un trabajo web vinculado a un proyecto web](#createlink)

	Cree un proyecto configurado para implementarse automáticamente como WebJob cuando se implementa un proyecto web en la misma solución. Utilice esta opción cuando desee ejecutar su trabajo web en la misma aplicación web en la que ejecuta la aplicación web relacionada.

En el SDK 2.4, la plantilla para nuevos proyectos WebJobs no es mucho más sencilla que crear un proyecto de aplicación de consola y habilitar la implementación WebJobs. En el futuro, la plantilla para nuevos proyectos de trabajos web será más práctica para la implementación del [SDK de Trabajos web](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs), porque instalará los paquetes de NuGet del SDK de Trabajos web apropiados automáticamente. Hasta entonces, puede configurar un proyecto para usar el SDK de Trabajos web instalando los paquetes manualmente tal y como se muestra en el [tutorial del SDK de Trabajos web](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs). 


### <a id="createnolink"></a>Uso de la plantilla para nuevos proyectos de trabajos web para un trabajo web independiente
  
1. Haga clic en **Archivo** > **Nuevo proyecto** y, en el cuadro de diálogo **Nuevo proyecto**, haga clic en **Nube** > **Trabajo web de Microsoft Azure**.

	![New Project dialog showing WebJob template](./media/websites-dotnet-deploy-webjobs/np.png)
	
2. Siga las instrucciones mostradas anteriormente para [crear el proyecto de aplicación de consola para un proyecto de trabajos web independiente](#convertnolink).

### <a id="createlink"></a>Uso de la plantilla para nuevos proyectos de trabajos web para un trabajo web vinculado a un proyecto web

1. Haga clic con el botón secundario en el proyecto web en el **Explorador de soluciones** y haga clic en **Agregar** > **Nuevo proyecto trabajo web de Azure**.

	![New Azure WebJob Project menu entry](./media/websites-dotnet-deploy-webjobs/nawj.png)

	Aparece el cuadro de diálogo [Agregar trabajo web de Azure](#configure) .

2. Complete el cuadro de diálogo [Agregar trabajo web de Azure](#configure) y haga clic en **Aceptar**.

## <a id="configure"></a>Cuadro de diálogo Agregar trabajo web de Azure

El cuadro de diálogo **Agregar trabajo web de Azure** le permite especificar el nombre de trabajo web y la configuración de programación para su trabajo web. 

![Add Azure WebJob dialog](./media/websites-dotnet-deploy-webjobs/aaw2.png)

The fields in this dialog correspond to fields on the **New Job** dialog of the Azure Management Portal. For more information, see [Run Background tasks with WebJobs](web-sites-create-web-jobs.md).

Para un trabajo web programado (no un trabajo web continuo), Visual Studio crea una colección de trabajos del [Programador de Azure](/services/scheduler/) si todavía no existe y, a su vez, crea un trabajo en la colección:

* La colección de trabajos del programador se denomina  *WebJobs-{regionname}* donde *{regionname}* hace referencia a la región donde se hospeda la aplicación web. Por ejemplo: WebJobs-OesteEE.UU.
* El trabajo del programador se denomina *{websitename}-{webjobname}*. Por ejemplo: MiSitioWeb-MiWebJob. 
 
>[AZURE.NOTE]
> 
>* Para obtener información sobre la implementación de línea de comandos, consulte [Activación de la línea de comandos o de la entrega continua de Trabajos web de Azure](/blog/2014/08/18/enabling-command-line-or-continuous-delivery-of-azure-webjobs/).
>* Si configura un **trabajo recurrente** y establece la frecuencia de periodicidad en un número de minutos, el servicio Programador de Azure no será gratuito. Otras periodicidades (horas, días, etc.) son gratuitas.
>* Si implementa un trabajo web y más tarde cambia el modo de ejecución de continuo a no continuo o viceversa, Visual Studio crea un nuevo trabajo web en Azure cuando vuelva a implementar. Si cambia otra configuración de programación pero deja el mismo modo de ejecución o cambia entre el modo Programado y Bajo demanda, Visual Studio actualiza el trabajo existente en lugar de crear uno nuevo.

## <a id="publishsettings"></a>webjob-publish-settings.json

Cuando se configura una aplicación de consola para la implementación de Trabajos web, Visual Studio instala el paquete de Nuget [Microsoft.Web.WebJobs.Publish](http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/) 
y almacena información de programación en un archivo  *webjob-publish-settings.json* en la carpeta  *Properties* del proyecto Trabajos web. A continuación se muestra un ejemplo de ese archivo:

		{
		  "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
		  "webJobName": "WebJob1",
		  "startTime": "2014-06-23T00:00:00-08:00",
		  "endTime": "2014-06-27T00:00:00-08:00",
		  "jobRecurrenceFrequency": "Minute",
		  "interval": 5,
		  "runMode": "Scheduled"
		}

Puede editar este archivo directamente y Visual Studio proporciona IntelliSense. El archivo de esquema se almacena en [http://schemastore.org](http://schemastore.org/schemas/json/webjob-publish-settings.json) y se puede ver allí.  

>[AZURE.NOTE]
>
>* Si configura un **trabajo recurrente** y establece la frecuencia de periodicidad en un número de minutos, el servicio Programador de Azure no será gratuito. Otras periodicidades (horas, días, etc.) son gratuitas.

## <a id="webjobslist"></a>webjobs-list.json

Cuando vincula un proyecto con funcionalidad de trabajos web a un proyecto web, Visual Studio almacena el nombre del proyecto de trabajos web en un archivo  *webjobs-list.json* en la carpeta  *Properties* del proyecto web. La lista puede contener varios proyectos WebJobs, tal y como se muestra en el siguiente ejemplo:

		{
		  "$schema": "http://schemastore.org/schemas/json/webjobs-list.json",
		  "WebJobs": [
		    {
		      "filePath": "../ConsoleApplication1/ConsoleApplication1.csproj"
		    },
		    {
		      "filePath": "../WebJob1/WebJob1.csproj"
		    }
		  ]
		}

Puede editar este archivo directamente y Visual Studio proporciona IntelliSense. El archivo de esquema se almacena en [http://schemastore.org](http://schemastore.org/schemas/json/webjobs-list.json) y se puede ver allí.
  
## <a id="deploy"></a>Implementación de un proyecto de trabajos web

Un proyecto WebJobs que ha vinculado a un proyecto web se implementa automáticamente con este último. Para obtener información sobre la implementación de proyectos web, consulte [Cómo implementar en aplicaciones web](web-sites-deploy.md).

Para implementar un proyecto de trabajos web por sí mismo, haga clic con el botón secundario en **Explorador de soluciones** y luego haga clic en **Publicar como trabajo web de Azure**. 

![Publish as Azure WebJob](./media/websites-dotnet-deploy-webjobs/paw.png)
	
Para un trabajo web independiente, aparece el mismo asistente **Publicación web** que se usa para los proyectos web, pero con menos configuraciones disponibles para cambiar.

>[AZURE.NOTE] Si quiere empezar a trabajar con el servicio de aplicaciones de Azure antes de contratar una cuenta de Azure, vaya a [Probar el servicio de aplicaciones](http://go.microsoft.com/fwlink/?LinkId=523751), donde puede crear inmediatamente una aplicación web de inicio de corta duración en el servicio de aplicaciones. No es necesario proporcionar ninguna tarjeta de crédito ni asumir ningún compromiso.

## <a id="nextsteps"></a>Pasos siguientes

Para obtener más información sobre cómo implementar trabajos web de Azure desde Visual Studio y con un proceso de entrega continua, consulte [Trabajos web de Azure: recursos recomendados - implementación](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources#deploying).

## Lo que ha cambiado
* Para obtener instrucciones sobre el cambio de sitios web al servicio de aplicaciones, consulte: [Servicio de aplicaciones de Azure y su impacto en los servicios de Azure existentes](http://go.microsoft.com/fwlink/?LinkId=529714)
* Para obtener instrucciones sobre el cambio del portal antiguo al nuevo portal, consulte: [Referencia para navegar por el portal de vista previa](http://go.microsoft.com/fwlink/?LinkId=529715)

<!--HONumber=49-->