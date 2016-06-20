<properties 
   pageTitle="Transmisión de datos de Diagnósticos de Azure en la ruta de acceso activa mediante Centros de eventos"
   description="Se muestra cómo configurar Diagnósticos de Azure con Centros de eventos de extremo a extremo, además de instrucciones para escenarios comunes."
   services="event-hubs"
   documentationCenter="na"
   authors="tomarcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="event-hubs"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="tarcher" />

# Transmisión de datos de Diagnósticos de Azure en la ruta de acceso activa mediante Centros de eventos

## Información general
Diagnósticos de Azure proporciona maneras flexibles de recopilar métricas y registros de máquinas virtuales de procesos y transferirlos a Almacenamiento de Azure. A partir de marzo de 2016 (SDK 2.9), existe ahora la posibilidad de insertar diagnósticos de Azure en orígenes de datos completamente personalizados y transferir datos de rutas activas en cuestión de segundos mediante Centros de eventos de Azure.

Entre los tipos de datos admitidos se incluyen:

- Eventos ETW
- Contadores de rendimiento
- Registros de eventos de Windows 
- Registros de aplicación
- Registros de infraestructura de Diagnósticos de Azure
 
En este artículo se muestra cómo configurar Diagnósticos de Azure con Centros de eventos de un extremo a otro. También se ofrecen instrucciones para escenarios comunes, por ejemplo, personalizar qué registros y métricas se insertan en los Centros de eventos, cómo cambiar la configuración en cada entorno, un ejemplo de cómo ver los datos de secuencia de Centros de eventos y cómo solucionar los problemas de la conexión.

## Requisitos previos
La inserción de Centros de eventos en Diagnósticos de Azure se admite en todos los tipos de procesos (servicios en la nube, máquinas virtuales, VMSS y Service Fabric) que admiten WAD, comenzando en el SDK 2.9 de Azure y las correspondientes herramientas de Azure Tools para Visual Studio.
  
