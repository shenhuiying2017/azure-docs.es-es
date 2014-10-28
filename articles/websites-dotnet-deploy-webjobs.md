<properties linkid="websites-dotnet-deploy-webjobs" urlDisplayName="Deploy Azure WebJobs to Azure Websites" pageTitle="Deploy Azure WebJobs to Azure Websites" metaKeywords="Azure Websites, WebJobs, background tasks" description="Learn how to deploy Azure WebJobs to Azure Websites using Visual Studio." metaCanonical="" services="web-sites" documentationCenter="" title="Deploy Azure WebJobs to Azure Websites" authors="tdykstra"  solutions="" manager="wpickett" editor="mollybos"  />

# Implementación de WebJobs de Azure en Sitios web de Azure

En este tema se explica cómo usar Visual Studio para implementar un proyecto de aplicación de consola en un sitio web de Azure como un [WebJob de Azure][WebJob de Azure]. Una forma alternativa de implementar WebJobs es usar el Portal de administración de Azure. Para obtener información acerca de ese método, consulte [Utilización de WebJobs para ejecutar tareas en segundo plano en Sitios web Microsoft Azure][Utilización de WebJobs para ejecutar tareas en segundo plano en Sitios web Microsoft Azure].

## Tabla de contenido

-   [Información general][Información general]
-   [Requisitos previos][Requisitos previos]
-   [Activación de la implementación de WebJobs para un proyecto de aplicación de consola existente][Activación de la implementación de WebJobs para un proyecto de aplicación de consola existente]
-   [Creación de un nuevo proyecto con funcionalidad WebJobs][Creación de un nuevo proyecto con funcionalidad WebJobs]
-   [Cuadro de diálogo Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure]
-   [Archivo webjob-publish-settings.json][Archivo webjob-publish-settings.json]
-   [Archivo webjobs-list.json][Archivo webjobs-list.json]
-   [Implementación de un proyecto WebJobs][Implementación de un proyecto WebJobs]
-   [Pasos siguientes][Pasos siguientes]

## <span id="overview"></span></a>Información general

Cuando Visual Studio implementa un proyecto de aplicación de consola con funcionalidad WebJobs, realiza dos tareas:

-   Copiar archivos de runtime a la carpeta apropiada de Sitios web Azure (*App\_Data/jobs/continuous* para WebJobs continuos, *App\_Data/jobs/triggered* para WebJobs programados y bajo demanda).
-   Configurar [trabajos del Programador de Azure][trabajos del Programador de Azure] para WebJobs que se programan para ejecutarse en momentos concretos. (Esto no se necesita para WebJobs continuos.)

Un proyecto con funcionalidad WebJobs tiene los siguientes elementos agregados:

-   El paquete de NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish].
-   Un archivo [webjob-publish-settings.json][Archivo webjob-publish-settings.json] que contiene configuración de implementación y del programador.

![Diagram showing what is added to a Console App to enable deployment as a WebJob][Diagram showing what is added to a Console App to enable deployment as a WebJob]

Puede agregar estos elementos a un proyecto de aplicación de consola existente o usar una plantilla para crear un nuevo proyecto de aplicación de consola con funcionalidad WebJobs.

Puede implementar un proyecto como un WebJob por sí mismo o vincularlo a un proyecto web que se implemente automáticamente siempre que implemente el proyecto web. Para vincular proyectos, Visual Studio incluye el nombre del proyecto con funcionalidad WebJob en un archivo [webjobs-list.json][Archivo webjobs-list.json] en el proyecto web.

![Diagram showing WebJob project linking to web project][Diagram showing WebJob project linking to web project]

## Requisitos previos

Las características de implementación de WebJobs están disponibles en Visual Studio 2013 al instalar la versión 2.4, o posterior, del SDK de Azure:

-   [SDK de Azure para Visual Studio 2013][SDK de Azure para Visual Studio 2013].

Las características de implementación de WebJobs también se incluyen en [Update 3][Update 3].

## <span id="convert"></span></a>Activación de la implementación de WebJobs para un proyecto de aplicación de consola existente

Dispone de dos opciones:

-   [Activación de la implementación automática con un proyecto web][Activación de la implementación automática con un proyecto web].

    Configure un proyecto de aplicación de consola existente de forma que se implemente automáticamente como un WebJob cuando implemente un proyecto web. Utilice esta opción cuando desee ejecutar WebJob en el mismo sitio web en el que ejecuta la aplicación web relacionada.

