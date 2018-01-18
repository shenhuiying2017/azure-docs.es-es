---
title: Eventos programados para VM Linux en Azure| Microsoft Docs
description: "Programe los eventos usando Azure Metadata Service para las máquinas virtuales Linux."
services: virtual-machines-windows, virtual-machines-linux, cloud-services
documentationcenter: 
author: zivraf
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/14/2017
ms.author: zivr
ms.openlocfilehash: ae9955253647f3277729e7905baf7bb07645de42
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2018
---
# <a name="azure-metadata-service-scheduled-events-preview-for-linux-vms"></a>Azure Metadata Service: Scheduled Events (versión preliminar) para máquinas virtuales Linux

> [!NOTE] 
> Las versiones preliminares están a su disposición con la condición de que acepte los términos de uso. Para obtener más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

Scheduled Events es un servicio de Azure Metadata Service que proporciona a la aplicación tiempo para prepararse para el mantenimiento de las máquinas virtuales. Da información sobre los eventos de mantenimiento próximos (por ejemplo, un reinicio) para que la aplicación pueda prepararlos y así limitar las interrupciones. Está disponible para todos los tipos de máquina virtual de Azure, incluidos IaaS y PaaS, tanto en Windows como en Linux. 

Para más información acerca de Scheduled Events en Windows, consulte [Scheduled Events para máquinas virtuales Windows](../windows/scheduled-events.md).

## <a name="why-use-scheduled-events"></a>¿Por qué usar Scheduled Events?

Muchas aplicaciones pueden aprovechar el tiempo para preparar el mantenimiento de las máquinas virtuales. Este tiempo se puede usar para realizar tareas específicas de la aplicación con el fin de mejorar la disponibilidad, la confiabilidad y la capacidad de servicio, lo que incluye: 

- Punto de control y restauración.
- Purga de la conexión.
- Conmutación por error de la réplica principal.
- Eliminación de un grupo de equilibradores de carga.
- Registros de eventos.
- Cierre estable.

Con Scheduled Events, la aplicación puede detectar cuándo se producirá el mantenimiento y desencadenar tareas para limitar sus efectos.  

Eventos programados proporciona eventos en los casos de uso siguientes:

- Mantenimiento iniciado por la plataforma (por ejemplo, una implementación del sistema operativo del host)
- Mantenimiento iniciado por el usuario (por ejemplo, el usuario reinicia o vuelve a implementar una máquina virtual)

## <a name="the-basics"></a>Conceptos básicos  

  Metadata Service expone información sobre la ejecución de máquinas virtuales mediante un punto de conexión de REST accesible desde la propia máquina virtual. La información se encuentra disponible a través de una dirección IP no enrutable, de modo que no se expone fuera de la máquina virtual.

### <a name="scope"></a>Scope
Los eventos programados se entregan a:

- Todas las máquinas virtuales en un servicio de nube.
- Todas las máquinas virtuales de un conjunto de disponibilidad.
- Todas las máquinas virtuales de un grupo de selección de ubicación de conjunto de escalado. 

Por ello, revise el campo `Resources` del evento para identificar cuáles son las máquinas virtuales que se verán afectadas.

### <a name="discover-the-endpoint"></a>Detección del punto de conexión
En el caso de las máquinas virtuales habilitadas para redes virtuales, el punto de conexión completo de la versión más reciente de Scheduled Events es: 

 > `http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01`

