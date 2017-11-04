---
title: "Configuración de un modo de distribución de Azure Load Balancer | Microsoft Docs"
description: "Cómo configurar el modo de distribución de Azure Load Balancer para admitir la afinidad de IP de origen."
services: load-balancer
documentationcenter: na
author: KumudD
manager: timlt
ms.assetid: 7df27a4d-67a8-47d6-b73e-32c0c6206e6e
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: kumud
ms.openlocfilehash: d04a469c04553b7d6a14df7054ad5ef795baa500
ms.sourcegitcommit: b979d446ccbe0224109f71b3948d6235eb04a967
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/25/2017
---
# <a name="configure-the-distribution-mode-for-azure-load-balancer"></a>Configuración del modo de distribución de Azure Load Balancer

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Distribución basada en hash

El modo de distribución predeterminado de Azure Load Balancer es un hash con una tupla de 5 elementos. La tupla se compone de la IP de origen, el puerto de origen, la IP de destino, el puerto de destino y el tipo de protocolo. El hash se utiliza para asignar el tráfico a los servidores disponibles y el algoritmo proporciona adherencia solo dentro de una sesión de transporte. Los paquetes que se encuentran en la misma sesión se dirigen a la misma instancia de IP del centro de datos (DIP) tras el punto de conexión con equilibrio de carga. Cuando el cliente inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia y provoca que el tráfico vaya hacia otro punto de conexión DIP.