-   [Activación de la implementación sin un proyecto web][Activación de la implementación sin un proyecto web].

    Configure un proyecto de aplicación de consola existente para implementar como WebJob por sí mismo, sin vínculo a un proyecto web. Utilice esta opción cuando desee ejecutar un WebJob en un sitio web por sí mismo, sin ninguna aplicación web ejecutándose en dicho sitio web. Puede que desee hacer esto para poder escalar los recursos de su WebJob independientemente de los recursos de la aplicación web.

### <span id="convertlink"></span></a>Activación de la implementación de WebJobs automática con un proyecto web

1.  Haga clic con el botón secundario en el proyecto web en el **Explorador de soluciones** y, después, haga clic en **Agregar** \> **Proyecto existente como WebJob de Azure**.

    ![Existing Project as Azure WebJob][Existing Project as Azure WebJob]

    Aparecerá el cuadro de diálogo [Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure].

2.  En la lista desplegable **Nombre de proyecto**, seleccione el proyecto de aplicación de consola para agregar como WebJob.

    ![Selecting project in Add Azure WebJob dialog][Selecting project in Add Azure WebJob dialog]

3.  Complete el cuadro de diálogo [Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure] y haga clic en **Aceptar**.

### <span id="convertnolink"></span></a> Activación de la implementación de WebJobs sin un proyecto web

1.  Haga clic con el botón secundario en el proyecto de aplicación de consola en el **Explorador de soluciones** y, después, haga clic en **Publicar como WebJob de Azure**.

    ![Publish as Azure WebJob][Publish as Azure WebJob]

    Aparecerá el cuadro de diálogo [Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure], con el proyecto seleccionado en el cuadro **Nombre de proyecto**.

2.  Complete el cuadro de diálogo [Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure] y haga clic en **Aceptar**.

    Aparecerá el asistente **Publicación web**. Si no quiere realizar la publicación inmediatamente, cierre el asistente. La configuración que ha escrito se guarda para cuando desee [implementar el proyecto][Implementación de un proyecto WebJobs].

## <span id="create"></span></a>Creación de un nuevo proyecto con funcionalidad WebJobs

Para crear un nuevo proyecto con funcionalidad WebJobs, puede usar la plantilla de proyecto de aplicación de consola y habilitar la implementación WebJobs tal y como se explicó en [la sección anterior][Activación de la implementación de WebJobs para un proyecto de aplicación de consola existente]. Como alternativa, puede usar la plantilla para nuevos proyectos WebJobs:

-   [Uso de la plantilla para nuevos proyectos WebJobs para un WebJob vinculado a un proyecto web][Uso de la plantilla para nuevos proyectos WebJobs para un WebJob vinculado a un proyecto web]

    Cree un proyecto configurado para implementarse automáticamente como WebJob cuando se implementa un proyecto web en la misma solución. Utilice esta opción cuando desee ejecutar WebJob en el mismo sitio web en el que ejecuta la aplicación web relacionada.

-   [Uso de la plantilla para nuevos proyectos WebJobs para un WebJob independiente][Uso de la plantilla para nuevos proyectos WebJobs para un WebJob independiente]

    Cree un proyecto y configúrelo para implementarse por sí mismo como WebJob, sin vínculo a un proyecto web. Utilice esta opción cuando desee ejecutar un WebJob en un sitio web por sí mismo, sin ninguna aplicación web ejecutándose en dicho sitio web. Puede que desee hacer esto para poder escalar los recursos de su WebJob independientemente de los recursos de la aplicación web.

En el SDK 2.4, la plantilla para nuevos proyectos WebJobs no es mucho más sencilla que crear un proyecto de aplicación de consola y habilitar la implementación WebJobs. En el futuro, la plantilla para nuevos proyectos WebJobs será más práctica para la implementación del [SDK de WebJobs][SDK de WebJobs], porque instalará los paquetes de NuGet del SDK de WebJobs apropiados automáticamente. Hasta entonces, puede configurar un proyecto para usar el SDK de WebJobs instalando los paquetes manualmente tal y como se muestra en el [tutorial del SDK de WebJobs][SDK de WebJobs].

### <span id="createlink"></span></a> Uso de la plantilla para nuevos proyectos WebJobs para un WebJob vinculado a un proyecto web