Al crear una máquina virtual dentro de una red virtual Metadata Service está disponible desde una dirección IP no enrutable (`169.254.169.254`).
Si la máquina virtual no se crea dentro de una red virtual (la opción predeterminada para servicios en la nube y máquinas virtuales clásicas), se necesita lógica adicional para detectar la dirección IP que se va a usar. Para aprender a [detectar el punto de conexión de host](https://github.com/azure-samples/virtual-machines-python-scheduled-events-discover-endpoint-for-non-vnet-vm), consulte este ejemplo.

### <a name="versioning"></a>Control de versiones 
El servicio Scheduled Events tiene versiones. Las versiones son obligatorias y la actual es `2017-08-01`.

| Versión | Notas de la versión | 
| - | - | 
| 2017-08-01 | <li> Se quitó el guion bajo antepuesto de los nombres de recursos en las máquinas virtuales de IaaS<br><li>Se aplicó el requisito de encabezado de metadatos para todas las solicitudes | 
| 2017-03-01 | <li>Versión preliminar pública


> [!NOTE] 
> Versiones preliminares de Scheduled Events admitidas {más recientes} como la versión de API. Este formato ya no es compatible y dejará de utilizarse en el futuro.

### <a name="use-headers"></a>Use encabezados
Al realizar consultas a Metadata Service, debe proporcionar el encabezado `Metadata:true` para asegurarse de que la solicitud no se haya redirigido de manera involuntaria. El encabezado `Metadata:true` es necesario para todas las solicitudes de eventos programados. Un error al incluir el encabezado en la solicitud generará una respuesta del tipo "Solicitud incorrecta" de Metadata Service.

### <a name="enable-scheduled-events"></a>Habilitación de eventos programados
La primera vez que efectúa una solicitud de eventos programados, Azure habilita de manera implícita la característica en la máquina virtual. Como resultado, debe esperar una respuesta diferida de hasta dos minutos en la primera llamada.

> [!NOTE]
> Scheduled Events se deshabilita automáticamente para el servicio, si este no llama al punto de conexión durante un día. Una vez que se deshabilita para el servicio, no habrá eventos creados para el mantenimiento iniciado por el usuario.

### <a name="user-initiated-maintenance"></a>Mantenimiento iniciado por el usuario
El mantenimiento de las máquinas virtuales iniciado por el usuario a través de Azure Portal, API, CLI o PowerShell, da lugar a un evento programado. Esto permite probar la lógica de preparación del mantenimiento en la aplicación, además de que la aplicación pueda prepararse para el mantenimiento iniciado por el usuario.

Si reinicia una máquina virtual, se programa un evento del tipo `Reboot`. Si vuelve a implementar una máquina virtual, se programa un evento del tipo `Redeploy`.

> [!NOTE] 
> Actualmente se pueden programar simultáneamente, como máximo, cien operaciones de mantenimiento iniciadas por el usuario.

> [!NOTE] 
> En este momento, no se puede configurar ningún mantenimiento iniciado por el usuario que dé lugar a eventos programados. Está planeado que esta capacidad de configuración se lance en el futuro.

## <a name="use-the-api"></a>Uso de la API

### <a name="query-for-events"></a>Consulta de eventos
Puede consultar los eventos programados; para ello, simplemente haga la siguiente llamada:

#### <a name="bash"></a>Bash
```
curl -H Metadata:true http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

Una respuesta contiene una matriz de eventos programados. Una matriz vacía significa que actualmente no hay eventos programados.
En caso de que haya eventos programados, la respuesta contiene una matriz de eventos. 
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
| EventType | Es el impacto causado por el evento. <br><br> Valores: <br><ul><li> `Freeze`: la máquina virtual se programa para ponerse en pausa durante unos segundos. La CPU entra en estado de suspensión, pero esto no afecta a la memoria, a los archivos abiertos ni a las conexiones de red. <li>`Reboot`: la máquina virtual se programa para reiniciar el equipo. (La memoria no persistente se pierde). <li>`Redeploy`: la máquina virtual se programa para pasar a otro nodo. (Los discos efímeros se pierden). |
| ResourceType | Es el tipo de recurso al que este evento afecta. <br><br> Valores: <ul><li>`VirtualMachine`|
| Recursos| Es la lista de recursos a los que este evento afecta. Se garantiza que contenga máquinas de un [dominio de actualización](manage-availability.md) como máximo, pero puede no contener todas las máquinas en dicho dominio. <br><br> Ejemplo: <br><ul><li> ["FrontEnd_IN_0", "BackEnd_IN_0"] |
| EventStatus | Es el estado de este evento. <br><br> Valores: <ul><li>`Scheduled`: este evento está programado para iniciarse después de la hora especificada en la propiedad `NotBefore`.<li>`Started`: este evento se ha iniciado.</ul> Ni `Completed` ni otro estado similar se han proporcionado antes. El evento ya no vuelve cuando finaliza el evento.
| NotBefore| Hora a partir de la que puede iniciarse este evento. <br><br> Ejemplo: <br><ul><li> 2016-09-19T18:29:47Z  |

### <a name="event-scheduling"></a>Programación de eventos
Cada evento se programa una cantidad mínima de tiempo en el futuro en función de su tipo. Este tiempo se refleja en la propiedad `NotBefore` de un evento. 

|EventType  | Aviso mínimo |
| - | - |
| Freeze| 15 minutos |
| Reboot | 15 minutos |
| Volver a implementar | 10 minutos |

### <a name="start-an-event"></a>Inicio de un evento 

Cuando se haya enterado de un evento próximo y completado la lógica para llevar a cabo un apagado estable, puede aprobar el evento pendiente mediante una llamada de `POST` a Metadata Service con `EventId`. Esta llamada indica a Azure que puede acortar el tiempo de notificación mínimo (cuando sea posible). 

A continuación se muestra el JSON de ejemplo en el cuerpo de la solicitud `POST`. La solicitud debe contener una lista de `StartRequests`. Cada `StartRequest` contiene el elemento `EventId` para el evento que desea acelerar:
```
{
    "StartRequests" : [
        {
            "EventId": {EventId}
        }
    ]
}
```

#### <a name="bash-sample"></a>Ejemplo de Bash
```
curl -H Metadata:true -X POST -d '{"DocumentIncarnation":"5", "StartRequests": [{"EventId": "f020ba2e-3bc0-4c40-a10b-86575a9eabd5"}]}' http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01
```

> [!NOTE] 
> Reconocer un evento permite a este continuar para todos sus elementos `Resources`, no solo para la máquina virtual que lo reconoce. Por tanto, puede optar por elegir un líder para que coordine el reconocimiento. Este puede ser tan sencillo como la primera máquina del campo `Resources`.

## <a name="python-sample"></a>Ejemplo de Python 

En el siguiente ejemplo se realiza una consulta a Metadata Service para eventos programados y se aprueban todos los eventos pendientes:

```python
#!/usr/bin/python

import json
import urllib2
import socket
import sys

metadata_url = "http://169.254.169.254/metadata/scheduledevents?api-version=2017-08-01"
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
            print "+ Scheduled Event. This host " + this_host + " is scheduled for " + eventtype + " not before " + notbefore
            # Add logic for handling events here


def main():
   data = get_scheduled_events()
   handle_scheduled_events(data)

if __name__ == '__main__':
  main()
  sys.exit(0)
```

## <a name="next-steps"></a>pasos siguientes 
- Repase los ejemplos de código de Scheduled Events en [Azure Instance Metadata Scheduled Events GitHub repository](https://github.com/Azure-Samples/virtual-machines-scheduled-events-discover-endpoint-for-non-vnet-vm) (Repositorio GitHub de Scheduled Events de Azure Instance Metadata).
- En [Instance Metadata Service](instance-metadata-service.md), puede obtener más información sobre las API disponibles.
- Obtenga información sobre cómo realizar [el mantenimiento planeado para máquinas virtuales Linux en Azure](planned-maintenance.md).
