<properties 
	pageTitle="Creación de una aplicación web Hello World para Azure en IntelliJ | Microsoft Azure" 
	description="En este tutorial se muestra cómo utilizar el Kit de herramientas de Azure para IntelliJ para crear una aplicación web Hello World para Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="selvasingh" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="08/11/2016" 
	ms.author="asirveda;robmcm"/>

# Creación de una aplicación web Hello World para Azure en IntelliJ

En este tutorial se muestra cómo crear e implementar una aplicación básica Hello World en Azure como una aplicación web mediante el [Kit de herramientas de Azure para IntelliJ]. Para que sea más sencillo, se muestra un ejemplo básico de JSP, pero los mismos pasos se aplicarían en el caso de un servlet Java en lo que respecta a la implementación de Azure.

Cuando haya completado este tutorial, la aplicación se parecerá a la que se muestra en la siguiente ilustración al verla en un explorador web:

![][01]
 
## Requisitos previos

* Un kit para desarrolladores de Java (JDK) de la versión 1.8 o superior.
* IntelliJ IDEA Ultimate Edition. Esto se puede descargar en <https://www.jetbrains.com/idea/download/index.html>.
* Una distribución de un servidor web basado en Java o un servidor de aplicaciones, como Apache Tomcat o Jetty.
* Una suscripción de Azure, que se puede adquirir en <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El kit de herramientas de Azure para IntelliJ. Para obtener más información, consulte [Installing the Azure Toolkit for IntelliJ] (Instalación del kit de herramientas de Azure para IntelliJ).

## Para crear una aplicación Hello World, siga estos pasos:

En primer lugar, empezaremos con la creación de un proyecto Java.

1. Inicie IntelliJ y, en el menú, haga clic en **Archivo**, en **Nuevo** y, después, en **Proyecto**.

   ![][02]

1. En el cuadro de diálogo Nuevo proyecto, seleccione **Java** y **Aplicación web** y, después, haga clic en **Siguiente**.

   ![][03a]

   Si se le pregunta si desea continuar sin haber asignado ningún SDK, haga clic en **Sí**.

   ![][03b]

1. Para este tutorial, llame al proyecto **Java-Web-App-On-Azure** y haga clic en **Finalizar**.

   ![][04]

1. En la vista del explorador de proyectos de IntelliJ, expanda **Java-Web-App-On-Azure**, después **web** y, luego, haga doble clic en **index.jsp**.

   ![][05]

1. Cuando el archivo index.jsp se abra en IntelliJ, agregue texto para que se muestre dinámicamente **Hello World!** dentro del elemento `<body>` existente. El contenido de `<body>` actualizado debe parecerse al siguiente ejemplo:

   `<body><b><% out.println("Hello World!"); %></b></body>`

1. Guarde el archivo index.jsp.

## Para implementar la aplicación en un contenedor de aplicaciones web de Azure, siga estos pasos:

Existen varias maneras de implementar una aplicación web de Java en Azure. En este tutorial se describe una de las más sencillas: la aplicación se implementará en un contenedor de aplicaciones de Azure: no se necesita ningún tipo de proyecto especial ni herramientas adicionales. Azure facilitará el JDK y el software de contenedor web, así que no es necesario que cargue los suyos; lo único que necesita es su aplicación web de Java. Como resultado, el proceso de publicación de su aplicación tardará unos segundos y no minutos.

1. En el Explorador de proyectos de IntelliJ, haga clic con el botón derecho en el proyecto **Java-Web-App-On-Azure**. Cuando aparezca el menú contextual, seleccione **Azure** y, después, haga clic en **Publish as Azure Web App** (Publicar como aplicación web de Azure).

   ![][06]

1. Si aún no ha iniciado sesión en Azure desde IntelliJ, se le pedirá que inicie sesión en su cuenta de Azure:

   ![][07]

   Nota: Si tiene varias cuentas de Azure, puede que algunos de los avisos que aparecen durante el proceso de inicio de sesión se muestren más de una vez, incluso si parecen ser iguales. Cuando esto ocurra, siga la instrucciones de inicio de sesión.

1. Cuando haya iniciado sesión correctamente en su cuenta de Azure, el cuadro de diálogo **Manage Subscriptions** (Administrar suscripciones) mostrará una lista de suscripciones que están asociados con sus credenciales. Si aparecen varias suscripciones y quiere trabajar solo con un subconjunto específico de ellas, opcionalmente puede desactivar las que no utilice. Cuando haya seleccionado las suscripciones, haga clic en **Cerrar**.

   ![][08]

