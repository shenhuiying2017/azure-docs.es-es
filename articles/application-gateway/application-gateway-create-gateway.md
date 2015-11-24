<properties
   pageTitle="Creación, inicio o eliminación de una puerta de enlace de aplicaciones | Microsoft Azure"
   description="Esta página proporciona instrucciones para crear, configurar, iniciar y eliminar una puerta de enlace de aplicaciones de Azure"
   documentationCenter="na"
   services="application-gateway"
   authors="joaoma"
   manager="jdial"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="joaoma"/>

# Creación, inicio o eliminación de una puerta de enlace de aplicaciones

La Puerta de enlace de aplicaciones es el equilibrador de carga de nivel 7. Ofrece conmutación por error, enrutamiento del rendimiento de solicitudes HTTP entra distintos servidores, ya se que se encuentren en la nube o en una implementación local. La puerta de enlace de aplicaciones tiene las siguientes características de entrega de aplicaciones: equilibrio de carga HTTP, afinidad de sesión basado en cookies, descarga SSL.

> [AZURE.SELECTOR]
- [Azure Classic PowerShell](application-gateway-create-gateway.md)
- [Azure Resource Manager PowerShell](application-gateway-create-gateway-arm.md)
- [Azure Resource Manager template](application-gateway-create-gateway-arm-template.md)


<BR>

Este artículo le guiará por los pasos necesarios para crear, configurar, iniciar y eliminar una puerta de enlace de aplicaciones.


>[AZURE.IMPORTANT]Antes de trabajar con recursos de Azure, es importante comprender que Azure tiene actualmente dos modelos de implementación: el Administrador de recursos y el clásico. Asegúrese de que comprende los [modelos de implementación y las herramientas](azure-classic-rm.md) antes de trabajar con recursos de Azure. Puede ver la documentación de las distintas herramientas haciendo clic en las pestañas de la parte superior de este artículo. Este documento tratará de la creación de una Puerta de enlace de aplicaciones con la implementación clásica de Azure. Para usar la versión del Administrador de recursos, vaya a [crear una implementación de Puerta de enlace de aplicaciones con el Administrador de recursos](application-gateway-create-gateway-arm.md).





## Antes de empezar

