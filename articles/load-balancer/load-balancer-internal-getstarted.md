<properties
   pageTitle="Introducción al Equilibrador de carga interno | Microsoft Azure"
   description="Configuración del Equilibrador de carga interno y cómo implementarlo para máquinas virtuales e implementaciones en la nube"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="joaoma" />

# Introducción a la configuración de un equilibrador de carga interno

> [AZURE.SELECTOR]
- [Azure Classic steps](load-balancer-internal-getstarted.md)
- [Resource Manager PowerShell steps](load-balancer-internal-arm-powershell.md)

El Equilibrio de carga interno (ILB) de Azure proporciona un equilibrio de carga entre las máquinas virtuales que residen dentro de un servicio en la nube o en una red virtual con un ámbito regional. Para obtener información sobre el uso y la configuración de redes virtuales con un ámbito regional, consulta [Redes virtuales regionales](virtual-networks-migrate-to-regional-vnet.md). Las redes virtuales existentes que se han configurado para un grupo de afinidad no pueden usar ILB.

## Para crear un equilibrador de carga interno establecido para máquinas virtuales

Para crear un conjunto con equilibrio de carga interno de Azure y los servidores que enviarán su tráfico a él, debe hacer lo siguiente:

1. Crea una instancia de Equilibrio de carga interno que será el extremo del tráfico entrante que su carga se va a equilibrar entre los servidores de un conjunto con equilibrio de carga.

1. Agregue extremos correspondientes a las máquinas virtuales que van a recibir el tráfico entrante.

1. Configura los servidores que van a enviar el tráfico cuya carga se va a equilibrar para que lo hagan a la dirección IP virtual (VIP) de la instancia de Equilibrio de carga interno.

### Paso 1: crear una instancia de Equilibrio de carga interno

Para un servicio en la nube existente o un servicio en la nube implementado en una red virtual regional, puedes crear una instancia de Equilibrio de carga interno con los siguientes comandos de Windows PowerShell:

	$svc="<Cloud Service Name>"
	$ilb="<Name of your ILB instance>"
	$subnet="<Name of the subnet within your virtual network>"
	$IP="<The IPv4 address to use on the subnet-optional>"

	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


Para usar estos comandos, rellene los valores y quite el elemento < and >. Este es un ejemplo:

	$svc="WebCloud-NY"
	$ilb="SQL-BE"
	$subnet="Farm1"
	$IP="192.168.98.10"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP


### Paso 2: agregar extremos a la instancia de Equilibrio de carga interno

Para las máquinas virtuales existentes, puedes agregar extremos a la instancia de Equilibrio de carga interno con los comandos siguientes:

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	$lbsetname="<Name of the load balancer set>"
	$prot="tcp" or "udp"
	$locport=<local port number>
	$pubport=<public port number>
	$ilb="<Name of your ILB instance>"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbsetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Para usar estos comandos, rellene los valores y quite el elemento < and >.

