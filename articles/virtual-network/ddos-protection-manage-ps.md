---
title: "Administración de Protección contra DDoS de Azure estándar mediante Azure PowerShell | Microsoft Docs"
description: "Aprenda a administrar Protección contra DDoS de Azure estándar mediante Azure PowerShell."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/15/2017
ms.author: kumud
ms.openlocfilehash: a1a3688d4ff215d05d2f78cdfa7d402e3fc20be2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="manage-azure-ddos-protection-standard-using-azure-powershell"></a>Administración de Protección contra DDoS de Azure estándar mediante Azure PowerShell

>[!IMPORTANT]
>El servicio Protección contra DDoS de Azure estándar (Protección contra DDoS) se encuentra actualmente en la versión preliminar. El servicio Protección contra DDoS es compatible con un número limitado de recursos de Azure y en un determinado número de regiones. Necesita [registrarse en el servicio](http://aka.ms/ddosprotection) durante la versión preliminar limitada para habilitar Protección contra DDoS para su suscripción. Después de registrarse, el equipo de DDoS de Azure se pondrá en contacto con usted para guiarle en el proceso de habilitación. El servicio Protección contra DDoS está disponible en las regiones Este de EE. UU., Oeste de EE. UU. y Centro occidental de EE. UU. Durante la versión preliminar, no se le cobrará por usar el servicio.

En este artículo se muestra cómo usar Azure PowerShell para habilitar y deshabilitar Protección contra DDoS, y cómo usar la telemetría para mitigar un ataque.

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar. Si necesita instalar o actualizar Azure PowerShell, vea [Install and configure Azure PowerShell](/powershell/azure/install-azurerm-ps) (Instalación y configuración de Azure PowerShell).

## <a name="log-in-to-azure"></a>Inicie sesión en Azure.

Inicie sesión en la suscripción de Azure con el comando `Login-AzureRmAccount` y siga las instrucciones de la pantalla.

```powershell
Login-AzureRmAccount
```

## <a name="enable-ddos-protection"></a>Habilitar Protección contra DDoS

### <a name="create-a-new-virtual-network-and-enable-ddos-protection"></a>Crear una red virtual y habilitar Protección contra DDoS

Para crear una red virtual que tenga habilitada Protección contra DDoS, ejecute este ejemplo:

```powershell
New-AzureRmResourceGroup -Name <ResourceGroupName> -Location westcentralus 
$frontendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <frontendSubnet> -AddressPrefix "10.0.1.0/24" 
$backendSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name <backendSubnet> -AddressPrefix "10.0.2.0/24" 
New-AzureRmVirtualNetwork -Name <MyVirtualNetwork> -ResourceGroupName <ResourceGroupName>  -Location westcentralus  -AddressPrefix "10.0.0.0/16" -Subnet $frontendSubnet,$backendSubnet -DnsServer 10.0.1.5,10.0.1.6 -EnableDDoSProtection
```

En este ejemplo se crea una red virtual con dos subredes y dos servidores DNS. Al especificar los servidores DNS en la red virtual se consigue que las NIC/máquinas virtuales que están implementadas en esta red virtual hereden estos servidores DNS según los valores predeterminados. Protección contra DDoS se habilita para todos los recursos protegidos de la red virtual.

### <a name="enable-ddos-protection-on-an-existing-virtual-network"></a>Habilitar Protección contra DDoS en una red virtual existente

Para habilitar Protección contra DDoS en una red virtual existente, ejecute este ejemplo:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $true
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName "ResourceGroupName" -ResourceName "ResourceName" -ResourceType Microsoft.Network/virtualNetworks
```

## <a name="disable-ddos-protection-on-a-virtual-network"></a>Deshabilitar Protección contra DDoS en una red virtual

Para deshabilitar Protección contra DDoS en una red virtual, ejecute este ejemplo:

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps.enableDdosProtection = $false
Set-AzureRmResource -PropertyObject $vnetProps -ResourceGroupName <RessourceGroupName> -ResourceName <ResourceName> -ResourceType "Microsoft.Network/virtualNetworks"
```

## <a name="review-the-ddos-protection-status-of-virtual-networks"></a>Revisar el estado de Protección contra DDoS en redes virtuales 

```powershell
$vnetProps = (Get-AzureRmResource -ResourceType "Microsoft.Network/virtualNetworks" -ResourceGroup <ResourceGroupName> -ResourceName <ResourceName>).Properties
$vnetProps
```

## <a name="use-ddos-protection-telemetry"></a>Usar telemetría de Protección contra DDoS

La telemetría para un ataque se proporciona a través de Azure Monitor en tiempo real. La telemetría está disponible solo durante el tiempo en que una dirección IP pública está en proceso de mitigación. No verá ninguna telemetría antes o después de mitigar un ataque.

### <a name="configure-alerts-on-ddos-protection-metrics"></a>Configurar alertas para las métricas de Protección contra DDoS

Al aprovechar la configuración de alertas de Azure Monitor, puede seleccionar cualquiera de las métricas de Protección contra DDoS disponibles para avisar cuando hay una mitigación activa durante un ataque.

#### <a name="configure-email-alert-rules-via-azure-powershell"></a>Configurar reglas de alertas de correo electrónico a través de Azure PowerShell

1. Obtenga una lista de las suscripciones disponibles. Compruebe que trabaja con la suscripción adecuada. Si no es así, establezca la suscripción correcta con la salida de Get-AzureRmSubscription. 

    ```powershell
    Get-AzureRmSubscription 
    Get-AzureRmContext 
    Set-AzureRmContext -SubscriptionId <subscriptionid>
    ```

2. Para ver las reglas existentes en un grupo de recursos, use el comando siguiente: 

    ```powershell
    Get-AzureRmAlertRule -ResourceGroup <myresourcegroup> -DetailedOutput
    ```

3. Para crear una regla, primero debe contar con varios datos importantes. 

    - El identificador del recurso para el que quiere establecer una alerta.
    - Las definiciones de métricas disponibles para ese recurso. Una forma de obtener el identificador de recurso es usar Azure Portal. Seleccione el recurso en Azure Portal, en el caso de que el recurso ya esté creado. En la sección *Configuración* de la página siguiente, seleccione *Propiedades*. El campo **Id. de recurso** está en la página siguiente. Otra forma que puede usar es el [Explorador de recursos de Azure](https://resources.azure.com/). Este es un ejemplo de identificador de recurso de una IP pública: `/subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroupname/providers/Microsoft.Network/publicIPAddresses/mypublicip`

    En el ejemplo siguiente se configura una alerta en una IP pública asociada a una red virtual. La métrica para crear una alerta es **Under DDoS attack or not** (Frente a ataque DDoS o no). Se trata de un valor booleano 1 o 0. Un **1** significa que está siendo atacado. Un **0** significa que no está siendo atacado. La alerta se crea cuando está siendo atacado en un intervalo de los últimos 5 minutos.

    Para crear un webhook o enviar un correo electrónico cuando se cree una alerta, debe crear primero el correo electrónico o los webhooks. Cree la regla inmediatamente después con la etiqueta -Actions, tal como se muestra en el ejemplo siguiente. No se pueden asociar webhooks o correos electrónicos a reglas ya creadas mediante PowerShell.

    ```powershell
    $actionEmail = New-AzureRmAlertRuleEmail -CustomEmail myname@company.com 
    Add-AzureRmMetricAlertRule -Name <myMetricRuleWithEmail> -Location "West Central US" -ResourceGroup <myresourcegroup> -TargetResourceId /subscriptions/dededede-7aa0-407d-a6fb-eb20c8bd1192/resourceGroups/myresourcegroup/providers/Microsoft.Network/publicIPAddresses/mypublicip -MetricName "IfUnderDDoSAttack" -Operator GreaterThan -Threshold 0 -WindowSize 00:05:00 -TimeAggregationOperator Total -Actions $actionEmail-Description "Under DDoS Attack" 
    ```

4. Observe las reglas individuales para comprobar que las alertas se crearon correctamente.

    ```powershell
    Get-AzureRmAlertRule -Name myMetricRuleWithEmail -ResourceGroup myresourcegroup -DetailedOutput 
    Get-AzureRmAlertRule -Name myLogAlertRule -ResourceGroup myresourcegroup -DetailedOutput
    ```

Puede aprender más sobre [configurar webhooks](../monitoring-and-diagnostics/insights-webhooks-alerts.md) y [aplicaciones lógicas](../logic-apps/logic-apps-what-are-logic-apps.md) para la creación de alertas.

## <a name="configure-logging-on-ddos-protection-metrics"></a>Configurar el registro de métricas de Protección contra DDoS

Vea [Ejemplos de inicio rápido de PowerShell de Azure Monitor](../monitoring-and-diagnostics/insights-powershell-samples.md) para aprender a acceder al registro de diagnóstico de Azure y configurarlo mediante PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los registros de Diagnósticos de Azure](../monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs.md)
- [Análisis de registros desde Azure Storage con Log Analytics](../log-analytics/log-analytics-azure-storage.md)
- [Introducción a Event Hubs](../event-hubs/event-hubs-csharp-ephcs-getstarted.md)