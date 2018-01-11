---
title: "Configuración de Load Balancer para Always On de SQL Server | Microsoft Docs"
description: "Configure Load Balancer para funcionar con Always On de SQL Server y aprenda a usar PowerShell para crear el equilibrador de carga para la implementación de SQL Server."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: d7bc3790-47d3-4e95-887c-c533011e4afd
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: 5e890f8314c8f191dbfa6c6818d810b91d0e829d
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/11/2017
---
# <a name="configure-a-load-balancer-for-sql-server-always-on"></a>Configuración de un equilibrador de carga para Always On de SQL Server

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

Grupos de disponibilidad Always On de SQL Server ahora se pueden ejecutar con un equilibrador de carga interno. Un grupo de disponibilidad es la solución estrella de SQL Server para conseguir alta disponibilidad y recuperación ante desastres. El agente de escucha de grupo de disponibilidad permite a las aplicaciones cliente conectarse sin problemas a la réplica principal, independientemente del número de réplicas en la configuración.

El nombre del agente de escucha (DNS) se asigna a una dirección IP de carga equilibrada. Azure Load Balancer dirige el tráfico entrante al servidor principal en el conjunto de réplicas.

Puede usar la compatibilidad con el equilibrador de carga interno para los puntos de conexión de Always On de SQL Server (agente de escucha). Ahora tiene control sobre la accesibilidad del agente de escucha. Puede elegir la dirección IP de carga equilibrada de una subred específica de la red virtual.

Mediante el uso un equilibrador de carga interno en el agente de escucha, solo se puede acceder al punto de conexión de SQL Server (por ejemplo, Server=tcp:ListenerName,1433;Database=DatabaseName) a través de:

* Servicios y máquinas virtuales de la misma red virtual.
* Servicios y máquinas virtuales de redes locales conectadas.
* Servicios y máquinas virtuales de redes virtuales conectadas.

![Equilibrador de carga para Always On de SQL Server](./media/load-balancer-configure-sqlao/sqlao1.png)

## <a name="add-an-internal-load-balancer-to-the-service"></a>Adición de un equilibrador de carga interno al servicio

1. En el siguiente ejemplo, se configura una red virtual que contiene una subred llamada "Subnet-1":

    ```powershell
    Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc
    ```
2. Agregue puntos de conexión de carga equilibrada para un equilibrador de carga interno en cada máquina virtual.

    ```powershell
    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -
    DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

    Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM
    ```

    En el ejemplo anterior, tiene dos máquinas virtuales denominadas "sqlsvc1" y "sqlsvc2" que se ejecutan en el servicio de nube "Sqlsvc". Después de crear el equilibrador de carga interno con el modificador `DirectServerReturn`, debe agregar puntos de conexión de carga equilibrada al equilibrador de carga interno. Los puntos de conexión de carga equilibrada permiten a SQL Server configurar los agentes de escucha para los grupos de disponibilidad.

Para obtener más información sobre SQL Always On de SQL Server, consulte [Configure an internal load balancer for an Always On availability group in Azure](../virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener.md) (Configuración de un equilibrador de carga interno para un grupo de disponibilidad Always On de Azure).

## <a name="see-also"></a>Otras referencias
* [Introducción a la configuración de un equilibrador de carga público](load-balancer-get-started-internet-arm-ps.md)
* [Introducción a la configuración de un equilibrador de carga interno](load-balancer-get-started-ilb-arm-ps.md)
* [Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)
* [Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
