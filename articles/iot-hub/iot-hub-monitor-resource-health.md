---
title: "Supervisión del mantenimiento de Azure IoT Hub | Microsoft Docs"
description: "Uso de Azure Monitor y Azure Resource Health para supervisar IoT Hub y diagnosticar problemas rápidamente"
services: iot-hub
documentationcenter: 
author: kgremban
manager: timlt
editor: 
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/09/2017
ms.author: kgremban
ms.openlocfilehash: 3051af03d0c1433db98bcc674a072188e7ce80e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-the-health-of-azure-iot-hub-and-diagnose-problems-quickly"></a>Supervisión del mantenimiento de Azure IoT Hub y diagnóstico de problemas rápidamente

Las empresas que implementan Azure IoT Hub esperan que sus recursos ofrezcan un rendimiento confiable. Para ayudar a mantener una estrecha vigilancia de las operaciones, IoT Hub está totalmente integrado con [Azure Monitor][lnk-AM] y [Azure Resource Health][lnk-ARH]. Estos dos servicios funcionan de forma conjunta para proporcionar los datos necesarios para que las soluciones de IoT se ejecuten con un estado correcto. 

Azure Monitor es un único origen para supervisar y registrar todos los servicios de Azure. Puede enviar los registros que Azure Monitor genera a Log Analytics de OMS, Event Hubs o Azure Storage para un procesamiento personalizado. La configuración de métricas y diagnósticos de Azure Monitor ofrece una visibilidad en tiempo real del rendimiento de los recursos. Continúe leyendo este artículo para obtener información sobre cómo [usar Azure Monitor](#use-azure-monitor) con IoT Hub. 

Azure Resource Health ayuda a diagnosticar si un problema de Azure afecta a los recursos y, en su caso, a obtener soporte técnico. En un panel personalizado se muestra el estado de mantenimiento actual y anterior de los recursos de IoT Hub. Continúe leyendo este artículo para obtener información sobre cómo [usar Azure Resource Health](#use-azure-resource-health) con IoT Hub. 

Además de la integración con estos dos servicios, IoT Hub también proporciona sus propias métricas que puede utilizar para consultar el estado de los recursos de IoT. Para más información, vea [Comprender las métricas de IoT Hub][lnk-metrics].

## <a name="use-azure-monitor"></a>Uso de Azure Monitor

Azure Monitor proporciona información de diagnóstico a nivel de recurso, lo que significa que puede supervisar las operaciones que tienen lugar dentro de IoT Hub. 

La configuración de diagnóstico de Azure Monitor reemplaza al monitor de operaciones de IoT Hub. Si usa la supervisión de operaciones actualmente, debe migrar los flujos de trabajos. Para más información, vea [Migrate from operations monitoring to diagnostics settings][lnk-migrate] (Migración de la supervisión de operaciones a la configuración de diagnóstico).

Para más información sobre las métricas y los eventos específicos que Azure Monitor supervisa, vea [Métricas compatibles con Azure Monitor][lnk-AM-metrics] y [Servicios, esquemas y categorías admitidos en los registros de diagnóstico de Azure][lnk-AM-schemas].

[!INCLUDE [iot-hub-diagnostics-settings](../../includes/iot-hub-diagnostics-settings.md)]

### <a name="understand-the-logs"></a>Descripción de los registros

Azure Monitor supervisa diferentes operaciones que tienen lugar en IoT Hub. Cada categoría tiene un esquema que define cómo se notifican los eventos de la categoría de que se trate. 

#### <a name="connections"></a>Conexiones

La categoría Conexiones supervisa los errores que se producen cuando los dispositivos se conectan o desconectan de un centro de IoT. El seguimiento de esta categoría resulta útil para identificar intentos de conexión no autorizados y para realizar el seguimiento de las situaciones en que una conexión se pierde para los dispositivos en las áreas de conectividad deficiente.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Information",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}", 
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-commands"></a>Comandos de nube a dispositivo

La categoría de comandos de nube a dispositivo realiza el seguimiento de los errores que se producen en el centro de IoT y se relacionan con la canalización de mensajes de nube a dispositivo. Esta categoría incluye errores que se producen al enviar mensajes de nube a dispositivo (por ejemplo, un remitente no autorizado), recibir mensajes de nube a dispositivo (por ejemplo, el número de entregas superado) y recibir mensajes de nube a dispositivo (por ejemplo, comentarios expirados). Esta categoría no detecta errores de un dispositivo que trata incorrectamente un mensaje de nube a dispositivo si este se ha entregado correctamente.

```json
{
    "time": " UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "messageExpired",
    "category": "C2DCommands",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddresss\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="device-identity-operations"></a>Operaciones de identidad de dispositivos

La categoría de operaciones de identidad de dispositivo supervisa los errores que se producen cuando se intenta crear, actualizar o eliminar una entrada en el registro de identidades de IoT Hub. El seguimiento de esta categoría resulta útil para los escenarios de aprovisionamiento.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "get",
    "category": "DeviceIdentityOperations",
    "level": "Error",    
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
    "location": "Resource location"
}
```

#### <a name="routes"></a>Rutas

La categoría de enrutamiento de mensajes realiza un seguimiento de los errores que se producen durante la evaluación de este proceso y el estado del punto de conexión según lo que observa IoT Hub. Esta categoría incluye eventos, como cuando una regla se evalúa como "sin definir", cuando IoT Hub marca un punto de conexión como inactivo y todos los errores recibidos de un punto de conexión. Esta categoría no incluye errores específicos de los mensajes (por ejemplo, de limitación del dispositivo), que se notifican en la categoría de telemetría de dispositivo.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "endpointUnhealthy",
    "category": "Routes",
    "level": "Error",
    "properties": "{\"deviceId\": \"<deviceId>\",\"endpointName\":\"<endpointName>\",\"messageId\":<messageId>,\"details\":\"<errorDetails>\",\"routeName\": \"<routeName>\"}",
    "location": "Resource location"
}
```

#### <a name="device-telemetry"></a>Telemetría de dispositivo

La categoría de telemetría del dispositivo realiza el seguimiento de los errores que se producen en el centro de IoT y se relacionan con la canalización de telemetría. Esta categoría incluye los errores que se producen al enviar eventos de telemetría (por ejemplo, la limitación) y recibir eventos de telemetría (por ejemplo, un lector no autorizado). Esta categoría no puede detectar los errores causados por el código que se ejecuta en el propio dispositivo.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
    "location": "Resource location"
}
```

#### <a name="file-upload-operations"></a>Operaciones de carga de archivos

La categoría de carga de archivos supervisa los errores que se producen en el centro de IoT y está relacionada con la funcionalidad de carga de archivos. Esta categoría incluye lo siguiente:

* Errores que se producen con el URI de SAS, como cuando caduca antes de que un dispositivo notifique al centro una carga completada.
* Cargas con errores notificadas por el dispositivo.
* Errores que se producen cuando no se encuentra un archivo en el almacenamiento durante la creación del mensaje de notificación de IoT Hub.

Esta categoría no puede detectar los errores que se producen directamente mientras el dispositivo está cargando un archivo a Storage.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "ingress",
    "category": "FileUploadOperations",
    "level": "Error",
    "resultType": "Event status",
    "resultDescription": "MessageDescription",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
    "location": "Resource location"
}
```

#### <a name="cloud-to-device-twin-operations"></a>Operaciones gemelas de la nube al dispositivo

La categoría de operaciones gemelas de la nube al dispositivo supervisa los eventos iniciados por el servicio en dispositivos gemelos. Estas operaciones pueden incluir la opción de obtener operaciones gemelas, actualizar las propiedades notificadas y suscribirse a las propiedades deseadas.

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "read",
    "category": "C2DTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="device-to-cloud-twin-operations"></a>Operaciones gemelas del dispositivo a la nube

La categoría de operaciones gemelas del dispositivo a la nube supervisa los eventos iniciados por el dispositivo en dispositivos gemelos. Estas operaciones pueden incluir la opción de obtener operaciones gemelas y actualizar o reemplazar etiquetas y propiedades. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "update",
    "category": "D2CTwinOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}", 
    "location": "Resource location"
}
```