![Distribución basada en un hash con una tupla de 5 elementos](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

## <a name="source-ip-affinity-mode"></a>Modo de afinidad de IP de origen

Load Balancer también se puede configurar mediante el modo de distribución de afinidad de IP de origen. Este modo de distribución también se conoce como afinidad de la sesión o afinidad de IP del cliente. El modo utiliza un hash con una tupla de 2 elementos (IP de origen e IP de destino) o de 3 elementos (IP de origen, IP de destino y tipo de protocolo) para asignar el tráfico a los servidores disponibles. Al usar la afinidad de IP de origen, las conexiones que se han iniciado desde el mismo equipo cliente van al mismo punto de conexión de DIP.

En la figura siguiente, se ilustra la configuración de una tupla de 2 elementos. Observe cómo la tupla de 2 elementos se ejecuta a través del equilibrador de carga en la máquina virtual 1 (VM1). A continuación, VM2 y VM3 realizan una copia de seguridad de VM1.

![Modo de distribución de afinidad de la sesión de una tupla de 2 elementos](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

El modo de afinidad de IP de origen resuelve una incompatibilidad entre Azure Load Balancer y la Puerta de enlace de Escritorio remoto (RD Gateway). Al utilizar este modo, puede crear una granja de servidores de Puerta de enlace de Escritorio remoto en un solo servicio en la nube.

Otro escenario de caso de uso es la carga de elementos multimedia. La carga de datos tiene lugar a través de UDP, pero el plano de control se consigue mediante TCP:

* Un cliente inicia una sesión TCP en la dirección pública de carga equilibrada y se dirige a una DIP específica. El canal se mantiene activo para supervisar el estado de conexión.
* Se inicia una nueva sesión UDP desde el mismo equipo cliente al mismo punto de conexión público de carga equilibrada. La conexión se dirige al mismo punto de conexión DIP que la conexión TCP anterior. La carga de elementos multimedia se puede ejecutar a alto rendimiento al tiempo que mantiene un canal de control a través de TCP.

> [!NOTE]
> Cuando un conjunto de carga equilibrada cambia al quitar o agregar una máquina virtual, la distribución de las solicitudes de cliente se vuelve a calcular. No puede depender de nuevas conexiones desde clientes existentes para terminar en el mismo servidor. Además, el uso del modo de distribución de afinidad de IP de origen puede ocasionar una distribución desigual del tráfico. Los clientes que se ejecutan detrás de servidores proxy pueden considerarse como una sola aplicación cliente.

## <a name="configure-source-ip-affinity-settings"></a>Configuración de la afinidad de IP de origen

En las máquinas virtuales, utilice Azure PowerShell para cambiar la configuración de tiempo de espera. Agregue un punto de conexión de Azure a una máquina virtual y configure el modo de distribución del equilibrador de carga:

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

Establezca el valor del elemento `LoadBalancerDistribution` para la cantidad deseada de equilibrio de carga. Especifique sourceIP para el equilibrio de carga con tupla de 2 elementos (IP de origen e IP de destino). Especifique sourceIPProtocol para el equilibrio de carga con tupla de 3 elementos (IP de origen, IP de destino y tipo de protocolo). Especifique none para el comportamiento predeterminado de equilibrio de carga con tupla de 5 elementos.

Recupere una configuración de modo de distribución del equilibrador de carga de punto de extremo mediante estos valores:

    PS C:\> Get-AzureVM –ServiceName MyService –Name MyVM | Get-AzureEndpoint

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
    LoadBalancerDistribution : sourceIP

Cuando el elemento `LoadBalancerDistribution` no está presente, Azure Load Balancer usa el algoritmo de tupla de 5 elementos predeterminado.

### <a name="configure-distribution-mode-on-load-balanced-endpoint-set"></a>Configuración del modo de distribución en un conjunto de puntos de conexión de carga equilibrada

Cuando los puntos de conexión forman parte de un conjunto de puntos de conexión de carga equilibrada, el modo de distribución debe configurarse en el conjunto de puntos de conexión de carga equilibrada:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="configure-distribution-mode-for-cloud-services-endpoints"></a>Configuración del modo de distribución para puntos de conexión de Cloud Services

Utilice el SDK de Azure para .NET 2.5 para actualizar el servicio en la nube. Los valores del punto de conexión para Cloud Services se establecen en el archivo .csdef. Para actualizar el modo de distribución del equilibrador de carga para una implementación de Cloud Services, se requiere una actualización de la implementación.

Este es un ejemplo de los cambios de .csdef para la configuración de extremo:

```xml
<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
    <Endpoints>
    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancerDistribution="sourceIP" />
    </Endpoints>
</WorkerRole>
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

## <a name="api-example"></a>Ejemplo de API

En el ejemplo siguiente se muestra cómo volver a configurar el modo de distribución del equilibrador de carga para un conjunto específico de carga equilibrada en una implementación. 

### <a name="change-distribution-mode-for-deployed-load-balanced-set"></a>Cambiar el modo de distribución para un conjunto de carga equilibrada implementado

Utilice el modelo de implementación clásica de Azure para cambiar una configuración de implementación existente. Agregue el `x-ms-version` encabezado y establezca el valor en la versión 2014-09-01 o posterior.

#### <a name="request"></a>Solicitud

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet   x-ms-version: 2014-09-01
    Content-Type: application/xml

    <LoadBalancedEndpointList xmlns="http://schemas.microsoft.com/windowsazure" xmlns:i="http://www.w3.org/2001/XMLSchema-instance">
      <InputEndpoint>
        <LoadBalancedEndpointSetName> endpoint-set-name </LoadBalancedEndpointSetName>
        <LocalPort> local-port-number </LocalPort>
        <Port> external-port-number </Port>
        <LoadBalancerProbe>
          <Port> port-assigned-to-probe </Port>
          <Protocol> probe-protocol </Protocol>
          <IntervalInSeconds> interval-of-probe </IntervalInSeconds>
          <TimeoutInSeconds> timeout-for-probe </TimeoutInSeconds>
        </LoadBalancerProbe>
        <Protocol> endpoint-protocol </Protocol>
        <EnableDirectServerReturn> enable-direct-server-return </EnableDirectServerReturn>
        <IdleTimeoutInMinutes>idle-time-out</IdleTimeoutInMinutes>
        <LoadBalancerDistribution>sourceIP</LoadBalancerDistribution>
      </InputEndpoint>
    </LoadBalancedEndpointList>

Como se ha descrito anteriormente, establezca el elemento `LoadBalancerDistribution` en sourceIP para la afinidad con tupla de 2 elementos, sourceIPProtocol para la afinidad con tupla de 3 elementos o none para los casos sin afinidad (afinidad con tupla de 5 elementos).

#### <a name="response"></a>Respuesta

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Pasos siguientes

* [Información general sobre el equilibrador de carga interno de Azure](load-balancer-internal-overview.md)
* [Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)
* [Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
