---
title: "Configuración del tiempo de espera de inactividad de TCP de Load Balancer | Microsoft Docs"
description: "Configuración del tiempo de espera de inactividad de TCP de Load Balancer"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: 4625c6a8-5725-47ce-81db-4fa3bd055891
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: f19ac77f7c7f7d4ab8909d628f9dcce08c07c928
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-tcp-idle-timeout-settings-for-azure-load-balancer"></a>Modificación de la configuración de tiempo de espera de inactividad de TCP para Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

En su configuración predeterminada, Azure Load Balancer tiene una configuración de tiempo de espera de inactividad de 4 minutos. Si un período de inactividad es mayor que el valor de tiempo de espera, no hay ninguna garantía de que todavía exista la sesión TCP o HTTP entre el cliente y el servicio en la nube.

Cuando se cierra la conexión, la aplicación cliente recibirá un mensaje de error similar a "Se ha terminado la conexión subyacente: una conexión que se esperaba que se mantuviera activa fue cerrada por el servidor".

Una práctica común es usar TCP Keep-alive. Esta práctica mantiene la conexión activa durante un periodo más largo. Para obtener más información, consulte estos [ejemplos de .NET](https://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Con Keep-alive habilitado, los paquetes se envían durante los periodos de inactividad en la conexión. Estos paquetes de Keep-alive garantizan que nunca se alcance el valor de tiempo de espera de inactividad y la conexión se mantenga durante un largo período.

Esta configuración funciona solo para conexiones entrantes. Para evitar la pérdida de la conexión, debe configurar TCP keep-alive con un intervalo menor que el valor de tiempo de espera de inactividad o aumentar el valor de tiempo de espera de inactividad. Para admitir tales escenarios, hemos agregado compatibilidad con un tiempo de espera de inactividad configurable. Ahora puede establecer una duración de entre 4 y 30 minutos.

TCP Keep-alive funciona bien en escenarios donde la batería no supone una restricción. No se recomienda para aplicaciones móviles. El uso de TCP Keep-alive desde una aplicación móvil puede agotarla batería del dispositivo más rápidamente.

![Tiempo de espera TCP](./media/load-balancer-tcp-idle-timeout/image1.png)

Las secciones siguientes describen cómo cambiar la configuración de tiempo de espera de inactividad en máquinas virtuales y servicios en la nube.

## <a name="configure-the-tcp-timeout-for-your-instance-level-public-ip-to-15-minutes"></a>Configuración del tiempo de espera de TCP para la IP pública a nivel de instancia en 15 minutos

```powershell
Set-AzurePublicIP -PublicIPName webip -VM MyVM -IdleTimeoutInMinutes 15
```

`IdleTimeoutInMinutes` es opcional. Si no se establece, el tiempo de espera predeterminado es de 4 minutos. El intervalo de tiempo de espera aceptable está entre 4 y 30 minutos.

## <a name="set-the-idle-timeout-when-creating-an-azure-endpoint-on-a-virtual-machine"></a>Establecimiento del tiempo de espera de inactividad al crear un punto de conexión de Azure en una máquina virtual

Para cambiar la configuración de tiempo de espera de un punto de conexión, siga estos pasos:

```powershell
Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM
```

Para recuperar la configuración de tiempo de espera de inactividad, use el siguiente comando:

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

## <a name="set-the-tcp-timeout-on-a-load-balanced-endpoint-set"></a>Establecimiento del tiempo de espera de TCP en un conjunto de puntos de conexión de carga equilibrada

Si los puntos de conexión forman parte de un conjunto de extremo de carga equilibrada, el tiempo de espera de TCP se debe establecer en el conjunto de punto de conexión de carga equilibrada. Por ejemplo:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15
```

## <a name="change-timeout-settings-for-cloud-services"></a>Cambio de la configuración de tiempo de espera de los servicios en la nube

Puede aprovechar Azure SDK para actualizar el servicio en la nube. La configuración de punto de conexión para los servicios en la nube se realiza en el archivo .csdef. La actualización del tiempo de espera de TCP para la implementación de un servicio en la nube requiere una actualización de la implementación. Se da una excepción si el tiempo de espera de TCP solo se especifica para una dirección IP pública. La configuración de IP pública se encuentra en el archivo .cscfg y se puede actualizar a través de la actualización de la implementación.

Los cambios de .csdef para la configuración de extremo son:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
    </Endpoints>
</WorkerRole>
```

Los cambios de .cscfg para el valor de tiempo de espera en las direcciones IP públicas son:

```xml
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
```

## <a name="rest-api-example"></a>Ejemplo de API de REST

Puede configurar el tiempo de espera de inactividad de TCP mediante Service Management API. Asegúrese de que el encabezado `x-ms-version` esté establecido en la versión `2014-06-01` o posterior. Actualice la configuración de los puntos de conexión de entrada de carga equilibrada especificados en todas las máquinas virtuales de una implementación.

### <a name="request"></a>Solicitud

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

### <a name="response"></a>Response

```xml
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
```

## <a name="next-steps"></a>Pasos siguientes

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la creación de un equilibrador de carga orientado a Internet](load-balancer-get-started-internet-arm-ps.md)

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)