#### <a name="twin-queries"></a>Consultas gemelas

La categoría de consultas gemelas informa sobre las solicitudes de consulta de los dispositivos gemelos que se inician en la nube. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "query",
    "category": "TwinQueries",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}", 
    "location": "Resource location"
}
```

#### <a name="jobs-operations"></a>Operaciones de trabajos

La categoría de operaciones de trabajos informa sobre las solicitudes de trabajo para actualizar los dispositivos gemelos o invocar métodos directos en varios dispositivos. Estas solicitudes se inician en la nube. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "jobCompleted",
    "category": "JobsOperations",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}", 
    "location": "Resource location"
}
```

#### <a name="direct-methods"></a>Métodos directos

La categoría de métodos directos realiza un seguimiento de las interacciones de solicitud y respuesta enviadas a dispositivos individuales. Estas solicitudes se inician en la nube. 

```json
{
    "time": "UTC timestamp",
    "resourceId": "Resource Id",
    "operationName": "send",
    "category": "DirectMethods",
    "level": "Information",
    "durationMs": "1",
    "properties": "{\"deviceId\":\"<deviceId>\", \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}", 
    "location": "Resource location"
}
```

### <a name="read-logs-from-azure-event-hubs"></a>Lectura de registros de Azure Event Hubs

Una vez configurados los registros mediante la configuración de diagnóstico, puede crear aplicaciones que lean los registros, a fin de poder actuar en función de la información que contienen. Este código de ejemplo recupera registros de un centro de eventos:

