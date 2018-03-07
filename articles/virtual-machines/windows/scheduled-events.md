---
title: Eventos programados para VM Windows en Azure| Microsoft Docs
description: "Eventos programados mediante el servicio Azure Metadata para las máquinas virtuales Windows."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: ericrad
manager: timlt
editor: 
tags: 
ms.assetid: 28d8e1f2-8e61-4fbe-bfe8-80a68443baba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: ericrad
ms.openlocfilehash: 8f78f476e28ec04acfea9fe45d57a4c18d5db678
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2018
---
# <a name="azure-metadata-service-scheduled-events-for-windows-vms"></a>Servicio Azure Metadata: Scheduled Events para Windows VM

Eventos programados es un servicio de Azure Metadata Service que proporciona el tiempo de aplicación para prepararse para el mantenimiento de la máquina virtual. Proporciona información sobre los eventos de mantenimiento próximos (por ejemplo, un reinicio) para que la aplicación se pueda preparar y así limitar las interrupciones. Está disponible para todos los tipos de máquina virtual de Azure, incluido IaaS y PaaS, tanto en Windows como en Linux. 

Para más información acerca de Scheduled Events en Linux, consulte [Scheduled Events para máquinas virtuales Linux](../linux/scheduled-events.md).

> [!Note] 
> Scheduled Events está disponible con carácter general en todas las regiones de Azure. Consulte el apartado sobre la [disponibilidad por región y versión](#version-and-region-availability) para obtener información sobre la versión más reciente.

## <a name="why-scheduled-events"></a>¿Por qué Scheduled Events?

Muchas aplicaciones pueden beneficiarse del tiempo de preparación para el mantenimiento de las máquinas virtuales. Este tiempo se puede usar para realizar tareas específicas de la aplicación con el fin de mejorar la disponibilidad, la confiabilidad y la capacidad de servicio, lo que incluye: 

- Punto de control y restauración
- Purga de la conexión
- Conmutación por error de la réplica principal 
- Eliminación del grupo de equilibradores de carga
- Registros de eventos
- Apagado correcto 

Con Eventos programados, la aplicación puede detectar cuándo se producirá el mantenimiento y desencadenar tareas para limitar su impacto.  

Eventos programados proporciona eventos en los casos de uso siguientes:
- Mantenimiento iniciado por la plataforma (por ejemplo, actualización del SO del host)
- Mantenimiento iniciado por el usuario (por ejemplo, el usuario reinicia o vuelve a implementar una máquina virtual)

## <a name="the-basics"></a>Conceptos básicos  

Azure Metadata Service expone información sobre la ejecución de máquinas virtuales mediante un punto de conexión de REST accesible desde la propia máquina virtual. La información se encuentra disponible a través de una dirección IP no enrutable, de modo que no se expone fuera de la máquina virtual.

### <a name="endpoint-discovery"></a>Detección de punto de conexión
En el caso de las máquinas virtuales con red virtual habilitada, el servicio de metadatos está disponible desde una dirección IP no enrutable estática, `169.254.169.254`. El punto de conexión completo de la versión más reciente de Scheduled Events es: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Si la máquina virtual no se crea dentro de una red virtual (lo habitual para servicios en la nube y VM clásicas), se necesita una lógica adicional para detectar la dirección IP que se va a usar. Consulte esta muestra para obtener información sobre cómo [descubrir el punto de conexión de host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm).

### <a name="version-and-region-availability"></a>Disponibilidad por región y versión
El servicio Eventos programados tiene versiones. Las versiones son obligatorias y la versión actual es la `2017-08-01`.

| Versión | Tipo de versión | Regiones | Notas de la versión | 
| - | - | - | - |
| 2017-08-01 | Disponibilidad general | Todo | <li> Se quitó el guion bajo antepuesto de los nombres de recursos en las máquinas virtuales de IaaS<br><li>Se aplicó el requisito de encabezado de metadatos para todas las solicitudes | 
| 2017-03-01 | Vista previa | Todo |<li>Versión inicial.

> [!NOTE] 
> Las versiones preliminares de eventos programados compatibles {más reciente} como la versión de api. Este formato ya no es compatible y dejará de utilizarse en el futuro.

### <a name="enabling-and-disabling-scheduled-events"></a>Habilitación y deshabilitación de Scheduled Events
Scheduled Events se habilita para un servicio la primera vez que se realiza una solicitud de eventos. Debe esperar hasta dos minutos de demora en la respuesta en la primera llamada.

Scheduled Events se deshabilita para el servicio si no se realiza una solicitud durante 24 horas.

### <a name="user-initiated-maintenance"></a>Mantenimiento iniciado por el usuario
El mantenimiento de máquina virtual iniciado por el usuario a través de Azure Portal, API, CLI o PowerShell da lugar a un evento programado. Esto permite probar la lógica de preparación de mantenimiento en su aplicación. Asimismo, permite a su aplicación prepararse para el mantenimiento iniciado por el usuario.

Si se reinicia una máquina virtual, se programa un evento con el tipo `Reboot`. Si vuelve a implementar una máquina virtual, se programa un evento con el tipo `Redeploy`.

## <a name="using-the-api"></a>Uso de la API

### <a name="headers"></a>encabezados
Al realizar consultas a Metadata Service, debe proporcionar el encabezado `Metadata:true` para asegurarse de que la solicitud no se haya redirigido de manera involuntaria. El encabezado `Metadata:true` es necesario para todas las solicitudes de eventos programados. Un error al incluir el encabezado en la solicitud generará una respuesta del tipo Solicitud incorrecta del servicio de metadatos.

### <a name="query-for-events"></a>Consulta de eventos
Puede consultar los eventos programados; para ello, simplemente haga la siguiente llamada:

#### <a name="powershell"></a>PowerShell
```
curl http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01 -H @{"Metadata"="true"}
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
|Propiedad  |  DESCRIPCIÓN |
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

### <a name="event-scope"></a>Ámbito actual     
Los eventos programados se entregan a:        
 - Todas las máquinas virtuales de un servicio en la nube.      
 - Todas las máquinas virtuales de un conjunto de disponibilidad.      
 - Todas las máquinas virtuales de un grupo de selección de ubicación de conjunto de escalado.         

Por ello, debería revisar el campo `Resources` del evento para identificar cuáles son las máquinas virtuales que se verán afectadas. 

### <a name="starting-an-event"></a>Inicio de un evento 

Una vez que se haya enterado de un evento próximo y que haya completado la lógica para un apagado estable, puede aprobar el evento pendiente mediante una llamada de `POST` a Metadata Service con `EventId`. Esta indica a Azure que puede acortar el tiempo de notificación mínimo (cuando sea posible). 

A continuación se muestra el JSON esperado en el cuerpo de la solicitud `POST`. La solicitud debe contener una lista de `StartRequests`. Cada `StartRequest` contiene el elemento `EventId` para el evento que desea acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="powershell"></a>PowerShell
```
curl -H @{"Metadata"="true"} -Method POST -Body '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' -Uri http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Reconocer un evento permite a este continuar para todos sus elementos `Resources`, no solo para la máquina virtual que lo reconoce. Por tanto, puede optar por elegir un líder para que coordine el reconocimiento. Este puede ser tan sencillo como la propia máquina del campo `Resources`.


## <a name="powershell-sample"></a>Ejemplo de PowerShell 

En el siguiente ejemplo se realiza una consulta a Metadata Service para eventos programados y se aprueban todos los eventos pendientes.

```PowerShell
# How to get scheduled events 
function Get-ScheduledEvents($uri)
{
    $scheduledEvents = Invoke-RestMethod -Headers @{"Metadata"="true"} -URI $uri -Method get
    $json = ConvertTo-Json $scheduledEvents
    Write-Host "Received following events: `n" $json
    return $scheduledEvents
}