1. Instale la versión más reciente de los cmdlets de Azure PowerShell mediante el Instalador de plataforma web. Puede descargar e instalar la versión más reciente desde la sección [Windows PowerShell](http://azure.microsoft.com/downloads/) de la **Página de descargas**.
2. Compruebe que tiene una red virtual de trabajo con una subred válida. Asegúrese de que ninguna máquina virtual o implementación de la nube usa la subred. La Puerta de enlace de aplicaciones debe encontrarse en una subred de red virtual.
3. Los servidores que configurará para usar la Puerta de enlace de aplicaciones deben existir o tener sus extremos creados en la red virtual o tener una VIP/IP pública asignada.

## ¿Qué se necesita para crear una Puerta de enlace de aplicaciones?


Si se usa el comando **New-AzureApplicationGateway** para crear la Puerta de enlace de aplicaciones, no se define ninguna configuración en ese momento y el recurso recién creado tendrá que configurarse con XML o con un objeto de configuración.


Los valores son:

- **Grupo de servidores back-end:** lista de direcciones IP de los servidores back-end. Las direcciones IP mostradas deben pertenecer a la subred de la red virtual o ser una VIP/IP pública.
- **Configuración del grupo de servidores back-end:** cada grupo tiene una configuración como el puerto, el protocolo y la afinidad basada en cookies. Estos valores están vinculados a un grupo y se aplican a todos los servidores del grupo.
- **Puerto front-end:** se trata del puerto público abierto en la Puerta de enlace de aplicaciones. El tráfico llega a este puerto y después se redirige a uno de los servidores back-end.
- **Agente de escucha:** la escucha tiene un puerto front-end, un protocolo (Http o Https, que distinguen mayúsculas de minúsculas) y el nombre del certificado SSL (si se configura la descarga de SSL).
- **Regla:** enlaza el agente de escucha y el grupo de servidores back-end y define a qué grupo de servidores back-end se redirigirá el tráfico cuando se seleccione un agente de escucha concreto. Actualmente, solo se admite la regla *básica*. La regla *básica* es la distribución de carga round robin.



## Creación de una Puerta de enlace de aplicaciones

Hay un orden de pasos que debe seguir para crear una Puerta de enlace de aplicaciones:

1. Creación de un recurso de la Puerta de enlace de aplicaciones.
2. Creación de un archivo de configuración XML o un objeto de configuración.
3. Confirmación de la configuración del recurso de la Puerta de enlace de aplicaciones recién creado

### Creación de un recurso de la Puerta de enlace de aplicaciones

Para crear la puerta de enlace, use el cmdlet `New-AzureApplicationGateway` y reemplace los valores por los suyos. Tenga en cuenta que la facturación de la puerta de enlace no se inicia en este momento. La facturación comienza en un paso posterior, cuando la puerta de enlace se ha iniciado correctamente.

En el ejemplo siguiente se crea una Puerta de enlace de aplicaciones nueva mediante una red virtual denominada "testvnet1" y una subred denominada "subnet-1":


	PS C:\> New-AzureApplicationGateway -Name AppGwTest -VnetName testvnet1 -Subnets @("Subnet-1")

	VERBOSE: 4:31:35 PM - Begin Operation: New-AzureApplicationGateway
	VERBOSE: 4:32:37 PM - Completed Operation: New-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   55ef0460-825d-2981-ad20-b9a8af41b399


 *Description*, *InstanceCount* y *GatewaySize* son parámetros opcionales.


**Para validar** la creación de la puerta de enlace, puede usar el cmdlet `Get-AzureApplicationGateway`.




	PS C:\> Get-AzureApplicationGateway AppGwTest
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Stopped
	VirtualIPs    : {}
	DnsName       :

>[AZURE.NOTE]El valor predeterminado de *InstanceCount* es 2, con un valor máximo de 10. El valor predeterminado de *GatewaySize* es Medium. Puede elegir entre Pequeño, Mediano y Grande.


 *Vip* y *DnsName* se muestran en blanco porque todavía no se ha iniciado la puerta de enlace. Se crearán una vez que la puerta de enlace esté en estado de ejecución.

## Configuración de la Puerta de enlace de aplicaciones

La Puerta de enlace de aplicaciones se puede configurar con un archivo XML o con un objeto de configuración.

## Configuración de la Puerta de enlace de aplicaciones con un archivo XML

En el ejemplo siguiente, se usa un archivo XML para configurar todos los valores de la Puerta de enlace de aplicaciones y confirmarlos en el recurso de dicha puerta de enlace.

### Paso 1  

Copie el texto siguiente y péguelo en el Bloc de notas.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>(name-of-your-frontend-port)</Name>
	            <Port>(port number)</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>(name-of-your-backend-pool)</Name>
	            <IPAddresses>
	                <IPAddress>(your-IP-address-for-backend-pool)</IPAddress>
	                <IPAddress>(your-second-IP-address-for-backend-pool)</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>(backend-setting-name-to-configure-rule)</Name>
	            <Port>80</Port>
	            <Protocol>[Http|Https]</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>(name-of-the-listener)</Name>
	            <FrontendPort>(name-of-your-frontend-port)</FrontendPort>
	            <Protocol>[Http|Https]</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>(name-of-load-balancing-rule)</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>(backend-setting-name-to-configure-rule)</BackendHttpSettings>
	            <Listener>(name-of-the-listener)</Listener>
	            <BackendAddressPool>(name-of-your-backend-pool)</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>

Edite los valores entre paréntesis de los elementos de configuración. Guarde el archivo con extensión .xml.

>[AZURE.IMPORTANT]El elemento de protocolo Http o Https distingue mayúsculas de minúsculas.

En el ejemplo siguiente se muestra cómo usar un archivo de configuración para configurar la Puerta de enlace de aplicaciones para que equilibre la carga de tráfico Http en el puerto público 80 y envíe el tráfico de red al puerto back-end 80 entre dos direcciones IP.

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationGatewayConfiguration xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/windowsazure">
	    <FrontendPorts>
	        <FrontendPort>
	            <Name>FrontendPort1</Name>
	            <Port>80</Port>
	        </FrontendPort>
	    </FrontendPorts>
	    <BackendAddressPools>
	        <BackendAddressPool>
	            <Name>BackendPool1</Name>
	            <IPAddresses>
	                <IPAddress>10.0.0.1</IPAddress>
	                <IPAddress>10.0.0.2</IPAddress>
	            </IPAddresses>
	        </BackendAddressPool>
	    </BackendAddressPools>
	    <BackendHttpSettingsList>
	        <BackendHttpSettings>
	            <Name>BackendSetting1</Name>
	            <Port>80</Port>
	            <Protocol>Http</Protocol>
	            <CookieBasedAffinity>Enabled</CookieBasedAffinity>
	        </BackendHttpSettings>
	    </BackendHttpSettingsList>
	    <HttpListeners>
	        <HttpListener>
	            <Name>HTTPListener1</Name>
	            <FrontendPort>FrontendPort1</FrontendPort>
	            <Protocol>Http</Protocol>
	        </HttpListener>
	    </HttpListeners>
	    <HttpLoadBalancingRules>
	        <HttpLoadBalancingRule>
	            <Name>HttpLBRule1</Name>
	            <Type>basic</Type>
	            <BackendHttpSettings>BackendSetting1</BackendHttpSettings>
	            <Listener>HTTPListener1</Listener>
	            <BackendAddressPool>BackendPool1</BackendAddressPool>
	        </HttpLoadBalancingRule>
	    </HttpLoadBalancingRules>
	</ApplicationGatewayConfiguration>





### Paso 2

Después, establecerá la puerta de enlace de aplicaciones. Usará el cmdlet `Set-AzureApplicationGatewayConfig` con un archivo de configuración XML.


	PS C:\> Set-AzureApplicationGatewayConfig -Name AppGwTest -ConfigFile "D:\config.xml"

	VERBOSE: 7:54:59 PM - Begin Operation: Set-AzureApplicationGatewayConfig
	VERBOSE: 7:55:32 PM - Completed Operation: Set-AzureApplicationGatewayConfig
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   9b995a09-66fe-2944-8b67-9bb04fcccb9d

## Configuración de la Puerta de enlace de aplicaciones con un objeto de configuración

En el ejemplo siguiente, se muestra cómo configurar la Puerta de enlace de aplicaciones con los objetos de configuración. Todos los elementos de configuración deben configurarse individualmente y, a continuación, se deben agregar a un objeto de configuración de la Puerta de enlace de aplicaciones. Después de crear el objeto de configuración, usará el comando `Set-AzureApplicationGateway` para confirmar la configuración del recurso de Puerta de enlace de aplicaciones creado anteriormente.

>[AZURE.NOTE]Antes de asignar un valor a cada objeto de configuración, es preciso declarar el tipo de objeto que usa PowerShell para el almacenamiento. La primera línea para crear los elementos individuales define qué Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model(nombre de objeto) se utilizará.

### Paso 1

Cree todos los elementos de configuración individuales.

Cree la IP de front-end, como se muestra en el ejemplo siguiente.

	PS C:\> $fip = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration
	PS C:\> $fip.Name = "fip1"
	PS C:\> $fip.Type = "Private"
	PS C:\> $fip.StaticIPAddress = "10.0.0.5"

Cree el puerto de front-end, como se muestra en el ejemplo siguiente.

	PS C:\> $fep = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort
	PS C:\> $fep.Name = "fep1"
	PS C:\> $fep.Port = 80

Cree el grupo de servidores back-end

 Defina las direcciones IP que se agregarán al grupo de servidores back-end como se muestra en el siguiente ejemplo.


	PS C:\> $servers = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendServerCollection
	PS C:\> $servers.Add("10.0.0.1")
	PS C:\> $servers.Add("10.0.0.2")

 Con el objeto $server, agregue los valores al objeto del grupo de servidores back-end ($pool)

	PS C:\> $pool = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool
	PS C:\> $pool.BackendServers = $servers
	PS C:\> $pool.Name = "pool1"

Crear la configuración del grupo de servidores back-end

	PS C:\> $setting = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings
	PS C:\> $setting.Name = "setting1"
	PS C:\> $setting.CookieBasedAffinity = "enabled"
	PS C:\> $setting.Port = 80
	PS C:\> $setting.Protocol = "http"

Crear el agente de escucha

	PS C:\> $listener = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener
	PS C:\> $listener.Name = "listener1"
	PS C:\> $listener.FrontendPort = "fep1"
	PS C:\> $listener.FrontendIP = "fip1"
	PS C:\> $listener.Protocol = "http"
	PS C:\> $listener.SslCert = ""

Crear regla

	PS C:\> $rule = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule
	PS C:\> $rule.Name = "rule1"
	PS C:\> $rule.Type = "basic"
	PS C:\> $rule.BackendHttpSettings = "setting1"
	PS C:\> $rule.Listener = "listener1"
	PS C:\> $rule.BackendAddressPool = "pool1"

### Paso 2

Asignar todos los elementos de configuración individuales a un objeto de configuración de la Puerta de enlace de aplicaciones ($appgwconfig):

Agregar la IP front-end a la configuración

	PS C:\> $appgwconfig = New-Object Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.ApplicationGatewayConfiguration
	PS C:\> $appgwconfig.FrontendIPConfigurations = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendIPConfiguration]"
	PS C:\> $appgwconfig.FrontendIPConfigurations.Add($fip)

