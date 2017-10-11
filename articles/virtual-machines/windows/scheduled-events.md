---
title: Eventos programados para VM Windows en Azure| Microsoft Docs
description: "Eventos programados mediante el servicio Azure Metadata para las máquinas virtuales Windows."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: 7198fa8d1a512d10ca7022078aa2ea7bde3a4c02
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2017
---
# <a name="azure-metadata-service-scheduled-events-preview-for-windows-vms"></a>Servicio Azure Metadata: eventos programados (versión preliminar) para VM Windows

> [!NOTE] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Scheduled Events es uno de los subservicios de Azure Metadata Service. Se encarga de mostrar información relacionada con eventos próximos (por ejemplo, un reinicio) para que la aplicación pueda prepararse para ellos y limitar las interrupciones. Está disponible para todos los tipos de máquina virtual de Azure, incluso para IaaS y PaaS. Scheduled Events permite que la máquina virtual tenga tiempo para realizar tareas de prevención y minimizar el efecto de un evento. 

Scheduled Events está disponible para máquinas virtuales Linux y Windows. Para más información acerca de Scheduled Events en Linux, consulte [Scheduled Events para máquinas virtuales Linux](../windows/scheduled-events.md).

## <a name="why-scheduled-events"></a>¿Por qué Scheduled Events?

Con Scheduled Events, puede tomar medidas para limitar el impacto del mantenimiento iniciado por la plataforma o de las acciones iniciadas por usuarios en el servicio. 

Las cargas de trabajo de varias instancias, que usan técnicas de replicación para mantener el estado, pueden ser vulnerables a las interrupciones que se producen en varias instancias. Esas interrupciones pueden dar lugar a tareas costosas (por ejemplo, volver a elaborar los índices) o, incluso, a una pérdida de las réplicas. 

En muchos otros casos, la disponibilidad global de los servicios puede mejorarse realizando una secuencia de apagado estable, por ejemplo, completando transacciones en curso (o cancelándolas), reasignando tareas a otras máquinas virtuales del clúster (conmutación por error manual) o eliminando la máquina virtual de un grupo de equilibradores de carga de red. 

Hay casos en los que notificar a un administrador sobre un evento próximo o registrar dicho evento puede mejorar el mantenimiento de las aplicaciones hospedadas en la nube.

Azure Metadata Service muestra Eventos programados en los siguientes casos de uso:
-   Mantenimiento iniciado por la plataforma (por ejemplo, la implementación del SO del host)
-   Llamadas iniciadas por el usuario (por ejemplo, si el usuario reinicia una VM o la vuelve a implementar)


## <a name="the-basics"></a>Conceptos básicos  

Azure Metadata Service expone información sobre la ejecución de máquinas virtuales mediante un punto de conexión de REST accesible desde la propia máquina virtual. La información se encuentra disponible a través de una dirección IP no enrutable, de modo que no se expone fuera de la máquina virtual.

### <a name="scope"></a>Scope
Los eventos programados se presentan a todas las máquinas virtuales en un servicio en la nube o a todas las máquinas virtuales en un conjunto de disponibilidad. Por ello, debería revisar el campo `Resources` del evento para identificar cuáles son las máquinas virtuales que se verán afectadas. 

