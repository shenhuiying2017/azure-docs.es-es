---
title: "Creación de una aplicación web básica de Azure en IntelliJ | Microsoft Docs"
description: "En este tutorial se muestra cómo utilizar el Kit de herramientas de Azure para IntelliJ para crear una aplicación web Hello World para Azure."
services: app-service\web
documentationcenter: java
author: selvasingh
manager: erikre
editor: 
ms.assetid: 75ce7b36-e3ae-491d-8305-4b42ce37db4e
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 04/25/2017
ms.author: robmcm
ms.translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: 756c9fa079782f8f0cb194ca07c637d083f9ebd7
ms.contentlocale: es-es
ms.lasthandoff: 03/15/2017

---
# <a name="create-a-basic-azure-web-app-in-intellij"></a>Creación de una aplicación web básica de Azure en IntelliJ
En este tutorial se muestra cómo crear e implementar una aplicación básica Hello World en Azure como una aplicación web mediante el [kit de herramientas de Azure para IntelliJ]. Para que sea más sencillo, se muestra un ejemplo básico de JSP, pero los pasos similares se aplicarían en el caso de un servlet Java en lo que respecta a la implementación de Azure.

Cuando haya completado este tutorial, la aplicación se parecerá a la que se muestra en la siguiente ilustración al verla en un explorador web:

![Página web de ejemplo][01]

## <a name="prerequisites"></a>Requisitos previos
* Un kit para desarrolladores de Java (JDK) de la versión 1.8 o superior.
* IntelliJ IDEA Ultimate Edition. Se puede descargar desde <https://www.jetbrains.com/idea/download/index.html>.
* Una distribución de un servidor web basado en Java o un servidor de aplicaciones, como [Apache Tomcat] o [Jetty].
* Una suscripción a Azure, que se puede adquirir en <https://azure.microsoft.com/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El [kit de herramientas de Azure para IntelliJ]. Para obtener información acerca de la instalación del kit de herramientas de Azure, consulte [Instalación del kit de herramientas de Azure para IntelliJ].

## <a name="to-create-a-hello-world-application"></a>Para crear una aplicación Hola a todos
En primer lugar, empezaremos con la creación de un proyecto Java.

1. Inicie IntelliJ y haga clic en **File** (Archivo), **New** (Nuevo) y, finalmente, en **Project** (Proyecto).
   
    ![Archivo Nuevo Proyecto][02]
2. En el cuadro de diálogo New Project (Nuevo proyecto), seleccione **Java** y **Web Application** (Aplicación web) y, después, haga clic en **New** (Nuevo) para agregar un SDK del proyecto.
   
    ![Cuadro de diálogo Nuevo proyecto][03a]
   
3. En el cuadro de diálogo Select Home Directory for JDK (Seleccionar directorio de inicio para JDK), seleccione la carpeta donde está instalado el JDK y, después, haga clic en **Aceptar**. Haga clic en **Siguiente** en el cuadro de diálogo de nuevo proyecto para continuar.
   
    ![Especificación del directorio de inicio de JDK][03b]
4. Para este tutorial, asigne al proyecto el nombre **Java-Web-App-On-Azure** y haga clic en **Finish** (Finalizar).
   
    ![Cuadro de diálogo Nuevo proyecto][04]
5. En la vista del explorador de proyectos de IntelliJ, expanda **Java-Web-App-On-Azure**, después expanda **web** y, luego, haga doble clic en **index.jsp**.
   
    ![Abrir página de índice][05c]
6. Cuando el archivo index.jsp se abra en IntelliJ, agregue texto para que se muestre dinámicamente **Hello World!** dentro del elemento `<body>` existente. El contenido de `<body>` actualizado debe parecerse al siguiente ejemplo:
   
    `<body><b><% out.println("Hello World!"); %></b></body>` 
7. Guarde el archivo index.jsp.