Agregar el puerto front-end a la configuración

	PS C:\> $appgwconfig.FrontendPorts = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.FrontendPort]"
	PS C:\> $appgwconfig.FrontendPorts.Add($fep)

Agregar el grupo de servidores back-end a la configuración

	PS C:\> $appgwconfig.BackendAddressPools = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendAddressPool]"
	PS C:\> $appgwconfig.BackendAddressPools.Add($pool)  

Agregar la configuraicón del grupo back-end a la configuración

	PS C:\> $appgwconfig.BackendHttpSettingsList = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.BackendHttpSettings]"
	PS C:\> $appgwconfig.BackendHttpSettingsList.Add($setting)

Agregar el agente de escucha a la configuración

	PS C:\> $appgwconfig.HttpListeners = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpListener]"
	PS C:\> $appgwconfig.HttpListeners.Add($listener)

Agregar la regla a la configuración

	PS C:\> $appgwconfig.HttpLoadBalancingRules = New-Object "System.Collections.Generic.List[Microsoft.WindowsAzure.Commands.ServiceManagement.Network.ApplicationGateway.Model.HttpLoadBalancingRule]"
	PS C:\> $appgwconfig.HttpLoadBalancingRules.Add($rule)

### Paso 3

Confirme el objeto de configuración en el recurso de Puerta de enlace de aplicaciones con `Set-AzureApplicationGatewayConfig`.

	Set-AzureApplicationGatewayConfig -Name AppGwTest -Config $appgwconfig

