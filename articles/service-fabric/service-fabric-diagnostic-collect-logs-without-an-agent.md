---
title: "Recopilación de registros directamente desde un proceso de servicio de Azure Service Fabric | Microsoft Azure"
description: "Describe las aplicaciones de Service Fabric que pueden enviar registros directamente a una ubicación central, como Azure Application Insights o Elasticsearch, sin tener que depender del agente Diagnósticos de Azure."
services: service-fabric
documentationcenter: .net
author: karolz-ms
manager: rwike77
editor: 
ms.assetid: ab92c99b-1edd-4677-8c28-4e591d909b47
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/18/2017
ms.author: karolz
translationtype: Human Translation
ms.sourcegitcommit: d7f7b157d8d6fb54259c8f23d5005509f4eb7872
ms.openlocfilehash: 22acb6afbfbfff753e71b5e821385798cc76ffdd
ms.lasthandoff: 01/19/2017


---
# <a name="collect-logs-directly-from-an-azure-service-fabric-service-process"></a>Recopilación de registros directamente desde un proceso de servicio de Azure Service Fabric
## <a name="in-process-log-collection"></a>Recopilación de registros en proceso
La recopilación de registros de aplicación mediante [la extensión Diagnósticos de Azure](service-fabric-diagnostics-how-to-setup-wad.md) es una buena opción para los servicios de **Azure Service Fabric** si el conjunto de orígenes y destinos de los registros es pequeño, no cambian con frecuencia y hay una asignación directa entre los orígenes y sus destinos. Si no es así, una alternativa es hacer que los servicios envíen sus registros directamente a una ubicación central. Este proceso se conoce como **recopilación de registros en proceso** y tiene varias ventajas posibles:

* *Fácil configuración e implementación*

    * La configuración de la recopilación de datos de diagnóstico es parte de la configuración del servicio. Es fácil mantenerla siempre "sincronizada" con el resto de la aplicación.
    * Se puede realizar fácilmente la configuración por aplicación o por servicio.
        * La recopilación de registros basada en agente normalmente requiere una implementación y configuración independientes del agente de diagnóstico, lo que supone una tarea administrativa adicional y una posible fuente de errores. A menudo solo se permite una instancia del agente por máquina virtual (nodo) y la configuración del agente se comparte entre todas las aplicaciones y los servicios que se ejecutan en ese nodo. 

* *Flexibilidad*
   
    * La aplicación puede enviar los datos dondequiera que deban ir siempre que haya una biblioteca de cliente que admita el sistema de almacenamiento de datos de destino. Se pueden agregar tantos destinos nuevos como se desee.
    * Se pueden implementar reglas complejas de captura, filtrado y agregación de datos.
    * Una recopilación de registros basada en agente suele estar limitada por los receptores de datos que el agente admite. Algunos agentes se pueden ampliar.

* *Acceso a los datos y el contexto internos de la aplicación*
   
    * El subsistema de diagnóstico que se ejecuta dentro del proceso de la aplicación o servicio puede ampliar fácilmente los seguimientos con información contextual.
    * Con la recopilación de registros basada en agente, los datos se deben enviar a un agente mediante algún mecanismo de comunicación entre procesos, como el Seguimiento de eventos para Windows. Este mecanismo podría imponer limitaciones adicionales.

Por supuesto, es posible combinar y beneficiarse de ambos métodos de recopilación. de hecho, puede que sea la mejor solución para muchas aplicaciones. La recopilación basada en agente es una solución natural para recopilar los registros relacionados con el clúster en su totalidad y con nodos de clúster individuales. Es una manera mucho más confiable que la recopilación de registros en proceso para diagnosticar problemas de inicio y bloqueos del servicio. Además, como hay muchos servicios en ejecución dentro de un clúster de Service Fabric y cada servicio realiza su propia recopilación de registros en proceso, se producen numerosas conexiones salientes desde el clúster. Un gran número de conexiones salientes supone una carga para el subsistema de red y para el destino de registros. Un agente como [ **Diagnósticos de Azure** ](../cloud-services/cloud-services-dotnet-diagnostics.md) puede recopilar datos de varios servicios y enviar todos los datos a través de unas pocas conexiones, lo que mejora el rendimiento. 

