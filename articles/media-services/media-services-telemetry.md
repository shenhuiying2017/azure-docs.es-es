<properties pageTitle="Telemetría de los servicios multimedia de Azure con .NET | Microsoft Azure" 
	description="Este artículo muestra cómo usar la telemetría de los Servicios multimedia de Azure." 
	services="" 
	documentationCenter=""
	authors="juliako" />

# Telemetría de los Servicios multimedia de Azure con .NET

## Información general

La telemetría y supervisión de los Servicios multimedia permite a los clientes de los mismos tener acceso a datos de métricas para sus servicios. La versión actual es compatible con los datos de telemetría para entidades "Channel" y "StreamingEndpoint". Puede configurar la telemetría en la granularidad de nivel de componente. Hay dos niveles de detalle "Normal" y "Detallado". La versión actual solo admite "Normal".

La telemetría se escribe en una cuenta de Almacenamiento de Azure proporcionada por el cliente (la cuenta de Servicios multimedia tiene que tener adjunta una cuenta de almacenamiento). Se escribe en una tabla de Almacenamiento de Azure en la cuenta de almacenamiento especificada. El sistema de telemetría creará una tabla independiente para cada día nuevo basada en 00:00 UTC. Por ejemplo "TelemetryMetrics20160321" donde "20160321" es la fecha de la tabla creada. Habrá una tabla independiente para cada día.

El sistema de telemetría no proporciona retención de datos ni eliminación automática de registros antiguos. Por ello es necesario que sea usted quien administre y elimine los registros antiguos. Tener tablas separadas para cada día facilita la eliminación de los registros antiguos. No tiene más que eliminar las tablas antiguas.

Este tema muestra cómo habilitar la telemetría de los servicios especificados de AMS y cómo consultar las métricas usando .NET.

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


## Registro StreamingEndpoint

###Métricas disponibles

Puede consultar las siguientes métricas de StreamingEndPoint.

- **PartitionKey** obtiene la clave de partición del registro.
- **RowKey** obtiene la clave de fila del registro.
- **AccountID** obtiene el ID de cuenta de Servicios multimedia.
- **AccountID** obtiene el ID del punto de conexión de streaming de Servicios multimedia.
- **ObservedTime** obtiene la hora observada de la métrica.
- **HostName** obtiene el nombre de host del punto de conexión de streaming.
- **StatusCode** obtiene el código de estado.
- **ResultCode** obtiene el código de resultado.
- **RequestCount** obtiene el número de solicitudes.
- **BytesSent** obtiene los bytes enviados.
- **BytesSent** obtiene la latencia del servidor.
- **BytesSent** obtiene el tiempo de solicitud de principio a fin.

###Ejemplo de resultado de consulta de punto de conexión de streaming

![Consulta de punto de conexión de streaming](media/media-services-telemetry/media-services-telemetry01.png)


## Latido de canal en directo

###Métricas disponibles

Puede consultar las siguientes métricas de canal en directo.

- **PartitionKey** obtiene la clave de partición del registro.
- **RowKey** obtiene la clave de fila del registro.
- **AccountID** obtiene el ID de cuenta de Servicios multimedia.
- **ChannelID** obtiene el ID de canal de Servicios multimedia.
- **ObservedTime** obtiene la hora observada de la métrica.
- **CustomAttributes** obtiene los atributos personalizados.
- **TrackType** obtiene el tipo de seguimiento.
- **TrackType** obtiene el nombre de seguimiento.
- **Bitrate** obtiene la velocidad de bits.
- **IncomingBitrate** obtiene la velocidad de bits entrante.
- **OverlapCount** obtiene el recuento de superposición.
- **DiscontinuityCount** obtiene el recuento de discontinuidad.
- **LastTimestamp** obtiene la última marca de tiempo.
 
###Ejemplo del resultado de consulta de canal en vivo

![Consulta de punto de conexión de streaming](media/media-services-telemetry/media-services-telemetry01.png)

## Ejemplo de métricas de StreamingEndpoint
		
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
	
	            INotificationEndPoint notificationEndPoint = 
	                          _context.NotificationEndPoints.Create("monitoring", NotificationEndPointType.AzureTable, GetTableEndPoint());
	
	            var monitoringConfigurations = _context.MonitoringConfigurations;
	            IMonitoringConfiguration monitoringConfiguration = null;
	
	            // No more than one monitoring configuration settings is allowed.
	            if (monitoringConfigurations.ToArray().Length != 0)
	            {
	                monitoringConfiguration = _context.MonitoringConfigurations.FirstOrDefault();
	            }
	            else
	            {
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

<!---HONumber=AcomDC_0713_2016-->