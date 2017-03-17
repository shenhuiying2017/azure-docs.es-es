---
title: Eventos programados con Azure Metadata Service| Microsoft Docs
description: "Reaccione ante eventos de alto impacto en la máquina virtual antes de que ocurran."
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
ms.date: 12/10/2016
ms.author: zivr
translationtype: Human Translation
ms.sourcegitcommit: f67ff1cc18b23c3d4006121e266a5d96933e49bf
ms.openlocfilehash: d6c4d0b9f9cbf05341d30fe75e9828d0237a1962
ms.lasthandoff: 03/02/2017


---
# <a name="azure-metadata-service---scheduled-events-preview"></a>Azure Metadata Service: eventos programados (versión preliminar)

> [!NOTE] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para más información, consulte [Términos de uso complementarios de las Vistas Previas de Microsoft Azure.] (https://azure.microsoft.com/en-us/support/legal/preview-supplemental-terms/)
>

Eventos programados es uno de los servicios secundarios bajo Azure Metadata Service que muestra información relacionada con próximos eventos (por ejemplo, un reinicio) para que la aplicación se pueda preparar y limitar la interrupción. Está disponible para todos los tipos de máquina virtual de Azure, incluso para IaaS y PaaS. Eventos programados permite que la máquina virtual tenga tiempo para realizar tareas de prevención y minimizar el efecto de un evento. 


## <a name="introduction---why-scheduled-events"></a>Introducción: ¿Por qué Eventos programados?

Con Eventos programados, puede tomar medidas para limitar el impacto en el servicio. Las cargas de trabajo de varias instancias, que usan técnicas de replicación para mantener el estado, pueden ser vulnerables a las interrupciones frecuentes que se producen en varias instancias. Esas interrupciones pueden dar lugar a tareas costosas (por ejemplo, volver a elaborar los índices) o, incluso, a una pérdida de las réplicas. En muchos otros casos, usar una secuencia de cierre estable mejora la disponibilidad general del servicio. Por ejemplo, completar (o cancelar) transacciones en curso, reasignar otras tareas a otras máquinas virtuales en el clúster (conmutación por error manual) o quitar la máquina virtual de un grupo de equilibradores de carga. Hay casos en los que notificar a un administrador sobre un evento próximo o incluso simplemente registrar dicho evento puede mejorar el mantenimiento de las aplicaciones hospedadas en la nube.
Azure Metadata Service muestra Eventos programados en los siguientes casos de uso:
-    Mantenimiento iniciado por la plataforma (por ejemplo, la implementación del SO del host)
-    Llamadas iniciadas por el usuario (por ejemplo, el usuario reinicia o vuelve a implementar una VM)


## <a name="scheduled-events---the-basics"></a>Eventos programados: conceptos básicos  

Azure Metadata Service expone información sobre las máquinas virtuales en ejecución mediante un punto de conexión de REST desde dentro de la máquina virtual. La información se encuentra disponible a través de una dirección IP no enrutable, de modo que no se expone fuera de la máquina virtual.

### <a name="scope"></a>Scope
Los eventos programados se presentan a todas las máquinas virtuales en un servicio en la nube o a todas las máquinas virtuales en un conjunto de disponibilidad. Como resultado, debe revisar el campo **Recursos** del evento para identificar cuáles son las máquinas virtuales que se verán afectadas. 

### <a name="discover-the-endpoint"></a>Detección del punto de conexión
En el caso donde se crea una máquina virtual dentro de una red virtual (VNet), el servicio de metadatos está disponible en la dirección IP no enrutable 169.254.169.254. De lo contrario, en los casos predeterminados para los servicios en la nube y las VM clásicas, es necesaria una lógica adicional para detectar el punto de conexión que se va a utilizar. Consulte este ejemplo para aprender a [detectar el punto de conexión del host.] (https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm)

### <a name="versioning"></a>Control de versiones 
El servicio de metadatos usa una API con versión en el formato siguiente: http://{dirección ip}/metadata/{versión}/scheduledevents. Se recomienda que el servicio use la versión más reciente disponible en: http://{dirección ip}/metadata/latest/scheduledevents

### <a name="using-headers"></a>Uso de encabezados
Cuando consulta el Servicio de metadatos, debe proporcionar el siguiente encabezado*Metadata: true*. 

### <a name="enable-scheduled-events"></a>Habilitación de eventos programados
La primera vez que llama a los eventos programados, Azure habilita de manera implícita la característica en la máquina virtual. Como resultado, debe esperar una respuesta diferida de hasta dos minutos en la primera llamada.

### <a name="testing-your-logic-with-user-initiated-operations"></a>Prueba de la lógica con operaciones iniciadas por el usuario
Para probar la lógica, puede usar Azure Portal, la API, la CLI o PowerShell para iniciar operaciones que dan lugar a eventos programados. El reinicio de una máquina virtual da lugar a un evento programado con un tipo de evento igual a Reboot. La nueva implementación de una máquina virtual da lugar a un evento programado con un tipo de evento igual a Redeploy.
En ambos casos, la operación iniciada por el usuario tarda más tiempo en completarse, porque los eventos programados dan más tiempo a una aplicación para que se cierre correctamente. 

## <a name="using-the-api"></a>Uso de la API

### <a name="query-for-events"></a>Consulta de eventos
Puede consultar los eventos programados; para ello, simplemente haga la siguiente llamada

    curl -H Metadata:true http://169.254.169.254/metadata/latest/scheduledevents

Una respuesta contiene una matriz de eventos programados. Una matriz vacía significa que actualmente no hay eventos programados.
En caso de que haya eventos programados, la respuesta contiene una matriz de eventos: 

    {
     "DocumentIncarnation":{IncarnationID},
     "Events":[
          {
                "EventId":{eventID},
                "EventType":"Reboot" | "Redeploy" | "Freeze",
                "ResourceType":"VirtualMachine",
                "Resources":[{resourceName}],
                "EventStatus":"Scheduled" | "Started",
                "NotBefore":{timeInUTC},              
         }
     ]
    }

EventType captura el impacto esperado en la máquina virtual donde:
- Freeze: la máquina virtual está programada para pausarse durante unos segundos. No hay ningún impacto en la memoria, los archivos abiertos ni las conexiones de red.
- Reboot: la máquina virtual está programada para reiniciarse (se borra la memoria).
- Redeploy: la máquina virtual está programada para moverse a otro nodo (el disco efímero se pierde). 

Cuando un eventos está programado (Status = Scheduled), Azure comparte el tiempo después del cual se puede iniciar el evento (especificado en el campo NotBefore).

### <a name="starting-an-event-expedite"></a>Inicio de un evento (acelerar)

Una vez que se entera de un evento próximo y que completa la lógica para un cierre estable, puede indicar a Azure que se mueva más rápido (cuando sea posible) mediante una llamada **POST** 
 

## <a name="powershell-sample"></a>Ejemplo de PowerShell 

El ejemplo siguiente lee el servidor de metadatos de los eventos programados y los aprueba.

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
function ApproveScheduledEvent($eventId, $uri)
{    
    # Create the Scheduled Events Approval Json
    $startRequests = [array]@{"EventId" = $eventId}
    $scheduledEventsApproval = @{"StartRequests" = $startRequests} 
    $approvalString = ConvertTo-Json $scheduledEventsApproval

    Write-Host "Approving with the following: `n" $approvalString

    # Post approval string to scheduled events endpoint
    Invoke-RestMethod -Uri $uri -Headers @{"Metadata"="true"} -Method POST -Body $approvalString
}

# Add logic relevant to your service here
function HandleScheduledEvents($scheduledEvents)
{

}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events uri for VNET enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/latest/scheduledevents' -f $localHostIP 


# Get the document
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
    ApproveScheduledEvent $event.EventId $scheduledEventURI 
    }
}
``` 


## <a name="c-sample"></a>Ejemplo de C\# 
El ejemplo siguiente es de un cliente que muestra API para comunicarse con Metadata Service:
```csharp
   public class ScheduledEventsClient
    {
        private readonly string scheduledEventsEndpoint;
        private readonly string defaultIpAddress = "169.254.169.254"; 

        public ScheduledEventsClient()
        {
            scheduledEventsEndpoint = string.Format("http://{0}/metadata/latest/scheduledevents", defaultIpAddress);
        }
        /// Retrieve Scheduled Events 
        public string GetDocument()
        {
            Uri cloudControlUri = new Uri(scheduledEventsEndpoint);
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Metadata", "true");
                return webClient.DownloadString(cloudControlUri);
            }   
        }

        /// Issues a post request to the scheduled events endpoint with the given json string
        public void PostResponse(string jsonPost)
        {
            using (var webClient = new WebClient())
            {
                webClient.Headers.Add("Content-Type", "application/json");
                webClient.UploadString(scheduledEventsEndpoint, jsonPost);
            }
        }
    }