-   Haga clic con el botón secundario en el proyecto web en el **Explorador de soluciones** y, después, haga clic en **Agregar** \> **Nuevo proyecto WebJob de Azure**.

    ![New Azure WebJob Project menu entry][New Azure WebJob Project menu entry]

    Aparecerá el cuadro de diálogo [Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure].

-   Complete el cuadro de diálogo [Agregar WebJob de Azure][Cuadro de diálogo Agregar WebJob de Azure] y haga clic en **Aceptar**.

### <span id="createnolink"></span></a> Uso de la plantilla para nuevos proyectos WebJobs para un WebJob independiente

1.  Haga clic en **Archivo** \> **Nuevo proyecto** y, después, en el cuadro de diálogo **Nuevo proyecto**, haga clic en **Nube** \> **WebJob de Microsoft Azure**.

    ![New Project dialog showing WebJob template][New Project dialog showing WebJob template]

2.  Siga las instrucciones mostradas anteriormente para [crear el proyecto de aplicación de consola para un proyecto WebJobs independiente][Activación de la implementación sin un proyecto web].

## <span id="configure"></span></a>Cuadro de diálogo Agregar WebJob de Azure

El cuadro de diálogo **Agregar WebJob de Azure** le permite especificar el nombre de WebJob y la configuración de programación para su WebJob.

![Add Azure WebJob dialog][Add Azure WebJob dialog]

Los campos de este cuadro de dialogo se corresponden con los campos del cuadro de diálogo **Nuevo trabajo** del Portal de administración de Azure. Para obtener más información, consulte [Utilización de WebJobs para ejecutar tareas en segundo plano en Sitios web Microsoft Azure][Utilización de WebJobs para ejecutar tareas en segundo plano en Sitios web Microsoft Azure].

Para un WebJob programado (no un WebJobs continuo), Visual Studio crea una colección de trabajos de [Programador de Azure][Programador de Azure] si todavía no existe y, a su vez, crea un trabajo en la colección:

-   La colección de trabajos del programador se llama *WebJobs-{nombreregión}* donde *{nombreregión}* se refiere a la región en la que se hospeda el sitio web. Por ejemplo: WebJobs-OesteEE.UU.
-   El trabajo del programador se llama *{nombresitioweb}-{nombrewebjob}*. Por ejemplo: MiSitioWeb-MiWebJob.

> [WACOM.NOTE]
>
> -   La implementación de la línea de comandos usando MSBuild no configura la programación para WebJobs programados. Esta función solamente está disponible cuando se realiza la implementación usando Visual Studio.
> -   Si configura un **trabajo recurrente** y establece la frecuencia de periodicidad en un número de minutos, el servicio Programador de Azure no será gratuito. Otras periodicidades (horas, días, etc.) son gratuitas.
> -   Si implementa un WebJob y más tarde cambia el modo de ejecución de continuo a no continuo o viceversa, Visual Studio crea un nuevo WebJob en Azure cuando vuelva a implementar. Si cambia otra configuración de programación pero deja el mismo modo de ejecución o cambia entre el modo Programado y Bajo demanda, Visual Studio actualiza el trabajo existente en lugar de crear uno nuevo.

## <span id="publishsettings"></span></a>webjob-publish-settings.json

Cuando crea una aplicación de consola para la implementación de WebJobs, Visual Studio instala el paquete de NuGet [Microsoft.Web.WebJobs.Publish][Microsoft.Web.WebJobs.Publish]
y almacena la información de programación en un archivo *webjob-publish-settings.json* en la carpeta *Properties* del proyecto WebJobs. A continuación se muestra un ejemplo de ese archivo:

        {
          "$schema": "http://schemastore.org/schemas/json/webjob-publish-settings.json",
          "webJobName": "WebJob1",
          "startTime": "2014-06-23T00:00:00-08:00",
          "endTime": "2014-06-27T00:00:00-08:00",
          "jobRecurrenceFrequency": "Minute",
          "interval": 5,
          "runMode": "Scheduled"
        }

Puede editar este archivo directamente y Visual Studio proporciona IntelliSense. El archivo de esquema se almacena en [][]<http://schemastore.org></a> y se puede ver allí.

> [WACOM.NOTE]
>
> -   Si configura un **trabajo recurrente** y establece la frecuencia de periodicidad en un número de minutos, el servicio Programador de Azure no será gratuito. Otras periodicidades (horas, días, etc.) son gratuitas.

## <span id="webjobslist"></span></a>webjobs-list.json