1. Cuando aparezca el cuadro de diálogo **Deploy to Azure Web App Container** (Implementar en el contenedor de aplicaciones web de Azure), se mostrarán los contenedores de aplicaciones web creados anteriormente; si no ha creado ningún contenedor, la lista estará vacía.

   ![][09]

1. Si no ha creado un contenedor de aplicaciones web de Azure antes, o si quiere publicar la aplicación en un nuevo contenedor, siga estos pasos. De lo contrario, seleccione un contenedor de aplicaciones web existente y vaya al paso 6 que encontrará a continuación.

  1. Haga clic en **+**.

        ![][10]

  1. Se mostrará el cuadro de diálogo **New Web App Container** (Nuevo contenedor de aplicaciones web), que se usará para los siguientes pasos.

        ![][11]

  1. Escriba un valor para **Etiqueta DNS** para el contenedor de aplicaciones web; esta será la etiqueta DNS hoja de la dirección URL del host de la aplicación web en Azure. Nota: El nombre debe estar disponible y ajustarse a los requisitos de nomenclatura de las aplicaciones web de Azure.

  1. En el menú desplegable **Contenedor web**, seleccione el software adecuado para su aplicación.

        Currently, you can choose from Tomcat 8, Tomcat 7 or Jetty 9. A recent distribution of the selected software will be provided by Azure, and it will run on a recent distribution of JDK 8 created by Oracle and provided by Azure.

  1. En el menú desplegable **Suscripción**, seleccione la suscripción que quiere usar para esta implementación.

  1. En el menú desplegable **Grupo de recursos**, seleccione el grupo de recursos con el que desea asociar la aplicación web.

        Note: Azure Resource Groups allow you to group related resources together so that, for example, they can be deleted together.

        You can select an existing Resource Group (if you have any) and skip to step g below, or use the following these steps to create a new Resource Group:

      * Haga clic en **Nuevo...**

      * Se muestra el cuadro de diálogo **Nuevo grupo de recursos**:

            ![][12]

      * En el cuadro de texto **Nombre**, especifique un nombre para el nuevo grupo de recursos.

      * En el menú desplegable **Región**, seleccione la ubicación adecuada del centro de recursos de Azure para su grupo de recursos.

      * Haga clic en **Aceptar**.

  1. En el menú desplegable **Plan del servicio de aplicaciones** se muestran los planes del servicio de aplicaciones que están asociados con el grupo de recursos seleccionado.

        Note: An App Service Plan specifies information such as the location of your Web App, the pricing tier and the compute instance size. A single App Service Plan can be used for multiple Web Apps, which is why it is maintained separately from a specific Web App deployment.

        You can select an existing App Service Plan (if you have any) and skip to step h below, or use the following these steps to create a new App Service Plan:

      * Haga clic en **Nuevo...**

      * Se muestra el cuadro de diálogo **Nuevo plan del servicio de aplicaciones**:

            ![][13]

      * En el cuadro de texto **Nombre**, especifique un nombre para el nuevo plan.

      * En el menú desplegable **Ubicación**, seleccione la ubicación adecuada del centro de recursos de Azure para el plan.

      * En el menú desplegable **Plan de tarifa**, seleccione la tarifa adecuada para el plan. Con fines de prueba, puede elegir **Gratis**.

      * En el menú desplegable **Tamaño de instancia**, seleccione el tamaño de instancia adecuado para el plan. Con fines de prueba, puede elegir **Pequeño**.

  1. Cuando haya completado todos los pasos anteriores, el cuadro de diálogo Nuevo contenedor de aplicaciones web debe parecerse al de la siguiente ilustración:

        ![][14]

  1. Haga clic en **Aceptar** para finalizar la creación de su nuevo contenedor de aplicaciones web.

        Wait a few seconds for the list of the Web App containers to be refreshed, and your newly-created web app container should now be selected in the list.

1. Ya puede completar la implementación inicial de su aplicación web en Azure; haga clic en **Aceptar** para implementar la aplicación de Java en el contenedor de aplicación web seleccionado.

    ![][15]

    Nota: De forma predeterminada, la aplicación se implementará como un subdirectorio del servidor de aplicaciones. Si desea que se implemente como la aplicación raíz, active la casilla **Deploy to root** (Implementar en raíz) antes de hacer clic en **Aceptar**.

