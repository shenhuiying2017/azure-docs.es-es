<properties 
	pageTitle="Depuración de una aplicación web de Java en Azure mediante IntelliJ | Microsoft Azure" 
	description="En este tutorial se muestra cómo utilizar el kit de herramientas de Azure para IntelliJ con el objetivo de crear una aplicación web de Java que se ejecute en Azure." 
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
	ms.date="06/27/2016" 
	ms.author="asirveda;robmcm"/>

# Depuración de una aplicación web de Java en Azure mediante IntelliJ

En este tutorial se muestra cómo depurar una aplicación web de Java que se ejecuta en Azure mediante el [kit de herramientas de Azure para IntelliJ]. Por simplicidad, en este tutorial se utilizará un ejemplo básico de Java Server Page (JSP), pero los pasos serían similares para depurar un servlet de Java en Azure.

Cuando haya terminado este tutorial, la aplicación se parecerá a la que se muestra en la siguiente ilustración al depurarla en IntelliJ:

![][01]
 
## Requisitos previos

* Un kit para desarrolladores de Java (JDK) de la versión 1.8 o superior.
* IntelliJ IDEA Ultimate Edition. Esto se puede descargar en <https://www.jetbrains.com/idea/download/index.html>.
* Una distribución de un servidor web basado en Java o un servidor de aplicaciones, como Apache Tomcat o Jetty.
* Una suscripción de Azure, que se puede adquirir en <https://azure.microsoft.com/es-ES/free/> o <http://azure.microsoft.com/pricing/purchase-options/>.
* El kit de herramientas de Azure para IntelliJ. Para obtener más información, consulte [Installing the Azure Toolkit for IntelliJ] \(Instalación del kit de herramientas de Azure para IntelliJ).
* Un proyecto web dinámico creado e implementado en el Servicio de aplicaciones de Azure; por ejemplo, consulte [Creación de una aplicación web Hello World para Azure en IntelliJ].

## Pasos para depurar una aplicación web de Java en Azure

Para completar los pasos de esta sección, puede usar un proyecto web dinámico existente que ya haya implementado como una aplicación web de Java en Azure, descargar un [proyecto web dinámico de ejemplo] y seguir los pasos de [Creación de una aplicación web Hello World para Azure en IntelliJ] para implementarla en Azure.

1. Abra el proyecto de aplicación web de Java que implementó en Azure mediante IntelliJ.

1. Haga clic en el menú **Run** (Ejecutar) y, después, en **Edit Configurations...** (Editar configuraciones).

    ![][02]

1. Cuando se abra el cuadro de diálogo **Run/Debug Configurations** (Ejecutar/depurar configuraciones), siga estos pasos:

    1. Seleccione **Azure Web App** (Aplicación web de Azure).
    1. Haga clic en **+** para agregar una nueva configuración.
    1. Proporcione un **nombre** a la configuración.
    1. Acepte los valores predeterminados restantes que sugiere el kit de herramientas de Azure y, después, haga clic en **OK** (Aceptar).

        ![][03]

1. Seleccione la configuración de depuración de la aplicación web de Azure que acaba de crear en la parte superior derecha del menú y haga clic en **Debug** (Depurar).

    ![][04]

1. Cuando se le pregunte si quiere **habilitar ahora la depuración remota en la aplicación web remota**, haga clic en **OK** (Aceptar).

1. Cuando se indique que **la aplicación web ya está lista para la depuración remota**, haga clic en **OK** (Aceptar).

    ![][05]

1. Seleccione la configuración de depuración de la aplicación web de Azure que acaba de crear en la parte superior derecha del menú y, después, haga clic en **Debug** (Depurar).

1. Un símbolo del sistema de Windows o el shell de Unix abrirán y prepararán la conexión necesaria para realizar la depuración. Antes de continuar, debe esperar hasta que la conexión a la aplicación web de Java remota se lleve a cabo correctamente. Si usa Windows, el resultado será similar al que se muestra en esta ilustración:

    ![][06]

1. Inserte un punto de interrupción en la página JSP; después, abra la dirección URL de la aplicación web de Java en un explorador:

    1. Abra el **Explorador de Azure** en IntelliJ.
    1. Vaya a **Web Apps** (Aplicaciones web) y a la aplicación web de Java que quiera depurar.
    1. Haga clic con el botón derecho en la aplicación web y, con el izquierdo, en **Open in Browser** (Abrir en el explorador).
    1. IntelliJ entrará ahora en el modo de depuración.

## Pasos siguientes

Para obtener más información sobre el uso de Azure con Java, vea el [Centro para desarrolladores de Java de Azure].

Para obtener más información sobre cómo crear aplicaciones web de Azure, consulte [Introducción a Aplicaciones web].

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- URL List -->

[Azure App Service]: http://go.microsoft.com/fwlink/?LinkId=529714
[kit de herramientas de Azure para IntelliJ]: ../azure-toolkit-for-intellij.md
[Installing the Azure Toolkit for IntelliJ]: ../azure-toolkit-for-intellij-installation.md
[Creación de una aplicación web Hello World para Azure en IntelliJ]: ./app-service-web-intellij-create-hello-world-web-app.md
[proyecto web dinámico de ejemplo]: http://go.microsoft.com/fwlink/?LinkId=817337

[Centro para desarrolladores de Java de Azure]: https://azure.microsoft.com/develop/java/
[Introducción a Aplicaciones web]: ./app-service-web-overview.md

<!-- IMG List -->

[01]: ./media/app-service-web-debug-java-web-app-in-intellij/01-debug-java-web-app-in-intellij.png
[02]: ./media/app-service-web-debug-java-web-app-in-intellij/02-configure-intellij-remote-debug.png
[03]: ./media/app-service-web-debug-java-web-app-in-intellij/03-debug-configuration.png
[04]: ./media/app-service-web-debug-java-web-app-in-intellij/04-select-debug.png
[05]: ./media/app-service-web-debug-java-web-app-in-intellij/05-ready-for-remote-debugging.png
[06]: ./media/app-service-web-debug-java-web-app-in-intellij/06-windows-command-prompt-connection-successful-to-remote.png

<!---HONumber=AcomDC_0706_2016-->