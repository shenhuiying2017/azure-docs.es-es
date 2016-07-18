<properties 
	pageTitle="Creación de una aplicación web Hello World para Azure en Eclipse | Microsoft Azure" 
	description="En este tutorial se muestra cómo utilizar el Kit de herramientas de Azure para Eclipse para crear una aplicación web Hello World para Azure." 
	services="app-service\web" 
	documentationCenter="java" 
	authors="rmcmurray" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="06/24/2016" 
	ms.author="robmcm"/>

# Creación de una aplicación web Hello World para Azure en Eclipse

En este tutorial se muestra cómo crear e implementar una aplicación básica Hello World en Azure como una aplicación web mediante el [Kit de herramientas de Azure para Eclipse]. Para que sea más sencillo, se muestra un ejemplo básico de JSP, pero los mismos pasos se aplicarían en el caso de un servlet Java en lo que respecta a la implementación de Azure.

Cuando haya completado este tutorial, la aplicación se parecerá a la que se muestra en la siguiente ilustración al verla en un explorador web:

![][01]
 
## Requisitos previos

* Un kit para desarrolladores de Java (JDK) de la versión 1.8 o superior.
* IDE de Eclipse para Java EE Developers, Luna o superior. Esto se puede descargar en <http://www.eclipse.org/downloads/>.
* Una distribución de un servidor web basado en Java o un servidor de aplicaciones, como Apache Tomcat o Jetty.
* Una suscripción de Azure, que se puede adquirir en <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El kit de herramientas de Azure para Eclipse. Para más información, consulte [Instalación del kit de herramientas de Azure para Eclipse].

## Para crear una aplicación Hello World, siga estos pasos:

En primer lugar, empezaremos con la creación de un proyecto Java.

1. Inicie Eclipse y, en el menú, haga clic en **Archivo**, **Nuevo** y **Dynamic Web Project**. (Si ve que **Dynamic Web Project** no aparece como proyecto disponible después de hacer clic en **File** (Archivo) y **New** (Nuevo), entonces lleve a cabo los siguientes pasos: haga clic en **File** (Archivo), **New** (Nuevo), **Project...** (Proyecto...), expanda **Web**, haga clic en **Dynamic Web Project** y luego haga clic en **Next** (Siguiente)).

1. Para los fines de este tutorial, asigne el nombre **MyHelloWorld** al proyecto. La pantalla se parecerá a la siguiente:

   ![][02]

1. Haga clic en **Finalizar**

1. En la vista del explorador de proyectos de Eclipse, expanda **MyHelloWorld**. Haga clic con el botón secundario en **WebContent**, luego haga clic en **Nuevo** y, a continuación, en **JSP File**.

1. En el cuadro de diálogo **New JSP File** (Nuevo archivo JSP), denomine al archivo **index.jsp**, mantenga la carpeta principal como **MyHelloWorld/WebContent** y, después, haga clic en **Next** (Siguiente).

1. Para los fines de este tutorial, en el cuadro de diálogo **Select JSP Template** (Seleccionar plantilla JSP), seleccione **New JSP File (html)** (Nuevo archivo JSP (html)) y haga clic en **Finalizar**.

1. Cuando el archivo index.jsp se abra en Eclipse, agregue texto para que se muestre dinámicamente **Hello World!** dentro del elemento `<body>` existente. El contenido de `<body>` actualizado debe parecerse al siguiente ejemplo:

   `<body><b><% out.println("Hello World!"); %></b></body>`

1. Guarde el archivo index.jsp.

## Para implementar la aplicación en un contenedor de aplicaciones web de Azure, siga estos pasos:

Existen varias maneras de implementar una aplicación web de Java en Azure. En este tutorial se describe una de las más sencillas: la aplicación se implementará en un contenedor de aplicaciones de Azure: no se necesita ningún tipo de proyecto especial ni herramientas adicionales. Azure facilitará el JDK y el software de contenedor web, así que no es necesario que cargue los suyos; lo único que necesita es su aplicación web de Java. Como resultado, el proceso de publicación de su aplicación tardará unos segundos y no minutos.

1. En el Explorador de proyectos de Eclipse, haga clic con el botón derecho en **MyHelloWorld**.

1. En el menú contextual, seleccione **Azure** y luego haga clic en **Publish as Azure Web App** (Publicar como aplicación web de Azure).

   ![][03]
   
   También, mientras está seleccionado el proyecto de aplicación web en el Explorador de proyectos, puede hacer clic en el botón desplegable **Publish** (Publicar) de la barra de herramientas y seleccionar el botón **Publish as Azure Web App** (Publicar como aplicación web de Azure) desde allí:
   
   ![][14]
   
1. Si aún no ha iniciado sesión en Azure desde Eclipse, se le pedirá que inicie sesión en su cuenta de Azure:

   ![][04]
   
   Nota: Si tiene varias cuentas de Azure, puede que algunos de los avisos que aparecen durante el proceso de inicio de sesión se muestren más de una vez, incluso si parecen ser iguales. Cuando esto ocurra, siga la instrucciones de inicio de sesión.