Cuando vincule un proyecto con funcionalidad WebJobs a un proyecto web, Visual Studio almacenará el nombre del proyecto WebJobs en un archivo *webjobs-list.json* en la carpeta *Properties* del proyecto web. La lista puede contener varios proyectos WebJobs, tal y como se muestra en el siguiente ejemplo:

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

Puede editar este archivo directamente y Visual Studio proporciona IntelliSense. El archivo de esquema se almacena en [][1]<http://schemastore.org></a> y se puede ver allí.

## <span id="deploy"></span></a>Implementación de un proyecto WebJobs

Un proyecto WebJobs que ha vinculado a un proyecto web se implementa automáticamente con este último. Para obtener más información acerca de la implementación de proyectos web, consulte [Implementación de un sitio web de Azure][Implementación de un sitio web de Azure].

Para implementar un proyecto WebJobs por sí mismo, haga clic con el botón secundario en **Explorador de soluciones** y, después, haga clic en **Publicar como WebJob de Azure**.

![Publish as Azure WebJob][Publish as Azure WebJob]

Para un WebJob independiente, aparece el mismo asistente **Publicación web** que se usa para los proyectos web, pero con menos configuraciones disponibles para cambiar.

## <span id="nextsteps"></span></a>Pasos siguientes

Para obtener más información acerca de WebJobs de Azure y del SDK de WebJobs, consulte [WebJobs de Azure - Recursos recomendados][WebJobs de Azure - Recursos recomendados].

  [WebJob de Azure]: http://go.microsoft.com/fwlink/?LinkId=390226
  [Utilización de WebJobs para ejecutar tareas en segundo plano en Sitios web Microsoft Azure]: /es-es/documentation/articles/web-sites-create-web-jobs/
  [Información general]: #overview
  [Requisitos previos]: #prerequisites
  [Activación de la implementación de WebJobs para un proyecto de aplicación de consola existente]: #convert
  [Creación de un nuevo proyecto con funcionalidad WebJobs]: #create
  [Cuadro de diálogo Agregar WebJob de Azure]: #configure
  [Archivo webjob-publish-settings.json]: #publishsettings
  [Archivo webjobs-list.json]: #webjobslist
  [Implementación de un proyecto WebJobs]: #deploy
  [Pasos siguientes]: #next-steps
  [trabajos del Programador de Azure]: #scheduler
  [Microsoft.Web.WebJobs.Publish]: http://www.nuget.org/packages/Microsoft.Web.WebJobs.Publish/
  [Diagram showing what is added to a Console App to enable deployment as a WebJob]: ./media/websites-dotnet-deploy-webjobs/convert.png
  [Diagram showing WebJob project linking to web project]: ./media/websites-dotnet-deploy-webjobs/link.png
  [SDK de Azure para Visual Studio 2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
  [Update 3]: http://go.microsoft.com/fwlink/?linkid=397827&clcid=0x409
  [Activación de la implementación automática con un proyecto web]: #convertlink
  [Activación de la implementación sin un proyecto web]: #convertnolink
  [Existing Project as Azure WebJob]: ./media/websites-dotnet-deploy-webjobs/eawj.png
  [Selecting project in Add Azure WebJob dialog]: ./media/websites-dotnet-deploy-webjobs/aaw1.png
  [Publish as Azure WebJob]: ./media/websites-dotnet-deploy-webjobs/paw.png
  [Uso de la plantilla para nuevos proyectos WebJobs para un WebJob vinculado a un proyecto web]: #createlink
  [Uso de la plantilla para nuevos proyectos WebJobs para un WebJob independiente]: #createnolink
  [SDK de WebJobs]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/getting-started-with-windows-azure-webjobs
  [New Azure WebJob Project menu entry]: ./media/websites-dotnet-deploy-webjobs/nawj.png
  [New Project dialog showing WebJob template]: ./media/websites-dotnet-deploy-webjobs/np.png
  [Add Azure WebJob dialog]: ./media/websites-dotnet-deploy-webjobs/aaw2.png
  [Programador de Azure]: /es-es/services/scheduler/
  []: http://schemastore.org/schemas/json/webjob-publish-settings.json
  [1]: http://schemastore.org/schemas/json/webjobs-list.json
  [Implementación de un sitio web de Azure]: /es-es/documentation/articles/websites-dotnet-deploy/
  [WebJobs de Azure - Recursos recomendados]: http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/azure-webjobs-recommended-resources
