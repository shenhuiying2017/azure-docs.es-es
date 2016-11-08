---
title: Configuración del tiempo de espera de inactividad de TCP de Load Balancer | Microsoft Docs
description: Configuración del tiempo de espera de inactividad de TCP de Load Balancer
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: ''

ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/03/2016
ms.author: sewhee

---
# Cambio de la configuración de tiempo de espera de inactividad de TCP para Load Balancer
En su configuración predeterminada, Azure Load Balancer tiene una configuración de tiempo de espera de inactividad de 4 minutos.

Esto significa que si un período de inactividad es mayor que el valor de tiempo de espera, no hay ninguna garantía de que todavía exista la sesión TCP o HTTP entre el cliente y el servicio en la nube.

Cuando se cierra la conexión, la aplicación cliente recibirá un mensaje de error similar a "Se ha terminado la conexión subyacente: una conexión que se esperaba que se mantuviera activa fue cerrada por el servidor".

Una práctica común para mantener la conexión activa durante un período más largo es usar TCP Keep-alive. (Puede encontrar [ejemplos de .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx)). Los paquetes se envían cuando no se detecta ninguna actividad en la conexión. La actividad de la red garantiza que nunca se alcance el valor de tiempo de espera de inactividad y la conexión se mantenga durante un largo período.

Para evitar la pérdida de la conexión, debe configurar TCP keep-alive con un intervalo menor que el valor de tiempo de espera de inactividad o aumentar el valor de tiempo de espera de inactividad.

Aunque TCP keep-alive funciona bien en escenarios en los que una batería no supone una restricción, por lo general no se recomienda hacerlo en aplicaciones móviles. El uso de TCP Keep-alive desde una aplicación móvil probablemente agotará la batería del dispositivo más rápidamente.

Para admitir tales escenarios, hemos agregado compatibilidad con un tiempo de espera de inactividad configurable. Ahora puede establecer una duración de entre 4 y 30 minutos. Esta configuración funciona solo para conexiones entrantes.

![Tiempo de espera TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Las secciones siguientes describen cómo cambiar la configuración de tiempo de espera de inactividad en máquinas virtuales y servicios en la nube.

> [!NOTE]
> Para admitir la configuración de estos valores, asegúrese de que ha instalado el paquete más reciente de Azure PowerShell.
> 
> 

## Configuración del tiempo de espera de TCP para la IP pública a nivel de instancia en 15 minutos
    Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15

`IdleTimeoutInMinutes` es opcional. Si no se establece, el tiempo de espera predeterminado es de 4 minutos.

> [!NOTE]
> El intervalo de tiempo de espera aceptable está entre 4 y 30 minutos.
> 
> 

## Establecimiento del tiempo de espera de inactividad al crear un punto de conexión de Azure en una máquina virtual
Cambiar la configuración de tiempo de espera de un punto de conexión:

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

Recuperar la configuración de tiempo de espera de inactividad:

    PS C:\> Get-AzureVM -ServiceName "MyService" -Name "MyVM" | Get-AzureEndpoint
    VERBOSE: 6:43:50 PM - Completed Operation: Get Deployment
    LBSetName : MyLoadBalancedSet
    LocalPort : 80
    Name : HTTP
    Port : 80
    Protocol : tcp
    Vip : 65.52.xxx.xxx
    ProbePath :
    ProbePort : 80
    ProbeProtocol : tcp
    ProbeIntervalInSeconds : 15
    ProbeTimeoutInSeconds : 31
    EnableDirectServerReturn : False
    Acl : {}
    InternalLoadBalancerName :
    IdleTimeoutInMinutes : 15

## Establecimiento del tiempo de espera de TCP en un conjunto de puntos de conexión de carga equilibrada
Si los puntos de conexión forman parte de un conjunto de puntos de conexión de carga equilibrada, el tiempo de espera de TCP se debe establecer en el conjunto de puntos de conexión de carga equilibrada:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Cambio de la configuración de tiempo de espera de los servicios en la nube
Puede aprovechar el SDK de Azure para .NET 2.4 para actualizar el servicio en la nube.

La configuración de punto de conexión para los servicios en la nube se realiza en el archivo .csdef. La actualización del tiempo de espera de TCP para la implementación de un servicio en la nube requiere una actualización de la implementación. Se da una excepción si el tiempo de espera de TCP solo se especifica para una dirección IP pública. La configuración de IP pública se encuentra en el archivo .cscfg y se puede actualizar a través de la actualización de la implementación.

Los cambios de .csdef para la configuración de extremo son:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>

Los cambios de .cscfg para el valor de tiempo de espera en las direcciones IP públicas son:

    <NetworkConfiguration>
      <VirtualNetworkSite name="VNet"/>
      <AddressAssignments>
    <InstanceAddress roleName="VMRolePersisted">
      <PublicIPs>
        <PublicIP name="public-ip-name" idleTimeoutInMinutes="timeout-in-minutes"/>
      </PublicIPs>
    </InstanceAddress>
      </AddressAssignments>
    </NetworkConfiguration>

## Ejemplo de API de REST
Puede configurar el tiempo de espera de inactividad de TCP mediante Service Management API. Asegúrese de agregar el encabezado x-ms-version y que esté establecido en la versión 2014-06-01 o posterior.

Actualice la configuración de los puntos de conexión de entrada de carga equilibrada especificados en todas las máquinas virtuales de una implementación.

    Request:

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>
<BR>

    Response:

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
    <InputEndpoint>
    <LoadBalancedEndpointSetName>endpoint-set-name</LoadBalancedEndpointSetName>
    <LocalPort>local-port-number</LocalPort>
    <Port>external-port-number</Port>
    <LoadBalancerProbe>
    <Path>path-of-probe</Path>
    <Port>port-assigned-to-probe</Port>
    <Protocol>probe-protocol</Protocol>
    <IntervalInSeconds>interval-of-probe</IntervalInSeconds>
    <TimeoutInSeconds>timeout-for-probe</TimeoutInSeconds>
    </LoadBalancerProbe>
    <LoadBalancerName>name-of-internal-loadbalancer</LoadBalancerName>
    <Protocol>endpoint-protocol</Protocol>
    <IdleTimeoutInMinutes>15</IdleTimeoutInMinutes>
    <EnableDirectServerReturn>enable-direct-server-return</EnableDirectServerReturn>
    <EndpointACL>
    <Rules>
    <Rule>
    <Order>priority-of-the-rule</Order>
    <Action>permit-rule</Action>
    <RemoteSubnet>subnet-of-the-rule</RemoteSubnet>
    <Description>description-of-the-rule</Description>
    </Rule>
    </Rules>
    </EndpointACL>
    </InputEndpoint>
    </LoadBalancedEndpointList>

## Pasos siguientes
[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la creación de un equilibrador de carga orientado a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

<!---HONumber=AcomDC_0914_2016-->