1. Cuando haya iniciado sesión correctamente en su cuenta de Azure, el cuadro de diálogo **Manage Subscriptions** (Administrar suscripciones) mostrará una lista de suscripciones que están asociados con sus credenciales. Si aparecen varias suscripciones y quiere trabajar solo con un subconjunto específico de ellas, opcionalmente puede desactivar las que no utilice. Cuando haya seleccionado las suscripciones, haga clic en **Close** (Cerrar).

   ![][05]
   
1. Cuando aparezca el cuadro de diálogo **Deploy to Azure Web App Container** (Implementar en el contenedor de aplicaciones web de Azure), se mostrarán los contenedores de aplicaciones web creados anteriormente; si no ha creado ningún contenedor, la lista estará vacía.

   ![][06]
   
1. Si no ha creado un contenedor de aplicaciones web de Azure antes, o si quiere publicar la aplicación en un nuevo contenedor, siga estos pasos. De lo contrario, seleccione un contenedor de aplicaciones web existente y vaya al paso 7 a continuación.

  1. Haga clic en **Nuevo**.

        ![][15]

  1. Se muestra el cuadro de diálogo **New Web App Container** (Nuevo contenedor de aplicaciones web):

        ![][07]

  1. Escriba un valor para **Etiqueta DNS** para el contenedor de aplicaciones web; esta será la etiqueta DNS hoja de la dirección URL del host de la aplicación web en Azure. Nota: El nombre debe estar disponible y ajustarse a los requisitos de nomenclatura de las aplicaciones web de Azure.

  1. En el menú desplegable **Web Container** (Contenedor web), seleccione el software adecuado para su aplicación.

        Actualmente, puede elegir entre Tomcat 8, Tomcat 7 o Jetty 9. Azure proporcionará una distribución reciente del software seleccionado y se ejecutará en una distribución reciente de JDK 8 creada por Oracle y proporcionada por Azure.

  1. En el menú desplegable **Suscripción**, seleccione la suscripción que quiere usar para esta implementación.

  1. En el menú desplegable **Grupo de recursos**, seleccione el grupo de recursos con el que desea asociar la aplicación web.

        Nota: Los grupos de recursos de Azure permiten agrupar recursos relacionados para que, por ejemplo, se puedan eliminar juntos.

        Puede seleccionar un grupo de recursos existente (si tiene alguno) y pasar al paso g a continuación o usar los siguientes pasos para crear un nuevo grupo de recursos:

      * Haga clic en **Nuevo**.

      * Se muestra el cuadro de diálogo **Nuevo grupo de recursos**:

            ![][08]

      * En el cuadro de texto **Nombre**, especifique un nombre para el nuevo grupo de recursos.

      * En el menú desplegable **Región**, seleccione la ubicación adecuada del centro de datos de Azure para su grupo de recursos.

      * Haga clic en **Aceptar**.

  1. En el menú desplegable **Plan del servicio de aplicaciones** se muestran los planes del servicio de aplicaciones que están asociados con el grupo de recursos seleccionado.

        Nota: un plan de servicio de aplicaciones especifica información como la ubicación de la aplicación web, el plan de tarifas y el tamaño de la instancia de proceso. Un único plan de servicio de aplicaciones se puede usar para varias aplicaciones web, motivo por el cual se mantiene independiente de una implementación específica de aplicaciones web.

        Puede seleccionar un plan de servicio de aplicaciones existente (si tiene alguno) y pasar al paso h a continuación o usar los siguientes pasos para crear un nuevo plan:

      * Haga clic en **Nuevo**.

      * Se muestra el cuadro de diálogo **Nuevo plan del servicio de aplicaciones**:

            ![][09]

      * En el cuadro de texto **Nombre**, especifique un nombre para el nuevo plan del Servicio de aplicaciones.

      * En el menú desplegable **Ubicación**, seleccione la ubicación adecuada del centro de datos de Azure para el plan.

      * En el menú desplegable **Plan de tarifa**, seleccione la tarifa adecuada para el plan. Con fines de prueba, puede elegir **Gratis**.

      * En el menú desplegable **Tamaño de instancia**, seleccione el tamaño de instancia adecuado para el plan. Con fines de prueba, puede elegir **Pequeño**.

  1. Cuando haya completado todos los pasos anteriores, el cuadro de diálogo Nuevo contenedor de aplicaciones web debe parecerse al de la siguiente ilustración:

        ![][10]

  1. Haga clic en **Aceptar** para finalizar la creación de su nuevo contenedor de aplicaciones web.

        Espere unos segundos para que se actualice la lista de los contenedores de aplicaciones web y el contenedor recién creado ya debería estar seleccionado en la lista.

1. Ahora está listo para terminar la implementación inicial de la aplicación web en Azure:

    ![][11]

    Haga clic en **Aceptar** para implementar su aplicación Java en el contenedor de aplicaciones web seleccionado.

    Nota: De forma predeterminada, la aplicación se implementará como un subdirectorio del servidor de aplicaciones. Si desea que se implemente como la aplicación raíz, active la casilla **Deploy to root** (Implementar en raíz) antes de hacer clic en **Aceptar**.

