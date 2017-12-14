---
title: "Transmisión de datos de Diagnósticos de Azure en la ruta de acceso activa mediante Event Hubs | Microsoft Docs"
description: "Configuración de Diagnósticos de Azure con Event Hubs de un extremo a otro, con instrucciones para escenarios comunes"
services: event-hubs
documentationcenter: na
author: rboucher
manager: carmonm
editor: 
ms.assetid: edeebaac-1c47-4b43-9687-f28e7e1e446a
ms.service: monitoring-and-diagnostics
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/13/2017
ms.author: robb
ms.openlocfilehash: ca0dd96389a605ed8bf34af81eb4d75bef581338
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2017
---
# <a name="streaming-azure-diagnostics-data-in-the-hot-path-by-using-event-hubs"></a>Transmisión de datos de Diagnósticos de Azure en la ruta de acceso activa mediante Event Hubs
Diagnósticos de Azure proporciona maneras flexibles de recopilar métricas y registros de máquinas virtuales de servicios en la nube (VM) y transferir los resultados a Azure Storage. A partir de marzo de 2016 (SDK 2.9), puede enviar diagnósticos a orígenes de datos personalizados y transferir datos de rutas de acceso activas en cuestión de segundos mediante [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/).

Entre los tipos de datos admitidos se incluyen:

* Eventos de Seguimiento de eventos para Windows (ETW)
* Contadores de rendimiento
* Registros de eventos de Windows
* Registros de aplicación
* Registros de infraestructura de diagnóstico de Azure

En este artículo se muestra cómo configurar Diagnósticos de Azure con Event Hubs de manera integral. También se proporcionan instrucciones para los siguientes escenarios comunes:

* Cómo personalizar los registros y las métricas enviadas a Event Hubs
* Cómo cambiar la configuración de cada entorno
* Cómo ver los datos de secuencia de Event Hubs
* Cómo solucionar los problemas de conexión  

## <a name="prerequisites"></a>Requisitos previos
La recepción de Diagnósticos de Azure en Event Hubs se admite en Cloud Services, Virtual Machines, Conjuntos de escalado de máquinas virtuales y Service Fabric a partir de Azure SDK 2.9 y las correspondientes herramientas de Azure Tools para Visual Studio.