- La extensión de Diagnósticos de Azure 1.6 ([Azure SDK para .NET 2.9 o posterior](https://azure.microsoft.com/downloads/) sirve a este fin de forma predeterminada).
- [Visual Studio 2013 o superior](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
- Configuración correcta de Diagnósticos de Azure en la aplicación mediante un archivo *wadcfgx* siguiendo uno de estos métodos:
	- Visual Studio: [Configuración de Diagnósticos en Servicios en la nube y Máquinas virtuales de Azure](../vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines.md)
	- Windows PowerShell: [Habilitar el diagnóstico en Servicios en la nube de Azure mediante PowerShell](../cloud-services/cloud-services-diagnostics-powershell.md)
- Espacio de nombres de Centros de eventos aprovisionado según el artículo [Introducción a los Centros de eventos](./event-hubs-csharp-ephcs-getstarted.md)

## Conexión de Diagnósticos de Azure al receptor de Centros de eventos
Diagnósticos de Azure siempre inserta registros y métricas, de forma predeterminada, en una cuenta de Almacenamiento de Azure. Adicionalmente, una aplicación se puede insertar en Centros de eventos agregando una nueva sección **Sinks** al elemento **WadCfg** de la sección **PublicConfig** del archivo *wadcfgx*. En Visual Studio, el archivo *.wadcfgx* está almacenado en el archivo *Proyecto de servicio de nube > Roles > (RoleName) > diagnostics.wadcfgx*.
  
	  <SinksConfig>
	    <Sink name="HotPath">
	      <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" />
	    </Sink>
	  </SinksConfig>

En este ejemplo, la URL del centro de eventos se establece en el espacio de nombres completo del centro de eventos (espacio de nombres de ServiceBus + "/" + nombre del centro de eventos).

La URL del centro de eventos se muestra en el [Portal de Azure clásico](http://go.microsoft.com/fwlink/?LinkID=213885) en el panel de Centros de eventos.

El nombre **Sink** se puede establecer en cualquier cadena válida siempre y cuando se use el mismo valor sistemáticamente en el archivo de configuración.

**Nota:** Puede que haya receptores adicionales configurados en esta sección, como "applicationInsights". Diagnósticos de Azure permite definir uno o varios receptores, siempre que cada receptor también se declare en la sección **PrivateConfig**.

El receptor de Centros de eventos también se debe declarar y definir en la sección **PrivateConfig** del archivo de configuración *.wadcfgx*.

	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diags-mycompany-ns.servicebus.windows.net/diageventhub" SharedAccessKeyName="SendRule" SharedAccessKey="9B3SwghJOGEUvXigc6zHPInl2iYxrgsKHZoy4nm9CUI=" />
	  </PrivateConfig>

El valor de **SharedAccessKeyName** debe coincidir con una clave y una directiva de SAS que se hayan definido en el espacio de nombres de **ServiceBus/EventHub**. Para hacer esto, vaya hasta el panel de Centro de eventos en el [Portal de Azure clásico](https://manage.windowsazure.com), seleccione la pestaña **Configurar** y configure una directiva con nombre (por ejemplo, "SendRule") que tenga permisos de *envío*. El elemento **StorageAccount** también se declara en **PrivateConfig**. No es necesario cambiar estos valores, sobre todo si funcionan. En este ejemplo, dejamos los valores vacíos, lo que es un indicio de que un recurso de nivel inferior establecerá los valores; por ejemplo, el archivo de configuración del entorno *ServiceConfiguration.Cloud.cscfg* establecerá los nombres y las claves del entorno apropiadas.

>[AZURE.WARNING] Tenga en cuenta que la clave SAS del centro de eventos se almacena en texto sin formato en el archivo *wadcfgx*. A menudo, esta se registra en el control de código fuente o como un recurso en el servidor de compilación, por lo que debe protegerla de la manera adecuada. Se recomienda usar aquí una clave SAS con permisos de *solo envío* para que cualquier usuario malintencionado pueda, si acaso, escribir en el centro de eventos, pero nunca escucharlo ni administrarlo.

## Configuración de registros y métricas de Diagnósticos de Azure para insertarlos con Centros de eventos
Como se ha indicado anteriormente, todos los datos de diagnóstico predeterminados y personalizados (es decir, métricas y registros) se insertan automáticamente en Almacenamiento de Azure en los intervalos configurados. Con Centros de eventos (y cualquier receptor adicional), tiene la opción de especificar que cualquier nodo raíz u hoja de la jerarquía se inserte con el centro de eventos. Esto incluye eventos ETW, contadores de rendimiento, registros de eventos de Windows y registros de aplicaciones.

Es importante tener en cuenta cuántos puntos de datos se deben transferir realmente a Centros de eventos. Normalmente, los desarrolladores emplean este procedimiento con datos de ruta de acceso activa y baja latencia que se deben consumir e interpretar rápidamente (por ejemplo, al supervisar sistemas de alertas o reglas de escalado automático). También puede utilizarse para configurar un análisis o un almacén de búsqueda alternativos, por ejemplo, Análisis de transmisiones, ElasticSearch, un sistema de supervisión personalizado o su sistema de supervisión de terceros favorito.

Las siguientes son algunas configuraciones de ejemplo.

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

En el ejemplo siguiente, el receptor se aplica al nodo principal **PerformanceCounters** de la jerarquía, lo que significa que todos los nodos secundarios **PerformanceCounters** se enviarán a Centros de eventos.

        <PerformanceCounters scheduledTransferPeriod="PT1M">
          <PerformanceCounterConfiguration counterSpecifier="\Memory\Available MBytes" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\ISAPI Extension Requests/sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\Web Service(_Total)\Bytes Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Requests/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET Applications(__Total__)\Errors Total/Sec" sampleRate="PT3M" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Queued" sampleRate="PT3M" sinks="HotPath" />
          <PerformanceCounterConfiguration counterSpecifier="\ASP.NET\Requests Rejected" sampleRate="PT3M" sinks="HotPath"/>
          <PerformanceCounterConfiguration counterSpecifier="\Processor(_Total)\% Processor Time" sampleRate="PT3M" sinks="HotPath"/>
        </PerformanceCounters>

En el ejemplo mencionado anteriormente, el receptor se aplica solo a tres contadores: Solicitudes en cola, Solicitudes rechazadas y % de tiempo de procesador.

En el ejemplo siguiente se muestra cómo un desarrollador puede tomar el control sobre la cantidad de datos que se envían como métricas críticas usadas para el estado del servicio, y establecer unos límites sobre esta cantidad.

        <Logs scheduledTransferPeriod="PT1M" sinks="HotPath" scheduledTransferLogLevelFilter="Error" />

En este ejemplo, el receptor se aplica a los registros y se filtra solo para el seguimiento de nivel Error.
 
## Implementación y actualización de una aplicación de servicios en la nube y configuración de diagnósticos

El camino más fácil para implementar la aplicación junto con la configuración del receptor de Centros de eventos es usar Visual Studio. Para ver y realizar los cambios deseados, abra el archivo *wadcfgx* en Visual Studio, que se encuentra almacenado en el archivo *Proyecto de servicio de nube -> Roles -> (RoleName) -> diagnostics.wadcfgx* y guárdelo cuando haya terminado.

En este punto, todas las acciones de implementación y de actualización de implementaciones en comandos o scripts basados en Visual Studio, Visual Studio Team System o MSBUILD (mediante el destino /t:publish), incluirán el archivo *.wadcfgx* en el proceso de empaquetado y garantizarán que se implementa en Azure con la extensión de agente WAD adecuada que funciona con sus máquinas virtuales.
  
Tras una implementación correcta de la aplicación y la configuración de Diagnósticos de Azure, verá inmediatamente la actividad en el panel de Centros de eventos. Esto quiere decir que está listo para continuar y ver los datos de la ruta de acceso activa en el cliente de escucha o la herramienta de análisis de su elección.
 
En la siguiente ilustración, el panel de Centros de eventos muestra el envío correcto de datos de diagnóstico al centro de eventos, que comienza algo después de las 11 p.m., cuando la aplicación se implementó con un archivo *.wadcfgx* actualizado y el receptor configurado correctamente.

![][0]
  
>[AZURE.NOTE] Al realizar actualizaciones en el archivo de configuración de Diagnósticos de Azure (.wadcfgx), se recomienda insertar las actualizaciones en toda la aplicación, además de la configuración, mediante el script de publicación de Visual Studio o el script de Windows PowerShell.

## Visualización de los datos de la ruta de acceso activa

Como se explicó anteriormente, la escucha y el procesamiento de datos de Centros de eventos sirven a muchos fines.
  
Un enfoque sencillo es crear una pequeña aplicación de consola de prueba para escuchar el centro de eventos e imprimir el flujo de salida. El código siguiente (que se explica con más detalle en el artículo [Introducción a los Centros de eventos](./event-hubs-csharp-ephcs-getstarted.md)) se puede colocar en una aplicación de consola.

Tenga en cuenta que la aplicación de consola debe incluir el [paquete de Nuget EventProcessor](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost/).

No olvide sustituir los valores que aparecen entre corchetes angulares en la función **Main** siguiente por los valores de sus recursos.

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
	            string eventHubConnectionString = "Endpoint= <Your Connection String>”
	            string eventHubName = "<EventHub Name>";
	            string storageAccountName = "<Storage Account Name>";
	            string storageAccountKey = "<Storage Account Key>”;
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

## Solución de problemas del receptor de Centros de eventos

- El centro de eventos no muestra la actividad de eventos entrante o saliente esperada

	Compruebe que el centro de eventos esté correctamente aprovisionado. Toda la información de conexión de la sección **PrivateConfig** de *wadcfgx* debe coincidir con los valores de su recurso, tal como se muestra en el portal. Asegúrese de tener una directiva SAS definida ("SendRule" en el ejemplo) en el portal y que se haya concedido el permiso de envío.

- Después de realizar una actualización, el centro de eventos ya no muestra actividad de eventos entrante o saliente

	En primer lugar, asegúrese de que la información de configuración y del centro de eventos coincida como se ha indicado anteriormente. Algunos problemas se deben al restablecimiento de **PrivateConfig** en una actualización de la implementación. La solución recomendada consiste en realizar todos los cambios en *.wadcfgx* en el proyecto, y luego insertar una actualización completa de la aplicación. Si esto no es posible, asegúrese de que la actualización de diagnósticos inserta completamente **PrivateConfig**, lo que incluye la clave SAS.

- He probado lo anterior y el centro de eventos sigue sin funcionar

	Pruebe a mirar en la tabla de Almacenamiento de Azure que contiene registros y errores del servicio Diagnósticos de Azure propiamente dicho: **WADDiagnosticInfrastructureLogsTable**. Una opción es usar una herramienta como el [Explorador de almacenamiento de Azure](http://www.storageexplorer.com) para conectarse a esta cuenta de almacenamiento, ver esta tabla y agregar una consulta para TimeStamp en las últimas 24 horas. Puede utilizar la herramienta para exportar el archivo CSV y abrirlo en una aplicación como Microsoft Excel, ya que Excel facilita la búsqueda de cadenas de tarjeta de llamada como "EventHubs" para ver qué error se notifica.

## Pasos siguientes
[Más información sobre Centros de eventos](https://azure.microsoft.com/services/event-hubs/)

## Apéndice: Archivo de configuración completo de Diagnósticos de Azure (.wadcfgx): ejemplo
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
	    <StorageAccount />
	  </PublicConfig>
	  <PrivateConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
	    <StorageAccount name="" key="" endpoint="" />
	    <EventHub Url="https://diageventhub-py-ns.servicebus.windows.net/diageventhub-py" SharedAccessKeyName="SendRule" SharedAccessKey="YOUR_KEY_HERE" />
	  </PrivateConfig>
	  <IsEnabled>true</IsEnabled>
	</DiagnosticsConfiguration>

El archivo *ServiceConfiguration.Cloud.cscfg* complementario para este ejemplo se parece al siguiente.

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceConfiguration serviceName="MyFixItCloudService" xmlns="http://schemas.microsoft.com/ServiceHosting/2008/10/ServiceConfiguration" osFamily="3" osVersion="*" schemaVersion="2015-04.2.6">
	  <Role name="MyFixIt.WorkerRole">
	    <Instances count="1" />
	    <ConfigurationSettings>
	      <Setting name="Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString" value="YOUR_CONNECTION_STRING" />
	    </ConfigurationSettings>
	  </Role>
	</ServiceConfiguration>

<!-- Images. -->
[0]: ./media/event-hubs-streaming-azure-diags-data/dashboard.png

<!---HONumber=AcomDC_0608_2016-->