```
Los eventos programados se pueden analizar mediante las siguientes estructuras de datos 

```csharp
    public class ScheduledEventsDocument
    {
        public List<CloudControlEvent> Events { get; set; }
    }

    public class CloudControlEvent
    {
        public string EventId { get; set; }
        public string EventStatus { get; set; }
        public string EventType { get; set; }
        public string ResourceType { get; set; }
        public List<string> Resources { get; set; }
        public DateTime NoteBefore { get; set; }
    }

    public class ScheduledEventsApproval
    {
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

Un programa de ejemplo que usa el cliente para recuperar, controlar y reconocer eventos:   

```csharp
public class Program
    {
    static ScheduledEventsClient client;
    static void Main(string[] args)
    {
        while (true)
        {
            client = new ScheduledEventsClient();
            string json = client.GetDocument();
            ScheduledEventsDocument scheduledEventsDocument = JsonConvert.DeserializeObject<ScheduledEventsDocument>(json);

            HandleEvents(scheduledEventsDocument.Events);

            // Wait for user response
            Console.WriteLine("Press Enter to approve executing events\n");
            Console.ReadLine();

            // Approve events
            ScheduledEventsApproval scheduledEventsApprovalDocument = new ScheduledEventsApproval();
            foreach (CloudControlEvent ccevent in scheduledEventsDocument.Events)
            {
                scheduledEventsApprovalDocument.StartRequests.Add(new StartRequest(ccevent.EventId));
            }
            if (scheduledEventsApprovalDocument.StartRequests.Count > 0)
            {
                // Serialize using Newtonsoft.Json
                string approveEventsJsonDocument =
                    JsonConvert.SerializeObject(scheduledEventsApprovalDocument);

                Console.WriteLine($"Approving events with json: {approveEventsJsonDocument}\n");
                client.PostResponse(approveEventsJsonDocument);
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

```python


#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url="http://169.254.169.254/metadata/latest/scheduledevents"
headers="{Metadata:true}"
this_host=socket.gethostname()

def get_scheduled_events():
   req=urllib2.Request(metadata_url)
   req.add_header('Metadata','true')
   resp=urllib2.urlopen(req)
   data=json.loads(resp.read())
   return data

def handle_scheduled_events(data):
    for evt in data['Events']:
        eventid=evt['EventId']
        status=evt['EventStatus']
        resources=evt['Resources'][0]
        eventype=evt['EventType']
        restype=evt['ResourceType']
        notbefore=evt['NotBefore'].replace(" ","_")
        if this_host in evt['Resources'][0]:
            print "+ Scheduled Event. This host is scheduled for " + eventype + " not before " + notbefore
            print "++ Add you logic here"

def main():
   data=get_scheduled_events()
   handle_scheduled_events(data)
   

if __name__ == '__main__':
  main()
  sys.exit(0)


```
## <a name="next-steps"></a>Pasos siguientes 
[Mantenimiento planeado de máquinas virtuales en Azure](./virtual-machines-linux-planned-maintenance.md)

