<properties linkid="create-vso-project-setup-continuous-deployment" urlDisplayName="How to create a VSO project and setup Continuous Deployment" pageTitle="How to create a Visual Studio Online team project and setup Continuous Deployment - Windows Azure" metaKeywords="Visual Studio Online create team project, continuous deployment to Azure" description="Learn how to create a Visual Studio Online team project and configure it for continuous deployment to Windows Azure." metaCanonical="" services="cloud-services, visual-studio-online" documentationCenter="" title="How to Create and Deploy a Cloud Service" authors="jimlamb" solutions="" writer="jimlamb" manager="" editor="" />

Creación de un proyecto de Visual Studio Online y configuración de la implementación continua en Azure
======================================================================================================

[WACOM.INCLUDE [disclaimer](../includes/disclaimer.md)]

El Portal de administración de Azure le permite crear un proyecto de equipo en Visual Studio Online y configurar la aplicación web para su implementación continua en un sitio web.

Tabla de contenido
------------------

-   [Creación de un proyecto de equipo](#create_team_project)
-   [Creación de una aplicación web e incorporación de la misma a un control de versiones de Git](#create_web_app)
-   [Configuración de la implementación continua](#continuous_deployment)

Creación de un proyecto de equipo
---------------------------------

1.  Inicie sesión en el Portal de administración.
2.  Haga clic en **New** en la esquina inferior izquierda.
3.  Haga clic en **Proyecto de equipo**.
4.  Ponga un nombre al proyecto de equipo. Tenga en cuenta que no podrá cambiar el nombre del proyecto de equipo una vez que lo haya creado.
5.  Elija el tipo de control de versiones que le gustaría usar para el proyecto. Puede elegir entre Git (un sistema de control de versiones distribuido) o el control de versiones de Team Foundation (un sistema de control de versiones centralizado). ¿No está seguro de qué sistema debe usar? Obtenga más información [aquí](http://msdn.microsoft.com/es-es/library/ms181368.aspx).
6.  Elija la plantilla de proceso. Para ver una comparación de las plantillas de proceso, consulte [Trabajar con artefactos de proyecto de equipo](http://msdn.microsoft.com/es-es/library/ms400752.aspx).
7.  Seleccione la cuenta de Visual Studio Online que desee usar para crear este proyecto de equipo, agregue usuarios y supervise el uso de los recursos.
8.  Deje activada la casilla **Add to Startboard** para que el nuevo proyecto de equipo aparezca automáticamente en el panel de inicio.
9.  Haga clic en **Crear**.

Creación de una aplicación web e incorporación de la misma a un control de versiones de Git
-------------------------------------------------------------------------------------------

1.  En el panel de inicio, haga clic en el nuevo proyecto de equipo.
2.  En el modo **Code**, en la sección **Repositorios**, haga clic en el repositorio de Git nombrado tras su proyecto de equipo.
3.  En el cuadro de repositorio/rama, haga clic en el comando de nivel de cuadro **Visual Studio** para abrir el nuevo repositorio en Visual Studio. Puede que su explorador web le pida que autorice el inicio de Visual Studio.
4.  En la ventana de la herramientas de Team Explorer de Visual Studio, haga clic en **Clonar este repositorio** para configurar una clonación local del nuevo repositorio en el disco local.
5.  En la sección **Soluciones** de la página principal de Team Explorer, haga clic en **New...** para crear un proyecto en el repositorio que acaba de clonar.
6.  En el cuadro de diálogo Nuevo proyecto, expanda el nodo de Visual Basic o Visual C\#, dependiendo de su preferencia de lenguaje de programación y, a continuación, seleccione **Web**.
7.  Haga clic en **Aplicación web ASP.NET** en la lista de plantillas de proyecto disponibles y especifique un nombre para la aplicación web.
8.  Haga clic en **OK**.
9.  Cambie a la ventana de herramientas de Team Explorer, navegue hasta la página Changes y escriba un mensaje de confirmación.
10. Haga clic en la flecha desplegable del botón **Confirmar** y en **Confirmar y sincronizar** para confirmar los cambios e insertar la confirmación en el repositorio remoto que ha clonado anteriormente.

Configuración de la implementación continua
-------------------------------------------

1.  Inicie sesión en el Portal de administración.
2.  En el panel de inicio, haga clic en el proyecto de equipo que ha creado anteriormente.
3.  En el modo **Compilación**, haga clic en la parte **Configurar la implementación continua**.
4.  Seleccione el sitio web donde desea implementar la aplicación web.
5.  Seleccione el repositorio donde reside el código fuente (en este momento solo debería tener un repositorio en el proyecto de equipo).
6.  Seleccione la rama que haya que compilar. Visual Studio Online examinará el contenido de la confirmación más reciente de esta rama para un solo archivo de solución de Visual Studio (\*.sln). Si encuentra uno, configurará una nueva definición de compilación (cuyo nombre terminará en "\_CD") para compilar dicha solución. Sin embargo, si no encuentra un archivo de solución, o si encuentra más de uno, configurará una nueva definición de compilación, pero se deshabilitará y tendrá que editarla en Visual Studio para especificar la solución que haya que compilar.
7.  Haga clic en **Crear**.
8.  Visual Studio Online creará una nueva definición de compilación para crear e implementar el proyecto de aplicación e intentar poner en cola una nueva compilación de dicha definición.

### Para comprobar que la implementación se haya completado correctamente

1.  En el panel de inicio, haga clic en el proyecto de equipo que ha creado anteriormente.
2.  En el modo **Compilación**, haga clic en la parte **Última compilación** para abrir el cuadro de la compilación.
3.  En el cuadro de la compilación, haga clic en el primer elemento de la parte **Implementaciones** para abrir el sitio web asociado.
4.  En el cuadro del sitio web, haga clic en el comando del nivel del cuadro **Examinar** para examinar el sitio web y comprobar la implementación de la aplicación web.

