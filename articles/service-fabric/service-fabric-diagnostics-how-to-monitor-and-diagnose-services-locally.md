<properties
   pageTitle="Service Fabric de Microsoft Azure Cómo supervisar y diagnosticar servicios localmente"
   description="Este artículo se describe cómo puede supervisar y diagnosticar sus servicios creados con Service Fabric de Microsoft Azure en una máquina de desarrollo local."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="samgeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/22/2015"
   ms.author="kunalds"/>


# Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local
Acciones como supervisar, detectar, diagnosticar y solucionar problemas permite a los servicios continuar con una interrupción mínima de la experiencia del usuario. Aunque resulta fundamental en un entorno de producción implementado real, la eficacia dependerá de la adopción de un modelo similar durante el desarrollo de servicios para garantizar que funciona cuando pasa a una configuración del mundo real. Service Fabric facilita a los desarrolladores de servicio la implementación de diagnóstico que puede funcionar sin problemas en el desarrollo local de máquina única y en configuraciones de clúster de producción del mundo real.

## Seguimiento y registro
[Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) es la tecnología recomendada para los mensajes de seguimiento en Service Fabric. Los motivos para esto son:

* ETW es rápido. Se creó como una tecnología de seguimiento que tiene un impacto mínimo en los tiempos de ejecución de código.
* El seguimiento de ETW funciona sin problemas en entornos de desarrollo locales y también en las instalaciones de clúster del mundo real. Esto significa que no tiene que volver a escribir el código de seguimiento cuando está listo para implementar su código en un clúster real.
* El código de tejido del sistema de Service Fabric también usa ETW para el seguimiento interno. Esto le permite ver los seguimientos de aplicación intercalados con seguimientos del sistema de Service Fabric, haciendo que sean más sencillo de comprender las secuencias y las interrelacciones entre sus eventos y código de aplicación en el sistema subyacente.
* Hay compatibilidad integrada en las herramientas de Visual Studio de Service Fabric para ver los eventos ETW.


## Ver eventos del sistema de Service Fabric en Visual Studio

Service Fabric emite los eventos ETW para ayudar a los desarrolladores de aplicaciones a comprender qué ocurre en la plataforma. Para ver estos eventos, sigue estos pasos:

1. Debe tener los siguientes requisitos previos instalados.
   * Visual Studio 2015
   * SDK de Service Fabric

2. Inicie Visual Studio como administrador.

3. Cree un proyecto (o abra uno existente) para un actor o servicio con o sin estado.

  ![Crear una aplicación de Service Fabric](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject.png)

  ![Crear un servicio de Service Fabric](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/CreateServiceFabricProject-2.png)

4. Presione F5 para depurar la aplicación. Los eventos de Service Fabric deben aparecer en la ventana de eventos de diagnóstico. Cada evento tiene información de metadatos estándar que indica el nodo, la aplicación y el servicio del que procede el evento. También puede filtrar la lista de eventos mediante el cuadro "Filtrar eventos" en la parte superior de las ventanas, por ejemplo, puede filtrar en el nombre del nodo o el nombre de servicio.

  ![Visor de eventos de diagnósticos de Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

5. Si la ventana de eventos de diagnóstico no aparece automáticamente, vaya a la pestaña Explorador de servidores en Visual Studio, haga clic con el botón secundario en el clúster de Service Fabric y elija "Ver eventos de diagnóstico" en el menú contextual.

  ![Abrir el Visor de eventos de diagnósticos de Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/ServerExViewDiagEvents.png)

## Agregue sus propios seguimientos personalizados al código de aplicación
Las plantillas de proyecto de Visual Studio de Service Fabric contienen código de ejemplo. El código muestra cómo agregar seguimientos de ETW de código de aplicación personalizado que aparecerán en el visor de ETW de Visual Studio junto con seguimientos desde Service Fabric. La ventaja de este método es que los metadatos se agregan automáticamente a los seguimientos y el Visor de diagnóstico de Visual Studio ya está configurado para mostrarlos.

Para los proyectos creados desde las **plantillas de servicio** (con o sin estado):

1. Abra el archivo **Service.cs**. La llamada a`ServiceEventSource.Current.Message` del método *RunAsync* muestra un ejemplo de un seguimiento de ETW personalizado desde el código de la aplicación.
2. En el archivo **ServiceEventSource.cs**, las sobrecargas del método `ServiceEventSource.Message` muestran una opción de cómo crear seguimientos ETW personalizados.

Para los proyectos creados desde las **plantillas de acto** (con o sin estado):

1. Abra el archivo **"NombreProyecto".cs** donde *NombreProyecto* es el nombre que eligió para su proyecto de Visual Studio.  
2. Busque el código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` en el método *DoWorkAsync*. Esto es un ejemplo de un seguimiento ETW personalizado del código de aplicación.  
3. En el archivo **ActorEventSource.cs**, las sobrecargas del método `ActorEventSource.ActorMessage` muestran una opción de cómo crear seguimientos ETW personalizados.

Después de agregar seguimiento ETW personalizado al código del servicio, puede crear, implementar y ejecutar la aplicación de nuevo para ver sus eventos en el visor de diagnósticos. Si depura la aplicación con F5, el Visor de diagnóstico se abrirá automáticamente.

##Próximamente
El mismo código de seguimiento que agregó a la aplicación anterior para diagnósticos locales funcionará con las herramientas que puede usar para ver estos eventos al ejecutar el mismo código en un clúster de Azure. Pronto estarán disponibles los detalles al respecto.

## Pasos siguientes

* [Introducción al estado de Service Fabric](service-fabric-health-introduction.md)
* [Configuración de Application Insights](service-fabric-diagnostics-application-insights-setup.md)
* [Supervisión de diagnósticos y rendimiento de actores de Service Fabric de Azure](service-fabric-reliable-actors-diagnostics.md)
* [Diagnóstico de servicios de confianza con estado](service-fabric-reliable-services-diagnostics.md)

<!---HONumber=Oct15_HO3-->