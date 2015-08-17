<properties 
   pageTitle="Configuración del equilibrador de carga para SQL always on | Microsoft Azure"
   description="Configuración del equilibrador de carga para trabajar con SQL always on y cómo puede sacar provecho de powershell para crear el equilibrador de carga para la implementación de SQL"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />

# Configuración del equilibrador de carga para SQL Always On

Los grupos de disponibilidad de SQL Server AlwaysOn se pueden ejecutar ahora con ILB. El grupo de disponibilidad es la solución estrella de SQL Server para conseguir alta disponibilidad y recuperación ante desastres. El agente de escucha de grupo de disponibilidad permite a las aplicaciones cliente conectarse sin problemas a la réplica principal, independientemente del número de réplicas en la configuración.

El nombre (DNS) del agente de escucha está asignado a una dirección IP con equilibrio de carga y el Equilibrador de carga de Azure dirige el tráfico entrante únicamente al servidor principal del conjunto de réplicas.


Puede usar ILB para los extremos de SQL Server AlwaysOn (escucha). Ahora tiene control sobre la accesibilidad del agente de escucha y puede elegir la dirección IP con equilibrio de carga de una subred específica de su red virtual (VNet).

Mediante el uso de ILB en el agente de escucha, solo pueden tener acceso al extremo del servidor SQL (por ejemplo, Server=tcp:ListenerName,1433;Database=DatabaseName):

Los servicios y las máquinas virtuales de los mismos servicios de red virtual, las máquinas virtuales de servicios de red locales conectados y las máquinas virtuales de redes virtuales interconectadas.

![ILB\_SQLAO\_NewPic](./media/load-balancer-configure-sqlao/sqlao1.jpg)


El equilibrador de carga interno solo puede configurarse a través de PowerShell.


## Agregar el equilibrador de carga interno al servicio 

### Paso 1.

En el siguiente ejemplo, se configurará una red virtual que contiene una subred llamada "Subred-1":

	Add-AzureInternalLoadBalancer -InternalLoadBalancerName ILB_SQL_AO -SubnetName Subnet-1 -ServiceName SqlSvc

Paso 2:

## Agregue los extremos con equilibrio de carga para ILB en cada máquina virtual.

	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc1 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –
	DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM

 	Get-AzureVM -ServiceName SqlSvc -Name sqlsvc2 | Add-AzureEndpoint -Name "LisEUep" -LBSetName "ILBSet1" -Protocol tcp -LocalPort 1433 -PublicPort 1433 -ProbePort 59999 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 –DirectServerReturn $true -InternalLoadBalancerName ILB_SQL_AO | Update-AzureVM


## Otras referencias

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-internet-getstarted.md)

[Introducción a la configuración de un equilibrador de carga interno](load-balancer-internal-getstarted.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
 

<!---HONumber=August15_HO6-->