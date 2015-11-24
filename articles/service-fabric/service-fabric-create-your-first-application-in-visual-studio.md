<properties
   pageTitle="Creación de la primera aplicación de Service Fabric en Visual Studio | Microsoft Azure"
   description="Creación, implementación y depuración de una aplicación de Service Fabric con Visual Studio"
   services="service-fabric"
   documentationCenter=".net"
   authors="seanmck"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/13/2015"
   ms.author="seanmck"/>

# Creación de la primera aplicación de Service Fabric en Visual Studio

El SDK de Service Fabric incluye un complemento para Visual Studio que proporciona herramientas y plantillas para crear, depurar e implementar aplicaciones de Service Fabric. Este tema le guiará por el proceso de creación de su primera aplicación en Visual Studio.

## Requisitos previos

Antes de comenzar, asegúrese de haber [configurado el entorno de desarrollo](service-fabric-get-started.md).

## Creación de la aplicación

Una aplicación de Service Fabric puede contener uno o varios servicios, cada uno de ellos con un rol específico en la prestación de la funcionalidad de la aplicación. El Asistente para nuevo proyecto permite crear un proyecto de aplicación junto con su primer proyecto de servicio. Puede agregar más servicios posteriormente.

1. Inicie Visual Studio como administrador.

2. Haga clic en **Archivo > Nuevo proyecto > Nube > Aplicación de Service Fabric**.

3. Asigne un nombre a la aplicación y haga clic en Aceptar.

	![Cuadro de diálogo de proyecto nuevo en Visual Studio.][1]

4. En el cuadro de diálogo siguiente, se le pedirá que elija el primer tipo de servicio que se va a incluir en la aplicación. En este tutorial, elegiremos "Servicio con estado". Asígnele un nombre y haga clic en Aceptar.

	![Cuadro de diálogo de servicio nuevo en Visual Studio.][2]

	>[AZURE.NOTE]Para obtener más información acerca de las opciones, consulte [Elección de un marco para su servicio](service-fabric-choose-framework.md).

	Visual Studio crea el proyecto de aplicación y el proyecto de servicio con estado y los muestra en el Explorador de soluciones.

	![Explorador de soluciones después de la creación de una aplicación con el servicio con estado][3]

	El proyecto de aplicación no contiene ningún código directamente; en su lugar, hace referencia a un conjunto de proyectos de servicio. Además, contiene otros tres tipos de contenido:

	- **Perfiles de publicación**: se usa para administrar las herramientas preferidas para los distintos entornos.

	- **Scripts**: script de PowerShell para implementar o actualizar una aplicación. Visual Studio usa este script en segundo plano y se puede invocar directamente desde la línea de comandos.

	- **Definición de aplicación**: el manifiesto de aplicación y los archivos de parámetros de la aplicación asociados definen la aplicación y permiten configurarla específicamente para un entorno determinado.

  Para obtener información general del contenido del proyecto de servicio, consulte [Introducción a Reliable Services](service-fabric-reliable-services-quick-start.md).

## Implemente la aplicación y depúrela

Ahora que tenemos una aplicación, probemos a ejecutarla.

1. Presione F5 en Visual Studio para implementar la aplicación, con el fin de depurarla.

	>[AZURE.NOTE]Esta operación tarda mucho tiempo en completarse la primera vez, ya que Visual Studio tiene que crear un clúster local para desarrollo. En los clústeres locales se ejecuta el mismo código de plataforma que se compila en los clústeres de varias máquinas, pero en una sola máquina. El estado de la creación del clúster se puede ver en la ventana de salida de Visual Studio.

	Cuando el clúster esté listo, recibirá una notificación de la aplicación de administración de la bandeja de sistema del clúster local que se incluye con el SDK.

	![Notificación de bandeja de sistema del clúster local][4]

