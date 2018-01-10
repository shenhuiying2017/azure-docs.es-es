---
title: "Introducción a los registros de flujo de grupos de seguridad de red con Azure Network Watcher | Microsoft Docs"
description: "Esta página explica cómo usar los registros de flujo de los grupos de seguridad de red, una característica de Azure Network Watcher."
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 47d91341-16f1-45ac-85a5-e5a640f5d59e
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 4eaffba08ccf601e440709d804891668340a376d
ms.sourcegitcommit: 234c397676d8d7ba3b5ab9fe4cb6724b60cb7d25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/20/2017
---
# <a name="introduction-to-flow-logging-for-network-security-groups"></a>Introducción a los registros de flujo de grupos de seguridad de red

Los registros de flujo de grupos de seguridad de red son una característica de Network Watcher que permite ver información acerca del tráfico IP de entrada y de salida en un grupo de seguridad de red. Estos registros de flujo se escriben en formato JSON y muestran los flujos de entrada y salida en función de cada regla, la NIC a la que se aplica el flujo, información de 5-tupla sobre el flujo (IP de origen/destino, puerto de origen/destino, protocolo), y si se permitió o denegó el tráfico.

![información general de los registros de flujo][1]

Aunque los registros de flujo tienen como objetivo los grupos de seguridad de red, no se muestran como los demás registros. Los registros de flujo se almacenan solo dentro de una cuenta de almacenamiento y siguen la ruta de acceso del registro que se muestra en el ejemplo siguiente:

```
https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/resourceId=/SUBSCRIPTIONS/{subscriptionID}/RESOURCEGROUPS/{resourceGroupName}/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/{nsgName}/y={year}/m={month}/d={day}/h={hour}/m=00/macAddress={macAddress}/PT1H.json
```

Las mismas directivas de retención vistas en otros registros se aplican a los registros de flujo. Los registros tienen una directiva de retención que se puede establecer entre 1 y 365 días. Si no se establece una directiva de retención, los registros se mantendrán indefinidamente.

## <a name="log-file"></a>Archivo de registro

Los registros de flujo tienen varias propiedades. En la lista siguiente puede ver una lista de las propiedades que se devuelven en el registro de flujo de un grupo de seguridad de red:

* **time**: la hora en la que se registró el evento
* **systemId**: el identificador de recurso del grupo de seguridad de red
* **category**: la categoría del evento, esta siempre es NetworkSecurityGroupFlowEvent
* **resourceid**: el identificador del recurso del grupo de seguridad de red
* **operationName**: siempre es NetworkSecurityGroupFlowEvents
* **properties**: una recopilación de las propiedades del flujo
    * **Version**: número de versión del esquema de eventos del registro de flujos
    * **flows**: una recopilación de flujos. Esta propiedad tiene varias entradas para diferentes reglas
        * **rule**: regla para la que se muestran los flujos
            * **flows**: una recopilación de flujos
                * **mac**: la dirección MAC de la NIC de la máquina virtual en la que se recopiló el flujo
                * **flowTuples**: una cadena que contiene varias propiedades de la tupla de flujo en un formato separado por comas
                    * **Time Stamp**: este valor es la marca de tiempo de cuando se produjo el flujo en formato UNIX EPOCH
                    * **Source IP**: la IP de origen
                    * **Destination IP**: la IP de destino
                    * **Source Port**: el puerto de origen
                    * **Destination Port**: el puerto de destino
                    * **Protocol**: el protocolo del flujo. Los valores válidos son **T** para TCP y **U** para UDP
                    * **Traffic Flow**: la dirección del flujo de tráfico. Los valores válidos son **I** para el correo entrante y **O** para el saliente.
                    * **Traffic**: indica si el tráfico se permitió o se denegó. Los valores válidos son **A** para permitido y **D** para denegado.


A continuación, se muestra un ejemplo de un registro de flujo. Como puede ver, hay varios registros que siguen la lista de propiedades que se describe en la sección anterior. 

> [!NOTE]
> Los valores de la propiedad flowTuples son una lista separada por comas.
 
```json
{
    "records": 
    [
        
        {
             "time": "2017-02-16T22:00:32.8950000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282421,42.119.146.95,10.1.0.4,51529,5358,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282370,163.28.66.17,10.1.0.4,61771,3389,T,I,A","1487282393,5.39.218.34,10.1.0.4,58596,3389,T,I,A","1487282393,91.224.160.154,10.1.0.4,61540,3389,T,I,A","1487282423,13.76.89.229,10.1.0.4,53163,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:01:32.8960000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282481,195.78.210.194,10.1.0.4,53,1732,U,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282435,61.129.251.68,10.1.0.4,57776,3389,T,I,A","1487282454,84.25.174.170,10.1.0.4,59085,3389,T,I,A","1487282477,77.68.9.50,10.1.0.4,65078,3389,T,I,A"]}]}]}
        }
        ,
        {
             "time": "2017-02-16T22:02:32.9040000Z",
             "systemId": "2c002c16-72f3-4dc5-b391-3444c3527434",
             "category": "NetworkSecurityGroupFlowEvent",
             "resourceId": "/SUBSCRIPTIONS/00000000-0000-0000-0000-000000000000/RESOURCEGROUPS/FABRIKAMRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/FABRIAKMVM1-NSG",
             "operationName": "NetworkSecurityGroupFlowEvents",
             "properties": {"Version":1,"flows":[{"rule":"DefaultRule_DenyAllInBound","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282492,175.182.69.29,10.1.0.4,28918,5358,T,I,D","1487282505,71.6.216.55,10.1.0.4,8080,8080,T,I,D"]}]},{"rule":"UserRule_default-allow-rdp","flows":[{"mac":"000D3AF8801A","flowTuples":["1487282512,91.224.160.154,10.1.0.4,59046,3389,T,I,A"]}]}]}
        }
        ,
        ...
```

## <a name="next-steps"></a>pasos siguientes

Aprenda a habilitar los registros de flujo visitando [Habilitamiento del registro de flujos](network-watcher-nsg-flow-logging-portal.md).

Obtenga información acerca del registro de NSG visitando [Análisis del registro para grupos de seguridad de red (NSG)](../virtual-network/virtual-network-nsg-manage-log.md).

Averigüe si se permite o no el tráfico en una máquina virtual visitando [Comprobación del tráfico con la comprobación de flujo de IP](network-watcher-check-ip-flow-verify-portal.md)

<!-- Image references -->
[1]: ./media/network-watcher-nsg-flow-logging-overview/figure1.png

