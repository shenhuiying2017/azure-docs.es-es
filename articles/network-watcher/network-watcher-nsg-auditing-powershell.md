---
title: "Automatización de la auditoría de grupos de seguridad de red con la vista de grupo de seguridad de Azure Network Watcher | Documentos de Microsoft"
description: "Esta página proporcionan instrucciones sobre cómo configurar la auditoría de un grupo de seguridad de red"
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: 
ms.assetid: 78a01bcf-74fe-402a-9812-285f3501f877
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 57f2200e541eeb629f72d60ffa0acb2d8233c018
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="automate-nsg-auditing-with-azure-network-watcher-security-group-view"></a>Automatización de la auditoría de grupos de seguridad de red con la vista de grupo de seguridad de Azure Network Watcher

Los clientes a menudo se enfrentan al desafío de comprobar la posición de seguridad de su infraestructura. Este desafío es similar para sus máquinas virtuales en Azure. Es importante contar con un perfil de seguridad similar basado en las reglas del grupo de seguridad de red (NSG) aplicadas. Mediante la vista de grupo de seguridad, ahora puede obtener la lista de reglas que se aplican a una máquina virtual dentro de un NSG. Puede definir un perfil de seguridad principal del NSG e iniciar la vista del grupo de seguridad con un ritmo semanal, comparar el resultado con el perfil principal y crear un informe. De este modo puede identificar con facilidad todas las máquinas virtuales que no cumplen con el perfil de seguridad recomendado.

Si no está familiarizado con los grupos de seguridad de red, acuda al artículo sobre [Información general sobre seguridad de red](../virtual-network/virtual-networks-nsg.md)

## <a name="before-you-begin"></a>Antes de empezar

En este escenario, se compara una línea base buena conocida con los resultados de la vista de grupo de seguridad devueltos para una máquina virtual.

En este escenario, se da por hecho que ya ha seguido los pasos descritos en [Create an Azure Network Watcher instance](network-watcher-create.md) (Creación de una instancia de Azure Network Watcher) para crear una instancia de Network Watcher. En este escenario también se da por hecho que existe un grupo de recursos con una máquina virtual válida.

## <a name="scenario"></a>Escenario

El escenario descrito en este artículo obtiene la vista de grupo de seguridad para una máquina virtual.

En este escenario va a:

- Recuperar un conjunto de buenas reglas conocidas
- Recuperar una máquina virtual con una API de REST
- Obtener la vista de grupo de seguridad para una máquina virtual
- Evaluar la respuesta

## <a name="retrieve-rule-set"></a>Recuperación del conjunto de reglas

El primer paso en este ejemplo es trabajar con una línea base existente. El ejemplo siguiente es un json extraído de un grupo de seguridad de red existente mediante el cmdlet `Get-AzureRmNetworkSecurityGroup` que se utiliza como línea de base para este ejemplo.

```json
[
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "3389",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1000,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "default-allow-rdp",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/default-allow-rdp"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "111",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1010,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "MyRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/MyRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "*",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "112",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Allow",
        "Priority":  1020,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "My2ndRuleDoNotDelete",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/My2ndRuleDoNotDelete"
    },
    {
        "Description":  null,
        "Protocol":  "TCP",
        "SourcePortRange":  "*",
        "DestinationPortRange":  "5672",
        "SourceAddressPrefix":  "*",
        "DestinationAddressPrefix":  "*",
        "Access":  "Deny",
        "Priority":  1030,
        "Direction":  "Inbound",
        "ProvisioningState":  "Succeeded",
        "Name":  "ThisRuleNeedsToStay",
        "Etag":  "W/\"d8859256-1c4c-4b93-ba7d-73d9bf67c4f1\"",
        "Id":  "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/testrg/providers/Microsoft.Network/networkSecurityGroups/testvm1-nsg/securityRules/ThisRuleNeedsToStay"
    }
]
```

## <a name="convert-rule-set-to-powershell-objects"></a>Conversión de los conjuntos de reglas en objetos de PowerShell

En este paso, leemos un archivo json que se creó anteriormente con las reglas que se espera que estén en el grupo de seguridad de red para este ejemplo.

```powershell
$nsgbaserules = Get-Content -Path C:\temp\testvm1-nsg.json | ConvertFrom-Json
```

## <a name="retrieve-network-watcher"></a>Recuperación de Network Watcher

El siguiente paso es recuperar la instancia de Network Watcher. La variable `$networkWatcher` se pasa al cmdlet `AzureRmNetworkWatcherSecurityGroupView`.

```powershell
$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq "WestCentralUS" } 
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName 
```

## <a name="get-a-vm"></a>Obtención de una máquina virtual

Se necesita una máquina virtual para ejecutar el cmdlet `Get-AzureRmNetworkWatcherSecurityGroupView`. En el ejemplo siguiente se obtiene un objeto de máquina virtual.

```powershell
$VM = Get-AzurermVM -ResourceGroupName "testrg" -Name "testvm1"
```

## <a name="retrieve-security-group-view"></a>Recuperación de la vista de grupos de seguridad

El siguiente paso es recuperar el resultado de la vista de grupos de seguridad. Este resultado se compara con el json de "línea base" que se mostró anteriormente.

```powershell
$secgroup = Get-AzureRmNetworkWatcherSecurityGroupView -NetworkWatcher $networkWatcher -TargetVirtualMachineId $VM.Id
```

## <a name="analyzing-the-results"></a>Análisis de los resultados

La respuesta se agrupa por interfaces de red. Los diferentes tipos de reglas devueltas son reglas de seguridad efectivas y predeterminadas. El resultado se divide más por la forma en la que se aplica,bien en una subred o una NIC virtual.

El siguiente script de PowerShell compara los resultados de la vista de grupo de seguridad con una salida existente de un NSG. El siguiente es un ejemplo sencillo de cómo se puedan comparar los resultados con el cmdlet `Compare-Object`.

```powershell
Compare-Object -ReferenceObject $nsgbaserules `
-DifferenceObject $secgroup.NetworkInterfaces[0].NetworkInterfaceSecurityRules `
-Property Name,Description,Protocol,SourcePortRange,DestinationPortRange,SourceAddressPrefix,DestinationAddressPrefix,Access,Priority,Direction
```

El ejemplo siguiente es el resultado. Puede ver que dos de las reglas que estaban en el primer conjunto de reglas no estaban presentes en la comparación.

```
Name                     : My2ndRuleDoNotDelete
Description              : 
Protocol                 : *
SourcePortRange          : *
DestinationPortRange     : 112
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Allow
Priority                 : 1020
Direction                : Inbound
SideIndicator            : <=

Name                     : ThisRuleNeedsToStay
Description              : 
Protocol                 : TCP
SourcePortRange          : *
DestinationPortRange     : 5672
SourceAddressPrefix      : *
DestinationAddressPrefix : *
Access                   : Deny
Priority                 : 1030
Direction                : Inbound
SideIndicator            : <=
```

## <a name="next-steps"></a>Pasos siguientes

Si se cambió la configuración, consulte [Administración de grupos de seguridad de red](../virtual-network/virtual-network-manage-nsg-arm-portal.md) para realizar un seguimiento de los grupos de seguridad de red y las reglas de seguridad que pueden estar afectados.