### <a name="discovering-the-endpoint"></a>Detección del punto de conexión
Al crear una máquina virtual dentro de una red virtual (VNet), Metadata Service está disponible desde una dirección IP no enrutable (`169.254.169.254`).
Si la máquina virtual no se crea dentro de una red virtual (lo habitual para servicios en la nube y VM clásicas), se necesita una lógica adicional para detectar el punto de conexión que vaya a usarse. Consulte esta muestra para obtener información sobre cómo [descubrir el punto de conexión de host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="versioning"></a>Control de versiones 
El servicio de metadatos de instancia tiene versiones. Las versiones son obligatorias y la versión actual es la `2017-03-01`.

> [!NOTE] 
> Las versiones preliminares de eventos programados compatibles {más reciente} como la versión de api. Este formato ya no es compatible y dejará de utilizarse en el futuro.

### <a name="using-headers"></a>Uso de encabezados
Al realizar consultas a Metadata Service, debe proporcionar el encabezado `Metadata: true` para asegurarse de que la solicitud no se haya redirigido de manera involuntaria.

### <a name="enabling-scheduled-events"></a>Habilitación de eventos programados
La primera vez que efectúe una solicitud de eventos programados, Azure habilita de manera implícita la característica en la máquina virtual. Como resultado, debe esperar una respuesta diferida de hasta dos minutos en la primera llamada.

### <a name="user-initiated-maintenance"></a>Mantenimiento iniciado por el usuario
El mantenimiento de máquina virtual iniciado por el usuario a través de Azure Portal, API, CLI o PowerShell da lugar a un evento programado. Esto permite probar la lógica de preparación de mantenimiento en su aplicación. Asimismo, permite a su aplicación prepararse para el mantenimiento iniciado por el usuario.

Si se reinicia una máquina virtual, se programa un evento con el tipo `Reboot`. Si vuelve a implementar una máquina virtual, se programa un evento con el tipo `Redeploy`.

> [!NOTE] 
> Actualmente se puede programar sumultáneamente un máximo de 10 operaciones de mantenimiento iniciadas por el usuario. Este límite será más flexible antes de la disponibilidad general de eventos programados.

> [!NOTE] 
> Actualmente no se puede configurar ningún mantenimiento iniciado por el usuario que dé lugar a eventos programados. Está planeado que esta capacidad de configuración se lance en el futuro.

## <a name="using-the-api"></a>Uso de la API

### <a name="query-for-events"></a>Consulta de eventos
Puede consultar los eventos programados; para ello, simplemente haga la siguiente llamada:

```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

Una respuesta contiene una matriz de eventos programados. Una matriz vacía significa que actualmente no hay eventos programados.
En caso de que haya eventos programados, la respuesta contiene una matriz de eventos: 
```
{
    "DocumentIncarnation": {IncarnationID},
    "Events": [
        {
            "EventId": {eventID},
            "EventType": "Reboot" | "Redeploy" | "Freeze",
            "ResourceType": "VirtualMachine",
            "Resources": [{resourceName}],
            "EventStatus": "Scheduled" | "Started",
            "NotBefore": {timeInUTC},              
        }
    ]
}
```

### <a name="event-properties"></a>Propiedades de evento
|Propiedad  |  Descripción |
| - | - |
| EventId | Es un identificador único global del evento. <br><br> Ejemplo: <br><ul><li>602d9444-d2cd-49c7-8624-8643e7171297  |
| EventType | Es el impacto causado por el evento. <br><br> Valores: <br><ul><li> `Freeze`: la máquina virtual está programada para pausarse durante unos segundos. La CPU entra en estado de suspensión, pero esto no afecta a la memoria, a los archivos abiertos ni a las conexiones de red. <li>`Reboot`: la máquina virtual está programada para reiniciarse (se borrará la memoria no persistente). <li>`Redeploy`: la máquina virtual está programada para moverse a otro nodo (los discos efímeros se pierden). |
| ResourceType | Es el tipo de recurso al que afecta este evento. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Es la lista de recursos a la que afecta este evento. Se garantiza que contenga máquinas de un [dominio de actualización](manage-availability.md) como máximo, pero puede no contener todas las máquinas en el dominio. <br><br> Ejemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| Estado de evento | Es el estado de este evento. <br><br> Valores: <ul><li>`Scheduled`: este evento está programado para iniciarse después de la hora especificada en la propiedad `NotBefore`.<li>`Started`: este evento se ha iniciado.</ul> Nunca se proporcionan `Completed` ni ningún estado similar; el evento ya no se devolverá cuando finalice.
| NotBefore| Hora a partir de la que puede iniciarse este evento. <br><br> Ejemplo: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Programación de eventos
Cada evento se programa una cantidad mínima de tiempo en el futuro en función del tipo de evento. Este tiempo se refleja en la propiedad `NotBefore` de un evento. 

|EventType  | Minimum Notice |
| - | - |
| Freeze| 15 minutos |
| Reboot | 15 minutos |
| Volver a implementar | 10 minutos |

### <a name="starting-an-event"></a>Inicio de un evento 

Una vez que se haya enterado de un evento próximo y que haya completado la lógica para un apagado estable, puede aprobar el evento pendiente mediante una llamada de `POST` a Metadata Service con `EventId`. Esta indica a Azure que puede acortar el tiempo de notificación mínimo (cuando sea posible). 

```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01
```

> [!NOTE] 
> Reconocer un evento permite a este continuar para todos sus elementos `Resources`, no solo para la máquina virtual que lo reconoce. Por tanto, puede optar por elegir un líder para que coordine el reconocimiento. Este puede ser tan sencillo como la propia máquina del campo `Resources`.


## <a name="powershell-sample"></a>Ejemplo de PowerShell 

En el siguiente ejemplo se realiza una consulta a Metadata Service para eventos programados y se aprueban todos los eventos pendientes.

```PowerShell
# How to get scheduled events 
function GetScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function ApproveScheduledEvent($eventId, $docIncarnation, $uri)
{    
    # Create the Scheduled Events Approval Document
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests; "DocumentIncarnation" = $docIncarnation} 
    
    # Convert to JSON string
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

function HandleScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

# Get events
$scheduledEvents = GetScheduledEvents $scheduledEventURI

# Handle events however is best for your service
HandleScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        ApproveScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>Ejemplo de C\# 

El siguiente ejemplo corresponde a un cliente sencillo que se comunica con Metadata Service.

```csharp
public class ScheduledEventsClient
{
    private readonly string scheduledEventsEndpoint;
    private readonly string defaultIpAddress = "169.254.169.254"; 

    // Set up the scheduled events URI for a VNET-enabled VM
    public ScheduledEventsClient()
    {
        scheduledEventsEndpoint = string.Format("http://{0}/metadata/scheduledevents?api-version=2017-03-01", defaultIpAddress);
    }

    // Get events
    public string GetScheduledEvents()
    {
        Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
        using (var webClient = new WebClient())
        {
            webClient.Headers.Add("Metadata", "true");
            return webClient.DownloadString(cloudControlUri);
        }   
    }

    // Approve events
    public void ApproveScheduledEvents(string jsonPost)
    {
        using (var webClient = new WebClient())
        {
            webClient.Headers.Add("Content-Type", "application/json");
            webClient.UploadString(scheduledEventsEndpoint, jsonPost);
        }
    }
}
```

Scheduled Events puede representarse mediante las siguientes estructuras de datos:

```csharp
public class ScheduledEventsDocument
{
    public string DocumentIncarnation;
    public List<CloudControlEvent> Events { get; set; }
}

public class CloudControlEvent
{
    public string EventId { get; set; }
    public string EventStatus { get; set; }
    public string EventType { get; set; }
    public string ResourceType { get; set; }
    public List<string> Resources { get; set; }
    public DateTime? NotBefore { get; set; }
}

public class ScheduledEventsApproval
{
    public string DocumentIncarnation;
    public List<StartRequest> StartRequests = new List<StartRequest>();
}

public class StartRequest
{
    [JsonProperty("EventId")]
    private string eventId;

    public StartRequest(string eventId)
    {
        this.eventId = eventId;
    }
}
```

En el siguiente ejemplo se realiza una consulta a Metadata Service para eventos programados y se aprueban todos los eventos pendientes.

```csharp
public class Program
{
    static ScheduledEventsClient client;

    static void Main(string[] args)
    {
        client = new ScheduledEventsClient();

        while (true)
        {
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval()
            {
                DocumentIncarnation = scheduledEventsDocument.DocumentIncarnation
            };
        
            foreach (CloudControlEvent event in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(event.EventId));
            }

            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.ApproveScheduledEvents(approveEventsJsonDocument);
            }

            Console.WriteLine("Complete. Press enter to repeat\n\n");
            Console.ReadLine();
            Console.Clear();
        }
    }

    private static void HandleEvents(List<CloudControlEvent> events)
    {
        // Add logic for handling events here
    }
}
```

## <a name="python-sample"></a>Ejemplo de Python 

En el siguiente ejemplo se realiza una consulta a Metadata Service para eventos programados y se aprueban todos los eventos pendientes.

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-03-01"
headers = "{Metadata:true}"
this_host = socket.gethostname()

def get_scheduled_events():
   req = urllib2.Request(metadata_url)
   req.add_header('Metadata', 'true')
   resp = urllib2.urlopen(req)
   data = json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid = evt['EventId']
        status = evt['EventStatus']
        resources = evt['Resources']
        eventtype = evt['EventType']
        resourcetype = evt['ResourceType']
        notbefore = evt['NotBefore'].replace(" ","_")
        if this_host in resources:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            # Add logic for handling events here

def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)
   
if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>Pasos siguientes 

- En [Instance Metadata Service](instance-metadata-service.md) (Servicio Instance Metadata), puede obtener más información sobre las API disponibles.
- Obtenga información sobre cómo realizar [el mantenimiento planeado para máquinas virtuales Windows en Azure](planned-maintenance.md).

