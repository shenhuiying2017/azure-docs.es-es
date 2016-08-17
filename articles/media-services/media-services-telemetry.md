<properties pageTitle="Telemetría de los servicios multimedia de Azure con .NET | Microsoft Azure" 
	description="Este artículo muestra cómo usar la telemetría de los Servicios multimedia de Azure." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/27/2016"   
	ms.author="juliako"/>

# Telemetría de los Servicios multimedia de Azure con .NET
 
## Información general

La telemetría y supervisión de los Servicios multimedia permite a los clientes de los mismos tener acceso a datos de métricas para sus servicios. La versión actual es compatible con los datos de telemetría para entidades Channel y StreamingEndpoint. Puede configurar la telemetría en la granularidad de nivel de componente. Hay dos niveles de detalle "Normal" y "Detallado". La versión actual solo admite "Normal".

La telemetría se escribe en una tabla de almacenamiento de una cuenta de Almacenamiento de Azure proporcionada por el cliente (la cuenta de Servicios multimedia tiene que tener adjunta una cuenta de almacenamiento). El sistema de telemetría creará una tabla independiente para cada día nuevo basada en 00:00 UTC. Por ejemplo "TelemetryMetrics20160321" donde "20160321" es la fecha de la tabla creada. Habrá una tabla independiente para cada día.

Tenga en cuenta que el sistema de telemetría no administra la retención de datos. Puede quitar los datos antiguos de telemetría eliminando las tablas de almacenamiento.

Puede utilizar los datos de telemetría de una de las maneras siguientes:

- Leer datos directamente desde Almacenamiento de tablas de Azure (por ejemplo, mediante el SDK de almacenamiento). Para la descripción de las tablas de almacenamiento de datos de telemetría, consulte la sección sobre **uso de información de telemetría** de [este](https://msdn.microsoft.com/library/mt742089.aspx) tema.

O

- Aproveche la compatibilidad del SDK de .NET de Servicios multimedia para leer los datos de almacenamiento. Este tema muestra cómo habilitar la telemetría de la cuenta de AMS especifica y cómo consultar las métricas usando el SDK de .NET de Servicios Multimedia de Azure.

## Configuración de telemetría para una cuenta de Servicios multimedia

Para habilitar la telemetría es necesario realizar los pasos siguientes:

- Obtener las credenciales de la cuenta de almacenamiento vinculada a la cuenta de Servicios multimedia.
- Crear un punto de conexión de notificación con **EndPointType** establecido en **AzureTable** y endPontAddress apuntando a la tabla de almacenamiento.

	    INotificationEndPoint notificationEndPoint = 
	                  _context.NotificationEndPoints.Create("monitoring", 
	                  NotificationEndPointType.AzureTable,
	                  "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/");

- Crear unos valores de configuración de supervisión para los servicios que desea supervisar. No se permite más que una configuración de supervisión.
  
        IMonitoringConfiguration monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
            new List<ComponentMonitoringSetting>()
            {
                new ComponentMonitoringSetting(MonitoringComponent.Channel, MonitoringLevel.Normal),
                new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
            });

## Uso de la información de telemetría

Los datos de telemetría se crean en una tabla de Almacenamiento de Azure de la cuenta de almacenamiento que especificó al configurar la telemetría para la cuenta de Servicios multimedia. El sistema de telemetría creará una tabla independiente para cada día nuevo basada en 00:00 UTC. Por ejemplo "TelemetryMetrics20160321" donde "20160321" es la fecha de la tabla creada. Habrá una tabla independiente para cada día.

Puede consultar las tablas de telemetría de la siguiente información de métricas. El ejemplo, que se muestra más adelante en este tema, muestra cómo utilizar el SDK de .NET de Servicios multimedia para consultar métricas.

### Registro StreamingEndpoint

Puede consultar las siguientes métricas de StreamingEndPoint.

Propiedad|Descripción|Valor de ejemplo
---|---|---
**PartitionKey**|Obtiene la clave de partición del registro.|60b71b0f6a0e4d869eb0645c16d708e1\_6efed125eef44fb5b61916edc80e6e23
**RowKey**|Obtiene la clave de fila del registro.|00959\_00000
**AccountId**|Obtiene el id. de cuenta de Servicios multimedia.|6efed125-eef4-4fb5-b619-16edc80e6e23
**StreamingEndpointId**|Obtiene el id. del punto de conexión de streaming de Servicios multimedia.|d17ec9e4-a5d4-033d-0c36-def70229f06f
**ObservedTime**|Obtiene la hora observada de la métrica.|1/20/16 23:44:01
**HostName**|Obtiene el nombre de host del punto de conexión de streaming.|builddemoserver.origin.mediaservices.windows.net
**StatusCode**|Obtiene el código de estado.|200
**ResultCode**|Obtiene el código de resultado.|S\_OK
**RequestCount**|Obtiene el número de solicitudes.|3
**BytesSent**|Obtiene los bytes enviados.|2987358
**ServerLatency**|Obtiene la latencia del servidor (incluido el almacenamiento).|129
**EndToEndLatency**|Obtiene el tiempo de solicitud de principio a fin.|250


### Latido de canal en directo

Puede consultar las siguientes métricas de canal en directo.