2. Una vez que se inicie la aplicación, Visual Studio mostrará automáticamente el visor de eventos de diagnóstico, donde se pueden ver los resultados del seguimiento desde el servicio.

	![Visor de eventos de diagnóstico][5]

	En el caso de la plantilla del servicio con estado, los mensajes muestran simplemente el valor del contador que se incrementa en el método `RunAsync` de MyStatefulService.cs.

3. Expanda uno de los eventos para ver más detalles, incluido el nodo en que se ejecuta el código (en este caso es el nodo 2, aunque en su máquina puede ser otro).

	![Detalle del Visor de eventos de diagnóstico][6]

	El clúster local se compone de cinco nodos hospedados en una sola máquina, que emula un clúster de cinco nodos en el que los nodos están en distintas máquinas. Desactivemos uno de los nodos del clúster local para simular la pérdida de una máquina y ejercitar el depurador de Visual Studio al mismo tiempo.

    >[AZURE.NOTE]Los eventos de diagnóstico de la aplicación emitidos por la plantilla del proyecto usan la clase `ServiceEventSource` incluida. Para obtener más información, consulte [Supervisión y diagnóstico locales de servicios](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally)

4. Busque la clase del proyecto de servicio que deriva de StatefulService (por ejemplo, MyStatefulService) y establezca un punto de interrupción en la primera línea del método `RunAsync`.

	![Punto de interrupción en el método RunAsync de servicio con estado][7]

5. Haga clic con el botón derecho en la aplicación de bandeja del sistema del Administrador de clústeres local y elija Administrar clúster Local para iniciar el Explorador de Service Fabric.

  ![Inicie el Explorador de Service Fabric desde el Administrador de clústeres locales][systray-launch-sfx]

  El Explorador de Service Fabric ofrece una representación visual de un clúster, incluidos el conjunto de las aplicaciones implementadas y el conjunto de nodos físicos que lo componen.

6. En el panel izquierdo, expanda **Clúster > Nodos** y busque el nodo en que se ejecuta el código.

7. Haga clic en **Acciones > Desactivar (reiniciar)** para simular un reinicio de la máquina.

	![Detener un nodo en el Explorador de Service Fabric][sfx-stop-node]

	Momentáneamente, debería ver que se alcanza el punto de interrupción en Visual Studio cuando el cálculo hacía perfectamente en un nodo se conmuta por error a otro.

8. Vuelva al Visor de eventos de diagnóstico y observe los mensajes. Tenga en cuenta que el contador no ha dejado de incrementarse, aunque los eventos realmente proceden de otro nodo.

  ![Visor de eventos de diagnóstico después de la conmutación por error][diagnostic-events-viewer-detail-post-failover]


## Pasos siguientes

- [Más información sobre cómo crear clústeres en Azure](service-fabric-cluster-creation-via-portal.md)
- [Más información acerca de cómo crear Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Vea cómo puede exponer los servicios a Internet con WebAPI](service-fabric-add-a-web-frontend.md)
- [Intente crear un servicio con el modelo de programación de Actor confiable](service-fabric-reliable-actors-get-started.md)

<!-- Image References -->

[1]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog.png
[2]: ./media/service-fabric-create-your-first-application-in-visual-studio/new-project-dialog-2.png
[3]: ./media/service-fabric-create-your-first-application-in-visual-studio/solution-explorer-stateful-service-template.png
[4]: ./media/service-fabric-create-your-first-application-in-visual-studio/local-cluster-manager-notification.png
[5]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer.png
[6]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail.png
[7]: ./media/service-fabric-create-your-first-application-in-visual-studio/runasync-breakpoint.png
[sfx-stop-node]: ./media/service-fabric-create-your-first-application-in-visual-studio/sfe-deactivate-node.png
[systray-launch-sfx]: ./media/service-fabric-create-your-first-application-in-visual-studio/launch-sfx.png
[diagnostic-events-viewer-detail-post-failover]: ./media/service-fabric-create-your-first-application-in-visual-studio/diagnostic-events-viewer-detail-post-failover.png

<!---HONumber=Nov15_HO4-->