En este artículo se muestra cómo configurar una recopilación de registros en proceso mediante la [**biblioteca de código abierto EventFlow**](https://github.com/Azure/diagnostics-eventflow). Podrían utilizarse otras bibliotecas para el mismo propósito, pero EventFlow tiene la ventaja de que ha sido diseñada específicamente para la recopilación de registros en proceso y para admitir los servicios de Service Fabric. Usamos [ **Azure Application Insights** ](https://azure.microsoft.com/services/application-insights/) como destino de los registros. Otros destinos como [ **Event Hubs** ](https://azure.microsoft.com/services/event-hubs/) o [ **Elasticsearch** ](https://www.elastic.co/products/elasticsearch) también son compatibles. Es simplemente una cuestión de instalar los paquetes de NuGet apropiados y configurar el destino en el archivo de configuración EventFlow. Para obtener más información sobre los destinos de los registros que no sea Application Insights, consulte la [documentación de EventFlow](https://github.com/Azure/diagnostics-eventflow).

## <a name="adding-eventflow-library-to-a-service-fabric-service-project"></a>Incorporación de la biblioteca EventFlow a un proyecto de servicio de Service Fabric
Los archivos binarios de EventFlow están disponibles como un conjunto de paquetes de NuGet. Para agregar EventFlow a un proyecto de servicio de Service Fabric, en el Explorador de soluciones, haga clic con el botón derecho en el proyecto y elija "Administrar paquetes de NuGet". Cambie a la pestaña "Examinar" y busque "`Diagnostics.EventFlow`":

![Paquetes de NuGet para EventFlow en la interfaz de usuario del administrador de paquetes de NuGet de Visual Studio][1]

El servicio que hospede EventFlow debe incluir los paquetes adecuados según el origen y destino de los registros de aplicaciones. Agregue los siguientes paquetes: 

* `Microsoft.Diagnostics.EventFlow.Input.EventSource` 
    * (para capturar datos de la clase EventSource del servicio y de las clases EventSource estándar como *Microsoft-ServiceFabric-Services* y *Microsoft-ServiceFabric-Actors*)
* `Microsoft.Diagnostics.EventFlow.Output.ApplicationInsights` 
    * (vamos a enviar los registros a un recurso de Azure Application Insights)  
* `Microsoft.Diagnostics.EventFlow.ServiceFabric` 
    * (permite inicializar la canalización de EventFlow desde la configuración del servicio de Service Fabric, y notifica los problemas con el envío de datos de diagnóstico como informes de estado de mantenimiento de Service Fabric)

> [!NOTE]
> El paquete `Microsoft.Diagnostics.EventFlow.Input.EventSource` necesita que proyecto de servicio sea para .NET Framework 4.6 o versiones posteriores. Asegúrese de establecer la plataforma de destino adecuada en las propiedades del proyecto antes de instalar este paquete. 

Una vez instalados todos los paquetes, el siguiente paso es configurar y habilitar EventFlow en el servicio.

## <a name="configuring-and-enabling-log-collection"></a>Configuración y habilitación de la recopilación de registros
La canalización de EventFlow, responsable del envío de los registros, se crea a partir de una especificación almacenada en un archivo de configuración. El paquete `Microsoft.Diagnostics.EventFlow.ServiceFabric` instala un archivo de configuración inicial de EventFlow en la carpeta de la solución `PackageRoot\Config`. El nombre de archivo es `eventFlowConfig.json`. Este archivo de configuración debe modificarse para capturar los datos de la clase `EventSource` del servicio predeterminado y enviar los datos al servicio Application Insights.

> [!NOTE]
> Se da por hecho que está familiarizado con el servicio **Azure Application Insights** y que tiene un recurso de Application Insights que va a usar para supervisar el servicio de Service Fabric. Si necesita más información, consulte [Creación de recursos en Application Insights](../application-insights/app-insights-create-new-resource.md).

Abra el archivo `eventFlowConfig.json` en el editor y cambie su contenido tal y como se muestra a continuación. Asegúrese de reemplazar el nombre ServiceEventSource y la clave de instrumentación de Application Insights según los comentarios. 

```json
{
  "inputs": [
    {
      "type": "EventSource",
      "sources": [
        { "providerName": "Microsoft-ServiceFabric-Services" },
        { "providerName": "Microsoft-ServiceFabric-Actors" },
        // (replace the following value with your service's ServiceEventSource name)
        { "providerName": "your-service-EventSource-name" }
      ]
    }
  ],
  "filters": [
    {
      "type": "drop",
      "include": "Level == Verbose"
    }
  ],
  "outputs": [
    {
      "type": "ApplicationInsights",
      // (replace the following value with your AI resource's instrumentation key)
      "instrumentationKey": "00000000-0000-0000-0000-000000000000"
    }
  ],
  "schemaVersion": "2016-08-11"
}
```

> [!NOTE]
> El nombre del origen ServiceEventSource del servicio es el valor de la propiedad Name de `EventSourceAttribute` aplicado a la clase ServiceEventSource. Se especifica todo en el archivo `ServiceEventSource.cs`, que forma parte del código del servicio. Por ejemplo, en el siguiente fragmento de código, el nombre del origen ServiceEventSource es *MyCompany-Application1-Stateless1*:
> ```csharp
> [EventSource(Name = "MyCompany-Application1-Stateless1")]
> internal sealed class ServiceEventSource : EventSource
> {
>    // (rest of ServiceEventSource implementation)
>} 
> ```

Tenga en cuenta que el archivo `eventFlowConfig.json` forma parte del paquete de configuración del servicio. Los cambios en este archivo pueden incluirse en actualizaciones completas o de solo configuración del servicio; están sujetos a las comprobaciones del estado de actualización de Service Fabric y se revierten automáticamente en caso de errores durante la actualización. Para más información, consulte el [tutorial sobre la actualización de aplicaciones de Service Fabric](service-fabric-application-upgrade.md).

El último paso consiste en crear una instancia de la canalización de EventFlow en el código de inicio del servicio, ubicado en el archivo `Program.cs`. En el siguiente ejemplo, se han marcado las incorporaciones relacionadas con EventFlow con comentarios que comienzan con `****`:

```csharp
using System;
using System.Diagnostics;
using System.Threading;
using Microsoft.ServiceFabric;
using Microsoft.ServiceFabric.Services.Runtime;

// **** EventFlow namespace
using Microsoft.Diagnostics.EventFlow.ServiceFabric;

namespace Stateless1
{
    internal static class Program
    {
        /// <summary>
        /// This is the entry point of the service host process.
        /// </summary>
        private static void Main()
        {
            try
            {
                // **** Instantiate log collection via EventFlow
                using (var diagnosticsPipeline = ServiceFabricDiagnosticPipelineFactory.CreatePipeline("MyApplication-MyService-DiagnosticsPipeline"))
                {

                    
                    ServiceRuntime.RegisterServiceAsync("Stateless1Type",
                    context => new Stateless1(context)).GetAwaiter().GetResult();

                    ServiceEventSource.Current.ServiceTypeRegistered(Process.GetCurrentProcess().Id, typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);
                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e.ToString());
                throw;
            }
        }
    }
}
```

El nombre que se pasa como parámetro del método `CreatePipeline` de `ServiceFabricDiagnosticsPipelineFactory` es el nombre de la *entidad de estado de mantenimiento* que representa la canalización de recopilación de registros de EventFlow. Este nombre se usa si EventFlow encuentra un error y lo notifica a través del subsistema de estado de mantenimiento de Service Fabric.

## <a name="verification"></a>Comprobación
Inicie el servicio y observe la ventana de resultados de depuración de Visual Studio. Una vez iniciado el servicio, deberá comenzar a ver la evidencia de que el servicio está enviando registros de "Telemetría de Application Insights". Abra un explorador web y vaya al recurso de Application Insights. Abra la pestaña "Buscar" (en la parte superior de la hoja "Información general" predeterminada). Tras una breve demora, comenzará a ver sus seguimientos en el portal de Application Insights:

![El portal de Application Insights muestra los registros de una aplicación de Service Fabric][2]

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre cómo diagnosticar y supervisar un servicio Service Fabric](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
* [Documentación de EventFlow](https://github.com/Azure/diagnostics-eventflow)


<!--Image references-->
[1]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/eventflow-nugets.png
[2]: ./media/service-fabric-diagnostics-collect-logs-without-an-agent/ai-traces.png