Tenga en cuenta que en este cmdlet [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) de Windows PowerShell, se usa el conjunto de parámetros DefaultProbe. Para obtener más información sobre conjuntos de parámetros adicionales, consulte [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

Este es un ejemplo:

	$svc="AZ-LOB1"
	$vmname="SQL-LOBAZ1"
	$epname="SQL1"
	$lbsetname="SQL-LB"
	$prot="tcp"
	$locport=1433
	$pubport=1433
	$ilb="SQL ILB"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


### Paso 3: configurar los servidores para que envíen su tráfico al nuevo extremo Equilibrio de carga interno

Tienes que configurar los servidores para los que se va a realizar el equilibrio de carga del tráfico para usar la nueva dirección IP (VIP) de la instancia de Equilibrio de carga interno. Esta es la dirección en la que se escuchará la instancia de Equilibrio de carga interno. En la mayoría de los casos, solo tienes que agregar o modificar un registro DNS de la dirección VIP de la instancia de Equilibrio de carga interno.

Si especificaste la dirección IP durante la creación de la instancia de Equilibrio de carga interno, ya tienes la dirección VIP. De lo contrario, puede ver a la dirección VIP con los siguientes comandos:

	$svc="<Cloud Service Name>"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer



Para usar estos comandos, rellene los valores y quite el elemento < and >. Aquí tiene un ejemplo:

	$svc="WebCloud-NY"
	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer


En la pantalla del comando Get-AzureInternalLoadBalancer, anote la dirección IP y realice los cambios necesarios en los servidores o registros DNS para asegurarse de que el tráfico se envía a la dirección VIP.

>[AZURE.NOTE]La Plataforma Microsoft Azure utiliza una dirección IPv4 estática enrutable públicamente para una variedad de escenarios de administración. La dirección IP es 168.63.129.16. Ningún firewall debe bloquear esta dirección IP, ya que puede causar un comportamiento inesperado. Con respecto al Equilibrio de carga interno de Azure, esta dirección IP la usan las sondas de supervisión del equilibrador de carga para determinar el estado de mantenimiento de las máquinas virtuales en un conjunto con equilibrio de carga. Si se usa un grupo de seguridad de red para restringir el tráfico a máquinas virtuales de Azure en un conjunto de carga equilibrada internamente o se aplica a una subred de Red virtual, asegúrate de agregar una regla de seguridad de red para permitir el tráfico desde 168.63.129.16.



## Ejemplos completos de equilibrio de carga interno

Para guiarle en el proceso completo de la creación de un conjunto con equilibrio de carga para dos configuraciones de ejemplo, vea las secciones siguientes.

### Una aplicación de niveles múltiples accesible desde Internet

Contoso Corporation desea proporcionar equilibrio de carga entre un conjunto de servidores web accesibles desde Internet y un conjunto de servidores de base de datos. Ambos conjuntos de servidores se hospedan en un solo servicio en la nube de Azure. El tráfico del servidor web al puerto TCP 1433 debe distribuirse entre tres máquinas virtuales en el nivel de base de datos. La Ilustración 1 muestra la configuración.

![Conjunto con equilibrio de carga interno para el nivel de base de datos](./media/load-balancer-internal-getstarted/IC736321.png)

Ilustración 1: Ejemplo de una aplicación de niveles múltiples accesible desde Internet

La configuración es la siguiente:

- El servicio en la nube existente que hospeda las máquinas virtuales se denomina Contoso-PartnerSite.

- Los tres servidores de base de datos existentes se denominan PARTNER-SQL-1, PARTNER-SQL-2 y PARTNER-SQL-3.

- Los servidores web del nivel web se conectan con los servidores de base de datos en el nivel de base de datos con el nombre DNS partner-sql.external.contoso.com.

Los comandos siguientes configuran una nueva instancia de Equilibrio de carga interno denominada PARTNER-DBTIER y agregan extremos a las máquinas virtuales correspondientes a los tres servidores de base de datos:

	$svc="Contoso-PartnerSite"
	$ilb="PARTNER-DBTIER"
	Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb

	$prot="tcp"
	$locport=1433
	$pubport=1433
	$epname="DBTIER1"
	$lbsetname="SQL-LB"
	$vmname="PARTNER-SQL-1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="DBTIER2"
	$vmname="PARTNER-SQL-2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="DBTIER3"
	$vmname="PARTNER-SQL-3"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

Luego, Contoso determina la dirección VIP de la instancia de Equilibrio de carga interno PARTNER-DBTIER con el siguiente comando:

	Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer

Contoso anota la dirección VIP de 100.64.65.211 de la pantalla de este comando y configura el registro de dirección (A) de DNS para que el nombre partner-sql.external.contoso.com use esta nueva dirección.

### Una aplicación LOB hospedada en Azure

Contoso Corporation quiere hospedar una aplicación de línea de negocio (LOB) en un conjunto de servidores web de Azure. La carga del tráfico de cliente al puerto TCP 80 se debe equilibrar entre tres máquinas virtuales que se ejecutan en una red virtual entre locales. La Ilustración 2 muestra la configuración.

![Equilibrio de carga interno para una aplicación LOB](./media/load-balancer-internal-getstarted/IC744148.png)

Figura 2: Ejemplo de una aplicación LOB hospedada en Azure

La configuración es la siguiente:

- El servicio en la nube existente que hospeda las máquinas virtuales se denomina Contoso-PartnerSite.

- La subred en la que se encuentran los servidores LOB se denomina LOB-LEGAL y Contoso eligió la dirección 198.168.99.145 como dirección VIP del Equilibrador de carga interno.

- Los tres servidores LOB existentes se denominan LEGAL-1, LEGAL-2 y LEGAL-3.

- Los clientes web de intranet se conectan a los servidores LOB con el nombre DNS legalnet.corp.contoso.com.

Los comandos siguientes crean una nueva instancia de Equilibrio de carga interno denominada LEGAL-ILB y agregan extremos a las máquinas virtuales correspondientes a los tres servidores LOB:


	$svc="Contoso-Legal"
	$ilb="LEGAL-ILB"
	$subnet="LOB-LEGAL"
	$IP="198.168.99.145"
	Add-AzureInternalLoadBalancer –ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP

	$prot="tcp"
	$locport=80
	$pubport=80
	$epname="LOB1"
	$lbsetname="LOB-LB"
	$vmname="LEGAL-1"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname-LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="LOB2"
	$vmname="LEGAL2"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

	$epname="LOB3"
	$vmname="LEGAL3"
	Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM


A continuación, Contoso configura el registro DNS A para que el nombre legalnet.corp.contoso.com use 198.168.99.145.

## Agregar una máquina virtual al Equilibrio de carga interno

Para agregar una máquina virtual a una instancia de Equilibrio de carga interno que se crea, puedes usar los cmdlets New-AzureInternalLoadBalancerConfig y New-AzureVMConfig.

Este es un ejemplo:

	$svc="AZ-LOB1"
	$ilb="LOB-ILB"
	$vnet="LOBNet_Azure"
	$subnet="LOBServers"
	$vmname="LOB-WEB1"
	$adminuser="Lando"
	$adminpw="Platform327"
	$regionname="North Central US"

	$myilbconfig=New-AzureInternalLoadBalancerConfig -InternalLoadBalancerName $ilb -SubnetName $subnet
	$images = Get-AzureVMImage
	New-AzureVMConfig -Name $vmname -InstanceSize Small -ImageName $images[50].ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $adminuser -Password $adminpw | New-AzureVM -ServiceName $svc -InternalLoadBalancerConfig $myilbconfig -Location $regionname –VNetName $vnet

## Para configurar el Equilibrio de carga interno para los servicios en la nube


El Equilibrio de carga interno es compatible tanto con las máquinas virtuales como con los servicios en la nube. Un extremo de Equilibrio de carga interno creado en un servicio en la nube que está fuera de una red virtual regional solo será accesible dentro del servicio en la nube.

La configuración del Equilibrio de carga interno se debe establecer durante la creación de la primera implementación en el servicio en la nube, como se muestra en el ejemplo siguiente.

>[AZURE.IMPORTANT]Un requisito previo para ejecutar los pasos siguientes es tener ya creada una red virtual para la implementación en la nube. Necesitarás el nombre de red virtual y el nombre de la subred para crear el Equilibrio de carga interno.

### Paso 1

Abre el archivo de configuración de servicio (.cscfg) para la implementación en la nube en Visual Studio y agrega la siguiente sección para crear el Equilibrio de carga interno en el último elemento «`</Role>`» para la configuración de red.




	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="name of the load balancer">
	      <FrontendIPConfiguration type="private" subnet="subnet-name" staticVirtualNetworkIPAddress="static-IP-address"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>


Vamos a agregar los valores para que el archivo de configuración de red muestre su aspecto. En el ejemplo, supongamos que creó una subred que se llama "test\_vnet" con una subred 10.0.0.0/24 denominada test\_subnet y una dirección IP estática 10.0.0.4. El equilibrador de carga se denominará testLB.

	<NetworkConfiguration>
	  <LoadBalancers>
	    <LoadBalancer name="testLB">
	      <FrontendIPConfiguration type="private" subnet="test_subnet" staticVirtualNetworkIPAddress="10.0.0.4"/>
	    </LoadBalancer>
	  </LoadBalancers>
	</NetworkConfiguration>

Para obtener más información sobre el esquema del equilibrador de carga, consulta [Agregar equilibrador de carga](https://msdn.microsoft.com/library/azure/dn722411.aspx)

### Paso 2


Cambia los archivos de definición (.csdef) para agregar extremos al Equilibrio de carga interno. Cuando se crea una instancia de rol, el archivo de definición de servicio agregará las instancias de rol al Equilibrio de carga interno.


	<WorkerRole name="worker-role-name" vmsize="worker-role-size" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="input-endpoint-name" protocol="[http|https|tcp|udp]" localPort="local-port-number" port="port-number" certificate="certificate-name" loadBalancerProbe="load-balancer-probe-name" loadBalancer="load-balancer-name" />
	  </Endpoints>
	</WorkerRole>

Vamos a agregar los valores del archivo de definición de servicio siguiendo los mismos valores del ejemplo anterior.

	<WorkerRole name=WorkerRole1" vmsize="A7" enableNativeCodeExecution="[true|false]">
	  <Endpoints>
	    <InputEndpoint name="endpoint1" protocol="http" localPort="80" port="80" loadBalancer="testLB" />
	  </Endpoints>
	</WorkerRole>

La carga del tráfico de red se equilibrará mediante el equilibrador de carga testLB, en el que se usa el puerto 80 para las solicitudes entrantes y se envía a instancias de rol de trabajo también en el puerto 80.


## Quitar una configuración de Equilibrio de carga interno

Para quitar una máquina virtual como extremo de una instancia de Equilibrio de carga interno, usa los comandos siguientes:

	$svc="<Cloud service name>"
	$vmname="<Name of the VM>"
	$epname="<Name of the endpoint>"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para usar estos comandos, rellene los valores y quite el elemento < and >.

Este es un ejemplo:

	$svc="AZ-LOB1"
	$vmname="SQL-LOBAZ1"
	$epname="SQL1"
	Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM

Para quitar una instancia de Equilibrio de carga interno de un servicio en la nube, usa los comandos siguientes:

	$svc="<Cloud service name>"
	Remove-AzureInternalLoadBalancer -ServiceName $svc

Para usar estos comandos, rellene el valor y quite el elemento < and >.

Este es un ejemplo:

	$svc="AZ-LOB1"
	Remove-AzureInternalLoadBalancer -ServiceName $svc



## Información adicional sobre los cmdlets de Equilibrio de carga interno


Para obtener información adicional sobre los cmdlets de Equilibrio de carga interno, ejecuta los comandos siguientes en un símbolo del sistema de Windows PowerShell:

- Get-help New-AzureInternalLoadBalancerConfig -full

- Get-help Add-AzureInternalLoadBalancer -full

- Get-help Get-AzureInternalLoadbalancer -full

- Get-help Remove-AzureInternalLoadBalancer -full

## Consulte también

[Configuración de un modo de distribución del equilibrador de carga](load-balancer-distribution-mode.md)

[Configuración de opciones de tiempo de espera de inactividad de TCP para el equilibrador de carga](load-balancer-tcp-idle-timeout.md)

<!---HONumber=Sept15_HO3-->