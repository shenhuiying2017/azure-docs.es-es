<properties 
   authors="danielceckert" 
   documentationCenter="dev-center-name" 
   editor=""
   manager="jefco" 
   pageTitle="Administrar: Tiempo de espera de inactividad del equilibrador de carga" 
   description="Características de administración para el tiempo de espera de inactividad del equilibrador de carga de Azure" 
   services="virtual-network" 
   />

<tags
   ms.author="danecke"
   ms.date="05/27/2015"
   ms.devlang="na"
   ms.service="virtual-network"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   />
   
# Administrar la red virtual: Tiempo de espera de inactividad de TCP del equilibrador de carga

**Tiempo de espera de inactividad de TCP** permite al desarrollador especificar un umbral garantizado de inactividad durante las sesiones de cliente/servidor que involucran al equilibrador de carga de Azure. Un valor de tiempo de espera de inactividad de TCP de 4 minutos (el valor predeterminado para el equilibrador de carga de Azure) significa que si hay un período de inactividad de más de 4 minutos durante una sesión de cliente/servidor que involucra al equilibrador de carga de Azure, se cerrará la conexión.

Cuando se cierra una conexión de cliente/servidor, la aplicación cliente obtendrá un mensaje de error similar a "Se cerró la conexión subyacente: Una conexión que se esperaba que se mantuviera activa fue cerrada por el servidor".

[Mantener conexión TCP](http://tools.ietf.org/html/rfc1122#page-101) es una práctica común para mantener las conexiones durante un período prolongado o inactivo [(ejemplo de MSDN)](http://msdn.microsoft.com/library/system.net.servicepoint.settcpkeepalive.aspx). Cuando se utiliza Mantener conexión TCP, un cliente envía paquetes simples periódicamente (por lo general, con un período de frecuencia menor que el umbral de tiempo de espera de inactividad del servidor). El servidor considera estas transmisiones como prueba de la actividad de conexión, incluso cuando no se produce ninguna otra actividad; por lo tanto, nunca se alcanza el valor de tiempo de espera de inactividad y la conexión se puede mantener durante un período de tiempo prolongado.

Aunque Mantener conexión TCP funciona bien, no suele ser una opción para las aplicaciones móviles ya que consume los recursos de energía limitados de los dispositivos móviles. Una aplicación móvil que use Mantener conexión TCP agotará la batería del dispositivo con más rapidez ya que obtiene la energía constantemente del uso de la red.

Para admitir escenarios de dispositivos móviles, el equilibrador de carga de Azure es compatible con un tiempo de espera de inactividad de TCP configurable. Los desarrolladores pueden establecer el tiempo de espera de inactividad de TCP para cualquier intervalo entre 4 y 30 minutos para las conexiones entrantes (el tiempo de espera de inactividad de TCP configurable no se aplica a las conexiones salientes). Esto permite a los clientes mantener una sesión mucho más extensa con un servidor con períodos de inactividad prolongados. Una aplicación en un dispositivo móvil aún puede aprovechar la técnica de Mantener conexión TCP para conservar las conexiones que esperan períodos de inactividad de más de 30 minutos, pero este tiempo de espera de inactividad de TCP mayor permite que las aplicaciones emitan solicitudes de Mantener conexión TCP con mucha menos frecuencia que antes, lo que reduce considerablemente la carga sobre los recursos de energía del dispositivo móvil.

## Implementación

El tiempo de espera de inactividad de TCP se puede configurar para:

* [Direcciones IP públicas en el nivel de instancia](http://msdn.microsoft.com/library/azure/dn690118.aspx)
* [Conjuntos de extremo de carga equilibrada](http://msdn.microsoft.com/library/azure/dn655055.aspx)
* [Extremos de máquina virtual](../virtual-machines/virtual-machines-set-up-endpoints.md)
* [Roles web](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)
* [Roles de trabajo](http://msdn.microsoft.com/library/windowsazure/ee758711.aspx)

## Pasos siguientes
* TBD

## Ejemplos de PowerShell
Descargue la [última versión de Azure PowerShell](https://github.com/Azure/azure-sdk-tools/releases) para obtener mejores resultados.

### Configurar el tiempo de espera de TCP para la IP pública en el nivel de instancia en 15 minutos

    Set-AzurePublicIP –PublicIPName webip –VM MyVM -IdleTimeoutInMinutes 15

El valor de IdleTimeoutInMinutes es opcional. Si no se establece, el tiempo de espera predeterminado es de 4 minutos. El valor ahora se puede establecer entre 4 y 30 minutos.

### Establecer el tiempo de espera de inactividad al crear un extremo de Azure en una máquina virtual

    Get-AzureVM -ServiceName "mySvc" -Name "MyVM1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 8080 -IdleTimeoutInMinutes 15| Update-AzureVM

### Recuperar la configuración de tiempo de espera de inactividad

    PS C:\> Get-AzureVM –ServiceName “MyService” –Name “MyVM” | Get-AzureEndpoint
    
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
    
### Establecer el tiempo de espera de TCP en un conjunto de extremo de carga equilibrada

Si los extremos forman parte de un conjunto de extremo de carga equilibrada, el tiempo de espera de TCP se debe establecer en el conjunto de extremo de carga equilibrada:

    Set-AzureLoadBalancedEndpoint -ServiceName "MyService" -LBSetName "LBSet1" -Protocol tcp -LocalPort 80 -ProbeProtocolTCP -ProbePort 8080 -IdleTimeoutInMinutes 15

## Ejemplos de servicio en la nube

Puede aprovechar el SDK de Azure para .NET para actualizar el servicio en la nube.

La configuración de extremo para los servicios en la nube se realiza en el archivo .csdef. Por lo tanto, para actualizar el tiempo de espera de TCP para una implementación de servicios en la nube, se requiere una actualización de la implementación. Una excepción es si el tiempo de espera de TCP solo se especifica para una dirección IP pública. La configuración de IP pública se encuentra en el archivo .cscfg y se puede actualizar a través de la actualización de la implementación.

Los cambios de .csdef para la configuración de extremo son:

    <WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
      <Endpoints>
        <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" idleTimeoutInMinutes="tcp-timeout" />
      </Endpoints>
    </WorkerRole>The .cscfg changes for the timeout setting on Public IPs are:
    
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

Los desarrolladores pueden configurar la distribución del equilibrador de carga con la API de administración de servicios. Asegúrese de agregar el encabezado x-ms-version y que esté establecido en la versión 2014-06-01 o posterior.

### Actualizar la configuración de los extremos de entrada de carga equilibrada especificados en todas las máquinas virtuales de una implementación

#### Solicitud

    POST https://management.core.windows.net/<subscription-id>/services/hostedservices/<cloudservice-name>/deployments/<deployment-name>

El valor de LoadBalancerDistribution puede ser sourceIP para la afinidad de 2-tupla, sourceIPProtocol para la afinidad de 3-tupla o ninguno (sin afinidad, es decir, 5-tupla).

#### Response

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
 

<!---HONumber=August15_HO6-->