Propiedad|Descripción|Valor de ejemplo
---|---|---
**PartitionKey**|Obtiene la clave de partición del registro.|60b71b0f6a0e4d869eb0645c16d708e1\_0625cc45918e4f98acfc9a33e8066628
**RowKey**|Obtiene la clave de fila del registro.|13872\_00005
**AccountId**|Obtiene el id. de cuenta de Servicios multimedia.|6efed125-eef4-4fb5-b619-16edc80e6e23
**ChannelId**|Obtiene el id. de canal de Servicios multimedia.|
**ObservedTime**|Obtiene la hora observada de la métrica.|1/21/2016 20:08:49
**CustomAttributes**|Obtiene los atributos personalizados.|
**TrackType**|Obtiene el tipo de seguimiento.|video
**TrackName**|Obtiene el nombre de seguimiento.|video
**Bitrate**|Obtiene la velocidad de bits.|785000
**IncomingBitrate**|Obtiene la velocidad de bits entrante.|784548
**OverlapCount**|Obtiene el recuento de superposición.|0
**DiscontinuityCount**|Obtiene el recuento de discontinuidad.|0
**LastTimestamp**|Obtiene la última marca de tiempo.|1800488800
 
## Ejemplo  
	
El ejemplo siguiente muestra cómo habilitar la telemetría de la cuenta de AMS especifica y cómo consultar las métricas usando el SDK de .NET de Servicios Multimedia de Azure.

	using System;
	using System.Collections.Generic;
	using System.Configuration;
	using System.Linq;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace AMSMetrics
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	        private static readonly string _mediaServicesAccountID =
	            ConfigurationManager.AppSettings["MediaServicesAccountID"];
	        private static readonly string _streamingEndpointID =
	            ConfigurationManager.AppSettings["StreamingEndpointID"];
	        private static readonly string _mediaServicesStorageAccountName =
	            ConfigurationManager.AppSettings["StorageAccountName"];
	        private static readonly string _mediaServicesStorageAccountKey =
	            ConfigurationManager.AppSettings["StorageAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	

	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
		            INotificationEndPoint notificationEndPoint = 
		                          _context.NotificationEndPoints.Create("monitoring", 
								  NotificationEndPointType.AzureTable, GetTableEndPoint());

	                monitoringConfiguration = _context.MonitoringConfigurations.Create(notificationEndPoint.Id,
	                    new List<ComponentMonitoringSetting>()
	                    {
	                        new ComponentMonitoringSetting(MonitoringComponent.StreamingEndpoint, MonitoringLevel.Normal)
	                    });
	            }
	
	            //Print metrics for a Streaming Endpoint.
	            PrintStreamingEndpointMetrics();
	
	            Console.ReadLine();
	        }
	
	        private static string GetTableEndPoint()
	        {
	            return "https://" + _mediaServicesStorageAccountName + ".table.core.windows.net/";
	        }
	
	        private static void PrintStreamingEndpointMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some streaming endpoint metrics.
	            var res = _context.StreamingEndPointRequestLogs.GetStreamingEndPointMetrics(
	                    GetTableEndPoint(),
	                    _mediaServicesStorageAccountKey,
	                    _mediaServicesAccountID,
	                    _streamingEndpointID,
	                    start,
	                    end);
	
	
	            Console.Title = "Streaming endpoint metrics:";
	
	            foreach (var log in res)
	            {
	                Console.WriteLine("AccountId: {0}", log.AccountId);
	                Console.WriteLine("BytesSent: {0}", log.BytesSent);
	                Console.WriteLine("EndToEndLatency: {0}", log.EndToEndLatency);
	                Console.WriteLine("HostName: {0}", log.HostName);
	                Console.WriteLine("ObservedTime: {0}", log.ObservedTime);
	                Console.WriteLine("PartitionKey: {0}", log.PartitionKey);
	                Console.WriteLine("RequestCount: {0}", log.RequestCount);
	                Console.WriteLine("ResultCode: {0}", log.ResultCode);
	                Console.WriteLine("RowKey: {0}", log.RowKey);
	                Console.WriteLine("ServerLatency: {0}", log.ServerLatency);
	                Console.WriteLine("StatusCode: {0}", log.StatusCode);
	                Console.WriteLine("StreamingEndpointId: {0}", log.StreamingEndpointId);
	                Console.WriteLine();
	            }
	
	            Console.WriteLine();
	        }

	        private static void PrintChannelMetrics()
	        {
	            var end = DateTime.UtcNow;
	            var start = DateTime.UtcNow.AddHours(-5);
	
	            // Get some channel metrics.
	            var channelMetrics = _context.ChannelMetrics.GetChannelMetrics(
	                GetTableEndPoint(),
	                _mediaServicesStorageAccountKey,
	                _mediaServicesAccountName,
	                _context.Channels.FirstOrDefault().Id,
	                start,
	                end);
	
	            // Print the channel metrics.
	            Console.WriteLine("Channel metrics:");
	
	            foreach (var channelHeartbeat in channelMetrics.OrderBy(x => x.ObservedTime))
	            {
	                Console.WriteLine(
	                    "    Observed time: {0}, Last timestamp: {1}, Incoming bitrate: {2}",
	                    channelHeartbeat.ObservedTime,
	                    channelHeartbeat.LastTimestamp,
	                    channelHeartbeat.IncomingBitrate);
	            }
	
	            Console.WriteLine();
	        }
	    }
	}


##Envío de comentarios

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Paso siguiente
 
Consulte las rutas de aprendizaje de los Servicios multimedia de Azure para conocer las magníficas características ofrecidas por AMS.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

<!---HONumber=AcomDC_0803_2016-->