# How to approve a scheduled event
function Approve-ScheduledEvent($eventId, $docIncarnation, $uri)
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

function Handle-ScheduledEvents($scheduledEvents)
{
    # Add logic for handling events here
}

######### Sample Scheduled Events Interaction #########

# Set up the scheduled events URI for a VNET-enabled VM
$localHostIP = "169.254.169.254"
$scheduledEventURI = 'http://{0}/metadata/scheduledevents?api-version=2017-03-01' -f $localHostIP 

# Get events
$scheduledEvents = Get-ScheduledEvents $scheduledEventURI

# Handle events however is best for your service
Handle-ScheduledEvents $scheduledEvents

# Approve events when ready (optional)
foreach($event in $scheduledEvents.Events)
{
    Write-Host "Current Event: `n" $event
    $entry = Read-Host "`nApprove event? Y/N"
    if($entry -eq "Y" -or $entry -eq "y")
    {
        Approve-ScheduledEvent $event.EventId $scheduledEvents.DocumentIncarnation $scheduledEventURI 
    }
}
``` 

## <a name="next-steps"></a>pasos siguientes 

- Vea una [demostración de Scheduled Events](https://channel9.msdn.com/Shows/Azure-Friday/Using-Azure-Scheduled-Events-to-Prepare-for-VM-Maintenance) en Azure el viernes. 
- Revise los ejemplos de código de Eventos programados en el [repositorio de Eventos programados de metadatos de instancia de Azure de GitHub](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm)
- En [Instance Metadata Service](instance-metadata-service.md) (Servicio Instance Metadata), puede obtener más información sobre las API disponibles.
- Obtenga información sobre cómo realizar [el mantenimiento planeado para máquinas virtuales Windows en Azure](planned-maintenance.md).
