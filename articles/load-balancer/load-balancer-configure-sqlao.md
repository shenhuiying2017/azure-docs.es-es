---
title: "Configuración de Load Balancer para SQL always on | Microsoft Docs"
description: "Configuración del equilibrador de carga para trabajar con SQL always on y cómo puede sacar provecho de powershell para crear el equilibrador de carga para la implementación de SQL"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 1a1c3c15c51b1e441f21158510e92cc8de057352
ms.openlocfilehash: 75f05f003b691ee6464168453fa7935f1fae166e

---

# <a name="configure-load-balancer-for-sql-always-on"></a>Configuración del equilibrador de carga para SQL Always On

Los grupos de disponibilidad de SQL Server AlwaysOn se pueden ejecutar ahora con ILB. El grupo de disponibilidad es la solución estrella de SQL Server para conseguir alta disponibilidad y recuperación ante desastres. El agente de escucha de grupo de disponibilidad permite a las aplicaciones cliente conectarse sin problemas a la réplica principal, independientemente del número de réplicas en la configuración.

El nombre (DNS) del agente de escucha está asignado a una dirección IP de carga equilibrada y el equilibrador de carga de Azure dirige el tráfico entrante únicamente al servidor principal del conjunto de réplicas.

Puede usar ILB para los extremos de SQL Server AlwaysOn (escucha). Ahora tiene control sobre la accesibilidad del agente de escucha y puede elegir la dirección IP con equilibrio de carga de una subred específica de su red virtual (VNet).

Mediante el uso de ILB en el agente de escucha, solo pueden tener acceso al extremo del servidor SQL (por ejemplo, Server=tcp:ListenerName,1433;Database=DatabaseName):

* Servicios y máquinas virtuales en la misma red virtual
* Servicios y máquinas virtuales de redes locales conectadas
* Servicios y máquinas virtuales de redes virtuales interconectadas

![ILB_SQLAO_NewPic](./media/load-balancer-configure-sqlao/sqlao1.png)

Figura 1 - SQL AlwaysOn configurado con equilibrador de carga accesible desde Internet

## <a name="add-internal-load-balancer-to-the-service"></a>Agregar el equilibrador de carga interno al servicio

1. En el siguiente ejemplo, se configurará una red virtual que contiene una subred llamada "Subred-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Agregue los extremos con equilibrio de carga para ILB en cada máquina virtual.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    En el ejemplo anterior, tiene 2 máquinas virtuales llamadas "sqlsvc1" y "sqlsvc2" que se ejecutan en el servicio en la nube "Sqlsvc". Después de crear el ILB con el conmutador `DirectServerReturn`, agregue los puntos de conexión de carga equilibrada al ILB para permitir que SQL configure los agentes de escucha para los grupos de disponibilidad.

Para más información sobre SQL AlwaysOn, consulte [Configuración de un equilibrador de carga interno para un grupo de disponibilidad AlwaysOn de Azure](../virtual-machines/virtual-machines-windows-portal-sql-alwayson-int-listener.md?toc=%2fazure%2fload-balancer%2ftoc.json).

## <a name="see-also"></a>Otras referencias
[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)



<!--HONumber=Nov16_HO3-->