1. A continuación, debería mostrarse la vista **Azure Activity Log** (Registro de actividad de Azure), que indicará el estado de implementación de la aplicación web.

    ![][16]

    El proceso de implementación de la aplicación web en Azure debería tardar solo unos segundos en completarse. Cuando su aplicación esté lista, verá un vínculo denominado **Published** (Publicada) en la columna **Status** (Estado). Al hacer clic en el vínculo, se le llevará a la página principal de la aplicación web implementada. Por otro lado, puede utilizar los pasos de la sección siguiente para ir a la aplicación web.

## Desplazamiento hasta su aplicación web en Azure

Para ir a su aplicación web en Azure, puede usar la vista **Azure Explorer** (Explorador de Azure).

Si la vista **Azure Explorer** (Explorador de Azure) no está abierta aún, puede abrirla haciendo clic en el botón **Ver** en IntelliJ, en **Ventanas de herramientas** y, después, en **Service Explorer** (Explorador de servicios). Si anteriormente no ha iniciado sesión, se le pedirá que lo haga.

Cuando se muestre la vista **Azure Explorer** (Explorador de Azure), siga estos pasos para detener su aplicación web:

1. Expanda el nodo **Azure**.

1. Expanda el nodo **Aplicaciones web**.

1. Haga clic en la aplicación web deseada.

1. Cuando aparezca el menú contextual, haga clic en **Abrir en el explorador**.

    ![][17]

## Actualización de la aplicación web

Actualizar una aplicación web de Azure existente es un proceso rápido y sencillo, y tiene dos opciones para ello:

* Puede actualizar la implementación de una aplicación web de Java existente.
* Puede publicar una aplicación Java adicional en el mismo contenedor de aplicaciones web.

En cualquier caso, el proceso es idéntico y tarda unos pocos segundos:

1. En el Explorador de proyectos de IntelliJ, haga clic con el botón derecho en la aplicación Java que quiere actualizar o agregar a un contenedor de aplicaciones web existente.

1. Cuando aparezca el menú contextual, seleccione **Azure** y, después, haga clic en **Publish as Azure Web App** (Publicar como aplicación web de Azure).

1. Puesto que ya ha iniciado sesión anteriormente, verá una lista de contenedores de aplicaciones web existentes. Seleccione aquel en el que quiere publicar o volver a publicar la aplicación Java y haga clic en **Aceptar**.

Unos segundos más tarde, la vista **Azure Activity Log** (Registro de actividad de Azure) mostrará la implementación actualizada como **Publicado** y podrá comprobar la aplicación actualizada en un explorador web.

## Inicio o detención de una aplicación web existente

Para iniciar o detener un contenedor de aplicaciones web existente (junto con todas las aplicaciones Java implementadas en él), puede utilizar la vista **Azure Explorer** (Explorador de Azure).

Si la vista **Azure Explorer** (Explorador de Azure) no está abierta aún, puede abrirla haciendo clic en el botón **Ver** en IntelliJ, en **Ventanas de herramientas** y, después, en **Service Explorer** (Explorador de servicios). Si anteriormente no ha iniciado sesión, se le pedirá que lo haga.

Cuando se muestre la vista **Azure Explorer** (Explorador de Azure), siga estos pasos para iniciar o detener su aplicación web:

1. Expanda el nodo **Azure**.

1. Expanda el nodo **Aplicaciones web**.

1. Haga clic en la aplicación web deseada.

1. Cuando aparezca el menú contextual, haga clic en **Iniciar** o en **Detener**. Tenga en cuenta que las opciones de menú dependen del contexto, por lo que solo podrá detener una aplicación web que se encuentre en ejecución o iniciar una aplicación web que no se esté ejecutando en ese momento.

    ![][18]

## Pasos siguientes

Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - [Instalación del Kit de herramientas de Azure para Eclipse]
  - [Creación de una aplicación web Hello World para Azure en Eclipse]
  - [Novedades del kit de herramientas de Azure para Eclipse]
- [Kit de herramientas de Azure para IntelliJ]
  - [Instalación del kit de herramientas de Azure para IntelliJ]
  - *Creación de una aplicación web Hello World para Azure en IntelliJ (este artículo)*
  - [Novedades del kit de herramientas de Azure para IntelliJ]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

Para obtener más información sobre cómo crear aplicaciones web de Azure, consulte [Introducción a Aplicaciones web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Installing the Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novedades del kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a Aplicaciones web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-No-SDK-Specified.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

<!---HONumber=AcomDC_0817_2016-->