* La extensión de Diagnósticos de Azure 1.6 ([Azure SDK para .NET 2.9 o posterior](https://azure.microsoft.com/downloads/) sirve a este fin de forma predeterminada).
* [Visual Studio 2013 o posterior.](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Configuraciones existentes de Diagnósticos de Azure en una aplicación mediante un archivo *.wadcfgx* y uno de los siguientes métodos:
  * Visual Studio: [Configuración de Diagnósticos en Azure Cloud Services y Virtual Machines](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
  * Windows PowerShell: [Habilitar el diagnóstico en Azure Cloud Services mediante PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
* Espacio de nombres de Event Hubs aprovisionado según el artículo [Introducción a los Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)

## <a name="connect-azure-diagnostics-to-event-hubs-sink"></a>Conectar Diagnósticos de Azure al receptor de Event Hubs
De forma predeterminada, Diagnósticos de Azure siempre envía registros y métricas a una cuenta de Azure Storage. Una aplicación también puede enviar datos a Event Hubs con la incorporación de una nueva sección **Sinks** al elemento **PublicConfig** / **WadCfg** del archivo *.wadcfgx*. En Visual Studio, el archivo *.wadcfgx* está almacenado en la siguiente ruta de acceso: **Proyecto de servicio en la nube** > **Roles** > **(nombreDelRol)** > archivo **diagnostics.wadcfgx**.

```xml
<SinksConfig>
  <Sink name="HotPath">
    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
  </Sink>
</SinksConfig>
```
```JSON
"SinksConfig": {
    "Sink": [
        {
            "name": "HotPath",
            "EventHub": {
                "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
                "SharedAccessKeyName": "SendRule"
            }
        }
    ]
}
```

En este ejemplo, la dirección URL del centro de eventos se establece en el espacio de nombres completo del centro de eventos: espacio de nombres del centro de eventos + "/" + nombre del centro de eventos.  

La dirección URL del centro de eventos se muestra en el [Azure portal](http://go.microsoft.com/fwlink/?LinkID=213885) del panel de Event Hubs.  

El nombre **Sink** se puede establecer en cualquier cadena válida siempre y cuando se use el mismo valor sistemáticamente en el archivo de configuración.

> [!NOTE]
> Puede que haya más receptores configurados en esta sección, como *applicationInsights* . Diagnósticos de Azure permite definir uno o varios receptores, si cada uno de ellos también se declara en la sección **PrivateConfig** .  
>
>

El receptor de Event Hubs también se debe declarar y definir en la sección **PrivateConfig** del archivo de configuración *.wadcfgx* .

```XML
<PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <StorageAccount name="{account name}" key="{account key}" endpoint="{optional storage endpoint}" />
  <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="{base64 encoded key}" />
</PrivateConfig>
```
```JSON
{
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{optional storage endpoint}",
    "EventHub": {
        "Url": "https://diags-mycompany-ns.servicebus.windows.net/diageventhub",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "{base64 encoded key}"
    }
}
```

El valor de `SharedAccessKeyName` debe coincidir con una directiva y una clave de firma de acceso compartido (SAS) que se hayan definido en el espacio de nombres de **Event Hubs** . Vaya al panel de Event Hubs en [Azure Portal](https://portal.azure.com), seleccione la pestaña **Configurar** y configure una directiva con nombre (por ejemplo, SendRule) que tenga permisos de *envío*. El elemento **StorageAccount** también se declara en **PrivateConfig**. No hace falta cambiar estos valores si funcionan. En este ejemplo, dejamos los valores vacíos, que indica que un activo de bajada establecerá los valores. Por ejemplo, el archivo de configuración del entorno *ServiceConfiguration.Cloud.cscfg* establecerá las claves y los nombres apropiados.  

> [!WARNING]
> Tenga en cuenta que la clave SAS de Event Hubs se almacena en texto sin formato en el archivo *wadcfgx*. A menudo, esta clave se registra en el control de código fuente o está disponible como un recurso en el servidor de compilación, por lo que debe protegerla de la manera adecuada. Se recomienda usar aquí una clave SAS con permisos de *solo envío* para que los usuarios malintencionados puedan escribir en el Centro de eventos, pero nunca realizar operaciones de administración ni de escucha.
>
>

## <a name="configure-azure-diagnostics-to-send-logs-and-metrics-to-event-hubs"></a>Configuración de Diagnósticos de Azure para enviar registros y métricas a Event Hubs
Como se ha indicado anteriormente, todos los datos de diagnóstico predeterminados y personalizados (es decir, métricas y registros) se envían automáticamente a Azure Storage en los intervalos configurados. Con Event Hubs y cualquier receptor adicional, puede especificar que cualquier nodo raíz u hoja de la jerarquía se envíe al centro de eventos. Esto incluye eventos ETW, contadores de rendimiento, registros de eventos de Windows y registros de aplicaciones.   

Es importante tener en cuenta cuántos puntos de datos se deben transferir realmente a Event Hubs. Normalmente, los desarrolladores transfieren datos de ruta de acceso activa y de baja latencia que deben consumirse e interpretarse rápidamente. Los sistemas que supervisan las alertas o las reglas de escalado automático son algunos ejemplos. Un desarrollador también puede configurar un almacén de búsqueda o un almacén de análisis alternativo (por ejemplo, Azure Stream Analytics, Elasticsearch, un sistema de supervisión personalizado o un sistema de supervisión favorito de terceros).

Las siguientes son algunas configuraciones de ejemplo.

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "sinks": "HotPath",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        }
    ]
}
```

En el ejemplo anterior, el receptor se aplica al nodo principal **PerformanceCounters** de la jerarquía, lo que significa que todos los nodos secundarios **PerformanceCounters** se enviarán a Event Hubs.  

```xml
<PerformanceCounters scheduledTransferPeriod="PT1M">
  <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
  <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
  <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
</PerformanceCounters>
```
```JSON
"PerformanceCounters": {
    "scheduledTransferPeriod": "PT1M",
    "PerformanceCounterConfiguration": [
        {
            "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\Memory\\Available MBytes",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
            "sampleRate": "PT3M"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Rejected",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        },
        {
            "counterSpecifier": "\\ASP.NET\\Requests Queued",
            "sampleRate": "PT3M",
            "sinks": "HotPath"
        }
    ]
}
```

En el ejemplo anterior, el receptor se aplica solo a tres contadores: **Solicitudes en cola**, **Solicitudes rechazadas** y **% de tiempo de procesador**.  

En el ejemplo siguiente se muestra cómo un desarrollador puede limitar la cantidad de datos que se envían como métricas críticas usadas para el estado del servicio.  

```XML
<Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
```
```JSON
"Logs": {
    "scheduledTransferPeriod": "PT1M",
    "scheduledTransferLogLevelFilter": "Error",
    "sinks": "HotPath"
}
```

En este ejemplo, el receptor se aplica a los registros y se filtra solo para el seguimiento de nivel Error.

## <a name="deploy-and-update-a-cloud-services-application-and-diagnostics-config"></a>Implementación y actualización de una aplicación de Cloud Services y configuración de diagnósticos
Visual Studio proporciona la manera más sencilla de implementar la aplicación y la configuración del receptor de Event Hubs. Para ver y editar el archivo, abra el archivo *.wadcfgx* en Visual Studio, edítelo y guárdelo. La ruta de acceso es **Proyecto de servicio en la nube** > **Roles** > **(nombreDelRol)** > **diagnostics.wadcfgx**.  

En este punto, toda la implementación y las acciones de actualización de la implementación en Visual Studio, Visual Studio Team System, además de todos los comandos o scripts que se basan en MSBuild y usan el destino **/t:publish** incluirán el archivo *.wadcfgx* en el proceso de empaquetado. Además, las implementaciones y actualizaciones implementan el archivo en Azure mediante la extensión del agente de Diagnósticos de Azure en las máquinas virtuales.

Después de implementar la aplicación y la configuración de Diagnósticos de Azure, verá inmediatamente la actividad en el panel del centro de eventos. Esto indica que está listo para continuar y ver los datos de la ruta de acceso activa en el cliente de escucha o la herramienta de análisis que prefiera.  

En la siguiente figura, el panel de Event Hubs muestra el envío correcto de los datos de diagnóstico al centro de eventos a partir de las 23:00. Ese es el momento en que la aplicación se ha implementado con un archivo *.wadcfgx* actualizado y el receptor se ha configurado correctamente.

![][0]  

> [!NOTE]
> Al realizar actualizaciones en el archivo de configuración de Diagnósticos de Azure (.wadcfgx), se recomienda insertar las actualizaciones en toda la aplicación, además de la configuración, mediante el script de publicación de Visual Studio o el de Windows PowerShell.  
>
>

## <a name="view-hot-path-data"></a>Ver los datos de la ruta de acceso activa
Como se explicó anteriormente, la escucha y el procesamiento de datos de Event Hubs tienen varias finalidades.

Un enfoque sencillo es crear una pequeña aplicación de consola de prueba para escuchar el centro de eventos e imprimir el flujo de salida. Puede colocar el código siguiente (que se explica con más detalle en [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)) en una aplicación de consola.  

Tenga en cuenta que la aplicación de consola debe incluir el [paquete NuGet del host del procesador de eventos](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).  

No olvide sustituir los valores que aparecen entre corchetes angulares en la función **Main** por los valores de sus recursos.   

```csharp
//Console application code for EventHub test client
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Microsoft.ServiceBus.Messaging;

namespace EventHubListener
{
    class SimpleEventProcessor : IEventProcessor
    {
        Stopwatch checkpointStopWatch;

        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }

        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }

        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, data));

                foreach (var x in eventData.Properties)
                {
                    Console.WriteLine(string.Format("    {0} = {1}", x.Key, x.Value));
                }
            }

            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
    }

    class Program
    {
        static void Main(string[] args)
        {
            string eventHubConnectionString = "Endpoint= <your connection string>”
            string eventHubName = "<Event hub name>";
            string storageAccountName = "<Storage account name>";
            string storageAccountKey = "<Storage account key>”;
            string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
            Console.WriteLine("Registering EventProcessor...");
            var options = new EventProcessorOptions();
            options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
            eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }
    }
}
```

## <a name="troubleshoot-event-hubs-sinks"></a>Solución de problemas con los receptores de Event Hubs
* El centro de eventos no muestra la actividad de eventos entrante o saliente esperada.

    Compruebe que el centro de eventos esté correctamente aprovisionado. Toda la información de conexión de la sección **PrivateConfig** de *wadcfgx* debe coincidir con los valores de su recurso, tal y como se muestra en el portal. Asegúrese de tener una directiva SAS definida (SendRule en el ejemplo) en el portal y que se haya concedido el permiso de *envío* .  
* Después de una actualización, el centro de eventos ya no muestra actividad de eventos entrante o saliente.

    Primero, asegúrese de que la información del centro de eventos y de la configuración es correcta tal y como se ha explicado anteriormente. A veces, **PrivateConfig** se restablece en una actualización de implementación. La solución recomendada consiste en realizar todos los cambios en *.wadcfgx* en el proyecto y, luego, insertar una actualización completa de la aplicación. Si no es posible, asegúrese de que la actualización de diagnósticos inserta completamente **PrivateConfig** , lo que incluye la clave SAS.  
* He probado las sugerencias y el centro de eventos sigue sin funcionar.

    Pruebe a mirar en la tabla de Azure Storage que contiene registros y errores del servicio Diagnósticos de Azure: **WADDiagnosticInfrastructureLogsTable**. Una opción es usar una herramienta como el [Explorador de Azure Storage](http://www.storageexplorer.com) para conectarse a esta cuenta de almacenamiento, ver esta tabla y agregar una consulta para TimeStamp en las últimas 24 horas. Puede usar la herramienta para exportar un archivo .csv y abrirlo en una aplicación como Microsoft Excel. Excel facilita la búsqueda de cadenas de tarjeta de llamadas, como **EventHubs**, para ver qué error se notifica.  

## <a name="next-steps"></a>Pasos siguientes
•    [Más información sobre Event Hubs](https://azure.microsoft.com/services/event-hubs/)

## <a name="appendix-complete-azure-diagnostics-configuration-file-wadcfgx-example"></a>Apéndice: Archivo de configuración completo de Diagnósticos de Azure (.wadcfgx): ejemplo
```xml
<?xml version="1.0" encoding="utf-8"?>
<DiagnosticsConfiguration xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
  <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <WadCfg>
      <DiagnosticMonitorConfiguration overallQuotaInMB="4096" sinks="applicationInsights.errors">
        <DiagnosticInfrastructureLogs scheduledTransferLogLevelFilter="Error" />
        <Directories scheduledTransferPeriod="PT1M">
          <IISLogs containerName="wad-iis-logfiles" />
          <FailedRequestLogs containerName="wad-failedrequestlogs" />
        </Directories>
        <PerformanceCounters scheduledTransferPeriod="PT1M" sinks="HotPath">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" />
        </PerformanceCounters>
        <WindowsEventLog scheduledTransferPeriod="PT1M">
          <DataSource name="Application!*" />
        </WindowsEventLog>
        <CrashDumps>
          <CrashDumpConfiguration processName="WaIISHost.exe" />
          <CrashDumpConfiguration processName="WaWorkerHost.exe" />
          <CrashDumpConfiguration processName="w3wp.exe" />
        </CrashDumps>
        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />
      </DiagnosticMonitorConfiguration>
      <SinksConfig>
        <Sink name="HotPath">
          <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" />
        </Sink>
        <Sink name="applicationInsights">
          <ApplicationInsights />
          <Channels>
            <Channel logLevel="Error" name="errors" />
          </Channels>
        </Sink>
      </SinksConfig>
    </WadCfg>
    <StorageAccount>ACCOUNT_NAME</StorageAccount>
  </PublicConfig>
  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
    <StorageAccount name="{account name}" key="{account key}" endpoint="{storage endpoint}" />
    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
  </PrivateConfig>
  <IsEnabled>true</IsEnabled>
