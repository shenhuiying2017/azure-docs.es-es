---
title: "Supervisión de operaciones, eventos y contadores para grupos de seguridad de red | Microsoft Docs"
description: Aprenda a habilitar contadores, eventos y registros operativos para grupos de seguridad de red
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 2e699078-043f-48bd-8aa8-b011a32d98ca
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/31/2017
ms.author: jdial
ms.openlocfilehash: 552f37dd704de25159bc0f0ad34fdae9ed8b73f5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="log-analytics-for-network-security-groups-nsgs"></a>Análisis del registro para grupos de seguridad de red (NSG)

Puede habilitar las siguientes categorías de registro de diagnóstico para los NSG:

* **Evento:** contiene entradas para las que se aplican reglas de NSG a las máquinas virtuales y a los roles de instancia en función de la dirección MAC. El estado de estas reglas se recopila cada 60 segundos.
* **Contador de regla:** contiene entradas para el número de veces que se aplica cada regla NSG para denegar o permitir el tráfico.

> [!NOTE]
> Los registros de diagnóstico solo están disponibles para los NSG implementados a través del modelo de implementación de Azure Resource Manager. No se puede habilitar el registro de diagnóstico para los NSG implementados a través del modelo de implementación clásica. Para entender mejor los dos modelos, consulte el artículo [Understanding Azure deployment models](../resource-manager-deployment-model.md) (Descripción de los modelos de implementación de Azure).

El registro de actividad (conocido anteriormente como registro operativo o de auditoría) está habilitado de forma predeterminada para los NSG creados a través de cualquier modelo de implementación de Azure. Para determinar qué operaciones se completaron en los NSG del registro de actividad, busque las entradas que contengan los siguientes tipos de recursos: 

- Microsoft.ClassicNetwork/networkSecurityGroups 
- Microsoft.ClassicNetwork/networkSecurityGroups/securityRules
- Microsoft.Network/networkSecurityGroups
- Microsoft.Network/networkSecurityGroups/securityRules 

Lea el artículo [Información general sobre el registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-activity-logs.md) para obtener más información sobre los registros de actividad. 

## <a name="enable-diagnostic-logging"></a>Activación del registro de diagnóstico

Debe habilitar el registro de diagnóstico para *cada* NSG para el que desee recopilar datos. El artículo [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md) explica dónde se pueden enviar los registros de diagnóstico. Si actualmente no tiene ningún NSG, siga los pasos del artículo [Create a network security group](virtual-networks-create-nsg-arm-pportal.md) (Creación de un grupo de seguridad de red) para crear uno. Puede habilitar el registro de diagnóstico de NSG por medio de cualquiera de los métodos siguientes:

### <a name="azure-portal"></a>Portal de Azure