```
class Program 
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}"; 
    static string monitoringEndpointName = "{your AMS event hub endpoint name}"; 
    static EventHubClient eventHubClient; 
//This is the Diagnostic Settings schema 
    class AzureMonitorDiagnosticLog 
    { 
        string time { get; set; } 
        string resourceId { get; set; } 
        string operationName { get; set; } 
        string category { get; set; } 
        string level { get; set; } 
        string resultType { get; set; } 
        string resultDescription { get; set; } 
        string durationMs { get; set; } 
        string callerIpAddress { get; set; } 
        string correlationId { get; set; } 
        string identity { get; set; } 
        string location { get; set; } 
        Dictionary<string, string> properties { get; set; } 
    }; 
    static void Main(string[] args) 
    { 
        Console.WriteLine("Monitoring. Press Enter key to exit.\n"); 
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName); 
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds; 
        CancellationTokenSource cts = new CancellationTokenSource(); 
        var tasks = new List<Task>(); 
        foreach (string partition in d2cPartitions) 
        { 
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token)); 
        } 
        Console.ReadLine(); 
        Console.WriteLine("Exiting..."); 
        cts.Cancel(); 
        Task.WaitAll(tasks.ToArray()); 
    } 
    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct) 
    { 
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow); 
        while (true) 
        { 
            if (ct.IsCancellationRequested) 
            { 
                await eventHubReceiver.CloseAsync(); 
                break; 
            } 
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10)); 
            if (eventData != null) 
            { 
                string data = Encoding.UTF8.GetString(eventData.GetBytes()); 
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data); 
                var deserializer = new JavaScriptSerializer(); 
                //deserialize json data to azure monitor object 
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result); 
 
            } 
        } 
    } 
} 
```

## <a name="use-azure-resource-health"></a>Uso de Azure Resource Health

Use Azure Resource Health para supervisar si IoT Hub está en funcionamiento. También permite saber si una interrupción regional está afectando al mantenimiento de IoT Hub. Para conocer los detalles específicos sobre el estado de mantenimiento de Azure IoT Hub, se recomienda [utilizar Azure Monitor](#use-azure-monitor). 

Azure IoT Hub indica el estado de mantenimiento a nivel regional. Si hay alguna interrupción regional que afecta a IoT Hub, el estado de mantenimiento aparece como **Desconocido**. Para más información sobre las comprobaciones de estado específicas que Azure Resource Health realiza, vea [Tipos de recursos y comprobaciones de estado en Azure Resource Health][lnk-ARH-checks].

Para comprobar el mantenimiento de los recursos de IoT Hub, siga estos pasos:

1. Inicie sesión en el [Portal de Azure](https://portal.azure.com).
1. Vaya a **Estado del servicio** > **Resource Health**.
1. En los cuadros desplegables, seleccione la suscripción y después **IoT Hub**.

Para más información sobre cómo interpretar los datos de estado, vea [Información general sobre Azure Resource Health][lnk-ARH]

## <a name="next-steps"></a>Pasos siguientes

- [Comprender las métricas de IoT Hub][lnk-metrics]
- [Supervisión remota y notificaciones de IoT con Azure Logic Apps conectando IoT Hub y el buzón de correo][lnk-monitoring-notifications]


[lnk-AM]: ../monitoring-and-diagnostics/index.yml
[lnk-ARH]: ../service-health/resource-health-overview.md
[lnk-metrics]: iot-hub-metrics.md
[lnk-migrate]: iot-hub-migrate-to-diagnostics-settings.md
[lnk-AM-metrics]: ../monitoring-and-diagnostics/monitoring-supported-metrics.md
[lnk-AM-schemas]: ../monitoring-and-diagnostics/monitoring-diagnostic-logs-schema.md
[lnk-ARH-checks]: ../service-health/resource-health-checks-resource-types.md
[lnk-monitoring-notifications]: iot-hub-monitoring-notifications-with-azure-logic-apps.md