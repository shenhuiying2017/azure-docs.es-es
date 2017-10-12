---
title: "Depuración de microservicios de Azure en Windows | Microsoft Docs"
description: "Aprenda a supervisar y diagnosticar sus servicios creados con Service Fabric de Microsoft Azure en una máquina de desarrollo local."
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: edcc0631-ed2d-45a3-851d-2c4fa0f4a326
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/24/2017
ms.author: dekapur
ms.openlocfilehash: 08998340afb2f242b9a268331607b0d1ddb9b0c6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Supervisión y diagnóstico de servicios en una configuración de desarrollo de máquina local
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
> 
> 

Acciones como supervisar, detectar, diagnosticar y solucionar problemas permiten a los servicios continuar con una interrupción mínima de la experiencia del usuario. Aunque la supervisión y el diagnóstico resultan fundamentales en un entorno de producción implementado real, la eficiencia dependerá de la adopción de un modelo similar durante el desarrollo de servicios para garantizar que funciona cuando pasa a una configuración del mundo real. Service Fabric facilita a los desarrolladores de servicio la implementación de diagnósticos que puede funcionar sin problemas en configuraciones de desarrollo local de máquina única y en configuraciones de clúster de producción del mundo real.

## <a name="event-tracing-for-windows"></a>Seguimiento de eventos para Windows
[Seguimiento de eventos para Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW) es la tecnología recomendada para los mensajes de seguimiento en Service Fabric. Algunas de las ventajas del uso de ETW son:

* **ETW es rápido.** Se creó como una tecnología de seguimiento que tiene un impacto mínimo en los tiempos de ejecución de código.
* **El seguimiento de ETW funciona sin problemas en entornos de desarrollo locales y también en las instalaciones de clúster del mundo real.** Esto significa que no tiene que volver a escribir el código de seguimiento cuando está listo para implementar su código en un clúster real.
* **El código de tejido del sistema de Service Fabric también usa ETW para el seguimiento interno.** Esto le permite ver los seguimientos de aplicación intercalados con seguimientos de sistema de Service Fabric. También puede ayudarle a comprender más fácilmente las secuencias y las relaciones mutuas entre sus eventos y el código de la aplicación en el sistema subyacente.
* **Hay compatibilidad integrada en las herramientas de Visual Studio de Service Fabric para ver los eventos ETW.** Los eventos ETW aparecen en la vista Eventos de diagnóstico de Visual Studio una vez que Visual Studio se ha configurado correctamente con Service Fabric. 

## <a name="view-service-fabric-system-events-in-visual-studio"></a>Ver eventos del sistema de Service Fabric en Visual Studio
Service Fabric emite los eventos ETW para ayudar a los desarrolladores de aplicaciones a comprender qué ocurre en la plataforma. Si aún no lo ha hecho, siga los pasos descritos en [Creación de la primera aplicación de Service Fabric en Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md). Esta información le ayudará a poner en funcionamiento una aplicación con el Visor de eventos de diagnóstico que muestra los mensajes de seguimiento.

1. Si la ventana de eventos de diagnóstico no aparece automáticamente, vaya a la pestaña **Vista** de Visual Studio, elija **Other Windows** (Otras ventanas) y, después, **Visor de eventos de diagnóstico**.
2. Cada evento tiene información de metadatos estándar que indica el nodo, la aplicación y el servicio del que procede el evento. También puede filtrar la lista de eventos mediante el cuadro **Filtrar eventos** de la parte superior de la ventana de eventos. Por ejemplo, puede filtrar por **Nombre del nodo** o **Nombre del servicio**. Y si se está fijando en los detalles del evento, también puede pausar utilizando el botón **Pausar** situado en la parte superior de la ventana de eventos y reanudar más adelante sin pérdida de eventos.
   
   ![Visor de eventos de diagnósticos de Visual Studio](./media/service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally/DiagEventsExamples2.png)

## <a name="add-your-own-custom-traces-to-the-application-code"></a>Agregue sus propios seguimientos personalizados al código de aplicación
Las plantillas de proyecto de Visual Studio de Service Fabric contienen código de ejemplo. El código muestra cómo agregar los seguimientos de ETW de código de aplicación personalizado que aparecerán en el visor de ETW de Visual Studio junto con los seguimientos del sistema de Service Fabric. La ventaja de este método es que los metadatos se agregan automáticamente a los seguimientos y el Visor de eventos de diagnóstico de Visual Studio ya está configurado para mostrarlos.

Para los proyectos creados desde las **plantillas de servicio** (con o sin estado), solo tiene que buscar la implementación de `RunAsync`:

1. La llamada a `ServiceEventSource.Current.ServiceMessage` in the `RunAsync` muestra un ejemplo de un seguimiento de ETW personalizado desde el código de la aplicación.
2. En el archivo **ServiceEventSource.cs** encontrará una sobrecarga para el método `ServiceEventSource.ServiceMessage` que se debe usar para eventos de alta frecuencia causados por razones de rendimiento.

Para los proyectos creados desde las **plantillas de acto** (con o sin estado):

1. Abra el archivo **"NombreProyecto".cs** donde *NombreProyecto* es el nombre que eligió para su proyecto de Visual Studio.  
2. Busque el código `ActorEventSource.Current.ActorMessage(this, "Doing Work");` en el método *DoWorkAsync* .  Esto es un ejemplo de un seguimiento ETW personalizado creado a partir del código de aplicación.  
3. En el archivo **ActorEventSource.cs** encontrará una sobrecarga para el método `ActorEventSource.ActorMessage` que se debe usar para los eventos de alta frecuencia causados por razones de rendimiento.

Después de agregar seguimiento ETW personalizado al código del servicio, puede crear, implementar y ejecutar la aplicación de nuevo para ver sus eventos en el Visor de eventos de diagnóstico. Si depura la aplicación con **F5**, el Visor de eventos de diagnóstico se abrirá automáticamente.

## <a name="next-steps"></a>Pasos siguientes
El mismo código de seguimiento que agregó a la aplicación anterior para diagnósticos locales funcionará con las herramientas que puede usar para ver estos eventos al ejecutar la aplicación en un clúster de Azure. Consulte estos artículos que tratan sobre las distintas opciones de las herramientas y describen cómo puede configurarlas.

* [Recopilación de registros con Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md)
* [Recopilación y agregación de eventos con EventFlow](service-fabric-diagnostics-event-aggregation-eventflow.md)