## Inicio de la puerta de enlace

Una vez configurada la puerta de enlace, use el cmdlet `Start-AzureApplicationGateway` para iniciarla. La facturación de una puerta de enlace de aplicaciones comienza después de que se inicie correctamente.


> [AZURE.NOTE]El cmdlet `Start-AzureApplicationGateway` puede tardar hasta 15-20 minutos en completarse.



	PS C:\> Start-AzureApplicationGateway AppGwTest

	VERBOSE: 7:59:16 PM - Begin Operation: Start-AzureApplicationGateway
	VERBOSE: 8:05:52 PM - Completed Operation: Start-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   fc592db8-4c58-2c8e-9a1d-1c97880f0b9b

## Comprobación del estado de la puerta de enlace

Use el cmdlet `Get-AzureApplicationGateway` para comprobar el estado de la puerta de enlace. Si *Start-AzureApplicationGateway* se ha completado correctamente en el paso anterior, el estado debe ser *En ejecución * y los valores VIP y DnsName deben tener entradas válidas.

En el siguiente ejemplo se muestra una Puerta de enlace de aplicaciones activa y que está lista para recibir el tráfico destinado a `http://<generated-dns-name>.cloudapp.net`.

	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 8:09:28 PM - Begin Operation: Get-AzureApplicationGateway
	VERBOSE: 8:09:30 PM - Completed Operation: Get-AzureApplicationGateway
	Name          : AppGwTest
	Description   :
	VnetName      : testvnet1
	Subnets       : {Subnet-1}
	InstanceCount : 2
	GatewaySize   : Medium
	State         : Running
	Vip           : 138.91.170.26
	DnsName       : appgw-1b8402e8-3e0d-428d-b661-289c16c82101.cloudapp.net