## <a name="to-deploy-your-application-to-an-azure-web-app-container"></a>Para implementar la aplicación en un contenedor de aplicaciones web de Azure
Existen varias maneras de implementar una aplicación web de Java en Azure. En este tutorial se describe una de las más sencillas: la aplicación se implementará en un contenedor de aplicaciones de Azure: no se necesita ningún tipo de proyecto especial ni herramientas adicionales. Azure facilitará el JDK y el software de contenedor web, así que no es necesario que cargue los suyos; lo único que necesita es su aplicación web de Java. Como resultado, el proceso de publicación de su aplicación tardará unos segundos y no minutos.

Antes de publicar la aplicación, primero debe configurar el módulo. Para ello, siga estos pasos:

1. En el Explorador de proyectos de IntelliJ, haga clic con el botón derecho en el proyecto **Java-Web-App-On-Azure** . Cuando aparezca el menú contextual, haga clic en **Open Module Settings** (Abrir configuración del módulo).

    ![Abrir configuración del módulo][05a]
2. Cuando se muestre el cuadro de diálogo Project Structure (Estructura de proyecto):

   a. Haga clic en **Artefactos** en la lista de **configuración del proyecto**.
   b. Cambie el nombre del artefacto en el cuadro **Nombre** para que contenga un espacio en blanco o caracteres especiales; esto es necesario porque el nombre se usará en el identificador uniforme de recursos (URI).
   c. Cambiar el **tipo** a **Aplicación Web: archivo**.
   d. Haga clic en **OK** (Aceptar) para cerrar el cuadro de diálogo Project Structure (Estructura del proyecto).

    ![Abrir configuración del módulo][05b]

Cuando se ha configurado el módulo, puede publicar la aplicación en Azure mediante los pasos siguientes:

1. En el Explorador de proyectos de IntelliJ, haga clic con el botón derecho en el proyecto **Java-Web-App-On-Azure** . Cuando aparezca el menú contextual, seleccione **Azure** y, después, haga clic en **Publish as Azure Web App...** (Publicar como aplicación web de Azure)
   
    ![Menú contextual de publicación de Azure][06]
2. Si aún no ha iniciado sesión en Azure desde IntelliJ, se le pedirá que lo haga. (Si tiene varias cuentas de Azure, puede que algunos de los avisos que aparecen durante el proceso de inicio de sesión se muestren más de una vez, incluso si parecen ser iguales. Cuando esto ocurra, siga la instrucciones de inicio de sesión).
   
    ![Cuadro de diálogo de inicio de sesión de Azure][07]
3. Cuando haya iniciado sesión correctamente en su cuenta de Azure, el cuadro de diálogo **Manage Subscriptions** (Administrar suscripciones) mostrará una lista de suscripciones que están asociadas con sus credenciales. Si aparecen varias suscripciones y quiere trabajar solo con un subconjunto específico de ellas, opcionalmente puede desactivar las que no utilice. Cuando haya seleccionado las suscripciones, haga clic en **Cerrar**.
   
    ![Administrar suscripciones][08]
4. Cuando aparezca el cuadro de diálogo **Deploy to Azure Web App Container** (Implementar en el contenedor de aplicaciones web de Azure), se mostrarán los contenedores de aplicaciones web creados anteriormente; si no ha creado ningún contenedor, la lista estará vacía.
   
    ![Contenedores de aplicaciones][09]
