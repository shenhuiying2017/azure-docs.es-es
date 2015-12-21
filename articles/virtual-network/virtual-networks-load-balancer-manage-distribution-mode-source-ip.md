<properties 
   pageTitle="Administración: Modo de distribución del equilibrador de carga (afinidad de IP de origen)"
   description="Características de administración para el modo de distribución del equilibrador de carga de Azure" 
   services="virtual-network" 
   documentationCenter="" 
   authors="telmosampaio" 
   manager="carmonm" 
   editor=""
   />

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/07/2015"
   ms.author="telmos"
   />
   
# Administrar la red virtual : Modo de distribución del equilibrador de carga (afinidad de IP de origen)
**Afinidad de IP de origen** (también conocida como **afinidad de la sesión** o **afinidad de IP del cliente**), un modo de distribución del equilibrador de carga de Azure, enlaza las conexiones de un solo cliente a un único servidor hospedado de Azure, en lugar de distribuir cada conexión de cliente de manera dinámica a distintos servidores hospedados de Azure (el comportamiento del equilibrador de carga predeterminado).

Al usar la afinidad de IP de origen, el equilibrador de carga de Azure se puede configurar para usar una combinación de 2-tupla (IP de origen, IP de destino) o una combinación de 3-tupla (IP de origen, IP de destino, protocolo) para asignar el tráfico al grupo de servidores hospedados de Azure disponibles. Cuando se usa la afinidad de IP de origen, un único extremo DIP (un único servidor hospedado de Azure) administra las conexiones iniciadas desde el mismo equipo cliente.

## Origen del servicio

Afinidad de IP de origen resuelve una [incompatibilidad anterior entre el equilibrador de carga de Azure y la puerta de enlace de escritorio remoto (DOC)](http://go.microsoft.com/fwlink/p/?LinkId=517389).

## Implementación

Afinidad de IP de origen se puede configurar para:

* [Extremos de máquina virtual](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Conjuntos de extremo de carga equilibrada](../load-balancer/load-balancer-overview.md)
* [Roles web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Roles de trabajo](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Escenarios
1. Clúster de puerta de enlace de escritorio remoto con un único servicio en la nube
2. Carga en medios (por ejemplo, UDP para datos, TCP para control)
  * El cliente inicia una sesión TCP en dirección IP pública de carga equilibrada de un servicio hospedado de Azure
  * El equilibrador de carga dirige una solicitud del cliente a una DIP; este canal permanece activo para supervisar el estado de la conexión
  * El cliente inicia una sesión UDP en la misma dirección IP pública de carga equilibrada de un servicio hospedado de Azure
  * El equilibrador de carga de Azure dirige la solicitud al mismo extremo DIP que la conexión TCP
  * El cliente carga el contenido multimedia con un mayor rendimiento de UDP manteniendo el canal de control sobre TCP para mayor confiabilidad
  
## Advertencias
* Si el conjunto de carga equilibrada cambia (por ejemplo, al agregar o quitar una máquina virtual), la distribución del canal de cliente se vuelve a calcular; las nuevas conexiones de los clientes existentes se pueden administrar con un servidor diferente al que se usó originalmente.
* El uso de la afinidad de IP de origen puede dar lugar a una distribución del tráfico desigual entre servidores hospedados de Azure.
* El equilibrador de carga de Azure puede considerar a los clientes que enrutan su tráfico a través de un servidor proxy como un único cliente.

## Ejemplos de PowerShell
Descargue la [última versión de Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) para obtener mejores resultados.

### Agregar un extremo de Azure a una máquina virtual y establecer el modo de distribución del equilibrador de carga

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 â€“LoadBalancerDistribution â€œsourceIPâ€�| Update-AzureVM  

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 Ã¢â‚¬â€œLoadBalancerDistribution Ã¢â‚¬Å“sourceIPÃ¢â‚¬ï¿½| Update-AzureVM  

LoadBalancerDistribution puede establecerse en sourceIP para equilibrio de carga de 2-tupla (IP de origen, IP de destino), sourceIPProtocol para equilibrio de carga de 3-tupla (IP de origen, IP de destino, protocolo) o ninguno si desea el comportamiento predeterminado (equilibrio de carga de 5-tupla).

### Recuperar una configuración de modo de distribución del equilibrador de carga de extremo
    PS C:\> Get-AzureVM â€“ServiceName "mySvc" -Name "MyVM1" | Get-AzureEndpoint
    
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

Si el elemento LoadBalancerDistribution no está presente, el equilibrador de carga de Azure usa el algoritmo predeterminado de 5-tupla.

### Establecer el modo de distribución en un conjunto de extremo de carga equilibrada

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 â€“LoadBalancerDistribution "sourceIP"

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 Ã¢â‚¬â€œLoadBalancerDistribution "sourceIP"
    
Si los extremos forman parte de un conjunto de extremo de carga equilibrada, el modo de distribución debe establecerse en el conjunto de extremo de carga equilibrada.

## Ejemplos de servicio en la nube

Puede aprovechar el SDK de Azure para .NET para actualizar el servicio en la nube.

La configuración de extremo para los servicios en la nube se realiza en el archivo .csdef. Para actualizar el modo de distribución del equilibrador de carga para una implementación de servicios en la nube, se requiere una actualización de la implementación.

Este es un ejemplo de los cambios de .csdef para la configuración de extremo:

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
    
## Ejemplos de API

Los desarrolladores pueden configurar la distribución del equilibrador de carga con la API de administración de servicios. Asegúrese de agregar el encabezado x-ms-version y que esté establecido en la versión 2014-09-01 o posterior.

### Actualizar la configuración del conjunto de carga equilibrada especificado en una implementación

#### Solicitud

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>?comp=UpdateLbSet 
    
    x-ms-version: 2014-09-01 
    
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

#### Response

    HTTP/1.1 202 Accepted 
    Cache-Control: no-cache 
    Content-Length: 0 
    Server: 1.0.6198.146 (rd_rdfe_stable.141015-1306) Microsoft-HTTPAPI/2.0 
    x-ms-servedbyregion: ussouth2 
    x-ms-request-id: 9c7bda3e67c621a6b57096323069f7af 
    Date: Thu, 16 Oct 2014 22:49:21 GMT
 

<!---HONumber=AcomDC_1210_2015-->