Si quiere usar el portal para habilitar el registro, inicie sesión en el [portal](https://portal.azure.com). Haga clic en **Más servicios** y, a continuación, escriba *grupos de seguridad de red*. Seleccione el NSG para el que desea habilitar el registro. Siga las instrucciones para los recursos que no son de proceso del artículo [Habilitación de los registros de diagnóstico en el portal](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Seleccione **NetworkSecurityGroupEvent**, **NetworkSecurityGroupRuleCounter**, o las dos categorías de registros.

### <a name="powershell"></a>PowerShell

Para usar PowerShell a fin de habilitar el registro, siga las instrucciones del artículo [Habilitación de los registros de diagnóstico en PowerShell](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Evalúe la siguiente información antes de especificar un comando del artículo:

- Puede determinar el valor que se usará para el parámetro `-ResourceId`; para ello, reemplace el siguiente [texto], según corresponda, y luego escriba el comando `Get-AzureRmNetworkSecurityGroup -Name [nsg-name] -ResourceGroupName [resource-group-name]`. La salida del identificador del comando es similar a */subscriptions/[Id. de suscripción]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[Nombre de NSG]*.
- Si solo desea recopilar datos de categoría de registro, agregue `-Categories [category]` al final del comando en el artículo, donde la categoría es *NetworkSecurityGroupEvent* o *NetworkSecurityGroupRuleCounter*. Si no utiliza el `-Categories` parámetro, la recopilación de datos estará habilitada para ambas categorías de registros.

### <a name="azure-command-line-interface-cli"></a>Interfaz de la línea de comandos (CLI) de Azure

Para usar la CLI a fin de habilitar el registro, siga las instrucciones del artículo [Habilitación de los registros de diagnóstico en CLI](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md#how-to-enable-collection-of-resource-diagnostic-logs). Evalúe la siguiente información antes de especificar un comando del artículo:

- Puede determinar el valor que se usará para el parámetro `-ResourceId`; para ello, reemplace el siguiente [texto], según corresponda, y luego escriba el comando `azure network nsg show [resource-group-name] [nsg-name]`. La salida del identificador del comando es similar a */subscriptions/[Id. de suscripción]/resourceGroups/[resource-group]/providers/Microsoft.Network/networkSecurityGroups/[Nombre de NSG]*.
- Si solo desea recopilar datos de categoría de registro, agregue `-Categories [category]` al final del comando en el artículo, donde la categoría es *NetworkSecurityGroupEvent* o *NetworkSecurityGroupRuleCounter*. Si no utiliza el `-Categories` parámetro, la recopilación de datos estará habilitada para ambas categorías de registros.

## <a name="logged-data"></a>Datos registrados

Se escriben datos con formato JSON para ambos registros. Los datos específicos que se escriben para cada tipo de registro se enumeran en las secciones siguientes:

### <a name="event-log"></a>Registro de eventos
Este registro contiene información acerca de las reglas NSG que se aplican a las máquinas virtuales y a las instancias de rol de servicio en la nube, en función de la dirección MAC. Los datos de ejemplo siguientes se registran para cada evento:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupEvent",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION-ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupEvents",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-B791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "priority":1000,
        "type":"allow",
        "conditions":{
            "protocols":"6",
            "destinationPortRange":"3389-3389",
            "sourcePortRange":"0-65535",
            "sourceIP":"0.0.0.0/0",
            "destinationIP":"0.0.0.0/0"
            }
        }
}
```

### <a name="rule-counter-log"></a>Registro de contador de regla

Este registro contiene información acerca de cada regla que se aplica a los recursos. Los datos de ejemplo siguiente se registran cada vez que se aplica una regla:

```json
{
    "time": "[DATE-TIME]",
    "systemId": "007d0441-5d6b-41f6-8bfd-930db640ec03",
    "category": "NetworkSecurityGroupRuleCounter",
    "resourceId": "/SUBSCRIPTIONS/[SUBSCRIPTION ID]/RESOURCEGROUPS/[RESOURCE-GROUP-NAME]TESTRG/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/[NSG-NAME]",
    "operationName": "NetworkSecurityGroupCounters",
    "properties": {
        "vnetResourceGuid":"{5E8AEC16-C728-441F-B0CA-791E1DBC2F4}",
        "subnetPrefix":"192.168.1.0/24",
        "macAddress":"00-0D-3A-92-6A-7C",
        "primaryIPv4Address":"192.168.1.4",
        "ruleName":"UserRule_default-allow-rdp",
        "direction":"In",
        "type":"allow",
        "matchedConnections":125
        }
}
```

## <a name="view-and-analyze-logs"></a>Visualización y análisis de los registros

Para obtener información sobre cómo ver los datos de registro de actividad, lea el artículo [Información general sobre el registro de actividad de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Para obtener información sobre cómo ver los datos de registro de diagnóstico, lea el artículo [Información general sobre los registros de diagnóstico de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md). Si envía datos de diagnóstico a Log Analytics, puede usar la solución de administración [Azure Network Security Group Analytics](../log-analytics/log-analytics-azure-networking-analytics.md) (versión preliminar) para obtener una mejor perspectiva. 