1. Después, debería mostrarse la vista **Azure Activity Log** (Registro de actividad de Azure), que indicará el estado de implementación de la aplicación web.

    ![][12]

    El proceso de implementación de la aplicación web en Azure debería tardar solo unos segundos en completarse. Cuando su aplicación esté lista, verá un vínculo denominado **Published** (Publicada) en la columna **Status** (Estado). Al hacer clic en el vínculo, irá a la página principal de la aplicación web implementada.

## Actualización de la aplicación web

Actualizar una aplicación web de Azure existente es un proceso rápido y sencillo, y tiene dos opciones para ello:

* Puede actualizar la implementación de una aplicación web de Java existente.
* Puede publicar una aplicación Java adicional en el mismo contenedor de aplicaciones web.

En cualquier caso, el proceso es idéntico y tarda unos pocos segundos:

1. En el Explorador de proyectos de Eclipse, haga clic con el botón derecho en la aplicación Java que quiere actualizar o agregar a un contenedor de aplicaciones web existente.

2. Cuando aparezca el menú contextual, seleccione **Azure** y, después, haga clic en **Publish as Azure Web App...** (Publicar como aplicación web de Azure).

3. Puesto que ya ha iniciado sesión anteriormente, verá una lista de contenedores de aplicaciones web existentes. Seleccione aquel en el que quiere publicar o volver a publicar la aplicación Java y haga clic en **Aceptar**.

Unos segundos más tarde, la vista **Azure Activity Log** (Registro de actividad de Azure) mostrará la implementación actualizada como **Publicado** y podrá comprobar la aplicación actualizada en un explorador web.

## Detención de una aplicación web existente

Para detener un contenedor de aplicaciones web de Azure existente (junto con todas las aplicaciones Java implementadas en él), puede utilizar la vista **Azure Explorer** (Explorador de Azure).

Si la vista **Azure Explorer** (Explorador de Azure) no está ya abierta, puede abrirla; para ello, haga clic en el menú **Window** (Ventana) en Eclipse, haga clic en **Show View** (Mostrar vista), **Other** (Otro), **Azure** y finalmente en **Azure Explorer** (Explorador de Azure). Si anteriormente no ha iniciado sesión, se le pedirá que lo haga.

Cuando se muestre la vista **Azure Explorer** (Explorador de Azure), siga estos pasos para detener su aplicación web:

1. Expanda el nodo **Azure**.
2. Expanda el nodo **Aplicaciones web**. 
3. Haga clic en la aplicación web deseada.
5. Cuando aparezca el menú contextual, haga clic en **Detener**. ![][13]

## Pasos siguientes

Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

- [Kit de herramientas de Azure para Eclipse]
  - [Instalación del Kit de herramientas de Azure para Eclipse]
  - *Creación de una aplicación web Hello World para Azure en Eclipse (este artículo)*
  - [Novedades del kit de herramientas de Azure para Eclipse]
- [Kit de herramientas de Azure para IntelliJ]
  - [Instalación del kit de herramientas de Azure para IntelliJ]
  - [Creación de una aplicación web Hello World para Azure en IntelliJ]
  - [Novedades del kit de herramientas de Azure para IntelliJ]

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

Para obtener más información sobre cómo crear aplicaciones web de Azure, consulte [Introducción a Aplicaciones web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[Kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Create a Hello World Web App for Azure in Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Instalación del kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novedades del kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a Aplicaciones web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-eclipse-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-eclipse-create-hello-world-web-app/02-Dynamic-Web-Project.png
[03]: ./media/app-service-web-eclipse-create-hello-world-web-app/03-Context-Menu.png
[04]: ./media/app-service-web-eclipse-create-hello-world-web-app/04-Log-In-Dialog.png
[05]: ./media/app-service-web-eclipse-create-hello-world-web-app/05-Manage-Subscriptions-Dialog.png
[06]: ./media/app-service-web-eclipse-create-hello-world-web-app/06-Deploy-To-Azure-Web-Container.png
[07]: ./media/app-service-web-eclipse-create-hello-world-web-app/07-New-Web-App-Container-Dialog.png
[08]: ./media/app-service-web-eclipse-create-hello-world-web-app/08-New-Resource-Group-Dialog.png
[09]: ./media/app-service-web-eclipse-create-hello-world-web-app/09-New-Service-Plan-Dialog.png
[10]: ./media/app-service-web-eclipse-create-hello-world-web-app/10-Completed-Web-App-Container-Dialog.png
[11]: ./media/app-service-web-eclipse-create-hello-world-web-app/11-Completed-Deploy-Dialog.png
[12]: ./media/app-service-web-eclipse-create-hello-world-web-app/12-Activity-Log-View.png
[13]: ./media/app-service-web-eclipse-create-hello-world-web-app/13-Azure-Explorer-Web-App.png
[14]: ./media/app-service-web-eclipse-create-hello-world-web-app/14-publishDropdownButton.png
[15]: ./media/app-service-web-eclipse-create-hello-world-web-app/15-New-Azure-Web-Container.png

<!---HONumber=AcomDC_0706_2016-->