5. Si no ha creado un contenedor de aplicaciones web de Azure antes, o si quiere publicar la aplicación en un nuevo contenedor, siga estos pasos. De lo contrario, seleccione un contenedor de aplicaciones web existente y vaya al paso 6 que encontrará a continuación.
   
   1. Haga clic en **+**
      
       ![Agregar contenedor de aplicaciones][10]
   2. Se mostrará el cuadro de diálogo **New Web App Container** (Nuevo contenedor de aplicaciones web), que se usará para los siguientes pasos.
      
       ![Nuevo contenedor de aplicaciones][11a]
   3. Escriba un valor para **Etiqueta DNS** para el contenedor de aplicaciones web; esta será la etiqueta DNS hoja de la dirección URL del host de la aplicación web en Azure. Tenga en cuenta que el nombre debe estar disponible y se ajusta a los requisitos de nomenclatura de las aplicaciones web de Azure.
   4. En el menú desplegable **Web Container** (Contenedor web), seleccione el software adecuado para su aplicación.
      
       Actualmente, puede elegir entre Tomcat 8, Tomcat 7 o Jetty 9. Azure proporcionará una distribución reciente del software seleccionado y se ejecutará en una distribución reciente de JDK 8 creada por Oracle y proporcionada por Azure.
   5. En el menú desplegable **Suscripción** , seleccione la suscripción que quiere usar para esta implementación.
   6. En el menú desplegable **Grupo de recursos** , seleccione el grupo de recursos con el que desea asociar la aplicación web. Los grupos de recursos de Azure permiten agrupar recursos relacionados para que, por ejemplo, se puedan eliminar juntos.
      
       Puede seleccionar un grupo de recursos existente (si tiene alguno) y pasar al paso g a continuación o usar los siguientes pasos para crear un nuevo grupo de recursos:
      
      * Seleccione **&lt;&lt; Crear nuevo grupo de recursos&gt;&gt;** en el menú desplegable **Grupo de recursos**.
      * Se muestra el cuadro de diálogo **Nuevo grupo de recursos** :
        
          ![Nuevo grupo de recursos][12]
      * En el cuadro de texto **Nombre** , especifique un nombre para el nuevo grupo de recursos.
      * En el menú desplegable **Región** , seleccione la ubicación adecuada del centro de datos de Azure para su grupo de recursos.
      * Haga clic en **Aceptar**.
   7. En el menú desplegable **Plan del servicio de aplicaciones** se muestran los planes del servicio de aplicaciones que están asociados con el grupo de recursos seleccionado. (Un plan de App Service especifica información como la ubicación de la aplicación web, el plan de tarifa y el tamaño de la instancia de proceso. Un único plan de App Service se puede usar para varias aplicaciones web, motivo por el cual se mantiene independiente de una implementación específica de aplicaciones web.
      
       Puede seleccionar un plan de plan de App Service existente (si tiene alguno) y pasar al paso h a continuación o usar los siguientes pasos para crear un nuevo plan:
      
      * Seleccione **&lt;&lt; Crear nuevo plan de App Service&gt;&gt;** en el menú desplegable **Plan de App Service**.
      * Se muestra el cuadro de diálogo **Nuevo plan del servicio de aplicaciones** :
        
          ![Nuevo plan de App Service][13]
      * En el cuadro de texto **Nombre** , especifique un nombre para el nuevo plan.
      * En el menú desplegable **Ubicación** , seleccione la ubicación adecuada del centro de datos de Azure para el plan.
      * En el menú desplegable **Plan de tarifa** , seleccione la tarifa adecuada para el plan. Con fines de prueba, puede elegir **Gratis**.
      * En el menú desplegable **Tamaño de instancia** , seleccione el tamaño de instancia adecuado para el plan. Con fines de prueba, puede elegir **Pequeño**.
      * Haga clic en **Aceptar**.
   8. (Opcional) De manera predeterminada, Azure implementará automáticamente una distribución reciente de Java 8 como máquina virtual de Java en el contenedor de la aplicación. Sin embargo, puede seleccionar una versión y una distribución diferentes de la máquina virtual de Java. Para ello, siga estos pasos:
      
      * Haga clic en la pestaña **JDK** en el cuadro de diálogo **New Web App Container** (Nuevo contenedor de aplicaciones web).
      * Puede elegir una de las siguientes opciones:
        
        * Implementar el JDK predeterminado que ofrece Azure
        * Implementar un JDK de terceros de lista desplegable de JDK adicionales que están disponibles en Azure
        * Implementar un JDK personalizado, que debe estar empaquetado en un archivo ZIP y bien estar disponible públicamente o en su cuenta de Azure Storage
        
        ![Pestaña JDK de nuevo contenedor de aplicaciones][11b]
   9. Cuando haya completado todos los pasos anteriores, el cuadro de diálogo Nuevo contenedor de aplicaciones web debe parecerse al de la siguiente ilustración:
      
       ![Nuevo contenedor de aplicaciones][14]
   10. Haga clic en **Aceptar** para finalizar la creación de su nuevo contenedor de aplicaciones web.
       
        Espere unos segundos para que se actualice la lista de los contenedores de aplicaciones web y el contenedor recién creado ya debería estar seleccionado en la lista.
6. Ya puede completar la implementación inicial de su aplicación web en Azure; haga clic en **Aceptar** para implementar la aplicación de Java en el contenedor de aplicación web seleccionado. De forma predeterminada, la aplicación se implementará como un subdirectorio del servidor de aplicaciones. Si desea que se implemente como aplicación raíz, active la casilla **Deploy to root** (Implementar en raíz) antes de hacer clic en **OK** (Aceptar).
   
    ![Implementar en Azure][15]
7. A continuación, debería mostrarse la vista **Azure Activity Log** (Registro de actividad de Azure), que indicará el estado de implementación de la aplicación web.
   
    ![Indicador de progreso][16]
   
    El proceso de implementación de la aplicación web en Azure debería tardar solo unos segundos en completarse. Cuando su aplicación esté lista, verá un vínculo denominado **Publicado** in the **Status** (Estado). Al hacer clic en el vínculo, se le llevará a la página principal de la aplicación web implementada. Por otro lado, puede utilizar los pasos de la sección siguiente para ir a la aplicación web.

## <a name="browsing-to-your-web-app-on-azure"></a>Desplazamiento hasta su aplicación web en Azure
Para ir a su aplicación web en Azure, puede usar la vista **Azure Explorer**.

Si la vista del **Explorador de Azure** no está abierta, puede abrirla haciendo clic en el menú **View** (Ver) de IntelliJ, luego haga clic en **Tool Windows** (Ventanas de herramientas) y, después, en **Service Explorer** (Explorador de servicios). Si anteriormente no ha iniciado sesión, se le pedirá que lo haga.

Cuando se muestre la vista **Azure Explorer**, siga estos pasos para examinar su aplicación web: 

1. Expanda el nodo **Azure** .
2. Expanda el nodo **Aplicaciones web** . 
3. Haga clic en la aplicación web deseada.
4. Cuando aparezca el menú contextual, haga clic en **Abrir en el explorador**.
   
    ![Examinar una aplicación web][17]

## <a name="updating-your-web-app"></a>Actualización de la aplicación web
Actualizar una aplicación web de Azure existente es un proceso rápido y sencillo, y tiene dos opciones para ello:

* Puede actualizar la implementación de una aplicación web de Java existente.
* Puede publicar una aplicación Java adicional en el mismo contenedor de aplicaciones web.

En cualquier caso, el proceso es idéntico y tarda unos pocos segundos:

1. En el Explorador de proyectos de IntelliJ, haga clic con el botón derecho en la aplicación Java que quiere actualizar o agregar a un contenedor de aplicaciones web existente.
2. Cuando aparezca el menú contextual, seleccione **Azure** y, después, haga clic en **Publish as Azure Web App...** (Publicar como aplicación web de Azure).
3. Puesto que ya ha iniciado sesión anteriormente, verá una lista de contenedores de aplicaciones web existentes. Seleccione aquel en el que quiere publicar o volver a publicar la aplicación Java y haga clic en **Aceptar**.

Unos segundos más tarde, la vista **Azure Activity Log** (Registro de actividad de Azure) mostrará la implementación actualizada como **Published** (Publicado) y podrá comprobar la aplicación actualizada en un explorador web.

## <a name="starting-stopping-or-restarting-an-existing-web-app"></a>Inicio, detención o reinicio de una aplicación web existente
Para iniciar o detener un contenedor de aplicaciones web existente (junto con todas las aplicaciones Java implementadas en él), puede utilizar la vista **Azure Explorer** (Explorador de Azure).

Si la vista del **Explorador de Azure** no está abierta, puede abrirla haciendo clic en el menú **View** (Ver) de IntelliJ, luego haga clic en **Tool Windows** (Ventanas de herramientas) y, después, en **Service Explorer** (Explorador de servicios). Si anteriormente no ha iniciado sesión, se le pedirá que lo haga.

Cuando se muestre la vista **Azure Explorer** (Explorador de Azure), siga estos pasos para iniciar o detener su aplicación web: 

1. Expanda el nodo **Azure** .
2. Expanda el nodo **Aplicaciones web** . 
3. Haga clic en la aplicación web deseada.
4. Cuando aparezca el menú contextual, haga clic en **Iniciar**, **Detener** o **Reiniciar**. Tenga en cuenta que las opciones de menú dependen del contexto, por lo que solo podrá detener una aplicación web que se encuentre en ejecución o iniciar una aplicación web que no se esté ejecutando en ese momento.
   
    ![Detener aplicación web][18]

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de los kits de herramientas de Azure para los IDE de Java, vea los siguientes vínculos:

* [Kit de herramientas de Azure para Eclipse]
  * [Instalación del Kit de herramientas de Azure para Eclipse]
  * [Creación de una aplicación web Hello World para Azure en Eclipse]
  * [Novedades del kit de herramientas de Azure para Eclipse]
* [kit de herramientas de Azure para IntelliJ]
  * [Instalación del kit de herramientas de Azure para IntelliJ]
  * *Creación de una aplicación web Hello World para Azure en IntelliJ (este artículo)*
  * [Novedades del kit de herramientas de Azure para IntelliJ]

<a name="see-also"></a>

## <a name="see-also"></a>Otras referencias
Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

Para obtener más información sobre cómo crear aplicaciones web de Azure, consulte [Introducción a Aplicaciones web].

[!INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse.md
[kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Creación de una aplicación web Hello World para Azure en Eclipse]: ./app-service-web-eclipse-create-hello-world-web-app.md
[Create a Hello World Web App for Azure in IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[Instalación del Kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-installation.md
[Instalación del kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Novedades del kit de herramientas de Azure para Eclipse]: ../azure-toolkit-for-eclipse-whats-new.md
[Novedades del kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij-whats-new.md

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a Aplicaciones web]: ./app-service-web-overview.md
[Apache Tomcat]: http://tomcat.apache.org/
[Jetty]: http://www.eclipse.org/jetty/

<!-- IMG List -->

[01]: ./media/app-service-web-intellij-create-hello-world-web-app/01-Web-Page.png
[02]: ./media/app-service-web-intellij-create-hello-world-web-app/02-File-New-Project.png
[03a]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-Dialog.png
[03b]: ./media/app-service-web-intellij-create-hello-world-web-app/03-New-Project-SDK-Dialog.png
[04]: ./media/app-service-web-intellij-create-hello-world-web-app/04-New-Project-Dialog.png
[05a]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Module-Settings.png
[05b]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Project-Structure-Dialog.png
[05c]: ./media/app-service-web-intellij-create-hello-world-web-app/05-Open-Index-Page.png
[06]: ./media/app-service-web-intellij-create-hello-world-web-app/06-Azure-Publish-Context-Menu.png
[07]: ./media/app-service-web-intellij-create-hello-world-web-app/07-Azure-Log-In-Dialog.png
[08]: ./media/app-service-web-intellij-create-hello-world-web-app/08-Manage-Subscriptions.png
[09]: ./media/app-service-web-intellij-create-hello-world-web-app/09-App-Containers.png
[10]: ./media/app-service-web-intellij-create-hello-world-web-app/10-Add-App-Container.png
[11a]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container.png
[11b]: ./media/app-service-web-intellij-create-hello-world-web-app/11-New-App-Container-JDK-Tab.png
[12]: ./media/app-service-web-intellij-create-hello-world-web-app/12-New-Resource-Group.png
[13]: ./media/app-service-web-intellij-create-hello-world-web-app/13-New-App-Service-Plan.png
[14]: ./media/app-service-web-intellij-create-hello-world-web-app/14-New-App-Container.png
[15]: ./media/app-service-web-intellij-create-hello-world-web-app/15-Deploy-To-Azure.png
[16]: ./media/app-service-web-intellij-create-hello-world-web-app/16-Progress-Indicator.png
[17]: ./media/app-service-web-intellij-create-hello-world-web-app/17-Browse-Web-App.png
[18]: ./media/app-service-web-intellij-create-hello-world-web-app/18-Stop-Web-App.png

