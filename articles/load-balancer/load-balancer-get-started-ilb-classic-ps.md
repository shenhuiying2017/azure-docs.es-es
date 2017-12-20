---
title: "Creación de un equilibrador de carga interno de Azure: PowerShell clásico | Microsoft Docs"
description: "Información sobre cómo crear un equilibrador de carga interno mediante PowerShell con el modelo de implementación clásica"
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 3be93168-3787-45a5-a194-9124fe386493
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: kumud
ms.openlocfilehash: 85fd757bdd6dd9906a23977b760f01bb1f85bf77
ms.sourcegitcommit: 68aec76e471d677fd9a6333dc60ed098d1072cfc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/18/2017
---
# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Primeros pasos en la creación de un equilibrador de carga interno (clásico) mediante PowerShell

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [CLI de Azure](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Cloud Services](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Azure tiene dos modelos de implementación diferentes para crear recursos y trabajar con ellos: [Resource Manager y el clásico](../azure-resource-manager/resource-manager-deployment-model.md).  Este artículo trata del modelo de implementación clásico. Microsoft recomienda que las implementaciones más recientes usen el modelo del Administrador de recursos. Obtenga información sobre cómo [realizar estos pasos con el modelo de Resource Manager](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Crear un equilibrador de carga interno establecido para máquinas virtuales

Para crear un conjunto con equilibrio de carga interno y los servidores que enviarán su tráfico a él, debe hacer lo siguiente:

1. Crea una instancia de Equilibrio de carga interno que será el extremo del tráfico entrante que su carga se va a equilibrar entre los servidores de un conjunto con equilibrio de carga.
2. Agregue extremos correspondientes a las máquinas virtuales que van a recibir el tráfico entrante.
3. Configura los servidores que van a enviar el tráfico cuya carga se va a equilibrar para que lo hagan a la dirección IP virtual (VIP) de la instancia de Equilibrio de carga interno.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>Paso 1: crear una instancia de Equilibrio de carga interno

Para un servicio en la nube existente o un servicio en la nube implementado en una red virtual regional, puedes crear una instancia de Equilibrio de carga interno con los siguientes comandos de Windows PowerShell:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Tenga en cuenta que en este cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) de Windows PowerShell, se usa el conjunto de parámetros DefaultProbe. Para obtener más información sobre conjuntos de parámetros adicionales, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>Paso 2: agregar extremos a la instancia de Equilibrio de carga interno

Este es un ejemplo:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>Paso 3: configurar los servidores para que envíen su tráfico al nuevo extremo Equilibrio de carga interno

Tiene que configurar los servidores en los que se va a realizar el equilibrio de carga del tráfico para que usen la nueva dirección IP (VIP) de la instancia de Equilibrio de carga interno. Esta es la dirección en la que se escuchará la instancia de Equilibrio de carga interno. En la mayoría de los casos, solo tienes que agregar o modificar un registro DNS de la dirección VIP de la instancia de Equilibrio de carga interno.

Si especificaste la dirección IP durante la creación de la instancia de Equilibrio de carga interno, ya tienes la dirección VIP. De lo contrario, puede ver a la dirección VIP con los siguientes comandos:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

Para usar estos comandos, rellene los valores y quite el elemento < and >. Este es un ejemplo:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

En la pantalla del comando Get-AzureInternalLoadBalancer, anote la dirección IP y realice los cambios necesarios en los servidores o registros DNS para asegurarse de que el tráfico se envía a la dirección VIP.

> [!NOTE]
> La Plataforma Microsoft Azure utiliza una dirección IPv4 estática enrutable públicamente para una variedad de escenarios de administración. La dirección IP es 168.63.129.16. Ningún firewall debe bloquear esta dirección IP, ya que puede causar un comportamiento inesperado.
> Con respecto al Equilibrio de carga interno de Azure, esta dirección IP la usan las sondas de supervisión del equilibrador de carga para determinar el estado de mantenimiento de las máquinas virtuales en un conjunto con equilibrio de carga. Si se usa un grupo de seguridad de red para restringir el tráfico a Azure Virtual Machines en un conjunto de carga equilibrada internamente o se aplica a una subred de Virtual Network, asegúrate de agregar una regla de seguridad de red para permitir el tráfico desde 168.63.129.16.

## <a name="example-of-internal-load-balancing"></a>Ejemplo de equilibrio de carga interno

Para guiarle en el proceso completo de la creación de un conjunto con equilibrio de carga para dos configuraciones de ejemplo, vea las secciones siguientes.

### <a name="an-internet-facing-multi-tier-application"></a>Una aplicación de niveles múltiples accesible desde Internet

Desea proporcionar un servicio de base de datos con equilibrio de carga para un conjunto de servidores de web accesibles desde Internet. Ambos conjuntos de servidores se hospedan en un solo servicio en la nube de Azure. El tráfico del servidor web al puerto TCP 1433 debe distribuirse entre dos máquinas virtuales en el nivel de base de datos. La Ilustración 1 muestra la configuración.

![Conjunto con equilibrio de carga interno para el nivel de base de datos](./media/load-balancer-internal-getstarted/IC736321.png)

La configuración es la siguiente:

* El servicio en la nube existente que hospeda las máquinas virtuales se denomina mytestcloud.
* Los dos servidores de base de datos existente se denominan DB1 y DB2.
* Los servidores web del nivel web se conectan con los servidores de base de datos en el nivel de base de datos mediante la dirección IP privada. Otra opción es usar su propio DNS para la red virtual y registrar manualmente un registro A para el conjunto de equilibrador de carga interno.

Los comandos siguientes configuran una nueva instancia de Equilibrio de carga interno denominada **ILBset** y agregan puntos de conexión a las máquinas virtuales correspondientes a los dos servidores de base de datos:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Quitar una configuración de Equilibrio de carga interno

Para quitar una máquina virtual como punto de conexión de una instancia de equilibrador de carga interno, use los comandos siguientes:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Para usar estos comandos, rellene los valores y quite el elemento < and >.

Este es un ejemplo:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Para quitar una instancia de equilibrador de carga interno de un servicio en la nube, use los comandos siguientes:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

Para usar estos comandos, rellene el valor y quite el elemento < and >.

Este es un ejemplo:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>Información adicional sobre los cmdlet de equilibrador de carga interno

Para obtener información adicional sobre los cmdlets de Equilibrio de carga interno, ejecuta los comandos siguientes en un símbolo del sistema de Windows PowerShell:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Pasos siguientes

[Configurar un modo de distribución del equilibrador de carga mediante la afinidad IP de origen](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

