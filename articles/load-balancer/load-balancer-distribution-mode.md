---
title: "Configuración de un modo de distribución de Load Balancer | Microsoft Docs"
description: "Cómo configurar el modo de distribución del equilibrador de carga de Azure para admitir la afinidad de IP de origen"
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
ms.openlocfilehash: a6b3c346358e0aed4c60c4903932236edc237379
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2017
---
# <a name="configure-the-distribution-mode-for-load-balancer"></a>Configuración del modo de distribución en el equilibrador de carga

[!INCLUDE [load-balancer-basic-sku-include.md](../../includes/load-balancer-basic-sku-include.md)]

## <a name="hash-based-distribution-mode"></a>Distribución basada en hash

El algoritmo de distribución predeterminado para asignar el tráfico a los servidores disponibles es un hash de 5-tupla (IP de origen, puerto de origen, IP de destino, puerto de destino, tipo de protocolo). Dicho algoritmo solo proporciona adherencia dentro de una sesión de transporte. Los paquetes de la misma sesión se dirigirán a la misma instancia de IP del centro de datos (DIP) tras el punto de conexión con equilibrio de carga. Cuando el cliente inicia una nueva sesión desde la misma IP de origen, el puerto de origen cambia y provoca que el tráfico vaya hacia otro punto de conexión DIP.

![equilibrador de carga basado en hash](./media/load-balancer-distribution-mode/load-balancer-distribution.png)

Figura 1 - Distribución de 5-tupla

## <a name="source-ip-affinity-mode"></a>Modo de afinidad de IP de origen

Tenemos un nuevo modo de distribución llamado Afinidad de IP de origen (también conocido como afinidad de cliente o afinidad de IP de cliente). Para asignar el tráfico a los servidores disponibles, se puede configurar Azure Load Balancer para usar una 2-tupla (IP de origen, IP de destino) o una 3-tupla (IP de origen, IP de destino, protocolo). Al usar la afinidad de IP de origen, las conexiones que se iniciaron desde el mismo equipo cliente van al mismo punto de conexión de DIP.

En el siguiente diagrama, se ilustra una configuración de 2-tupla. Observe cómo la 2-tupla se ejecuta a través del equilibrador de carga en la máquina virtual 1 (VM1) de la que luego VM2 y VM3 realizan una copia de seguridad.

![afinidad de la sesión](./media/load-balancer-distribution-mode/load-balancer-session-affinity.png)

Figura 2 - Distribución de 2-tupla

La afinidad de IP de origen resuelve una incompatibilidad entre Azure Load Balancer y la Puerta de enlace de Escritorio remoto (RD). Ahora, puede crear una granja de servidores de puerta de enlace de Escritorio remoto en un solo servicio en la nube.

Otro caso de uso es la carga de contenido multimedia donde la carga de los datos tiene lugar a través de UDP pero el plano de control se consigue mediante TCP:

* Un cliente inicia primero una sesión TCP en la dirección pública con equilibrio de carga y se le dirige a una DIP específica; este canal se deja activo para supervisar el estado de conexión.
* Una nueva sesión UDP se inicia desde el mismo equipo cliente en el mismo extremo público con equilibrio de carga. Lo que se espera aquí, es que esta conexión también se dirija al mismo extremo DIP que la conexión TCP anterior, de modo que la carga de contenido multimedia se pueda ejecutar con un elevado rendimiento, al mismo tiempo que se mantiene también un canal de control a través de TCP.

> [!NOTE]
> Cuando el conjunto con equilibrio de carga cambia (al quitar o agregar una máquina virtual), la distribución de las solicitudes de cliente se vuelve a calcular. No puede depender de nuevas conexiones desde clientes existentes que terminan en el mismo servidor. Además, el uso del modo de distribución de afinidad de IP de origen puede ocasionar una distribución desigual del tráfico. Los clientes que se ejecutan detrás de servidores proxy pueden considerarse como una sola aplicación cliente.

## <a name="configuring-source-ip-affinity-settings-for-load-balancer"></a>Configuración de la afinidad de IP de origen para el equilibrador de carga

En las máquinas virtuales, puede usar PowerShell para cambiar la configuración de tiempo de espera:

Agregar un extremo de Azure a una máquina virtual y establecer el modo de distribución del equilibrador de carga

```powershell
Get-AzureVM -ServiceName mySvc -Name MyVM1 | Add-AzureEndpoint -Name HttpIn -Protocol TCP -PublicPort 80 -LocalPort 8080 –LoadBalancerDistribution sourceIP | Update-AzureVM
```

LoadBalancerDistribution puede establecerse en sourceIP para equilibrio de carga de 2-tupla (dirección IP de origen, dirección IP de destino), en sourceIPProtocol para equilibrio de carga de 3-tupla (dirección IP de origen, IP de destino, protocolo) o en ninguno si desea el comportamiento predeterminado de equilibrio de carga de 5-tupla.

Utilice lo siguiente para recuperar una configuración de modo de distribución del equilibrador de carga de punto de conexión:

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

Si el elemento LoadBalancerDistribution no está presente, el Equilibrador de carga de Azure usa el algoritmo predeterminado de 5-tupla.

### <a name="set-the-distribution-mode-on-a-load-balanced-endpoint-set"></a>Establecer el modo de distribución en un conjunto de extremo de carga equilibrada

Si los extremos forman parte de un conjunto de extremos con equilibrio de carga, el modo de distribución debe establecerse en el conjunto de extremos con equilibrio de carga:

```powershell
Set-AzureLoadBalancedEndpoint -ServiceName MyService -LBSetName LBSet1 -Protocol TCP -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 –LoadBalancerDistribution sourceIP
```

### <a name="cloud-service-configuration-to-change-distribution-mode"></a>Configuración de servicios en la nube para cambiar el modo de distribución

Puede aprovechar el SDK de Azure para .NET 2.5 para actualizar el servicio en la nube. La configuración de extremo para los servicios en la nube se realiza en el archivo .csdef. Para actualizar el modo de distribución del equilibrador de carga para una implementación de servicios en la nube, se requiere una actualización de la implementación.
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

Puede configurar la distribución del equilibrador de carga con Service Management API. Asegúrese de agregar el encabezado `x-ms-version` y que esté establecido en la versión `2014-09-01` o posterior.

### <a name="update-the-configuration-of-the-specified-load-balanced-set-in-a-deployment"></a>Actualizar la configuración del conjunto de carga equilibrada especificado en una implementación

#### <a name="request-example"></a>Ejemplo de solicitud

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

El valor de LoadBalancerDistribution puede ser sourceIP para la afinidad de 2-tupla, sourceIPProtocol para la afinidad de 3-tupla o ninguno (sin afinidad, es decir, 5-tupla).

#### <a name="response"></a>Response

    HTTP/1.1 202 Accepted
    Cache-Control: no-cache
    Content-Length: 0
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0
    x-ms-servedbyregion: ussouth2
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af
    Date: Thu, 16 Oct 2014 22:49:21 GMT

## <a name="next-steps"></a>Pasos siguientes

[Información general sobre el equilibrador de carga interno](load-balancer-internal-overview.md)

[Introducción a la configuración de un equilibrador de carga accesible desde Internet](load-balancer-get-started-internet-arm-ps.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)