## Eliminación de una Puerta de enlace de aplicaciones

Para eliminar una puerta de enlace de aplicaciones:

1. Use el cmdlet `Stop-AzureApplicationGateway` para detener la puerta de enlace.
2. Use el cmdlet `Remove-AzureApplicationGateway` para quitar la puerta de enlace.
3. Compruebe que se ha quitado la puerta de enlace con el cmdlet `Get-AzureApplicationGateway`.

En el siguiente ejemplo se muestra el cmdlet `Stop-AzureApplicationGateway` en la primera línea, seguido de la salida.

	PS C:\> Stop-AzureApplicationGateway AppGwTest

	VERBOSE: 9:49:34 PM - Begin Operation: Stop-AzureApplicationGateway
	VERBOSE: 10:10:06 PM - Completed Operation: Stop-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   ce6c6c95-77b4-2118-9d65-e29defadffb8

Cuando la Puerta de enlace de aplicaciones tenga el estado detenido, use el cmdlet `Remove-AzureApplicationGateway` para quitar el servicio.


	PS C:\> Remove-AzureApplicationGateway AppGwTest

	VERBOSE: 10:49:34 PM - Begin Operation: Remove-AzureApplicationGateway
	VERBOSE: 10:50:36 PM - Completed Operation: Remove-AzureApplicationGateway
	Name       HTTP Status Code     Operation ID                             Error
	----       ----------------     ------------                             ----
	Successful OK                   055f3a96-8681-2094-a304-8d9a11ad8301

Para comprobar que se ha quitado el servicio, puede usar el cmdlet `Get-AzureApplicationGateway`. Este paso no es necesario.


	PS C:\> Get-AzureApplicationGateway AppGwTest

	VERBOSE: 10:52:46 PM - Begin Operation: Get-AzureApplicationGateway

	Get-AzureApplicationGateway : ResourceNotFound: The gateway does not exist.
	.....

## Pasos siguientes

Si quiere configurar la descarga SSL, vea [Configuración de una Puerta de enlace de aplicaciones para descarga SSL](application-gateway-ssl.md).

Si quiere configurar una Puerta de enlace de aplicaciones para usarla con ILB, vea [Creación de una Puerta de enlace de aplicaciones con un equilibrador de carga interno (ILB)](application-gateway-ilb.md).

Si desea obtener más información acerca de opciones de equilibrio de carga en general, vea:

- [Equilibrador de carga de Azure](https://azure.microsoft.com/documentation/services/load-balancer/)
- [Administrador de tráfico de Azure](https://azure.microsoft.com/documentation/services/traffic-manager/)

<!---HONumber=Nov15_HO4-->