</DiagnosticsConfiguration>
```

El archivo *ServiceConfiguration.Cloud.cscfg* complementario para este ejemplo se parece al siguiente.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
  <Role name="MyFixIt.WorkerRole">
    <Instances count="1" />
    <ConfigurationSettings>
      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
    </ConfigurationSettings>
  </Role>
</ServiceConfiguration>
```

La configuración JSON equivalente para máquinas virtuales es la siguiente:
```JSON
"settings": {
    "WadCfg": {
        "DiagnosticMonitorConfiguration": {
            "overallQuotaInMB": 4096,
            "sinks": "applicationInsights.errors",
            "DiagnosticInfrastructureLogs": {
                "scheduledTransferLogLevelFilter": "Error"
            },
            "Directories": {
                "scheduledTransferPeriod": "PT1M",
                "IISLogs": {
                    "containerName": "wad-iis-logfiles"
                },
                "FailedRequestLogs": {
                    "containerName": "wad-failedrequestlogs"
                }
            },
            "PerformanceCounters": {
                "scheduledTransferPeriod": "PT1M",
                "sinks": "HotPath",
                "PerformanceCounterConfiguration": [
                    {
                        "counterSpecifier": "\\Memory\\Available MBytes",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\ISAPI Extension Requests/sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Web Service(_Total)\\Bytes Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Requests/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET Applications(__Total__)\\Errors Total/Sec",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Queued",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\ASP.NET\\Requests Rejected",
                        "sampleRate": "PT3M"
                    },
                    {
                        "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                        "sampleRate": "PT3M"
                    }
                ]
            },
            "WindowsEventLog": {
                "scheduledTransferPeriod": "PT1M",
                "DataSource": [
                    {
                        "name": "Application!*"
                    }
                ]
            },
            "Logs": {
                "scheduledTransferPeriod": "PT1M",
                "scheduledTransferLogLevelFilter": "Error",
                "sinks": "HotPath"
            }
        },
        "SinksConfig": {
            "Sink": [
                {
                    "name": "HotPath",
                    "EventHub": {
                        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
                        "SharedAccessKeyName": "SendRule"
                    }
                },
                {
                    "name": "applicationInsights",
                    "ApplicationInsights": "",
                    "Channels": {
                        "Channel": [
                            {
                                "logLevel": "Error",
                                "name": "errors"
                            }
                        ]
                    }
                }
            ]
        }
    },
    "StorageAccount": "{account name}"
}


"protectedSettings": {
    "storageAccountName": "{account name}",
    "storageAccountKey": "{account key}",
    "storageAccountEndPoint": "{storage endpoint}",
    "EventHub": {
        "Url": "https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py",
        "SharedAccessKeyName": "SendRule",
        "SharedAccessKey": "YOUR_KEY_HERE"
    }
}
```

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de Event Hubs, visite los vínculos siguientes:

* [Información general de Event Hubs](../event-hubs/event-hubs-what-is-event-hubs.md)
* [Creación de un centro de eventos](../event-hubs/event-hubs-create.md)
* [Preguntas más frecuentes sobre Event Hubs](../event-hubs/event-hubs-faq.md)

<!-- Images. -->
[0]: ../event-hubs/media/event-hubs-streaming-azure-diags-data/dashboard.png
